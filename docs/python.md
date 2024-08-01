# python

## 打印项目结构
- 代码
```python
    import os
    def list_files(startpath):
        output = []
        for root, dirs, files in os.walk(startpath):
            level = root.replace(startpath, '').count(os.sep)
            indent = '│   ' * level
            output.append(f"{indent}├── {os.path.basename(root)}/")
            subindent = '│   ' * (level + 1)
            for i, f in enumerate(files):
                if i == len(files) - 1:
                    output.append(f"{subindent}└── {f}")
                else:
                    output.append(f"{subindent}├── {f}")
        return output
    if __name__ == "__main__":
        directory_path = input("请输入目录路径: ")
        result = list_files(directory_path)
        output_file = "./OS_tools/directory_structure.txt"
        with open(output_file, 'w', encoding='utf-8') as f:
            f.write(f"目录结构: {directory_path}\n")
            f.write("=" * 50 + "\n")
            for line in result:
                f.write(line + "\n")
        print(f"目录结构已保存到 {output_file}")
```
- 效果
  ![image.png](https://ossk.cc/file/17f6727de90a378c36198.png ':zoom')

## 使用 ffmpeg 从 `.mfv` 中提取音频

    import subprocess
    def extract_audio(input_file, output_file):
        try:
            subprocess.run(['ffmpeg', '-i', input_file, '-vn', '-acodec', 'copy', output_file], check=True)
            print("音频提取完成")
        except subprocess.CalledProcessError as e:
            print("提取音频时出错:", e)
    """ 指定输入文件和输出文件的路径 """
    input_file = r'C:\Users\10023\Videos\2024-05-03 18-31-34.mkv'
    output_file = r'C:\Users\10023\Videos\output_audio.mp3'
    """ 调用函数提取音频 """
    extract_audio(input_file, output_file)

## `.mp3`转`.pcm`

    from pydub import AudioSegment

    def mp3_to_pcm(input_mp3, output_pcm):
        # Load the MP3 file
        sound = AudioSegment.from_mp3(input_mp3)
        # Export the sound as raw PCM
        sound.export(output_pcm, format="s16le")
    input_mp3_file = "./audio/input.mp3"
    output_pcm_file = "./audio/output.pcm"
    mp3_to_pcm(input_mp3_file, output_pcm_file)

## `.pcm`转`.silk`

    import os, pilk
    from pydub import AudioSegment
    def convert_to_silk(media_path: str) -> str:
        """将输入的媒体文件转出为 silk, 并返回silk路径"""
        media = AudioSegment.from_file(media_path)
        pcm_path = os.path.basename(media_path)
        pcm_path = os.path.splitext(pcm_path)[0]
        silk_path = pcm_path + '.silk'
        pcm_path += '.pcm'
        media.export(pcm_path, 's16le', parameters=['-ar', str(media.frame_rate), '-ac', '1']).close()
        pilk.encode(pcm_path, silk_path, pcm_rate=media.frame_rate, tencent=True)
        return silk_path
    def main():
        media_file_path = "./audio/input.mp3"
        silk_file_path = convert_to_silk(media_file_path)
        print(f"转换完成！生成的 Silk 文件路径为: {silk_file_path}")
    if __name__ == "__main__":
        main()
## `.wav`转`.mp3`

    from pydub import AudioSegment
    def wav_to_mp3(input_wav, output_mp3):
        # Load the WAV file
        sound = AudioSegment.from_wav(input_wav)
        # Export the sound as MP3
        sound.export(output_mp3, format="mp3")
    input_wav_file = "./audio/input.wav"
    output_mp3_file = "./audio/output.mp3"
    wav_to_mp3(input_wav_file, output_mp3_file)

## 使用 python 操作 Redis
- 代码
    ```terminal
    import json
    import redis

    json_data = [
        [
            118.897299,
            32.02599
        ],
        [
            118.893303,
            32.026212
        ],
        [
            118.895235,
            32.028178
        ],
        [
            118.894384,
            32.032069
        ]
    ]
    redis_client = redis.StrictRedis(host='your.ip', port=6379, db=00,password="123456")
    for index, coords in enumerate(json_data):
        i=1;
        i = i+1;
        # 生成唯一的键名
        key = f'loc+i:{index}'
        # 将经纬度对转换为JSON字符串
        value = json.dumps(coords)
        # 将键值对存入Redis
        redis_client.set(key, value)
        print("JSON数据已存入Redis的Hash中。")
    ```



## pip 使用镜像下载
- 清华源
```terminal
    >|pip install <package_name> -i https://pypi.tuna.tsinghua.edu.cn/simple
```
- 阿里源
```terminal
    >|pip install <package_name> -i https://pypi.tuna.tsinghua.edu.cn/simple
```
- 中科大
```terminal
    >|pip install <package_name> -i https://pypi.mirrors.ustc.edu.cn/simple/
```