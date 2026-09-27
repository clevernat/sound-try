# sound-try

Voice/TTS sandbox. Third-party projects are vendored as git submodules:

| Path | Upstream | License |
|---|---|---|
| `VoiceStudio/` | [debpalash/VoiceStudio](https://github.com/debpalash/VoiceStudio) | AGPL-3.0 |
| `kokoro/` | [hexgrad/kokoro](https://github.com/hexgrad/kokoro) | Apache-2.0 |

```sh
git clone --recurse-submodules https://github.com/clevernat/sound-try.git
cd sound-try
```

Prerequisites: [Bun](https://bun.sh), [uv](https://docs.astral.sh/uv/), Python 3.11, and `espeak-ng` (for Kokoro).

## VoiceStudio

```sh
cd VoiceStudio
bun install
bun run setup:api   # Python backend deps (uv sync)
bun run dev         # Electron desktop app; it starts the backend itself
```

Headless (API only): `bun run dev:api`, then open `http://localhost:3900/health`
and `http://localhost:3900/docs`. The engine models download on first use; you can also
install them with `POST /models/install`.

## Kokoro

```sh
cd kokoro
uv venv .venv --python 3.11
VIRTUAL_ENV=$PWD/.venv uv pip install -e . soundfile
.venv/bin/python - <<'PY'
from kokoro import KPipeline
import numpy as np, soundfile as sf
p = KPipeline(lang_code='a')  # American English
audio = np.concatenate([a for _, _, a in p("Hello from Kokoro.", voice='af_heart')])
sf.write('hello.wav', audio, 24000)
PY
```

The first run downloads the `hexgrad/Kokoro-82M` weights from Hugging Face.
