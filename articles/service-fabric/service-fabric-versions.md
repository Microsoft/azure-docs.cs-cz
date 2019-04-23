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
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998817"
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
| Všechny verze clusteru před 5.3.121 | 5.1.158.* |Menší nebo rovna verzi 2.3 |20. ledna 2017 |
| 5.3.* | 5.1.158.* |Menší nebo rovna verzi 2.3 |24. února 2017 |
| 5.4.* | 5.1.158.* |Menší nebo rovna verze 2.4 |10. května 2017       |
| 5.5.* | 5.4.164.* |Menší nebo rovna verzi 2.5 |Srpen 10,2017    |
| 5.6.* | 5.4.164.* |Menší nebo rovna verzi 2.6 |Říjen 13,2017   |
| 5.7.* | 5.4.164.* |Menší nebo rovna verze 2.7 |15. prosince 2017  |
| 6.0.* | 5.6.205.* |Menší nebo rovna verze 2.8 |30. března 2018     |
| 6.1.* | 5.7.221.* |Menší nebo rovna verzi 3.0 |15. července 2018      |
| 6.2.* | 6.0.232.* |Menší nebo rovna verzi 3.1 |26. října 2018   |
| 6.3.* | 6.1.480.* |Menší nebo rovna verzi 3.2 |Do 31. března 2019  |
| 6.4.* | 6.2.301.* |Menší nebo rovna verze 3.3 |Aktuální verze, tak žádné datum ukončení |

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

