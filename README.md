# stackhaus

New Graph QL powered frontend tech stack. Built on Nuxt, using Apollo. Works best with the [stackhuas-backend](https://github.com/funkhaus/stackhaus-backend) WordPress theme.

## Build Setup

```bash
# install dependencies
$ npm install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm start

# generate static project
$ npm run generate
```

For detailed explanation on how things work, checkout [Nuxt.js docs](https://nuxtjs.org).

## Theme Config

1.  Install `stackhaus-backend` theme into WordPress [from here](https://github.com/funkhaus/stackhaus-backend)
    1.  Disable privacy mode on WordPress host (if applicable)
    1.  Install promoted Plugins
    1.  Go to Settings > General, and set the "Site Address (URL)" to be the frontend URL. Often times you'll have to email your host (Flywheel) to get them to allow this field to be edited.
    1.  Save Permalinks in the WordPress dashboard (WP-GQL requires this)
    1.  Define image sizes and menu locations in `/functions/theme-config.php`.
1.  Rename your `.env.example` to `.env` and config "DEFAULT_ENDPOINT" to be the GraphQL endpoint from the WordPress you installed above.

## Fonts

This theme uses [webfontloader](https://github.com/typekit/webfontloader) for font loading.

1.  If you are using a `@font-face` font, then you will need to put the font files in `/static/fonts`
1.  Then define the CSS in `/styles/fonts.css`
1.  Set the names of the font families in `/web-font-loader.js`
1.  Uncomment the `fonts.css` CSS line in `nuxt.config.js`
1.  Uncomment the `web-font-loader.client.js` plugin line in `nuxt.config.js`

For Typekit or google fonts, please see the [webfontloader](https://github.com/typekit/webfontloader) documentation.

## Menus

Stackhaus has built in SSR friendly menus. To use them, you first need to define menu locations in WordPress. This can be done in the `/functions/theme-config.php` file in the `stackhaus-backend` WordPress theme. We have some boilerplate code in the `register_custom_nav_menus()` function.

1. Define menu locations in your WordPress theme in `functions/theme-config.php`.
1. Assign menus to those menu-locations in the WordPress dashboard.
1. Define your menu locations in `store/index.js`, see the `nuxtServerInit()` function for commented out boilerplate.
1. That is it! Your menus will now be in the Vuex store under `$store.menus.locations`.

## SVGs

The theme includes global loading of all SVGs in the `/assets/svgs` directory. This means you can use any SVG by using a component with the filename prepended by `svg`. For example `<svg-filename class="svg"/>` if your SVG was located at `/assets/svgs/filename.svg`.

If you want to use an SVG in your CSS file, you can't place it in `/assets/svgs/`, you'be put it in just `/assets/filename.svg` and use it like this in your CSS: `background-image: url("~assets/filename.svg")`.

## Linting

This theme has perfect linting for Vue/GQL/JS/SCSS/CSS and HTML using Prettier and ESLint. The ruleset is the Vue style guide "Recommended" spec.

Behind the scenes, the workflow is this: Ugly code > Prettier > ESLint > Nice code!

The idea is that Prettier formats things like CSS and GQL nicely, and ESLint goes last and does everything else. This allows us to config ESLint to the Vue specs (Prettier has little config options by design). So that is what the .eslintrc.js file is for.

In your code editor, you'll want to install a Prettier plugin ([VS Code](https://github.com/prettier/prettier-vscode) or [Atom](https://github.com/prettier/prettier-atom)), and set the "ESLint Integration" to `true` and "Format on Save" to `true`. The settings for "prettier-eslint” should have a toggle for "Run Prettier Last", you want that set to `false`. We need ESLint to run last, this ensures the Vue Recommended specs get priority, not Prettier's specs.

The 4 `devDependencies` installed in the package are "eslint", "eslint-plugin-vue", "prettier" and "prettier-eslint". The "prettier" plugin is not required as most editor plugins will use a default version they ship with, although we have more control over the version of Prettier if we include it in the package file, so we did just to be safe. It also means it would run the same server side or client side if you do that as part of CI. "prettier-eslint" is used to integrate "prettier" and "eslint" together, and get them running one after the other.

## Deploying to Heroku

If you're comfortable with command clines, install the [heroku cli tools](https://devcenter.heroku.com/articles/heroku-cli).

Otherwise you can do it all through the website Dashboard too.

1.  Login in to [heroku](https://heroku.com)
1.  Create an App

Follow the instructions here:[nuxt heroku deployment](https://nuxtjs.org/faq/heroku-deployment/)

**TODO this section needs to be filled out more, speak to Drew in the meantime**

## Going Live!

We recommended using CloudFlare for your DNS, it's free plan is enough to do everything you need.

1.  Prep Nuxt to go live.
    1.  Disable Basic Auth in `nuxt.config.js`
    1.  Set the Apollo endpoints to be the new domain names. Probably `https://api.your-site.com/graphql` and if you're using Shopify, `https://shop.your-site.com/api/{version number here}/graphql.json`. You may want to use a `.env` for these, it helps when dealing with staging setups later.
    1.  If you want the site to be HTTPS (you should), you'll probably want to install the `Redirect SSL` module in Nuxt [from here](https://github.com/nuxt-community/redirect-ssl).
    1.  Be sure to turn off Privacy mode in Flywheel.
1.  In Heroku, add your custom domain name to the App. [See here](https://devcenter.heroku.com/articles/custom-domains]). Note the "DNS Target" Heroku gives you, it should be some funny names and a random string like `space-balls-12345drewish.herokudns.com`.
1.  Turn on SSL in Heroku using the "Automated Certificate Management" (this only works if you're hosting on a paid Heroku plan, which Funkhaus has).
1.  In CloudFlare, click "+ Add Site" (top left of screen).
    1.  When it asks for DNS entries, you'll want to set two CNAME's pointing to the "DNS Target" shown in Heroku (mentioned in Step 1). One CNAME for `www` and one CNAME set to `@`, which CloudFlare will then auto apply "CNAME Flattening", which is good. [See here for more](https://thoughtbot.com/blog/set-up-cloudflare-free-ssl-on-heroku).
    1.  Add an A-RECORD for your API backend, which is on Flywheel. Generally you want this to be `api.your-site.com`.
    1.  You do not need to do anything for SSL in CloudFlare, Heroku handles it.
1.  Setting up www redirects in CloudFlare:
    1. Click 'Page Rules'.
    1. Click 'Add a rule'.
    1. Match the URL to `*www.your-site.com`.
    1. select 'Forwarding URL' from the dropdown menu.
    1. Choose '301 - Permanent Redirect'.
    1. Set the redirect URL to `https://your-site.com`.
    1. Repeat above steps adding `*www.your-site.com/*` URL and `https://your-site.com/$1`.
    1. Repeat again adding `*www.your-site.com/*/*` URL and `https://your-site.com/$1/$2` (You can only have 3 rules on free plan).
1.  In Flywheel, add a new primary domain, this should match the CloudFlare entry you set above, probably `api.your-site.com`.
    1.  Setup the free SSL on Flywheel, and then make sure "Force SSL" is turn on.
    1.  Be sure to turn off Privacy mode in Flywheel.
1.  At this point, you will probably want to redirect your nameservers to point to CloudFlare. Probably these are `ed.ns.cloudflare.com` and `marge.ns.cloudflare.com`.
1.  If the site uses Shopify, you'll need to "Add an existing domain". Probably this will be `shop.your-site.com`.
    1.  In CloudFlare set a CNAME for `shop.your-site.com` to point to `shops.myshopify.com`.
    1.  **You do not need to setup an A-RECORD if you only want the shop to be on a sub-domain.**

## Basic authentication

This theme has built in basic authentication (.htaccess) protection. It is disabled by default, you can config this under the `basic` section of `nuxt.config.js`. The default username is `funkhaus` and the default password is `12345`. It's highly recommended that you change these defaults. Behind the scenes we use the [nuxt-basic-auth-module](https://www.npmjs.com/package/nuxt-basic-auth-module).

Note: If you start getting this error after enabling basic auth "Cannot read property toLowerCase of undefined" clear local storage. See [https://github.com/nuxt/nuxt.js/issues/6116](https://github.com/nuxt/nuxt.js/issues/6116)

## Google Analytics using `this.$gtag()`

You can set tracking codes in Settings > General. By default page views are tracked, but you can track anything you want using `this.$gtag()` in a component or page. See: https://developers.google.com/analytics/devguides/collection/gtagjs

```
    this.gtag('event', <action>, {
      'event_category': <category>,
      'event_label': <label>,
      'value': <value>
    });
```

## Store tracking breakpoint size

By default, on nuxtServerInit a user agent detect will happen and set the `state.breakpoint` to equal mobile or desktop. This is also set in `Default.vue` as well. We do this to try to make sure that SSR is at least close to the client as possible. We use a combination of `@nuxtjs/device` and window width to do this.

## Shortcodes

This theme unpacks WordPress shortcods into Vue components. You need to register the shortcodes in `Stackhaus Backend` in the `/functions/shortcods.php` file [here](https://github.com/funkhaus/stackhaus-backend/blob/master/functions/shortcodes.php). There are a few common ones we have built, that you can uncomment the `//add_shortcode` lines to turn on.

Common shortcodes we've included:

### Gallery

`[gallery]` that builds a grid of images. Designed to work with `<responsive-image>` and FocusHaus.

`[gallery ids="123,456,789" columns="2"]`

### Columns

`[columns]` shortcode that can be used to layout elements into columns.

```
[columns columns="2"]
    [column]Left text[/column]
    [column]Right text, or an <img> even![/column]
[/columns]
```

**For this to work, you must also turn on `[column]` shortcode, but this does not require any Vue code to work.**

### SVG images

`[svg]` That maps to an SVG from `~/assets/svgs` using the `name` prop.

```
[svg name="arrow-icon"]
```

## TODO list

TODO Boilerplate improvements:

-   Get preview URLs working: https://gist.githubusercontent.com/resir014/28bd011b909c44cb9a9bcfce8a63b0b6/raw/7e388ba7bd21c6981769c95f972865b0622c926a/functions.php
-   Add CI pipeline (TBD what we are testing for)
-   Wire up UPDATE_REFERRAL_ROUTE into router. Maybe this helps: https://github.com/funkhaus/vuehaus/blob/9adbf3cdcde3426455848fcce19f8f5d1d2c18c0/src/utils/router.js#L51-L77
-   Get SCSS functions for type settings working: https://github.com/funkhaus/stackhaus/issues/1
-   Setup ACF site options panel, move GA tracking code and frontend/backend URL settings to that panel. See: https://www.advancedcustomfields.com/resources/options-page/
-   Get a meta field added to admin dashboard for Frontend URL. Needs both staging and production.
-   Animate scrollToTop on router: https://nuxtjs.org/api/pages-scrolltotop/
-   Get this working: https://github.com/nuxt-community/sitemap-module
-   Perhaps add this for better SEO: https://developers.google.com/search/docs/guides/intro-structured-data or https://www.npmjs.com/package/nuxt-jsonld
-   Make the backend URL be protocol agnostic, this requires fixing smart link and maybe wp-menu-item?
-   Figure out a way to have better staging site URLs for Funkhaus (hide Heroku)?
-   A WordPress function to generate all post/page/category/tags/CPT routes?
-   A better auto caption/color plugin. Yes image names help SEO: https://yoast.com/image-seo/#name
-   Refactor Responsive Image component to use <picture> element and IntersectionObserver?
-   Build a InfinateScroll component that uses IntersectionObserver?
-   Build a <click-to-load-more> component that handles pagination loading (maybe pass in GQL query?)
-   Document how to use Heroku Pipelines. Connect GitHub to Pipeline. Never deploy to Production from GitHub for live site. Always promote from Staging to Production.
-   Would be nice to have a class added to body when the router is in the middle of something. See: https://stackoverflow.com/a/46063580/503546
