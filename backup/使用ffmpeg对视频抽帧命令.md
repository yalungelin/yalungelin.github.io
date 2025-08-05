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
### 7.**批量处理视频抽帧**
代码中视频分布在不同的文件夹中

```c

import os
import subprocess

def extract_all_frames_to_one_folder(input_root, output_dir, frame_rate=1):
    os.makedirs(output_dir, exist_ok=True)

    for folder_name in os.listdir(input_root):
        folder_path = os.path.join(input_root, folder_name)
        if not os.path.isdir(folder_path):
            continue

        video_files = [f for f in os.listdir(folder_path)
                       if f.lower().endswith(('.mp4', '.avi', '.mov', '.mkv'))]
        if not video_files:
            print(f"No video found in {folder_path}")
            continue

        video_file = video_files[0]
        video_path = os.path.join(folder_path, video_file)

        # 提取前缀：video_001 或 abc（你可以自定义）
        video_prefix = folder_name  # 或用 os.path.splitext(video_file)[0]

        # 临时输出到缓存文件夹
        temp_dir = os.path.join(output_dir, "_temp")
        os.makedirs(temp_dir, exist_ok=True)

        temp_pattern = os.path.join(temp_dir, "frame_%05d.jpg")

        # 执行 FFmpeg
        cmd = [
            'ffmpeg',
            '-i', video_path,
            '-vf', f'fps={frame_rate}',
            temp_pattern
        ]
        print(f"Extracting: {video_path}")
        subprocess.run(cmd)

        # 重命名临时帧并移动
        for i, frame_name in enumerate(sorted(os.listdir(temp_dir))):
            src = os.path.join(temp_dir, frame_name)
            dst_name = f"{video_prefix}_{frame_name}"
            dst = os.path.join(output_dir, dst_name)
            os.rename(src, dst)

    # 删除临时目录
    if os.path.exists(temp_dir):
        os.rmdir(temp_dir)

# 示例调用
extract_all_frames_to_one_folder(
    input_root='path/to/input_videos',      # 视频根目录
    output_dir='path/to/output_frames',     # 所有帧统一输出到这个目录
    frame_rate=1                            # 每秒提一帧
)

```
