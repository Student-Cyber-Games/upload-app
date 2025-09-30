# Reference for `tourdeapp.yaml`

Schema URL: https://portalbush.tourde.cloud/static/schema.json  

This is a reference manual for the `tourdeapp.yaml` configuration file used by the Tour de Cloud (TdC) platform. 
This file is essential for defining how your web application should be built and deployed to TdC.

> [!TIP]
> Want to get autocomplete? Paste the following into the start of the file:  
> `# $schema: https://portalbush.tourde.cloud/static/schema.json`

## 📝 File Location

The `tourdeapp.yaml` is a YAML which should be placed in the root directory of your project repository.

> [!IMPORTANT]
> The file HAS to be named exactly `tourdeapp.yaml`, different file extensions, such as `.yml`, are not supported.

## 🗜️ Environment substitution

If `tourdeapp.yaml` is used with our `upload-app` GitHub action, it supports environment variable substitution using the mustache syntax `{{ENV_VAR}}`.
You just have to pass an environment variable named `ENV_VAR` to the action, and it will replace all instances of `{{ENV_VAR}}` in the `tourdeapp.yaml` file with the value of the environment variable:

```yaml
- name: Upload to TdA
  uses: Student-Cyber-Games/upload-app@v1
  env:
    ENV_VAR: ${{ secrets.API_KEY_ENV_VAR }}
```

## ⚙️ Configuration Options

### `exposedPort`
- **Type**: Integer
- **Description**: Specifies the port on which your application will be accessible. This should match one of the ports specified
later in the `containers` section.
- **Required**: Yes

### `containers`
- **Type**: List of container definitions
- **Description**: Defines the containers that make up your application. Each container can have its own image, ports, and environment variables.
- **Required**: Yes

#### Container Definition
Each container in the `containers` list can have the following properties:
- `name`: (String, Required, `^[a-z0-9]([-a-z0-9]*[a-z0-9])?$`) The name of the container.
- `image`: (String, Required) The Docker image to be used for the container.
- `tag`: (String, Required) The tag of the Docker image. Will be overridden by the Git commit SHA if using our registry(`regitry.tourde.cloud`).
- `port`: (Integer, Required) The port on which the container will listen.
- `env`: (Dictionary of environment variable definitions, Optional) Environment variables to be set in the container.
  - Example:
    ```yaml
    env:
      ENV_VAR_NAME: "value"
      ANOTHER_ENV_VAR: "another_value"
    ```
- `command`: (String, Optional) The command to run in the container.

### `build`
- **Type**: List of build definitions
- **Description**: Specifies the build process for your application's Docker images.
- **Required**: Yes

#### Build Definition
Each build target in the `build` list can have the following properties:
- `name`: (String, Required, `^[a-z0-9]([-a-z0-9]*[a-z0-9])?$`) The name of the image.
- `context`: (String, Required) The build context for the Docker build.
- `dockerfile`: (String, Required) The path to the Dockerfile to be used for building the image.
- `args`: (Dictionary, Optional) Build-time arguments to be passed to the Docker build process.
  - Example:
    ```yaml
    args:
      ARG_NAME: "value"
      ANOTHER_ARG: {{ENV_VAR}}
    ```