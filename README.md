[![Made with pollinations.ai](https://raw.githubusercontent.com/pollinations/pollinations/main/packages/ui/src/brand/badge-made-with.svg)](https://pollinations.ai/?ref=badge)

# 🔍 pixel-secret — hidden messages that only this tool can read

Type a topic. A **Pollinations LLM** writes a secret sentence about it. **flux** paints a normal-looking picture. The sentence is then buried in the image's lowest bits, and only pixel-secret can pull it back out.

Built for quest [#18367](https://github.com/pollinations/pollinations/issues/18367).

## The two Pollinations calls
| What | Endpoint | Model |
|---|---|---|
| Writes the secret | `POST https://gen.pollinations.ai/v1/chat/completions` | `deepseek/deepseek-v4-flash` |
| Draws the cover | `POST https://gen.pollinations.ai/v1/images/generations` | `flux` |

Both are wrapped in the root-level [`pollinations-client.js`](./pollinations-client.js) as `PolliClient.writeSecret()` and `PolliClient.drawCover()`.

## Paying with your own Pollen (BYOP)
```html
<polli-connect app-key="pk_…" budget="5" expiry="7" models="text,image"></polli-connect>
```
Click **Pollinations Connect**, approve a scoped key on the consent screen (5 Pollen, 7 days, text + image only), and every generation bills **your** balance. Revoke it any time from your Keys page. No secret keys are pasted into the page.

## How the hiding works
1. The LLM sentence becomes `PSEC` + a 4-byte length + UTF-8 bytes.
2. The bits go into the least-significant bits of the RGB channels — 1 to 4 bits per channel, set by the depth slider.
3. Extraction tries depth 1 through 4, checks the `PSEC` marker, and decodes whatever matches.

**Depth is about capacity, not durability.** A 512×512 image at depth 1 holds roughly 96 KB, and deeper embedding buys more room plus a little tolerance to pixel noise — but **LSB hiding only survives lossless formats**. Any lossy step (JPEG, lossy WebP, a screenshot, a chat app that recompresses) rewrites the low bits and the message is gone. pixel-secret always exports lossless PNG.

## Run
Open the live app, or serve the folder (`python3 -m http.server`). Everything runs client-side.
