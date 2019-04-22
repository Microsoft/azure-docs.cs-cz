---
title: Další informace o verzích clusterů Azure Service Fabric | Dokumentace Microsoftu
description: Podporované verze clusteru Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: d99000e1682b662f4bceb28096395243c894282f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681603"
---
# <a name="supported-service-fabric-versions"></a>Podporované verze Service Fabric

Ujistěte se, že váš cluster běží vždy s podporovanou verzí Service Fabric. Jak a kdy jsme oznamujeme vydání nové verze aplikace Service Fabric, předchozí verze budou označena k ukončení podpory po minimálně za 60 dní od data. Oznámení nových vydaných verzích [na blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Přečtěte si následující dokumenty na podrobnosti o tom, jak zajistit cluster spuštěna podporovaná verze Service Fabric.

- [Upgrade verze Service Fabric v clusteru Azure](service-fabric-cluster-upgrade.md)
- [Upgrade Service Fabric verze na serveru windows samostatného clusteru](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Podporované verze

V následující tabulce jsou uvedeny podporované verze Service Fabric a jejich podpora koncové datum.

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
| 6.2.* | 6.0.232.* |Menší nebo rovna verzi 3.1 |Říjen 26,2018   |
| 6.3.* | 6.1.480.* |Menší nebo rovna verzi 3.2 |Březen 31,2019  |
| 6.4.* | 6.2.301.* |Menší nebo rovna verze 3.3 |Aktuální verze a proto žádné datum ukončení |

## <a name="supported-operating-systems"></a>Podporované operační systémy

 Následující tabulka uvádí podporované operační systémy podporované verze Service Fabric.

| **Operační systém** | **Verze Fabric nejstarší podporované služby** |
| --- | --- |
| Windows Server 2012 R2 | Všechny verze |
| Windows Server 2016 | Všechny verze |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

