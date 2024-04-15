# Faster Whisper Real Time Server
This repo originates from [whisper_mic](https://github.com/mallorbc/whisper_mic) by Blake Mallory. The main purpose of this repo is to run the [faster-whisper](https://github.com/SYSTRAN/faster-whisper) backend as a remote server on Linux/Windows and use the service from a Windows desktop through gRPC.


## Installation

- If you want to transcribe the sound output, please use audio-mixer driver or 3rd-party application (e.g. `VB-AUDIO`, `Voicemeter`, ...) to generate a vitual microphone which records the sound output in your device.
- If you want to use nvidia `cuda` to run the audio model, make sure you have installed the [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads).
- Install the [PyTorch](https://pytorch.org/get-started/locally/) before you run the pip command below.
    - Make sure you select the correct Platform (Linux/Windows) and the corresponding cuda version in your environment.
- Install the module from pip and choose the platform your device is.
```
pip install requirements_for_{Ubuntu/Windows}.txt
```
- **NOTICE**: if you're using Ubuntu, you might need to run the following command after the above finished:
```bash
export LD_LIBRARY_PATH=`python3 -c 'import os; import nvidia.cublas.lib; import nvidia.cudnn.lib; print(os.path.dirname(nvidia.cublas.lib.__file__) + ":" + os.path.dirname(nvidia.cudnn.lib.__file__))'`
```
- If you encountered the environment problem, make sure your backend meets the [requirements](https://github.com/SYSTRAN/faster-whisper?tab=readme-ov-file#requirements) for [faster-whisper](https://github.com/SYSTRAN/faster-whisper).


## Usage

### Client
- Run `python client.py --help` to see all the options.
- Run `python client.py --list_devices` to get the available `mic_index` from your microphones.
- Use `--grpc_address` option to indicate the address remote gRPC server.
    - Or transcribe **locally** without this option.
        - Make sure you indicate `--model`, `--device`, and `--precision` option when you run the transcription locally.
- The example command to use the remote gRPC server with `192.168.x.y:50021`:
    - Here I use the `--language ja` to indicate the target language is Japanese.
        - Or you can use multilingual model without this option.
```bash
python client.py --language ja --mic_index 4 --grpc_address 192.168.x.y:50021
```
- The example command to run the transcription **locally**:
```bash
python client.py --language ja --mic_index 4 --model large-v3 --device cuda --precision float16
```
- You can see the available models and languages in [here](#available_models_and_languages).


### Server
- Run `python server.py --help` to see all the options.
- Use `--model`, `--device`, and `--precision` option to run on your prefered settings.
- Use `--port_number` to indicate a port number to listen.
- The example command the run a server at port `50021`:
```bash
python grpc_server.py --model large-v3 --device cuda --precision float16 --port 50021
```
- You can see the available models and languages in [here](#available_models_and_languages).


## Reminder
- If you want to run the en-only transcription, try `--model distil-large-v3`.
- If you encountered the install problem with pip module `pyaudio` in Ubuntu, try:
```bash
sudo apt install portaudio19-dev python-all-dev python3-all-dev
```
- If you encountered pip wheel issues, try:
```bash
pip install --upgrade pip setuptools
```
- There may be some `cuda` environment problems in Ubuntu, please check the [requirements](https://github.com/SYSTRAN/faster-whisper?tab=readme-ov-file#requirements) for details or use a docker container instead.

<!----><a name="available_models_and_languages"></a>
## Available models and languages

- [models](https://github.com/SYSTRAN/faster-whisper/blob/master/faster_whisper/utils.py)
- [language codes](https://github.com/SYSTRAN/faster-whisper/blob/master/faster_whisper/tokenizer.py)
    - [codes mapping](https://github.com/openai/whisper/blob/main/whisper/tokenizer.py)
