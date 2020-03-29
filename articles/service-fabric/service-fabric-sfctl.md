---
title: Azure Service Fabric CLI- sfctl
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů a podskupin.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906219"
---
# <a name="sfctl"></a>sfctl
Příkazy pro správu clusterů a entit Service Fabric. Tato verze je kompatibilní s runtime Service Fabric 7.0.

Příkazy postupujte podle vzoru slovesa spodiny. Další informace naleznete v podskupinách.

## <a name="subgroups"></a>Podskupin
|Podskupina|Popis|
| --- | --- |
| [aplikace](service-fabric-sfctl-application.md) | Vytvářejte, odstraňujte a spravujte aplikace a typy aplikací. |
| [Chaos](service-fabric-sfctl-chaos.md) | Začněte, zastavte a oznamte službu testování chaosu. |
| [Clusteru](service-fabric-sfctl-cluster.md) | Vyberte, spravujte a provozujte clustery Service Fabric. |
| [Skládat](service-fabric-sfctl-compose.md) | Vytvářejte, odstraňujte a spravujte aplikace Docker Compose. |
| [Kontejner](service-fabric-sfctl-container.md) | Spusťte příkazy související s kontejnery v uzlu clusteru. |
| [Události](service-fabric-sfctl-events.md) | Načtěte události z úložiště událostí (pokud je služba EventStore již nainstalována). |
| [is](service-fabric-sfctl-is.md) | Dotazovat a odesílat příkazy do služby infrastruktury. |
| [mesh](service-fabric-sfctl-mesh.md) | Odstraňte a spravujte aplikace Service Fabric Mesh. |
| [Uzel](service-fabric-sfctl-node.md) | Spravujte uzly, které tvoří cluster. |
| [Oddíl](service-fabric-sfctl-partition.md) | Dotazovat a spravovat oddíly pro všechny služby. |
| [Vlastnost](service-fabric-sfctl-property.md) | Ukládat a dotazovat vlastnosti v části Názvy prostředků infrastruktury služeb. |
| [Replika](service-fabric-sfctl-replica.md) | Spravujte repliky, které patří do oddílů služby. |
| [Rpm](service-fabric-sfctl-rpm.md) | Dotazujte a odesílejte příkazy službě správce oprav. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Spravujte samostatné clustery Service Fabric. |
| [služba](service-fabric-sfctl-service.md) | Vytvářejte, odstraňujte a spravujte služby, typy služeb a balíčky služeb. |
| [zdroje dat](service-fabric-sfctl-settings.md) | Nakonfigurujte místní nastavení pro tuto instanci sfctl. |
| [úložiště](service-fabric-sfctl-store.md) | Provádějte základní operace na úrovni souborů v úložišti bitových obrazů clusteru. |

## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) cli service fabric.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).