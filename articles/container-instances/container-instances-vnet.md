---
title: Nasazení skupiny kontejnerů do služby Azure Virtual Network
description: Naučte se, jak nasadit skupinu kontejnerů do nové nebo existující virtuální sítě Azure pomocí rozhraní příkazového řádku Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js
ms.openlocfilehash: f8f61bc74f79c1712c3c662be66384c5ef689eb7
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518122"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Nasazení instancí kontejnerů do virtuální sítě Azure

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečenou privátní síť pro vaše Azure a místní prostředky. Když nasadíte skupiny kontejnerů do služby Azure Virtual Network, můžou vaše kontejnery bezpečně komunikovat s ostatními prostředky ve virtuální síti.

V tomto článku se dozvíte, jak pomocí příkazu [AZ Container Create][az-container-create] v Azure CLI nasadit skupiny kontejnerů do nové virtuální sítě nebo existující virtuální sítě. 

Informace o scénářích a omezeních sítě najdete v tématu [scénáře a prostředky virtuální sítě pro Azure Container Instances](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> Nasazení skupiny kontejnerů do virtuální sítě je všeobecně dostupné pro kontejnery Linux ve většině oblastí, kde je Azure Container Instances k dispozici. Podrobnosti najdete v tématu věnovaném [oblastem a dostupnosti prostředků](container-instances-virtual-network-concepts.md#where-to-deploy). 

Příklady v tomto článku jsou formátované pro prostředí bash shell. Pokud dáváte přednost jinému prostředí, například PowerShellu nebo příkazovému řádku, upravte odpovídajícím způsobem řádky pro pokračování řádku.


## <a name="deploy-to-new-virtual-network"></a>Nasadit do nové virtuální sítě

Pokud chcete provést nasazení do nové virtuální sítě a nechat Azure vytvořit síťové prostředky automaticky, zadejte při spuštění [AZ Container Create][az-container-create]:

* Název virtuální sítě
* Předpona adresy virtuální sítě ve formátu CIDR
* Název podsítě
* Předpona adresy podsítě ve formátu CIDR

Předpony adresy virtuální sítě a podsítě určují adresní prostory pro virtuální síť a podsíť v uvedeném pořadí. Tyto hodnoty jsou reprezentovány v zápisu CIDR (Classless Inter-Domain Routing), například `10.0.0.0/16` . Další informace o práci s podsítěmi najdete v tématu [Přidání, změna nebo odstranění podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

Po nasazení první skupiny kontejnerů pomocí této metody můžete nasadit do stejné podsítě zadáním virtuální sítě a názvů podsítí nebo síťového profilu, který vám Azure pro vás automaticky vytvoří. Vzhledem k tomu, že Azure deleguje podsíť k Azure Container Instances, můžete do podsítě nasadit *jenom* skupiny kontejnerů.

### <a name="example"></a>Příklad

Následující příkaz [AZ Container Create][az-container-create] určí nastavení pro novou virtuální síť a podsíť. Zadejte název skupiny prostředků, která byla vytvořena v oblasti, kde jsou [k dispozici](container-instances-region-availability.md)skupiny kontejnerů nasazení ve virtuální síti. Tento příkaz nasadí veřejný kontejner Microsoft [ACI-HelloWorld][aci-helloworld] , který spouští malý Node.js webový server obsluhující statickou webovou stránku. V další části nasadíte druhou skupinu kontejnerů do stejné podsítě a otestujete komunikaci mezi dvěma instancemi kontejneru.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

Při nasazení do nové virtuální sítě pomocí této metody může nasazení během vytváření síťových prostředků trvat několik minut. Po počátečním nasazení se další skupiny kontejnerů nasazování do stejné podsítě dokončí rychleji.

## <a name="deploy-to-existing-virtual-network"></a>Nasadit do existující virtuální sítě

Nasazení skupiny kontejnerů do existující virtuální sítě:

1. Vytvořte podsíť v rámci stávající virtuální sítě, použijte existující podsíť, ve které je skupina kontejnerů už nasazená, nebo použijte existující podsíť, která je vyprázdná u *všech* ostatních prostředků.
1. Nasaďte skupinu kontejnerů pomocí [AZ Container Create][az-container-create] a zadejte jednu z následujících možností:
   * Název virtuální sítě a název podsítě
   * ID prostředku virtuální sítě a ID prostředku podsítě, které umožňuje používat virtuální síť z jiné skupiny prostředků.
   * Název nebo ID síťového profilu, které můžete získat pomocí [seznamu AZ Network Profile list][az-network-profile-list]

### <a name="example"></a>Příklad

Následující příklad nasadí druhou skupinu kontejnerů do stejné podsítě vytvořené dříve a ověří komunikaci mezi dvěma instancemi kontejneru.

Nejdřív Získejte IP adresu první skupiny kontejnerů, kterou jste nasadili, a *kontejneru AppContainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

Výstup zobrazí IP adresu skupiny kontejnerů v privátní podsíti. Například:

```console
10.0.0.4
```

Nyní nastavte `CONTAINER_GROUP_IP` na IP adresu, kterou jste načetli pomocí `az container show` příkazu, a spusťte následující `az container create` příkaz. Tento druhý kontejner *commchecker*spustí bitovou kopii systému Alpine Linux a provede se s `wget` první IP adresou privátní podsítě pro skupinu kontejnerů.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Po dokončení tohoto druhého nasazení kontejneru načtěte protokoly, abyste viděli výstup `wget` příkazu, který jste provedli:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Pokud druhý kontejner úspěšně komunikoval s prvním, výstupem je podobný:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Výstup protokolu by měl ukázat, že se mohl `wget` připojit a stáhnout soubor indexu z prvního kontejneru pomocí jeho privátní IP adresy v místní podsíti. Síťový provoz mezi oběma skupinami kontejnerů zůstal v rámci virtuální sítě.

### <a name="example---yaml"></a>Příklad – YAML

Můžete také nasadit skupinu kontejnerů do existující virtuální sítě pomocí souboru YAML, [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)nebo jiné programové metody, jako je sada Python SDK. 

Například při použití souboru YAML můžete nasadit do virtuální sítě s podsítí delegovanou na Azure Container Instances. Zadejte následující vlastnosti:

* `ipAddress`: Nastavení privátních IP adres pro skupinu kontejnerů.
  * `ports`: Porty, které se mají otevřít.
  * `protocol`: Protokol (TCP nebo UDP) pro otevřený port.
* `networkProfile`: Nastavení sítě pro virtuální síť a podsíť.
  * `id`: Úplné ID Správce prostředků prostředku `networkProfile` .

Pokud chcete získat ID profilu sítě, spusťte příkaz [AZ Network Profile list][az-network-profile-list] , který určí název skupiny prostředků, která obsahuje vaši virtuální síť a delegovanou podsíť.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Ukázkový výstup:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Jakmile budete mít ID profilu sítě, zkopírujte následující YAML do nového souboru s názvem *VNet-Deploy-ACI. yaml*. V části `networkProfile` nahraďte `id` hodnotu hodnotou ID, kterou jste právě načetli, a pak soubor uložte. Tento YAML vytvoří ve vaší virtuální síti skupinu kontejnerů s názvem *appcontaineryaml* .

```YAML
apiVersion: '2019-12-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Nasaďte skupinu kontejnerů pomocí příkazu [AZ Container Create][az-container-create] a zadáním názvu souboru YAML pro `--file` parametr:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Po dokončení nasazení spusťte příkaz [AZ Container show][az-container-show] , který zobrazí jeho stav. Ukázkový výstup:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="delete-container-instances"></a>Odstranit instance kontejnerů

Až budete pracovat s instancemi kontejnerů, které jste vytvořili, odstraňte je pomocí následujících příkazů:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Odstranění síťových prostředků

Tato funkce v současnosti vyžaduje několik dalších příkazů k odstranění síťových prostředků, které jste vytvořili dříve. Pokud jste použili ukázkové příkazy v předchozích částech tohoto článku k vytvoření virtuální sítě a podsítě, můžete k odstranění těchto síťových prostředků použít následující skript. Skript předpokládá, že vaše skupina prostředků obsahuje jednu virtuální síť s jedním síťovým profilem.

Před spuštěním skriptu nastavte `RES_GROUP` proměnnou na název skupiny prostředků obsahující virtuální síť a podsíť, kterou chcete odstranit. Pokud jste nepoužili dříve navržený název, aktualizujte název virtuální sítě `aci-vnet` . Skript je naformátován pro prostředí bash shell. Pokud dáváte přednost jinému prostředí, například PowerShellu nebo příkazovému řádku, budete muset odpovídajícím způsobem upravit proměnnou přiřazení a přistupující objekty.

> [!WARNING]
> Tento skript odstraní prostředky. Odstraní virtuální síť a všechny podsítě, které obsahuje. Ujistěte se, že už nepotřebujete *žádné* prostředky ve virtuální síti, včetně všech podsítí, které obsahuje, před spuštěním tohoto skriptu. Po odstranění se **tyto prostředky neobnoví**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Další kroky

Postup nasazení nové virtuální sítě, podsítě, profilu sítě a skupiny kontejnerů pomocí šablony Správce prostředků najdete v tématu [Vytvoření skupiny kontejnerů Azure s virtuální](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)sítí.

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
