---
title: Osvědčené postupy pro aplikace Azure Service Fabric a clustery
description: Doporučené postupy a aspekty návrhu pro správu clusterů, aplikací a služeb pomocí Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551773"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Osvědčené postupy pro aplikace Azure Service Fabric a clustery

Tento článek obsahuje odkazy na osvědčené postupy pro správu aplikací a clusterů Azure Service Fabric. Důrazně doporučujeme implementovat tyto postupy k optimalizaci spolehlivosti vašeho produkčního prostředí. Pomocí jedné ze [šablon clusteru Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) můžete začít navrhovat produkční řešení nebo aktualizovat stávající šablonu tak, aby tyto postupy zahrnovala.

## <a name="security"></a>Zabezpečení

* [Doporučené postupy pro zabezpečení](service-fabric-best-practices-security.md)

## <a name="networking"></a>Síťové služby

* [Doporučené postupy pro vytváření sítí](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Škálování a plánování výpočetních prostředků

* [Doporučené postupy pro škálování výpočetních prostředků](service-fabric-best-practices-capacity-scaling.md)
* [Plánování výpočetní kapacity](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktura jako kód

* [Osvědčené postupy pro implementaci infrastruktury jako kódu](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

* [Doporučené postupy pro monitorování a diagnostiku clusteru](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Návrh aplikací

* [Doporučené postupy pro návrh aplikace](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Kontrolní seznam

Po implementaci postupů navržených v předchozích částech se ujistěte, že jste do kontrolního seznamu připravenosti na výrobu integrovali všechny osvědčené postupy:
* [Kontrolní seznam připravenosti produkčního prostředí Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo v počítačích se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Systém Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích s [Linuxem: Vytvoření linuxového clusteru](service-fabric-cluster-creation-via-portal.md)
* Poradce při potížích s prostředků na řešení potíží: [Příručky pro řešení potíží](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)