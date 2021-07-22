# Thy Street API clients generator

This repo contains the information and the tools needed to automatically generate API clients for the `Thy Street` service in [any language][0] supported by OpenAPI generator.

The OpenAPI generator is orchestrated by a python tool called [apigentools][1] that let us keep the spec files and the configuration bits in one single repo (this one) without duplicating the boilerplate on each git repo hosting the actual clients.

## [IMPORTANT] Client generation process

The process to genereate the clients explained in paragraphs below is fully automated through GitHub actions. The workflow will start every time code is pushed to `master` branch. If the workflow completes successfully, a PR will be opened for each client in their respective git repositories. See the [actions page](https://github.com/bcmi-labs/clients-iot-api/actions) to monitor the status of a workflow.

## Sample clients

Following clients have been successfully generated with the present workflow:

* [Arduino/PlatformIO](https://github.com/thystreet/thystreet-pio-sdk)
* [Javascript](https://github.com/thystreet/thystreet-javascript-sdk)
* [Python](https://github.com/thystreet/thystreet-python-sdk)

## Generation process

part from the initial setup (the procedures needed to setup this git repo), the ideal workflow consists in updating this repo every time the API service changes, re-generate all the clients, push the generated code to the git repo it belongs to and release the updated clients (this last step may vary depending on the programming language).

The operations are detailed in the following paragraphs.

### Requirements

To be able to run the workflow locally in a developmnent environment, you'll
need the following:

* Python 3.7+
* Apigentools 1.0+ (`pip install apigentools`)

### Get an updated version of the API specification

In this case the generated specs can be found at [thystreet.yaml][2]. To be more future proof and to leverage the latest versions of the tools available, we're using the version 3 of the OpenAPI for the generator.

### Validate the Spec

The generator can validate the content of the spec, this should likely go in a CI step:

```sh
apigentools validate
```

### Generate the clients

To generate clients and documentation using a template, and apply pathces before template filling, this is the command to run:

```sh
apigentools generate
```

The previous command is fine for local development but in case the generated code has to be pushed upstream to each client's repository, run the previous command as follows:

```sh
 apigentools generate --clone-repo
```

### Push generated code to each client's git repository

`apigentools` has a simple command that can be invoked for each client generated that will push the resulting code to different repos, using different branches so the code can be reviewed and merged through a regular PR:

```sh
apigentools push
```

A release process should take from here and shouldn't be part of this workflow.

### Render upstream templates

This step is not currently necessary as the `generate` step does it implicitly, but in case you want to only apply patches to the original (upstream) templates **before** the generation step, you can run it to check how the pathces will modify the generated code. The step consists of cloning the openapi-generator repo, applying one or more patches in the form of patch files to it and copy the relevant templates in the folder `templates`:

```sh
apigentools templates
```

There are other ways to provide upstream templates other than the currently configured, please refer to `apigentools` docs for more details.

## Customization

The code generator can be fine tuned using [templates][3]. By patching existing templates for each language or adding new ones, any aspect of the resulting client can be adjusted to fit custom needs: the readme, the docs, the tests, the model classes themselves can be changed downstream, and changes will reflect on the resulting client.

[0]: https://openapi-generator.tech/docs/generators
[1]: https://github.com/DataDog/apigentools
[2]: spec/v1/thystreet.yaml
[3]: https://openapi-generator.tech/docs/templating
