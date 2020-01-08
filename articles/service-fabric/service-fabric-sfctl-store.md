---
title: Azure Service Fabric CLI – úložiště sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro provádění operací na úrovni souborů v úložišti imagí clusteru.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 64a8ea963afa9e20dfafb71db69b2baf66959369
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645255"
---
# <a name="sfctl-store"></a>sfctl store
Proveďte základní operace na úrovni souborů v úložišti imagí clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní existující obsah úložiště imagí. |
| kořen-informace | Načte informace o obsahu v kořenovém adresáři úložiště imagí. |
| etiket | Načte informace o obsahu úložiště imagí. |

## <a name="sfctl-store-delete"></a>odstranění úložiště sfctl
Odstraní existující obsah úložiště imagí.

Odstraní existující obsah úložiště imagí, který se nachází v rámci dané relativní cesty úložiště imagí. Tento příkaz lze použít k odstranění nahraných balíčků aplikace po jejich zřízení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Content-Path [požadováno] | Relativní cesta k souboru nebo složce v úložišti imagí ze svého kořene |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-store-root-info"></a>sfctl úložiště – informace o kořenu
Načte informace o obsahu v kořenovém adresáři úložiště imagí.

Vrátí informace o obsahu úložiště imagí v kořenovém adresáři úložiště imagí.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-store-stat"></a>Statistika úložiště sfctl
Načte informace o obsahu úložiště imagí.

Vrátí informace o obsahu úložiště imagí v zadaném contentPath. Modul contentPath je relativní vzhledem ke kořenovému adresáři úložiště imagí.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Content-Path [požadováno] | Relativní cesta k souboru nebo složce v úložišti imagí ze svého kořene |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

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