---
title: Přehled služby Azure a Service Fabric samostatné clustery | Dokumentace Microsoftu
description: Na všechny virtuální počítače nebo počítače se systémem Windows Server nebo Linux můžete vytvořit clustery Service Fabric. To znamená, že máte možnost k nasazení a spuštění aplikace Service Fabric v jakémkoli prostředí, kde se nachází sada systému Windows Server nebo Linux počítačů, které jsou propojeny typu místní – místní, Microsoft Azure, nebo jakýkoli jiný poskytovatel cloudu.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: cf16a01ef1003b73740c05ff45f703a65aaf0acc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961176"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Porovnání služeb Azure a samostatné Service Fabric clusterů v systému Windows Server a Linux
Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Clustery je možné škálovat na tisíce uzlů. Pokud přidáte nové uzly do clusteru Service Fabric znovu vytvoří rovnováhu replik oddílů služby a instance napříč zvýšeného počtu uzlů. Celkově zlepšuje výkon aplikací a snižuje kolize pro přístup k paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu znovu vytvoří rovnováhu replik oddílů a instancí napříč snížený počet uzlů, abyste měli lepší využití hardwaru na každém uzlu.

Service Fabric umožňuje vytvářet clustery Service Fabric na všechny virtuální počítače nebo počítače se systémem Windows Server nebo Linux. To znamená, že budete moci nasazení a spuštění aplikace Service Fabric v jakémkoli prostředí, kam máte sadu Windows Server nebo Linux počítačů, které jsou propojeny, ať už místní, Microsoft Azure, nebo jakýkoli jiný poskytovatel cloudu.

## <a name="benefits-of-clusters-on-azure"></a>Výhody clustery v Azure
V Azure zajišťuje integraci s jinými funkce Azure a služby, které provádí operace a Správa clusteru jednodušší a spolehlivější.

* **Azure portal:** Azure portal umožňuje snadno vytvářet a spravovat clustery.
* **Azure Resource Manageru:** Použití Azure Resource Manageru umožňuje snadnou správu všechny prostředky používané položkou clusteru jako celek a zjednodušuje sledování nákladů a fakturace.
* **Cluster Service Fabric jako prostředek Azure** clusteru A Service Fabric je prostředek Azure, takže lze modelovat, stejně jako ostatní prostředky v Azure.
* **Integrace s infrastrukturou Azure** koordinuje Service Fabric se základní infrastrukturou Azure pro operační systém, sítě a jiné upgradu zvýšit dostupnost a spolehlivost vašich aplikací.  
* **Diagnostika:** V Azure zajišťuje integraci s diagnostikou Azure a službou Log Analytics.
* **Automatické škálování:** Pro clustery v Azure poskytujeme integrované funkce automatického škálování z důvodu škálovací sady virtuálních počítačů. V místní a dalších cloudových prostředích budete muset sestavit vlastní automatické škálování funkce nebo určený počet číslic ručně pomocí rozhraní API, která poskytuje Service Fabric pro škálování clusterů.

## <a name="benefits-of-standalone-clusters"></a>Výhody samostatné clustery
* Můžete libovolně vybrat jakýkoli jiný poskytovatel cloudu pro hostování vašeho clusteru.
* Aplikace Service Fabric, jednou zapsat, lze spustit ve více hostitelských prostředích s minimálním pro žádné změny.
* Informace o vytváření aplikací Service Fabric se přenesou z jednoho hostování prostředí do jiného.
* Provozní prostředí provoz a správu Service Fabric clusterů mělo přes z jednoho prostředí do druhého.
* Dosah široké zákazníka, je bez vazby hostování prostředí omezení.
* Další úroveň spolehlivosti a ochrany proti rozšířených výpadky existuje, protože můžete přesouvat služby do jiného prostředí nasazení Pokud zprostředkovatele datového centra nebo cloudu se nedostupnosti.

## <a name="next-steps"></a>Další postup

* Přečtěte si přehled o [clusterů Service Fabric v Azure](service-fabric-azure-clusters-overview.md)
* Přečtěte si přehled o [samostatných clusterů Service Fabric](service-fabric-standalone-clusters-overview.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)