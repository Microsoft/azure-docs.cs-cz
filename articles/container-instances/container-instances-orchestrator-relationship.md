---
title: Azure Container Instances a orchestrace kontejnerů
description: Pochopte, jak služby Azure Container Instances komunikují s orchestrací kontejnerů.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: c83648124f616670423b2ef459530c191d7e17e4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325767"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances a orchestrace kontejnerů

Z důvodu jejich malé velikosti a orientace aplikace jsou kontejnery vhodné pro prostředí agilního doručování a architektury založené na mikroslužbách. Úkol automatizace a správy velkého počtu kontejnerů a jejich interakce se označují jako *orchestrace*. Populární orchestrace kontejnerů zahrnují Kubernetes, DC/OS a Docker Swarm.

Azure Container Instances poskytuje některé základní možnosti plánování platforem Orchestration. A i když nepokrývá služby s vyšší hodnotou, které tyto platformy poskytují, Azure Container Instances je lze doplňovat. Tento článek popisuje rozsah toho, co Azure Container Instances Handles a jak se s ním můžou používat úplné orchestrace kontejnerů.

## <a name="traditional-orchestration"></a>Tradiční orchestrace

Standardní definice orchestrace zahrnuje následující úlohy:

- **Plánování**: V případě image kontejneru a žádosti o prostředky Najděte vhodný počítač, na kterém se má kontejner spustit.
- **Spřažení/proti spřažení**: Určete, že by se měla sada kontejnerů běžet vedle sebe (pro výkon) nebo dostatečně daleko od sebe (k dispozici).
- **Sledování stavu**: Sledujte selhání kontejneru a automaticky je znovu naplánujte.
- **Převzetí služeb při selhání**: Sledujte, co na každém počítači běží, a znovu Naplánujte kontejnery z neúspěšných počítačů do funkčních uzlů.
- **Škálování:** Přidejte nebo odeberte instance kontejnerů, které odpovídají požadavkům, a to buď ručně, nebo automaticky.
- **Síťové služby**: Poskytněte překryvnou síť pro koordinaci kontejnerů pro komunikaci mezi několika hostitelskými počítači.
- **Zjišťování služby**: Umožněte, aby se kontejnery navzájem vyhledaly automaticky, i když se přesunují mezi hostitelskými počítači a změnily IP adresy.
- **Koordinované upgrady aplikací**: Spravujte upgrady kontejnerů, aby nedocházelo k výpadkům aplikací, a pokud se něco nepovede, povolte vrácení zpět

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestrace pomocí Azure Container Instances: Vrstvený přístup

Azure Container Instances umožňuje vrstvený přístup k orchestraci a poskytuje všechny možnosti plánování a správy potřebné ke spuštění jednoho kontejneru a zároveň umožňuje platformám Orchestrator spravovat úlohy s více kontejnery nad ním.

Vzhledem k tomu, že základní infrastruktura pro instance kontejnerů je spravována Azure, platforma Orchestrator nemusí se zabývat tím, že najde příslušný hostitelský počítač, na kterém se spouští jeden kontejner. Flexibilita v cloudu zajišťuje, že jedna je vždy k dispozici. Místo toho se může Orchestrator soustředit na úlohy, které zjednodušují vývoj architektur s více kontejnery, včetně škálování a koordinovaných upgradů.

## <a name="scenarios"></a>Scénáře

I když je integrace nástroje Orchestrator s Azure Container Instances stále nascent, předpokládáme, že se v několika různých prostředích ukáže:

### <a name="orchestration-of-container-instances-exclusively"></a>Pouze orchestrace kontejnerových instancí

Vzhledem k tomu, že začínají rychle a účtují druhé, prostředí založené výhradně na Azure Container Instances nabízí nejrychlejší způsob, jak začít a řešit vysoce proměnlivé úlohy.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinace instancí kontejnerů a kontejnerů v Virtual Machines

Pro dlouhotrvající a stabilní úlohy jsou orchestrace kontejnerů v clusteru vyhrazených virtuálních počítačů obvykle levnější než spuštění stejných kontejnerů s Azure Container Instances. Nicméně instance kontejnerů nabízí skvělé řešení pro rychlé rozšiřování a zadávání celkové kapacity k tomu, abyste se mohli vypořádat s neočekávanými nebo krátkodobými špičkami v používání.

Místo horizontálního navýšení kapacity virtuálních počítačů v clusteru a následné nasazení dalších kontejnerů na tyto počítače může Orchestrator jednoduše naplánovat další kontejnery v Azure Container Instances a odstranit je, když už nejsou vyžadována.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Ukázková implementace: virtuální uzly pro službu Azure Kubernetes (AKS)

Pokud chcete rychle škálovat úlohy aplikace v clusteru AKS ( [Azure Kubernetes Service](../aks/intro-kubernetes.md) ), můžete použít *virtuální uzly* vytvořené dynamicky v Azure Container Instances. Virtuální uzly umožňují síťovou komunikaci mezi lusky, které běží v ACI a v clusteru AKS. 

Virtuální uzly aktuálně podporují instance kontejnerů Linux. Začněte s virtuálními uzly pomocí [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) nebo [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2047545).

Virtuální uzly používají open source [Virtual Kubelet][aci-connector-k8s] k napodobení Kubernetes [Kubelet][kubelet-doc] tím, že se registrují jako uzel s neomezenou kapacitou. Virtual Kubelet odešle do Azure Container Instances vytváření lusků jako [][pod-doc] skupiny kontejnerů.

Další příklady rozšíření rozhraní Kubernetes API na platformy kontejnerů bez serveru najdete v projektu [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) .

## <a name="next-steps"></a>Další kroky

Vytvořte svůj první kontejner pomocí Azure Container Instances [Průvodce rychlým startem](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/