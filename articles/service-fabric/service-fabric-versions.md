---
title: Podporované verze clusteru v Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o verzích clusterů ve službě Azure Service Fabric.
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
ms.date: 06/10/2019
ms.author: aljo
ms.openlocfilehash: 8a548bce6da8b3bae004b0be4f6e8f09c9894025
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060339"
---
# <a name="supported-service-fabric-versions"></a>Podporované verze Service Fabric

Zajistěte, aby že váš cluster běží vždy s podporovanou verzí Azure Service Fabric. Minimálně 60 dní poté, co jsme oznamujeme vydání nové verze aplikace Service Fabric, podporu pro předchozí verze skončí. Oznámení o nových verzích najdete v [blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Přečtěte si podrobnosti o tom, jak zajistit cluster spuštěna podporovaná verze Service Fabric v následujících dokumentech:

- [Upgrade clusteru Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade verze Service Fabric, která běží na samostatného clusteru Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Podporované verze

Následující tabulka uvádí verze aplikace Service Fabric a jejich podpora koncové datum.

| Modul runtime Service Fabric v clusteru | Můžete upgradovat přímo z verze clusteru |Kompatibilní verze balíčku sady SDK nebo NuGet | Ukončení podpory |
| --- | --- |--- | --- |
| Všechny verze clusteru před 5.3.121 | 5.1.158.* |Menší nebo rovna verzi 2.3 |20\. ledna 2017 |
| 5.3.* | 5.1.158.* |Menší nebo rovna verzi 2.3 |24\. února 2017 |
| 5.4.* | 5.1.158.* |Menší nebo rovna verze 2.4 |10\. května 2017       |
| 5.5.* | 5.4.164.* |Menší nebo rovna verzi 2.5 |Srpen 10,2017    |
| 5.6.* | 5.4.164.* |Menší nebo rovna verzi 2.6 |Říjen 13,2017   |
| 5.7.* | 5.4.164.* |Menší nebo rovna verze 2.7 |15\. prosince 2017  |
| 6.0.* | 5.6.205.* |Menší nebo rovna verze 2.8 |30\. března 2018     |
| 6.1.* | 5.7.221.* |Menší nebo rovna verzi 3.0 |15\. července 2018      |
| 6.2.* | 6.0.232.* |Menší nebo rovna verzi 3.1 |26\. října 2018   |
| 6.3.* | 6.1.480.* |Menší nebo rovna verzi 3.2 |Do 31. března 2019  |
| 6.4.* | 6.2.301.* |Menší nebo rovna verze 3.3 |15\. září 2019 |
| 6.5.* | 6.4.617.* |Menší nebo rovna verzi 3.4 |Aktuální verze, tak žádné datum ukončení |

## <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí podporované operační systémy podporované verze Service Fabric.

| Operační systém | Nejdříve podporovanou verzi Service Fabric |
| --- | --- |
| Windows Server 2012 R2 | Všechny verze |
| Windows Server 2016 | Všechny verze |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>Názvy podporovaná verze

Následující tabulka uvádí názvy verzí Service Fabric a jejich odpovídající čísla verzí.

| Název verze | Číslo verze Windows | Číslo verze Linuxu |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | Není k dispozici |
| 5.3 CU1 | 5.3.204.9494 | Není k dispozici |
| 5.3 KUMULATIVNÍ AKTUALIZACI 2 | 5.3.301.9590 | Není k dispozici |
| 5.3 CU3 | 5.3.311.9590 | Není k dispozici |
| 5.4 KUMULATIVNÍ AKTUALIZACI 2 | 5.4.164.9494 | Není k dispozici |
| 5.5 CU1 | 5.5.216.0    | Není k dispozici |
| 5.5 KUMULATIVNÍ AKTUALIZACI 2 | 5.5.219.0    | Není k dispozici |
| 5.5 CU3 | 5.5.227.0    | Není k dispozici |
| 5.5 CU4 | 5.5.232.0    | Není k dispozici |
| 5.6 RTO | 5.6.204.9494 | Není k dispozici |
| 5.6 KUMULATIVNÍ AKTUALIZACI 2 | 5.6.210.9494 | Není k dispozici |
| 5.6 CU3 | 5.6.220.9494 | Není k dispozici |
| 5.7 RTO | 5.7.198.9494 | Není k dispozici |
| 5.7 CU4 | 5.7.221.9494 | Není k dispozici |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 KUMULATIVNÍ AKTUALIZACI 2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | Není k dispozici |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 KUMULATIVNÍ AKTUALIZACI 2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 KUMULATIVNÍ AKTUALIZACI 2 | 6.4.622.9590 | Není k dispozici |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | Není k dispozici |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |