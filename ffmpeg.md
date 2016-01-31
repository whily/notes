Notes for FFmpeg.

* Convert audio (e.g. to mp3 as in example below) without converting video

    ffmpeg -i input.mp4 -acodec mp3 -vcodec copy output.mp4

* When seeing error message "Unable to set encoding parameters", it might be due to
  that libvo_aacenc only support stereo, but not 5.1 channels. Try to use the native
  experimental FFmpeg AAC encoder by adding parameters "-c:a aac -strict experimental"

    ffmpeg -i input.mp4 -c:a aac -strict experimental output.mp4
