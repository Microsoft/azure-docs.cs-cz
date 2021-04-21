---
title: Kurz – nasazení aplikace kontejneru do instance kontejneru
description: Azure Container Instances kurz 3 ze 3 – nasazení kontejnerové aplikace do Azure Container Instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2d12b0bc91cf844001b770c8a7e534ad6b1b9b2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763878"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Kurz: nasazení aplikace typu kontejner do Azure Container Instances

Toto je poslední kurz třídílné série. V předchozích částech série se [vytvářela image kontejneru](container-instances-tutorial-prepare-app.md) a [odesílala se do služby Azure Container Registry](container-instances-tutorial-prepare-acr.md). Tento článek sérii uzavírá nasazením kontejneru do služby Azure Container Instances.

V tomto kurzu jste:

> [!div class="checklist"]
> * Nasadit kontejner ze služby Azure Container Registry do služby Azure Container Instances
> * Zobrazit spuštěnou aplikaci v prohlížeči
> * Zobrazit protokoly kontejneru

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

V této části budete používat Azure CLI k nasazení image sestavené v [první části kurzu](container-instances-tutorial-prepare-app.md) a odeslané do služby Azure Container Registry v [druhé části kurzu](container-instances-tutorial-prepare-acr.md). Než budete pokračovat, přesvědčte se, že jste tyto kurzy dokončili.

### <a name="get-registry-credentials"></a>Získání přihlašovacích údajů registru

Když nasadíte image, která je hostovaná v privátním registru kontejneru Azure, jako je ta vytvořená v [druhém kurzu](container-instances-tutorial-prepare-acr.md), musíte zadat přihlašovací údaje pro přístup k registru. 

Osvědčeným postupem pro mnoho scénářů je vytvořit a nakonfigurovat instanční objekt Azure Active Directory s oprávněním pro *vyžádání* obsahu pro váš registr. Ukázkové skripty pro vytvoření instančního objektu s potřebnými oprávněními najdete v tématu [ověřování pomocí Azure Container Registry z Azure Container Instances](../container-registry/container-registry-auth-aci.md) . Poznamenejte si *ID instančního objektu* a *heslo objektu služby*. Tyto přihlašovací údaje použijete pro přístup k registru při nasazení kontejneru.

Také potřebujete úplný název přihlašovacího serveru registru kontejneru (nahraďte `<acrName>` názvem vašeho registru):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Nasazení kontejneru

Nyní nasaďte kontejner pomocí příkazu [az container create][az-container-create]. Nahraďte `<acrLoginServer>` hodnotou, kterou jste získali z předchozího příkazu. Nahraďte `<service-principal-ID>` a `<service-principal-password>` pomocí ID a hesla instančního objektu, který jste vytvořili pro přístup k registru. Nahraďte `<aciDnsLabel>` požadovaným názvem DNS.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
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

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Například:
```output
"aci-demo.eastus.azurecontainer.io"
```

Pokud chcete zobrazit spuštěnou aplikaci, v oblíbeném prohlížeči přejděte na zobrazený název DNS:

![Aplikace Hello World v prohlížeči][aci-app-browser]

Můžete zobrazit také výstup protokolu kontejneru:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Příklad výstupu:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1&quot; 200 1663 &quot;-&quot; &quot;Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
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
