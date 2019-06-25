---
title: Osvědčené postupy Azure aplikace Service Fabric a clusteru | Dokumentace Microsoftu
description: Doporučené postupy pro správu clusterů Service Fabric a aplikací.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206790"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Osvědčené postupy Azure aplikace Service Fabric a clusteru

Tento článek obsahuje odkazy na doporučené postupy pro správu aplikace Azure Service Fabric a clustery. Důrazně doporučujeme implementovat tyto postupy a optimalizovat spolehlivost vašeho produkčního prostředí. Použijte jednu z [šablony clusteru Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) začít návrhu řešení pro výrobu nebo aktualizovat stávající šablonu začlenit tyto postupy.

## <a name="security"></a>Zabezpečení

* [Osvědčené postupy pro zabezpečení](service-fabric-best-practices-security.md)

## <a name="networking"></a>Sítě

* [Osvědčené postupy pro síť](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Škálování a plánování výpočetních prostředků

* [Osvědčené postupy pro změnu velikosti výpočetní prostředky](service-fabric-best-practices-capacity-scaling.md)
* [COMPUTE plánování kapacity](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktura jako kód

* [Osvědčené postupy při implementaci infrastruktury jako kódu](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

* [Osvědčené postupy pro monitorování clusteru a Diagnostika](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Návrh aplikací

* [Osvědčené postupy pro navrhování aplikací](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Kontrolní seznam

Po implementaci postupů uvedených v předchozích částech, ujistěte se, že jste integrovali osvědčené postupy v seznamu úkolů připravenosti produkčního prostředí:
* [Kontrolní seznam připravenosti produkční Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na počítače s Linuxem nebo virtuální počítače: [Vytvoření clusteru s Linuxem](service-fabric-cluster-creation-via-portal.md)
* Řešení potíží s Service Fabric: [Průvodce odstraňováním potíží](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)