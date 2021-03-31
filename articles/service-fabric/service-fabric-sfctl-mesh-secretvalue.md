---
title: Azure Service Fabric CLI – sfctl sítě secretvalue
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání a odstranění Service Fabricch secretvaluech sítí.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245768"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Získejte a odstraňte secretvalue prostředky sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou hodnotu pojmenovaného tajného prostředku. |
| list | Vypíše názvy všech hodnot zadaného tajného prostředku. |
| show | Vypíše zadanou hodnotu tajného prostředku. |

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
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
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
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
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
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
