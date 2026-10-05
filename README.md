# Offset

Measure a lever arm with a phone: record the phone's accelerometer and gyroscope while it is carried from one point to another (for example an INS to a GNSS antenna) and work out the offset between them (X right, Y forward, Z up, in mm). Working name.

Live: https://tbailey-oxts.github.io/offset/

## How it works

1. **Start hold (2 s still)**: gravity direction and gyro bias are measured; the start attitude defines the world frame (Y = top of the phone, flattened to horizontal).
2. **Orientation**: the bias-corrected gyro is integrated so accelerations are rotated into the world frame while the phone turns.
3. **Holds**: every still pause (and the end) resets velocity to zero and re-anchors tilt from gravity, so drift can't build up across the whole move.
4. **Loop mode**: go out to one or more points and back to the exact start; the closing gap is drift and is removed. Survey mode measures up to 3 points in one loop.
5. **Sensor calibration**: six faces plus two full turns measure the phone's own accelerometer and gyro scale errors, which are then corrected in every measurement.
6. **Sessions**: repeat a measurement and get the mean with a suggested accuracy (spread between runs plus a systematic allowance).

## Files

- `processing.js`: calibration, orientation, integration, drift correction, loop closure
- `sensorcal.js`, `calui.js`: sensor calibration fit and its guided routine
- `session.js`: statistics for repeated runs
- `capture.js`: native bridge, Generic Sensor API, `devicemotion` fallback
- `app.js`, `index.html`, `style.css`, `viz.js`: UI and 3D path view
- `contribute.js`, `worker/`: optional upload of runs to a Cloudflare Worker + D1 database
- `tests/test.html`: synthetic tests (serve the folder and open it); `tests/dataset.html` re-scores uploaded runs
- `hardware/cradles/`: 3D-printable phone cradles (OpenSCAD)

## Android app

A native wrapper (WebView around the same web files plus a full-rate sensor bridge) lives in `android/`. Browsers round sensor data and cap it at about 55 Hz; the app records full-rate, unrounded data.

- Download the APK: https://github.com/tbailey-oxts/offset/releases/tag/android-latest
- On the phone, allow **Install unknown apps** for your browser or file manager, then open the APK.
- Built by GitHub Actions (`.github/workflows/android.yml`) on every push that touches the web files or `android/`.

Use **Download raw data (CSV)** on the phone and **Load CSV** on a desktop browser to replay recordings.
