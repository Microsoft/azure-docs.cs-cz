---
title: Rychlý start – spuštění aplikace ve službě Azure Container Instances – rozhraní příkazového řádku
description: V tomto rychlém startu použijete Azure CLI k nasazení aplikace typu kontejner Dockeru pro spuštění v izolovaného kontejneru ve službě Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 93a41610035d91774256410cea6af1d06b085d30
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562058"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-the-azure-cli"></a>Rychlý start: Spuštění aplikace typu kontejner ve službě Azure Container Instances pomocí Azure CLI

Spouštějte kontejnery Dockeru v Azure rychle a snadno pomocí Azure Container Instances. Nemusíte nasazovat virtuální počítače ani používat úplnou platformu orchestrace kontejnerů jako Kubernetes. V tomto rychlém startu pomocí Azure CLI vytvoříte kontejner v Azure a zpřístupněte svou aplikaci s použitím plně kvalifikovaného názvu domény (FQDN). Několik sekund po provedení příkazu k jednomu nasazení můžete přejít k běžící aplikaci:

![Aplikace nasazená do služby Azure Container Instances zobrazená v prohlížeči][aci-app-browser]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][azure-account] před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení tohoto rychlého startu můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud chcete používat místně, potřebujete verzi 2.0.27 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Instance kontejnerů Azure, stejně jako všechny prostředky Azure, se musí nasadit do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

Nejprve pomocí následujícího příkazu [az group create][az-group-create]vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Teď máte skupinu prostředků a můžete spustit kontejner v Azure. K vytvoření instance kontejneru pomocí Azure CLI zadejte do příkazu [az container create] [ az-container-create] název skupiny prostředků, název instance kontejneru a image kontejneru Dockeru. V tomto rychlém startu použijete `microsoft/aci-helloworld` bitovou kopii z veřejného registru Docker Hub. Tento obrázek balíčky malé webové aplikace napsané v Node.js, která slouží jako statická stránka HTML.

Kontejnery můžete zveřejnit na internetu tak, že zadáte jeden nebo více otevíraných portů, popisek názvu DNS nebo oboje. V tomto rychlém startu nasadíte kontejner s použitím popisku názvu DNS tak, aby webová aplikace je veřejně dostupný.

Spuštěním následujícího příkazu spusťte instanci kontejneru. Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci. Pokud se zobrazí chybová zpráva „Popisek názvu DNS není dostupný“, zkuste jiný popisek názvu DNS.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Během několika sekund byste měli dostat odpověď z rozhraní příkazového řádku Azure oznamující, že nasazení bylo dokončeno. Zkontrolujte stav pomocí příkazu [az container show] [ az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Po spuštění příkazu se zobrazí plně kvalifikovaný název domény kontejneru a stav jeho zřizování.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Jakmile je kontejner `ProvisioningState` ve stavu **Úspěšné**, přejděte v prohlížeči na jeho plně kvalifikovaný název domény. Pokud zobrazená webová stránka vypadá přibližně takto, blahopřejeme! Úspěšně jste nasadili aplikaci spuštěnou v kontejneru Dockeru do Azure.

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure][aci-app-browser]

Pokud se zpočátku aplikace nezobrazí, může být nutné několik sekund počkat, než se DNS rozšíří, a pak zkusit aktualizovat stránku v prohlížeči.

## <a name="pull-the-container-logs"></a>Vyžádání protokolů kontejneru

Pokud potřebujete odstranit potíže s kontejnerem nebo aplikací, která je v něm spuštěna (nebo pouze zobrazit její výstup), začněte zobrazením protokolů instance kontejneru.

Vyžádejte si protokoly instance kontejneru pomocí příkazu [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Ve výstupu se zobrazí protokoly kontejneru a měly by se zobrazit i požadavky HTTP GET, které se vygenerovaly, když jste aplikaci zobrazili v prohlížeči.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.105 - - [01/Oct/2018:18:25:51 +0000] "GET / HTTP/1.0" 200 1663 "-" "-"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```

## <a name="attach-output-streams"></a>Připojení výstupních datových proudů

Kromě zobrazení protokolů můžete k datovým proudům kontejneru připojit i standardní výstupní a chybový datový proud.

Nejprve je potřeba provést [az container attach] [ az-container-attach] výstupním datovým proudům příkazu připojte svou konzolu ke kontejneru:

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

Po připojení několikrát aktualizujte svůj prohlížeč, aby se vygeneroval další výstup. Když jste hotovi, odpojte konzolu stisknutím `Control+C`. Zobrazený výstup by měl vypadat přibližně takto:

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

Když jste hotovi se skupinou prostředků *myResourceGroup* a všemi prostředky, které obsahuje, odstraňte ji pomocí příkazu [az group delete] [ az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili instanci kontejneru Azure pomocí image ve veřejném registru Docker Hub. Pokud chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Vyzkoušet možnosti spouštění kontejnerů v systému Orchestrace v Azure, najdete v článku [Azure Kubernetes Service (AKS)] [ container-service] šablon rychlý start.

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
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
