---
title: "Kurz služby Azure Container Instances – Nasazení aplikace"
description: "Kurz služby Azure Container Instances, část 3 ze 3 – Nasazení aplikace"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0532d255b271b2155ae3115f8f96c4cbb53916e4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Nasazení kontejneru do služby Azure Container Instances

Toto je poslední kurz třídílné série. V předchozích částech série se [vytvořila image kontejneru](container-instances-tutorial-prepare-app.md) a [nasdílela se do služby Azure Container Registry](container-instances-tutorial-prepare-acr.md). Tento článek uzavírá sérii kurzů nasazením kontejneru do služby Azure Container Instances.

V tomto kurzu jste:

> [!div class="checklist"]
> * Nasazení kontejneru ze služby Azure Container Registry pomocí Azure CLI
> * Zobrazení aplikace v prohlížeči
> * Zobrazení protokolů kontejneru

## <a name="before-you-begin"></a>Než začnete

Tento kurz vyžaduje použití Azure CLI verze 2.0.27 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0][azure-cli-install].

K dokončení tohoto kurzu potřebujete vývojové prostředí pro Docker nainstalované místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-mac] nebo systému [Windows][docker-windows] či [Linux][docker-linux].

Azure Cloud Shell neobsahuje součásti Dockeru nutné pro dokončení všech kroků v tomto kurzu. Je nutné nainstalovat do místního počítače rozhraní příkazového řádku Azure a vývojové prostředí Dockeru, aby bylo možné tento kurz dokončit.

## <a name="deploy-the-container-using-the-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

Azure CLI umožňuje nasazení kontejneru do služby Azure Container Instances jediným příkazem. Vzhledem k tomu, že je image kontejneru hostovaná v privátní službě Azure Container Registry, musíte zahrnout přihlašovací údaje vyžadované pro přístup k ní. Přihlašovací údaje získáte pomocí následujících příkazů Azure CLI.

Přihlašovací server registru kontejneru (použijte název vašeho registru):

```azurecli
az acr show --name <acrName> --query loginServer
```

Heslo registru kontejneru:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Vaše aplikace bude muset být [připravená předem][prepare-app]. Pokud chcete nasadit svou image kontejneru z registru kontejneru s požadavkem na prostředky 1 jádra procesoru a 1 GB paměti, spusťte následující příkaz [az container create][az-container-create]. Nahraďte `<acrLoginServer>` a `<acrPassword>` hodnotami, které jste získali z předchozích dvou příkazů. Nahraďte `<acrName>` názvem vašeho registru kontejneru. Můžete také nahradit `aci-tutorial-app` názvem, který chcete použít pro novou aplikaci.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Během několika sekund by se měla zobrazit první odezva z Azure Resource Manageru. Hodnota `--dns-name-label` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci kontejneru. Pokud se po spuštění příkazu zobrazí chybová zpráva týkající se **popisku názvu DNS**, aktualizujte hodnotu v předchozím příkladu.

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

Příklad výstupu: `"aci-demo.eastus.azurecontainer.io"`

Pokud chcete zobrazit spuštěnou aplikaci, v oblíbeném prohlížeči přejděte na zobrazený název DNS:

![Aplikace Hello World v prohlížeči][aci-app-browser]

Můžete zobrazit také výstup protokolu kontejneru:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Výstup:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v této sérii kurzů, můžete spuštěním příkazu [az group delete][az-group-delete] odebrat skupinu prostředků a všechny prostředky, které obsahuje. Tento příkaz odstraní vytvořený registr kontejneru, spuštěný kontejner i všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili proces nasazení kontejnerů do služby Azure Container Instances. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Nasadili jste kontejner ze služby Azure Container Registry pomocí Azure CLI.
> * Zobrazili jste aplikaci v prohlížeči.
> * Zobrazili jste protokoly kontejneru.

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
