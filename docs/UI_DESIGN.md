# UI Design: Whisper Transcription Frontend

1. User uploads a WAV audio file.
2. User selects language (initially Vietnamese: `vi`).
3. User clicks Transcribe to call the API.
4. UI displays JSON response with transcribed text and metadata.
5. Optional: Inline audio preview of the uploaded file.

## User Flows
- Transcribe Audio
  - Upload WAV → Select language → Click Transcribe → View JSON result

## Screen Layout
- Header
  - Title: "Audio Transcription (Whisper)"
  - Status pill (Online/Busy)
- Main Form
  - File input: WAV only
  - Language selector: dropdown (default `vi`)
  - Actions: Transcribe (primary), Reset (secondary)
  - Result: JSON viewer (text + metadata)

## Components
- FileInput
  - Props: `accept="audio/wav"`
  - State: selected file
- LanguageSelector
  - Props: `languages[]` (initially `["vi"]`)
  - State: selectedLanguage
- TranscribeButton
  - Disabled states: invalid form, busy
- AudioPreview (optional)
  - Props: `src` (object URL)
- JsonResult
  - Props: response object

## Validation & UX
- Require WAV file selected (extension `.wav`)
- Language defaults to `vi`
- Loading state with progress indicator
- Error toast on API failure with retry option
- Persist last selected language in `localStorage` (optional)

## API Integration (FastAPI)
- Endpoint: `POST /transcribe`
  - `multipart/form-data`: `file` (wav), `language` (e.g., `vi`), optional `add_punctuation`
  - Response: JSON `{ success, filename, text, text_no_punctuation, language, duration, segments_count, punctuation_restored }`
- Health: `GET /health`
- Frontend hosting: static page served at `/ui` (mounted via `StaticFiles`)

## Accessibility
- Keyboard navigation for all controls
- Visible focus states
- Clear labels and error messages
- Audio controls accessible via screen readers

## Mobile/Responsive
- Single-column layout on mobile
- Responsive audio controls
- Touch-friendly buttons

## Styling
- Neutral dark theme emphasizing primary action
- Clear error and progress states
- Show generation duration and filename when available

## Metrics (optional)
- Total transcribe requests
- Success/error rates
- Average latency and audio duration
- Language selection distribution

## Future Enhancements
- Support additional languages
- Waveform visualization
- Upload non-WAV formats (server-side conversion)
- Job progress via WebSocket for long audios
