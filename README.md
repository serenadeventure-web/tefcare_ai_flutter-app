# TefCare AI – Flutter skeleton (untested starter)

Offline Android app: camera/gallery -> on-device TFLite classifier -> result with
confidence, "can't tell" handling, SQLite history, English/Amharic/Afaan Oromoo labels.
There is NO trained model in this project. Without `assets/model/teff_model.tflite`
the app runs but shows "Model not found" instead of inventing results.

## 1. Create the Android project, then copy these files in
    flutter create tefcare_ai --org com.yourteam --platforms android
Copy this folder's `lib/`, `assets/` and `pubspec.yaml` over the new project
(overwrite when asked), then:
    cd tefcare_ai
    flutter pub get

## 2. Android settings
If the build complains about minSdk, open `android/app/build.gradle(.kts)` and set
`minSdk = 26` (tflite_flutter needs a recent minimum).

## 3. Add your trained model
Put `teff_model.tflite` in `assets/model/`. Edit `assets/model/labels.txt`: one class per
line, in EXACTLY the order of your training classes (Keras `class_names` is alphabetical).
Check `lib/classifier.dart` assumptions: input 224x224x3 float32 (0-255), softmax output.
Convert in Colab:
    import tensorflow as tf
    model = tf.keras.models.load_model("teff_model.keras")
    conv = tf.lite.TFLiteConverter.from_keras_model(model)
    conv.optimizations = [tf.lite.Optimize.DEFAULT]   # re-evaluate accuracy after this
    open("teff_model.tflite", "wb").write(conv.convert())

## 4. Run and build
Phone: Settings > Developer options > USB debugging. Then:
    flutter run                      # test on the phone
    flutter build apk --release      # build/app/outputs/flutter-apk/app-release.apk

## TODO before you present results
- Replace the placeholder text in `kInfo` (lib/screens.dart) with expert-reviewed content.
- Tune `Classifier.threshold` on validation data; test with non-Teff photos.
- Have native speakers review lib/strings.dart; translate the other screens.
- Measure model size and inference time on a real phone.
