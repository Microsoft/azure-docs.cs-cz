---
title: Azure Service Fabric CLI – brána sítě sfctl | Microsoft Docs
description: Popisuje příkazy sfctl Service Fabric rozhraní příkazového řádku služby CLI.
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
ms.openlocfilehash: b8acb23b274e6062ae3c50ab80220ab93297037e
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036426"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Získejte a odstraňte prostředky brány sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| odstraňovat | Odstraní prostředek brány. |
| list | Zobrazí seznam všech prostředků brány. |
| zobrazit | Získá prostředek brány s daným názvem. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl sítě pro bránu DELETE
Odstraní prostředek brány.

Odstraní prostředek brány identifikovaný názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název prostředku brány |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-gateway-list"></a>seznam bran sítě sfctl
Zobrazí seznam všech prostředků brány.

Načte informace o všech prostředcích brány v dané skupině prostředků. Tyto informace zahrnují popis a další vlastnosti brány.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-gateway-show"></a>Zobrazit bránu sítě sfctl
Získá prostředek brány s daným názvem.

Načte informace o prostředku brány s daným názvem. Tyto informace zahrnují popis a další vlastnosti brány.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název prostředku brány |

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