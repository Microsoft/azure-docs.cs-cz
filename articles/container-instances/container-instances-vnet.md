---
title: Nasazení skupiny kontejnerů do virtuální sítě Azure
description: Zjistěte, jak nasadit skupiny kontejnerů do nové nebo existující virtuální sítě Azure.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 318576e9b5c5b32bbc993ea16494c938b74bd2f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200057"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Nasazení instancí kontejnerů do virtuální sítě Azure

[Virtuální síť Azure](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečené privátní sítě pro vaše azure a místní prostředky. Nasazením skupin kontejnerů do virtuální sítě Azure můžou vaše kontejnery bezpečně komunikovat s jinými prostředky ve virtuální síti.

Skupiny kontejnerů nasazené do virtuální sítě Azure umožňují scénáře, jako jsou:

* Přímá komunikace mezi skupinami kontejnerů ve stejné podsíti
* Odeslání výstupu [úlohy založené na úlohách založených na úlohách](container-instances-restart-policy.md) z instancí kontejneru do databáze ve virtuální síti
* Načtení obsahu pro instance kontejneru z [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) ve virtuální síti
* Kontejnerová komunikace s virtuálními počítači ve virtuální síti
* Kontejnerová komunikace s místními prostředky prostřednictvím [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Nasazení skupinkontejnerů do virtuální sítě jsou obecně dostupná pro produkční úlohy pouze v následujících oblastech: **VÝCHODNÍ USA, Usa – střed usa – jih a USA – západ 2**. V jiných oblastech, kde je tato funkce k dispozici, jsou nasazení virtuálních sítí aktuálně ve verzi Preview, přičemž obecná dostupnost je plánována v blízké budoucnosti. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Omezení nasazení virtuální sítě

Určitá omezení platí při nasazení skupin kontejnerů do virtuální sítě.

* Chcete-li nasadit skupiny kontejnerů do podsítě, podsíť nemůže obsahovat žádné jiné typy prostředků. Odeberte všechny existující prostředky z existující podsítě před nasazením skupin kontejnerů do ní nebo vytvořte novou podsíť.
* [Spravovanou identitu](container-instances-managed-identity.md) nelze použít ve skupině kontejnerů nasazené do virtuální sítě.
* Nelze povolit [sondu liveness](container-instances-liveness-probe.md) nebo [sondu připravenosti](container-instances-readiness-probe.md) ve skupině kontejnerů nasazené do virtuální sítě.
* Vzhledem k další síťové prostředky, které jsou zapojeny, nasazení skupiny kontejnerů do virtuální sítě je obvykle pomalejší než nasazení instance standardní kontejner.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Omezení prostředků kontejneru se může lišit od omezení pro instance kontejneru bez sítě v těchto oblastech. V současné době jsou pro tuto funkci podporovány pouze kontejnery Linux. Je plánována podpora systému Windows.

### <a name="unsupported-networking-scenarios"></a>Scénáře nepodporovaných sítí 

* **Azure Load Balancer** – umístění Azure Balancer před instance kontejneru ve skupině síťových kontejnerů není podporována
* **Partnerský vztah virtuální sítě**
  * Partnerský vztah virtuální sítě nebude fungovat pro ACI, pokud síť, ke které virtuální síť ACI používá veřejný prostor IP. Partnerská síť potřebuje privátní IP prostor RFC 1918, aby partnerský vztah virtuální sítě fungoval. 
  * Můžete jenom peer your Virtuální síť na jednu jinou virtuální síť
* **Směrování provozu virtuální sítě** – vlastní trasy nelze nastavit kolem veřejných IP adres. Trasy lze nastavit v privátním prostoru IP delegované podsítě, ve které jsou nasazeny prostředky ACI. 
* **Skupiny zabezpečení sítě** – odchozí pravidla zabezpečení v skupinách nsg aplegované na podsíť delegované na instance kontejnerů Azure nejsou aktuálně vynucena 
* **Veřejný popisek IP nebo DNS** – skupiny kontejnerů nasazené do virtuální sítě momentálně nepodporují vystavení kontejnerů přímo na internet s veřejnou IP adresou nebo plně kvalifikovaným názvem domény.
* **Překlad interního názvu** – překlad názvů pro prostředky Azure ve virtuální síti prostřednictvím interního Azure DNS není podporovaný.

**Odstranění síťových prostředků** vyžaduje [další kroky](#delete-network-resources) po nasazení skupin kontejnerů do virtuální sítě.

## <a name="required-network-resources"></a>Požadované síťové prostředky

Existují tři prostředky virtuální sítě Azure potřebné pro nasazení skupin kontejnerů do virtuální sítě: [samotná virtuální síť,](#virtual-network) [delegovaná podsíť](#subnet-delegated) v rámci virtuální sítě a [profil sítě](#network-profile). 

### <a name="virtual-network"></a>Virtuální síť

Virtuální síť definuje adresní prostor, ve kterém vytvoříte jednu nebo více podsítí. Potom nasadíte prostředky Azure (jako skupiny kontejnerů) do podsítí ve vaší virtuální síti.

### <a name="subnet-delegated"></a>Podsíť (delegovaná)

Podsítě segmentvirtuální síť do samostatných adresprostorů použitelných prostředky Azure, které umístíte v nich. Vytvoření jedné nebo několika podsítí v rámci virtuální sítě.

Podsíť, kterou používáte pro skupiny kontejnerů, může obsahovat pouze skupiny kontejnerů. Při prvním nasazení skupiny kontejnerů do podsítě Azure deleguje tuto podsíť na instance kontejneru Azure. Po delegování lze podsíť použít pouze pro skupiny kontejnerů. Pokud se pokusíte nasadit jiné prostředky než skupiny kontejnerů do delegované podsítě, operace se nezdaří.

### <a name="network-profile"></a>Profil sítě

Profil sítě je šablona konfigurace sítě pro prostředky Azure. Určuje určité vlastnosti sítě pro prostředek, například podsíť, do které by měl být nasazen. Při prvním použití příkazu [az kontejner vytvořit][az-container-create] k nasazení skupiny kontejnerů do podsítě (a tedy virtuální sítě), Azure vytvoří síťový profil pro vás. Tento profil sítě pak můžete použít pro budoucí nasazení do podsítě. 

Chcete-li k nasazení skupiny kontejnerů do podsítě použít šablonu Správce prostředků, soubor YAML nebo programovou metodu, je třeba zadat úplné ID prostředků Správce prostředků síťového profilu. Můžete použít profil dříve vytvořený pomocí [vytvoření kontejneru az][az-container-create]nebo vytvořit profil pomocí šablony Správce prostředků (viz [příklad šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) a [odkaz).](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles) Chcete-li získat ID dříve vytvořeného profilu, použijte příkaz [seznamu profilů sítě az.][az-network-profile-list] 

V následujícím diagramu několik skupin kontejnerů byly nasazeny do podsítě delegované na Azure Container Instances. Po nasazení jedné skupiny kontejnerů do podsítě do ní můžete nasadit další skupiny kontejnerů zadáním stejného síťového profilu.

![Skupiny kontejnerů v rámci virtuální sítě][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scénáře nasazení

Pomocí [vytvoření kontejneru az][az-container-create] můžete nasadit skupiny kontejnerů do nové virtuální sítě a povolit Azure vytvářet požadované síťové prostředky pro vás nebo nasadit do existující virtuální sítě. 

### <a name="new-virtual-network"></a>Nová virtuální síť

Chcete-li nasadit do nové virtuální sítě a nechat Azure automaticky vytvořit síťové prostředky, zadejte následující při spuštění [vytvoření kontejneru AZ][az-container-create]:

* Název virtuální sítě
* Předpona adresy virtuální sítě ve formátu CIDR
* Název podsítě
* Předpona adresy podsítě ve formátu CIDR

Předpony adresy virtuální sítě a podsítě určují adresní prostory pro virtuální síť a podsíť. Tyto hodnoty jsou reprezentovány v zápisu classless Inter-Domain `10.0.0.0/16`Routing (CIDR), například . Další informace o práci s podsítěmi najdete v tématu [Přidání, změna nebo odstranění podsítě virtuální sítě](../virtual-network/virtual-network-manage-subnet.md).

Po nasazení první skupiny kontejnerů pomocí této metody můžete nasadit do stejné podsítě zadáním názvů virtuální sítě a podsítě nebo síťového profilu, který pro vás Azure automaticky vytvoří. Protože Azure deleguje podsíť na instance kontejnerů Azure, můžete do podsítě nasadit *jenom* skupiny kontejnerů.

### <a name="existing-virtual-network"></a>Existující virtuální síť

Nasazení skupiny kontejnerů do existující virtuální sítě:

1. Vytvořte podsíť v rámci existující virtuální sítě, použijte existující podsíť, ve které je skupina kontejnerů již nasazená, nebo použijte existující podsíť vyprázdněnou *ze všech* ostatních prostředků.
1. Nasaďte skupinu kontejnerů s [vytvořením kontejneru a][az-container-create] zadejte jednu z následujících možností:
   * Název virtuální sítě a podsítě
   * ID prostředku virtuální sítě a ID prostředku podsítě, které umožňuje použití virtuální sítě z jiné skupiny prostředků
   * Název nebo ID síťového profilu, které můžete získat pomocí [seznamu profilů sítě AZ][az-network-profile-list]

Jakmile nasadíte první skupinu kontejnerů do existující podsítě, Azure deleguje tuto podsíť na instance kontejneru Azure. Do této podsítě již nelze nasadit jiné prostředky než skupiny kontejnerů.

## <a name="deployment-examples"></a>Příklady nasazení

Následující části popisují, jak nasadit skupiny kontejnerů do virtuální sítě pomocí azure CLI. Příklady příkazů jsou formátovány pro prostředí **Bash.** Pokud dáváte přednost jinému prostředí, například prostředí PowerShell nebo příkazovém řádku, upravte odpovídajícím způsobem znaky pokračování řádku.

### <a name="deploy-to-a-new-virtual-network"></a>Nasazení do nové virtuální sítě

Nejprve nasadit skupinu kontejnerů a zadat parametry pro novou virtuální síť a podsíť. Když zadáte tyto parametry, Azure vytvoří virtuální síť a podsíť, deleguje podsíť na instance Azure Container a také vytvoří síťový profil. Po vytvoření těchto prostředků se skupina kontejnerů nasadí do podsítě.

Spusťte následující příkaz [az kontejner vytvořit,][az-container-create] který určuje nastavení pro novou virtuální síť a podsíť. Je třeba zadat název skupiny prostředků, která byla vytvořena v oblasti, kde jsou [k dispozici](#virtual-network-deployment-limitations)nasazení skupiny kontejnerů ve virtuální síti . Tento příkaz nasazuje veřejný kontejner Microsoft [aci-helloworld,][aci-helloworld] který spouští malý webový server Node.js sloužící statickou webovou stránku. V další části nasadíte druhou skupinu kontejnerů do stejné podsítě a otestujete komunikaci mezi dvěma instancemi kontejneru.

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

Při nasazení do nové virtuální sítě pomocí této metody může nasazení trvat několik minut, než se vytvoří síťové prostředky. Po počátečním nasazení se další nasazení skupin kontejnerů dokončí rychleji.

### <a name="deploy-to-existing-virtual-network"></a>Nasazení do existující virtuální sítě

Teď, když jste nasadili skupinu kontejnerů do nové virtuální sítě, nasaďte druhou skupinu kontejnerů do stejné podsítě a ověřte komunikaci mezi dvěma instancemi kontejneru.

Nejprve získejte IP adresu první skupiny kontejnerů, kterou jste nasadili, *kontejneru aplikace*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Výstup by měl zobrazit IP adresu skupiny kontejnerů v privátní podsíti:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Nyní nastavte `CONTAINER_GROUP_IP` ip adresu, kterou `az container show` jste načetli `az container create` pomocí příkazu, a proveďte následující příkaz. Tento druhý kontejner, *commchecker*, spustí image založenou na systému Alpine Linux a provede se proti privátní IP adrese první skupiny `wget` kontejnerů.

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

Po dokončení tohoto druhého kontejneru nasazení, vyžádat jeho `wget` protokoly, takže můžete vidět výstup příkazu, který provedl:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Pokud druhý kontejner úspěšně komunikoval s prvním, výstup by měl být podobný:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Výstup protokolu by `wget` měl ukázat, že byl schopen připojit a stáhnout soubor indexu z prvního kontejneru pomocí jeho privátní IP adresu v místní podsíti. Síťový provoz mezi dvěma skupinami kontejnerů zůstal ve virtuální síti.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Nasazení do existující virtuální sítě – YAML

Skupinu kontejnerů můžete také nasadit do existující virtuální sítě pomocí souboru YAML, šablony Správce prostředků nebo jiné programové metody, například pomocí sady Python SDK. Chcete-li nasadit do podsítě ve virtuální síti, zadejte několik dalších vlastností v YAML:

* `ipAddress`: Nastavení adresy IP pro skupinu kontejnerů.
  * `ports`: Porty, které chcete otevřít, pokud existuje.
  * `protocol`: Protokol (TCP nebo UDP) pro otevřený port.
* `networkProfile`: Určuje nastavení sítě, jako je virtuální síť a podsíť pro prostředek Azure.
  * `id`: Úplné ID prostředku Správce `networkProfile`prostředků v oblasti .

Chcete-li nasadit skupinu kontejnerů do virtuální sítě se souborem YAML, musíte nejprve získat ID profilu sítě. Spusťte příkaz [seznamu profilů sítě az][az-network-profile-list] a zadejte název skupiny prostředků, která obsahuje vaši virtuální síť a delegovanou podsíť.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Výstup příkazu zobrazí úplné ID prostředku pro profil sítě:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Jakmile budete mít ID profilu sítě, zkopírujte následující YAML do nového souboru s názvem *vnet-deploy-aci.yaml*. V `networkProfile`části `id` nahraďte hodnotu id, které jste právě načetli, a soubor uložte. Tento YAML vytvoří skupinu kontejnerů s názvem *appcontaineryaml* ve vaší virtuální síti.

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

Nasazení skupiny kontejnerů pomocí příkazu [az container create][az-container-create] a `--file` určením názvu souboru YAML pro parametr:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Po dokončení nasazení spusťte příkaz [az container show][az-container-show] a zobrazte jeho stav:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="delete-container-instances"></a>Odstranění instancí kontejneru

Až skončíte s prací s instancemi kontejneru, které jste vytvořili, odstraňte je pomocí následujících příkazů:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Odstranění síťových prostředků

Tato funkce v současné době vyžaduje několik dalších příkazů k odstranění síťových prostředků, které jste vytvořili dříve. Pokud jste použili ukázkové příkazy v předchozích částech tohoto článku k vytvoření virtuální sítě a podsítě, můžete tyto síťové prostředky odstranit pomocí následujícího skriptu. Skript předpokládá, že vaše skupina prostředků obsahuje jednu virtuální síť s jedním profilem sítě.

Před spuštěním skriptu `RES_GROUP` nastavte proměnnou na název skupiny prostředků obsahující virtuální síť a podsíť, která by měla být odstraněna. Aktualizujte název virtuální sítě, pokud jste `aci-vnet` nepoužili název navržený dříve. Skript je formátován pro prostředí Bash. Pokud dáváte přednost jinému prostředí, jako je prostředí PowerShell nebo příkazový řádek, budete muset odpovídajícím způsobem upravit přiřazení proměnných a přistupující chod.

> [!WARNING]
> Tento skript odstraní prostředky! Odstraní virtuální síť a všechny podsítě, které obsahuje. Před spuštěním tohoto skriptu se ujistěte, že již nepotřebujete *žádné* prostředky ve virtuální síti, včetně všech podsítí, které obsahuje. Po odstranění **jsou tyto prostředky neopravitelné**.

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

Pokud chcete nasadit novou virtuální síť, podsíť, profil sítě a skupinu kontejnerů pomocí šablony Správce prostředků, přečtěte si informace [o vytvoření skupiny kontejnerů Azure pomocí virtuální sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

V tomto článku bylo popsáno několik prostředků a funkcí virtuální sítě, i když krátce. Dokumentace k virtuální síti Azure rozsáhle popisuje tato témata:

* [Virtuální síť](../virtual-network/manage-virtual-network.md)
* [Podsíť](../virtual-network/virtual-network-manage-subnet.md)
* [Koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md)
* [Brána VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
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
