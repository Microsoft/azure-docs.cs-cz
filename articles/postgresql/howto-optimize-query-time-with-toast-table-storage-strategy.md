---
title: Optimalizace času dotaz na databázi Azure pro PostgreSQL server s použitím strategie úložiště tabulky informační zprávy
description: Tento článek popisuje způsob optimalizace dotazů, když se strategie úložiště tabulku informačních zpráv ve službě Azure Database pro PostgreSQL server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 96793cb1785a7ffa86331285f401453641b50dac
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820862"
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