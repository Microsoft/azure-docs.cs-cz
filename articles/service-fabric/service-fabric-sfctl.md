---
title: Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů a podskupin.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 518e60ee92a637533fdf5ab44053d1a1c8757bbe
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645238"
---
# <a name="sfctl"></a>sfctl
Příkazy pro správu Service Fabric clusterů a entit Tato verze je kompatibilní s modulem runtime Service Fabric 6,5.

Příkazy se řídí vzorem slovesa substantivum. Další informace najdete v tématu podskupiny.

## <a name="subgroups"></a>Podskupiny
|Podskupina|Popis|
| --- | --- |
| [použití](service-fabric-sfctl-application.md) | Vytvářejte, odstraňujte a spravujte aplikace a typy aplikací. |
| [chaos](service-fabric-sfctl-chaos.md) | Spuštění, zastavení a hlášení služby chaos test Service. |
| [cluster](service-fabric-sfctl-cluster.md) | Umožňuje vybrat, spravovat a provozovat clustery Service Fabric. |
| [sestavit](service-fabric-sfctl-compose.md) | Vytvářejte, odstraňujte a spravujte aplikace Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Spusťte příkazy související s kontejnerem na uzlu clusteru. |
| [událost](service-fabric-sfctl-events.md) | Načtěte události z úložiště událostí (Pokud je už nainstalovaná služba Eventstoru). |
| [is](service-fabric-sfctl-is.md) | Dotazování a posílání příkazů do služby infrastruktury |
| [mesh](service-fabric-sfctl-mesh.md) | Umožňuje odstraňovat a spravovat aplikace Service Fabric sítě. |
| [uzlu](service-fabric-sfctl-node.md) | Spravujte uzly, které tvoří cluster. |
| [rozdělován](service-fabric-sfctl-partition.md) | Dotazování a Správa oddílů pro libovolnou službu. |
| [property](service-fabric-sfctl-property.md) | Uložení a dotazování vlastností pod názvy Service Fabric. |
| [změněno](service-fabric-sfctl-replica.md) | Spravovat repliky patřící k oddílům služeb. |
| [/min](service-fabric-sfctl-rpm.md) | Dotazování a posílání příkazů do služby správce oprav. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Spravujte samostatné Service Fabric clustery. |
| [službám](service-fabric-sfctl-service.md) | Umožňuje vytvářet, odstraňovat a spravovat služby, typy služeb a balíčky služeb. |
| [možnost](service-fabric-sfctl-settings.md) | Nakonfigurujte nastavení místní pro tuto instanci sfctl. |
| [store](service-fabric-sfctl-store.md) | Proveďte základní operace na úrovni souborů v úložišti imagí clusteru. |

## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).