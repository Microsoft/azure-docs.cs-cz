---
title: Azure Service Fabric CLI – sfctl pro záznam o síti – balíček – protokol | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl pro rozhraní příkazového řádku-Package-log.
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
ms.openlocfilehash: d1f0e34389a48b79c049f26e8b04c870f0f1a9a7
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901266"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Načte protokoly pro kontejner zadaného balíčku kódu pro danou repliku služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Čtěte | Načte protokoly z kontejneru. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl – kód sítě – balíček – načíst protokol
Načte protokoly z kontejneru.

Načte protokoly pro kontejner zadaného balíčku kódu repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Code-Package-Name [povinné] | Název balíčku kódu služby. |
| --název repliky [povinné] | Název repliky Service Fabric. |
| --Service-Name [povinné] | Název služby |
| --konec | Počet řádků, které se mají zobrazit na konci protokolů Výchozí hodnota je 100. All pro zobrazení úplných protokolů. |

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