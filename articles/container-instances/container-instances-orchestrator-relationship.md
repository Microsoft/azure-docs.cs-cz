---
title: Azure instancí kontejnerů a kontejner orchestration
description: Pochopte, jak Azure kontejner instancí interakci s orchestrators kontejneru.
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/23/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 79dcb4be49791e84efa99b36b1c0a0d474a372f4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure instancí kontejnerů a kontejner orchestrators

Kvůli jejich malá velikost a orientaci aplikace je výhodné pro agilní doručení prostředí a na základě mikroslužbu architektury kontejnery. Úlohu automatizace a správa velký počet kontejnerů a jejich vzájemné interakce se označuje jako *orchestration*. Oblíbené kontejneru orchestrators zahrnují Kubernetes, DC/OS a Docker Swarm.

Azure instancí kontejneru obsahuje některé základní funkce plánování orchestration platforem. A při nepokrývá služeb vyšší hodnota, které zajišťují tyto platformy, může být instancí kontejnerů Azure doplňkové k nim. Tento článek popisuje oboru, co zpracuje instancí kontejnerů Azure a jak úplné orchestrators kontejner může pracovat s ním.

## <a name="traditional-orchestration"></a>Tradiční orchestration

Standardní definice orchestration zahrnuje následující úkoly:

- **Plánování**: daného kontejneru image a žádost o prostředek, najít vhodný počítač, na který se má spustit kontejneru.
- **Spřažení/proti-affinity**: určení, že by měl sadu kontejnery spuštěny nedaleko druhou (pro výkon) nebo dostatečně daleko od sebe (pro dostupnost).
- **Sledování stavu**: Podívejte se na chyby kontejneru a automaticky znovu je naplánujte.
- **Převzetí služeb při selhání**: sledovat co běží na každý počítač a změnit plán naplánovaných kontejnery z počítačů se nezdařilo pro uzly v pořádku.
- **Škálování**: Přidání nebo odebrání kontejneru instancí tak, aby odpovídaly vyžádání, ručně nebo automaticky.
- **Sítě**: zadejte překryvné sítě pro spolupráci kontejnery pro komunikaci mezi více počítačů hostitele.
- **Zjišťování služby**: Povolit kontejnery najít navzájem automaticky a to i v případě jejich přechodu mezi hostitele počítače a změnit IP adresy.
- **Koordinované upgradů aplikací**: Správa kontejneru upgrady výpadky aplikací a povolte vrácení zpět, pokud dojde k chybě.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestration s instancemi Azure kontejneru: vrstveného přístupu

Azure instancí kontejnerů umožňuje vrstveného přístupu k orchestration, poskytuje všechny funkce správy a plánování, které jsou nutná k provozování jediný kontejner, současně platformy orchestrator ke správě více kontejneru úloh nad jeho.

Protože základní infrastruktury pro kontejner instancí je spravovaná službou Azure, není potřeba platformě orchestrator zabývat se hledání příslušné hostitelský počítač, na který se má spustit jeden kontejner. Pružnost cloudu zajistí, že než je vždy k dispozici. Místo toho orchestrator můžete soustředit na úlohy, které zjednodušují vývoj architektury více kontejneru, včetně změny a koordinované upgrady.

## <a name="potential-scenarios"></a>Možných scénářů

Sice stále nascent orchestrator integrace s instancemi Azure kontejneru, Očekáváme, že můžou vznikat v několika různých prostředích:

### <a name="orchestration-of-container-instances-exclusively"></a>Výhradně instance Orchestration kontejneru

Protože rychle začít a účtovat pošle druhou, prostředí založené výhradně na kontejner instancí Azure nabízí nejrychlejší způsob začít a jak nakládat s vysoce proměnné úlohy.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinace instancí kontejnerů a kontejnery v virtuální počítače

Pro dlouhodobé, stabilní úlohy Orchestrace kontejnery v clusteru s podporou vyhrazených virtuálních počítačů je obvykle levnější než systémem stejné kontejnery s instancemi Azure kontejneru. Kontejner instancí však nabízí vynikající řešení pro rychlé rozšiřování a smluvní vaší celkovou kapacitu, jak nakládat s neočekávanou nebo krátkodobou špičky využití.

Místo škálování počtu virtuálních počítačů v clusteru, pak nasazení další kontejnery na tyto počítače, orchestrator můžete jednoduše naplánovat další kontejnery v kontejneru instance Azure a odstranit, když jsou už potřeba.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Ukázka implementace: Azure kontejner instancí konektor pro Kubernetes

K předvedení, jak integrovat platformy orchestration kontejner s instancemi Azure kontejneru, jsme spustili sestavování [ukázka konektor pro Kubernetes][aci-connector-k8s].

Konektor pro Kubernetes napodobuje [kubelet] [ kubelet-doc] registrace jako uzel neomezená kapacitu a odeslání vytvoření [pracovními stanicemi soustředěnými kolem] [ pod-doc] kontejneru a skupiny v Azure kontejner instancí.

Konektory pro ostatní orchestrators může být postavená které podobně integrovat platformy primitiv kombinovat napájení nástroje orchestrator rozhraní API s rychlostí a zjednodušení správy kontejnerů v Azure kontejner instancí.

> [!WARNING]
> Konektor nástroje ACI pro Kubernetes *experimentální* a neměl by se používat v produkčním prostředí.

## <a name="next-steps"></a>Další postup

Vytvoření vaší první kontejneru s instancí kontejnerů Azure pomocí [Průvodce rychlým zahájením](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/