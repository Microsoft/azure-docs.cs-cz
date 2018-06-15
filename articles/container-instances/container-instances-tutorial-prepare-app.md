---
title: Kurz služby Azure Container Instances – Příprava aplikace
description: Kurz služby Azure Container Instances, část 1 ze 3 – Příprava aplikace na nasazení do služby Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 79041123196559c5759789638228ea0dd21f2762
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163278"
---
# <a name="tutorial-create-container-for-deployment-to-azure-container-instances"></a>Kurz: Vytvoření kontejneru pro nasazení do služby Azure Container Instances

Azure Container Instances umožňuje nasazení kontejnerů Dockeru na infrastrukturu Azure bez zřizování virtuálních počítačů nebo využívání služby vyšší úrovně. V tomto kurzu zabalíte malou webovou aplikaci Node.js do image kontejneru, kterou bude možné spustit pomocí služby Azure Container Instances.

V tomto článku, který je první částí série, se naučíte:

> [!div class="checklist"]
> * Klonování zdrojového kódu aplikace z GitHubu
> * Vytvoření image kontejneru ze zdroje aplikace
> * Testování image v místním prostředí Dockeru

V druhé a třetím kurzu nahrajete svou image do služby Azure Container Registry a potom ji nasadíte do služby Azure Container Instances.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Získání kódu aplikace

Ukázková aplikace v tomto kurzu je jednoduchá webová aplikace vytvořená v [Node.js][nodejs]. Tato aplikace slouží jako statická stránka HTML a vypadá podobně jako následující snímek obrazovky:

![Ukázková aplikace zobrazená v prohlížeči][aci-tutorial-app]

Pomocí Gitu naklonujte úložiště ukázkové aplikace:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

[Archiv ZIP][aci-helloworld-zip] můžete také stáhnout přímo z GitHubu.

## <a name="build-the-container-image"></a>Sestavení image kontejneru

Soubor Dockerfile v ukázkové aplikaci ukazuje postup sestavení kontejneru. Začíná od [oficiální image Node.js][docker-hub-nodeimage] založené na systému [Alpine Linux][alpine-linux], malé distribuci vhodné pro použití s kontejnery. Potom zkopíruje soubory aplikace do kontejneru, nainstaluje závislosti pomocí Node Package Manageru a nakonec aplikaci spustí.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Pomocí příkazu [docker build][docker-build] vytvořte image kontejneru a označte ji jako *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Výstup příkazu [docker build][docker-build] je podobný následujícímu výstupu (zkrácenému pro lepší čitelnost):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Pomocí příkazu [docker images][docker-images] zobrazte sestavenou image:

```bash
docker images
```

Nově vytvořená image by se měla objevit na seznamu:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Než kontejner nasadíte do služby Azure Container Instances, spusťte ho místně pomocí příkazu [docker run][docker-run] a ověřte, že funguje. Přepínač `-d` umožňuje spuštění kontejneru na pozadí, zatímco přepínač `-p` umožňuje mapování libovolného portu vašeho počítače na port 80 kontejneru.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Pokud byl příkaz `docker run` úspěšný, výstup z něj zobrazí ID spuštěného kontejneru:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Teď ve svém prohlížeči přejděte na adresu http://localhost:8080 a ověřte, že je kontejner spuštěný. Zobrazená webová stránka by měla vypadat přibližně takto:

![Místní spuštění aplikace v prohlížeči][aci-tutorial-app-local]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili image kontejneru, kterou je možné nasadit do služby Azure Container Instances a ověřit, že běží místně. Zatím jste provedli následující:

> [!div class="checklist"]
> * Klonování zdroje aplikace z GitHubu
> * Vytvoření image kontejneru ze zdroje aplikace
> * Místní test kontejneru

Přejděte k dalšímu kurzu v sérii, ve kterém se seznámíte s ukládáním image kontejneru do služby Azure Container Registry:

> [!div class="nextstepaction"]
> [Nahrání image do služby Azure Container Registry](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
