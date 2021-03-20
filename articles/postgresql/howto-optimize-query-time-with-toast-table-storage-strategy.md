---
title: Optimalizace času dotazu pomocí strategie ukládání tabulek informační služby v Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak optimalizovat dobu dotazování s využitím strategie ukládání tabulek informační služby na Azure Database for PostgreSQL jednom serveru.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86116178"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optimalizace času dotazu pomocí strategie ukládání tabulek informační služby 
Tento článek popisuje, jak optimalizovat dobu dotazování s využitím strategie úložiště tabulek s neoptimalizovanými atributy (informační technika).

## <a name="toast-table-storage-strategies"></a>Strategie úložiště tabulek informační zprávy
K ukládání sloupců na disk, který může používat informační zprávy, se používají čtyři různé strategie. Představují různé kombinace mezi kompresí a doplňováním úložiště. Strategii lze nastavit na úrovni datového typu a na úrovni sloupce.
- Možnost **prostý** zabraňuje kompresi nebo dopředné úložiště. Zakáže použití hlaviček s jedním bajtem pro typy varlena. Jednoduchá je jediná možná strategie pro sloupce datových typů, které nemůžou používat informační zprávy.
- **Rozšířená** umožňuje kompresi i dopředné úložiště. Rozšířená je výchozí hodnota pro většinu datových typů, které mohou používat informační zprávy. Při prvním pokusu o kompresi. V případě, že je řádek stále příliš velký, probíhá pokus o nedostatku úložiště.
- **Externí** umožňuje dopředné úložiště, ale ne kompresi. Použití External umožňuje rychlejší operace s podřetězci na šířku textu a na sloupcích. Tato rychlost se dodává s pokutou zvýšeného úložného prostoru. Tyto operace jsou optimalizované tak, aby se načetly jenom požadované části přesahující hodnoty, když se nekomprimuje.
- **Main** umožňuje kompresi, ale ne mimo řádek úložiště. Pro tyto sloupce se stále provádí přesahující úložiště, ale jenom jako poslední. K tomu dochází, pokud neexistuje žádný jiný způsob, jak řádek dostatečně přizpůsobit na stránku.

## <a name="use-toast-table-storage-strategies"></a>Použití INFORMAČNÍch strategií tabulek pro ukládání zpráv
Pokud vaše dotazy přistupují k datovým typům, které můžou používat informační zprávy, zvažte použití hlavní strategie namísto výchozí rozšířené možnosti pro zkrácení časů dotazů. Hlavní nepravidlo vyčerpá mimo řádek úložiště. Pokud vaše dotazy nepřístupují k datovým typům, které můžou používat informační zprávy, může být vhodné tuto rozšířenou možnost zachovat. Větší část řádků hlavní tabulky se vejde do sdílené mezipaměti vyrovnávací paměti, která pomáhá s výkonem.

Pokud máte úlohy, které používají schéma s nejrůznějšími tabulkami a vysokými počty znaků, zvažte použití tabulek informační zprávy PostgreSQL. Ukázková tabulka zákazníků obsahovala více než 350 sloupců s několika sloupci, které rozrostly 255 znaků. Po převedení na hlavní strategii tabulky informační zprávy se jejich srovnávací čas dotazu na 467 sekund zkrátí z 4203 sekund. To je 89 procento vylepšení.

## <a name="next-steps"></a>Další kroky
Předchozí charakteristiky si můžete projít u svých úloh. 

Přečtěte si následující dokumentaci k PostgreSQL: 
- [Kapitola 68, databáze fyzického úložiště](https://www.postgresql.org/docs/current/storage-toast.html) 