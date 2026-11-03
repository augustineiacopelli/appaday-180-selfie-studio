# Selfie Studio

**AppADay 180** | Category: Creative (C) | Uses Claude

Selfie Studio turns a handful of everyday selfies into polished headshots without generating fake faces. Claude scores each photo for lighting, framing, expression, and background and writes a LinkedIn headline and caption, then an in browser cutout places you on five studio backdrops and exports four ready sizes.

Live app: https://augustineiacopelli.github.io/appaday-180-selfie-studio/

Portfolio: https://augustineiacopelli.github.io/appaday/

## How to use it

1. **Add.** Choose 3 to 10 photos from your library or take a selfie with the front camera. Three photos unlock the critique. One photo is enough to use Skip Critique and go straight to the Studio. Tap the small x on any thumbnail to remove it.
2. **Critique.** Optionally describe your role or field so the headline fits, then run the critique. Claude scores every photo from 1 to 10 on lighting, framing, expression, and background (40 possible), marks the Best photo, suggests a backdrop, and drafts a LinkedIn headline (220 characters at most) and a caption (150 at most). Both are editable, with live character counters and Copy buttons. Use This Photo on any card sends that photo to the Studio.
3. **Studio.** The on device cutout lifts you off the background. Pick a backdrop (Studio gradient in three tones, Office blur, Editorial, Warm outdoor, or Solid in white, light gray, navy, or plum), drag the photo to reposition it, set Zoom, and adjust Edge softness. Cutout Preview shows the cutout over a checkerboard. The Cutout and No Cutout control switches modes. In No Cutout mode the backdrop row becomes a treatment row: Natural, Warm, Editorial, and Soft Vignette.
4. **Export.** Four sizes are rendered fresh from the full resolution photo, each with its own head framing. Download saves a PNG. On a phone it opens the share sheet so you can save to Photos; on a computer it downloads. Download All shares all four files at once, or downloads them one after another.

| Size | Pixels | File |
| --- | --- | --- |
| LinkedIn | 800 by 800 (circle crop previewed on screen only) | selfie-studio-linkedin.png |
| Instagram | 1080 by 1350 | selfie-studio-instagram.png |
| ID Square | 600 by 600 (badges and school IDs, not a passport photo) | selfie-studio-id-square.png |
| Parish Directory | 1200 by 1500 | selfie-studio-parish-directory.png |

## Privacy

Photos are decoded in the browser tab and never saved to storage. Closing or reloading the tab discards them. The critique sends each photo to Claude as an 896 pixel JPEG and nothing else. The cutout runs entirely on the device. The only things saved in the browser are the critique text, the chosen photo number, the edited headline and caption, the optional role line, and the default edge softness. Clear Session in Settings removes all of it.

## Claude

The critique uses the sonnet tier through the shared models.js, so the model follows the portfolio config and no model ID lives in this app. The key comes from the shared AppADay key ring (keys.js, app 180). Settings mounts the key manager; the app has no key field of its own. If the key sheet is dismissed, the critique simply does not run. Requests use max_tokens 1800, read only text blocks, and fall back through the thinking settings (between_tools, then disabled, then none) when the model rejects one. Replies must be strict JSON; the parser strips code fences, takes the outermost braces, validates every field, and clamps every score to 1 to 10. Each kind of failure (connection, rejected key, rate limit, oversized request, busy service, unexpected format) gets a plain sentence and a Retry button.

## The MediaPipe exception

AppADay apps normally load nothing beyond fonts and Chart.js. This app also loads Google MediaPipe Tasks Vision, pinned to **@mediapipe/tasks-vision 1.2.0** from jsdelivr, with the wasm files from that same version, plus the selfie_segmenter float16 model from storage.googleapis.com. It is imported lazily the first time the Studio opens, so the Add and Critique steps never download it. The first load is about 15 MB and is cached by the browser afterward. The segmenter runs as an ImageSegmenter in IMAGE mode on the CPU delegate with confidence masks.

The mask is softened with a smoothstep curve, feathered by downscaling and upscaling under the Edge softness slider, and measured for the head top, head width, and neck so each export can place the eyes near the upper third. All effects use pixel loops and the downscale blur; canvas filters are never used. Office blur builds a clean plate by removing the person and filling the gap from coarse to fine before blurring, so no ghost of your outline shows behind you.

## Fallback behavior

The model load races a 15 second timeout. If jsdelivr or Google storage is blocked, slow, or fails, a toast explains it and the Studio switches to No Cutout mode, where the four treatments and all four export sizes still work. If the load finishes after the timeout, a toast says the cutout is ready, and tapping Cutout uses it. Tapping Cutout after a failed load tries again. If no person is found in a photo, that photo stays in No Cutout mode and the next photo returns to Cutout automatically.

## Files

Single index.html with inline CSS and JS, plus this README. Head tags: home screen icon block, Save to Phone manifest and save.js, models.js, and keys.js with data-app 180.

## 2.0 candidate

Photo to video: turn the finished headshot into a short subtle motion clip (a slow push in with a light sweep across the backdrop) for profile banners and story posts, still built only from the real photo.
