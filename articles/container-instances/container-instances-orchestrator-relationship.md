---
title: Instance kontejneru a orchestrace kontejneru
description: Zjistěte, jak instance kontejnerů Azure interagují s orchestrátory kontejnerů.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: f3f8693d1a9a12e7c35d126ab3e3ca53448e5e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533662"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Instance kontejnerů Azure a orchestrátory kontejnerů

Vzhledem k jejich malé velikosti a orientaci aplikace jsou kontejnery vhodné pro agilní doručovací prostředí a architektury založené na mikroslužbách. Úloha automatizace a správy velkého počtu kontejnerů a jejich interakce se označuje jako *orchestrace*. Mezi oblíbené kontejnerové orchestrátory patří Kubernetes, DC/OS a Docker Swarm.

Azure Container Instances poskytuje některé základní možnosti plánování platformy orchestrace. A i když se nevztahuje na služby s vyšší hodnotou, které tyto platformy poskytují, instance kontejneru Azure je může doplňovat. Tento článek popisuje rozsah, co zpracovává instance kontejneru Azure a jak s ním mohou pracovat orchestrators s úplným kontejnerem.

## <a name="traditional-orchestration"></a>Tradiční orchestrace

Standardní definice orchestrace zahrnuje následující úkoly:

- **Plánování**: Vzhledem k image kontejneru a požadavek na prostředek, najít vhodný počítač, na kterém chcete spustit kontejneru.
- **Spřažení/Anti-afinita**: Určete, že sada kontejnerů by měla běžet v blízkosti sebe (pro výkon) nebo dostatečně daleko od sebe (pro dostupnost).
- **Monitorování stavu**: Sledujte selhání kontejnerů a automaticky je přeplánujte.
- **Převzetí služeb při selhání**: Sledujte, co je spuštěno na každém počítači a přeplánujte kontejnery z neúspěšných počítačů na uzly v pořádku.
- **Změna měřítka**: Přidejte nebo odeberte instance kontejneru tak, aby odpovídaly poptávce, a to ručně nebo automaticky.
- **Sítě**: Poskytují překryvnou síť pro koordinaci kontejnerů pro komunikaci mezi více hostitelskými počítači.
- **Zjišťování služby**: Povolit kontejnery najít navzájem automaticky, i když se pohybují mezi hostitelskými počítači a změnit IP adresy.
- **Koordinované upgrady aplikací:** Správa upgradů kontejnerů, aby se zabránilo prostojům aplikací, a povolte vrácení zpět, pokud se něco pokazí.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestrace s instancemi kontejnerů Azure: přístup s vrstvami

Azure Container Instances umožňuje vrstvený přístup k orchestraci, poskytuje všechny možnosti plánování a správy potřebné ke spuštění jednoho kontejneru, a zároveň umožňuje platformy orchestrator spravovat úlohy s více kontejnery nad ním.

Vzhledem k tomu, že základní infrastrukturu pro instance kontejnerů spravuje Azure, platforma orchestrator se nemusí zabývat hledáním vhodného hostitelského počítače, na kterém by bylo nutné spustit jeden kontejner. Elasticita cloudu zajišťuje, že jeden je vždy k dispozici. Místo toho se orchestrátor může zaměřit na úlohy, které zjednodušují vývoj architektur s více kontejnery, včetně škálování a koordinovaných upgradů.

## <a name="scenarios"></a>Scénáře

Zatímco integrace orchestrator s instancemi kontejnerů Azure je stále rodící se, očekáváme, že se objeví několik různých prostředí:

### <a name="orchestration-of-container-instances-exclusively"></a>Orchestrace instancí kontejneru výhradně

Vzhledem k tomu, že začínají rychle a fakturují za druhé, prostředí založené výhradně na Azure Container Instances nabízí nejrychlejší způsob, jak začít a vypořádat se s vysoce variabilními úlohami.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinace instancí kontejnerů a kontejnerů ve virtuálních počítačích

Pro dlouhotrvající a stabilní úlohy je orchestrace kontejnerů v clusteru vyhrazených virtuálních počítačů obvykle levnější než spuštění stejných kontejnerů s instancemi kontejnerů Azure. Instance kontejneru však nabízejí skvělé řešení pro rychlé rozšíření a smluvní celkové kapacity pro řešení neočekávaných nebo krátkodobých špičky v používání.

Spíše než horizontální navýšení kapacity počtu virtuálních počítačů ve vašem clusteru, pak nasazení dalších kontejnerů na tyto počítače, orchestrator můžete jednoduše naplánovat další kontejnery v Azure Container Instances a odstranit je, když už nejsou už Potřebné.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Ukázková implementace: virtuální uzly pro službu Azure Kubernetes Service (AKS)

Chcete-li rychle škálovat úlohy aplikací v clusteru [Služby Azure Kubernetes](../aks/intro-kubernetes.md) (AKS), můžete použít *virtuální uzly* vytvořené dynamicky v azure container instances. Virtuální uzly umožňují síťovou komunikaci mezi pody, které běží v ACI a clusteru AKS. 

Virtuální uzly aktuálně podporují instance kontejnerů Linuxu. Začínáme s virtuálními uzly pomocí [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) nebo [portálu Azure](https://go.microsoft.com/fwlink/?linkid=2047545).

Virtuální uzly používají open source [Virtual Kubelet][aci-connector-k8s] k napodobování [Kubelet][kubelet-doc] kubernetes registrací jako uzel s neomezenou kapacitou. Virtuální Kubelet odešle vytvoření podů jako [skupiny][pod-doc] kontejnerů v Azure Container Instances.

Další příklady rozšíření rozhraní API Kubernetes na platformy kontejnerů bez serveru najdete v projektu [Virtual Kubelet.](https://github.com/virtual-kubelet/virtual-kubelet)

## <a name="next-steps"></a>Další kroky

Vytvořte svůj první kontejner s instancemi kontejneru Azure pomocí [průvodce rychlým startem](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
