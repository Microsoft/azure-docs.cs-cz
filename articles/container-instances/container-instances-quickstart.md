---
title: "Rychlý start: Vytvoření prvního kontejneru služby Azure Container Instances"
description: "Nasazení služby Azure Container Instances a zahájení práce"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: f8fe53f834e4fcf7f16174222cb51d89e40305ec
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Vytvoření prvního kontejneru ve službě Azure Container Instances
Služba Azure Container Instances usnadňuje vytváření a správu kontejnerů Dockeru v Azure, aniž byste museli zřizovat virtuální počítače nebo používat službu vyšší úrovně. V tomto rychlém startu vytvoříte kontejner v Azure a zveřejníte ho na internetu s použitím plně kvalifikovaného názvu domény. K dokončení této operace stačí jediný příkaz. Během několika sekund uvidíte ve svém prohlížeči toto:

![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][aci-app-browser]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-account] před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení tohoto rychlého startu můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.27 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Služba Azure Container Instances je prostředek Azure a musí být umístěná ve skupině prostředků Azure, což je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create].

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner můžete vytvořit zadáním názvu, image Dockeru a skupiny prostředků Azure do příkazu [az container create][az-container-create]. Volitelně můžete kontejner zveřejnit na internetu zadáním popisku názvu DNS. V tomto rychlém startu nasadíte kontejner, který je hostitelem malé webové aplikace napsané v [Node.js][node-js].

Spuštěním následujícího příkazu spusťte instanci kontejneru. Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které instanci vytváříte, takže ji možná budete muset pro zajištění jedinečnosti upravit.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Během několika sekund byste měli obdržet odpověď na váš požadavek. Zpočátku je kontejner ve stavu **Vytváření**, ale během několika sekund by se měl spustit. Stav můžete zkontrolovat pomocí příkazu [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Po spuštění příkazu se zobrazí plně kvalifikovaný název domény kontejneru a stav jeho zřizování:

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Jakmile se kontejner přesune do stavu **Úspěšné**, můžete k němu přistoupit v prohlížeči přechodem na jeho plně kvalifikovaný název domény:

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure][aci-app-browser]

## <a name="pull-the-container-logs"></a>Vyžádání protokolů kontejneru

Protokoly pro vytvořený kontejner si můžete vyžádat pomocí příkazu [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Výstup:

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>Odstranění kontejneru

Až s kontejnerem skončíte, můžete ho odebrat pomocí příkazu [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Pokud chcete ověřit odstranění kontejneru, spusťte příkaz [az container list](/cli/azure/container#az_container_list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Kontejner **mycontainer** by se neměl zobrazit ve výstupu příkazu. Pokud ve skupině prostředků nemáte žádné další kontejnery, nezobrazí se žádný výstup.

## <a name="next-steps"></a>Další kroky

Veškerý kód pro kontejner použitý v tomto rychlém startu je k dispozici [na GitHubu][app-github-repo] společně s příslušným souborem Dockerfile. Pokud byste si chtěli sami vyzkoušet jeho sestavení a nasazení do služby Azure Container Instances pomocí služby Azure Container Registry, pokračujte na kurz služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurzy služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Pokud chcete vyzkoušet možnosti spouštění kontejnerů v systému orchestrace v Azure, prostudujte si rychlé starty pro [Service Fabric][service-fabric] nebo [Azure Container Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
