# How this clip was made

## The moment

Source: **Grand Theft Auto VI Trailer 2** (Rockstar Games' official YouTube channel,
`youtube.com/watch?v=VQRLujxTm3c`, 2:47, captured entirely in-game on PlayStation 5), downloaded
in this session with yt-dlp at 1080p.

Chosen moment: **the police chase explosion at t≈117.85–118.77** of the trailer. From the back
seat of Lucia's car, a police SUV slams a white sedan, which flips end over end through a
fireball. It is the trailer's single most arresting beat — a chase gone wrong, exactly the kind
of moment the bounty brief names — and it needs no context to read: crash, fireball, done.

I mapped the whole trailer on a 4-second contact sheet, then sampled the chase region at 1 s,
0.25 s and 0.1 s steps to find the exact cut points: the shot starts at 117.85 s (smash cut in,
crash already happening) and ends at 118.78 s (cut to two bystanders). The fireball peaks at
≈118.35 s.

## The creative

One moment, three passes — no montage:

1. **Full speed (0.92 s), blur-fill vertical.** The blast itself is the hook; the whole 16:9
   frame stays visible over a blurred, darkened fill so Lucia's point of view survives.
   On-screen text from frame one: `GTA VI CHASE GONE WRONG`.
2. **Slow-motion replay (3.1 s at 0.3×), punch-in crop.** A 540×960 crop on the flipping car
   (x=760, y=40 of the 1080p frame) scaled to full 1080×1920. Text: `WATCH IT AGAIN IN SLO-MO`.
   The audio is slowed with it (atempo 0.5 × 0.6), so the blast drops into a rumble.
3. **The pitch (7.4 s).** The same moment at 0.25×, looped once, blurred and darkened as a
   living backdrop. Three lines, ≈2.45 s each, centred in the vertical safe area (clear of
   YouTube's bottom and right-edge buttons):
   - `Make clips like this with AI`
   - `and earn money on make.fun`
   - `make.fun/bounty/gta6-clipper-01`

Final file: 1080×1920, 30 fps, H.264 + AAC, ≈11.4 s — a YouTube Short. Audio throughout is the
trailer's own (the song and the blast), slowed where the picture is slowed, faded out at the end.

## Commands (run in this session, ffmpeg 8.1.2)

Font: `/usr/share/fonts/truetype/liberation/LiberationSans-Bold.ttf`; each text line was written
to a small `t_*.txt` file and passed with `textfile=` to avoid escaping issues.

Segment 1 — full speed, blur-fill, hook text:

```sh
ffmpeg -ss 117.85 -i trailer2.mp4 -t 0.92 -filter_complex "\
[0:v]split[a][b];\
[a]scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920,gblur=sigma=35,eq=brightness=-0.08[bg];\
[b]scale=1080:-2[fg];\
[bg][fg]overlay=(W-w)/2:(H-h)/2,\
drawtext=fontfile=$FONT:textfile=t_hook.txt:fontcolor=white:borderw=6:bordercolor=black:\
shadowcolor=black@0.6:shadowx=3:shadowy=3:fontsize=64:x=(w-text_w)/2:y=170[v]" \
-map "[v]" -map 0:a -c:v libx264 -crf 18 -preset medium -r 30 -pix_fmt yuv420p \
-c:a aac -b:a 192k -ar 48000 -ac 2 seg1.mp4
```

Segment 2 — slow motion, punch-in crop (`-t` on the input side, before `setpts` stretches it):

```sh
ffmpeg -ss 117.85 -t 0.92 -i trailer2.mp4 -filter_complex "\
[0:v]crop=540:960:760:40,scale=1080:1920,setpts=PTS/0.3,\
drawtext=fontfile=$FONT:textfile=t_slo.txt:fontcolor=white:borderw=6:bordercolor=black:\
shadowcolor=black@0.6:shadowx=3:shadowy=3:fontsize=64:x=(w-text_w)/2:y=170[v];\
[0:a]atempo=0.5,atempo=0.6,aformat=sample_rates=48000:channel_layouts=stereo[a]" \
-map "[v]" -map "[a]" -c:v libx264 -crf 18 -preset medium -r 30 -pix_fmt yuv420p \
-c:a aac -b:a 192k seg2.mp4
```

Segment 3 — pitch over the blurred slow-motion loop:

```sh
ffmpeg -stream_loop 1 -ss 117.85 -i trailer2.mp4 -t 7.36 -filter_complex "\
[0:v]setpts=PTS/0.25,split[a][b];\
[a]scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920,gblur=sigma=35[bg0];\
[b]scale=1080:-2[fg];[bg0][fg]overlay=(W-w)/2:(H-h)/2,gblur=sigma=12,eq=brightness=-0.22,\
drawtext=...t_p1.txt...:fontsize=76:x=(w-text_w)/2:y=840:enable='between(t,0,2.45)',\
drawtext=...t_p2.txt...:fontsize=76:x=(w-text_w)/2:y=840:enable='between(t,2.45,4.9)',\
drawtext=...t_p3.txt...:fontsize=50:x=(w-text_w)/2:y=880:enable='between(t,4.9,7.36)'[v];\
[0:a]atempo=0.5,atempo=0.5,volume=0.45,afade=t=out:st=6.56:d=0.8,\
aformat=sample_rates=48000:channel_layouts=stereo[a]" \
-map "[v]" -map "[a]" -c:v libx264 -crf 18 -preset medium -r 30 -pix_fmt yuv420p \
-c:a aac -b:a 192k seg3.mp4
```

Join (timestamps normalised per input, then the concat filter; a plain stream-copy concat
mis-joins these segments and loses time):

```sh
ffmpeg -i seg1.mp4 -i seg2.mp4 -i seg3.mp4 -filter_complex "\
[0:v]setpts=PTS-STARTPTS[v0];[0:a]asetpts=PTS-STARTPTS[a0];\
[1:v]setpts=PTS-STARTPTS[v1];[1:a]asetpts=PTS-STARTPTS[a1];\
[2:v]setpts=PTS-STARTPTS[v2];[2:a]asetpts=PTS-STARTPTS[a2];\
[v0][a0][v1][a1][v2][a2]concat=n=3:v=1:a=1[v][a]" \
-map "[v]" -map "[a]" -c:v libx264 -crf 18 -preset medium -r 30 -pix_fmt yuv420p \
-c:a aac -b:a 192k -ar 48000 -movflags +faststart gta6-chase-gone-wrong.mp4
```

## Verification

`ffprobe` on the final file (1080×1920, h264, aac) and stills pulled at the hook, the slow-motion
replay and each pitch line were inspected before hand-off.
