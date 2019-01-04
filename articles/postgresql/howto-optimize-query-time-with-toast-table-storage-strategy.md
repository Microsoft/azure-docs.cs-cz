---
title: Optimalizace času dotazu ve službě Azure Database for PostgreSQL pomocí strategie úložiště tabulky informační zprávy
description: Tento článek popisuje způsob optimalizace dotazů, když se informační zpráva strategie úložiště tabulky ve službě Azure Database pro PostgreSQL server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 1fb818a65e26f969f72131b0f5265f3efdd36bb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542195"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>Optimalizace době zpracování dotazu se strategie úložiště tabulky informační zprávy 
Tento článek popisuje, jak optimalizovat dotaz časy s strategie úložiště tabulky informační zprávy.

## <a name="toast-table-storage-strategies"></a>Strategie úložiště tabulky informační zprávy
Existují čtyři různé strategie pro ukládání informačních zpráv u sloupců na disku, které představují různé kombinace mezi komprese a úložiště mimo řádek. Strategie může být nastaven na úrovni datového typu na úrovni sloupce.
- **Prostý** brání komprese nebo úložiště mimo řádek; dále, zakáže použití hlaviček jednobajtové varlena typů. **Prostý** je možná pouze strategie pro sloupce bez informačních zpráv u datových typů.
- **Rozšířené** umožňuje úložiště komprese a mimo řádek. **Rozšířené** je výchozí nastavení pro většinu schopné informační zprávy datových typů. Komprese bude pokus o první pak mimo řádek úložiště, pokud stále řádek je příliš velký.
- **Externí** umožňuje úložiště mimo řádek, ale ne komprese. Použití **externí** bude vytvořit dílčí řetězec operace s širokou text a bytea sloupce rychlejší, v penalizace větší úložný prostor, protože tyto operace jsou optimalizovány pro načtení jenom požadované části na více instancí z řádku hodnotu v případě ji není komprimovaná.
- **Hlavní** umožňuje kompresi, ale ne out řádku úložiště. Úložiště mimo řádek stále se provede pro tyto sloupce, ale pouze jako poslední možnost při neexistuje jiný způsob, jak vytvořit řádek dostatečně malá, aby se vešly na stránku.

## <a name="using-toast-table-storage-strategies"></a>Pomocí strategií úložiště tabulky informační zprávy
Pokud vaše dotazy přístup u informačních zpráv datové typy, zvažte použití **hlavní** místo výchozího **Extended** možnost snížit dobu dotazu. **Hlavní** nevylučuje úložiště mimo řádek. Na druhé straně, pokud vaše dotazy nemají přístup k informačních zpráv u datových typů, může být výhodné ponechat **Extended** možnost. Proto větší část řádky v tabulce hlavní, se vejde do mezipaměti sdílené vyrovnávací paměti, pomáhá výkonu.

Pokud máte úlohu pomocí schématu s širokých tabulek a počty Vysoký znak, zvažte použití tabulky PostgreSQL informační zprávy. Tabulky se zákazníky příkladu má větší než 350 sloupce s více sloupci odsazení 255 znaků. Čas jejich srovnávacího testu dotazu redukovaným z 4203 sekund 467 sekund, zlepšení 89 procent, po převedení strategie informační zprávy **hlavní**.

## <a name="next-steps"></a>Další postup
Zkontrolujte vaše úlohy pro výše uvedených charakteristik. 

Projděte si následující dokumentaci k PostgreSQL: [Kapitola 68, fyzické úložiště databáze](https://www.postgresql.org/docs/current/storage-toast.html) 