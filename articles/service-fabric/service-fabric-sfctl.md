---
title: Azure Service Fabric CLI - sfctl | Dokumentace Microsoftu
description: Popisuje sfctl příkazy Service Fabric CLI.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: b6e3c769307f037c9f31dbb3f1a86675145ec1f6
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495403"
---
# <a name="sfctl"></a>sfctl
Příkazy pro správu clusterů Service Fabric a entity. Tato verze není kompatibilní s modulem runtime Service Fabric 6.3.

Příkazy mají tvar sloveso-podstatné jméno. Další informace najdete v tématu podskupiny.

## <a name="subgroups"></a>Podskupiny
|Podskupina|Popis|
| --- | --- |
| [Aplikace](service-fabric-sfctl-application.md) | Vytvářet, odstraňovat a spravovat aplikace a typy aplikací. |
| [chaosu](service-fabric-sfctl-chaos.md) | Spuštění, zastavení a vytváření sestav o tento Chaos se dá testovat službu. |
| [Clusteru](service-fabric-sfctl-cluster.md) | Vyberte, spravovat a provozovat clustery Service Fabric. |
| [Compose](service-fabric-sfctl-compose.md) | Vytvářet, odstraňovat a spravovat aplikace Docker Compose. |
| [Kontejner](service-fabric-sfctl-container.md) | Spusťte příkazy související s kontejneru na uzlu clusteru. |
| [je](service-fabric-sfctl-is.md) | Dotazování a posílání příkazů do služeb infrastruktury. |
| [Uzel](service-fabric-sfctl-node.md) | Spravujte uzly, které tvoří cluster. |
| [oddíl](service-fabric-sfctl-partition.md) | Dotazování a správu oddílů pro libovolnou službu. |
| [Vlastnost](service-fabric-sfctl-property.md) | Vlastnosti v Service Fabric názvy Store a dotazu. |
| [repliky](service-fabric-sfctl-replica.md) | Spravujte repliky, které patří do oddílů služby. |
| [ot. / min](service-fabric-sfctl-rpm.md) | Dotazování a posílání příkazů do service Manageru opravit. |
| [přidružení zabezpečení clusteru](service-fabric-sfctl-sa-cluster.md) | Správa samostatné clustery Service Fabric. |
| [Služba](service-fabric-sfctl-service.md) | Vytvářet, odstraňovat a spravovat služby, typů služeb a balíčky služeb. |
| [úložiště](service-fabric-sfctl-store.md) | Provedení operace na úrovni základní soubor na úložiště imagí clusteru. |

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).