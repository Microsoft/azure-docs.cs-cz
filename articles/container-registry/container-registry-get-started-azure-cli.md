---
title: Rychlý start – vytvoření privátního registru Dockeru v Azure – Azure CLI
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí Azure CLI.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: e75a2d126680c71542aa04bae5a30ea7c376cea1
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255920"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Rychlý start: Vytvořit privátní registr pomocí rozhraní příkazového řádku Azure

Azure Container Registry je spravovaná služba registru kontejnerů Dockeru sloužící k ukládání privátních imagí kontejnerů Dockeru. Tato příručka podrobně popisuje vytvoření instance služby Azure Container Registry pomocí Azure CLI, nasdílení image kontejneru do registru a nakonec nasazení kontejneru z registru do služby Azure Container Instances (ACI).

Tento rychlý start vyžaduje použití Azure CLI verze 2.0.27 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto rychlém startu vytvoříte registr úrovně *Basic*. Služba Azure Container Registry je dostupná v několika různých skladových položkách, které stručně popisuje následující tabulka. Další podrobnosti o každé z nich najdete v tématu [Skladové položky služby Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Pomocí příkazu [az acr create][az-acr-create] vytvořte instanci služby ACR. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu se používá *myContainerRegistry007*. Aktualizujte název na jedinečnou hodnotu.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Po vytvoření registru je výstup podobný tomuto:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
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

V celé zbývající části tohoto rychlého startu se položka `<acrName>` používá jako zástupný symbol pro název registru kontejneru.

## <a name="log-in-to-acr"></a>Přihlášení ke službě ACR

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k instanci služby ACR. K tomu použijte příkaz [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné).

## <a name="push-image-to-acr"></a>Nasdílení image do služby ACR

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. Pokud ještě nemáte žádné image místního kontejneru, spusťte následující příkaz a vyžádejte si existující image z Docker Hubu.

```bash
docker pull microsoft/aci-helloworld
```

Předtím než můžete nasdílet image do registru, musíte ji označit plně kvalifikovaným názvem přihlašovacího serveru ACR. Spuštěním následujícího příkazu získáte úplný název přihlašovacího serveru instance ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Označte image pomocí příkazu [docker tag][docker-tag]. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Nakonec pomocí příkazu [docker push][docker-push] nasdílejte image do instance služby ACR. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Výpis imagí kontejnerů

Následující příklad uvádí úložiště v registru:

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```bash
Result
----------------
aci-helloworld
```

Následující příklad vypíše značky v úložišti **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Výstup:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Nasazení image do služby ACI

Abyste mohli nasadit instanci kontejneru z registru, který jste vytvořili, musíte při nasazování zadat přihlašovací údaje registru. V produkčních scénářích byste pro přístup k registru kontejneru měli použít [instanční objekt][container-registry-auth-aci], ale pro zkrácení tohoto rychlého startu povolte ve svém registru uživatele s rolí správce pomocí následujícího příkazu:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

Po povolení správce bude uživatelské jméno stejné jako název vašeho registru a heslo můžete načíst pomocí tohoto příkazu:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Pokud chcete nasadit svou image kontejneru s 1 jádrem procesoru a 1 GB paměti, spusťte následující příkaz. Nahraďte `<acrName>`, `<acrLoginServer>` a `<acrPassword>` hodnotami, které jste získali z předchozích příkazů.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Z Azure Resource Manageru byste měli získat první odezvu s podrobnostmi o kontejneru. Pokud chcete monitorovat stav kontejneru a zkontrolovat, kdy je spuštěný, zopakujte příkaz [az container show][az-container-show]. Mělo by to trvat necelou minutu.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Zobrazení aplikace

Po úspěšném nasazení do služby ACI načtěte plně kvalifikovaný název domény kontejneru pomocí příkazu [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Příklad výstupu: `"aci-demo.eastus.azurecontainer.io"`

Pokud chcete zobrazit spuštěnou aplikaci, v oblíbeném prohlížeči přejděte na tuto veřejnou IP adresu.

![Aplikace Hello World v prohlížeči][aci-app-browser]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, instance ACR a všech imagí kontejneru použít příkaz [az group delete][az-group-delete].

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste pomocí Azure CLI vytvořili službu Azure Container Registry, nasdíleli jste image kontejneru do registru a prostřednictvím služby Azure Container Instances jste spustili její instanci. Pokračujte ke kurzu služby Azure Container Instances, kde najdete podrobnější přehled ACI.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az-container-show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md
