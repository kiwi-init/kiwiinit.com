# kiwiinit.com

Kiwi Init's page: free software, optimism and computers, and the two things we make, Channels and Interlinked, each with its Homebrew line. It is static files in `site/`, served by a Cloudflare Worker that does nothing but serve them, at kiwiinit.com.

The style is Channels' site (`site/` in [kiwi-init/channels](https://github.com/kiwi-init/channels)): OLED black, Paper Mono for headings and labels, SF Pro for body text, the same colour tokens, copy boxes and spectrum sheen.

## Files

- `site/index.html`, `site/styles.css`, `site/app.js`: the page, hand-written, no build step. `app.js` only runs the copy buttons. It is a file of its own so the CSP can stay `script-src 'self'`.
- `site/404.html`: what any other path shows.
- `site/_headers`: the security headers (the same CSP as Channels' site).
- `site/media/channels.svg`, `site/media/interlinked.svg`: the app icons, from `site/icon.svg` in kiwi-init/channels and `script/app-icon/icon.svg` in kiwi-init/interlinked. Interlinked's copy has its viewBox cropped to the icon's square, so both are full-bleed and show their art at the same size.
- `site/icon.svg`: the tab icon, a placeholder until Kiwi Init has a mark of its own.
- `site/fonts/`: Paper Mono (`PaperMono.woff2`, SIL OFL 1.1 in `PaperMono-OFL.txt`), as on Channels' site.
- `wrangler.jsonc`: the Worker: static assets from `site/`, `404-page` for unknown paths, and kiwiinit.com as its custom domain.

## Look at it locally

```sh
npx wrangler@4 dev
```

That serves it at http://localhost:8787 with the headers applied. Without Node, `python3 -m http.server -d site 8787` shows the same page without the headers.

## Put it on kiwiinit.com

This needs the Cloudflare account that owns kiwiinit.com, so it is a person's job, done once:

1. Log wrangler in to that account. It opens the browser:

   ```sh
   npx wrangler@4 login
   ```

2. Turn off today's redirect. In the Cloudflare dashboard, open kiwiinit.com, then Rules, and delete or turn off the redirect that sends kiwiinit.com elsewhere (look under Redirect Rules, Page Rules and Bulk Redirects). Redirects run before Workers, so the page can't show while one is on.

3. If kiwiinit.com has an apex DNS record (A, AAAA or CNAME on `kiwiinit.com`) that only existed for the redirect, delete it. A Worker's custom domain needs the name free, and the deploy creates its own record.

4. From this folder, deploy:

   ```sh
   npx wrangler@4 deploy
   ```

   It uploads `site/` and attaches kiwiinit.com to the Worker.

5. Open https://kiwiinit.com.

After that, every change ships with `npx wrangler@4 deploy` again. `www.kiwiinit.com` isn't set up. To serve it too, add `{ "pattern": "www.kiwiinit.com", "custom_domain": true }` to `routes` in `wrangler.jsonc`.
