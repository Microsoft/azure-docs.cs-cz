---
title: Tutorial - Trigger image build on base image update
description: In this tutorial, you learn how to configure an Azure Container Registry Task to automatically trigger container image builds in the cloud when a base image is updated.
ms.topic: tutorial
ms.date: 08/12/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: b89bf0364165822368647b4c5b773bf422902aec
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456144"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Tutorial: Automate container image builds when a base image is updated in an Azure container registry 

ACR Tasks podporuje automatizované spouštění sestavení při aktualizaci základní image, například při opravě operačního systému nebo architektury aplikace v jedné ze základních imagí. V tomto kurzu zjistíte, jak v ACR Tasks vytvořit úlohu, která aktivuje sestavení v cloudu, když se do vašeho registru odešle základní image kontejneru.

V poslední části série tohoto kurzu se naučíte:

> [!div class="checklist"]
> * Sestavit základní image
> * Vytvořit úlohu sestavení image aplikace
> * Aktualizovat základní image tak, aby aktivovala úlohu image aplikace
> * Zobrazit aktivovanou úlohu
> * Ověřit aktualizovanou image aplikace

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete Azure CLI používat místně, musíte mít nainstalovanou verzi Azure CLI **2.0.46** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. If you need to install or upgrade the CLI, see [Install Azure CLI][azure-cli].

## <a name="prerequisites"></a>Předpoklady

### <a name="complete-the-previous-tutorials"></a>Dokončení předchozích kurzů

Tento kurz předpokládá, že jste už dokončili kroky v prvních dvou kurzech série:

* Vytvoření registru kontejneru Azure
* Vytvoření forku ukázkového úložiště
* Klonování ukázkového úložiště
* Vytvoření tokenu PAT GitHubu

Než budete pokračovat, dokončete (pokud jste to už neudělali) první dva kurzy:

[Sestavení imagí kontejnerů v cloudu pomocí Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatizace sestavení imagí kontejnerů pomocí Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Konfigurace prostředí

Tyto proměnné prostředí naplňte hodnotami vhodnými pro vaše prostředí. Tento krok není nezbytně nutný, ale usnadní provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné prostředí nevyplníte, musíte jednotlivé hodnoty ručně nahradit všude tam, kde se v ukázkových příkazech vyskytují.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Základní image

Soubory Dockerfile definující většinu imagí kontejnerů specifikují nadřazenou image, na které jsou založené. Tato image se často označuje jako *základní image*. Base images typically contain the operating system, for example [Alpine Linux][base-alpine] or [Windows Nano Server][base-windows], on which the rest of the container's layers are applied. They might also include application frameworks such as [Node.js][base-node] or [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Aktualizace základní image

Základní image často aktualizuje maintainer image, aby zahrnovala nové funkce nebo vylepšení operačního systému nebo architektury v imagi. Dalším běžným důvodem pro aktualizaci základní image jsou opravy zabezpečení.

Když se základní image aktualizuje, budete muset znovu sestavit jakékoli image kontejnerů v registru, které jsou na ní založené, aby zahrnovaly nové funkce a opravy. ACR Tasks zahrnuje možnost, která za vás při aktualizaci základní image kontejnerů automaticky tyto image sestaví.

### <a name="tasks-triggered-by-a-base-image-update"></a>Tasks triggered by a base image update

* For image builds from a Dockerfile, an ACR task detects dependencies on base images in the following locations:

  * The same Azure container registry where the task runs
  * Another Azure container registry in the same region 
  * A public repo in Docker Hub 
  * A public repo in Microsoft Container Registry

   If the base image specified in the `FROM` statement resides in one of these locations, the ACR task adds a hook to ensure the image is rebuilt any time its base is updated.

* Currently, an ACR tasks only tracks base image updates for application (*runtime*) images. It doesn't track base image updates for intermediate (*buildtime*) images used in multi-stage Dockerfiles.  

* When you create an ACR task with the [az acr task create][az-acr-task-create] command, by default the task is *enabled* for trigger by a base image update. That is, the `base-image-trigger-enabled` property is set to True. If you want to disable this behavior in a task, update the property to False. For example, run the following [az acr task update][az-acr-task-update] command:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* To enable an ACR task to determine and track a container image's dependencies -- which include its base image -- you must first trigger the task **at least once**. For example, trigger the task manually using the [az acr task run][az-acr-task-run] command.

* To trigger a task on base image update, the base image must have a *stable* tag, such as `node:9-alpine`. This tagging is typical for a base image that is updated with OS and framework patches to a latest stable release. If the base image is updated with a new version tag, it does not trigger a task. For more information about image tagging, see the [best practices guidance](container-registry-image-tag-version.md). 

### <a name="base-image-update-scenario"></a>Scénář aktualizace základní image

Tento kurz vás provede scénářem aktualizace základní image. The [code sample][code-sample] includes two Dockerfiles: an application image, and an image it specifies as its base. In the following sections, you create an ACR task that automatically triggers a build of the application image when a new version of the base image is pushed to the same container registry.

[Dockerfile-app][dockerfile-app]: A small Node.js web application that renders a static web page displaying the Node.js version on which it's based. Řetězec verze je simulovaný: zobrazuje obsah proměnné prostředí `NODE_VERSION`, která je definovaná v základní imagi.

[Dockerfile-base][dockerfile-base]: The image that `Dockerfile-app` specifies as its base. It is itself based on a [Node][base-node] image, and includes the `NODE_VERSION` environment variable.

V následujících částech vytvoříte úlohu, aktualizujete hodnotu `NODE_VERSION` v souboru Dockerfile základní image a potom použijete ACR Tasks k sestavení základní image. Když úloha ACR odešle do registru novou základní image, automaticky aktivuje sestavení image aplikace. Volitelně můžete spustit image kontejneru aplikace místně, abyste se mohli podívat na různé řetězce verze v sestavených imagích.

In this tutorial, your ACR task builds and pushes an application container image specified in a Dockerfile. ACR Tasks can also run [multi-step tasks](container-registry-tasks-multi-step.md), using a YAML file to define steps to build, push, and optionally test multiple containers.

## <a name="build-the-base-image"></a>Sestavit základní image

Začněte sestavením základní image pomocí *rychlé úlohy* v ACR Tasks. Jak je popsáno v [prvním kurzu](container-registry-tutorial-quick-task.md) série, tímto postupem se nejen sestaví image, ale v případě úspěšného sestavení se odešle do registru kontejneru.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Vytvoření úkolu

Next, create a task with [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> If you previously created tasks during the preview with the `az acr build-task` command, those tasks need to be re-created using the [az acr task][az-acr-task] command.

Tato úloha je podobná rychlé úloze vytvořené v [předchozím kurzu](container-registry-tutorial-build-task.md). Dává službě ACR Tasks pokyn aktivovat sestavení image, když se do úložiště určeného parametrem `--context` odešlou potvrzení. While the Dockerfile used to build the image in the previous tutorial specifies a public base image (`FROM node:9-alpine`), the Dockerfile in this task, [Dockerfile-app][dockerfile-app], specifies a base image in the same registry:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

This configuration makes it easy to simulate a framework patch in the base image later in this tutorial.

## <a name="build-the-application-container"></a>Sestavení kontejneru aplikace

Use [az acr task run][az-acr-task-run] to manually trigger the task and build the application image. This step ensures that the task tracks the application image's dependency on the base image.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Jakmile se úloha dokončí, poznamenejte si **ID spuštění** (například „da6“), pokud chcete provést následující volitelný krok.

### <a name="optional-run-application-container-locally"></a>Volitelné: Spuštění kontejneru aplikace místně

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte kontejner, abyste zobrazili aplikaci vykreslenou ve webovém prohlížeči dříve, než opětovně sestavíte její základní image. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `az acr login` ani `docker run`).

First, authenticate to your container registry with [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Teď pomocí `docker run` spusťte kontejner místně. Nahraďte **\<run-id\>** za ID spuštění z výstupu z předchozího kroku (například „da6“). This example names the container `myapp` and includes the `--rm` parameter to remove the container when you stop it.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na `http://localhost:8080`. Měli byste vidět číslo verze Node.js vykreslené na webové stránce. Mělo by vypadat takto. V dalším kroku změníte verzi tak, že do řetězce verze přidáte znak „a“.

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-01]

To stop and remove the container, run the following command:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Výpis sestavení

Next, list the task runs that ACR Tasks has completed for your registry using the [az acr task list-runs][az-acr-task-list-runs] command:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Pokud jste dokončili předchozí kurz (a nevymazali jste registr), měli byste vidět výstup podobný následujícímu. Poznamenejte si počet spuštění úloh a ID nejnovějšího spuštění, abyste je mohli porovnat s výstupem po aktualizaci základní image, kterou provedete v další části.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Aktualizace základní image

V tomto kroku budete simulovat opravu architektury v základní imagi. Upravte **Dockerfile-base** a za číslo verze definované v `NODE_VERSION` přidejte znak „a“:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Spusťte rychlou úlohu, která sestaví upravenou základní image. Poznamenejte si **ID spuštění** uvedené ve výstupu.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Jakmile se sestavení dokončí a úloha ACR odešle novou základní image do registru, aktivuje se sestavení image aplikace. Než úloha, kterou jste vytvořili dříve, aktivuje sestavení image aplikace, může to chvíli trvat, protože musí zjistit nově sestavenou a odeslanou základní image.

## <a name="list-updated-build"></a>Výpis aktualizovaného sestavení

Teď, když jste aktualizovali základní image, znovu zobrazte seznam spuštění úloh a porovnejte ho s předchozím seznamem. Pokud se napoprvé výstup neliší, opakovaně spouštějte příkaz, aby se nové spuštění úlohy v seznamu zobrazilo.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Výstup je podobný tomuto. Triggerem pro poslední spuštěné sestavení by měl být „Image Update“ značící, že se úloha zahájila rychlou úlohou základní image.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Pokud chcete provést následující volitelný krok, kterým je spuštění nově sestaveného kontejneru, abyste se mohli podívat na aktualizované číslo verze, poznamenejte si hodnotu **ID spuštění** pro sestavení aktivované pomocí Image Update (v předchozím výstupu to bylo „da8“).

### <a name="optional-run-newly-built-image"></a>Volitelné: Spuštění nově sestavené image

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte novou image aplikace, jakmile se její sestavení dokončí. `<run-id>` nahraďte hodnotou ID spuštění, kterou jste získali v předchozím kroku. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na http://localhost:8081. Měli byste vidět aktualizované číslo verze Node.js (se znakem „a“) na webové stránce:

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-02]

Důležité je uvědomit si, že jste aktualizovali **základní** image s novým číslem verze, ale novou verzi zobrazuje poslední sestavená image **aplikace**. Služba ACR Tasks převzala změnu základní image a automaticky znovu sestavila image aplikace.

To stop and remove the container, run the following command:

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste v této sérii kurzů vytvořili, včetně registru kontejneru, instance kontejneru, trezoru klíčů a instančního objektu, použijte následující příkazy:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí úlohy automaticky aktivovat sestavení imagí kontejnerů při aktualizaci základní image. Now, move on to the next tutorial to learn how to trigger tasks on a defined schedule.

> [!div class="nextstepaction"]
> [Run a task on a schedule](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
