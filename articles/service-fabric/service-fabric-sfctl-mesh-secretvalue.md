---
title: Azure Service Fabric CLI – sfctl sítě secretvalue | Microsoft Docs
description: Popisuje příkazy Service Fabric sfctl CLI secretvalue.
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
ms.openlocfilehash: 96ce4efe00d89c136a0c11d445170b2f67be6fcd
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901179"
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