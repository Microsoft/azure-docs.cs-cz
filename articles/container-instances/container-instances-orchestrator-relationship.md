---
title: Služba Azure Container Instances a Orchestrace kontejnerů
description: Seznamte se s Azure container instance interakci s orchestrátorů kontejnerů.
services: container-instances
author: seanmck
ms.service: container-instances
ms.topic: article
ms.date: 10/05/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c17bdb5a81640a7162ae735a4633a31cdfffbb1d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803507"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Služba Azure Container Instances a orchestrátorů kontejnerů

Vzhledem k jejich malou velikost a orientace aplikace kontejnery jsou vhodná pro prostředí agile doručování a architektur založených na mikroslužbách. Úlohy automatizace a Správa velkého počtu kontejnerů a jejich vzájemné interakce se označuje jako *Orchestrace*. Oblíbených orchestrátorů kontejnerů zahrnují Kubernetes, DC/OS a Docker Swarm.

Služba Azure Container Instances nabízí některé základní schopnosti plánování Orchestrace platforem. A přestože nepokrývá služby vyšší hodnota, které poskytují tyto platformy, Azure Container Instances může být doplňkové k nim. Tento článek popisuje obor co zpracovává Azure Container Instances a jak orchestrátorů kontejnerů může pracovat s ním.

## <a name="traditional-orchestration"></a>Tradiční Orchestrace

Standardní definice Orchestrace zahrnuje následující úlohy:

- **Plánování**: Zadaný image kontejneru a požadavkem na prostředky, najít vhodnou počítači, ve kterém pro spuštění kontejneru.
- **Spřažení/proti-affinity**: určení, by měla sadu kontejnerů spuštěny okolních druhou (pro výkonu) nebo dostatečně daleko od sebe (dostupnosti).
- **Monitorování stavu**: Podívejte se na selhání kontejneru a automaticky přeplánovat.
- **Převzetí služeb při selhání**: sledovat, co běží na každém počítači a plánovanou zkoušku přeplánovat kontejnerů z neúspěšné počítače pro uzly v dobrém stavu.
- **Škálování**: Přidání nebo odebrání instance kontejneru tak, aby odpovídaly poptávky, ručně nebo automaticky.
- **Sítě**: poskytují překryvné sítě pro koordinaci kontejnery pro komunikaci mezi více hostitelských počítačů.
- **Zjišťování služby**: umožňují používání kontejnerů k sobě navzájem i při přesunu mezi hostitelské počítače a změnit IP adres automaticky, vyhledejte.
- **Koordinovaných upgradů aplikací**: Správa kontejneru upgrady výpadky aplikací a povolit možnost vrácení zpět, pokud se něco nepovede.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestrace s Azure Container Instances: vrstveného přístupu

Azure Container Instances umožňuje vrstveného přístupu k orchestraci, poskytuje všechny možnosti plánování a správu potřebné ke spuštění jednoho kontejneru, přičemž platformy orchestrator ke správě více kontejnerů úloh dojde k jeho zvýraznění.

Protože základní infrastruktury pro container instances je spravuje Azure, není potřeba zabývat se hledání počítače s vhodného hostitele, ve kterém se spustí jedním kontejnerem platformě nástroje orchestrator. Flexibilitu cloudu zajišťuje, že jeden je vždy k dispozici. Místo toho orchestrator můžete zaměřit na úkoly, které zjednodušují vývoj architektury více kontejnerů, včetně škálování a koordinovaných upgradů.

## <a name="potential-scenarios"></a>Potenciální scénáře

Při integraci nástroje orchestrator pomocí služby Azure Container Instances je stále vznikajícím, Očekáváme, že mohou vzniknout několik různých prostředích:

### <a name="orchestration-of-container-instances-exclusively"></a>Orchestrace kontejnerů instance výhradně

Protože rychle začít a fakturaci po sekundách, prostředí založen výhradně na Azure Container Instances nabízí nejrychlejší způsob, jak začít a řešit velmi proměnlivé úlohy.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinace container instances a kontejnerů ve službě Virtual Machines

Pro úlohy dlouhotrvající, stabilní a Orchestrace kontejnerů v clusteru vyhrazených virtuálních počítačů je obvykle levnější než systémem stejné kontejnery pomocí služby Azure Container Instances. Instance kontejneru však nabízí vynikající řešení pro rychlé rozšiřování a smluvní vaše celková kapacita se neočekávaný nebo krátkodobou špičky využití.

Místo horizontální navýšení kapacity počtu virtuálních počítačů v clusteru, pak nasazení dalších kontejnerů na těchto počítačích, orchestrator můžete jednoduše naplánovat dalších kontejnerů ve službě Azure Container Instances a je odstranit, když už budou potřeba.

## <a name="sample-implementation-virtual-kubelet-for-kubernetes"></a>Ukázková implementace: Virtual Kubelet pro Kubernetes

[Virtual Kubelet] [ aci-connector-k8s] projektu ukazuje, jak integrovat platformy Orchestrace kontejnerů pomocí služby Azure Container Instances.

Virtual Kubelet napodobuje Kubernetes [kubelet] [ kubelet-doc] prostřednictvím registrace jako uzel s neomezenou kapacitu a odesílání vytváření [podů] [ pod-doc] jako skupiny kontejnerů ve službě Azure Container Instances.

Konektory pro jiných orchestrátorů může být postavená podobně integrovaných se službou prvkům platformu a výkonné funkce orchestrátoru API díky rychlosti a jednoduchost Správa kontejnerů ve službě Azure Container Instances.

## <a name="next-steps"></a>Další postup

Vytvoření prvního kontejneru služby s využitím Azure Container Instances [příručky rychlý Start](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/