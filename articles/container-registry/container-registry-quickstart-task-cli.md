---
title: Rychlý Start – sestavení image kontejneru na vyžádání v Azure
description: Použijte Azure Container Registry příkazy k rychlému sestavení, vložení a spuštění image kontejneru Docker na vyžádání v cloudu Azure.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperfq1, devx-track-azurecli
ms.openlocfilehash: adb63b716172c979ca31f1ef70bf1523ff1ad5cc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845046"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Rychlý Start: sestavení a spuštění image kontejneru pomocí Azure Container Registrych úloh

V tomto rychlém startu použijete příkazy [Azure Container Registry úkoly][container-registry-tasks-overview] k rychlému sestavování, nasdílení a spuštění image kontejneru Docker v Azure, a to bez místní instalace Docker. ACR úkoly jsou sadou funkcí v rámci Azure Container Registry, které vám pomůžou se správou a úpravou imagí kontejnerů napříč životním cyklem kontejneru. Tento příklad ukazuje, jak přesměrovat cyklus vývoje image kontejneru "vnitřní smyčka" do cloudu pomocí buildů na vyžádání s využitím místních souboru Dockerfile. 

Po tomto rychlém startu se seznámíte s pokročilejšími funkcemi úloh ACR s využitím [kurzů](container-registry-tutorial-quick-task.md). ACR úlohy můžou automatizovat sestavení imagí na základě potvrzení kódu nebo základních aktualizací imagí nebo můžete paralelně testovat více kontejnerů, a to i v jiných scénářích. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.58 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud ještě nemáte registr kontejnerů, vytvořte nejprve skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

Pomocí příkazu [AZ ACR Create][az-acr-create] vytvořte registr kontejnerů. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu se používá *myContainerRegistry008* . Aktualizujte název na jedinečnou hodnotu.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

Tento příklad vytvoří *základní* registr, což je možnost pro vývojáře, která se naučí o Azure Container Registry. Podrobnosti o dostupných úrovních služby najdete v tématu [úrovně služby registru kontejnerů][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Sestavení a vložení obrázku z souboru Dockerfile

Nyní použijte Azure Container Registry k sestavení a vložení obrázku. Nejprve vytvořte místní pracovní adresář a pak vytvořte souboru Dockerfile s názvem *souboru Dockerfile* s jedním řádkem: `FROM mcr.microsoft.com/hello-world` . Toto je jednoduchý příklad pro sestavení image kontejneru Linux z `hello-world` Image hostované v Microsoft Container Registry. Můžete vytvářet vlastní standardní souboru Dockerfile a image sestavení pro jiné platformy. Pokud pracujete v prostředí bash, vytvořte souboru Dockerfile pomocí následujícího příkazu:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Spusťte příkaz [AZ ACR Build, který sestaví][az-acr-build] image a po úspěšném sestavení image ji nahrajte do svého registru. Následující příklad sestaví a vloží `sample/hello-world:v1` obrázek. Na `.` konci příkazu nastaví umístění souboru Dockerfile, v tomto případě aktuální adresář.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
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
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
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

## <a name="run-the-image"></a>Spustit bitovou kopii

Nyní můžete rychle spustit obrázek, který jste vytvořili a vložili do registru. Tady pomocí příkazu [AZ ACR Run][az-acr-run] spustíte příkaz Container. V pracovním postupu vývoje kontejneru může to být krok ověření před nasazením image, nebo můžete zahrnout příkaz do [YAML souboru s více kroky][container-registry-tasks-multi-step]. 

Následující příklad používá `$Registry` k určení registru, ve kterém je spuštěný příkaz:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

`cmd`Parametr v tomto příkladu spustí kontejner ve své výchozí konfiguraci, ale `cmd` podporuje další `docker run` parametry nebo dokonce i jiné `docker` příkazy.

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

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, registru kontejneru a uložených imagí kontejneru použít příkaz [AZ Group Delete][az-group-delete] .

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí funkcí ACR úkoly rychle vytvořili, nastavili a spouštěli image kontejneru Docker nativně v rámci Azure bez místní instalace Docker. Další informace o používání úloh ACR k automatizaci sestavení a aktualizací imagí najdete v kurzech k Azure Container Registry úlohám.

> [!div class="nextstepaction"]
> [Kurzy k Azure Container Registry úlohám][container-registry-tutorial-quick-task]

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
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
