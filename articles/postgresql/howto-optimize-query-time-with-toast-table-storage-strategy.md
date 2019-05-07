---
title: Optimalizace času dotaz s použitím strategie úložiště tabulku informačních zpráv ve službě Azure Database for PostgreSQL – jeden Server
description: Tento článek popisuje, jak optimalizovat době zpracování dotazu se strategií úložiště tabulku informačních zpráv ve službě Azure Database for PostgreSQL – jeden Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65066983"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optimalizace času dotazu s strategie úložiště tabulky informační zprávy 
Tento článek popisuje, jak optimalizovat dotaz časy s strategie úložiště zvětšená atribut technika (informační zpráva) tabulky úložiště.

## <a name="toast-table-storage-strategies"></a>Strategie úložiště tabulky informační zprávy
Čtyři různé strategie se používají k ukládání sloupce na disku, který můžete použít informační zprávy. Představují různé kombinace mezi komprese a úložiště mimo řádek. Strategie může být nastaven na úrovni datového typu na úrovni sloupce.
- **Prostý** brání úložiště komprese nebo mimo řádek. Zakáže použití hlaviček jednobajtové varlena typů. Prostý je možná pouze strategie pro sloupce datové typy, které nelze použít informační zprávy.
- **Rozšířené** umožňuje úložiště komprese a mimo řádek. Rozšířené je výchozí nastavení pro většinu typů dat, které můžete použít informační zprávy. Nejprve se vyzkouší komprese. Úložiště mimo řádek dojde k pokusu o Pokud řádku je pořád příliš velký.
- **Externí** umožňuje úložiště mimo řádek, ale ne komprese. Použití externích díky podřetězec operace na široké text bytea sloupce a rychlejší. Rychlost se dodává s penalizace větší úložný prostor. Tyto operace jsou optimalizovány pro načtení pouze požadované části hodnoty mimo řádek, když není komprimována.
- **Hlavní** umožňuje kompresi, ale ne out řádku úložiště. Úložiště mimo řádek je stále prováděny pro tyto sloupce, ale pouze jako poslední možnost. K tomu dochází, když neexistuje jiný způsob, jak vytvořit řádek dostatečně malá, aby se vešly na stránku.

## <a name="use-toast-table-storage-strategies"></a>Použití strategie úložiště tabulky informační zprávy
Vaše dotazy získat přístup k datové typy, které můžete použít informační zprávy, zvažte možnost strategie hlavní místo výchozí rozšířenou možnost snížit dobu dotazu. Hlavní není vyloučit úložiště mimo řádek. Pokud vaše dotazy Nepřistupujte datové typy, které můžete použít informační zprávy, může být výhodné ponechat možnost rozšíření. Největší podíl řádků v tabulce hlavní přizpůsobit v mezipaměti sdílené vyrovnávací paměti, která pomáhá výkonu.

Pokud máte úlohu, která používá schéma s širokých tabulek a počty Vysoký znak, zvažte použití tabulky PostgreSQL informační zprávy. Tabulky se zákazníky příkladu má větší než 350 sloupce s několika sloupců, které doby trvání 255 znaků. Poté, co byl převeden do tabulky informační zprávy hlavní strategie, jejich doba dotazu testu výkonnosti snížit 4203 sekund 467 sekund. To je vylepšení 89 procent.

## <a name="next-steps"></a>Další postup
Projděte si pro předchozí charakteristiky vašich úloh. 

Projděte si následující dokumentaci k PostgreSQL: 
- [Kapitola 68, fyzické úložiště databáze](https://www.postgresql.org/docs/current/storage-toast.html) 