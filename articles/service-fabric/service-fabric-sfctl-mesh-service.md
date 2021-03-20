---
title: Azure Service Fabric CLI – služba sítě sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání podrobností o službě prostředku aplikace.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 68ea876d9951b49a6683cc74df8b9107fd942e51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245666"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Získejte podrobnosti o službě a seznam služeb prostředku aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| list | Zobrazí seznam všech prostředků služby. |
| show | Získá prostředek služby se zadaným názvem. |

## <a name="sfctl-mesh-service-list"></a>seznam služeb sítě sfctl
Zobrazí seznam všech prostředků služby.

Získá informace o všech službách prostředku aplikace. Tyto informace zahrnují popis a další vlastnosti služby.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-service-show"></a>sfctl síť – zobrazení služby
Získá prostředek služby se zadaným názvem.

Načte informace o prostředku služby s daným názvem. Tyto informace zahrnují popis a další vlastnosti služby.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Name-n [povinné] | Název služby |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
