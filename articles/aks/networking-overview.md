---
title: Konfigurace sítě ve službě Azure Kubernetes Service (AKS)
description: Další informace o konfiguraci základních a pokročilých sítě ve službě Azure Kubernetes Service (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/23/2018
ms.author: marsma
ms.openlocfilehash: cfe034d6dcac48d7c9e4b2ce17e4926a81a27886
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216100"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Konfigurace sítě ve službě Azure Kubernetes Service (AKS)

Při vytváření clusteru služby Azure Kubernetes Service (AKS), můžete vybrat ze dvou možností sítě: **základní** nebo **Upřesnit**.

## <a name="basic-networking"></a>Základní sítě

**Základní** sítě možnost je výchozí konfigurace pro vytvoření clusteru AKS. Konfigurace sítě clusteru a jeho podů zcela spravuje Azure a je vhodný pro nasazení, které nevyžadují vlastní konfigurace virtuální sítě. Nemáte kontrolu nad konfigurací sítě, jako je podsítí nebo IP adresy rozsahů se přiřazuje při výběru základní sítě.

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

* Virtuální síť pro AKS cluster musí umožňovat odchozí připojení k Internetu.
* Nevytvářejte více než jeden cluster AKS ve stejné podsíti.
* Nesmíte používat AKS clustery `169.254.0.0/16`, `172.30.0.0/16`, nebo `172.31.0.0/16` pro Kubernetes service rozsah adres.
* Instanční objekt používané clusterem AKS musí mít minimálně [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) oprávnění na podsítě v rámci vaší virtuální sítě. Pokud chcete definovat [vlastní roli](../role-based-access-control/custom-roles.md) nemusíte používat předdefinovaná role Přispěvatel sítě, se vyžadují následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Naplánujte IP adresování pro váš cluster

Clusterech nakonfigurovaných s rozšířeného sítě vyžadovat další plánování. Velikost vaší virtuální sítě a podsítě musí podle počtu podů, které máte v úmyslu spustit i z počtu uzlů clusteru.

IP adresy pro tyto pody a uzly clusteru se přidělují Zadaná podsíť virtuální sítě. Každý uzel je nakonfigurovaný pomocí primární IP adresu, která je IP adresa uzlu a 30 dalších IP adres předem nakonfiguroval Azure CNI, které jsou přiřazeny podů naplánované k uzlu. Při horizontálním navýšením kapacity vašeho clusteru má každý uzel podobně nakonfigurovanou IP adresu z podsítě.

Plán IP adres pro AKS cluster se skládá z virtuální sítě, alespoň jednu podsíť pro uzly a podů a rozsah adres služby Kubernetes.

| Rozsah adres / Azure resource | Omezení a změna velikosti |
| --------- | ------------- |
| Virtuální síť | Virtuální síť Azure můžou být velké až /8 ale může mít 16 000 nakonfigurovat jenom IP adresy. |
| Podsíť | Musí být dostatečně velký, aby uzly, podů a všechny Kubernetes a Azure prostředky, které může být zřízené ve vašem clusteru. Například pokud nasadíte interní Azure Load Balancer, jeho front-endových IP adres se přidělují z podsítě clusteru, není veřejné IP adresy. <p/>Chcete-li vypočítat *minimální* velikost podsítě: `(number of nodes) + (number of nodes * pods per node)` <p/>Příklad pro cluster s 50 uzly: `(50) + (50 * 30) = 1,550` (/ 21, nebo větší) |
| Rozsah adres služby Kubernetes | Tento rozsah by neměly používat libovolný prvek sítě na nebo připojené k této virtuální síti. Adresa služby CIDR musí být menší než /12. |
| IP adresa služby Kubernetes DNS | IP adresu v rámci rozhraní Kubernetes služby rozsah adres, který bude používat zjišťování služby cluster (kube-dns). |
| Adresa mostu docker | IP adresa (v notaci CIDR) použít jako Docker bridge IP adresu na uzlech. Výchozí 172.17.0.1/16. |

Každá virtuální síť zřízených pro použití s modulem plug-in Azure CNI je omezené na **16 000 konfigurované IP adresy**.

## <a name="maximum-pods-per-node"></a>Maximální podů na jeden uzel

Výchozí maximální počet podů na jeden uzel v clusteru AKS se pohybuje mezi základní a rozšířené sítě a metody nasazení clusteru.

### <a name="default-maximum"></a>Výchozí maximální

* Základní síť: **110 podů na jeden uzel**
* Rozšířeného sítě **30 podů na jeden uzel**

### <a name="configure-maximum"></a>Nakonfigurujte maximální

V závislosti na způsobu nasazení budete moci změnit maximální počet podů na jeden uzel v clusteru AKS.

* **Azure CLI**: Zadejte `--max-pods` argument při nasazování clusteru s [az aks vytvořit] [ az-aks-create] příkazu.
* **Šablony Resource Manageru**: Zadejte `maxPods` vlastnost [ManagedClusterAgentPoolProfile] objektu při nasazování clusteru pomocí šablony Resource Manageru.
* **Azure portal**: nelze změnit maximální počet podů na jeden uzel, při nasazování clusteru pomocí webu Azure portal. Rozšířeného sítě clustery jsou omezená na 30 podů podle počtu uzlů po nasazení na webu Azure Portal.

## <a name="deployment-parameters"></a>Parametry nasazení

Při vytváření clusteru AKS následující parametry se dají konfigurovat pro rozšířeného sítě:

**Virtuální síť**: virtuální síť, do které chcete nasadit Kubernetes cluster. Pokud chcete vytvořit novou virtuální síť pro cluster, vyberte *vytvořit nový* a postupujte podle pokynů *vytvořit virtuální síť* oddílu. Virtuální síť je omezena na 16 000 nakonfigurovaných IP adres.

**Podsíť**: podsítě v rámci virtuální sítě, ve které chcete nasadit cluster. Pokud chcete vytvořit novou podsíť ve virtuální síti pro váš cluster, vyberte *vytvořit nový* a postupujte podle pokynů *vytvořit podsíť* části.

**Rozsah adres služby Kubernetes**: *rozsah adres služby Kubernetes* je rozsah IP, ze které jsou přiřazeny adres služby Kubernetes v clusteru (Další informace o službách Kubernetes najdete v části [ Služby] [ services] v dokumentaci ke Kubernetes).

Rozsah IP adres služby Kubernetes:

* Nesmí být v rozsahu adres virtuální sítě IP clusteru
* Se nesmí překrývat s jiných které partnerský vztah virtuální sítě clusteru virtuálních sítí
* Se nesmí překrývat s každé místní IP adresy

Pokud překrývající se rozsahy IP adres se používají může způsobit nepředvídatelné chování. Například pokud pod pokusí o přístup k IP adresy mimo cluster, a že IP také se stane, chcete-li být IP adresa služby, může se zobrazit nepředvídatelné chování. počet chyb.

**IP adresa služby Kubernetes DNS**: IP adresa pro službu DNS clusteru. Tato adresa musí být v rámci *rozsah adres služby Kubernetes*.

**Adresa mostu docker**: IP adresa a síťová maska přiřadit mostu Docker. Tato IP adresa nesmí být v rozsahu adres virtuální sítě IP clusteru.

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

* *Jak nakonfigurovat další vlastnosti pro podsíť, která jsem vytvořil při vytváření clusteru AKS Například koncových bodů služby.*

  Úplný seznam vlastností pro virtuální síť a podsítě, které jste vytvořili při vytváření clusteru AKS můžete nakonfigurovat na stránce standardní konfigurace virtuální sítě na webu Azure Portal.

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
[aks-ssh]: aks-ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
