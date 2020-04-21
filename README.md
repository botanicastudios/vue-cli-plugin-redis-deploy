redis-deploy for vue-cli
===

Usage
===

This [vue-cli](https://github.com/vuejs/vue-cli) plugin deploys your built index.html file to Redis, making it easier to run the latest version of the Vue app from a dynamic server-side application.

Installation
---
```
npm install vue-cli-plugin-redis-deploy
```

Usage
---

After installation, invoke the plugin with `vue invoke redis-deploy`.

Answer the configuration prompts. This will inject a `redis-deploy` script command into your `package.json` file.

Deploy with `npm run redis-deploy`.

Options
---

Options are set in `vue.config.js` and overridden on a per-environment basis by `.env`, `.env.staging`, `.env.production`, etc.

```js
module.exports = {
  pluginOptions: {
    redisDeploy: {
      redisUrl: "The connection string for Redis (default: redis://localhost:6379)",
      keyPrefix: "The first part of the Redis key to store your content (default: frontend)",
      assetPath: "The directory containing your built index.html file (default: dist)",
      activationSuffix: "The suffix of the Redis key containing the hash of the current index HTML (default: current)",
      activeContentSuffix: "The suffix of the Redis key that always contains the current index HTML (default: current-content)"
    }
  }
}
```

The plugin will update three Redis keys on each deploy:

- `keyPrefix:index:activationSuffix` with the hash of your current HTML. Example key: `my-app:index:current`
- `keyPrefix:index:activeContentSuffix` with the index HTML. Example key: `my-app:index:current-content`
- `keyPrefix:index:htmlHash` with the index HTML. Example key: `my-app:index:7f72ccb08633ba47434688c9bcb3ada1`

You can therefore access the active revision either by retrieving the `my-app:index:current-content` key:

```
$ redis-cli

127.0.0.1:6379> KEYS *
1) my-app:index:7f72ccb08633ba47434688c9bcb3ada1
2) my-app:index:current
3) my-app:index:current-content

127.0.0.1:6379> GET my-app:index:current-content
"<html>...</html"
```

... or by retrieving the active revision first, and using that to build the Redis key containing the active HTML:

```
$ redis-cli

127.0.0.1:6379> GET my-app:index:current
"7f72ccb08633ba47434688c9bcb3ada1"

127.0.0.1:6379> GET my-app:index:7f72ccb08633ba47434688c9bcb3ada1
"<html>...</html"

```

You can of course also access older revisions.

Credits
---

The plugin is _heavily_ inspired by [vue-cli-plugin-s3-deploy](https://github.com/multiplegeorges/vue-cli-plugin-s3-deploy) and [ember-cli-deploy-redis](https://github.com/ember-cli-deploy/ember-cli-deploy-redis).