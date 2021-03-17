---
title: Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů a podskupin.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fc317345155a6807a20d342e2cefd0701b20f180
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257049"
---
# <a name="sfctl"></a>sfctl
Příkazy pro správu Service Fabric clusterů a entit Tato verze je kompatibilní s modulem runtime Service Fabric 7,0.

Příkazy se řídí vzorem slovesa substantivum. Další informace najdete v tématu podskupiny.

## <a name="subgroups"></a>Podskupiny
|Podskupina|Description|
| --- | --- |
| [aplikace](service-fabric-sfctl-application.md) | Vytvářejte, odstraňujte a spravujte aplikace a typy aplikací. |
| [chaos](service-fabric-sfctl-chaos.md) | Spuštění, zastavení a hlášení služby chaos test Service. |
| [služby](service-fabric-sfctl-cluster.md) | Umožňuje vybrat, spravovat a provozovat clustery Service Fabric. |
| [sestavit](service-fabric-sfctl-compose.md) | Vytvářejte, odstraňujte a spravujte aplikace Docker Compose. |
| [container (kontejner)](service-fabric-sfctl-container.md)  | Spusťte příkazy související s kontejnerem na uzlu clusteru. |
| [událost](service-fabric-sfctl-events.md) | Načtěte události z úložiště událostí (Pokud je už nainstalovaná služba Eventstoru). |
| [dojde](service-fabric-sfctl-is.md) | Dotazování a posílání příkazů do služby infrastruktury |
| [dílce](service-fabric-sfctl-mesh.md) | Umožňuje odstraňovat a spravovat aplikace Service Fabric sítě. |
| [uzlu](service-fabric-sfctl-node.md) | Spravujte uzly, které tvoří cluster. |
| [rozdělován](service-fabric-sfctl-partition.md) | Dotazování a Správa oddílů pro libovolnou službu. |
| [majetek](service-fabric-sfctl-property.md) | Uložení a dotazování vlastností pod názvy Service Fabric. |
| [změněno](service-fabric-sfctl-replica.md) | Spravovat repliky patřící k oddílům služeb. |
| [/min](service-fabric-sfctl-rpm.md) | Dotazování a posílání příkazů do služby správce oprav. |
| [SA – cluster](service-fabric-sfctl-sa-cluster.md) | Spravujte samostatné Service Fabric clustery. |
| [službám](service-fabric-sfctl-service.md) | Umožňuje vytvářet, odstraňovat a spravovat služby, typy služeb a balíčky služeb. |
| [zdroje dat](service-fabric-sfctl-settings.md) | Nakonfigurujte nastavení místní pro tuto instanci sfctl. |
| [úložiště](service-fabric-sfctl-store.md) | Proveďte základní operace na úrovni souborů v úložišti imagí clusteru. |

## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
