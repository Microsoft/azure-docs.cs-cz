---
title: Rychlý start – sestavení a spuštění image kontejneru ve službě Azure Container Registry
description: Úlohy lze rychle spustit pomocí Azure Container Registry k sestavení a spuštění kontejneru obrázků na vyžádání v cloudu.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 61a17842158326c927f049af893a00818f3acc55
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919690"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Rychlý start: Sestavte a spusťte image kontejneru pomocí Azure Container Registry úlohy

V tomto rychlém startu použijete Azure Container Registry úlohy příkazy rychle vytvářet, vkládat a spuštění Docker image kontejneru z nativně v rámci Azure, ukazuje, jak přesměrovat cyklu vývoje "vnitřní smyčky" do cloudu. [Úlohy služby ACR] [ container-registry-tasks-overview] je sada funkcí v rámci Azure Container Registry k vám pomůžou spravovat a upravovat imagí kontejnerů v cyklu kontejneru. 

Po tomto rychlém startu prozkoumání pokročilejších funkcí úloh služby ACR. Úlohy služby ACR může automatizace sestavování imagí na základě potvrzení změn kódu nebo aktualizace základní image, nebo testovat více kontejnerů, paralelně, mimo jiné scénáře. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-account] před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení tohoto rychlého startu můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud chcete použít ho místně, verze 2.0.58 nebo později se doporučuje. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud ještě nemáte registru kontejneru, nejdřív vytvořte skupinu prostředků s [vytvořit skupiny az] [ az-group-create] příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Vytvoření registru kontejnerů pomocí [az acr vytvořit] [ az-acr-create] příkazu. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu *myContainerRegistry008* se používá. Aktualizujte název na jedinečnou hodnotu.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

Tento příklad vytvoří *základní* registru, možnost pro vývojáře, další informace o službě Azure Container Registry a optimalizovat náklady. Podrobnosti o úrovních služeb najdete v tématu [skladové položky služby Container registry][container-registry-skus].

## <a name="build-an-image-from-a-dockerfile"></a>Sestavení image ze souboru Dockerfile

Teď pomocí Azure Container Registry k sestavení image. Nejprve vytvořte pracovní adresář a potom vytvořte soubor Dockerfile s názvem *soubor Dockerfile* s následujícím obsahem. Toto je jednoduchý příklad k vytvoření image kontejneru Linuxu, ale můžete vytvořit vlastní standardní soubor Dockerfile a tvorbě bitových kopií pro jiné platformy.

```bash
echo "FROM hello-world" > Dockerfile
```

Spustit [az acr sestavení] [ az-acr-build] příkazu k sestavení image. Po úspěšném sestavení, je na obrázku nahrány do vašeho registru. Následující příklad nabízených oznámení `sample/hello-world:v1` bitové kopie. `.` Na konci příkaz nastaví umístění souboru Dockerfile, v tomto případě aktuální adresář.

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
```

Výstup z úspěšného sestavení a nabízených oznámení je podobný následujícímu:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Spusťte image

Nyní lze rychle spustit obrázek, který vytvořeny a nahrány do vašeho registru. V pracovním postupu vývoje kontejneru může se jednat o ověřovací krok před nasazením image.

Vytvořte soubor *quickrun.yaml* v místní pracovní složky s následujícím obsahem pro jednoho kroku. Nahraďte názvem přihlašovacího serveru vašeho registru pro  *\<acrLoginServer\>*. Název přihlašovacího serveru je ve formátu  *\<název registru\>. azurecr.io* (malými písmeny), například *mycontainerregistry008.azurecr.io*. Tento příklad předpokládá vytvořeny a nahrány `sample/hello-world:v1` bitové kopie v předchozí části:

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

`cmd` Krok v tomto příkladu se spustí kontejner v její výchozí konfiguraci, ale `cmd` podporuje další `docker run` parametry nebo dokonce i v dalších `docker` příkazy.

Spusťte kontejner pomocí následujícího příkazu:

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

Výstup je podobný tomuto:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odstranění skupiny az] [ az-group-delete] příkazu odeberte skupinu prostředků, do registru kontejneru a imagí kontejnerů v ní uloženy.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili funkce ACR úloh rychle vytvářet, vkládat a spuštění Docker image kontejneru z nativně v rámci Azure. I nadále kurzů Azure Container Registry a další informace o použití služby ACR úloh k automatizaci sestavování imagí a aktualizace.

> [!div class="nextstepaction"]
> [Kurzy služby Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
