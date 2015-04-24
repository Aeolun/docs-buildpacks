---
title: Tips for Node.js Applications
---

_This page assumes you are using cf CLI v6._

This topic provides Node-specific information to supplement the general guidelines in the  [Deploy an Application](../../devguide/deploy-apps/deploy-app.html) topic.

## <a id='packagejson'></a> Application Package File ##

Cloud Foundry expects a `package.json` in your Node.js application.
You can specify the version of Node.js you want to use in the `engine` node of
your `package.json` file.
As of April, 2015, and build pack version 1.3 Cloud Foundry uses 0.12.2 as the default. See [the github](https://github.com/cloudfoundry/nodejs-buildpack) page for the most up to date information.

Example `package.json` file:

~~~JSON
{
  "name": "default-nodejs-app",
  "version": "0.0.1",
  "author": "Your Name",
  "dependencies": {
    "express": "3.4.8",
    "consolidate": "0.10.0",
    "express": "3.4.8",
    "swig": "1.3.2",
  },
  "engines": {
    "node": "0.12.2",
    "npm": "2.7.4"
  }
}
~~~

## <a id='port'></a> Application Port ##

You need to use the VCAP\_APP\_PORT environment variable to determine which
port your application should listen on.
In order to also run your application locally, you may want to make port 3000
the default:

~~~javascript

app.listen(process.env.VCAP_APP_PORT || 3000);

~~~

## <a id='start'></a> Application Start Command ##

Node.js applications require a start command.
You can specify a Node.js applications's web start command in a Procfile or the
application deployment manifest.

You will be asked if you want to save your configuration the first time you
deploy.
This will save a `manifest.yml` in your application with the settings you
entered during the initial push.
Edit the `manifest.yml` file and create a start command as follows:

~~~yaml
---
applications:
- name: my-app
  command: node my-app.js
... the rest of your settings ...
~~~

Alternately, specify the start command with `cf push -c`.

<pre class="termainl">
$ cf push my-app -c "node my-app.js"
</pre>

## <a id='nodemodules'></a> Application Bundling ##

You do not need to run `npm install` before deploying your application.
Cloud Foundry will run it for you when your application is pushed.
If you would prefer to run `npm install` and create a `node_modules` folder
inside of your application, this is also supported.

## <a id='discovery'></a> Solving Discovery Problems ##

If Cloud Foundry does not automatically detect that your application is a
Node.js application, you can override the auto-detection by specifying the
Node.js buildpack.

Add the buildpack into your `manifest.yml` and re-run `cf push` with your
manifest:

~~~yaml
---
applications:
- name: my-app
  buildpack: https://github.com/cloudfoundry/nodejs-buildpack
... the rest of your settings ...
~~~

Alternately, specify the buildpack on the command line with `cf push -b`:

<pre class="termainl">
$ cf push my-app -b https://github.com/cloudfoundry/nodejs-buildpack
</pre>

## <a id='services'></a> Binding Services ##

Refer to [Configure Service Connections for Node.js](./node-service-bindings.html).

## <a id='buildpack'></a> About the Node.js Buildpack ##

For information about using and extending the Node.js buildpack in Cloud
Foundry, see https://github.com/cloudfoundry/nodejs-buildpack

The table below lists:

* **Resource** --- The software installed by the buildpack.
* **Available Versions** --- The versions of each software resource that are available from the buildpack.
* **Installed by Default** --- The version of each software resource that is installed by default.
* **To Install a Different Version** --- How to change the buildpack to install a different version of a software resource.

----------------------------

 **This page was last updated on April 23, 2015 based on version 1.3 of the build pack. Run `cf buildpacks` to verify your version.**

| Resource | Available Versions | Installed by Default| To Install a Different Version
| --------- | --------- | --------- |---------
| Node.js | 0.8.27 <br> 0.8.28 <br>0.9.11<br>0.9.12<br>0.10.37<br>0.10.38<br>0.11.15<br>0.11.16<br>0.12.1<br>0.12.2| latest version of 0.12.2 | To change the default version installed by the buildpack, see <br>“Supported binary dependencies” on https://github.com/cloudfoundry/nodejs-buildpack. <br><br>To specify the versions of Node.js and npm an application <br>requires, edit the application’s `package.json`, as described in “Specifying a node version” on  https://github.com/cloudfoundry/nodejs-buildpack.
| npm | 2.7.4-1.3.2 | latest version of 2.7.4 | as above

## <a id='env-var'></a>Environment Variables ##

You can access environments variable programmatically.

For example, you can obtain `VCAP_SERVICES` like this:

```
process.env.VCAP_SERVICES
```

Environment variables available to you include both those [defined by the DEA]
(../../devguide/deploy-apps/environment-variable.html#dea-set)
and those defined by the Node.js buildpack, described below.

### <a id='BUILD-DIR'></a>BUILD_DIR ###
Directory into which Node.js is copied each time a Node.js application is run.

### <a id='CACHE-DIR'></a>CACHE_DIR ###

Directory that Node.js uses for caching.

### <a id='PATH'></a>PATH ###

The system path used by Node.js.

`PATH=/home/vcap/app/bin:/home/vcap/app/node_modules/.bin:/bin:/usr/bin`

