---
title: Další informace o verzích clusterů Azure Service Fabric | Dokumentace Microsoftu
description: Podporované verze clusteru Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: twhitney
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: TylerMSFT
ms.openlocfilehash: 154869c3e6bcd44a71480a3cf7363537dddcebf9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727677"
---
# <a name="supported-service-fabric-versions"></a>Podporované verze Service Fabric

Ujistěte se, že váš cluster běží vždy s podporovanou verzí Service Fabric. Jak a kdy jsme oznamujeme vydání nové verze aplikace Service Fabric, předchozí verze budou označena k ukončení podpory po minimálně za 60 dní od data. Oznámení nových vydaných verzích [na blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Přečtěte si následující dokumenty na podrobnosti o tom, jak zajistit cluster spuštěna podporovaná verze Service Fabric.

- [Upgrade verze Service Fabric v clusteru Azure ](service-fabric-cluster-upgrade.md)
- [Upgrade Service Fabric verze na serveru windows samostatného clusteru ](service-fabric-cluster-upgrade-windows-server.md)

Tady je seznam podporovaných verzích Service Fabric a jejich podpora koncové datum.

| **Modul runtime Service Fabric v clusteru** | **Můžete upgradovat přímo z verze clusteru** |**Kompatibilní SDK / verze balíčku NuGet** | **Podpora datum ukončení** |
| --- | --- |--- | --- |
| Všechny verze starší než 5.3.121 clusteru | 5.1.158* |Menší nebo rovna verzi 2.3 |20. ledna 2017 |
| 5.3.* | 5.1.158.* |Menší nebo rovna verzi 2.3 |24. února 2017 |
| 5.4.* | 5.1.158.* |Menší nebo rovna verze 2.4 |Může 10,2017       |
| 5.5.* | 5.4.164.* |Menší nebo rovna verzi 2.5 |Srpen 10,2017    |
| 5.6.* | 5.4.164.* |Menší nebo rovna verzi 2.6 |Říjen 13,2017   |
| 5.7.* | 5.4.164.* |Menší nebo rovna verze 2.7 |Prosinec 15,2017  |
| 6.0.* | 5.6.205.* |Menší nebo rovna verze 2.8 |Březen 30,2018     |
| 6.1.* | 5.7.221.* |Menší nebo rovna verzi 3.0 |Červenec 15,2018      |
| 6.2. * | 6.0.232.* |Menší nebo rovna verzi 3.1 |Říjen 26,2018   |
| 6.3. * | 6.1.480.* |Menší nebo rovna verzi 3.2 |Únor 28,2019  |
| 6.4. * | 6.2.301.* |Menší nebo rovna verze 3.3 |Aktuální verze a proto žádné datum ukončení |