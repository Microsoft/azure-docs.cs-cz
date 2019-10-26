---
title: Azure Service Fabric CLI – služba sítě sfctl – replika | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl sítě – repliky.
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
ms.openlocfilehash: e8eaa5fd7c9eeeff3f70a949b04d0fbf5f6c388f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901176"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Získá podrobnosti o replice a vypíše repliky dané služby v prostředku aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| list | Zobrazí seznam všech replik služby. |
| zobrazit | Získá danou repliku služby aplikace. |

## <a name="sfctl-mesh-service-replica-list"></a>Služba sítě sfctl – seznam replik
Zobrazí seznam všech replik služby.

Získá informace o všech replikách služby. Tyto informace zahrnují popis a další vlastnosti repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Service-Name [povinné] | Název služby |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-service-replica-show"></a>Služba sítě sfctl – zobrazení repliky
Získá danou repliku služby aplikace.

Načte informace o replice služby se zadaným názvem. Tyto informace zahrnují popis a další vlastnosti repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Name-n [povinné] | Název repliky služby. |
| --Service-Name [povinné] | Název služby |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).