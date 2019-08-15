---
title: Azure Service Fabric CLI – služba sítě sfctl – replika | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl sítě – repliky.
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
ms.openlocfilehash: 6819bb32eecf8477e2c0727b50641858db21c784
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035911"
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
| --Service-Name [povinné] | Název služby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-service-replica-show"></a>Služba sítě sfctl – zobrazení repliky
Získá danou repliku služby aplikace.

Načte informace o replice služby se zadaným názvem. Tyto informace zahrnují popis a další vlastnosti repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Name-n [povinné] | Název repliky služby. |
| --Service-Name [povinné] | Název služby. |

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