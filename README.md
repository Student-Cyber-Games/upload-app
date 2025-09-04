![Upload APP logo](https://github.com/user-attachments/assets/4b8145b6-db05-415b-9d1c-511b88dfff83)

GitHub action used to build and upload your web app to [Tour de App](https://tourdeapp.cz).

## ❓ How to use

There are 2 main steps to make sure your application builds successfully:

1) Make sure your project has a `.github/workflows/deploy.yml` file with the following(or similar) content:

    ```yaml
    name: Build and push Web App to TdA
    
    on:
      push:
        branches:
          - main
    
    permissions:
      contents: read
    
    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          - name: Check Out Repo
            uses: actions/checkout@v4
    
          - name: Upload to TdA
            uses: Student-Cyber-Games/upload-app@v1
            with:
              tdc_token: ${{ secrets.TDC_TOKEN }}
    ```
    
    This will trigger an upload action on every push to the `main` branch. You can change this to any branch you want.
    
> [!IMPORTANT] 
> Make sure to set the `TDC_TOKEN` secret in your repository settings. [How to create?](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository)

2) Make sure your project has a `tourdeapp.yaml` file in the root directory of your repository. This file is used to configure the build process and specify the details of your web app. Here is an example configuration:

    ```yaml
    # $schema: https://portalbush.tourde.cloud/static/schema.json
    # ... (the rest of your configuration - see guides to create in TdC documentation)
    build:
      - name: frontend
        context: .
        dockerfile: ./apps/web/Dockerfile
      - name: backend
        context: .
        dockerfile: ./apps/server/Dockerfile
    
    ```
    
    This example specifies 2 docker images to be built: `frontend` and `backend`. You can adjust the `context` and `dockerfile` paths according to your project structure.