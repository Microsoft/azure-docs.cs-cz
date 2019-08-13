---
title: Rychlý Start – vytvoření privátního registru Docker v Azure – Azure CLI
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí Azure CLI.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 6c511c56ab8df14cc6ea81363772ae0fd6d61272
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68309530"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Rychlý start: Vytvoření soukromého registru kontejnerů pomocí Azure CLI

Azure Container Registry je spravovaná služba registru kontejnerů Dockeru sloužící k ukládání privátních imagí kontejnerů Dockeru. Tato příručka podrobně popisuje vytvoření instance služby Azure Container Registry pomocí Azure CLI. Pak pomocí příkazů Docker nahrajte image kontejneru do registru a nakonec si vydejte a spusťte image z registru.

Tento rychlý Start vyžaduje, abyste spustili Azure CLI (doporučuje se verze 2.0.55 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker poskytuje balíčky, které snadno konfigurují Docker na jakémkoli systému [MacOS][docker-mac], [Windows][docker-windows]nebo [Linux][docker-linux] .

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto rychlém startu vytvoříte *základní* registr, což je výhodná možnost pro vývojáře, kteří se naučí o Azure Container Registry. Podrobnosti k dostupným úrovním služeb najdete v tématu [SKU služby Container Registry][container-registry-skus].

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

Poznamenejte si `loginServer` výstup, který je plně kvalifikovaný název registru (malými písmeny). V celé zbývající části tohoto rychlého startu se položka `<acrName>` používá jako zástupný symbol pro název registru kontejneru.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před vložením a stažením imagí kontejneru se musíte přihlásit k registru. K tomu použijte příkaz [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Následující příklad vypíše úložiště v registru:

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```
Result
----------------
hello-world
```

Následující příklad vypíše značky v úložišti **Hello-World** .

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

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, registru kontejneru a uložených imagí kontejneru použít příkaz [AZ Group Delete][az-group-delete] .

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Azure Container Registry pomocí Azure CLI, nahráli jste image kontejneru do registru a z registru jste vyžádali a spustili image. Pokračujte Azure Container Registry výukové kurzy, kde najdete hlubší přehled na ACR.

> [!div class="nextstepaction"]
> [Kurzy Azure Container Registry][container-registry-tutorial-quick-task]

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
