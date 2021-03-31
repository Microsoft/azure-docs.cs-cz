---
title: Azure Service Fabric CLI – úložiště sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro provádění operací na úrovni souborů v úložišti imagí clusteru.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257066"
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

|Argument|Description|
| --- | --- |
| --Content-Path [požadováno] | Relativní cesta k souboru nebo složce v úložišti imagí ze svého kořene |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-store-root-info"></a>sfctl úložiště – informace o kořenu
Načte informace o obsahu v kořenovém adresáři úložiště imagí.

Vrátí informace o obsahu úložiště imagí v kořenovém adresáři úložiště imagí.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-store-stat"></a>Statistika úložiště sfctl
Načte informace o obsahu úložiště imagí.

Vrátí informace o obsahu úložiště imagí v zadaném contentPath. Modul contentPath je relativní vzhledem ke kořenovému adresáři úložiště imagí.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Content-Path [požadováno] | Relativní cesta k souboru nebo složce v úložišti imagí ze svého kořene |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

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
