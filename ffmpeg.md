Notes for FFmpeg.

* Convert audio (e.g. to mp3 as in example below) without converting video

    ffmpeg -i input.mp4 -acodec mp3 -vcodec copy output.mp4
