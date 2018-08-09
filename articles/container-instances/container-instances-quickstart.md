---
title: 'Rychlý start: Vytvoření prvního kontejneru služby Azure Container Instances'
description: V tomto rychlém startu pomocí Azure CLI nasadíte kontejner ve službě Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 07632e85719e2d0d446b8f718dbc64d2e9d77617
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441370"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Rychlý start: Vytvoření prvního kontejneru ve službě Azure Container Instances

Služba Azure Container Instances usnadňuje vytváření a správu kontejnerů Dockeru v Azure, aniž byste museli zřizovat virtuální počítače nebo používat službu vyšší úrovně. V tomto rychlém startu vytvoříte kontejner v Azure a zveřejníte ho na internetu s použitím plně kvalifikovaného názvu domény. K dokončení této operace stačí jediný příkaz. Během několika sekund uvidíte ve svém prohlížeči toto:

![Aplikace nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][aci-app-browser]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-account] před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení tohoto rychlého startu můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.27 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Služba Azure Container Instances, stejně jako všechny prostředky Azure, musí být umístěná ve skupině prostředků, což je logická kolekce, ve které se nasazují a spravují prostředky Azure.

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

Jakmile se kontejner přesune do stavu **Úspěšné**, přejděte v prohlížeči na jeho plně kvalifikovaný název domény:

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure][aci-app-browser]

## <a name="pull-the-container-logs"></a>Vyžádání protokolů kontejneru

Prohlížení protokolů pro instanci kontejneru je užitečné při řešení problémů s kontejnerem nebo aplikací, která se v něm spouští.

Vyžádejte si protokoly kontejneru pomocí příkazu [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Ve výstupu se zobrazí protokoly kontejneru a měly by se zobrazit i požadavky HTTP GET, které se vygenerovaly, když jste aplikaci zobrazili v prohlížeči.

```console
$ az container logs --resource-group myResourceGroup -n mycontainer
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="attach-output-streams"></a>Připojení výstupních datových proudů

Kromě sledování protokolů můžete k datovým proudům kontejneru připojit i standardní výstupní a chybový datový proud.

Nejprve spuštěním příkazu [az container attach][az-container-attach] připojte svou konzolu k výstupním datovým proudům kontejneru:

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

Po připojení několikrát aktualizujte svůj prohlížeč, aby se vygeneroval další výstup. Nakonec konzolu odpojte stisknutím `Control+C`. Zobrazený výstup by měl vypadat přibližně takto:

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až s kontejnerem skončíte, odeberte ho pomocí příkazu [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Pokud chcete ověřit odstranění kontejneru, spusťte příkaz [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Kontejner **mycontainer** by se neměl zobrazit ve výstupu příkazu. Pokud ve skupině prostředků nemáte žádné další kontejnery, nezobrazí se žádný výstup.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z image ve veřejném registru Docker Hub. Pokud si chcete sami sestavit image kontejneru a nasadit ji do služby Azure Container Instances z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Pokud chcete vyzkoušet možnosti spouštění kontejnerů v systému orchestrace v Azure, prostudujte si rychlé starty pro [Service Fabric][service-fabric] nebo [Azure Kubernetes Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
