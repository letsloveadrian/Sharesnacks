# ShareSnacks /plxcandy starter

- Put your logo images into `plxcandy/images/`.
- Commit & push. GitHub Pages will auto-build.
- The gallery will be served at:

  - Default Pages URL: `https://letsloveadrian.github.io/ShareSnacks/plxcandy/`
  - Or via your domain with a Cloudflare Worker route: `https://sharesnacks.ca/plxcandy` (instructions below).

## Cloudflare Worker proxy (optional, to keep `/plxcandy` on your domain)

1. In Cloudflare → Workers, create a new Worker named `plxcandy`.
2. Paste this code and deploy. Replace `USERNAME` and `REPO` if you fork this elsewhere.

```js
export default {
  async fetch(request, env, ctx) {
    const url = new URL(request.url);
    // Proxy everything under /plxcandy to GitHub Pages
    const upstream = new URL(`https://letsloveadrian.github.io/ShareSnacks${url.pathname}`);
    // Keep query strings
    upstream.search = url.search;
    // Fetch and stream the response from Pages
    return fetch(upstream.toString(), {
      method: request.method,
      headers: request.headers,
      body: request.body,
      redirect: "follow",
    });
  }
}
```

3. Add a route under your zone: `sharesnacks.ca/plxcandy*` → Worker `plxcandy`.
4. Done. Your gallery lives at `https://sharesnacks.ca/plxcandy` while your root keeps forwarding to Ombi.
