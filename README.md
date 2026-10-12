# Mayra Assistant — Android Studio source project

A starter native Android app in Kotlin + Jetpack Compose for a voice-first Hinglish assistant. It includes:
- Compose home screen (no text chat UI)
- microphone permission request
- microphone foreground service + persistent notification
- PCM audio capture and audio chunk playback
- Gemini Live WebSocket scaffold and interruption handling
- separate backend example for short-lived token creation (API key stays server-side)

## Important status

This is a **starter scaffold, not a verified production build**. Gemini Live API is evolving, and request/response field details, token minting and output audio sample rate must be verified against the current official documentation before release. The app will not connect until you deploy the backend and update the HTTPS URL in `MayraVoiceService.kt`. Do not put a permanent Gemini API key in Android code.

Google lists `gemini-3.1-flash-live-preview` as a legacy preview model and recommends `gemini-3.8-live` for most new low-latency voice-agent experiences. The model is kept at 3.1 here to match the requested spec; update `MODEL` and the backend model together if migrating.

## Requirements
- Android Studio (current stable)
- JDK 17
- Android device/emulator, API 26+
- HTTPS-hosted backend with Node.js 18+ and a Gemini API key configured as an environment variable

## Open in Android Studio
1. Extract this ZIP.
2. Android Studio → Open → select the extracted `MayraAssistant` folder.
3. Wait for Gradle sync.
4. Deploy the backend (instructions below).
5. Replace `https://YOUR_BACKEND_DOMAIN.example.com/mayra/live-token` in `MayraVoiceService.kt` with your deployed HTTPS endpoint.
6. Run on a real Android phone, grant Microphone and Notifications permissions, then tap **Start Mayra**.

## Deploy token backend
```bash
cd backend
npm install
GEMINI_API_KEY="YOUR_GEMINI_API_KEY" npm start
```
Use a proper HTTPS hosting provider for deployment. Never commit `.env`, keys, or service credentials. Add authentication, rate limits, abuse monitoring, and per-user quotas before public release. A token-mint endpoint without access controls can be abused.

## Android background behavior
Android requires the microphone foreground service to be started while the app is visible and after microphone permission is granted. A persistent notification is shown while listening. This starter does not promise always-on wake-word detection or automatic restart after the OS kills the process.

## Security checklist
- API key belongs only in the server environment.
- Restrict API key usage and monitor quotas.
- Add user authentication and rate limits to token endpoint.
- Use HTTPS only.
- Give a clear, persistent indication when microphone capture is active.
- Add privacy policy, consent and data-retention details before publishing.

## Troubleshooting
- If token request fails, check backend logs, model access and the current Gemini Live ephemeral token documentation.
- If WebSocket setup fails, check the current Live API WebSocket endpoint and auth header expected for ephemeral tokens.
- If audio sounds too fast/slow, verify model output sample rate and PCM encoding in the current docs.
- This code is an initial integration scaffold and may require API protocol adjustments to match current Gemini Live API.


## Build APK using only an Android phone (GitHub Actions)
1. Create a GitHub account and a new repository.
2. Upload the extracted project files, including the hidden `.github/workflows/android.yml` file, to the repository's `main` branch.
3. Open the repository's Actions tab and run **Build Mayra APK** (or push a commit).
4. When the workflow succeeds, open the run and download the **Mayra-debug-apk** artifact. GitHub may require downloading the artifact ZIP and extracting `app-debug.apk`.
5. This only builds the starter app. The Live API connection still needs a deployed backend URL and current API protocol validation.
