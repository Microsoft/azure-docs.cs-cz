---
title: Statická IP adresa pro skupinu kontejnerů
description: Vytvoření skupiny kontejnerů ve virtuální síti a použití služby Azure Application Gateway k vystavení statické IP adresy front-endu pro kontejnerové webové aplikace
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035049"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Zveřejňuje statickou IP adresu pro skupinu kontejnerů.

Tento článek ukazuje jeden ze způsobů, jak zveřejnit statickou veřejnou IP adresu pro [skupinu kontejnerů](container-instances-container-groups.md) pomocí [služby Azure Application Gateway](../application-gateway/overview.md). Pokud potřebujete statický vstupní bod pro externě orientovanou aplikaci, která běží v Azure Container Instances, postupujte podle těchto kroků. 

V tomto článku vytvoříte pomocí rozhraní příkazového řádku Azure CLI prostředky pro tento scénář:

* Virtuální síť Azure
* Skupina kontejnerů nasazená [ve virtuální síti](container-instances-vnet.md) , která je hostitelem malé webové aplikace
* Aplikační brána s veřejnou IP adresou front-end, naslouchací proces pro hostování webu v bráně a směrování do back-endové skupiny kontejnerů.

Pokud je Aplikační brána spuštěná a skupina kontejnerů zpřístupňuje stabilní privátní IP adresu v delegované podsíti sítě, je tato skupina kontejnerů přístupná na této veřejné IP adrese.

> [!NOTE]
> Za Azure se účtují poplatky za Aplikační bránu na základě doby, po kterou je brána zřízená a dostupná, a také na množství dat, která zpracovává. Podívejte se na [ceny](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě

V typickém případě už můžete mít virtuální síť Azure. Pokud ho ještě nemáte, vytvořte ho tak, jak je znázorněno v následujících ukázkových příkazech. Virtuální síť potřebuje samostatné podsítě pro aplikační bránu a skupinu kontejnerů.

Pokud ho potřebujete, vytvořte skupinu prostředků Azure. Příklad:

```azureci
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [AZ Network VNet Create][az-network-vnet-create] . Tento příkaz vytvoří podsíť *myAGSubnet* v síti.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Pomocí příkazu [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] vytvořte podsíť pro back-end skupinu kontejnerů. Tady je název *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Pomocí příkazu [AZ Network Public-IP Create][az-network-public-ip-create] vytvořte prostředek statické veřejné IP adresy. V pozdějším kroku je tato adresa nakonfigurovaná jako front-end aplikační brány.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Vytvoření skupiny kontejnerů

Spusťte následující příkaz [AZ Container Create][az-container-create] a vytvořte skupinu kontejnerů ve virtuální síti, kterou jste nakonfigurovali v předchozím kroku. 

Skupina je nasazená v podsíti *myACISubnet* a obsahuje jednu instanci s názvem *kontejneru AppContainer* , která načte `aci-helloworld` image. Jak je vidět v dalších článcích v dokumentaci, tento obrázek zabalí malou webovou aplikaci napsanou v Node.js, která slouží jako statická stránka HTML. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Po úspěšném nasazení se skupině kontejnerů přiřadí privátní IP adresa ve virtuální síti. Pokud například chcete načíst IP adresu skupiny, spusťte následující příkaz [AZ Container show][az-container-show] :

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Výstup je podobný tomuto: `10.0.2.4`.

Pro použití v pozdějším kroku uložte IP adresu do proměnné prostředí:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Pokud je skupina kontejnerů zastavená, spuštěná nebo restartovaná, může se změnit soukromá IP adresa skupiny kontejnerů. Pokud k tomu dojde, budete muset aktualizovat konfiguraci aplikační brány.

## <a name="create-application-gateway"></a>Vytvoření služby Application Gateway

Vytvořte Aplikační bránu ve virtuální síti podle kroků v [rychlém startu služby Application Gateway](../application-gateway/quick-create-cli.md). Následující příkaz [AZ Network Application-Gateway Create][az-network-application-gateway-create] vytvoří bránu s veřejnou IP adresou front-endu a trasou do back-endové skupiny kontejnerů. Podrobnosti o nastavení brány najdete v [dokumentaci k Application Gateway](../application-gateway/index.yml) .

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


Může trvat až 15 minut, než Azure vytvoří Aplikační bránu. 

## <a name="test-public-ip-address"></a>Test veřejné IP adresy
  
Nyní můžete testovat přístup k webové aplikaci běžící ve skupině kontejnerů za službou Application Gateway.

Spuštěním příkazu [AZ Network Public-IP show][az-network-public-ip-show] načtěte veřejnou IP adresu front-endu brány:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Výstupem je veřejná IP adresa, která se podobá: `52.142.18.133` .

Pokud chcete po úspěšné konfiguraci běžící webové aplikace zobrazit, přejděte v prohlížeči do veřejné IP adresy brány. Úspěšný přístup je podobný následujícímu:

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit skupinu kontejnerů s instancí kontejneru WordPress jako back-end serverem za aplikační bránou, podívejte se na [šablonu pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) .
* Můžete také nakonfigurovat aplikační bránu s certifikátem pro ukončení protokolu SSL. Podívejte se na [Přehled](../application-gateway/ssl-overview.md) a [kurz](../application-gateway/create-ssl-portal.md).
* V závislosti na vašem scénáři zvažte použití dalších řešení vyrovnávání zatížení Azure s Azure Container Instances. Můžete například použít [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) k distribuci provozu napříč několika instancemi kontejnerů a napříč několika oblastmi. Podívejte se na tento [Blogový příspěvek](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
