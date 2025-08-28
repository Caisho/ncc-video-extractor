# ncc-video-extractor

A comprehensive tool for downloading videos and transcribing audio. This project combines `yt-dlp` for video downloading (with special support for Vimeo videos, including password-protected content) and `openai-whisper` for high-quality audio transcription from video files.

## Installation

Ensure you have Python 3.11 or lower and `uv` installed. You can find `uv` installation instructions [here](https://github.com/astral-sh/uv).

**Additionally, `yt-dlp` often relies on `ffmpeg` for merging video and audio formats or for converting video formats. Please ensure `ffmpeg` is installed on your system. You can typically install it using your system's package manager (e.g., `sudo apt install ffmpeg` on Debian/Ubuntu, `brew install ffmpeg` on macOS).**

1.  Clone the repository.
2.  Create and activate a virtual environment using `uv`:
    ```sh
    uv venv
    source .venv/bin/activate # On Windows use `.venv\Scripts\activate`
    ```
    Alternatively, you can often run `uv` commands directly without explicitly activating the environment if `uv` is configured to detect the project's virtual environment.
3.  Install dependencies using `uv`:
    ```sh
    uv pip install -e .
    # or to sync with pyproject.toml
    # uv sync
    ```

## Usage

### Downloading Vimeo Videos

To download a Vimeo video, you can use the `yt-dlp` command.

**Basic Download:**
```sh
yt-dlp <VIDEO_URL>
```

**Password-Protected Videos:**

If the Vimeo video is password-protected, use the `--video-password` option:
```sh
yt-dlp --video-password YOUR_PASSWORD <VIDEO_URL>
```
Replace `YOUR_PASSWORD` with the actual password for the video.

**Specifying File Formats:**

You can specify the desired video format using the `-f` or `--format` option.

To list available formats for a video:
```sh
yt-dlp -F <VIDEO_URL>
# or
yt-dlp --list-formats <VIDEO_URL>
```

To download a specific format (e.g., mp4):
```sh
yt-dlp -f mp4 <VIDEO_URL>
```

To download the best available quality:
```sh
yt-dlp -f best <VIDEO_URL>
```

For more advanced format selection (e.g., best video and best audio, then merge):
```sh
yt-dlp -f "bestvideo+bestaudio/best" <VIDEO_URL>
```

**Extracting Audio:**

To download only the audio from a video and save it as an audio file:

```sh
yt-dlp -f bestaudio --extract-audio --audio-format m4a <VIDEO_URL>
```

*   `-f bestaudio`: Selects the best quality audio stream
*   `--extract-audio` or `-x`: Extracts only the audio
*   `--audio-format m4a`: Converts the audio to M4A format (you can also use `mp3`, `wav`, `opus`, etc.)

For example, to extract audio as MP3:
```sh
yt-dlp -f bestaudio --extract-audio --audio-format mp3 <VIDEO_URL>
```

Refer to the [yt-dlp documentation](https://github.com/yt-dlp/yt-dlp#format-selection) for more details on format selection.

### Transcribing Audio from Video Files

This project also includes `openai-whisper` for audio transcription.

**Transcribing an MP4 file using the 'medium' model:**

To transcribe the audio from an MP4 file, you can use the `whisper` command-line tool.
The following command will process `your_video.mp4` and use the `medium` model for transcription.
```sh
whisper your_video.mp4 --model medium
```
Replace `your_video.mp4` with the actual path to your video file.

**Output:**
The transcription will be saved in various formats (e.g., `.txt`, `.srt`, `.vtt`) in the same directory as the input file by default.

**Controlling Output:**

*   **Specify Output Directory:** You can use the `--output_dir` (or `-o`) option to save the transcription files to a specific directory.
    ```sh
    whisper your_video.mp4 --model medium --output_dir /path/to/output_directory
    ```
*   **Specify Output Format(s):** While Whisper outputs multiple formats by default, you can specify desired formats using `--output_format`. For example, to get only a TXT file:
    ```sh
    whisper your_video.mp4 --model medium --output_format txt
    ```
    To get multiple specific formats, you can list them (the exact syntax might depend on the whisper version, often comma-separated or by repeating the flag, check `whisper --help`):
    ```sh
    # Example for comma-separated, check documentation if this specific syntax doesn't work
    whisper your_video.mp4 --model medium --output_format txt --verbose False
    ```

**Other models:**
Whisper offers various models like `tiny`, `base`, `small`, `medium`, and `large`. Larger models generally offer better accuracy but require more computational resources and time. You can specify the model using the `--model` flag. For example, to use the `base` model:
```sh
whisper your_video.mp4 --model base
```

For more advanced options and features of `openai-whisper`, refer to its [official documentation](https://github.com/openai/whisper).

