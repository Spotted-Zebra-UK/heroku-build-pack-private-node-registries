# heroku-build-pack-private-node-registries

Sets up `.npmrc` with required private NPM registries.

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
NPM_REGISTRIES: "namespace-1|https://registry-1.com/|token-1;namespace-2|http://registry-2.com/|token-2"
```

The above variable will create the following `.npmrc`:

```bash
@namespace-1:registry=https://registry-1.com/
//registry-1.com/:_authToken=token-1
@namespace-2:registry=http://registry-2.com/
//registry-2.com/:_authToken=token-2
```

# Example Github action

Within this example we are assuming you are using this buildpack and you are building your application then with the `heroku/nodejs` buildpack.

```yaml
name: deploy-to-heroku
on:
  pull_request:
    types:
      - closed
    branches:
      - main

jobs:
  deploy-to-heroku:
    name: 'Deploy to heroku'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: my-app-name
          heroku_email: user@example.com
        env:
          HD_NODE_MODULES_CACHE: false
          HD_USE_YARN_CACHE: false
          HD_NPM_REGISTRIES: "my-npm-namespace|https://registry.com|${{ secrets.REGISTRY_TOKEN }}"
```