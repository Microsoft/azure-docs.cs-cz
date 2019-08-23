---
title: Kurz – automatizace sestavení imagí kontejneru při aktualizaci základní image – Azure Container Registry úlohy
description: V tomto kurzu se naučíte konfigurovat úlohu Azure Container Registry pro automatické spouštění sestavení imagí kontejneru v cloudu, když se aktualizuje základní image.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 1a6f73841550c64e93fa09fe0a1b033f840c9752
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971384"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Kurz: Automatizace sestavení imagí kontejneru, když se v registru kontejnerů Azure aktualizuje základní image 

ACR Tasks podporuje automatizované spouštění sestavení při aktualizaci základní image, například při opravě operačního systému nebo architektury aplikace v jedné ze základních imagí. V tomto kurzu zjistíte, jak v ACR Tasks vytvořit úlohu, která aktivuje sestavení v cloudu, když se do vašeho registru odešle základní image kontejneru.

V poslední části série tohoto kurzu se naučíte:

> [!div class="checklist"]
> * Sestavit základní image
> * Vytvořit úlohu sestavení image aplikace
> * Aktualizovat základní image tak, aby aktivovala úlohu image aplikace
> * Zobrazit aktivovanou úlohu
> * Ověřit aktualizovanou image aplikace

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete Azure CLI používat místně, musíte mít nainstalovanou verzi Azure CLI **2.0.46** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku, přečtěte si téma [instalace Azure CLI][azure-cli].

## <a name="prerequisites"></a>Požadavky

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

Soubory Dockerfile definující většinu imagí kontejnerů specifikují nadřazenou image, na které jsou založené. Tato image se často označuje jako *základní image*. Základní image typicky obsahují operační systém, například [Alpine Linux][base-alpine] nebo [Windows nano Server][base-windows], na kterém se používají zbývající vrstvy kontejneru. Můžou taky zahrnovat aplikační architektury, jako je [Node. js][base-node] nebo [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Aktualizace základní image

Základní image často aktualizuje maintainer image, aby zahrnovala nové funkce nebo vylepšení operačního systému nebo architektury v imagi. Dalším běžným důvodem pro aktualizaci základní image jsou opravy zabezpečení.

Když se základní image aktualizuje, budete muset znovu sestavit jakékoli image kontejnerů v registru, které jsou na ní založené, aby zahrnovaly nové funkce a opravy. ACR Tasks zahrnuje možnost, která za vás při aktualizaci základní image kontejnerů automaticky tyto image sestaví.

### <a name="tasks-triggered-by-a-base-image-update"></a>Úlohy aktivované aktualizací základní image

* V současné době pro sestavení imagí z souboru Dockerfile úlohy ACR detekuje závislosti základních imagí ve stejném registru kontejneru Azure, veřejné úložiště Docker Hub nebo veřejné úložiště v Microsoft Container Registry. Pokud se základní bitová kopie zadaná `FROM` v příkazu nachází v jednom z těchto umístění, úloha ACR přidá vidlici, aby se zajistilo, že se image znovu vytvoří, kdykoli se aktualizuje její základ.

* Když vytvoříte úlohu ACR pomocí příkazu [AZ ACR Task Create][az-acr-task-create] , ve výchozím nastavení je úloha povolená pro aktivaci pomocí základní aktualizace image. To znamená, `base-image-trigger-enabled` že vlastnost je nastavena na hodnotu true. Pokud chcete toto chování v úloze zakázat, aktualizujte vlastnost na false. Například spusťte následující příkaz [AZ ACR Task Update][az-acr-task-update] :

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Pokud chcete, aby úkol ACR určil a sledoval závislosti image kontejneru – což zahrnuje základní image, musíte nejdřív spustit úlohu **aspoň jednou**. Úlohu můžete například aktivovat ručně pomocí příkazu [AZ ACR Task Run][az-acr-task-run] .

* Chcete-li aktivovat úlohu na základě aktualizace základního obrázku, musí mít základní image *stabilní* značku, například `node:9-alpine`. Toto označení je typické pro základní bitovou kopii, která je aktualizována pomocí operačních systémů a oprav rozhraní .NET na nejnovější stabilní verzi. Pokud se základní image aktualizuje pomocí nové značky verze, neaktivuje úlohu. Další informace o označování obrázků naleznete v doprovodnéch materiálech k [osvědčeným postupům](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/). 

### <a name="base-image-update-scenario"></a>Scénář aktualizace základní image

Tento kurz vás provede scénářem aktualizace základní image. [Ukázka kódu][code-sample] zahrnuje dva fázemi: obrázek aplikace a obrázek, který určuje jako základní. V následujících částech vytvoříte úlohu ACR, která automaticky aktivuje sestavení image aplikace, když je do stejného registru kontejneru vložena nová verze základní image.

[Dockerfile-app][dockerfile-app]: Malá webová aplikace Node. js, která vykreslí statickou webovou stránku se zobrazením verze Node. js, na které je založena. Řetězec verze je simulovaný: zobrazuje obsah proměnné prostředí `NODE_VERSION`, která je definovaná v základní imagi.

[Souboru Dockerfile-Base][dockerfile-base]: Obrázek, který `Dockerfile-app` určuje jako základní. Je sám o sobě založená na imagi [uzlu][base-node] a obsahuje `NODE_VERSION` proměnnou prostředí.

V následujících částech vytvoříte úlohu, aktualizujete hodnotu `NODE_VERSION` v souboru Dockerfile základní image a potom použijete ACR Tasks k sestavení základní image. Když úloha ACR odešle do registru novou základní image, automaticky aktivuje sestavení image aplikace. Volitelně můžete spustit image kontejneru aplikace místně, abyste se mohli podívat na různé řetězce verze v sestavených imagích.

V tomto kurzu vaše úloha ACR sestaví a nahraje image kontejneru aplikace zadanou v souboru Dockerfile. ACR úlohy mohou také spouštět [úlohy s více kroky](container-registry-tasks-multi-step.md), pomocí souboru YAML k definování kroků pro sestavení, vložení a volitelně testování více kontejnerů.

## <a name="build-the-base-image"></a>Sestavit základní image

Začněte sestavením základní image pomocí *rychlé úlohy* v ACR Tasks. Jak je popsáno v [prvním kurzu](container-registry-tutorial-quick-task.md) série, tímto postupem se nejen sestaví image, ale v případě úspěšného sestavení se odešle do registru kontejneru.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Vytvoření úkolu

V dalším kroku vytvořte úkol pomocí [AZ ACR Task Create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Pokud jste dříve vytvořili úkoly ve verzi Preview pomocí `az acr build-task` příkazu, je nutné tyto úlohy znovu vytvořit pomocí příkazu [AZ ACR Task][az-acr-task] .

Tato úloha je podobná rychlé úloze vytvořené v [předchozím kurzu](container-registry-tutorial-build-task.md). Dává službě ACR Tasks pokyn aktivovat sestavení image, když se do úložiště určeného parametrem `--context` odešlou potvrzení. Zatímco souboru Dockerfile použitý k sestavení image v předchozím kurzu určuje veřejnou základní Image (`FROM node:9-alpine`), souboru Dockerfile v této úloze, [souboru Dockerfile-App][dockerfile-app], určí základní image ve stejném registru:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Tato konfigurace usnadňuje simulaci opravy architektury v základní imagi později v tomto kurzu.

## <a name="build-the-application-container"></a>Sestavení kontejneru aplikace

K ruční aktivaci úlohy a sestavení image aplikace použijte [příkaz AZ ACR Task Run][az-acr-task-run] . Tento krok zajistí, že úloha sleduje závislost obrázku aplikace na základní imagi.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Jakmile se úloha dokončí, poznamenejte si **ID spuštění** (například „da6“), pokud chcete provést následující volitelný krok.

### <a name="optional-run-application-container-locally"></a>Volitelné: Místní spuštění kontejneru aplikace

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte kontejner, abyste zobrazili aplikaci vykreslenou ve webovém prohlížeči dříve, než opětovně sestavíte její základní image. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `az acr login` ani `docker run`).

Nejprve proveďte ověření ve vašem registru kontejneru pomocí [AZ ACR Login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Teď pomocí `docker run` spusťte kontejner místně. Nahraďte **\<run-id\>** za ID spuštění z výstupu z předchozího kroku (například „da6“). Tento příklad pojmenuje `myapp` kontejner a `--rm` obsahuje parametr pro odebrání kontejneru při jeho zastavení.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na `http://localhost:8080`. Měli byste vidět číslo verze Node.js vykreslené na webové stránce. Mělo by vypadat takto. V dalším kroku změníte verzi tak, že do řetězce verze přidáte znak „a“.

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-01]

Chcete-li zastavit a odebrat kontejner, spusťte následující příkaz:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Výpis sestavení

V dalším kroku vypíšete úlohu, kterou ACR úlohy dokončily pro váš registr pomocí příkazu [AZ ACR Task list-běhy][az-acr-task-list-runs] :

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

### <a name="optional-run-newly-built-image"></a>Volitelné: Spustit nově sestavenou image

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte novou image aplikace, jakmile se její sestavení dokončí. `<run-id>` nahraďte hodnotou ID spuštění, kterou jste získali v předchozím kroku. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

V prohlížeči přejděte na http://localhost:8081. Měli byste vidět aktualizované číslo verze Node.js (se znakem „a“) na webové stránce:

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-02]

Důležité je uvědomit si, že jste aktualizovali **základní** image s novým číslem verze, ale novou verzi zobrazuje poslední sestavená image **aplikace**. Služba ACR Tasks převzala změnu základní image a automaticky znovu sestavila image aplikace.

Chcete-li zastavit a odebrat kontejner, spusťte následující příkaz:

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste v této sérii kurzů vytvořili, včetně registru kontejneru, instance kontejneru, trezoru klíčů a instančního objektu, použijte následující příkazy:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak pomocí úlohy automaticky aktivovat sestavení imagí kontejnerů při aktualizaci základní image. Teď se přesuneme k informacím o ověřování registru kontejneru.

> [!div class="nextstepaction"]
> [Ověřování ve službě Azure Container Registry](container-registry-authentication.md)

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
