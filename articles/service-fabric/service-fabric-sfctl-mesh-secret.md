---
title: Azure Service Fabric CLI – tajný kód sítě sfctl | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl na mřížku.
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
ms.openlocfilehash: 5e58e6a401cc166c176dc465d58ba9e8a8ed83b0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035958"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Získá a odstraní prostředky tajného klíče sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| odstraňovat | Odstraní prostředek tajného kódu. |
| list | Zobrazí seznam všech tajných prostředků. |
| zobrazit | Získá prostředek tajného kódu se zadaným názvem. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl odstranit tajný kód sítě
Odstraní prostředek tajného kódu.

Odstraní zadaný tajný prostředek a všechny jeho pojmenované hodnoty.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název tajného klíče |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secret-list"></a>sfctl sítě – seznam tajných klíčů
Zobrazí seznam všech tajných prostředků.

Načte informace o všech tajných zdrojích v dané skupině prostředků. Tyto informace obsahují popis a další vlastnosti tajného klíče.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secret-show"></a>zobrazení tajného kódu sítě sfctl
Získá prostředek tajného kódu se zadaným názvem.

Načte informace o prostředku tajného kódu se zadaným názvem. Tyto informace obsahují popis a další vlastnosti tajného klíče.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název tajného klíče |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).