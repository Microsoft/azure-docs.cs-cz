---
title: Přehled clusterů Azure a samostatných service fabric
description: Clustery Service Fabric můžete vytvořit na všech virtuálních počítačích nebo počítačích se systémem Windows Server nebo Linux. To znamená, že můžete nasadit a spustit aplikace Service Fabric v jakémkoli prostředí, kde máte sadu počítačů se systémem Windows Server nebo Linux, které jsou propojené místně, Microsoft Azure nebo s jakýmkoli poskytovatelem cloudu.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751157"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Porovnání clusterů Azure a samostatných service fabric na Windows Serveru a Linuxu

Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Clustery lze škálovat na tisíce uzlů. Pokud do clusteru přidáte nové uzly, service fabric znovu vyvažuje repliky a instance oddílů služby napříč zvýšeným počtem uzlů. Celkový výkon aplikace zlepšuje a tvrzení o přístupu k paměti snižuje. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyvažuje repliky oddílů a instance přes snížený počet uzlů, aby bylo možné lépe využít hardware v každém uzlu.

Service Fabric umožňuje vytváření clusterů Service Fabric na všech virtuálních počítačích nebo počítačích se systémem Windows Server nebo Linux. To znamená, že můžete nasadit a spustit aplikace Service Fabric v jakémkoli prostředí, kde máte sadu počítačů se systémem Windows Server nebo Linux, které jsou vzájemně propojené, ať už místně, v Microsoft Azure nebo s jakýmkoli poskytovatelem cloudu.

## <a name="benefits-of-clusters-on-azure"></a>Výhody clusterů v Azure

V Azure poskytujeme integraci s dalšími funkcemi a službami Azure, což usnadňuje a zspolehlivuje provoz a správu clusteru.

* **Portál Azure:** Portál Azure usnadňuje vytváření a správu clusterů.
* **Správce prostředků Azure:** Použití Azure Resource Manager umožňuje snadnou správu všech prostředků používaných clusteru jako jednotky a zjednodušuje sledování nákladů a fakturaci.
* **Cluster Prostředků infrastruktury služeb jako prostředek Azure** Cluster Service Fabric je prostředek Azure, takže ho můžete modelovat stejně jako ostatní prostředky v Azure.
* **Integrace s infrastrukturou Azure** Service Fabric koordinuje základní infrastrukturu Azure pro operační systém, síť a další upgrady ke zlepšení dostupnosti a spolehlivosti vašich aplikací.  
* **Diagnostika:** V Azure poskytujeme integraci s diagnostikou Azure a protokoly Azure Monitoru.
* **Automatické škálování:** Pro clustery v Azure poskytujeme integrované funkce automatického škálování díky škálovacím sestavám virtuálních strojů. V místních a jiných cloudových prostředích je nutné vytvořit vlastní funkci automatického škálování nebo škálovat ručně pomocí api, která service fabric zveřejňuje pro škálování clusterů.

## <a name="benefits-of-standalone-clusters"></a>Výhody samostatných clusterů

* Můžete si vybrat libovolného poskytovatele cloudu, který bude hostovat váš cluster.
* Service Fabric aplikace, po napsání, lze spustit ve více hostitelských prostředích s minimálními až žádné změny.
* Znalost vytváření aplikací Service Fabric se přenáší z jednoho hostitelského prostředí do druhého.
* Provozní zkušenosti s provozem a správou clusterů Service Fabric se přenesou z jednoho prostředí do druhého.
* Široký dosah zákazníků je neomezený omezením hostitelského prostředí.
* Existuje další vrstva spolehlivosti a ochrany proti rozsáhlým výpadkům, protože služby můžete přesunout do jiného prostředí nasazení, pokud má datové centrum nebo poskytovatel cloudu výpadek.

## <a name="next-steps"></a>Další kroky

* Přečtěte si přehled [clusterů Service Fabric v Azure](service-fabric-azure-clusters-overview.md)
* Přečtěte si přehled [samostatných clusterů Service Fabric](service-fabric-standalone-clusters-overview.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)