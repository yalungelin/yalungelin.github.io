[ffmpeg安装教程](https://blog.csdn.net/csdn_yudong/article/details/129182648)
使用 `ffmpeg` 抽帧是个高效的方法，具体命令可以根据你的需求进行调整。以下是几种常见的抽帧方式：
### 1. **按固定间隔抽帧**
如果你希望每秒抽取一帧，可以使用：
```bash
ffmpeg -i input.mp4 -vf "fps=1" output_%04d.png
```
这会以 **1 FPS**（每秒 1 帧）从 `input.mp4` 抽取帧，并保存为 `output_0001.png`、`output_0002.png`...

如果你想以更高的频率抽帧，比如 **每秒 5 帧**，可以改成：
```bash
ffmpeg -i input.mp4 -vf "fps=5" output_%04d.png
```

---

### 2. **按时间间隔抽帧**
如果你想 **每隔 X 秒** 抽取一张图片，比如 **每 2 秒抽一帧**：
```bash
ffmpeg -i input.mp4 -vf "select='not(mod(t,2))'" -vsync vfr output_%04d.png
```
这里 `mod(t,2)` 让 ffmpeg 每 2 秒保存一帧。

---

### 3. **提取关键帧**
如果你只想提取关键帧（关键帧通常包含更多信息）：
```bash
ffmpeg -i input.mp4 -vf "select='eq(pict_type,PICT_TYPE_I)'" -vsync vfr output_%04d.png
```

---

### 4. **抽取指定时间段的帧**
如果你只想抽取视频 `start_time` 到 `end_time` 之间的帧，比如从 **10 秒到 30 秒**：
```bash
ffmpeg -i input.mp4 -vf "fps=5" -ss 10 -to 30 output_%04d.png
```

---

### 5. **调整输出图片质量**
默认情况下，FFmpeg 会按原始质量保存图像，但如果你希望压缩图像（减少存储空间），可以调整质量：
```bash
ffmpeg -i input.mp4 -vf "fps=5" -q:v 2 output_%04d.jpg
```
`-q:v` 控制 JPEG 质量，范围 `2`（高质量）到 `31`（低质量）。

---

### 6. **保存为 `.tif` 格式**
如果你需要 `.tif` 格式：
```bash
ffmpeg -i input.mp4 -vf "fps=5" output_%04d.tif
```
