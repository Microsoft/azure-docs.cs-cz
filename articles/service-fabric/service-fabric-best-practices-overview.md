---
title: Osvědčené postupy pro aplikace a Cluster Service Fabric pro Azure
description: Osvědčené postupy a pokyny k návrhu pro správu clusterů, aplikací a služeb s využitím Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551773"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Osvědčené postupy pro aplikace a Cluster Service Fabric pro Azure

Tento článek obsahuje odkazy na osvědčené postupy pro správu aplikací a clusterů Azure Service Fabric. Důrazně doporučujeme, abyste tyto postupy implementovali k optimalizaci spolehlivosti provozního prostředí. Pomocí jedné z [Service Fabric šablon clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates) můžete začít navrhovat produkční řešení nebo aktualizovat stávající šablonu, aby tyto postupy zahrnovala.

## <a name="security"></a>Zabezpečení

* [Osvědčené postupy pro zabezpečení](service-fabric-best-practices-security.md)

## <a name="networking"></a>Sítě

* [Osvědčené postupy pro síťové služby](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Škálování a plánování výpočetních prostředků

* [Osvědčené postupy pro škálování na výpočetní výkon](service-fabric-best-practices-capacity-scaling.md)
* [Plánování kapacity výpočtů](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastruktura jako kód

* [Osvědčené postupy pro implementaci infrastruktury jako kódu](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

* [Osvědčené postupy pro monitorování a diagnostiku clusterů](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Návrh aplikací

* [Osvědčené postupy pro návrh aplikace](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Kontrolní seznam

Po implementaci postupů navrhovaných v předchozích částech se ujistěte, že jste provedli integraci všech osvědčených postupů v kontrolním seznamu připravenosti na provoz:
* [Kontrolní seznam připravenosti na produkci pro Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvoření clusteru se systémem Linux](service-fabric-cluster-creation-via-portal.md)
* Řešení potíží s Service Fabric: [Průvodci odstraňováním potíží](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)