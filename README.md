# VirtualCam

**Câmera virtual universal** — transforma qualquer vídeo em uma webcam virtual no Linux e Windows.

```
 _    ___      __              ________
| |  / (_)____/ /___  ______ _/ / ____/___ _____ ___
| | / / / ___/ __/ / / / __`/ / /   / __`/ __`__ \
| |/ / / /  / /_/ /_/ / /_/ / / /___/ /_/ / / / / / /
|___/_/_/   \__/\__,_/\__,_/_/\____/\__,_/_/ /_/ /_/
```

## Funcionalidades

- **Cross-platform** — Linux (v4l2loopback) e Windows (softcam / Unity Capture / OBS)
- **Sem OBS Studio necessário** no Windows — usa softcam (MIT) ou Unity Capture (MIT)
- **Playlist** — modo sequencial ou aleatório com auto-avanço
- **Loop** — repetir vídeo infinitamente
- **FPS configurável** — 1 a 120 quadros por segundo
- **Overlay de texto** — texto personalizado sobre o vídeo
- **Seletor gráfico de pasta** — tkinter nativo no Linux e Windows
- **Hotkeys** — troque vídeo, pasta, resolução e FPS sem parar o stream
- **Config persistente** — salva suas preferências em `~/.virtualcam/config.json`
- **Modo CLI** — scripts e automação com argumentos de linha de comando

## Instalação

### Linux

```bash
# dependências do sistema
sudo apt install ffmpeg v4l2loopback-dkms v4l2loopback-utils

# clonar e executar
git clone https://github.com/seuusuario/virtualcam.git
cd virtualcam
chmod +x virtualcam
./virtualcam
```

### Windows

```bash
# 1. Instalar ffmpeg (https://ffmpeg.org/download.html)
# 2. Escolher um backend de câmera virtual:

# Opção A — softcam (recomendado, MIT, sem OBS)
pip install softcam numpy
# Baixar driver: https://github.com/tshino/softcam

# Opção B — Unity Capture + pyvirtualcam (MIT, sem OBS)
pip install pyvirtualcam numpy
# Baixar e registrar: https://github.com/schellingb/UnityCapture

# Opção C — OBS Virtual Camera (fallback automático)
pip install pyvirtualcam numpy
# Baixar plugin: https://obsproject.com
```

### macOS

```bash
pip install pyvirtualcam numpy
```

## Uso

### Modo interativo

```bash
./virtualcam
```

1. Escolha a pasta com vídeos (explorador gráfico)
2. Selecione a resolução
3. Configure o FPS
4. Escolha o vídeo
5. Pronto! A câmera virtual está ativa

### Modo CLI

```bash
./virtualcam --dir ~/Videos --video video.mp4 --resolution 1920x1080 --fps 30 --loop --shuffle
```

### Argumentos

| Argumento | Descrição |
|-----------|-----------|
| `-d, --dir DIR` | Pasta com vídeos |
| `-v, --video VIDEO` | Arquivo de vídeo específico |
| `-r, --resolution WxH` | Resolução (ex: `1920x1080`) |
| `--fps FPS` | Quadros por segundo (1-120) |
| `--loop` | Repetir vídeo |
| `--no-loop` | Não repetir |
| `--shuffle` | Modo aleatório |
| `--overlay TEXTO` | Overlay de texto |
| `--cli` | Modo CLI (sem menus interativos) |
| `--list` | Listar vídeos da pasta |
| `--devices` | Listar dispositivos de vídeo |

## Hotkeys

Durante a transmissão:

| Tecla | Ação |
|-------|------|
| `T` | Trocar vídeo (menu interativo) |
| `N` | Próximo vídeo (modo shuffle) |
| `L` | Alternar loop |
| `P` | Trocar pasta (explorador gráfico) |
| `F` | Alterar FPS |
| `R` | Alterar resolução |
| `Q` | Sair |

## Configuração

As configurações são salvas automaticamente em `~/.virtualcam/config.json`:

```json
{
  "video_dir": "/home/user/Videos",
  "device": "/dev/video2",
  "width": 720,
  "height": 1280,
  "fps": 30,
  "loop": true,
  "shuffle": false,
  "overlay_text": ""
}
```

## Arquitetura

```
                   ┌──────────────────────┐
                   │     virtualcam       │
                   │   (Python script)    │
                   └──────┬───────────────┘
                          │
            ┌─────────────┴──────────────┐
            │                            │
     ┌──────▼──────┐            ┌────────▼────────┐
     │   Linux      │           │    Windows      │
     │ v4l2loopback │           │ softcam /       │
     │ + ffmpeg     │           │ Unity Capture   │
     │ (direto)     │           │ + ffmpeg (pipe) │
     └──────────────┘           └─────────────────┘
```

### Linux

O ffmpeg escreve diretamente no dispositivo v4l2loopback.

### Windows

O ffmpeg decodifica o vídeo e envia frames raw via pipe. O backend (softcam ou pyvirtualcam) lê do pipe e envia frame a frame para a câmera virtual registrada no sistema.

## Licença

MIT — sinta-se livre para usar, modificar e compartilhar.
