---
title: FFmpeg——常用命令
date: 2020-11-30 15:32:00
tags:
- FFmpeg
---

#### 安装

[官方文档 Centos7安装ffmpeg]: https://trac.ffmpeg.org/wiki/CompilationGuide/Centos#FFmpeg

#### 命令

```bash
ffmpeg -i akane.mp4 -c copy -map 0 -f segment -segment_list akane.m3u8 -segment_time 10 segment%d.ts
```

```bash
ffmpeg -y \
-i The_Kriss_Vector.mp4 \
-hls_time 20 \       # 将test.mp4分割成每个小段多少秒
-hls_key_info_file encrypt.keyinfo \
-hls_playlist_type vod \   # vod 是点播，表示PlayList不会变
-hls_segment_filename "playlist%d.ts" \  #  每个小段的文件名
playlist.m3u8   #  生成的m3u8文件
```

```bash
ffmpeg -y \
-i The_Kriss_Vector.mp4 \
-hls_time 20 \
-hls_key_info_file encrypt.keyinfo \
-hls_playlist_type event \
-hls_segment_filename "playlist%03d.ts" \
playlist.m3u8
```

```bash
ffmpeg -y -i akane.mp4 -hls_time 30 -hls_key_info_file enc.keyinfo -hls_segment_filename "akane%03d.ts" akane.m3u8
```

```bash
ffmpeg -y -i akane.mp4 -hls_time 4 -hls_segment_filename "playlist%03d.ts" playlist.m3u8
```

### 获取视频自带封面

```sh
# disposition 匹配
ffmpeg -y -i input.mp4 -map 0:v:disp:attached_pic -frames:v 1 =q:v 2 cover.jpg
# 查看流数据
ffprobe -v quiet -print_format json -show_streams input.mp4

# 视频流索引
ffmpeg -i input.mp4 -map 0:v:1 cover.jpg
# 视频流索引-不知道在哪个视频流
ffmpeg -y -i input.mp4 -map 0:$(ffprobe -v quiet -select_streams v -show_entries stream=index:stream_disposition -of json output2.mp4 | jq -r '.streams[] | select(.disposition.attached_pic==1) | .index') -frames:v 2 cover.jpg
```

### 截取图片

```sh
# 取第一帧
ffmpeg -y -i input.mp4 -map  -frames:v 1 -q:v 2 output.jpg  
# 从(-ss) 0 秒开始 只输出 1 帧
ffmpeg -i input.mp4 -ss 0 -frames:v 1 -q:v 2 output.jpg
```

参数：

- `-frames:v 1`：取第一张视频帧
- `-q:v 2`：JPEG 质量（1 最好，31 最差）

### 添加静音音轨

```sh
ffmpeg -y -i input.mp4 -f lavfi -i anullsrc=channel_layout=stereo:sample_rate=44100 -shortest -c:v copy -c:a aac -movflags +faststart output.mp4
```

### 给 MP4 添加内嵌封面

```sh
ffmpeg -y -i input.mp4 -i cover.jpg -map 0:v -map 0:a? -map 1:v -c copy -c:v:1 mjpeg -disposition:v:1 ttached_pic output.mp4
```

### 替换第一帧

```bash
ffmpeg \
-y \
-loop 1 -i cover.jpg \
-i output2.mp4 \
-filter_complex "
[0:v]scale=584:620,setsar=1,format=yuv420p,trim=duration=1,setpts=PTS-STARTPTS[v0];
[1:v]setsar=1,format=yuv420p,setpts=PTS-STARTPTS[v1];
[v0][v1]concat=n=2:v=1:a=0[v]
" \
-map "[v]" \
-map "1:a?" \
-c:v libx264 \
-c:a copy \
output3.mp4
```
