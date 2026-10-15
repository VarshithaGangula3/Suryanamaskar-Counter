# Surya Namaskar Counter

A browser-based Surya Namaskar (Sun Salutation) counter that uses your webcam and real-time pose detection to track your progress through all 12 poses and count completed rounds. No installs, no accounts, no backend — everything runs locally in your browser.

## Features

- **Real-time pose detection** using TensorFlow.js and the MoveNet (SinglePose Thunder) model
- **Tracks all 12 poses** of the Surya Namaskar sequence, in order, and counts full rounds
- **Hold detection** — each pose must be held briefly before it's confirmed
- **Look-ahead skipping** — if a pose can't be confirmed but you've clearly moved on to the next one, the counter marks it as "skipped" (amber) and keeps going instead of getting stuck
- **Round summaries** that tell you which poses to slow down on
- **Skeleton overlay** drawn on the live camera feed
- **Debug panel** showing joint angles and the pose-check flags, useful for tuning thresholds
- **Automatic light/dark mode**
- Your video never leaves your device

## Versions

| File | Best for | Notes |
|------|----------|-------|
| `surya_namaskar_counter_6.html` | Desktop / laptop | Standard version. A pose is confirmed after 14 consecutive matching frames; look-ahead checks one pose ahead after 3 s. |
| `surya_namaskar_counter_6fast.html` | Faster-paced practice | Uses a sliding window (10 of the last 15 frames) so brief holds and jittery detections still count. Looks up to 5 poses ahead and can skip forward after 1.2 s. |
| `surya_namaskar_mobile.html` | Phones | Full-screen mobile UI with a hold-progress ring, 12-step progress strip, front/back camera flip, haptic feedback, screen wake lock, and an installable "Add to Home Screen" web-app manifest. |

## Getting started

1. Open one of the HTML files in a modern browser (Chrome, Edge, Safari, or Firefox).
   - Camera access requires a **secure context**: open the file locally, serve it from `localhost`, or host it over HTTPS (e.g. GitHub Pages).
2. Click **Start** and allow camera access. The model loads on first start (needs an internet connection to fetch the libraries and model weights).
3. Stand **6–8 ft (2–2.5 m)** from the camera with your **full body in frame** and **good, even lighting**.
4. Move through the sequence, holding each pose until it's confirmed. The round counter increases after pose 12.

Use **Pause** to stop the camera and **Reset** to clear progress.

### Running a local server (optional)

```bash
# from the repo folder
python3 -m http.server 8000
# then visit http://localhost:8000/surya_namaskar_counter_6.html
```

### Hosting on GitHub Pages

Enable GitHub Pages for this repository (Settings → Pages → deploy from the `main` branch), then open
`https://<username>.github.io/Suryanamaskar-Counter/surya_namaskar_mobile.html` on your phone.

## The 12 poses

| # | Pose | Cue |
|---|------|-----|
| 1 | Pranamasana | Stand, palms together at chest |
| 2 | Hasta Uttanasana | Arms stretched up, slight backbend |
| 3 | Hastapadasana | Forward fold, hands near feet |
| 4 | Ashwa Sanchalanasana | Right leg back, left knee bent |
| 5 | Dandasana | Plank — body straight |
| 6 | Ashtanga Namaskara | Knees, chest, chin to floor |
| 7 | Bhujangasana | Cobra — chest lifted, hips down |
| 8 | Adho Mukha Svanasana | Downward dog — hips high |
| 9 | Ashwa Sanchalanasana | Left leg back, right knee bent |
| 10 | Hastapadasana | Forward fold again |
| 11 | Hasta Uttanasana | Arms up, slight backbend |
| 12 | Pranamasana | Return to prayer |

## How it works

1. Each video frame is passed to MoveNet, which returns 17 body keypoints.
2. `computeMetrics()` turns the keypoints into angles (hip, knees, elbows) and simple facts such as *upright*, *torso folded*, *arms up*, *body horizontal*, *hips high*, and *palms close*.
3. `matchesPose()` checks those facts against the rules for the current pose.
4. Once a pose is held long enough it's marked **done** (green) and the counter moves on; after pose 12 a round is added.
5. If you're stuck on a pose but are clearly holding a later one, the missed pose is marked **skipped** (amber) so the flow keeps going.

Detection thresholds (e.g. `HOLD_NEEDED`, `WINDOW`, `MIN_HITS`, `STUCK_MS`, `LOOKAHEAD`) are constants near the top of each file's script and can be tweaked. Turn on the **Debug** panel to see live values while adjusting.

## Tech stack

- Plain HTML, CSS, and JavaScript — single self-contained files
- [TensorFlow.js](https://www.tensorflow.org/js) 4.10
- [@tensorflow-models/pose-detection](https://github.com/tensorflow/tfjs-models/tree/master/pose-detection) 2.1.3 (MoveNet)
- [Tabler Icons](https://tabler.io/icons) webfont

## Tips & limitations

- Detection works best when all major joints are visible to the camera.
- Floor poses (5–8) are the hardest to detect; if they're often marked skipped, try adjusting camera height or angle.
- Only one person is tracked at a time.
- This is a practice aid, not a form-correction tool — listen to your body.
