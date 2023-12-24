# WSL-GUI-Docker-Minimum

![](https://github.com/Sunwood-ai-labs/WSL-GUI-Docker-Minimum/blob/main/docs/image.png)


このリポジトリは、Windows Subsystem for Linux (WSL) 上でDockerを通じてGUIアプリケーションを実行するための最小構成を提供します。このセットアップは、Windows上でDockerを使用してLinux GUIアプリケーションを実行するための最初の一歩です。

## Prerequisites
- WSL2がインストールされたWindows 10以降。
- WSL2統合が有効になっているDocker Desktop for Windows。
- Windows上で実行されているXサーバー（例：VcXsrv、Xming、または組み込みのWSLg）。

## Quick Start

### 1. リポジトリのクローン 

はじめに、このリポジトリをローカルマシンにクローンします。

```bash
git clone https://github.com/Sunwood-ai-labs/WSL-GUI-Docker-Minimum.git
cd WSL-GUI-Docker-Minimum

``` 
### 2. 環境設定 

`.env`ファイルを適切な値に設定します。提供されている`.env`ファイルにはデフォルト値が設定されており、WSLgを使用している場合はそのまま動作する可能性があります。 

### 3. Dockerコンテナのビルドと実行 

`docker-compose`を使用してコンテナをビルドし、実行します。

```bash
docker-compose up --build
```



正しくセットアップされていれば、画面上に`xeyes`アプリケーションのウィンドウが表示されるはずです。

## Configuration
- .env : Dockerコンテナに必要な環境変数を含みます。デフォルト値と異なる設定を使用する場合は、値を変更してください。 
- `DISPLAY`: Xサーバーに使用されるディスプレイサーバー。 
- `PULSE_SERVER`: 音声サポートのためのPulseAudioサーバーのアドレス。 
- `WAYLAND_DISPLAY`: Waylandディスプレイサーバー（WSLgを使用している場合は通常`wayland-0`です）。 
- `XDG_RUNTIME_DIR`: XDG準拠のアプリケーション用のランタイムディレクトリ。 
- docker-compose.yml : 環境変数、ボリューム、実行するコマンドを含むDockerサービスを定義します。 
- Dockerfile : Debianベースイメージを指定し、`xeyes`アプリケーションを提供する`x11-apps`をインストールします。


## File list
`docker-compose.yml`
```
version: '3.8'

services:
  gui-app:
    build:
      context: .
    image: gui-app-image
    environment:
      - DISPLAY=${DISPLAY}
      - PULSE_SERVER=${PULSE_SERVER}
      - WAYLAND_DISPLAY=${WAYLAND_DISPLAY}
      - XDG_RUNTIME_DIR=${XDG_RUNTIME_DIR}
    volumes:
      - /mnt/wslg:/mnt/wslg
      - /tmp/.X11-unix:/tmp/.X11-unix
    command: xeyes
```

`Dockerfile`

```
FROM debian:latest

RUN apt update -y &&\
    apt-get install x11-apps -y;
```

`.env`
```
DISPLAY=:0
PULSE_SERVER=/mnt/wslg/PulseServer
WAYLAND_DISPLAY=wayland-0
XDG_RUNTIME_DIR=/run/user/1000/

```