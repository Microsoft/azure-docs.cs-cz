---
title: Statická ADRESA IP pro skupinu kontejnerů
description: Vytvoření skupiny kontejnerů ve virtuální síti a použití aplikační brány Azure k vystavení statické ip adresy front-endu do kontejnerizované webové aplikace
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481785"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Vystavit statickou adresu IP pro skupinu kontejnerů

Tento článek ukazuje jeden způsob, jak vystavit statickou, veřejnou IP adresu pro [skupinu kontejnerů](container-instances-container-groups.md) pomocí [aplikační brány](../application-gateway/overview.md)Azure . Postupujte podle těchto kroků, když potřebujete statický vstupní bod pro externí kontejnerizované aplikace, která běží v Azure Container Instances. 

V tomto článku pomocí azure cli k vytvoření prostředků pro tento scénář:

* Virtuální síť Azure
* Skupina kontejnerů nasazená [ve virtuální síti (preview),](container-instances-vnet.md) která je hostitelem malé webové aplikace
* Aplikační brána s veřejnou ip adresou front-endu, naslouchací proces pro hostování webu na bráně a trasa do skupiny back-endových kontejnerů

Dokud je spuštěna aplikační brána a skupina kontejnerů zpřístupňuje stabilní privátní IP adresu v delegované podsíti sítě, skupina kontejnerů je přístupná na této veřejné IP adrese.

> [!NOTE]
> Poplatky za aplikační bránu Azure na základě doby, po kterou je brána zřízená a dostupná, a také na množství dat, která zpracovává. Viz [ceny](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě

V typickém případě už možná máte virtuální síť Azure. Pokud ho nemáte, vytvořte ho tak, jak je znázorněno na následujících ukázkových příkazech. Virtuální síť potřebuje samostatné podsítě pro aplikační bránu a skupinu kontejnerů.

Pokud ji potřebujete, vytvořte skupinu prostředků Azure. Například:

```azureci
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [vytvořit virtuální síť az.][az-network-vnet-create] Tento příkaz vytvoří podsíť *myAGSubnet* v síti.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Pomocí příkazu [vytvořit podsíť sítě AZ][az-network-vnet-subnet-create] síť vytvořte podsíť pro skupinu back-endových kontejnerů. Zde je *pojmenována myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Pomocí příkazu [az network public-ip create][az-network-public-ip-create] vytvořte statický veřejný prostředek IP. V pozdějším kroku je tato adresa nakonfigurována jako front-end aplikační brány.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Vytvoření skupiny kontejnerů

Spusťte následující [vytvoření kontejneru az][az-container-create] a vytvořte skupinu kontejnerů ve virtuální síti, kterou jste nakonfigurovali v předchozím kroku. 

Skupina je nasazena v podsíti *myACISubnet* a obsahuje jednu instanci s názvem *appcontainer,* která vytáhne bitovou `aci-helloworld` kopii. Jak je znázorněno v jiných článcích v dokumentaci, tento obrázek zabalí malou webovou aplikaci napsanou v souboru Node.js, která slouží statické stránce HTML. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Při úspěšném nasazení je skupině kontejnerů přiřazena privátní IP adresa ve virtuální síti. Chcete-li například načíst IP adresu skupiny, spusťte následující příkaz [az container show:][az-container-show]

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Výstup je podobný tomuto: `10.0.2.4`.

Pro pozdější použití uložte ip adresu do proměnné prostředí:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Vytvoření služby Application Gateway

Vytvořte aplikační bránu ve virtuální síti podle kroků v [rychlém startu aplikační brány](../application-gateway/quick-create-cli.md). Následující příkaz [vytvoření síťové aplikační brány az][az-network-application-gateway-create] vytvoří bránu s veřejnou front-endovou IP adresou a trasou do skupiny back-endových kontejnerů. Podrobnosti o nastavení brány najdete v dokumentaci k [aplikační bráně.](/azure/application-gateway/)

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Vytvoření aplikační brány může azure trvat až 15 minut. 

## <a name="test-public-ip-address"></a>Otestovat veřejnou IP adresu
  
Teď můžete otestovat přístup k webové aplikaci spuštěné ve skupině kontejnerů za aplikační bránou.

Spusťte příkaz [az network public-ip show][az-network-public-ip-show] a načtěte front-endovou veřejnou IP adresu brány:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Výstup emituje veřejnou `52.142.18.133`IP adresu, podobnou: .

Chcete-li zobrazit spuštěnou webovou aplikaci při úspěšné konfiguraci, přejděte v prohlížeči na veřejnou IP adresu brány. Úspěšný přístup je podobný:

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Další kroky

* Podívejte se na [šablonu rychlého startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) a vytvořte skupinu kontejnerů s instancí kontejneru WordPress jako back-endový server za aplikační bránou.
* Můžete také nakonfigurovat aplikační bránu s certifikátem pro ukončení SSL. Podívejte se na [přehled](../application-gateway/ssl-overview.md) a [výukový program](../application-gateway/create-ssl-portal.md).
* V závislosti na scénáři zvažte použití jiných řešení azure vyrovnávání zatížení s instancemi kontejnerů Azure. Pomocí Azure [Traffic Manageru](../traffic-manager/traffic-manager-overview.md) můžete například distribuovat provoz mezi více instancí kontejnerů a do více oblastí. Podívejte se na tento [blogový příspěvek](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show