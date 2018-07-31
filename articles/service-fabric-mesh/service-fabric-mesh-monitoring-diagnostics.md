---
title: Monitorování a Diagnostika aplikace Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Další informace o monitorování a Diagnostika aplikace v Service Fabric sítě v Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f857c23b5500bc7790a0ff7fcf81eaec51f37c9
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358268"
---
# <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika
Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Monitorování a Diagnostika sítě pro Service Fabric je rozdělená na tři hlavní typy diagnostických dat:

- Protokoly aplikací – jsou definované jako protokoly z kontejnerizované aplikace, podle toho, jak máte instrumentované aplikace (například protokoly dockeru)
- Události platformy – události z relevantní pro vaši operaci kontejner sítě platformy aktuálně včetně kontejneru aktivaci, deaktivaci a ukončení.
- Metriky kontejnerů - metriky využití a výkonu prostředků pro kontejnery (docker statistiky)

Tento článek popisuje možnosti monitorování a diagnostiky pro nejnovější dostupná verze preview.

## <a name="application-logs"></a>Protokoly aplikací

Zobrazit protokoly dockeru na nasazenými kontejnery, na základě za kontejner. Každý kontejner v aplikační model sítě pro Service Fabric, je balíček kódu v aplikaci. Přidružených protokolů se balíček kódu zobrazíte následujícím příkazem:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Příkaz "az mřížky repliku služby" můžete získat název repliky. Replika je zvyšování čísla z 0.*

Zde je, jak to vypadá k zobrazení protokolů z kontejnerů VotingWeb.Code z hlasovací aplikace:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Další postup
Další informace o Service Fabric sítě, naleznete v přehledu:
- [Přehled Service Fabric mřížky](service-fabric-mesh-overview.md)
