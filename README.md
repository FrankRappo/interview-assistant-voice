# v26_assistant — VOICE

Голосовой ассистент-суфлёр для собеседований — версия, которая **отвечает голосом через динамики**.

Отдельно от [interview-assistant](https://github.com/FrankRappo/interview-assistant) (текстовая версия).
База: финальная текстовая версия v26_assistant от 2026-09-25 14:17 (свежие промпты,
быстрая модель второй панели, улучшенный загрузчик ключей) + playback-блоки озвучки
из сборки `bak_revert_playback` 12:15.

## Отличие от текстовой версии

Ответы Gemini Live воспроизводятся вслух через системные динамики:

- класс `ModelAudioPlayer` — проигрывание PCM-чанков (`model_turn.parts[].inline_data`) через pyaudio;
- голос модели — prebuilt `Zephyr` (мультиязычный RU+EN);
- текстовые панели 2×2 при этом работают как обычно (транскрипция ответа всё равно выводится).

Осторожно: голос ассистента слышен всем вокруг — для скрытного суфлирования на реальном
собеседовании используйте текстовую версию из основного репозитория.

## Ключи API

Вшитых ключей в коде нет. Создайте `assistant_secrets.py` рядом со скриптом:

```python
API_KEY_GEMINI = "your-gemini-key"
API_KEY_GEMMA = "your-gemma-key"   # можно продублировать GEMINI-ключ

# Прокси для доступа к Google API из регионов, где API недоступен напрямую.
# Пустая строка или отсутствие строки = работать напрямую.
API_PROXY = "socks5://127.0.0.1:8081"
```

Либо переменные окружения `GEMINI_API_KEY` / `GEMMA_API_KEY`.

Не публикуйте `assistant_secrets.py` на GitHub — он в `.gitignore`.

## Запуск

```powershell
python -m pip install pyaudiowpatch sounddevice google-genai numpy pynput socksio
python v26_assistant.py
```

1. Открыть программу, нажать `CONNECT`.
2. Говорить вопрос в микрофон.
3. Ответ приходит и текстом в панели, и голосом в динамики.

## Модели

```python
GEMINI_MODEL = "models/gemini-2.5-flash-native-audio-preview-12-2025"
GEMMA_MODEL  = "models/gemini-2.5-flash"
```

## Возможности

- Распознавание речи: микрофон (sounddevice / WDM-KS fallback) + WASAPI loopback (pyaudiowpatch), замиксованы.
- Interview Mode — тренировка Senior SysAdmin / DevOps / SRE.
- Хоткей Space — мьют микрофона.
- Health monitor и автопереподключение сессии (session resumption).
