---
title: Optimalizace doby dotazu pomocí strategie úložiště tabulky TOAST v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak optimalizovat čas dotazu pomocí strategie úložiště tabulky TOAST na Azure Database for PostgreSQL - Single Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65066983"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optimalizace času dotazu pomocí strategie úložiště tabulky TOAST 
Tento článek popisuje, jak optimalizovat časy dotazů pomocí strategie ukládání tabulek s nadrozměrnými atributy (TOAST).

## <a name="toast-table-storage-strategies"></a>STRATEGIE SKLADOVÁNÍ STOLU TOAST
Čtyři různé strategie se používají k ukládání sloupců na disk, který lze použít TOAST. Představují různé kombinace mezi kompresí a mimořádek úložiště. Strategii lze nastavit na úrovni datového typu a na úrovni sloupce.
- **Plain** zabraňuje kompresi nebo mimo řádek úložiště. Zakáže použití jednobajtových hlaviček pro typy varlena. Prostý je jediná možná strategie pro sloupce datových typů, které nelze použít TOAST.
- **Extended** umožňuje kompresi i out-of-line úložiště. Extended je výchozí pro většinu datových typů, které lze použít TOAST. Nejprve se pokusí o kompresi. Pokud je řádek stále příliš velký, dojde k pokusu o mimořádkové úložiště.
- **Externí** umožňuje mimo-of-line skladování, ale ne komprese. Použití Externí umožňuje operace podřetězce na široký text a bytea sloupce rychleji. Tato rychlost přichází s trestem zvýšeného úložného prostoru. Tyto operace jsou optimalizovány tak, aby načítaly pouze požadované části out-of-line hodnoty, když není komprimována.
- **Hlavní** umožňuje kompresi, ale ne out-of-line skladování. Pro tyto sloupce se stále provádí mimořádkové úložiště, ale pouze jako poslední možnost. K tomu dochází, když neexistuje žádný jiný způsob, jak vytvořit řádek dostatečně malý, aby se vešel na stránku.

## <a name="use-toast-table-storage-strategies"></a>Použití strategií ukládání stolu TOAST
Pokud vaše dotazy přístup k datovým typům, které můžete použít TOAST, zvažte použití hlavní strategie namísto výchozí rozšířené možnost snížit časy dotazů. Hlavní nevylučuje mimo-of-line skladování. Pokud vaše dotazy nemají přístup k datovým typům, které můžete použít TOAST, může být výhodné zachovat možnost Rozšířené. Větší část řádků hlavní tabulky se vejde do sdílené mezipaměti vyrovnávací paměti, což pomáhá výkonu.

Pokud máte zatížení, které používá schéma s širokými tabulkami a vysokým počtu znaků, zvažte použití tabulek PostgreSQL TOAST. Příklad tabulky zákazníků měl větší než 350 sloupců s několika sloupci, které překlenuly 255 znaků. Poté, co byl převeden na hlavní strategii tabulky TOAST, se jejich čas srovnávacího dotazu snížil ze 4203 sekund na 467 sekund. To je 89% zlepšení.

## <a name="next-steps"></a>Další kroky
Zkontrolujte pracovní vytížení pro předchozí charakteristiky. 

Projděte si následující postgreSQL dokumentaci: 
- [Kapitola 68, Fyzické úložiště databáze](https://www.postgresql.org/docs/current/storage-toast.html) 