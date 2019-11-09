---
title: Kurz – nasazení aplikace kontejneru do Azure Container Instances
description: Azure Container Instances kurz 3 ze 3 – nasazení kontejnerové aplikace do Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f2890948dd15fa972104e4ef11001e83a2abd4f8
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846584"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Kurz: nasazení aplikace typu kontejner do Azure Container Instances

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

Když nasadíte image, která je hostovaná v privátním registru kontejneru Azure, jako je ta vytvořená v [druhém kurzu](container-instances-tutorial-prepare-acr.md), musíte zadat přihlašovací údaje pro přístup k registru. 

Osvědčeným postupem pro mnoho scénářů je vytvořit a nakonfigurovat instanční objekt Azure Active Directory s oprávněním pro *vyžádání* obsahu pro váš registr. Ukázkové skripty pro vytvoření instančního objektu s potřebnými oprávněními najdete v tématu [ověřování pomocí Azure Container Registry z Azure Container Instances](../container-registry/container-registry-auth-aci.md) . Poznamenejte si *ID instančního objektu* a *heslo objektu služby*. Tyto přihlašovací údaje použijete pro přístup k registru při nasazení kontejneru.

Také potřebujete úplný název přihlašovacího serveru registru kontejneru (nahraďte `<acrName>` názvem vašeho registru):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Nasazení kontejneru

Nyní pomocí příkazu [AZ Container Create][az-container-create] nasaďte kontejner. Nahraďte `<acrLoginServer>` hodnotou, kterou jste získali z předchozího příkazu. Nahraďte `<service-principal-ID>` a `<service-principal-password>` pomocí ID a hesla instančního objektu, který jste vytvořili pro přístup k registru. Nahraďte `<aciDnsLabel>` požadovaným názvem DNS.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

Během několika sekund by se měla zobrazit první odezva z Azure. Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci kontejneru. Pokud se po spuštění příkazu zobrazí chybová zpráva týkající se **popisku názvu DNS**, upravte hodnotu v předchozím příkazu.

### <a name="verify-deployment-progress"></a>Ověření průběhu nasazení

Chcete-li zobrazit stav nasazení, použijte příkaz [AZ Container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Opakujte příkaz [AZ Container show][az-container-show] , dokud se nezmění stav z *nedokončené* na *spuštěno*, což by mělo trvat až minutu. Až bude kontejneru ve stavu *Spuštěno*, přejděte k dalšímu kroku.

## <a name="view-the-application-and-container-logs"></a>Zobrazení aplikace a protokolů kontejneru

Po úspěšném nasazení Zobrazte plně kvalifikovaný název domény kontejneru (FQDN) pomocí příkazu [AZ Container show][az-container-show] :

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

Pokud už nepotřebujete žádné prostředky, které jste vytvořili v této sérii kurzů, můžete spuštěním příkazu [AZ Group Delete][az-group-delete] odebrat skupinu prostředků a všechny prostředky, které obsahuje. Tento příkaz odstraní vytvořený registr kontejneru, spuštěný kontejner i všechny související prostředky.

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
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
