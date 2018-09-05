---
title: Konfigurace sítě ve službě Azure Kubernetes Service (AKS)
description: Další informace o konfiguraci základních a pokročilých sítě ve službě Azure Kubernetes Service (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/31/2018
ms.author: marsma
ms.openlocfilehash: e78be76d68cf75cf9d59f5b5dff86c65524275a9
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697234"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Konfigurace sítě ve službě Azure Kubernetes Service (AKS)

Při vytváření clusteru služby Azure Kubernetes Service (AKS), můžete vybrat ze dvou možností sítě: **základní** nebo **Upřesnit**.

## <a name="basic-networking"></a>Základní sítě

**Základní** sítě možnost je výchozí konfigurace pro vytvoření clusteru AKS. Konfigurace sítě clusteru a jeho podů je zcela spravuje Azure a je vhodný pro nasazení, které nevyžadují vlastní konfigurace virtuální sítě. Nemáte kontrolu nad konfigurací sítě, jako je podsítí nebo IP adresy rozsahů se přiřazuje při výběru základní sítě.

Uzly v clusteru AKS nakonfigurovaný pro základní sítě použijte [kubenet] [ kubenet] modulu plug-in Kubernetes.

## <a name="advanced-networking"></a>Rozšířeného sítě

**Pokročilé** sítě umístí pody ve službě Azure Virtual Network (VNet), který konfigurujete, přičemž jim automatické připojení k virtuální síti prostředky a nastavení díky bohaté integraci funkcí nabídku virtuální sítě. Rozšířeného sítě je k dispozici při nasazování AKS clustery [webu Azure portal][portal], rozhraní příkazového řádku Azure, nebo pomocí šablony Resource Manageru.

Uzly v clusteru AKS nakonfigurován pro použití rozšířeného sítě [Azure Container síťové rozhraní (CNI)] [ cni-networking] modulu plug-in Kubernetes.

![Diagram znázorňující dva uzly s mostů propojení každý z nich jedné virtuální sítě Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Pokročilé funkce sítě

Rozšířeného sítě poskytují následující výhody:

* Nasazení clusteru AKS do existující virtuální síť, nebo vytvořte novou virtuální síť a podsíť pro váš cluster.
* Každý pod v clusteru je přiřazená IP adresa ve virtuální síti a může komunikovat přímo s dalších podů v clusteru a ostatní uzly ve virtuální síti.
* Podu můžete připojit k jiným službám v partnerské virtuální síti a místní sítě přes ExpressRoute a site-to-site (S2S) připojení k síti VPN. Podů jsou také dostupné z místní.
* Služba Kubernetes zveřejnit prostřednictvím služby Azure Load Balancer interně nebo externě. Také funkce základní sítě.
* Podů v podsíti, které mají povolené koncové body služeb můžete bezpečně připojit ke službám Azure, například Azure Storage a SQL DB.
* Použití tras definovaných uživatelem (UDR) pro směrování provozu z podů na síťové virtuální zařízení.
* Podů mít přístup k prostředkům na veřejném Internetu. Také funkce základní sítě.

## <a name="advanced-networking-prerequisites"></a>Pokročilé síťové požadavky

* Virtuální síť pro AKS cluster, musíte povolit odchozí připojení k Internetu.
* Nevytvářejte více než jeden cluster AKS ve stejné podsíti.
* Nesmíte používat AKS clustery `169.254.0.0/16`, `172.30.0.0/16`, nebo `172.31.0.0/16` pro Kubernetes service rozsah adres.
* Instanční objekt používané clusterem AKS musí mít minimálně [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) oprávnění na podsítě v rámci vaší virtuální sítě. Pokud chcete definovat [vlastní roli](../role-based-access-control/custom-roles.md) nemusíte používat předdefinovaná role Přispěvatel sítě, se vyžadují následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Naplánujte IP adresování pro váš cluster

Clusterech nakonfigurovaných s rozšířeného sítě vyžadovat další plánování. Velikost vaší virtuální sítě a podsítě musí podle počtu podů, které máte v úmyslu spustit i z počtu uzlů clusteru.

IP adresy pro uzly clusteru a podů přidělují v zadané podsíti ve virtuální síti. Každý uzel je nakonfigurovaný pomocí primární IP adresu, která je IP adresa uzlu a 30 dalších IP adres předem nakonfiguroval Azure CNI, které jsou přiřazeny podů naplánované k uzlu. Při horizontálním navýšením kapacity vašeho clusteru má každý uzel podobně nakonfigurovanou IP adresu z podsítě.

Plán IP adres pro AKS cluster se skládá z virtuální sítě, alespoň jednu podsíť pro uzly a podů a rozsah adres služby Kubernetes.

| Rozsah adres / Azure resource | Omezení a změna velikosti |
| --------- | ------------- |
| Virtuální síť | Virtuální síť Azure můžou být velké až /8, ale je omezená na 65 536 nakonfigurovaných IP adres. |
| Podsíť | Musí být dostatečně velký, aby uzly, podů a všechny Kubernetes a Azure prostředky, které může být zřízené ve vašem clusteru. Například pokud nasadíte interní Azure Load Balancer, jeho front-endových IP adres se přidělují z podsítě clusteru, není veřejné IP adresy. <p/>Chcete-li vypočítat *minimální* velikost podsítě: `(number of nodes) + (number of nodes * pods per node)` <p/>Příklad pro cluster s 50 uzly: `(50) + (50 * 30) = 1,550` (/ 21, nebo větší) |
| Rozsah adres služby Kubernetes | Tento rozsah by neměly používat libovolný prvek sítě na nebo připojení k této virtuální síti. Adresa služby CIDR musí být menší než /12. |
| IP adresa služby Kubernetes DNS | IP adresu v rámci rozhraní Kubernetes služby rozsah adres, který bude používat zjišťování služby cluster (kube-dns). |
| Adresa mostu docker | IP adresa (v notaci CIDR) použít jako Docker bridge IP adresu na uzlech. Výchozí 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Maximální podů na jeden uzel

Výchozí maximální počet podů na jeden uzel v clusteru AKS se pohybuje mezi Basic a rozšířeného sítě a metody nasazení clusteru.

### <a name="default-maximum"></a>Výchozí maximální

Jedná se o *výchozí* maxima při nasazení AKS clusteru bez zadání maximálního počtu podů v době nasazení:

| Metoda nasazení | Basic | Rozšířený | Možnost konfigurace během nasazení |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ano |
| Šablona Resource Manageru | 110 | 30 | Ano |
| Portál | 110 | 30 | Ne |

### <a name="configure-maximum---new-clusters"></a>Nakonfigurujte maximální - nových clusterů

Chcete-li určit jiný maximální počet podů na uzel při nasazování clusteru AKS:

* **Azure CLI**: Zadejte `--max-pods` argument při nasazování clusteru s [az aks vytvořit] [ az-aks-create] příkazu.
* **Šablony Resource Manageru**: Zadejte `maxPods` vlastnost [ManagedClusterAgentPoolProfile] objektu při nasazování clusteru pomocí šablony Resource Manageru.
* **Azure portal**: nelze změnit maximální počet podů na jeden uzel, při nasazování clusteru pomocí webu Azure portal. Rozšířeného sítě clustery jsou omezená na 30 podů podle počtu uzlů po nasazení na webu Azure Portal.

### <a name="configure-maximum---existing-clusters"></a>Nakonfigurujte maximální - stávajících clusterů

Nelze změnit maximální podů na jeden uzel v existujícím clusteru AKS. Číslo můžete upravit pouze v případě, že počáteční nasazení clusteru.

## <a name="deployment-parameters"></a>Parametry nasazení

Při vytváření clusteru AKS následující parametry se dají konfigurovat pro rozšířeného sítě:

**Virtuální síť**: virtuální sítě, do které chcete nasadit Kubernetes cluster. Pokud chcete vytvořit novou virtuální síť pro cluster, vyberte *vytvořit nový* a postupujte podle pokynů *vytvořit virtuální síť* oddílu. Informace o omezení a kvóty pro virtuální síť Azure, najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsíť**: podsíť ve virtuální síti, ve které chcete nasadit cluster. Pokud chcete vytvořit novou podsíť ve virtuální síti pro váš cluster, vyberte *vytvořit nový* a postupujte podle pokynů *vytvořit podsíť* části.

**Rozsah adres služby Kubernetes**: Toto je sada virtuálních IP adres, který přiřazuje Kubernetes [služby] [ services] ve vašem clusteru. Můžete použít libovolný rozsah privátních adres, který splňuje následující požadavky:

* Nesmí být v rozsahu IP adres virtuální sítě clusteru
* Se nesmí překrývat s jinými virtuálními sítěmi, které vytvoří partnerskou virtuální síť s clustery
* Se nesmí překrývat s každé místní IP adresy
* Nesmí být v rozsahu `169.254.0.0/16`, `172.30.0.0/16`, nebo `172.31.0.0/16`

I když je technicky možné určit rozsah adres služby v rámci stejné virtuální síti jako cluster, tím proto nedoporučujeme. Pokud překrývající se rozsahy IP adres se používají může způsobit nepředvídatelné chování. Další informace najdete v tématu [nejčastější dotazy k](#frequently-asked-questions) části tohoto článku. Další informace o službách Kubernetes najdete v části [služby] [ services] v dokumentaci ke Kubernetes.

**IP adresa služby Kubernetes DNS**: IP adresa pro službu DNS clusteru. Tato adresa musí být v rámci *rozsah adres služby Kubernetes*.

**Adresa mostu docker**: IP adresa a síťová maska přiřadit mostu Docker. Tato IP adresa nesmí být v rámci rozsahu IP adres virtuální sítě ve vašem clusteru.

## <a name="configure-networking---cli"></a>Konfigurace sítě – rozhraní příkazového řádku

Při vytváření clusteru AKS pomocí Azure CLI můžete také nakonfigurovat rozšířeného sítě. Pomocí následujících příkazů vytvořte nový cluster AKS pomocí pokročilých síťových služeb povolené.

Nejprve Získejte ID prostředku podsítě pro existující podsíť, do které budou připojeny clusteru AKS:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Použití [az aks vytvořit] [ az-aks-create] příkazů `--network-plugin azure` argument k vytvoření clusteru pomocí rozšířeného sítě. Aktualizace `--vnet-subnet-id` hodnotu s ID podsítě shromážděných v předchozím kroku:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurace sítě – portál

Na následujícím snímku obrazovky z portálu Azure portal ukazuje příklad konfigurace tato nastavení při vytváření clusteru AKS:

![Upřesnit konfiguraci sítě na webu Azure Portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Platí následující dotazy a odpovědi k **Upřesnit** konfiguraci sítě.

* *Můžete nasadit virtuální počítače v podsíti Moje clusteru?*

  Ne. Nasazení virtuálních počítačů v podsíti používají svůj cluster Kubernetes se nepodporuje. Virtuální počítače jde nasadit ve stejné virtuální síti, ale v jiné podsíti.

* *Můžete nakonfigurovat zásady sítě než pod?*

  Ne. Zásady sítě než pod nejsou aktuálně podporovány.

* *Maximální počet podů je nasadit do uzlu konfigurovatelné?*

  Ano, pokud nasadíte cluster pomocí šablony Resource Manageru nebo rozhraní příkazového řádku Azure. Zobrazit [maximální počet podů na uzel](#maximum-pods-per-node).

  Nelze změnit maximální počet podů na jeden uzel v existujícím clusteru.

* *Jak nakonfigurovat další vlastnosti pro podsíť, která jsem vytvořil při vytváření clusteru AKS Například koncových bodů služby.*

  Úplný seznam vlastností pro virtuální síť a podsítě, které jste vytvořili při vytváření clusteru AKS můžete nakonfigurovat na stránce konfigurace standardní virtuální sítě na webu Azure Portal.

* *Můžete použít jiné podsíti v rámci virtuální sítě pro tento cluster* **rozsah adres služby Kubernetes**?

  Není doporučeno, ale tato konfigurace je možné. Rozsah adres služby je sada virtuální IP adresy (VIP), který přiřazuje služby v clusteru Kubernetes. Sítě Azure nemá žádný vhled do rozsahu IP služby clusteru Kubernetes. Z důvodu nedostatku vhled do rozsahu adres služby clusteru je možné později vytvořit novou podsíť ve virtuální síti clusteru, který se překrývá s rozsahem adres služby. Pokud dojde k takové překrývají, Kubernetes přiřadit služby integrační balíček, který je již používán jiným prostředkem v podsíti, způsobit nepředvídatelné chování nebo selhání. Tím zajistíte, že používáte rozsah adres mimo virtuální síť clusteru, se můžete vyhnout toto riziko překrývají.

## <a name="next-steps"></a>Další postup

### <a name="networking-in-aks"></a>Správa sítě v AKS

Další informace o možnostech sítě v AKS v následujících článcích:

[Statické IP adresy pomocí nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS)](static-ip.md)

[HTTPS příchozího přenosu dat ve službě Azure Container Service (AKS)](ingress.md)

[Použití interního nástroje Azure Container Service (AKS)](internal-lb.md)

### <a name="acs-engine"></a>Modul ACS

[Modul Azure Container Service (jádro ACS)] [ acs-engine] je opensourcový projekt, který generuje šablon Azure Resource Manageru můžete použít pro nasazení clusterů s podporou Dockeru v Azure. Kubernetes, DC/OS, Swarm režimu a Swarm orchestrátorů je možné nasadit s modulem ACS.

Clustery Kubernetes jsou vytvořené pomocí modul ACS podporovat [kubenet] [ kubenet] a [Azure CNI] [ cni-networking] moduly plug-in. V důsledku toho základních a pokročilých síťové scénáře jsou podporované modulem ACS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
