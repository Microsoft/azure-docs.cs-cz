---
title: Azure Service Fabric CLI – sfctl | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 7e7fc7bbc65e92960d7839f6531ef1f7c1935ed3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900881"
---
# <a name="sfctl"></a>sfctl
Příkazy pro správu Service Fabric clusterů a entit Tato verze je kompatibilní s modulem runtime Service Fabric 6,5.

Příkazy se řídí vzorem slovesa substantivum. Další informace najdete v tématu podskupiny.

## <a name="subgroups"></a>Podskupiny
|Podskupina|Popis|
| --- | --- |
| [použití](service-fabric-sfctl-application.md) | Vytvářejte, odstraňujte a spravujte aplikace a typy aplikací. |
| [chaos](service-fabric-sfctl-chaos.md) | Spuštění, zastavení a hlášení služby chaos test Service. |
| [služby](service-fabric-sfctl-cluster.md) | Umožňuje vybrat, spravovat a provozovat clustery Service Fabric. |
| [sestavit](service-fabric-sfctl-compose.md) | Vytvářejte, odstraňujte a spravujte aplikace Docker Compose. |
| [vnitřního](service-fabric-sfctl-container.md) | Spusťte příkazy související s kontejnerem na uzlu clusteru. |
| [událost](service-fabric-sfctl-events.md) | Načtěte události z úložiště událostí (Pokud je už nainstalovaná služba Eventstoru). |
| [dojde](service-fabric-sfctl-is.md) | Dotazování a posílání příkazů do služby infrastruktury |
| [mesh](service-fabric-sfctl-mesh.md) | Umožňuje odstraňovat a spravovat aplikace Service Fabric sítě. |
| [uzlu](service-fabric-sfctl-node.md) | Spravujte uzly, které tvoří cluster. |
| [rozdělován](service-fabric-sfctl-partition.md) | Dotazování a Správa oddílů pro libovolnou službu. |
| [majetek](service-fabric-sfctl-property.md) | Uložení a dotazování vlastností pod názvy Service Fabric. |
| [změněno](service-fabric-sfctl-replica.md) | Spravovat repliky patřící k oddílům služeb. |
| [/min](service-fabric-sfctl-rpm.md) | Dotazování a posílání příkazů do služby správce oprav. |
| [SA – cluster](service-fabric-sfctl-sa-cluster.md) | Spravujte samostatné Service Fabric clustery. |
| [službám](service-fabric-sfctl-service.md) | Umožňuje vytvářet, odstraňovat a spravovat služby, typy služeb a balíčky služeb. |
| [možnost](service-fabric-sfctl-settings.md) | Nakonfigurujte nastavení místní pro tuto instanci sfctl. |
| [uchovávat](service-fabric-sfctl-store.md) | Proveďte základní operace na úrovni souborů v úložišti imagí clusteru. |

## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).