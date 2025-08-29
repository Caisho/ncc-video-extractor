# ncc-video-extractor

Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.

This is a Python utility project that bundles `yt-dlp` (video downloading) and `openai-whisper` (audio transcription) CLI tools for downloading and transcribing videos, particularly from NCC sources.

## Working Effectively

**CRITICAL: NETWORK REQUIRED** - Both yt-dlp and whisper require internet access to function. They will fail gracefully in restricted environments.

### Bootstrap, Build, and Setup
- Install uv package manager: `pip install uv`
- Create virtual environment: `uv venv` (downloads Python 3.11 automatically - takes ~2 minutes)
- Activate environment: `source .venv/bin/activate` (Linux/Mac) or `.venv\Scripts\activate` (Windows)
- Install dependencies: `uv pip install -e .` -- takes 45 seconds. NEVER CANCEL. Set timeout to 120+ seconds.
- Install system dependency: `sudo apt-get install ffmpeg` (Linux) or `brew install ffmpeg` (macOS) -- takes 3-5 minutes. NEVER CANCEL. Set timeout to 600+ seconds.

### First-Time Whisper Setup
- **CRITICAL**: Whisper downloads models on first use (40MB-1.5GB per model). This requires internet access.
- Test with smallest model: `whisper --help` (validates installation)
- Download tiny model: `whisper any_audio_file.mp3 --model tiny` -- downloads ~40MB model, takes 1-2 minutes. NEVER CANCEL.
- Available models: tiny, base, small, medium, large (tiny=40MB, base=140MB, small=460MB, medium=1.4GB, large=2.9GB)

### Validation Commands
Run these to ensure environment is working:
- `yt-dlp --version` (should show version like 2025.8.27)
- `whisper --help` (should show usage information)
- `ffmpeg -version` (should show ffmpeg information)

### No Build Process
This project has NO traditional build, test, or lint processes. It only manages dependencies for CLI tools.

## Usage Workflows

### Downloading Videos
- **List available formats**: `yt-dlp -F <VIDEO_URL>` or `yt-dlp --list-formats <VIDEO_URL>`
- **Basic download**: `yt-dlp <VIDEO_URL>`
- **Best quality**: `yt-dlp -f best <VIDEO_URL>`
- **Password-protected**: `yt-dlp --video-password PASSWORD <VIDEO_URL>`
- **Audio extraction**: `yt-dlp -f bestaudio --extract-audio --audio-format mp3 <VIDEO_URL>`
- **Combined video+audio**: `yt-dlp -f "bestvideo+bestaudio/best" <VIDEO_URL>`

### Transcribing Audio
- **Basic transcription**: `whisper your_video.mp4 --model medium` -- medium model recommended for accuracy/speed balance
- **Specify output directory**: `whisper your_video.mp4 --model medium --output_dir /path/to/output`
- **Specific output format**: `whisper your_video.mp4 --model medium --output_format txt`
- **Different models**: `whisper your_video.mp4 --model tiny|base|small|medium|large`

### Combined Workflow Example
```bash
# 1. Download video
yt-dlp -f best <VIDEO_URL>

# 2. Extract audio (if needed)
yt-dlp -f bestaudio --extract-audio --audio-format mp3 <VIDEO_URL>

# 3. Transcribe audio
whisper downloaded_audio.mp3 --model medium --output_format txt
```

## Common Issues and Solutions

### Network-Related Failures
- `[Errno -5] No address associated with hostname`: No internet access - tools require network connectivity
- `Unable to download webpage`: URL inaccessible or network restrictions
- `URLError`: Whisper model download failed - retry with internet access

### Expected Timing
- Environment setup: 1-2 minutes (uv venv) + 45 seconds (dependencies) + 3-5 minutes (ffmpeg)
- First whisper model download: 1-5 minutes depending on model size
- Video downloads: Varies by file size and connection speed
- Transcription: ~10% of audio duration for medium model (5-minute audio = ~30 seconds processing)

## Repository Structure Reference

```
.
├── README.md              # User documentation
├── pyproject.toml         # Dependency specifications (yt-dlp, openai-whisper)
├── uv.lock               # Dependency lock file
├── .python-version       # Python 3.11 requirement
├── .gitignore            # Excludes data/ directory
└── ncc_video_extractor.egg-info/  # Package metadata (auto-generated)
```

### Key Files
- **pyproject.toml**: Contains exact dependency versions
  - `yt-dlp[curl-cffi,default]>=2024.10.22`
  - `openai-whisper>=20240930`
- **README.md**: User-facing documentation with usage examples
- **No source code files**: This is purely a dependency management project

## Environment Notes
- **Python Version**: 3.11 (required by dependencies, auto-installed by uv)
- **No CI/CD**: No GitHub Actions workflows
- **No Tests**: No test suite (nothing to test - pure dependency bundling)
- **No Linting**: No code style requirements (no custom code)

## Development Workflow
1. Make dependency changes in `pyproject.toml` if needed
2. Update `README.md` with new usage examples
3. Test that dependencies install correctly: `uv pip install -e .`
4. Validate CLI tools work: `yt-dlp --version && whisper --help`
5. No additional validation required (no custom code to test)