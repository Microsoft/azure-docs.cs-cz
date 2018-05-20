---
title: Kurz – Automatizace sestavení imagí kontejnerů se službou Azure Container Registry Build
description: V tomto kurzu se naučíte konfigurovat úlohu sestavení, aby při vložení zdrojového kódu do úložiště Git automaticky spouštěla sestavení image kontejneru v cloudu.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4accbcb61e57d58100b6a4c06142dd3dc633f7f4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Kurz: Automatizace sestavení imagí kontejnerů se službou Azure Container Registry Build

Kromě funkce [Quick Build](container-registry-tutorial-quick-build.md) podporuje služba ACR Build automatizované sestavení image kontejneru Dockeru pomocí *úlohy sestavení*. V tomto kurzu použijete Azure CLI k vytvoření úlohy sestavení, která automaticky spustí sestavení image v cloudu, když potvrdíte zdrojový kód do úložiště Git.

V této druhé části série kurzů se naučíte:

> [!div class="checklist"]
> * Vytvoření úlohy sestavení
> * Otestování úlohy sestavení
> * Zobrazení stavu sestavení
> * Spuštění úlohy sestavení vložením kódu

Tento kurz předpokládá, že jste už dokončili kroky z [předchozího kurzu](container-registry-tutorial-quick-build.md). Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](container-registry-tutorial-quick-build.md#prerequisites) předchozího kurzu.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete Azure CLI používat místně, musíte mít nainstalovanou verzi Azure CLI **2.0.32** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku (CLI), přečtěte si téma [Instalace Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>Požadavky

### <a name="get-sample-code"></a>Získání vzorového kódu

Tento kurz předpokládá, že jste už dokončili kroky v [předchozím kurzu](container-registry-tutorial-quick-build.md) a že jste vytvořili fork ukázkového úložiště a naklonovali ho. Pokud jste to ještě neudělali, dokončete před pokračováním kroky v části [Požadavky](container-registry-tutorial-quick-build.md#prerequisites) předchozího kurzu.

### <a name="container-registry"></a>Registr kontejneru

Abyste mohli dokončit tento kurz, musíte mít ve svém předplatném registr kontejneru Azure. Pokud potřebujete registr, podívejte se na [předchozí kurz](container-registry-tutorial-quick-build.md) nebo článek [Rychlý start: Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Úloha sestavení

Úloha sestavení definuje vlastnosti automatizovaného sestavení, včetně umístění zdrojového kódu image kontejneru a události, která sestavení aktivuje. Když dojde k události definované v úloze sestavení, například k vložení do úložiště Git, služba ACR Build iniciuje sestavení image kontejneru v cloudu. Ve výchozím nastavení pak zapíše úspěšně sestavenou image do registru kontejneru Azure určeného v úloze.

ACR Build momentálně podporuje následující aktivační události úlohy sestavení:

* Vložení do úložiště Git
* Aktualizace základní image

## <a name="create-a-build-task"></a>Vytvoření úlohy sestavení

V této části nejdřív vytvoříte token PAT GitHubu pro použití se službou ACR Build. Potom vytvoříte úlohu sestavení, která sestavení spustí, když dojde k vložení kódu do forku úložiště.

### <a name="create-a-github-personal-access-token"></a>Vytvoření tokenu PAT GitHubu

Pokud chcete spustit sestavení při vložení do úložiště Git, bude služba ACR Build potřebovat token PAT, aby mohla k úložišti přistupovat. Token PAT v GitHubu vygenerujete pomocí tohoto postupu:

1. Přejděte na stránku vytvoření tokenu PAT na GitHubu na adrese https://github.com/settings/tokens/new.
1. Zadejte krátký **popis** tokenu, například „Ukázka úlohy ACR Build“.
1. V části **repo** (úložiště) povolte **repo:status** (úložiště:stav) a **public_repo** (veřejné_úložiště).

   ![Snímek obrazovky se stránkou generování tokenu PAT na GitHubu][build-task-01-new-token]

1. Vyberte tlačítko **Generate token** (Vygenerovat token). (Můžete být vyzváni k potvrzení hesla.)
1. Vygenerovaný token zkopírujte a uložte v **zabezpečeném umístění** (tento token použijete při definici úlohy sestavení v následující části).

   ![Snímek obrazovky s vygenerovaným tokenem PAT na GitHubu][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Vytvoření úlohy sestavení

Nyní jste dokončili kroky potřebné k tomu, abyste službě ACR Build povolili číst stav vložení a vytvářet webhooky v úložišti. Teď můžete vytvořit úlohu sestavení, která aktivuje sestavení image kontejneru při vložení do úložiště.

Nejdřív vyplňte tyto proměnné prostředí hodnotami vhodnými pro vaše prostředí. Není to nezbytně nutné, ale usnadní se tím provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné nevyplníte, musíte jednotlivé hodnoty ručně nahradit všude tam, kde se v ukázkových příkazech vyskytují.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Spuštěním následujícího příkazu [az acr build-task create][az-acr-build-task-create] teď vytvořte úlohu sestavení:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

Tato úloha sestavení určuje, že kdykoli se do *hlavní* větve úložiště určeného pomocí `--context` vloží kód, služba ACR Build z kódu v této větvi sestaví image kontejneru. Argument `--image` určuje parametrizovanou hodnotu `{{.Build.ID}}` pro část verze značky image a zajišťuje tak, že sestavená image koreluje s konkrétním sestavením a je jedinečným způsobem označená.

Výstup úspěšného příkazu [az acr build-task create][az-acr-build-task-create] je podobný následujícímu:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>Otestování úlohy sestavení

Teď máte úlohu sestavení, která vaše sestavení definuje. Pokud chcete definici sestavení otestovat, spusťte sestavení ručně pomocí příkazu [az acr build-task run][az-acr-build-task-run]:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Příkaz `az acr build-task run` ve výchozím nastavení při spuštění příkazu streamuje výstup protokolu do vaší konzoly. Výstup zobrazuje, že sestavení **aa2** je ve frontě a sestavilo se.

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>Zobrazení stavu sestavení

Příležitostně vám může přijít vhod zobrazení stavu probíhajícího sestavení, které jste nespustili ručně. Například při řešení potíží se sestaveními aktivovanými potvrzením zdrojového kódu. V této části spustíte ruční sestavení, ale potlačíte výchozí chování streamování protokolu sestavení do vaší konzoly. Potom použijete příkaz `az acr build-task logs` ke sledování probíhajícího sestavení.

Nejdřív spusťte sestavení ručně, jako jste to udělali předtím, ale zadejte argument `--no-logs`, který potlačí protokolování do vaší konzoly:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Pak pomocí příkazu `az build-task logs` zobrazte protokol aktuálně spuštěného sestavení:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Protokol aktuálně spuštěného sestavení se streamuje do vaší konzoly a měl by vypadat podobně jako následující výstup (je zobrazený zkráceně):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>Spuštění sestavení pomocí vložení

Když jste teď ručním spuštěním otestovali úlohu sestavení, spusťte ji automaticky pomocí změny zdrojového kódu.

Nejdřív se ujistěte, že jste v adresáři obsahujícím místní klon [úložiště][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Potom spuštěním následujících příkazů vytvořte, potvrďte a zapište nový soubor do forku úložiště na GitHubu:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Při spuštění příkazu `git push` můžete být vyzváni k zadání přihlašovacích údajů pro GitHub. Zadejte svoje uživatelské jméno pro GitHub a zadejte token PAT, který jste dříve vytvořili pro heslo.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Jakmile zapíšete potvrzení do úložiště, spustí se webhook vytvořený službou ACR Build a zahájí sestavení ve službě Azure Container Registry. Zobrazte protokoly sestavení pro aktuálně spuštěné sestavení, abyste mohli ověřit a sledovat průběh sestavení:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Výstup je podobný následujícímu a zobrazuje aktuálně spuštěné (nebo naposledy spuštěné) sestavení:

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>Seznam sestavení

Pokud chcete zobrazit seznam sestavení, která pro váš registr služba ACR Build dokončila, spusťte příkaz [az acr build-task list-builds][az-acr-build-task-list-builds]:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Výstup příkazu by měl vypadat podobně jako ten následující. Zobrazí se sestavení spuštěná službou ACR Build a ve sloupci označujícím způsob aktivace se u nejnovějšího sestavení zobrazí „Git Commit“:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili konfigurovat úlohu sestavení, aby při potvrzení zdrojového kódu v úložišti Git automaticky spouštěla sestavení image kontejneru v Azure. Přejděte k dalšímu kurzu, ve kterém se naučíte vytvářet úlohy sestavení, které spustí sestavení při aktualizaci základní image kontejneru.

> [!div class="nextstepaction"]
> [Automatizace sestavení při aktualizaci základní image](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
