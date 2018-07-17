---
title: Kurz – Automatizace sestavení image kontejneru při aktualizaci základní image se službou Azure Container Registry Build
description: V tomto kurzu se naučíte konfigurovat úlohu sestavení, aby při aktualizaci základní image automaticky spouštěla sestavení image kontejneru v cloudu.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: a302cdcf94baa869e55262c4cd380fc05bf64299
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461601"
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Kurz: Automatizace sestavení image při aktualizaci základní image se službou Azure Container Registry Build

ACR Build podporuje automatizované spouštění sestavení při aktualizaci základní image, například při opravě operačního systému nebo architektury aplikace v jedné z vašich základních imagí. V tomto kurzu se naučíte vytvořit úlohu sestavení ve službě ACR Build, která spustí sestavení v cloudu, pokud se základní image kontejneru vloží do vašeho registru.

V poslední části série tohoto kurzu se naučíte:

> [!div class="checklist"]
> * Sestavit základní image
> * Vytvořit úlohu sestavení image aplikace
> * Aktualizovat základní image tak, aby spouštěla sestavení image aplikace
> * Zobrazit spuštěné sestavení
> * Ověřit aktualizovanou image aplikace

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete Azure CLI používat místně, musíte mít nainstalovanou verzi Azure CLI **2.0.32** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="prerequisites"></a>Požadavky

### <a name="complete-previous-tutorials"></a>Dokončení předchozích kurzů

Tento kurz předpokládá, že jste už dokončili kroky v prvních dvou kurzech série:

* Vytvoření registru kontejneru Azure
* Vytvoření forku ukázkového úložiště
* Klonování ukázkového úložiště
* Vytvoření tokenu PAT GitHubu

Než budete pokračovat, dokončete (pokud jste to už neudělali) první dva kurzy:

[Sestavení imagí kontejnerů v cloudu se službou Azure Container Registry Build](container-registry-tutorial-quick-build.md)

[Automatizace sestavení imagí kontejnerů se službou Azure Container Registry Build](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>Konfigurace prostředí

Tyto proměnné prostředí naplňte hodnotami vhodnými pro vaše prostředí. Není to nezbytně nutné, ale usnadní se tím provádění víceřádkových příkazů Azure CLI v tomto kurzu. Pokud tyto proměnné nevyplníte, musíte jednotlivé hodnoty ručně nahradit všude tam, kde se v ukázkových příkazech vyskytují.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Základní image

Soubory Dockerfile definující většinu imagí kontejnerů specifikují nadřazenou image, na které jsou založené. Tato image se často označuje jako *základní image*. Základní image obvykle obsahují operační systém, například [Alpine Linux][base-alpine] nebo [Windows Nano Server][base-windows], na kterém jsou použity zbývající vrstvy kontejneru. Můžou také obsahovat architektury aplikace, jako je třeba [Node.js][base-node] nebo [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Aktualizace základní image

Základní image často aktualizuje maintainer image, aby zahrnovala nové funkce nebo vylepšení operačního systému nebo architektury v imagi. Dalším běžným důvodem pro aktualizaci základní image jsou opravy zabezpečení.

Když se základní image aktualizuje, budete muset znovu sestavit jakékoli image kontejnerů v registru, které jsou na ní založené, aby zahrnovaly nové funkce a opravy. ACR Build má možnost, která za vás při aktualizaci základní image kontejnerů automaticky tyto image sestaví.

### <a name="base-image-update-scenario"></a>Scénář aktualizace základní image

Tento kurz vás provede scénářem aktualizace základní image. [Vzorový kód][code-sample] zahrnuje dva soubory Dockerfile: image aplikace a image, na které je založená. V následujících částech vytvoříte úlohu služby ACR Build, která automaticky spustí sestavení image aplikace v případě, že se do registru kontejneru vloží nová verze základní image.

[Dockerfile-app][dockerfile-app]: Jedná se o malou webovou aplikaci Node.js, která vykreslí statickou webovou stránku zobrazující verzi Node.js, na které je založena. Řetězec verze je simulovaný, zobrazuje obsah proměnné prostředí `NODE_VERSION`, která je definovaná v základní imagi.

[Dockerfile-base][dockerfile-base]: Image, kterou `Dockerfile-app` specifikuje jako svou základní image. Je založená na imagi [Node][base-node] a zahrnuje proměnnou prostředí `NODE_VERSION`.

V následujících částech vytvoříte úlohu sestavení, aktualizujete hodnotu `NODE_VERSION` v souboru Dockerfile základní image a potom použijete službu ACR Build k sestavení základní image. Když ACR Build vloží novou základní image do registru, automaticky spustí sestavení image aplikace. Volitelně můžete spustit image kontejneru aplikace místně, abyste se mohli podívat na různé řetězce verze v sestavených imagích.

## <a name="build-base-image"></a>Sestavení základní image

Začněte sestavením základní image pomocí funkce *Quick Build* služby ACR Build. Jak je popsáno v [prvním kurzu](container-registry-tutorial-quick-build.md) série, vytvoří se tak nejen image, ale v případě úspěšného sestavení se vloží do registru kontejneru.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Vytvoření úlohy sestavení

Dále pomocí [az acr build-task create][az-acr-build-task-create] vytvořte úlohu sestavení:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

Tato úloha sestavení je podobná úloze vytvořené v [předchozím kurzu](container-registry-tutorial-build-task.md). Dává službě ACR Build pokyn spustit sestavení image, když se do úložiště specifikovaného `--context` vkládají potvrzení.

Její chování je jiné, protože spustí sestavení image i při aktualizaci *základní image*. Soubor Dockerfile určený argumentem `--file`, [Dockerfile-app][dockerfile-app], podporuje specifikaci image ze stejného registru, ve kterém se nachází jeho základní image:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Když spustíte úlohu sestavení, služba ACR Build zjistí závislosti image. Pokud se základní image specifikovaná v příkazu `FROM` nachází ve stejném registru, přidá se hook, který zajistí, že se tato image znovu sestaví vždy, když dojde k aktualizaci její základní image.

> [!NOTE]
> Ve verzi Preview služba ACR Build podporuje spuštění odvozeného sestavení image, když se základní image i image, která na ni odkazuje, nacházejí ve stejném registru kontejneru Azure.

## <a name="build-application-container"></a>Sestavení kontejneru aplikace

Pokud chcete službě ACR Build povolit určování a sledování závislostí image kontejneru, mezi které patří její základní image, **musíte** nejprve **alespoň jednou** spustit její úlohu sestavení.

Pomocí příkazu [az acr build-task run][az-acr-build-task-run] spusťte ručně úlohu sestavení a sestavte image aplikace:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Jakmile se sestavení dokončí, poznamenejte si **ID sestavení** (například „aa6“) pro provedení následujícího volitelného kroku.

### <a name="optional-run-application-container-locally"></a>Volitelné: Spuštění kontejneru aplikace místně

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte kontejner, abyste zobrazili aplikaci vykreslenou ve webovém prohlížeči dříve, než opětovně sestavíte její základní image. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `az acr login` ani `docker run`).

Nejdřív se pomocí příkazu [az acr login][az-acr-login] přihlaste do registru kontejneru:

```azurecli
az acr login --name $ACR_NAME
```

Teď pomocí `docker run` spusťte kontejner místně. Nahraďte **\<build-id\>** pomocí ID sestavení, které jste si poznamenali ve výstupu předchozího kroku (například „aa6“).

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

V prohlížeči přejděte na http://localhost:8080. Měli byste vidět číslo verze Node.js vykreslené na webové stránce. Mělo by vypadat takto. V dalším kroku změníte verzi tak, že do řetězce verze přidáte znak „a“.

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-01]

## <a name="list-builds"></a>Seznam sestavení

Potom zobrazte seznam sestavení, která služba ACR Build pro registr dokončila:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Pokud jste dokončili předchozí kurz (a nevymazali jste registr), měli byste vidět výstup podobný následujícímu. Poznamenejte si počet sestavení a nejnovější ID sestavení, abyste je mohli porovnat s výstupem po aktualizaci základní image, kterou provedete v další části.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa6         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual      2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="update-base-image"></a>Aktualizace základní image

V tomto kroku budete simulovat opravu architektury v základní imagi. Upravte **Dockerfile-base** a za číslo verze definované v `NODE_VERSION` přidejte znak „a“:

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Ve službě ACR Build spusťte funkci Quick Build a sestavte upravenou základní image. Poznamenejte si **ID sestavení** ve výstupu.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Jakmile se sestavení dokončí a ACR Build vloží novou základní image do registru, spustí sestavení image aplikace. Než úloha služby ACR Build, kterou jste vytvořili dříve, spustí sestavení image aplikace, může to chvíli trvat, protože musí zjistit nově dokončenou a vloženou základní image.

## <a name="list-builds"></a>Seznam sestavení

Teď když jste základní image aktualizovali, zobrazte si seznam sestavení znovu a porovnejte ho s předchozím seznamem. Pokud se napoprvé výstup neliší, opakovaně spouštějte příkaz, aby se nové sestavení v seznamu zobrazilo.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Výstup je podobný tomuto. Aktivační událostí pro poslední spuštěné sestavení by měla být „Image Update“ určující, že úloha sestavení byla zahájena funkcí Quick Build základní image.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
aa8         buildhelloworld  Linux       Succeeded  Image Update  2018-05-10T20:09:52Z  00:00:45
aa7                          Linux       Succeeded  Manual        2018-05-10T20:09:17Z  00:00:40
aa6         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual        2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit    2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual        2018-05-10T19:10:14Z  00:00:55
```

Pokud chcete provést následující volitelný krok, kterým je spuštění nově sestaveného kontejneru, abyste se mohli podívat na aktualizované číslo verze, poznamenejte si hodnotu **ID sestavení** pro sestavení spuštěné pomocí Image Update (v předchozím výstupu to bylo „aa8“).

### <a name="optional-run-newly-built-image"></a>Volitelné: Spuštění nově sestavené image

Pokud pracujete místně (nejste v Cloud Shellu) a máte nainstalovaný Docker, spusťte novou image aplikace, jakmile se její sestavení dokončí. `<build-id>` nahraďte hodnotou ID sestavení, kterou jste získali v předchozím kroku. Pokud Cloud Shell používáte, tuto část přeskočte (Cloud Shell nepodporuje `docker run`).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

V prohlížeči přejděte na http://localhost:8081. Měli byste vidět aktualizované číslo verze Node.js (se znakem „a“) na webové stránce:

![Snímek obrazovky ukázkové aplikace vykreslené v prohlížeči][base-update-02]

Důležité je uvědomit si, že jste aktualizovali **základní** image s novým číslem verze, ale novou verzi zobrazuje poslední sestavená image **aplikace**. Služba ACR Build převzala změnu základní image a automaticky znovu sestavila image aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste v této sérii kurzů vytvořili, včetně registru kontejneru, instance kontejneru, trezoru klíčů a instančního objektu, použijte následující příkazy:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili použít úlohu sestavení k automatickému spuštění sestavení imagí kontejnerů při aktualizaci základní image. Teď se přesuneme k informacím o ověřování registru kontejneru.

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
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png