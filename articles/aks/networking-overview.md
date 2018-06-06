---
title: Konfigurace sítě v Azure Kubernetes služby (AKS)
description: Další informace o konfiguraci základních a pokročilých sítě v Azure Kubernetes služby (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/04/2018
ms.author: marsma
ms.openlocfilehash: d6f42a5f3ce907fdb759bef29ca25bdc7fe365d9
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757004"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Konfigurace sítě v Azure Kubernetes služby (AKS)

Při vytváření clusteru služby Azure Kubernetes služby (AKS), můžete vybrat ze dvou možností sítě: **základní** nebo **Upřesnit**.

## <a name="basic-networking"></a>Základní sítě

**Základní** sítě možnost je výchozí konfigurace pro vytvoření clusteru AKS. Konfigurace sítě clusteru a jeho pracovními stanicemi soustředěnými kolem úplně spravuje Azure a je vhodný pro nasazení, které nevyžadují vlastní konfiguraci virtuální sítě. Nemáte kontrolu nad konfiguraci sítě, jako je podsítě nebo IP adresy rozsahů clusteru přiřazuje při výběru základní sítě.

Uzly v clusteru služby AKS nakonfigurovaný na používání základní sítě [kubenet] [ kubenet] Kubernetes modulu plug-in.

## <a name="advanced-networking"></a>Pokročilé vytváření sítí.

**Rozšířené** sítě umístí vaší pracovními stanicemi soustředěnými kolem virtuální síť Azure (VNet), který konfigurujete, poskytovat jim automatické připojení k prostředkům sítě VNet a nastavte integrace s bohaté možnosti nabídky této virtuální sítě.
Pokročilé sítě je nyní k dispozici pouze při nasazení AKS clusterů v [portál Azure] [ portal] nebo pomocí šablony Resource Manageru.

Uzly v clusteru služby AKS nakonfigurována pro použití pokročilých síťových [Azure kontejneru síťové rozhraní (CNI)] [ cni-networking] Kubernetes modulu plug-in.

![Diagram zobrazující dva uzly s mostů připojení jednotlivých na jednu virtuální síť Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Pokročilé funkce sítě

Pokročilé sítě poskytuje následující výhody:

* Nasazení clusteru AKS do stávající virtuální síť, nebo vytvořit novou virtuální síť a podsíť pro váš cluster.
* Každý pod v clusteru je přiřazená IP adresa ve virtuální síti a může komunikovat přímo s další pracovními stanicemi soustředěnými kolem v clusteru a dalších uzlů ve virtuální síti.
* Pod může připojit k jiným službám ve peered virtuální síti a místní sítě prostřednictvím ExpressRoute a site-to-site (S2S) připojení k síti VPN. Pracovními stanicemi soustředěnými kolem jsou taky dostupné z místní.
* Vystavit služby Kubernetes externě nebo interně pomocí nástroje pro vyrovnávání zatížení Azure. Také funkce základní sítě.
* Pracovními stanicemi soustředěnými kolem v podsíti, které mají povolené koncové body služby lze bezpečně připojit ke službám Azure, například Azure Storage a databázi SQL.
* Použijte trasy definované uživatelem (UDR) směrovat provoz z pracovními stanicemi soustředěnými kolem do sítě virtuálního zařízení.
* Pracovními stanicemi soustředěnými kolem mají přístup k prostředkům na veřejného Internetu. Také funkce základní sítě.

> [!IMPORTANT]
> Každý uzel v clusteru služby AKS konfigurován pro pokročilé sítě může být hostitelem maximálně **30 pracovními stanicemi soustředěnými kolem**. Každý virtuální sítě zřízené pro použití s modul plug-in Azure CNI je omezeno na **4096 konfigurované IP adresy**.

## <a name="advanced-networking-prerequisites"></a>Rozšířené síťové požadavky

* Virtuální sítě pro AKS cluster musí umožňovat odchozí připojení k Internetu.
* Nevytvářejte více než jeden cluster AKS ve stejné podsíti.
* Pokročilé sítě pro AKS nepodporuje virtuálních sítí patřících používání zón DNS privátní Azure.
* Nesmíte používat clustery AKS `169.254.0.0/16`, `172.30.0.0/16`, nebo `172.31.0.0/16` pro Kubernetes služby rozsah adres.
* Objekt služby určené pro AKS cluster musí mít `Contributor` oprávnění ke skupině prostředků, který obsahuje existující virtuální síť.

## <a name="plan-ip-addressing-for-your-cluster"></a>Naplánujte IP adresování pro váš cluster

Clustery nakonfigurované pokročilé sítě vyžadují další plánování. Velikost vaší virtuální sítě a jeho podsíť musí zohlednit počet pracovními stanicemi soustředěnými kolem, kterou plánujete spustit jak počtu uzlů clusteru.

IP adresy pro pracovními stanicemi soustředěnými kolem a uzly clusteru se přidělují do zadané podsítě v rámci virtuální sítě. Každý uzel je nakonfigurovaný s primární IP adresy, což je IP adresa uzlu a 30 další IP adres předem nakonfigurované pomocí Azure CNI přiřazených k pracovními stanicemi soustředěnými kolem naplánované k uzlu. Když škálovat cluster, každý uzel podobně nakonfigurovaný pomocí IP adresy z podsítě.

Plán IP adresu pro cluster služby AKS se skládá z virtuální sítě, alespoň jednu podsíť pro uzly a pracovními stanicemi soustředěnými kolem a rozsah adres Kubernetes služby.

| Rozsah adres / Azure prostředků | Omezení a změna velikosti |
| --------- | ------------- |
| Virtuální síť | Virtuální síť Azure může být tak velké, jaká podsíť/8 ale může mít 4096 nakonfigurovat jenom IP adresy. |
| Podsíť | Musí být dostatečně velký na to, aby dokázala pojmout uzly a pracovními stanicemi soustředěnými kolem. Vypočítat velikost vašeho minimální podsítě: (počet uzlů) + (počet uzlů * pracovními stanicemi soustředěnými kolem na každém uzlu). Pro cluster s podporou 50 uzlu: (50) + (50 * 30) = 1,550, podsíť by musela být /21 nebo větší. |
| Rozsah adres Kubernetes služby | Tento rozsah by neměly být použity žádné sítě prvku na nebo připojené k této virtuální sítě. Adresa služby CIDR musí být menší než /12. |
| IP adresa pro službu Kubernetes DNS | IP adresa v rámci Kubernetes služby rozsah adres, který se použije zjišťování služby cluster (kube-dns). |
| Adresa most docker | IP adresa (v notaci CIDR) používá jako most Docker IP adresu na uzlech. Výchozí 172.17.0.1/16. |

Jak je uvedeno nahoře, každý virtuální sítě zřízené pro použití s modul plug-in Azure CNI je omezeno na **4096 konfigurované IP adresy**. Každý uzel v clusteru nakonfigurované pro pokročilé sítě může být hostitelem maximálně **30 pracovními stanicemi soustředěnými kolem**.

## <a name="configure-advanced-networking"></a>Nakonfigurujte pokročilé vytváření sítí.

Pokud jste [vytvořit AKS cluster](kubernetes-walkthrough-portal.md) na portálu Azure, se dají konfigurovat pro pokročilé sítě následující parametry:

**Virtuální síť**: virtuální sítě, do které chcete nasadit Kubernetes clusteru. Pokud chcete vytvořit novou virtuální síť pro váš cluster, vyberte *vytvořit nový* a postupujte podle kroků v *vytvořit virtuální síť* části.

**Podsíť**: podsítí v rámci virtuální sítě, ve které chcete nasadit cluster. Pokud chcete vytvořit novou podsíť ve virtuální síti pro váš cluster, vyberte *vytvořit nový* a postupujte podle kroků v *vytvořit podsíť* části.

**Rozsah adres služby Kubernetes**: rozsah IP adres pro službu clusteru Kubernetes IP adresy. Tento rozsah nesmí být v rozsahu adres virtuální sítě IP clusteru.

**IP adresa pro službu Kubernetes DNS**: IP adresa pro službu DNS do clusteru. Tato adresa musí být v rámci *Kubernetes služby rozsah adres*.

**Most docker adresu**: IP adresa a síťová maska přiřadit Docker most. Tato IP adresa nesmí být v rozsahu adres virtuální sítě IP clusteru.

Z portálu Azure následující snímek obrazovky ukazuje příklad konfigurace těchto nastavení při vytváření clusteru AKS:

![Rozšířené konfigurace sítí na portálu Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Použít následující otázky a odpovědi **Upřesnit** síťové konfigurace.

* *Můžete nakonfigurovat pokročilé sítě pomocí rozhraní příkazového řádku Azure?*

  Ne. Pokročilé sítě je aktuálně k dispozici pouze při nasazení AKS clusterů na portálu Azure nebo pomocí šablony Resource Manageru.

* *Můžete nasadit virtuální počítače v podsíti Moje clusteru?*

  Ne. Nasazení virtuálních počítačů v podsíti používá váš cluster Kubernetes není podporována. Virtuální počítače může nasadit ve stejné virtuální síti, ale v jiné podsíti.

* *Můžete nakonfigurovat zásady sítě za pod?*

  Ne. Zásady sítě za pod nejsou aktuálně podporovány.

* *Maximální počet pracovními stanicemi soustředěnými kolem je nasadit do uzlu konfigurovat?*

  Ve výchozím nastavení můžete každý uzel hostitele nesmí být delší než 30 pracovními stanicemi soustředěnými kolem. Maximální hodnota může změnit pouze úpravou `maxPods` vlastnost při nasazování clusteru pomocí šablony Resource Manageru.

* *Jak lze nakonfigurovat další vlastnosti pro podsíť, vytvořené během vytváření clusteru AKS? Například koncové body služby.*

  Úplný seznam vlastností pro virtuální sítě a podsítě, které vytvoříte při vytváření clusteru AKS lze nakonfigurovat na stránce standardní konfigurace sítě VNet na portálu Azure.

## <a name="next-steps"></a>Další postup

### <a name="networking-in-aks"></a>Sítě v AKS

Další informace o možnostech sítě v AKS v těchto článcích:

[Použijte statickou IP adresu s nástrojem pro vyrovnávání zatížení Azure Kubernetes služby (AKS)](static-ip.md)

[Příchozí přenos HTTPS na Azure Container Service (AKS)](ingress.md)

[Použijte Vyrovnávání zatížení interní s Azure Container Service (AKS)](internal-lb.md)

### <a name="acs-engine"></a>Modul služby ACS

[Stroj služby Azure kontejneru (stroj služby ACS)] [ acs-engine] je open-source projekt, který generuje šablony Azure Resource Manager můžete použít pro nasazení povolená Docker clusterů v Azure. Kubernetes, DC/OS, Swarm režimu a Swarm orchestrators se dá nasadit s modulem služby ACS.

Podporovat Kubernetes clustery, které jsou vytvořené pomocí modulu služby ACS [kubenet] [ kubenet] a [Azure CNI] [ cni-networking] modulů plug-in. Základní a rozšířené síťové scénáře jsou podporovány jako takový modul služby ACS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md
