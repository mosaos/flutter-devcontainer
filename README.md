# flutter-devcontainer

This project provides a development environment setup for Flutter using Dev Containers.

- **Dev Container**  
  Enables Flutter development within a Docker container.  
  This approach avoids polluting your local environment by encapsulating development tools within Docker.

## Environment

Verified under the following setup:

- Windows 11  
- WSL2 (Ubuntu)  
- Docker  
  Installed via `apt` (not Docker Desktop), running under WSL2  
- VSCode  
- Dev Containers extension

> **Note:** Place this project directory inside the WSL2 Ubuntu filesystem.  
It may not work correctly when located in the Windows file system due to limitations with `docker-ce` on WSL2 with DevContainer.

## Setup Steps

1. Launch WSL2 (Ubuntu).
2. Navigate to a working directory for your projects:

```shell
cd ~
mkdir my-projects
cd my-projects
````

3. Clone this repository:

```shell
git clone <git-url>
```

4. Navigate into the project directory:

```shell
cd flutter-devcontainer
```

5. Launch VSCode:

```shell
code .
```

6. Once VSCode opens, click on the green bottom-left corner (`>< WSL:Ubuntu`) and select `Reopen in Container`.
   Building the container image may take some time. Grab a coffee while you wait ☕.

7. On the first run, the container will be built using the provided Dockerfile.
   After the build, `flutter doctor` will be executed via the `postCreateCommand` setting.

8. Once completed, the integrated terminal will be connected to the shell inside the container.
   From here, you can use Flutter commands as needed.

> **Note:** You may see a message recommending you run `flutter upgrade`.
> Run it if needed to update to the latest Flutter SDK.

---

## Creating a Flutter Project

You can create a new Flutter project from within the container shell.

> Replace `<projectname>` with your desired project name.

```shell
flutter create <projectname>
cd <projectname>
flutter pub get
cd ..
chmod -R 777 ./<projectname>
```

You can now start developing your Flutter application inside the newly created project directory.

---

## Starting the Emulator

### Android

```shell
emulator @android33 -no-metrics
```

> You may see some warnings due to running the emulator inside a container environment:

* Hardware acceleration may not work, falling back to SwiftShader (software rendering):

```shell
WARNING | Your GPU drivers may have a bug. Switching to software rendering.
```

* Vulkan libraries may not be found:

```shell
SharedLibrary::open for [...]libvulkan.so failed
```

* PulseAudio connection errors (audio may not work):

```shell
pulseaudio: pa_context_connect() failed
```

* ADB "device offline" warning may appear, but the emulator still launches successfully:

```shell
adb: device offline
```

> To address initial launch errors, the following was added to `devcontainer.json`:

```json
"runArgs": [
  "--device", "/dev/kvm"
]
```

---

## Running the Flutter App

In the terminal (or another terminal tab), run:

```shell
flutter run
```

> **Note:**
>
> * Ensure the Android emulator is running before executing this.
> * This process may take a while ☕.

Once successful, the Flutter app will launch on the emulator.

![emulator](./img/flutter-android33-emu-02.webp)

---

## Building APK

To build a release APK:

```shell
flutter build apk --release
```

---

## Debugging

VSCode may not recognize the Flutter project for debugging if the `pubspec.yaml` file is not located at the project root.
In this template, we create Flutter projects within a subdirectory after connecting to the Dev Container, so debugging directly may not work out-of-the-box.

However, the following workaround allows you to debug via VSCode:

1. In the terminal, navigate to the Flutter project directory:

```shell
cd <projectname>
```

2. Open VSCode from that directory:

```shell
code .
```

3. A new VSCode window will open. Confirm it is connected to the Dev Container.
   You should see `>< Dev Container: Flutter Dev Container` in the bottom-left corner.

4. From this terminal, start the Android emulator:

```shell
emulator @android33 -no-metrics
```

5. Set a breakpoint in `lib/main.dart`, for example inside the `_incrementCounter()` method.

6. Open `main.dart`, click the "Run and Debug" icon in the left sidebar.

7. Click the "Run and Debug" button to start debugging.
   VSCode may prompt you for some options—choose the appropriate ones.

8. Once the app is running on the emulator, press the `+` button in the bottom right.

9. If a breakpoint was set on `_counter++;`, execution will pause there:

```dart
void _incrementCounter() {
  setState(() {
    _counter++;
  });
}
```

10. Add `_counter` to the Watch list to observe the counter increase.

This confirms that interactive debugging is possible with this setup.

---

## Troubleshooting

### SSLException during `flutter run`

Occasionally, the following error may occur during `flutter run`:

```shell
Launching lib/main.dart on Android SDK built for x86 64 in debug mode...
Exception in thread "main" javax.net.ssl.SSLException: Received fatal alert: bad_record_mac
```

Retrying the command usually resolves it—could be due to network instability.

---

## Summary

Although debugging requires a few extra steps, this setup provides a clean and flexible Flutter development environment using Docker and Dev Containers.
Once set up, you can easily manage and create multiple Flutter projects within this structure.

Feel free to customize or improve it to better fit your workflow!


