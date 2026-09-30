# videojs-vast-vpaid

Historical MailOnline plugin for VAST and VPAID preroll ads in Video.js 4.
This checkout retains the original HTML5 and Flash integrations.

## Install and example

Copy the built JavaScript/CSS from [bin/](bin/) into a page that loads the
original compatible Video.js version. Then initialize the plugin:

```js
const player = videojs('video')
player.vastClient({
  adTagUrl: 'https://example.com/ad.xml',
  adsEnabled: true,
  adCancelTimeout: 3000
})
```

The URL is a placeholder; supply your own tag. See the
[historical integration reference](docs/integration.md) for scripts, options,
events, and examples. Flash-era support claims are historical, not a current
browser compatibility promise.

## Development

The original workflow uses npm, Bower, Gulp, and Karma:

```sh
npm install
bower install
npx gulp start-dev
```

The demo uses port 8085. `npm test` runs `gulp ci-test`; `npx gulp build` creates
the plugin. npm's preinstall script installs repository Git hooks. These old
build dependencies and browser integrations have not been exercised in this pass.

## License

[MIT](LICENSE), copyright MailOnline.
