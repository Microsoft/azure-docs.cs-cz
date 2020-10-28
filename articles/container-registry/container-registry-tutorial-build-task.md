---
title: Kurz – sestavení image při potvrzení kódu
description: V tomto kurzu se naučíte konfigurovat úlohu Azure Container Registry, která automaticky aktivuje sestavení imagí kontejneru v cloudu při potvrzení zdrojového kódu do úložiště Git.
ms.topic: tutorial
ms.date: 05/04/2019
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: a4d0a2d3d98bec28e4d6389c9069db3e7b395597
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745558"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Kurz: Automatizace sestavení imagí kontejneru v cloudu při potvrzení zdrojového kódu

Kromě [Rychlé úlohy](container-registry-tutorial-quick-task.md)ACR úlohy podporují automatické sestavení imagí kontejneru Docker v cloudu, když potvrdíte zdrojový kód do úložiště Git. Podporované kontexty Git pro úlohy ACR zahrnují veřejné nebo soukromé úložiště GitHubu nebo Azure.

> [!NOTE]
> V současné době ACR úkoly nepodporují potvrzení změn nebo triggery žádostí o přijetí změn v úložištích GitHub Enterprise.

V tomto kurzu vaše úloha ACR sestaví a nahraje jednu Image kontejneru určenou v souboru Dockerfile při potvrzení zdrojového kódu do úložiště Git. Pokud chcete vytvořit [úlohu s více kroky](container-registry-tasks-multi-step.md) , která používá soubor YAML k definování kroků pro sestavování, nasdílení a volitelně testování více kontejnerů při potvrzení kódu, přečtěte si téma [kurz: spuštění pracovního postupu s více kroky v cloudu při potvrzení zdrojového kódu](container-registry-tutorial-multistep-task.md). Přehled úloh ACR najdete v tématu [Automatizace oprav operačního systému a architektury s úlohami ACR](container-registry-tasks-overview.md) .

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření úkolu
> * Test úlohy
> * Zobrazení stavu úkolů
> * Aktivace úlohy potvrzením kódu

Tento kurz předpokládá, že jste už dokončili kroky z [předchozího kurzu](container-registry-tutorial-quick-task.md). Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](container-registry-tutorial-quick-task.md#prerequisites) předchozího kurzu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete rozhraní příkazového řádku Azure používat místně, musíte mít nainstalovanou verzi Azure CLI **2.0.46** nebo novější a přihlášeni pomocí [AZ Login][az-login]. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku (CLI), přečtěte si téma [Instalace Azure CLI][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-the-build-task"></a>Vytvoření úlohy sestavení

Dokončili jste kroky potřebné k tomu, abyste službě ACR Tasks povolili číst stav potvrzení a vytvářet webhooky v úložišti. Teď můžete vytvořit úlohu, která aktivuje sestavení image kontejneru při potvrzení do úložiště.

Nejdřív vyplňte tyto proměnné prostředí hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné prostředí neplníte, je nutné ručně nahradit každou hodnotu, pokud se zobrazí v ukázkových příkazech.

[![Vložit spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Teď úlohu vytvořte spuštěním následujícího příkazu [AZ ACR Task Create][az-acr-task-create] :

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Pokud jste už dříve vytvořili úlohy ve verzi Preview pomocí příkazu `az acr build-task`, tyto úlohy bude potřeba vytvořit znovu pomocí příkazu [az acr task][az-acr-task].

Tato úloha určuje, že kdykoli se do *hlavní* větve úložiště určeného parametrem `--context` potvrdí kód, služba ACR Tasks z kódu v této větvi sestaví image kontejneru. `--file`K sestavení image se používá souboru Dockerfile určený z kořenu úložiště. Argument `--image` určuje parametrizovanou hodnotu `{{.Run.ID}}` pro část verze značky image a zajišťuje tak, že sestavená image koreluje s konkrétním sestavením a je jedinečným způsobem označená.

Výstup úspěšného příkazu [az acr task create][az-acr-task-create] je podobný následujícímu:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>Otestování úlohy sestavení

Teď máte úlohu, která definuje vaše sestavení. Pokud chcete otestovat kanál buildu, aktivujte sestavení ručně pomocí příkazu [az acr task run][az-acr-task-run]:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Příkaz `az acr task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly.

```output
2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>Spuštění sestavení pomocí vložení

Když jste teď ručním spuštěním otestovali úlohu, aktivujte ji automaticky pomocí změny zdrojového kódu.

Nejdřív se ujistěte, že jste v adresáři obsahujícím místní klon [úložiště][sample-repo]:

```console
cd acr-build-helloworld-node
```

Potom spuštěním následujících příkazů vytvořte, potvrďte a zapište nový soubor do forku úložiště na GitHubu:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Při spuštění příkazu `git push` můžete být vyzváni k zadání přihlašovacích údajů pro GitHub. Zadejte svoje uživatelské jméno pro GitHub a zadejte token PAT, který jste dříve vytvořili pro heslo.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Jakmile odešlete potvrzení do úložiště, spustí se webhook vytvořený službou ACR Tasks a zahájí sestavení ve službě Azure Container Registry. Zobrazte protokoly pro aktuálně spuštěnou úlohu, abyste mohli ověřit a monitorovat průběh sestavení:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Výstup je podobný následujícímu a zobrazuje aktuálně spuštěnou (nebo naposledy spuštěnou) úlohu:

```output
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>Seznam sestavení

Pokud chcete zobrazit seznam spuštění úloh, která služba ACR Tasks dokončila pro váš registr, spusťte příkaz [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Výstup příkazu by měl vypadat podobně jako ten následující. Zobrazí se spuštění provedená službou ACR Tasks a ve sloupci označujícím způsob aktivace se u nejnovější úlohy zobrazí „Git Commit“:

```output
RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí úlohy automaticky aktivovat sestavení image kontejneru v Azure při potvrzení zdrojového kódu do úložiště Git. Přejděte k dalšímu kurzu, ve kterém se naučíte vytvářet úlohy, které aktivují sestavení při aktualizaci základní image kontejneru.

> [!div class="nextstepaction"]
> [Automatizace sestavení při aktualizaci základní image](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-login]: /cli/azure/reference-index#az-login



