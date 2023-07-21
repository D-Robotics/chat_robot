# 功能介绍

智能语音聊天机器人由智能语音，GPT交互和文本转语音组成，实现人和机器人语音聊天功能。

# 物料清单

| 机器人名称 | 生产厂家 | 参考链接                                                        |
| :--------- | -------- | --------------------------------------------------------------- |
| RDK X3     | 多厂家   | [点击跳转](https://developer.horizon.cc/rdkx3)                  |
| 麦克风板   | 微雪电子 | [点击跳转](https://www.waveshare.net/shop/Audio-Driver-HAT.htm) |

# 使用方法

## 准备工作

在体验之前，需要具备以下基本条件：

- 地平线RDK已烧录好地平线提供的Ubuntu 20.04系统镜像
- 已拥有OpenAI API key，并可正常访问OpenAI API。
- 音频板正确连接到RDK X3，耳机接口接上耳机或音响

连接步骤：

1. 将麦克风板连接到地平线RDK X3 40PIN GPIO 接口上，连接后实物如下图：

    ![circle_mic_full](./imgs/circle_mic_full.png)

2. 耳机接口接上耳机或音响

## 安装功能包

启动RDK X3后，通过终端SSH或者VNC连接机器人，复制如下命令在RDK的系统上运行，完成相关Node的安装。

```bash
sudo apt update
sudo apt install -y tros-chat-robot
```

## 运行智能聊天机器人

1. 拷贝配置文件和加载音频驱动

    ```shell
    # 从tros.b的安装路径中拷贝出运行示例需要的配置文件，若已拷贝过则可忽略
    cp -r /opt/tros/lib/hobot_audio/config/ .
    cp -r /opt/tros/lib/hobot_gpt/config ./

    # 加载音频驱动，设备启动之后只需要加载一次
    bash config/audio.sh
    ```

    注意：加载音频驱动时确保无其他音频设备连接，例如USB麦克风或带麦克风功能的USB摄像头，否则会导致应用打开音频设备失败，报错退出。

2. 修改配置文件，只需修改一次
   1. 修改 *config/audio_config.json*，`asr_mode`字段为1。
   2. 修改 *config/gpt_config.json*，将`api_key`字段设置为自己的OpenAI API key。

3. 下载TTS模型
    首次运行需要下载模型文件并解压，详细命令如下：

    ```bash
    wget http://archive.sunrisepi.tech//tts-model/tts_model.tar.gz
    sudo tar -xf tts_model.tar.gz -C /opt/tros/lib/hobot_tts/
    ```

4. 配置tros.b环境和启动应用

    ```shell
    # 配置tros.b环境
    source /opt/tros/setup.bash

    # 屏蔽调式打印信息
    export GLOG_minloglevel=3

    #启动launch文件，运行前确认网络可访问OpenAI API
    ros2 launch chat_robot chat_robot.launch.py
    ```

    启动成功后，用户先使用唤醒词“地平线你好”唤醒机器人，然后紧接着和机器人聊天，片刻之后机器人语音应答。

# 原理简介

智能语音hobot_audio package开始运行之后，会从麦克风阵列采集音频，并且将采集到的音频数据送入语音智能算法SDK模块做智能处理，输出唤醒事件、命令词、ASR结果等智能信息，其中唤醒事件、命令词通过`audio_msg::msg::SmartAudioData`类型消息发布，ASR结果通过`std_msgs::msg::String`类型消息发布。

# 常见问题

1. 机器人无应答？

- 确认音频设备连接是否正常，并连接耳机或音响
- 确认是否加载音频驱动
- 确认加载音频驱动前是否已有音频设备连接
- *config/audio_config.json*，`asr_mode`字段为`1`
- 确认*config/gpt_config.json*，`api_key`字段设置正确
- 确认网络可访问OpenAI API
