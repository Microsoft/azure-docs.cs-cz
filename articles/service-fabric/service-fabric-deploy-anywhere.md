---
title: Přehled clusterů Azure a samostatných Service Fabric
description: Clustery Service Fabric můžete vytvořit na libovolném virtuálním počítači nebo na počítačích se systémem Windows Server nebo Linux. To znamená, že můžete nasazovat a spouštět aplikace Service Fabric v jakémkoli prostředí, kde máte sadu počítačů se systémem Windows Server nebo Linux, které jsou propojeny místně, Microsoft Azure nebo s jakýmkoli poskytovatelem cloudu.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576124"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Porovnání clusterů Azure a samostatných Service Fabric v systému Windows Server a Linux

Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se označuje jako uzel clusteru. Clustery se můžou škálovat na tisíce uzlů. Pokud do clusteru přidáte nové uzly, Service Fabric rebilance repliky oddílů služby a instance napříč rostoucím počtem uzlů. Celkový výkon aplikace vylepšuje a kolizí pro přístup k snížení velikosti paměti. Pokud se uzly v clusteru nepoužívají efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyrovnává repliky oddílů a instance napříč sníženým počtem uzlů, aby bylo možné lépe využívat hardware na jednotlivých uzlech.

Service Fabric umožňuje vytváření clusterů Service Fabric na všech virtuálních počítačích nebo počítačích se systémem Windows Server nebo Linux. To znamená, že můžete nasazovat a spouštět aplikace Service Fabric v jakémkoli prostředí, kde máte připojenou sadu počítačů se systémem Windows Server nebo Linux, které jsou propojeny místně, Microsoft Azure nebo s jakýmkoli poskytovatelem cloudu.

## <a name="benefits-of-clusters-on-azure"></a>Výhody clusterů v Azure

V Azure zajišťujeme integraci s dalšími funkcemi a službami Azure, což usnadňuje a spolehlivě zajišťuje provoz a správu clusteru.

* **Azure Portal:** Azure Portal usnadňuje vytváření a správu clusterů.
* **Azure Resource Manager:** Použití Azure Resource Manager umožňuje snadnou správu všech prostředků používaných clusterem jako jednotky a zjednodušuje sledování a účtování nákladů.
* **Service Fabric clusteru jako prostředek Azure** Cluster Service Fabric je prostředek Azure, takže ho můžete modelovat stejným způsobem jako ostatní prostředky v Azure.
* **Integrace s infrastrukturou Azure** Service Fabric koordinuje základní infrastrukturu Azure pro operační systémy, sítě a další upgrady, aby se zlepšila dostupnost a spolehlivost vašich aplikací.  
* **Diagnostika:** V Azure zajišťujeme integraci s nástroji Azure Diagnostics a protokoly Azure Monitor.
* Automatické **škálování:** Pro clustery v Azure poskytujeme integrované funkce automatického škálování prostřednictvím sady škálování virtuálních počítačů. V místním i jiném cloudovém prostředí je nutné vytvořit vlastní funkci automatického škálování nebo škálovat ručně pomocí rozhraní API, které Service Fabric zveřejňuje pro škálování clusterů.

## <a name="benefits-of-standalone-clusters"></a>Výhody samostatných clusterů

* Pro hostování clusteru můžete zvolit libovolného poskytovatele cloudu.
* Po napsání aplikací Service Fabric aplikace může běžet v několika hostitelských prostředích s minimálním počtem změn.
* Znalost vytváření Service Fabricch aplikací přenáší z jednoho hostitelského prostředí na jiný.
* Provozní prostředí pro spouštění a správu Service Fabricch clusterů přenáší z jednoho prostředí do druhého.
* Široká dostupnost zákazníků není ohraničená omezeními prostředí pro hostování.
* Další vrstva spolehlivosti a ochrany proti širšímu výpadku existuje, protože služby můžete přesunout do jiného prostředí nasazení, pokud má datové centrum nebo poskytovatel cloudu nedostupnosti.

## <a name="next-steps"></a>Další kroky

* Přečtěte si přehled [Service Fabric clusterů v Azure](service-fabric-azure-clusters-overview.md)
* Přečtěte si přehled [Service Fabric samostatných clusterů](service-fabric-standalone-clusters-overview.md) .
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)