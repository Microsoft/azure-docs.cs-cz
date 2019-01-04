---
title: Nasazení instancí kontejneru do služby Azure virtual network
description: Zjistěte, jak nasadit skupiny kontejnerů na nový nebo existující virtuální síť Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 11/28/2018
ms.author: danlep
ms.openlocfilehash: 172ddd11cb956ab6d74e1ce870e2378205dd1613
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993282"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Nasazení instancí kontejneru do služby Azure virtual network

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečené a privátní sítě, včetně filtrování a směrování, partnerský vztah pro Azure a místním prostředkům. Nasazením skupiny kontejnerů do služby Azure virtual network můžete své kontejnery bezpečně komunikovat s ostatními prostředky ve virtuální síti.

Skupiny kontejner nasadí do služby Azure virtual network umožňují scénáře, jako jsou:

* Přímá komunikace mezi skupinami kontejnerů ve stejné podsíti
* Odeslat [založené na úlohách](container-instances-restart-policy.md) výstup úlohy z instance kontejneru na databázi ve virtuální síti
* Načíst obsah pro container instances ze [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) ve virtuální síti
* Kontejner komunikaci s virtuálními počítači ve virtuální síti
* Kontejner komunikaci s místním prostředkům přes [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi preview a některé [platí omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="virtual-network-deployment-limitations"></a>Omezení nasazení virtuální sítě

Při nasazení skupin kontejneru k virtuální síti platí určitá omezení.

* Kontejnery Windows se nepodporují
* Nasazení skupin kontejnerů k podsíti, podsíť nemůže obsahovat další typy prostředků. Odeberte všechny existující prostředky z existující podsítě před nasazením skupiny kontejnerů na ni, nebo vytvořte novou podsíť.
* Skupin kontejnerů nasazený do virtuální sítě nepodporuje aktuálně veřejné IP adresy nebo popisky názvů DNS.
* Z důvodu dalších síťových prostředků zahrnutých nasazení skupiny kontejnerů k virtuální síti je obvykle o něco pomalejší než nasazení kontejneru standardní instance.

## <a name="preview-limitations"></a>Omezení verze Preview

Tato funkce je ve verzi preview, platí následující omezení při nasazování instancí kontejneru k virtuální síti.

**Podporované** oblastech:

* Severní Evropa (northeurope)
* Západní Evropa (westeurope)
* USA – západ (westus)
* USA – východ (eastus)

**Nepodporovaná** síťové prostředky:

* Skupina zabezpečení sítě
* Nástroj pro vyrovnávání zatížení Azure

**Odstranění prostředku sítě** vyžaduje [další kroky](#delete-network-resources) po nasazení skupiny kontejnerů na virtuální síť.

## <a name="required-network-resources"></a>Požadovaným síťovým zdrojům

Existují tři Azure Virtual Network prostředky potřebné pro nasazení skupiny kontejnerů na virtuální síť: [virtuální sítě](#virtual-network) samostatně, [delegovaný podsítě](#subnet-delegated) v rámci virtuální sítě a [sítě profilu](#network-profile). 

### <a name="virtual-network"></a>Virtuální síť

Virtuální síť definuje adresní prostor, ve kterém můžete vytvořit jednu nebo více podsítí. Potom nasazujte prostředky Azure (např. skupiny kontejnerů) do podsítě ve virtuální síti.

### <a name="subnet-delegated"></a>Podsíť (delegovaný)

Podsítě virtuální sítě rozdělit do samostatné adresní prostory použitelné Azure prostředky, které umístíte do nich. Můžete vytvořit jednu nebo několik podsítí ve virtuální síti.

Podsítě, který používáte pro skupiny kontejnerů může obsahovat pouze skupiny kontejnerů. Při prvním nasazení skupiny kontejnerů na podsíť, Azure deleguje tuto podsíť do služby Azure Container Instances. Jakmile delegovat, podsítě je možné jenom pro skupiny kontejnerů. Při pokusu o nasazení prostředků než skupiny kontejnerů do delegované podsítě, operace se nezdaří.

### <a name="network-profile"></a>Profil sítě

Profil sítě je šablonu konfigurace sítě pro prostředky Azure. Určuje určité vlastnosti sítě pro prostředek, třeba podsíť, do kterého mají být nasazeny. Při prvním použití [az container vytvořit] [ az-container-create] příkaz pro nasazení skupiny kontejnerů do podsítě (a tedy virtuální sítě), Azure vytvoří profil sítě za vás. Pak můžete použít tento profil sítě pro budoucí nasazení do podsítě. 

Použití šablony Resource Manageru, soubor YAML nebo programové metody k nasazení skupiny kontejnerů k podsíti, budete muset zadat úplné ID prostředku Resource Manageru profilu sítě. Můžete použít profil dříve vytvořené využitím [az container vytvořit][az-container-create], nebo vytvořte profil pomocí šablony Resource Manageru (naleznete v tématu [příkladu šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) a [odkaz](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Chcete-li získat ID dříve vytvořený profil, použijte [az přehled profilu sítě] [ az-network-profile-list] příkazu. 

V následujícím diagramu se nasadilo do podsítě delegovat do služby Azure Container Instances několik skupin kontejnerů. Po nasazení jedné skupiny kontejnerů na podsíť, do něj můžete nasadit další kontejneru skupiny tak, že zadáte stejný profil sítě.

![Skupin kontejnerů v rámci virtuální sítě][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scénáře nasazení

Můžete použít [az container vytvořit] [ az-container-create] nasazení kontejneru skupiny pro novou virtuální síť a povolit Azure k vytvoření požadovaných síťových prostředků za vás, nebo nasadit do existující virtuální sítě. 

### <a name="new-virtual-network"></a>Nová virtuální síť.

Pro nasazení do nové virtuální sítě a Azure vytvoří síťové prostředky pro vás automaticky, zadejte následující po spuštění [az container vytvořit][az-container-create]:

* Název virtuální sítě
* Předponu adresy virtuální sítě ve formátu CIDR
* Název podsítě
* Předpona adresy podsítě ve formátu CIDR

Virtuální síť a předpony podsítě zadejte adresní prostory virtuální sítě a podsítě, v uvedeném pořadí. Tyto hodnoty jsou reprezentovány v zápisu notace CIDR (Classless Inter-Domain Routing), třeba `10.0.0.0/16`. Další informace o práci s podsítěmi, naleznete v tématu [přidání, změna nebo odstranění podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

Po nasazení vaší první skupinu kontejnerů s touto metodou, můžete nasadit do stejné podsítě zadáním virtuální síť a podsíť názvy nebo síťový profil, který Azure automaticky vytvoří za vás. Protože Azure deleguje podsítě do služby Azure Container Instances, můžete nasadit *pouze* skupiny kontejnerů do podsítě.

### <a name="existing-virtual-network"></a>Existující virtuální sítě

Nasazení kontejneru skupiny pro existující virtuální sítě:

1. Vytvořte podsíť v existující virtuální síti, nebo je prázdný existující podsíti *všechny* další prostředky
1. Nasadit skupinu kontejnerů s [az container vytvořit] [ az-container-create] a zadejte jednu z následujících akcí:
   * Název virtuální sítě a podsítě
   * Prostředek virtuální sítě v ID a ID prostředku podsítě, která umožňuje pomocí virtuální sítě z jiné skupiny prostředků
   * Název profilu sítě nebo ID, které lze získat pomocí [přehled profilu sítě az][az-network-profile-list]

Po nasazení vaší první skupinu kontejnerů do existující podsítě Azure deleguje tuto podsíť do služby Azure Container Instances. Už moct nasazovat prostředky než kontejner skupiny pro tuto podsíť.

## <a name="deployment-examples"></a>Příklady nasazení

Následující části popisují, jak nasadit skupiny kontejnerů do virtuální sítě pomocí Azure CLI. U příkladů příkazů se formátují pro **Bash** prostředí. Pokud chcete jiné prostředí, jako je PowerShell nebo příkazového řádku, znaky pokračování řádku odpovídajícím způsobem upravte.

### <a name="deploy-to-a-new-virtual-network"></a>Nasazení do nové virtuální sítě

Nejprve nasadit skupinu kontejnerů a zadejte parametry pro nové virtuální sítě a podsítě. Pokud zadáte tyto parametry, Azure vytvoří virtuální síť a podsíť, deleguje podsítě do služby Azure Container instances a také vytvoří profil sítě. Po vytvoření těchto prostředků, vaše skupina kontejnerů se nasadí do podsítě.

Spusťte následující příkaz [az container vytvořit] [ az-container-create] příkaz, který určuje nastavení pro nové virtuální sítě a podsítě. Je třeba zadat název skupiny prostředků, který byl vytvořen v oblasti, která [podporuje](#preview-limitations) skupiny kontejnerů ve službě virtual network. Tento příkaz nasadí [microsoft/aci-helloworld] [ aci-helloworld] kontejner, který malé Node.js webovým serverem, který obsluhuje statický webové stránky. V další části bude nasazení skupiny pro druhý kontejner do stejné podsítě a testování komunikace mezi instance dvou kontejnerů.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image microsoft/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Při nasazení do nové virtuální sítě s použitím této metody nasazení může trvat několik minut, zatímco se vytvoří síťové prostředky. Po počátečním nasazení skupiny nasazení dalších kontejnerů dokončení rychleji.

### <a name="deploy-to-existing-virtual-network"></a>Nasazení do existující virtuální sítě

Teď, když nasadíte skupinu kontejnerů do nové virtuální sítě nasadit druhé skupiny kontejnerů do stejné podsítě a ověřili komunikaci mezi instance dvou kontejnerů.

Nejprve získejte IP adresu první skupiny kontejnerů jste nasadili, *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Výstup by měl zobrazit IP adresu skupiny kontejnerů v privátní podsítě:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Nyní nastavte `CONTAINER_GROUP_IP` na IP adresu, který jste získali s `az container show` příkazů a spuštěním následujících `az container create` příkazu. Tento druhý kontejner *commchecker*, spustí image založené na Alpine Linuxu a spustí `wget` před první skupinu kontejnerů privátní podsítě IP adres.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Po dokončení této druhé nasazení kontejneru vyžádat protokoly, zobrazí se výstup `wget` příkaz provede:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Pokud se druhý kontejner adrese úspěšně s první, by měl být podobný výstup:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Výstup protokolu, který by měl zobrazit `wget` se může připojit a stáhnout soubor indexu z první kontejner pomocí jeho privátní IP adresa v místní podsíti. Síťový provoz mezi dvěma kontejneru skupiny zůstala v rámci virtuální sítě.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Nasazení do existující virtuální sítě – YAML

Skupiny kontejnerů do existující virtuální sítě můžete nasadit také pomocí souboru YAML. Pokud chcete nasadit do podsítě ve virtuální síti, zadáte několik dalších vlastností YAML:

* `ipAddress`: Nastavení IP adresy pro skupinu kontejnerů.
  * `ports`: Porty, které chcete spustit, pokud existuje.
  * `protocol`: Protokol (TCP nebo UDP) pro je otevřený port.
* `networkProfile`: Určuje nastavení sítě, jako jsou virtuální síť a podsíť pro prostředek Azure.
  * `id`: Úplné ID prostředku Resource Manageru z `networkProfile`.

Pokud chcete nasadit skupinu kontejnerů do virtuální sítě pomocí souboru YAML, musíte nejprve získat ID profilu sítě. Spustit [přehled profilu sítě az] [ az-network-profile-list] příkazu, zadáním názvu skupiny prostředků, která obsahuje virtuální síť a podsíť delegovaný.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Výstup příkazu se zobrazí úplné ID prostředku pro profil sítě:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Jakmile budete mít síť ID profilu, zkopírujte následující kód YAML do nového souboru s názvem *virtuální sítě nasadit aci.yaml*. V části `networkProfile`, nahraďte `id` hodnotu s ID, které jste právě načteny, pak soubor uložte. Tato YAML vytvoří skupinu kontejnerů *appcontaineryaml* ve vaší virtuální síti.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: microsoft/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Nasadit skupinu kontejnerů s [az container vytvořit] [ az-container-create] příkaz a určete název souboru YAML `--file` parametr:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Po dokončení nasazení spusťte [az container show] [ az-container-show] příkazu můžete zobrazit její stav:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                     IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   microsoft/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="delete-container-instances"></a>Odstranit instance kontejnerů

Po dokončení práce se službou container instances jste vytvořili, je odstranit pomocí následujících příkazů:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Odstranit síťové prostředky

Počáteční verzi preview tato funkce vyžaduje několik dalších příkazů odstraňte prostředky sítě, že kterou jste vytvořili dříve. Pokud jste použili příklady příkazů v předchozích částech tohoto článku vytvořte virtuální síť a podsíť, můžete použít následující skript k odstranění těchto síťových prostředků.

Před spuštěním skriptu, nastavte `RES_GROUP` proměnnou pro název skupiny prostředků obsahující virtuální síť a podsíť, která měla by být odstraněna. Skript formátována pro prostředí Bash. Pokud chcete jiné prostředí, jako je PowerShell nebo příkazového řádku, budete muset odpovídajícím způsobem upravit přiřazení proměnné a přístupové objekty.

> [!WARNING]
> Tento skript se odstraní prostředky! Odstraní virtuální sítě i všechny podsítě, které obsahuje. Ujistěte se, že už nepotřebujete *jakékoli* prostředků ve virtuální síti, včetně podsítí obsahuje, před spuštěním tohoto skriptu. Po odstranění **tyto prostředky neopravitelné**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Další postup

Pokud chcete nasadit nové virtuální sítě, podsítě, profil sítě a skupinu kontejnerů pomocí šablony Resource Manageru, najdete v článku [vytvořit novou skupinu kontejnerů Azure s virtuální sítí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Několik prostředky virtuální sítě a funkce byly popisovaných v tomto článku, i když stručně. Dokumentace ke službě Azure Virtual Network výrazně obsahuje tato témata:

* [Virtuální síť](../virtual-network/manage-virtual-network.md)
* [Podsíť](../virtual-network/virtual-network-manage-subnet.md)
* [Koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
