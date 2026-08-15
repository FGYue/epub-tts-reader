# EPUB TTS Reader

A browser-based EPUB reader with synchronized text-to-speech, text highlighting, reading-position memory, and optional AI-generated voices.

## Current Version

**EPUB TTS Reader 2.2**

The 2.2 release focuses on:

- Local EPUB loading
- Browser-based EPUB parsing
- Chapter navigation
- Character-level text highlighting
- Reading-position memory
- Browser/system TTS
- Optional Hugging Face Qwen3-TTS integration
- Mobile browser support
- Single-file deployment

## Features

- Load EPUB files directly in the browser
- No server is required for basic EPUB reading
- EPUB ZIP parsing implemented inside the application
- EPUB `container.xml` and OPF parsing
- Spine-based chapter extraction
- Chapter table of contents
- Start reading from any selected character
- Character-level highlighting during speech
- Click any character to move the reading position
- Save the current reading position locally
- Restore the previous reading position
- Browser/system Speech Synthesis support
- Optional Hugging Face Qwen3-TTS integration
- Adjustable speech speed
- Mobile-friendly interface
- Designed for iPhone, iPad and Android browsers
- Distributed as a single HTML file

## How It Works

```text
                    EPUB File
                        │
                        ▼
                  ZIP Reader
                        │
                        ▼
                EPUB container.xml
                        │
                        ▼
                     OPF
                        │
                        ▼
                    Spine
                        │
                        ▼
                  XHTML Chapters
                        │
                        ▼
               Character Segmentation
                        │
                        ▼
                      TTS
                 ┌──────┴──────┐
                 │             │
                 ▼             ▼
            System TTS    Qwen3-TTS
                 │             │
                 └──────┬──────┘
                        ▼
                   Audio Output
                        │
                        ▼
                Current Character
                   Highlighting
```

The EPUB itself is processed locally in the browser.

## Local EPUB Processing

The basic EPUB reader does not require a server.

The application reads the selected EPUB file using the browser's File API and parses its ZIP structure locally.

The implementation includes:

- ZIP End of Central Directory detection
- ZIP Central Directory parsing
- Stored ZIP entries
- Deflate-compressed ZIP entries
- `META-INF/container.xml`
- OPF manifest
- OPF spine
- XHTML chapter extraction

The reader does not upload the entire EPUB to a remote server merely to display the book.

## Reading Position

The reader remembers the current reading position using browser `localStorage`.

The saved state includes:

```text
chapter
character
```

This allows the user to stop playback and return to approximately the same location later.

Clicking a character in the reader also changes the saved reading position.

## Text Highlighting

Readable text is divided into individual character spans.

During TTS playback, the current character receives an active highlight.

With browser/system TTS, speech boundary events are used when available.

With generated audio, the current position is estimated from the elapsed audio duration.

The exact synchronization behavior therefore depends on the TTS engine and browser.

# TTS

## Browser / System TTS

The default TTS engine uses the browser's native:

```text
SpeechSynthesis
SpeechSynthesisUtterance
```

No external API is required.

The available voices depend on the operating system and browser.

For Chinese, different devices may provide different Traditional Chinese and Simplified Chinese voices.

For this reason, TTS quality can vary substantially between devices.

## Hugging Face Qwen3-TTS

The application optionally supports Qwen3-TTS through a Hugging Face Gradio Space.

The default Space is:

```text
Qwen/Qwen3-TTS
```

The reader calls the Space's Gradio API instead of bundling the Qwen3-TTS model or source code into this repository.

Qwen3-TTS supports multilingual speech generation and voice/style instructions. The current Hugging Face model/Space licensing information should be checked at the time of use.

### Important

The Qwen3-TTS model and Hugging Face Space are external services/components.

They are not redistributed as part of this repository.

Users should check the current terms, availability and licensing information of the external service before using it.

## Gradio Client

The optional Hugging Face integration loads the Gradio JavaScript client at runtime.

The client is not bundled into this repository.

The upstream package is:

```text
@gradio/client
```

The application loads the client from jsDelivr rather than copying the package source into this repository.

The exact CDN version should remain pinned for reproducible releases.

## External Network Requests

Basic EPUB reading does not depend on Hugging Face.

The Hugging Face integration is only used when the user selects the Qwen3-TTS engine and starts TTS playback.

The general flow is:

```text
Local EPUB
    │
    ▼
Local browser processing
    │
    ▼
Text segment
    │
    │ only when HF TTS is enabled
    ▼
Hugging Face Gradio Space
    │
    ▼
Generated audio
    │
    ▼
Browser playback
```

Because the AI TTS service is remote, its operation depends on:

- Internet connectivity
- Hugging Face availability
- Space availability
- Gradio API compatibility
- Browser network policies
- CORS and security restrictions
- Remote inference speed

## Running the Application

The application is intentionally distributed as a single HTML file.

```text
epub-tts-reader.html
```

For basic EPUB reading, the file can be opened directly in a modern browser.

For external TTS testing, serving the file over HTTP/HTTPS is recommended because browser security policies can behave differently for `file://` and `http://` / `https://`.

## Mobile Browsers

The application was developed with mobile browser use in mind.

Examples include:

- Safari on iPhone
- Safari on iPad
- Microsoft Edge on Android
- Other modern Chromium-based browsers

Browser implementations of Speech Synthesis differ considerably.

In particular, the available Chinese voices and their quality are controlled by the operating system/browser environment rather than by this application.

## Background Playback

The application uses normal browser audio and speech APIs.

Background and lock-screen behavior depends on the browser and operating system.

A browser may allow audio playback to continue after the screen is locked while restricting JavaScript execution in the background.

Therefore, continuous background JavaScript execution is not guaranteed.

This is one of the areas planned for improvement in future versions.

# Known Limitations

## 1. Browser TTS Quality

System TTS quality varies between devices.

For example, Android browsers may provide noticeably different Chinese voices from iPhone Safari.

The application cannot guarantee identical voice quality across devices.

## 2. Traditional / Simplified Chinese

The selected language does not necessarily guarantee a specific voice.

The final voice is determined by the browser and operating system's available speech engines.

## 3. AI TTS Generation Time

Remote AI TTS generation may take longer than the audio playback time of a sentence.

This can result in a pause before the next sentence is generated.

A future TTS queue is planned to reduce this problem.

## 4. Background Execution

Mobile browsers can suspend JavaScript when the application is backgrounded or the screen is locked.

Background playback therefore depends on browser and operating-system behavior.

## 5. EPUB Compatibility

The reader implements a lightweight EPUB parser rather than a complete EPUB rendering engine.

Some complex EPUB files may not render correctly, especially books using:

- unusual ZIP structures
- advanced XHTML
- complex CSS
- JavaScript-based EPUB content
- SVG-heavy layouts
- uncommon EPUB packaging structures

The application is primarily intended for text-oriented EPUB books.

# Roadmap

- [ ] TTS generation queue
- [ ] Pre-generation of upcoming sentences
- [ ] Smoother AI TTS transitions
- [ ] Better Traditional Chinese voice selection
- [ ] TTS provider abstraction
- [ ] Custom TTS API support
- [ ] Additional TTS providers
- [ ] Media Session API controls
- [ ] Improved lock-screen playback
- [ ] Background ambience
- [ ] Rain sound
- [ ] Fireplace sound
- [ ] High-pass filter
- [ ] Low-pass filter
- [ ] Background audio ducking
- [ ] Improved mobile browser compatibility

These features are intentionally kept separate from the core EPUB reader.

# TTS Provider Architecture

The long-term design is to keep the EPUB reader independent from any particular TTS provider.

The intended architecture is:

```text
                   EPUB Reader
                        │
                        ▼
                 Text Segmentation
                        │
                        ▼
                   TTS Manager
                        │
          ┌─────────────┼─────────────┐
          │             │             │
          ▼             ▼             ▼
      System TTS    Qwen3-TTS     Custom API
          │             │             │
          └─────────────┼─────────────┘
                        ▼
                   Audio Queue
                        │
                        ▼
                  Audio Playback
```

This should make it possible to add another TTS service without rewriting the EPUB parser.

# Copyrighted EPUB Content

EPUB TTS Reader is a reading tool.

No commercial books or copyrighted EPUB content are included in this repository.

Users are responsible for ensuring that EPUB files and other content they use with the reader are lawfully obtained and used.

# Third-Party Components and Services

The core EPUB reader is implemented as a standalone HTML application.

The repository does not bundle the Qwen3-TTS model, Qwen3-TTS source code, or the Gradio client library.

Optional AI TTS functionality uses:

### Gradio JavaScript Client

```text
@gradio/client
```

The client is loaded at runtime from jsDelivr.

Please refer to the upstream package for its current license and terms.

### Hugging Face Qwen3-TTS

```text
Qwen/Qwen3-TTS
```

The model and Space are external components and retain their own licenses and terms.

Their availability and behavior may change independently of this project.

# Development Notes

The project was developed iteratively with a strong focus on real-device testing.

Particular attention was given to:

- iPhone Safari
- Android browsers
- EPUB files with different internal structures
- Chinese text
- Character-level reading position
- TTS failures that should not prevent EPUB loading

The Hugging Face integration is intentionally isolated from the EPUB loading path so that failure of an external TTS service does not prevent the user from opening an EPUB.

# Credits

This project was developed by the author with the assistance of **Luna** (ChatGPT), an AI development companion.

Luna contributed to implementation ideas, debugging, architecture discussions and iterative development throughout the project.

The project direction, feature decisions, testing and final integration belong to the author.

# Status

**Active development**

Current release:

**EPUB TTS Reader 2.2**

The EPUB reader is functional.

TTS provider integration, AI generation latency and mobile background playback remain areas of ongoing development.

# Future Direction

A separate project may eventually explore OCR-based TTS for image-based content such as comics and manga.

That work is intentionally separate from EPUB TTS Reader so that this project can remain focused on EPUB reading and document-based TTS.
