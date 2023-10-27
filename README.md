# heroku-build-pack-private-node-registries

Sets up `.yarnrc.yml` with required private NPM registries.

# How to use

Add this buildpack before the `heroku/nodejs` or any other buildpack that installs your nodejs application.
You can do this either through Heroku cli, or directly on the GUI of Heroku for your application.

CLI:
```bash
heroku buildpacks:set https://github.com/spotted-zebra-uk/heroku-build-pack-private-node-registries -a my-app
```

GUI:

Paste the URL of this repository in the Settings of your application under buildpack and place it **BEFORE** the actual node buildpack.

# How to pass the data

The environment variable name is `NPM_REGISTRIES`.

If you are using the Github action `akhileshns/heroku-deploy` you will need to prefix your variable with `HD` - `HD_NPM_REGISTRIES` so it is passed into Heroku from your action directly, and it will appear under your configuration vars with the proper naming.

The format of the variable:

```
HD_NPM_REGISTRIES: "namespace-1|https://registry-1.com/|token-1;namespace-2|https://registry-2.com/|token-2"
```

The above variable will create the following `.yarnrc.yml`:

```yaml
npmScopes:
  namespace-1:
    npmAuthToken: "token-1"
    npmRegistryServer: https://registry-1.com/
  namespace-2:
    npmAuthToken: "token-2"
    npmRegistryServer: https://registry-2.com/
```