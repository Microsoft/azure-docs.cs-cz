---
title: Nasazení skupiny kontejnerů do služby Azure Virtual Network
description: Naučte se nasazovat skupiny kontejnerů do nové nebo existující virtuální sítě Azure.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 920ad9598f17fbab25218827045a396d953a6531
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845178"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Nasazení instancí kontejnerů do služby Azure Virtual Network

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečenou privátní síť pro vaše Azure a místní prostředky. Když nasadíte skupiny kontejnerů do služby Azure Virtual Network, můžou vaše kontejnery bezpečně komunikovat s ostatními prostředky ve virtuální síti.

Skupiny kontejnerů nasazené do služby Azure Virtual Network povolují scénáře, jako jsou:

* Přímá komunikace mezi skupinami kontejnerů ve stejné podsíti
* Odeslání výstupu [úloh na základě úlohy](container-instances-restart-policy.md) z instancí kontejneru do databáze ve virtuální síti
* Načte obsah pro instance kontejnerů z [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) ve virtuální síti.
* Komunikace kontejneru s virtuálními počítači ve virtuální síti
* Komunikace kontejneru s místními prostředky prostřednictvím [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Nasazení skupiny kontejnerů do virtuální sítě je všeobecně dostupné pro produkční úlohy pouze v následujících oblastech: **východní USA, střed USA – jih a západní USA 2**. V jiných oblastech, kde je funkce dostupná, jsou nasazení virtuálních sítí v současnosti ve verzi Preview, přičemž v blízké budoucnosti je plánována Obecná dostupnost. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Omezení nasazení virtuální sítě

Určitá omezení platí při nasazení skupin kontejnerů do virtuální sítě.

* Pro nasazení skupin kontejnerů do podsítě nemůže podsíť obsahovat žádné další typy prostředků. Před nasazením skupin kontejnerů do něj odeberte všechny stávající prostředky ze stávající podsítě, nebo vytvořte novou podsíť.
* [Spravovanou identitu](container-instances-managed-identity.md) nemůžete použít ve skupině kontejnerů nasazené do virtuální sítě.
* V rámci skupiny kontejnerů nasazených do virtuální sítě nemůžete povolit [test živého provozu](container-instances-liveness-probe.md) nebo test [připravenosti](container-instances-readiness-probe.md) .
* Kvůli dalším zapojení síťových prostředků je nasazení skupiny kontejnerů na virtuální síť obvykle pomalejší než nasazení standardní instance kontejneru.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Omezení prostředků kontejneru se mohou lišit od omezení pro instance kontejnerů, které nejsou v síti v těchto oblastech. Pro tuto funkci jsou aktuálně podporovány pouze kontejnery Linux. Podpora Windows je plánována.

### <a name="unsupported-networking-scenarios"></a>Nepodporované scénáře sítě 

* **Azure Load Balancer** – umístění Azure Load Balancer před instancemi kontejnerů v síťové skupině kontejnerů se nepodporuje.
* **Partnerské vztahy virtuálních sítí**
  * Partnerský vztah virtuálních sítí nebude fungovat pro ACI, pokud síť, kterou virtuální síť ACI využívá partnerský vztah k používání veřejného prostoru IP adres. Aby partnerský vztah virtuálních sítí fungoval, potřebuje síť s privátním IP adresou RFC 1918. 
  * Síť VNet se dá vytvořit jenom pro jednu jinou virtuální síť.
* **Směrování provozu virtuální sítě** – vlastní trasy nejde nastavit kolem veřejných IP adres. Trasy je možné nastavit v privátním IP prostoru delegované podsítě, ve které jsou nasazené prostředky ACI. 
* **Skupiny zabezpečení sítě** – odchozí pravidla zabezpečení v skupin zabezpečení sítě použitá pro podsíť delegovaná na Azure Container Instances se momentálně neuplatňují. 
* **Veřejná IP adresa nebo popisek DNS** – skupiny kontejnerů nasazené ve virtuální síti aktuálně nepodporují vystavování kontejnerů přímo na internetu s použitím veřejné IP adresy nebo plně kvalifikovaného názvu domény.
* **Interní překlad adres IP** – překlad názvů pro prostředky Azure ve virtuální síti prostřednictvím interního Azure DNS není podporovaný.

Po nasazení skupin kontejnerů do virtuální sítě musí **Odstranění síťového prostředku** vyžadovat [Další kroky](#delete-network-resources) .

## <a name="required-network-resources"></a>Požadované síťové prostředky

Existují tři prostředky Azure Virtual Network, které jsou potřeba k nasazení skupin kontejnerů do virtuální sítě: vlastní [virtuální síť](#virtual-network) , [delegovaná podsíť](#subnet-delegated) v rámci virtuální sítě a [profil sítě](#network-profile). 

### <a name="virtual-network"></a>Virtuální síť

Virtuální síť definuje adresní prostor, ve kterém vytvoříte jednu nebo více podsítí. Potom do podsítí ve vaší virtuální síti nasadíte prostředky Azure (například skupiny kontejnerů).

### <a name="subnet-delegated"></a>Podsíť (delegovaný)

Podsítě virtuální sítě rozdělit do samostatné adresní prostory použitelné Azure prostředky, které umístíte do nich. Vytvoříte jednu nebo několik podsítí v rámci virtuální sítě.

Podsíť, kterou použijete pro skupiny kontejnerů, může obsahovat pouze skupiny kontejnerů. Když nasadíte skupinu kontejnerů do podsítě poprvé, Azure deleguje tuto podsíť Azure Container Instances. Po delegování se podsíť dá použít jenom pro skupiny kontejnerů. Pokud se pokusíte nasadit jiné prostředky než skupiny kontejnerů na delegovanou podsíť, operace se nezdaří.

### <a name="network-profile"></a>Profil sítě

Profil sítě je šablona konfigurace sítě pro prostředky Azure. Určuje určité vlastnosti sítě pro prostředek, například podsíť, do které se má nasadit. Při prvním použití příkazu [AZ Container Create][az-container-create] k nasazení skupiny kontejnerů do podsítě (a tedy virtuální sítě) vytvoří Azure profil sítě za vás. Pak můžete tento profil sítě použít pro budoucí nasazení do podsítě. 

Chcete-li použít šablonu Správce prostředků, soubor YAML nebo programovou metodu pro nasazení skupiny kontejnerů do podsítě, je nutné zadat úplné ID Správce prostředků prostředku profilu sítě. Můžete použít profil, který jste vytvořili dříve pomocí [AZ Container Create][az-container-create], nebo vytvořit profil pomocí šablony Správce prostředků (viz [příklad šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) a [reference](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Pokud chcete získat ID dříve vytvořeného profilu, použijte příkaz [AZ Network Profile list][az-network-profile-list] . 

V následujícím diagramu byly nasazeny některé skupiny kontejnerů do podsítě delegované do Azure Container Instances. Po nasazení jedné skupiny kontejnerů do podsítě můžete do ní nasadit další skupiny kontejnerů zadáním stejného profilu sítě.

![Skupiny kontejnerů v rámci virtuální sítě][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scénáře nasazení

Pomocí [AZ Container Create][az-container-create] můžete nasazovat skupiny kontejnerů do nové virtuální sítě a dovolit Azure vytvořit požadované síťové prostředky pro vás nebo nasadit do existující virtuální sítě. 

### <a name="new-virtual-network"></a>Nová virtuální síť

Pokud chcete provést nasazení do nové virtuální sítě a nechat Azure vytvořit síťové prostředky automaticky, zadejte při spuštění [AZ Container Create][az-container-create]:

* Název virtuální sítě
* Předpona adresy virtuální sítě ve formátu CIDR
* Název podsítě
* Předpona adresy podsítě ve formátu CIDR

Předpony adresy virtuální sítě a podsítě určují adresní prostory pro virtuální síť a podsíť v uvedeném pořadí. Tyto hodnoty jsou reprezentovány v zápisu CIDR (Classless Inter-Domain Routing), například `10.0.0.0/16`. Další informace o práci s podsítěmi najdete v tématu [Přidání, změna nebo odstranění podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

Po nasazení první skupiny kontejnerů pomocí této metody můžete nasadit do stejné podsítě zadáním virtuální sítě a názvů podsítí nebo síťového profilu, který vám Azure pro vás automaticky vytvoří. Vzhledem k tomu, že Azure deleguje podsíť k Azure Container Instances, můžete do podsítě nasadit *jenom* skupiny kontejnerů.

### <a name="existing-virtual-network"></a>Existující virtuální síť

Nasazení skupiny kontejnerů do existující virtuální sítě:

1. Vytvořte podsíť v rámci stávající virtuální sítě, použijte existující podsíť, ve které je skupina kontejnerů už nasazená, nebo použijte existující podsíť, která je vyprázdná u *všech* ostatních prostředků.
1. Nasaďte skupinu kontejnerů pomocí [AZ Container Create][az-container-create] a zadejte jednu z následujících možností:
   * Název virtuální sítě a název podsítě
   * ID prostředku virtuální sítě a ID prostředku podsítě, které umožňuje používat virtuální síť z jiné skupiny prostředků.
   * Název nebo ID síťového profilu, které můžete získat pomocí [seznamu AZ Network Profile list][az-network-profile-list]

Jakmile nasadíte svou první skupinu kontejnerů do existující podsítě, Azure deleguje tuto podsíť Azure Container Instances. Do této podsítě už nemůžete nasazovat jiné prostředky než skupiny kontejnerů.

## <a name="deployment-examples"></a>Příklady nasazení

Následující části popisují, jak nasadit skupiny kontejnerů do virtuální sítě pomocí Azure CLI. Příklady příkazů jsou formátovány pro prostředí **bash** Shell. Pokud dáváte přednost jinému prostředí, například PowerShellu nebo příkazovému řádku, upravte odpovídajícím způsobem řádky pro pokračování řádku.

### <a name="deploy-to-a-new-virtual-network"></a>Nasadit do nové virtuální sítě

Nejdřív nasaďte skupinu kontejnerů a zadejte parametry pro novou virtuální síť a podsíť. Při zadání těchto parametrů Azure vytvoří virtuální síť a podsíť, deleguje podsíť do služby Azure Container Instances a vytvoří taky profil sítě. Po vytvoření těchto prostředků se vaše skupina kontejnerů nasadí do podsítě.

Spusťte následující příkaz [AZ Container Create][az-container-create] , který určuje nastavení pro novou virtuální síť a podsíť. Je potřeba, abyste zadali název skupiny prostředků, která byla vytvořená v oblasti, ve které jsou [dostupná](#virtual-network-deployment-limitations)nasazení skupin kontejnerů ve virtuální síti. Tento příkaz nasadí veřejný kontejner Microsoft [ACI-HelloWorld][aci-helloworld] , který spouští malý webový server Node. js obsluhující statickou webovou stránku. V další části nasadíte druhou skupinu kontejnerů do stejné podsítě a otestujete komunikaci mezi dvěma instancemi kontejneru.

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

Při nasazení do nové virtuální sítě pomocí této metody může nasazení během vytváření síťových prostředků trvat několik minut. Po počátečním nasazení se další nasazení skupin kontejnerů dokončí rychleji.

### <a name="deploy-to-existing-virtual-network"></a>Nasadit do existující virtuální sítě

Teď, když jste nasadili skupinu kontejnerů do nové virtuální sítě, nasadíte druhou skupinu kontejnerů do stejné podsítě a ověříte komunikaci mezi těmito dvěma instancemi kontejnerů.

Nejdřív Získejte IP adresu první skupiny kontejnerů, kterou jste nasadili, a *kontejneru AppContainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Výstup by měl zobrazovat IP adresu skupiny kontejnerů v privátní podsíti:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Nyní nastavte `CONTAINER_GROUP_IP` na IP adresu, kterou jste načetli pomocí příkazu `az container show` a spusťte následující `az container create` příkaz. Tento druhý kontejner *commchecker*spustí bitovou kopii systému Alpine Linux a provede `wget` proti první IP adrese privátní podsítě pro danou skupinu kontejnerů.

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

Po dokončení tohoto druhého nasazení kontejneru si vyžádejte své protokoly, abyste viděli výstup příkazu `wget`, který jste provedli:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Pokud druhý kontejner úspěšně komunikoval s prvním, výstup by měl vypadat přibližně takto:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Výstup protokolu by měl ukázat, že `wget` se dokázal připojit a stáhnout soubor indexu z prvního kontejneru pomocí jeho privátní IP adresy v místní podsíti. Síťový provoz mezi oběma skupinami kontejnerů zůstal v rámci virtuální sítě.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Nasazení do existující virtuální sítě – YAML

Můžete také nasadit skupinu kontejnerů do existující virtuální sítě pomocí souboru YAML, šablony Správce prostředků nebo jiné programové metody, jako je sada Python SDK. K nasazení do podsítě ve virtuální síti zadáte několik dalších vlastností v YAML:

* `ipAddress`: nastavení IP adresy pro skupinu kontejnerů.
  * `ports`: porty, které se mají otevřít (pokud nějaké jsou).
  * `protocol`: protokol (TCP nebo UDP) pro otevřený port.
* `networkProfile`: Určuje nastavení sítě, jako je virtuální síť a podsíť pro prostředek Azure.
  * `id`: úplné ID prostředku Správce prostředků `networkProfile`.

Pokud chcete nasadit skupinu kontejnerů do virtuální sítě se souborem YAML, musíte nejdřív získat ID profilu sítě. Spusťte příkaz [AZ Network Profile list][az-network-profile-list] a určete název skupiny prostředků, která obsahuje vaši virtuální síť a delegovanou podsíť.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Výstup příkazu zobrazí úplné ID prostředku pro síťový profil:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Jakmile budete mít ID profilu sítě, zkopírujte následující YAML do nového souboru s názvem *VNet-Deploy-ACI. yaml*. V části `networkProfile`nahraďte hodnotu `id` hodnotou ID, kterou jste právě načetli, a pak soubor uložte. Tento YAML vytvoří ve vaší virtuální síti skupinu kontejnerů s názvem *appcontaineryaml* .

```YAML
apiVersion: '2018-09-01'
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

Nasaďte skupinu kontejnerů pomocí příkazu [AZ Container Create][az-container-create] a zadáním názvu souboru YAML pro parametr `--file`:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Po dokončení nasazení spusťte příkaz [AZ Container show][az-container-show] , který zobrazí jeho stav:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
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


> [!NOTE]
> Pokud při pokusu o odebrání profilu sítě dojde k chybě, povolte 3-4 dní, aby platforma automaticky zmírnila problém a znovu se pokusila o odstranění. Pokud potřebujete profil sítě odstranit okamžitě, [otevřete žádost o podporu](https://azure.microsoft.com/support/create-ticket/) odkazující na službu Azure Container Instances.

Tato funkce v současnosti vyžaduje několik dalších příkazů k odstranění síťových prostředků, které jste vytvořili dříve. Pokud jste použili ukázkové příkazy v předchozích částech tohoto článku k vytvoření virtuální sítě a podsítě, můžete k odstranění těchto síťových prostředků použít následující skript.

Před spuštěním skriptu nastavte `RES_GROUP` proměnnou na název skupiny prostředků obsahující virtuální síť a podsíť, která se má odstranit. Aktualizujte název virtuální sítě, pokud jste nepoužívali dříve navržený název `aci-vnet`. Skript je naformátován pro prostředí bash shell. Pokud dáváte přednost jinému prostředí, například PowerShellu nebo příkazovému řádku, budete muset odpovídajícím způsobem upravit proměnnou přiřazení a přistupující objekty.

> [!WARNING]
> Tento skript odstraní prostředky. Odstraní virtuální síť a všechny podsítě, které obsahuje. Ujistěte se, že už nepotřebujete *žádné* prostředky ve virtuální síti, včetně všech podsítí, které obsahuje, před spuštěním tohoto skriptu. Po odstranění se **tyto prostředky neobnoví**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Další kroky

Postup nasazení nové virtuální sítě, podsítě, profilu sítě a skupiny kontejnerů pomocí šablony Správce prostředků najdete v tématu [Vytvoření skupiny kontejnerů Azure s virtuální](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)sítí.

V tomto článku jsme popisovali několik prostředků virtuální sítě a funkcí, ale krátce. Dokumentace k Azure Virtual Network zahrnuje i rozsáhlá témata:

* [Virtuální síť](../virtual-network/manage-virtual-network.md)
* [Podsíť](../virtual-network/virtual-network-manage-subnet.md)
* [Koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
