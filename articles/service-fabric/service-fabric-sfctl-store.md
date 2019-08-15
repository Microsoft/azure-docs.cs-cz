---
title: Azure Service Fabric CLI – úložiště sfctl | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl Store.
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
ms.openlocfilehash: ccd9e8323f6e0de7b81c7600e7828e4858c51201
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035862"
---
# <a name="sfctl-store"></a>sfctl store
Proveďte základní operace na úrovni souborů v úložišti imagí clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| odstraňovat | Odstraní existující obsah úložiště imagí. |
| kořen-informace | Načte informace o obsahu v kořenovém adresáři úložiště imagí. |
| etiket | Načte informace o obsahu úložiště imagí. |

## <a name="sfctl-store-delete"></a>odstranění úložiště sfctl
Odstraní existující obsah úložiště imagí.

Odstraní existující obsah úložiště imagí, který se nachází v rámci dané relativní cesty úložiště imagí. Tento příkaz lze použít k odstranění nahraných balíčků aplikace po jejich zřízení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Content-Path [požadováno] | Relativní cesta k souboru nebo složce v úložišti imagí ze svého kořene |
| --Timeout-t | Časový limit serveru v sekundách.  Výchozí\: hodnota je 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-store-root-info"></a>sfctl úložiště – informace o kořenu
Načte informace o obsahu v kořenovém adresáři úložiště imagí.

Vrátí informace o obsahu úložiště imagí v kořenovém adresáři úložiště imagí.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Timeout-t | Časový limit serveru v sekundách.  Výchozí\: hodnota je 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-store-stat"></a>Statistika úložiště sfctl
Načte informace o obsahu úložiště imagí.

Vrátí informace o obsahu úložiště imagí v zadaném contentPath. Modul contentPath je relativní vzhledem ke kořenovému adresáři úložiště imagí.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Content-Path [požadováno] | Relativní cesta k souboru nebo složce v úložišti imagí ze svého kořene |
| --Timeout-t | Časový limit serveru v sekundách.  Výchozí\: hodnota je 60. |

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