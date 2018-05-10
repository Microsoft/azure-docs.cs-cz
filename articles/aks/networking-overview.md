---
title: Konfigurace sítě v Azure Kubernetes služby (AKS)
description: Další informace o konfiguraci základních a pokročilých sítě v Azure Kubernetes služby (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/07/2018
ms.author: marsma
ms.openlocfilehash: 80d12d1f5d6b388c46ed90eb84b7bc00250e17ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
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
* Každý pod v clusteru je přiřazená IP adresa ve virtuální síti a může komunikovat přímo s další pracovními stanicemi soustředěnými kolem v clusteru a ostatní virtuální počítače ve virtuální síti.
* Pod může připojit k jiným službám ve peered virtuální síti a místní sítě prostřednictvím ExpressRoute a site-to-site (S2S) připojení k síti VPN. Pracovními stanicemi soustředěnými kolem jsou taky dostupné z místní.
* Vystavit služby Kubernetes externě nebo interně pomocí nástroje pro vyrovnávání zatížení Azure. Také funkce základní sítě.
* Pracovními stanicemi soustředěnými kolem v podsíti, které mají povolené koncové body služby lze bezpečně připojit ke službám Azure, například Azure Storage a databázi SQL.
* Použijte trasy definované uživatelem (UDR) směrovat provoz z pracovními stanicemi soustředěnými kolem do sítě virtuálního zařízení.
* Pracovními stanicemi soustředěnými kolem mají přístup k prostředkům na veřejného Internetu. Také funkce základní sítě.

> [!IMPORTANT]
> Každý uzel v clusteru služby AKS konfigurován pro pokročilé sítě může být hostitelem maximálně **30 pracovními stanicemi soustředěnými kolem**. Každý virtuální sítě zřízené pro použití s modul plug-in Azure CNI je omezeno na **4096 IP adresy** (/ 20).

## <a name="configure-advanced-networking"></a>Nakonfigurujte pokročilé vytváření sítí.

Pokud jste [vytvořit AKS cluster](kubernetes-walkthrough-portal.md) na portálu Azure, se dají konfigurovat pro pokročilé sítě následující parametry:

**Virtuální síť**: virtuální sítě, do které chcete nasadit Kubernetes clusteru. Pokud chcete vytvořit novou virtuální síť pro váš cluster, vyberte *vytvořit nový* a postupujte podle kroků v *vytvořit virtuální síť* části.

**Podsíť**: podsítí v rámci virtuální sítě, ve které chcete nasadit cluster. Pokud chcete vytvořit novou podsíť ve virtuální síti pro váš cluster, vyberte *vytvořit nový* a postupujte podle kroků v *vytvořit podsíť* části.

**Rozsah adres služby Kubernetes**: rozsah IP adres pro službu clusteru Kubernetes IP adresy. Tento rozsah nesmí být v rozsahu adres virtuální sítě IP clusteru.

**IP adresa pro službu Kubernetes DNS**: IP adresa pro službu DNS do clusteru. Tato adresa musí být v rámci *Kubernetes služby rozsah adres*.

**Most docker adresu**: IP adresa a síťová maska přiřadit Docker most. Tato IP adresa nesmí být v rozsahu adres virtuální sítě IP clusteru.

Z portálu Azure následující snímek obrazovky ukazuje příklad konfigurace těchto nastavení při vytváření clusteru AKS:

![Rozšířené konfigurace sítí na portálu Azure][portal-01-networking-advanced]

## <a name="plan-ip-addressing-for-your-cluster"></a>Naplánujte IP adresování pro váš cluster

Clustery nakonfigurované pokročilé sítě vyžadují další plánování. Velikost vaší virtuální sítě a jeho podsíť musí mohla pojmout množství pracovními stanicemi soustředěnými kolem, kterou plánujete spustit současně v clusteru, jakož i požadavků na škálování.

IP adresy pro pracovními stanicemi soustředěnými kolem a uzly clusteru se přidělují do zadané podsítě v rámci virtuální sítě. Každý uzel je nakonfigurovaný s primární IP adresy, který je IP adresa uzel a 30 dalších IP adres předem nakonfigurované pomocí Azure CNI přiřazených k pracovními stanicemi soustředěnými kolem naplánované k uzlu. Když škálovat cluster, každý uzel podobně nakonfigurovaný pomocí IP adresy z podsítě.

Jak je uvedeno nahoře, každý virtuální sítě zřízené pro použití s modul plug-in Azure CNI je omezeno na **4096 IP adresy** (/ 20). Každý uzel v clusteru nakonfigurované pro pokročilé sítě může být hostitelem maximálně **30 pracovními stanicemi soustředěnými kolem**.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Použít následující otázky a odpovědi **Upřesnit** síťové konfigurace.

* *Můžete nakonfigurovat pokročilé sítě pomocí rozhraní příkazového řádku Azure?*

  Ne. Pokročilé sítě je aktuálně k dispozici pouze při nasazení AKS clusterů na portálu Azure nebo pomocí šablony Resource Manageru.

* *Můžete nasadit virtuální počítače v podsíti Moje clusteru?*

  Ne. Nasazení virtuálních počítačů v podsíti používá váš cluster Kubernetes není podporována. Virtuální počítače může nasadit ve stejné virtuální síti, ale v jiné podsíti.

* * Můžete nakonfigurovat zásady sítě za pod?

  Ne. Zásady sítě za pod nejsou aktuálně podporovány.

* *Maximální počet pracovními stanicemi soustředěnými kolem je nasadit do uzlu konfigurovat?*

  Ve výchozím nastavení můžete každý uzel hostitele nesmí být delší než 30 pracovními stanicemi soustředěnými kolem. Maximální hodnota můžete změnit aktuálně jenom změnou `maxPods` vlastnost při nasazování clusteru pomocí šablony Resource Manageru.

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
