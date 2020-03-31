---
title: Úvodní příručka – vytvoření registru – azure cli
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí Azure CLI.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 551a3659feb39943c9f794484abb6f2da4367f39
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455166"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Úvodní příručka: Vytvoření registru privátního kontejneru pomocí azure cli

Azure Container Registry je spravovaná služba registru kontejnerů Dockeru sloužící k ukládání privátních imagí kontejnerů Dockeru. Tato příručka podrobně popisuje vytvoření instance služby Azure Container Registry pomocí Azure CLI. Potom pomocí příkazů Dockeru vysuňte bitovou kopii kontejneru do registru a nakonec ji vyprovokejte a spusťte z registru.

Tento rychlý start vyžaduje, abyste spustili azure CLI (verze 2.0.55 nebo novější doporučeno). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto rychlém startu vytvoříte *základní* registr, což je nákladově optimalizovaná možnost pro vývojáře, kteří se učí o registru kontejnerů Azure. Podrobnosti o dostupných úrovních služeb naleznete [v tématu Kontejner registru SKU][container-registry-skus].

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

Poznamenejte si `loginServer` ve výstupu, což je plně kvalifikovaný název registru (všechna malá písmena). V celé zbývající části tohoto rychlého startu se položka `<acrName>` používá jako zástupný symbol pro název registru kontejneru.

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesláním a vytažením bitové kopie kontejneru, musíte se přihlásit do registru. K tomu použijte příkaz [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Výpis imagí kontejnerů

V následujícím příkladu jsou uvedena úložiště v registru:

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```
Result
----------------
hello-world
```

V následujícím příkladu jsou uvedeny značky v úložišti **hello-world.**

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

Pokud již není potřeba, můžete použít příkaz [odstranění skupiny az][az-group-delete] k odebrání skupiny prostředků, registru kontejneru a bitových kopií kontejneru, které jsou v něm uloženy.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili registr kontejnerů Azure s Azure CLI, zasunul i image kontejneru do registru a stáhli a spustili image z registru. Pokračujte do kurzů Azure Container Registry pro hlubší pohled na ACR.

> [!div class="nextstepaction"]
> [Kurzy registru kontejnerů Azure][container-registry-tutorial-quick-task]

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
