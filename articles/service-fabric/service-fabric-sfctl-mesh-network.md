---
title: Azure Service Fabric CLI – síť sítě sfctl | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl Network.
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
ms.openlocfilehash: e92fbc3856bba69db11aba6ad5d85e814bf30a81
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035054"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Získat a odstranit síťové prostředky sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| odstraňovat | Odstraní síťový prostředek. |
| list | Zobrazí seznam všech síťových prostředků. |
| zobrazit | Získá síťový prostředek s daným názvem. |

## <a name="sfctl-mesh-network-delete"></a>odstranění sítě v síti sfctl
Odstraní síťový prostředek.

Odstraní síťový prostředek identifikovaný názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název sítě. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-network-list"></a>seznam sítí sítě sfctl
Zobrazí seznam všech síťových prostředků.

Načte informace o všech síťových prostředcích v dané skupině prostředků. Tyto informace zahrnují popis a další vlastnosti sítě.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-network-show"></a>sfctl síť sítě – zobrazení
Získá síťový prostředek s daným názvem.

Načte informace o síťovém prostředku s daným názvem. Tyto informace zahrnují popis a další vlastnosti sítě.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název sítě. |

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