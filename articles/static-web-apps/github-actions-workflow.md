---
title: Pracovní postupy akcí GitHubu pro statické Web Apps Azure
description: Naučte se používat úložiště GitHub k nastavení průběžného nasazování na statické Web Apps Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 4f1f432da33bded4fc0f04170673e5943dec5fb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311324"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Pracovní postupy akcí GitHubu pro Azure static Web Apps Preview

Když vytvoříte nový prostředek služby Azure static Web App, Azure vygeneruje pracovní postup akcí GitHubu pro řízení průběžného nasazování aplikace. Pracovní postup je založený na souboru YAML. Tento článek podrobně popisuje strukturu a možnosti souboru pracovního postupu.

Nasazení jsou iniciována [triggery](#triggers), které spouštějí [úlohy](#jobs) , které jsou definovány jednotlivými [kroky](#steps).

## <a name="file-location"></a>Umístění souboru

Když propojíte úložiště GitHub s Azure static Web Apps, do úložiště se přidá soubor pracovního postupu.

Chcete-li zobrazit vygenerovaný soubor pracovního postupu, postupujte podle těchto kroků.

1. Otevřete úložiště aplikace na GitHubu.
1. Na kartě _kód_ klikněte na `.github/workflows` složku.
1. Klikněte na soubor s názvem, který vypadá nějak takto `azure-static-web-apps-<RANDOM_NAME>.yml` :.

Soubor YAML v úložišti bude vypadat podobně jako v následujícím příkladu:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: 'upload'
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          app_location: '/' # App source code path
          api_location: 'api' # Api source code path - optional
          output_location: 'dist' # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          action: 'close'
```

## <a name="triggers"></a>Aktivační události

[Trigger](https://help.github.com/actions/reference/events-that-trigger-workflows) akcí GitHubu upozorní pracovní postup akcí GitHubu, aby spustil úlohu na základě triggerů událostí. Triggery jsou uvedeny pomocí `on` vlastnosti v souboru pracovního postupu.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

Pomocí nastavení přidružených k této `on` vlastnosti můžete definovat, které větve aktivují úlohu, a nastavit triggery, které se aktivují pro různé stavy žádostí o přijetí změn.

V tomto příkladu se pracovní postup spustí, když se změní _Hlavní_ větev. Změny, které spouštějí pracovní postup, zahrnují vkládání potvrzení a otevírání žádostí o přijetí změn proti vybrané větvi.

## <a name="jobs"></a>Úlohy

Každá aktivační událost události vyžaduje obslužnou rutinu události. [Úlohy](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definují, co se stane, když se aktivuje událost.

V souboru pracovního postupu statického Web Apps jsou k dispozici dvě dostupné úlohy.

| Název                     | Description                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Provede se při vložení potvrzení změn nebo otevření žádosti o přijetí změn pro větev uvedenou ve `on` Vlastnosti.          |
| `close_pull_request_job` | Provede se jenom při zavření žádosti o přijetí změn, která odebere pracovní prostředí vytvořené z žádostí o přijetí změn. |

## <a name="steps"></a>Postup

Kroky jsou sekvenční úlohy pro úlohu. Krok provádí akce, jako je instalace závislostí, spouštění testů a nasazování vaší aplikace do produkčního prostředí.

Soubor pracovního postupu definuje následující postup.

| Úloha                      | Postup                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Rezervuje úložiště v prostředí akce.<li>Vytvoří a nasadí úložiště do statického Web Apps Azure.</ol> |
| `close_pull_request_job` | <ol><li>Upozorňuje na statickou Web Apps Azure, že se uzavřela žádost o získání dat.</ol>                                                        |

## <a name="build-and-deploy"></a>Sestavení a nasazení

Krok s názvem `Build and Deploy` buildy a nasadí se do vaší instance statického Web Apps Azure. V části `with` můžete přizpůsobit následující hodnoty pro vaše nasazení.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

`repo_token`Hodnoty, `action` a `azure_static_web_apps_api_token` jsou nastaveny za vás službou Azure static Web Apps by se neměly ručně změnit.

## <a name="custom-build-commands"></a>Vlastní příkazy sestavení

Můžete mít jemně odstupňovanou kontrolu nad tím, které příkazy se spustí během nasazení. V části úlohy je možné definovat následující příkazy `with` .

Nasazení vždy volá `npm install` před libovolným vlastním příkazem.

| Příkaz             | Popis                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Definuje vlastní příkaz, který se má spustit během nasazování aplikace statického obsahu.<br><br>Například pro konfiguraci výrobního sestavení pro úhlovou aplikaci vytvořte skript npm s názvem `build-prod` ke spuštění `ng build --prod` a zadejte `npm run build-prod` jako vlastní příkaz. Pokud necháte pole prázdné, pracovní postup se pokusí spustit `npm run build` příkazy nebo `npm run build:azure` . |
| `api_build_command` | Definuje vlastní příkaz, který se spustí během nasazování aplikace Azure Functions API.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>Přeskočit Build aplikace

Pokud potřebujete plnou kontrolu nad tím, jak je vytvořená aplikace front-end, můžete do pracovního postupu přidat vlastní kroky sestavení. Pak můžete nakonfigurovat akci statického Web Apps pro obejít procesu automatického sestavení a stačí nasadit aplikaci vytvořenou v předchozím kroku.

Pokud chcete přeskočit sestavení aplikace, nastavte `skip_app_build` na `true` a `app_location` umístění složky, která se má nasadit.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Vlastnost         | Popis                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Nastavením hodnoty na `true` přeskočíte sestavování aplikace front-end. |

> [!NOTE]
> Můžete přeskočit sestavení pouze pro front-endové aplikace. Pokud vaše aplikace obsahuje rozhraní API, bude i nadále sestavena akcí na základě statické Web Apps GitHubu.

## <a name="route-file-location"></a>Umístění souboru tras

Pracovní postup můžete přizpůsobit tak, aby vyhledal [staticwebapp.config.js](routes.md) v libovolné složce v úložišti. Následující vlastnost může být definována v `with` oddílu úlohy.

| Vlastnost          | Popis                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Definuje umístění adresáře, kde se nachází _staticwebapp.config.jsv_ souboru. Toto umístění je relativní vzhledem ke kořenu úložiště. |

Explicitní informace o umístění vašeho _staticwebapp.config.jsv_ souboru je zvláště důležité, pokud se ve výchozím kroku sestavení rozhraní front-end nepřesouvá tento soubor do `output_location` výchozího nastavení.

## <a name="environment-variables"></a>Proměnné prostředí

Můžete nastavit proměnné prostředí pro sestavení prostřednictvím `env` oddílu konfigurace úlohy.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Podpora Monorepo

Monorepo je úložiště, které obsahuje kód pro více než jednu aplikaci. Ve výchozím nastavení sleduje statický soubor pracovního postupu Web Apps všechny soubory v úložišti, ale můžete je upravit tak, aby se nacházela na jednu aplikaci. Proto pro monorepos každá statická aplikace má vlastní konfigurační soubor, který se nachází vedle sebe ve složce _. GitHub/pracovní postupy_ úložiště.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Chcete-li zacílit soubor pracovního postupu na jednu aplikaci, zadejte cesty `push` v `pull_request` částech a.

Následující příklad ukazuje, jak přidat `paths` uzel do `push` `pull_request` oddílů a souboru s názvem _Azure-static-Web-Apps-Purple-Pond. yml_.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

V této instanci spustí nové sestavení pouze změny provedené v následujících souborech:

- Všechny soubory ve složce _app1_
- Všechny soubory ve složce _api1_
- Změny souboru pracovního postupu _Azure-static-Web-Apps-Purple-Pond. yml_ aplikace

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontrola žádostí o přijetí změn v předprodukčních prostředích](review-publish-pull-requests.md)
