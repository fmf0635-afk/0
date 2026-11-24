# Instinct AI — full-stack foundation (v0.2)

Bilingual Arabic/English personal assistant starter for web, installable mobile PWA, and a Windows/macOS/Linux desktop shell.

## What is real in this build
- Responsive bilingual web/PWA interface and local task list.
- Node.js backend with a server-side, OpenAI-compatible chat endpoint.
- Electron desktop shell with a narrow, allowlisted app-launch bridge (Calculator and Notepad shortcuts where supported) and safe HTTP(S) URL opening.
- No arbitrary shell execution exposed to the renderer.

## What still needs setup / implementation
- Real AI: add your own provider API key to `.env` (see `.env.example`). The backend never sends the key to the browser.
- Accounts, cloud sync, Gmail/Outlook/Calendar/Todoist: OAuth connectors are not yet connected. See `integrations/README.md`.
- Broad computer control: not enabled by default. It requires a separate local automation agent, OS-specific permissions, a visible approval step, and a tested allowlist. This foundation deliberately does not claim universal control of every app.
- Native Android/iOS packaging: PWA works in supported browsers; store apps require Capacitor/native project, signing, permissions, privacy policy, and review.

## Run the web app
1. Install Node.js 20+.
2. In this folder run `npm install`.
3. Copy `.env.example` to `.env` and set `AI_API_KEY` and optionally `AI_MODEL` / `AI_BASE_URL`.
4. Run `npm start` and open `http://127.0.0.1:8787`.

Without an API key, the UI remains usable but chat reports that AI is not configured. Do not commit `.env`.

## Run desktop shell
1. Start the backend in one terminal (`npm start`).
2. In another terminal run `npm run desktop`.

The desktop app expects the local backend at `127.0.0.1:8787`. Desktop automation is intentionally restricted; add capabilities only through reviewed IPC methods, not by exposing Node or arbitrary shell access to the page.

## Production checklist
Authentication; database; encrypted secrets; OAuth state/PKCE; CSRF protection; audit log; rate limits; user consent; deletion/export; backup; monitoring; secure updates; Android/iOS permission review; penetration testing.

## Android app (new in v0.3)
The `android/` folder is a native Android Studio project (Kotlin + WebViewAssetLoader). It packages the bilingual interface locally, keeps tasks in Android WebView storage, supports a secure HTTPS backend connection, and can display the phone's launchable apps. Choosing an app always shows a confirmation before Android opens it.

### Build the Android app
1. Install Android Studio (current stable) with Android SDK Platform 35 and JDK 17.
2. Open the `android/` folder in Android Studio and allow Gradle sync.
3. Connect an Android phone with USB debugging enabled, or start an emulator.
4. Select the `app` run configuration and press Run. To make an APK, use **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
5. In Instinct AI, open **Settings**, enter your deployed backend's HTTPS URL and the app access token configured on that server.

### Android capabilities and boundaries
- The native app can browse launchable apps installed on the phone and open one only after a confirmation dialog.
- This is app launching, not unrestricted control of another app's screen or private data. Android does not permit arbitrary cross-app control without explicit special permissions and app-specific support.
- Gmail, Outlook, Calendar and task-provider accounts are not connected in this starter. Each needs a proper OAuth integration and provider review.
- The app requires an HTTPS backend for real AI chat. The AI provider key remains on that backend; never paste it into the Android app.

### Deploying the backend safely
- Set `AI_API_KEY` on the server, not in the app.
- Set `APP_ACCESS_TOKEN` to a long, unique random secret and configure the same token in Android Settings.
- Put the Node server behind HTTPS (for example, a reverse proxy or managed hosting). Set `HOST=0.0.0.0` only inside the protected hosting environment. Do not expose an unauthenticated development server to the public internet.
- The current token is a starter-level shared access token, not a replacement for per-user accounts, rate limiting, refreshable sessions, or a production security review.


## Animated companion (Android)
- An always-visible animated avatar is included in the app UI. Choose a boy or girl appearance; the choice is saved locally.
- The face animates while listening, thinking, speaking, and preparing an action. The activity line describes the current step.
- Android voice input uses the native SpeechRecognizer and asks for microphone permission. Spoken replies use Android TextToSpeech.
- Voice recognition depends on a speech recognition service installed on the device and available language packs.
- The avatar is a lightweight vector animation (not a prerecorded video or a human operator). It visualizes assistant states; it does not imply that an app action succeeded.
- App actions still require supported integrations and Android permissions. High-impact actions should be confirmed before execution.
