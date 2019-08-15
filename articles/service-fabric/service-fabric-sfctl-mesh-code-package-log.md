---
title: Azure Service Fabric CLI – sfctl pro záznam o síti – balíček – protokol | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl pro rozhraní příkazového řádku-Package-log.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b1949f87dcdb1e3d9fe8e7fd08d8d8ba3b8203a0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036444"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Načte protokoly pro kontejner zadaného balíčku kódu pro danou repliku služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| získat | Načte protokoly z kontejneru. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl – kód sítě – balíček – načíst protokol
Načte protokoly z kontejneru.

Načte protokoly pro kontejner zadaného balíčku kódu repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Code-Package-Name [povinné] | Název balíčku kódu služby. |
| --název repliky [povinné] | Název repliky Service Fabric. |
| --Service-Name [povinné] | Název služby. |
| --konec | Počet řádků, které se mají zobrazit na konci protokolů Výchozí hodnota je 100. All pro zobrazení úplných protokolů. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další postup
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).