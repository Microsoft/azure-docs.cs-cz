---
title: Azure Service Fabric CLI – aplikace sfctl pro síť | Microsoft Docs
description: Service Fabric popisuje příkazy aplikace sfctl CLI.
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
ms.openlocfilehash: 18cf1107ed86915f96dbc92d588a6ebedab530b4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901482"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
Získejte a odstraňte prostředky aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní prostředek aplikace. |
| list | Zobrazí seznam všech prostředků aplikace. |
| zobrazit | Získá prostředek aplikace se zadaným názvem. |

## <a name="sfctl-mesh-app-delete"></a>odstranění aplikace sfctl Mesh
Odstraní prostředek aplikace.

Odstraní prostředek aplikace identifikovaný názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název aplikace |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-app-list"></a>seznam aplikací sfctl pro síť
Zobrazí seznam všech prostředků aplikace.

Získá informace o všech prostředcích aplikace v dané skupině prostředků. Tyto informace zahrnují popis a další vlastnosti aplikace.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-app-show"></a>zobrazení aplikace sfctl Mesh
Získá prostředek aplikace se zadaným názvem.

Načte informace o prostředku aplikace s daným názvem. Tyto informace zahrnují popis a další vlastnosti aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název aplikace |

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