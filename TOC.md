## 429 Too Many Requests
The server is temporarily refusing because it thinks you’re sending too many or “bot-like” requests. Often clears after a cooldown (minutes to hours) or with different headers/IP.

## WAF
A Web Application Firewall that sits in front of a site and applies bot rules (rate limits, JS challenges, CAPTCHAs). Examples include Cloudflare, Akamai, Fastly, Imperva.

## Cloudflare
A CDN/WAF that commonly serves a JS challenge before granting access. Passing the challenge sets a cookie that allows subsequent requests from that browser/session.

## Cookie
Small key–value data saved by your browser and sent back on requests to the same site (e.g., session IDs, challenge passes). Domain- and path-scoped, with an expiration.

## cf_clearance
A Cloudflare cookie that proves your browser solved its challenge for a specific hostname. Must be fresh and match the exact subdomain to work.

## User-Agent
An HTTP header that identifies your client (e.g., Chrome vs CLI). Some WAFs block uncommon or default tool UAs.

## Referer
An HTTP header indicating the page that linked to the resource. Some CDNs require a valid referer for embedded media.

## Accept-Language
A hint about preferred languages (e.g., en-US). Helps requests look like real browsers.

## Impersonation
Matching a real browser’s TLS+HTTP fingerprint (beyond just the User-Agent). Reduces WAF friction when supported by the client.

## TLS Fingerprint
Low-level handshake parameters (cipher suites, extensions) that differ between browsers and libraries; WAFs use these to spot non-browsers.

## NAT/Shared IP
Many users share one public IP (home, office, carrier). One noisy neighbor can hurt IP reputation for everyone.

## VPN
Routes traffic via a different egress IP. Can fix a bad reputation or trigger stricter bot rules depending on the provider.

## Extractor (yt-dlp)
Site-specific code that knows how to locate and normalize media on a page (e.g., “Loom extractor”). The “Generic” extractor is a fallback HTML parser.

## Format (yt-dlp)
A concrete download option combining container, codecs, resolution, bitrate, etc. Each gets an ID you can select with `-f`.

## Format Selector (yt-dlp)
A mini-language to pick formats by attributes, with fallbacks. Example: `-f "bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best"`.

## --cookies-from-browser (yt-dlp)
Imports cookies directly from a local browser profile. Syntax examples: `chrome`, `"chrome:Default"`, `"chrome:Profile 197"`, `firefox`, `edge`. Use the same profile you used to pass any WAF challenge.

## --referer (yt-dlp)
Sets the Referer header. Often needed when downloading media embedded on a different origin.

## --user-agent (yt-dlp)
Overrides the UA string to look like a browser. Helps with naive blocks but not full TLS fingerprinting.

## --sleep-requests, --retries, --retry-sleep (yt-dlp)
Backoff controls to avoid bursts that trip rate limits.

## --dump-pages, -v (yt-dlp)
Save/inspect fetched HTML and output verbose logs for troubleshooting.

## Impersonate Targets (yt-dlp)
With the curl_cffi backend, `--impersonate` can mimic real browsers (e.g., `chrome120`, `safari17`) at TLS+HTTP levels.

## Embed URL (Loom)
The URL placed in an iframe on a host page (usually `https://www.loom.com/embed/<id>` or `…/share/<id>`). yt-dlp can accept either.

## GraphQL JSON (Loom)
Metadata API responses that include title, duration, and links to playable sources.

## http-raw (Loom)
Usually the original upload; commonly WebM (VP9/Opus). Highest fidelity but not always the most compatible.

## http-transcoded (Loom)
Loom’s re-encoded copy; often MP4 (H.264/AAC) as a progressive file. More universally playable, but sometimes lower bitrate than the raw.

## Container
The file wrapper that holds one or more tracks (video, audio, subtitles). Examples: MP4 (`.mp4`), WebM (`.webm`). The container does not dictate the codec.

## Codec
The compression algorithm for each track. Video examples: H.264/AVC, HEVC/H.265, VP9, AV1. Audio examples: AAC, Opus, MP3. Players must support the codec to decode.

## Progressive Download
A single file served over HTTP; players can start playback before the full file is downloaded. No adaptive quality switching mid-playback.

## Streaming (HLS/DASH)
Media split into segments with a manifest (`.m3u8`, `.mpd`). Supports adaptive bitrate (auto quality changes) and live playback.

## Remux
Change only the container (e.g., WebM → MP4) without re-encoding the tracks. Instant to fast, zero quality loss, but only works if codecs are compatible with the target container.

## Recode / Transcode
Decode and re-encode tracks to new codecs/containers (e.g., VP9/Opus → H.264/AAC in MP4). Slower, uses more CPU, and is lossy.

## Bitrate
How many bits per second the media consumes. Higher generally means better quality and larger files; can be constant (CBR) or variable (VBR).

## Resolution
Pixel dimensions of the video frame (e.g., 1920×1080 = 1080p). Affects clarity and file size.

## Frame Rate (FPS)
Images per second; common rates are 24 (film), 30, and 60 (smooth motion).

## MP4
Ubiquitous container for distribution. Typical tracks are H.264 video + AAC audio. Plays on most OSes, phones, and browsers.

## WebM
Open container; typically VP9 or AV1 video + Opus audio. Excellent compression, but legacy devices/apps may not support it.

## H.264 / AVC
Very compatible video codec; hardware-accelerated on most devices.

## VP9 / AV1
Newer video codecs with better compression efficiency; AV1 support is growing but still uneven.

## AAC / Opus
AAC is broadly compatible; Opus is highly efficient at lower bitrates, great for speech.

## yt-dlp
Media downloader with hundreds of site extractors, a powerful format selector, and integrations with browsers/cookies.

## ffmpeg
Swiss‑army knife for media processing. yt-dlp invokes it for remuxing (`--remux-video`) and transcoding (`--recode-video`).

## Examples: List Formats
`yt-dlp -F "<loom-url>"`

## Examples: Prefer MP4 with Fallbacks
`yt-dlp -f "bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best" "<loom-url>"`

## Examples: Prefer Loom Transcoded MP4
`yt-dlp -f "http-transcoded/best" "<loom-url>"`

## Examples: Remux WebM to MP4
`yt-dlp --remux-video mp4 "<loom-url>"`

## Examples: Re-encode to MP4
`yt-dlp --recode-video mp4 "<loom-url>"`

## MPEG-DASH
An adaptive streaming protocol that breaks media into many small segments. Players download segments as needed and can switch quality on the fly based on bandwidth.

## MPD Manifest
The XML playlist used by MPEG-DASH. It lists available qualities, codecs, segment URLs, and timing so the player/downloader knows how to fetch and assemble the stream.

## DASH Video
A format entry where the media is delivered as DASH segments instead of a single progressive file. Usually marked with notes like “DASH video”. Often “video only” and must be merged with a matching audio stream.

## PROTO (yt-dlp column)
The delivery protocol shown in yt-dlp’s format table (e.g., `dash`, `https`, `m3u8`). Indicates how the media is fetched rather than the container/codec.

## TBR (yt-dlp column)
Total bitrate estimate for the format (kilobits per second). Useful for judging quality vs size. For separate video/audio streams, each will show its own bitrate.

## VBR (yt-dlp column)
Video bitrate estimate for the entry (kilobits per second). Not always present; when shown, helps compare video-only tracks of different qualities.

## Video Only / Audio Only
Format entries that contain only one track. To get a playable file, download matching video and audio and let yt-dlp/ffmpeg merge them automatically.

## webm_dash
A hint in yt-dlp’s notes indicating the DASH segments are in the WebM container (commonly VP9/Opus). Merged outputs can be WebM unless you remux/recode.

## dash-cdn-1 (Loom label)
A Loom-provided DASH stream served via a CDN (content delivery network). Typically optimized for global delivery and adaptive streaming.

## dash-raw-original (Loom label)
A Loom DASH stream that reflects the original upload characteristics more closely (often same codecs/bitrate as the source) compared to transcoded variants.

## CDN
Content Delivery Network: a distributed network of servers that cache and serve content closer to users, improving performance and reliability.

## Merge (yt-dlp/ffmpeg)
When downloading separate video-only and audio-only streams, yt-dlp calls ffmpeg to combine them into a single playable file in your chosen container.

## HTTP / HTTPS (Progressive)
Direct, single-file delivery over HTTP(S). Common for MP4/WebM downloads. In yt-dlp, PROTO often shows `https` or `http` for these entries.

## HLS (HTTP Live Streaming)
Apple’s adaptive streaming protocol using `.m3u8` manifests and MPEG-TS (or fMP4) segments. Widely used on the web and mobile. yt-dlp shows PROTO as `m3u8` (or `m3u8_native`).

## M3U8 Manifest
The playlist file used by HLS that lists variant streams (qualities) and segment URLs. Similar role to MPD in DASH.

## Low-Latency HLS (LL-HLS)
An HLS variant with shorter segments/parts for near-real-time playback. Still appears as `m3u8` to yt-dlp; handling is similar to standard HLS.

## Microsoft Smooth Streaming (MSS)
Adaptive streaming protocol using ISM/ISMC manifests with fragmented MP4 segments. Less common today but still supported by some services/tools.

## ISM / ISMC Manifest
Manifest files for Smooth Streaming (`.ism` server manifest, `.ismc` client manifest) describing tracks, qualities, and segment timing.

## RTMP / RTMPS
Real-Time Messaging Protocol (and its TLS variant). Older streaming transport used by legacy players and some live workflows. yt-dlp can fetch `rtmp`/`rtmps` URLs when exposed.

## Adobe HDS (F4M)
HTTP Dynamic Streaming using `.f4m` manifests and fragmented MP4 segments (`.f4f`). Largely legacy/obsolete but can still appear on older sites.

## WebTorrent
BitTorrent over WebRTC/WebSockets. Rare in mainstream video sites; yt-dlp can sometimes download if a direct webtorrent URL is provided.

## WebRTC
Real-time, peer-to-peer transport optimized for live interactive media (calls, conferencing). Not typically downloadable via yt-dlp because there is no static manifest/segment list.
