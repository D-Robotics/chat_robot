# Chat Robot

**chat_robot**基于hobot_audio、hobot_gpt和hobot_tts实现一个聊天机器人。

运行方式：

1. 配置hobot_audio

   ```bash
   cp -r /opt/tros/lib/hobot_audio/config/ .
   ```

   修改 config/audio_config.json，`asr_mode`字段为`1`

   ```bash
   bash config/audio.sh
   ```

2. 配置hobot_gpt

   ```bash
   cp -rf /opt/tros/lib/hobot_gpt/config ./
   ```

   修改 *config/gpt_config.json* ，将**api_key**字段设置为自己的ChatGPT API

   设置网络代理，确保可以访问ChatGPT

3. 配置hobot_tts
   首次运行需要下载模型文件解压，详细命令如下：

   ```bash
   wget http://archive.sunrisepi.tech//tts-model/tts_model.tar.gz
   sudo tar -xf tts_model.tar.gz -C /opt/tros/lib/hobot_tts/
   ```

4. 启动程序

   ```bash
   source /opt/tros/setup.bash

   # 屏蔽调式打印信息
   export GLOG_minloglevel=1

   ros2 launch chat_robot chat_robot.launch.py
   ```

   程序运行成功后，先使用唤醒词“地平线你好”唤醒机器人，紧接着说话，然后等待机器人语音回答。
