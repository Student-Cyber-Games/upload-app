[🇺🇸 English README](README.md)

GitHub akce používaná k **sestavení** a **nahrání** vašich Docker obrazů do [Tour de Cloud](https://tourde.cloud).

> [!WARNING]
> Tato akce selže, pokud tým ještě nezaplatil startovné za soutěž.

## ❓ Jak na to

Jsou 2 hlavní kroky, aby se tvoje aplikace úspěšně sestavila a nahrála:

1) Ujisti se, že tvůj projekt má soubor `.github/workflows/deploy.yml` s následujícím (nebo podobným) obsahem:

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

   Toto spustí akci sestavení a nahrání při každém pushi do větve `main`. Můžeš to ale změnit na jakoukoli větev, kterou chceš.

> [!IMPORTANT]
> Ujisti se, že v nastavení repozitáře máš nastavený secret `TDC_TOKEN`. [Jak vytvořit?](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository)

2) Ujisti se také, že tvůj projekt má v kořenovém adresáři repozitáře soubor `tourdeapp.yaml`. Tento soubor se používá k nastavení procesu sestavení a specifikaci detailů tvé webové aplikace. Zde je příklad konfigurace:

    ```yaml
    # $schema: https://portalbush.tourde.cloud/static/schema.json
    # ... (zbytek konfigurace - viz průvodce pro vytvoření v dokumentaci TdC)
    build:
      - name: frontend
        context: .
        dockerfile: ./apps/web/Dockerfile
      - name: backend
        context: .
        dockerfile: ./apps/server/Dockerfile
    
    ```

    Tento příklad specifikuje 2 docker obrazy k sestavení: `frontend` a `backend`(bude se lišit pro tvojí aplikaci). Můžeš upravit cesty `context` a `dockerfile` podle struktury svého projektu.
> [!TIP]
> Soubor `tourdeapp.yaml` také podporuje předávání [build arguments](https://docs.docker.com/reference/cli/docker/buildx/build/#build-arg) a náhradu proměnných do Dockerfile. To je užitečné, pokud potřebuješ předat nějaké tajné klíče nebo jiné proměnné prostředí do svého Dockerfile. Můžeš to udělat takto:
> ```yaml
> build:
>  - name: frontend
>    context: .
>    dockerfile: ./apps/web/Dockerfile
>    args:
>      REACT_APP_API_URL: "https://api.example.com"
>      REACT_APP_API_KEY: ${API_KEY_ENV_VAR}
> ```
> Avšak aby se proměnná `API_KEY_ENV_VAR` správně nahradila, musíš ji vložit do sekce `env` GitHub akce, například takto:
> ```yaml
> env:
>   API_KEY_ENV_VAR: ${{ secrets.API_KEY_ENV_VAR }}
> ```