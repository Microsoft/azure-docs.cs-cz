---
title: "Kurz služby Azure Container Instances – Příprava aplikace"
description: "Kurz služby Azure Container Instances, část 1 ze 3 – Příprava aplikace na nasazení do služby Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5012412ec642a04102836274caea253635376efb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Vytvoření kontejneru pro nasazení do služby Azure Container Instances

Azure Container Instances umožňuje nasazení kontejnerů Dockeru na infrastrukturu Azure bez zřizování virtuálních počítačů nebo využívání služby vyšší úrovně. V tomto kurzu vytvoříte malou webovou aplikaci v Node.js a zabalíte ji do kontejneru, který bude možné spustit pomocí služby Azure Container Instances.

V tomto článku, který je první částí série, se naučíte:

> [!div class="checklist"]
> * Klonování zdrojového kódu aplikace z GitHubu
> * Vytvoření image kontejneru ze zdroje aplikace
> * Testování image v místním prostředí Dockeru

V následujících kurzech nahrajete svou image do služby Azure Container Registry a potom ji nasadíte do služby Azure Container Instances.

## <a name="before-you-begin"></a>Než začnete

Tento kurz vyžaduje použití Azure CLI verze 2.0.23 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0][azure-cli-install].

V tomto kurzu se předpokládá základní znalost klíčových konceptů Dockeru, jako jsou kontejnery, image kontejnerů a základní příkazy `docker`. V případě potřeby najdete základní informace o kontejnerech v článku [Get started with Docker][docker-get-started] (Začínáme s Dockerem).

K dokončení tohoto kurzu potřebujete vývojové prostředí pro Docker nainstalované místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-mac] nebo systému [Windows][docker-windows] či [Linux][docker-linux].

Azure Cloud Shell neobsahuje součásti Dockeru nutné pro dokončení všech kroků v tomto kurzu. Je nutné nainstalovat do místního počítače rozhraní příkazového řádku Azure a vývojové prostředí Dockeru, aby bylo možné tento kurz dokončit.

## <a name="get-application-code"></a>Získání kódu aplikace

Ukázka v tomto kurzu zahrnuje jednoduchou webovou aplikaci vytvořenou v [Node.js][nodejs]. Tato aplikace slouží jako statická stránka HTML a vypadá takto:

![Ukázková aplikace zobrazená v prohlížeči][aci-tutorial-app]

Pomocí gitu si stáhněte ukázku:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Sestavení image kontejneru

Soubor Dockerfile, který je součástí ukázkového úložiště, ukazuje postup sestavení kontejneru. Začíná od [oficiální image Node.js][docker-hub-nodeimage] založené na systému [Alpine Linux][alpine-linux], malé distribuci vhodné pro použití s kontejnery. Potom zkopíruje soubory aplikace do kontejneru, nainstaluje závislosti pomocí Node Package Manageru a nakonec aplikaci spustí.

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

```bash
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

Výstup:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Než se pokusíte kontejner nasadit do služby Azure Container Instances, spusťte ho místně, abyste ověřili, že funguje. Přepínač `-d` umožňuje spuštění kontejneru na pozadí, zatímco přepínač `-p` umožňuje mapování libovolného portu vašeho počítače na port 80 kontejneru.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Otevřete v prohlížeči adresu http://localhost:8080 a ověřte, že je kontejner spuštěný.

![Místní spuštění aplikace v prohlížeči][aci-tutorial-app-local]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili image kontejneru, kterou je možné nasadit do služby Azure Container Instances. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Klonování zdroje aplikace z GitHubu
> * Vytvoření imagí kontejnerů ze zdroje aplikace
> * Místní test kontejneru

Přejděte k dalšímu kurzu, ve kterém se seznámíte s ukládáním imagí kontejnerů ve službě Azure Container Registry.

> [!div class="nextstepaction"]
> [Nahrávání imagí do služby Azure Container Registry](./container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
