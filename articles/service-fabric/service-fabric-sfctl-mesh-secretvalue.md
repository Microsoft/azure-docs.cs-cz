---
title: Azure Service Fabric CLI – sfctl sítě secretvalue
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání a odstranění Service Fabricch secretvaluech sítí.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: a29d32dff9ad51942acb30dd834ad6fbd362ac65
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646105"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Získejte a odstraňte secretvalue prostředky sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou hodnotu pojmenovaného tajného prostředku. |
| list | Vypíše názvy všech hodnot zadaného tajného prostředku. |
| zobrazit | Vypíše zadanou hodnotu tajného prostředku. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl sítě secretvalue odstranit
Odstraní zadanou hodnotu pojmenovaného tajného prostředku.

Odstraní prostředek tajné hodnoty identifikovaný názvem. Název prostředku je obvykle verze přidružená k této hodnotě. Pokud je zadaná hodnota používána, odstranění se nezdaří.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --tajný klíč-name-n [povinné] | Název tajného prostředku |
| --Version-v [povinné] | Název tajné verze |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secretvalue-list"></a>seznam secretvalue mřížky sfctl
Vypíše názvy všech hodnot zadaného tajného prostředku.

Načte informace o všech prostředcích tajných hodnot zadaného tajného prostředku. Tyto informace zahrnují názvy prostředků tajné hodnoty, ale ne skutečné hodnoty.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --tajný klíč-name-n [povinné] | Název tajného prostředku |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl ok secretvalue zobrazit
Vypíše zadanou hodnotu tajného prostředku.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --tajný klíč-name-n [povinné] | Název tajného prostředku |
| --Version-v [povinné] | Název tajné verze |
| --show-Value | Zobrazit skutečnou hodnotu tajné verze |

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