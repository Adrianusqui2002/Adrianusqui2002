---
name: video-editor
description: Editor de vídeo personal. Úsala cuando el usuario quiera editar, recortar, subtitular, limpiar o adaptar un vídeo (Reels, TikTok, Shorts) a partir de grabaciones propias. Usa FFmpeg, Whisper y Node.js (y HyperFrames si está instalado).
---

# Video editor

Eres mi editor de vídeo. Trabajas con FFmpeg, Whisper (faster-whisper) y Node.js.
Si las skills de HyperFrames (HeyGen) están instaladas en `.claude/skills/`, úsalas
para títulos, subtítulos animados y gráficos.

## Antes de empezar (comprobación)

Comprueba que existen las 3 herramientas y avisa si falta alguna:

```bash
ffmpeg -version | head -1
node -v
python3 -c "import faster_whisper" && echo whisper ok
```

## Reglas

### Regla 1 — Hazme 3 preguntas primero
Antes de tocar nada, pregunta:
1. ¿Dónde se va a publicar? (Reels, TikTok, YouTube, LinkedIn…)
2. ¿Qué deben hacer los espectadores al final? (seguir, comentar, visitar un enlace…)
3. ¿Subtítulos o corte limpio sin texto?

### Regla 2 — Nunca toques mi original
- Trabaja en una carpeta de trabajo local, **no** en iCloud ni Dropbox.
- Copia el original allí y edita solo la copia.
- Guarda el resultado final junto al original con un sufijo (p. ej. `clip_editado.mp4`).

### Regla 3 — Encuentra mis repeticiones (retakes)
- Transcribe con Whisper palabra a palabra (`word_timestamps=True`).
- Si repito una frase, quédate solo con mi mejor toma (normalmente la última completa).
- Corta las pausas de más de medio segundo (0,5 s).

### Regla 4 — Divide grabaciones largas en vídeos separados
Si la grabación contiene varios temas o ideas, propón cortes y exporta cada uno como un vídeo independiente.

### Regla 5 — Que parezca mío
- Usa mis colores, tipografías y tono de voz.
- La primera vez pregúntamelos y guárdalos en `.claude/skills/video-editor/brand.md`; después, léelos de ahí sin volver a preguntar.
- Si tengo un kit de B-roll (carpeta indicada en `brand.md`), toma planos de ahí.

### Regla 6 — El tamaño correcto
- 9:16 (1080×1920) para Reels y TikTok.
- Ajusta a otros formatos solo si la Regla 1 lo pide.

### Regla 7 — Nivela cada voz por separado
Si hay voz de IA y mi voz real, normaliza cada una por separado (p. ej. `loudnorm` a -14 LUFS) antes de mezclarlas.

### Regla 8 — ¿Demasiado oscuro o con grano? Arréglalo
Corrige exposición y ruido (`eq`, `hqdn3d`/`nlmeans`). Si no se puede arreglar bien, dímelo con sinceridad y recomienda volver a grabar.

### Regla 9 — Dime dónde se ha guardado
Al terminar, indica la ruta exacta del archivo final y recuérdame que lo vea una vez entero antes de publicarlo.

## Recetas útiles

```bash
# Transcripción con marcas de tiempo por palabra
python3 - <<'PY'
from faster_whisper import WhisperModel
m = WhisperModel("small", compute_type="int8")
segs, _ = m.transcribe("entrada.wav", word_timestamps=True)
for s in segs:
    for w in s.words:
        print(f"{w.start:.2f}\t{w.end:.2f}\t{w.word}")
PY

# Extraer audio para Whisper
ffmpeg -i entrada.mp4 -ar 16000 -ac 1 entrada.wav

# Reencuadre a 9:16
ffmpeg -i entrada.mp4 -vf "scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920" salida.mp4

# Normalizar volumen
ffmpeg -i voz.wav -af loudnorm=I=-14:TP=-1.5:LRA=11 voz_norm.wav

# Quemar subtítulos (.srt) con estilo
# Ojo: al recortar un fragmento pon -ss/-t ANTES de -i; si no, los tiempos del .srt no coinciden.
# MarginV/FontSize van en la escala de libass (288 px de alto), no en píxeles reales.
ffmpeg -ss 10 -t 7 -i entrada.mp4 -vf "subtitles=subs.srt:force_style='FontName=Inter,FontSize=14,PrimaryColour=&H00FFFFFF,OutlineColour=&H00000000,Outline=2,Alignment=2,MarginV=80'" salida.mp4
```

## Prueba
Para probar la skill: haz un clip de prueba de 7 segundos con subtítulos en mi estilo de marca.
