# PocketMind for Android — v0.1.0

PocketMind is an experimental, local-first Android playground for running a compatible on-device language model through Google's LiteRT-LM Kotlin API. It includes bounded autonomous exploration cycles, locally persisted notes, an activity log, a manual one-shot prompt, CPU/GPU selection with a GPU-to-CPU fallback, and a stop control.

## Current status

This is a source project, not a prebuilt APK. The environment used to prepare it has Java 21 but no Android SDK, no Gradle installation, and no DNS/network access for downloading build tools, so an APK could not be compiled or smoke-tested here. A GitHub Actions workflow is included to build `app-debug.apk` once the project is pushed to a GitHub repository.

## Build the APK with GitHub Actions

1. Create a new, empty GitHub repository.
2. Upload the contents of this project (the contents of the `pocketmind-android` folder, not the outer folder itself) to the repository.
3. Open the repository's **Actions** tab. Allow workflows if GitHub asks.
4. Select **Build PocketMind APK** and run the workflow, or push to `main` / `master`.
5. When the workflow succeeds, open its run and download the `pocketmind-debug-apk` artifact. Extract it to get `app-debug.apk`.
6. Transfer the APK to the Galaxy S24 Ultra, open it, and permit installation from that source when Android prompts.

You can also build in Android Studio with JDK 21, Android SDK Platform 36, Build Tools 36.0.0, and Gradle 8.13:

```bash
gradle --no-daemon assembleDebug
```

## Import a model

PocketMind loads local **`.litertlm`** files, not arbitrary `.gguf`, `.task`, or raw Hugging Face weight files.

1. Obtain a compatible `.litertlm` model from the [LiteRT Community on Hugging Face](https://huggingface.co/litert-community) and ensure you comply with that model's license.
2. Save the file under Downloads or another folder accessible through Android's document picker.
3. In PocketMind, tap **Import model**, select the `.litertlm` file, and wait for the private copy to finish.
4. Choose CPU (most compatible) or GPU (hardware acceleration if supported), then tap **Load model**.
5. Ask a question or start a bounded autonomous session.

The app stores the imported model and notes in its private app storage. Importing copies the full model file, so allow enough free space for the model plus a second copy during import.

## How autonomous sessions work

The app runs a foreground, user-visible loop while the activity is open. Each cycle creates a bounded model generation based on the user's focus and recent local notes, then appends the result to the local memory log. Sessions can be limited to 2, 10, 30, or 60 minutes and stopped manually. The app deliberately does not request the Android internet permission or provide external tools/network access to the model.

This is repeated inference with persistent notes, not evidence of subjective consciousness. The app does not run indefinitely in the background; keep it open during sessions. Generation speed, thermal throttling, memory pressure, and supported acceleration depend on the model, its license/format, available device RAM, and the phone's firmware.

## Known limitations in this first build

- The UI and build workflow are prepared, but no successful build has yet been verified in this environment.
- The app supports LiteRT-LM `.litertlm` models only.
- Each imported model is copied to private app storage and can occupy multiple gigabytes.
- Long sessions are bounded to preserve battery and avoid an uncontrolled tight loop.
- No model is bundled in the APK; this keeps the app package from being multiple gigabytes.
- LiteRT-LM API and dependency changes can require build fixes when the project is compiled against future releases.

## Technology

- Kotlin / Android SDK
- Google LiteRT-LM Android API (`com.google.ai.edge.litertlm:litertlm-android:0.18.0`)
- Kotlin Coroutines 1.11.0
- No cloud inference endpoint or account
