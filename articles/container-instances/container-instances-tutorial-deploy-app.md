---
title: Kurz služby Azure Container Instances – Nasazení aplikace
description: Kurz služby Azure Container Instances, část 3 ze 3 – Nasazení aplikace
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 29d7114f288f7387d0c7cd5c6afe2eaaa7a8c560
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>Kurz: Nasazení kontejneru do služby Azure Container Instances

Toto je poslední kurz třídílné série. V předchozích částech série se [vytvářela image kontejneru](container-instances-tutorial-prepare-app.md) a [odesílala se do služby Azure Container Registry](container-instances-tutorial-prepare-acr.md). Tento článek sérii uzavírá nasazením kontejneru do služby Azure Container Instances.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasadit kontejner ze služby Azure Container Registry do služby Azure Container Instances
> * Zobrazit spuštěnou aplikaci v prohlížeči
> * Zobrazit protokoly kontejneru

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

V této části budete používat Azure CLI k nasazení image sestavené v [první části kurzu](container-instances-tutorial-prepare-app.md) a odeslané do služby Azure Container Registry v [druhé části kurzu](container-instances-tutorial-prepare-acr.md). Než budete pokračovat, přesvědčte se, že jste tyto kurzy dokončili.

### <a name="get-registry-credentials"></a>Získání přihlašovacích údajů registru

Když nasazujete image hostovanou v privátním registru kontejneru, jako je ten, který jste vytvořili v [druhé části kurzu](container-instances-tutorial-prepare-acr.md), musíte zadat přihlašovací údaje daného registru.

Nejprve získejte úplný název přihlašovacího serveru registru kontejneru (nahraďte zástupný text `<acrName>` názvem svého registru):

```azurecli
az acr show --name <acrName> --query loginServer
```

Potom získejte heslo registru kontejneru:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>Nasazení kontejneru

Nyní nasaďte kontejner pomocí příkazu [az container create][az-container-create]. Nahraďte `<acrLoginServer>` a `<acrPassword>` hodnotami, které jste získali z předchozích dvou příkazů. Nahraďte položku `<acrName>` názvem svého registru kontejneru.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Během několika sekund by se měla zobrazit první odezva z Azure. Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci kontejneru. Pokud se po spuštění příkazu zobrazí chybová zpráva týkající se **popisku názvu DNS**, upravte hodnotu v předchozím příkazu.

### <a name="verify-deployment-progress"></a>Ověření průběhu nasazení

Pokud chcete zobrazit stav nasazení, použijte příkaz [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Opakujte příkaz [az container show][az-container-show], dokud se stav nezmění z *Čekající* na *Spuštěno*, což by mělo trvat necelou minutu. Až bude kontejneru ve stavu *Spuštěno*, přejděte k dalšímu kroku.

## <a name="view-the-application-and-container-logs"></a>Zobrazení aplikace a protokolů kontejneru

Po úspěšném nasazení zobrazte plně kvalifikovaný název domény kontejneru pomocí příkazu [az container show][az-container-show]:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Příklad:
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

Pokud chcete zobrazit spuštěnou aplikaci, v oblíbeném prohlížeči přejděte na zobrazený název DNS:

![Aplikace Hello World v prohlížeči][aci-app-browser]

Můžete zobrazit také výstup protokolu kontejneru:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Příklad výstupu:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v této sérii kurzů, můžete spuštěním příkazu [az group delete][az-group-delete] odebrat skupinu prostředků a všechny prostředky, které obsahuje. Tento příkaz odstraní vytvořený registr kontejneru, spuštěný kontejner i všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili proces nasazení kontejneru do služby Azure Container Instances. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Nasadili jste kontejner ze služby Azure Container Registry pomocí Azure CLI.
> * Zobrazili jste aplikaci v prohlížeči.
> * Zobrazili jste protokoly kontejneru.

Když už máte základy zvládnuté, můžete pokračovat dalšími informacemi o službě Azure Container Instances. Například tím, jak fungují skupiny kontejnerů:

> [!div class="nextstepaction"]
> [Skupiny kontejnerů ve službě Azure Container Instances](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
