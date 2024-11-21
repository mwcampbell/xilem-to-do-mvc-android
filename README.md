# Xilem to_do_mvc example for Android

This proof-of-concept repository packages the [Xilem to_do_mvc example](https://github.com/linebender/xilem/tree/main/xilem/examples/to_do_mvc.rs) in a project with the necessary Gradle boilerplate to run it on Android using [GameActivity](https://developer.android.com/games/agdk/game-activity), complete with accessibility via the [work-in-progress Android adapter for AccessKit](https://github.com/AccessKit/accesskit/tree/android-basics).

## Building and running

```bash
export ANDROID_NDK_HOME="path/to/ndk"
export ANDROID_HOME="path/to/sdk"

rustup target add aarch64-linux-android
cargo install cargo-ndk

cargo ndk -t arm64-v8a -o app/src/main/jniLibs/ build --release
./gradlew build
./gradlew installDebug
adb shell am start -n org.linebender.todomvc/.MainActivity
```

## Known limitations

This is based on the [agdk-winit-wgpu example from rust-android-examples](https://github.com/rust-mobile/rust-android-examples/tree/main/agdk-winit-wgpu). The Gradle boilerplate and Android assets haven't been fully stripped down. In particular, this project reuses the images from that example.

Notice that in the build commands above, we built the Rust code in release mode but installed a debug build of the Android project. The former is to work around a known issue with Xilem on Android; when running a Rust debug build, Xilem tries to write logs to a temporary file that it doesn't have permission to write to. At the same time, we use an Android debug build to avoid the need to set up the Gradle project to sign a release build.

This repository has a checked-in copy of libc++_shared.so which I copied from the NDK. I couldn't find any mention of the need to manually copy this library into app/src/main/jniLibs/arm64-v8a, in either android-activity or the rust-android-examples repository, but it was clearly necessary, at least on my device. This is one downside of using GameActivity; at least as used by android-activity, it requires C++ glue code.
