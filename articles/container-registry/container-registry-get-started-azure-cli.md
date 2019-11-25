---
title: Quickstart - Create registry - Azure CLI
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí Azure CLI.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 551a3659feb39943c9f794484abb6f2da4367f39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455166"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Quickstart: Create a private container registry using the Azure CLI

Azure Container Registry je spravovaná služba registru kontejnerů Dockeru sloužící k ukládání privátních imagí kontejnerů Dockeru. Tato příručka podrobně popisuje vytvoření instance služby Azure Container Registry pomocí Azure CLI. Then, use Docker commands to push a container image into the registry, and finally pull and run the image from your registry.

This quickstart requires that you are running the Azure CLI (version 2.0.55 or later recommended). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker provides packages that easily configure Docker on any [macOS][docker-mac], [Windows][docker-windows], or [Linux][docker-linux] system.

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

In this quickstart you create a *Basic* registry, which is a cost-optimized option for developers learning about Azure Container Registry. For details on available service tiers, see [Container registry SKUs][container-registry-skus].

Pomocí příkazu [az acr create][az-acr-create] vytvořte instanci služby ACR. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu se používá *myContainerRegistry007*. Aktualizujte název na jedinečnou hodnotu.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Po vytvoření registru je výstup podobný tomuto:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Take note of `loginServer` in the output, which is the fully qualified registry name (all lowercase). V celé zbývající části tohoto rychlého startu se položka `<acrName>` používá jako zástupný symbol pro název registru kontejneru.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Before pushing and pulling container images, you must log in to the registry. K tomu použijte příkaz [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

The following example lists the repositories in your registry:

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```
Result
----------------
hello-world
```

The following example lists the tags on the **hello-world** repository.

```azurecli
az acr repository show-tags --name <acrName> --repository hello-world --output table
```

Výstup:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

When no longer needed, you can use the [az group delete][az-group-delete] command to remove the resource group, the container registry, and the container images stored there.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

In this quickstart, you created an Azure Container Registry with the Azure CLI, pushed a container image to the registry, and pulled and ran the image from the registry. Continue to the Azure Container Registry tutorials for a deeper look at ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry tutorials][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
