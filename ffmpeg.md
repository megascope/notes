## Cut a Video at a keyframe
```
# to is    HH:MM:SS.sss
ffmpeg -to 12:34:56.789 -i input.mp4 -vcodec copy -acodec copy output.mp4
```

## Replace audio in video
```
ffmpeg -i input_video.mp4 -i input_audio.mp3 -c:v copy -map 0:v:0 -map 1:a:0 -shortest output.mp4
```

`-map 0:v:0` maps the first (index 0) video stream from the input to the first (index 0) video stream in the output.

`-map 1:a:0` maps the second (index 1) audio stream from the input to the first (index 0) audio stream in the output.

If the audio is longer than the video, add `-shortest` before the output file name.

Not specifying an audio codec, will automatically select a working one. You can specify one by for example adding `-c:a libvorbis` after `-c:v copy`. You can also use `-c copy` to avoid re-encoding the audio, but this may lead to compatibility and synchronization problems.
