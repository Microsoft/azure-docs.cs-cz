---
title: Data zpracovaná pomocí fondu SQL bez serveru
description: Tento dokument popisuje, jak se počítá množství zpracovaná data při dotazování dat ve službě Data Lake.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381197"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Data zpracovaná pomocí fondu SQL bez serveru ve službě Azure synapse Analytics

*Zpracovaná data* jsou množství dat, která systém dočasně ukládá během spuštění dotazu. Zpracovaná data se skládají z následujících množství:

- Objem dat načtených z úložiště Tato částka zahrnuje:
  - Čtení dat při čtení dat.
  - Čtení dat při čtení metadat (pro formáty souborů obsahující metadata, jako je Parquet).
- Množství dat v mezilehlých výsledcích. Tato data se přenesou mezi uzly během spuštění dotazu. Zahrnuje přenos dat do vašeho koncového bodu v nekomprimovaném formátu. 
- Množství dat zapsaných do úložiště Pokud používáte CETAS k exportu sady výsledků do úložiště, pak se množství zapsaných dat přidá do množství zpracovaných dat pro vybranou součást CETAS.

Čtení souborů z úložiště je vysoce optimalizované. Proces používá:

- Přednačtení, které může znamenat určitou režii množství čtených dat. Pokud dotaz přečte celý soubor, vyplatí se žádná režie. Pokud je soubor částečně čtený, například v prvních N dotazech, pak se načtou další data pomocí předběžného načítání.
- Optimalizovaný analyzátor hodnot oddělených čárkami (CSV). Pokud ke čtení souborů CSV používáte PARSER_VERSION = "2.0", pak se objem dat načtených z úložiště mírně zvyšuje. Optimalizovaný analyzátor CSV čte soubory paralelně v blocích se stejnou velikostí. Bloky dat nemusí nutně obsahovat celé řádky. Aby bylo zajištěno, že jsou všechny řádky analyzovány, optimalizovaný analyzátor CSV také přečte malé fragmenty sousedících bloků dat. Tento proces přidá malé množství režijních nákladů.

## <a name="statistics"></a>Statistika

Optimalizátor dotazů fondu SQL bez serveru spoléhá na statistiku pro generování optimálních plánů spouštění dotazů. Statistiky můžete vytvořit ručně. V opačném případě je fond SQL bez serveru automaticky vytvoří. V obou případech je statistika vytvořena spuštěním samostatného dotazu, který vrátí konkrétní sloupec s poskytnutou vzorkovací frekvencí. Tento dotaz má přidružené množství zpracovaných dat.

Pokud spustíte stejný nebo jakýkoli jiný dotaz, který by měl těžit z vytvořených statistik, pak se statistiky znovu použijí, pokud je to možné. Pro vytváření statistik nejsou zpracovaná žádná další data.

Při vytváření statistik pro sloupec Parquet je ze souborů čten pouze příslušný sloupec. Při vytváření statistik pro sloupec CSV jsou načteny a analyzovány celé soubory.

## <a name="rounding"></a>Zaokrouhlení

Množství zpracovaných dat se zaokrouhluje na jeden dotaz na nejbližší MB. Každý dotaz má nejméně 10 MB zpracovaných dat.

## <a name="what-data-processed-doesnt-include"></a>Která data zpracovaná nebudou zahrnovat

- Metadata na úrovni serveru (jako přihlašování, role a přihlašovací údaje na úrovni serveru).
- Databáze, které vytvoříte ve svém koncovém bodu. Tyto databáze obsahují jenom metadata (například uživatele, role, schémata, zobrazení, vložené funkce vracející tabulku [TVF], uložené procedury, přihlašovací údaje v oboru databáze, externí zdroje dat, formáty externích souborů a externí tabulky).
  - Použijete-li odvození schématu, jsou fragmenty souborů čteny k odvození názvů sloupců a datových typů a množství čtených dat je přidáno k množství zpracovaných dat.
- Příkazy jazyka DDL (Data Definition Language), s výjimkou příkazu CREATE STATISTICs, protože zpracovávají data z úložiště na základě zadaného ukázkového procenta.
- Dotazy, které jsou jenom metadata.

## <a name="reducing-the-amount-of-data-processed"></a>Omezení množství zpracovaných dat

Můžete optimalizovat množství zpracovaných dat na dotazování a zvýšit výkon pomocí dělení a převodu dat na komprimovaný formát založený na sloupcích, jako je Parquet.

## <a name="examples"></a>Příklady

Představte si tři tabulky.

- Tabulka population_csv se zálohuje o 5 TB souborů CSV. Soubory jsou uspořádány v pěti sloupcích stejné velikosti.
- Tabulka population_parquet má stejná data jako population_csv tabulka. Je zajištěno 1 TB souborů Parquet. Tato tabulka je menší než předchozí, protože data jsou komprimována ve formátu Parquet.
- Tabulka very_small_csv se zálohuje o 100 KB souborů CSV.

**Dotaz 1** : Vyberte Sum (populace) z population_csv

Tento dotaz čte a analyzuje celé soubory, aby získal hodnoty pro sloupec populace. Uzly zpracovávají fragmenty této tabulky a součet obyvatel pro jednotlivé fragmenty se přenáší mezi uzly. Konečný součet se převede do vašeho koncového bodu. 

Tento dotaz zpracovává 5 TB dat a snižuje režijní náklady na převod součtů fragmentů.

**Dotaz 2** : Vyberte Sum (populace) z population_parquet

Při dotazování na komprimované a sloupcové formáty, jako je Parquet, je méně dat čteno než v dotazu 1. Vidíte tento výsledek, protože fond SQL bez serveru čte jeden komprimovaný sloupec místo celého souboru. V tomto případě je čtena 0,2 TB. (Pět sloupců stejné velikosti je 0,2 TB.) Uzly zpracovávají fragmenty této tabulky a součet obyvatel pro jednotlivé fragmenty se přenáší mezi uzly. Konečný součet se převede do vašeho koncového bodu. 

Tento dotaz zpracovává 0,2 TB a zvyšuje velkou režii pro přenos součtů fragmentů.

**Dotaz 3** : vyberte * z population_parquet

Tento dotaz přečte všechny sloupce a převede všechna data v nekomprimovaném formátu. Pokud je kompresní formát 5:1, zpracuje dotaz 6 TB, protože čte 1 TB a převede 5 TB nekomprimovaných dat.

**Dotaz 4** : Vyberte počet (*) z very_small_csv

Tento dotaz čte celé soubory. Celková velikost souborů v úložišti pro tuto tabulku je 100 KB. Uzly zpracovávají fragmenty této tabulky a součet pro jednotlivé fragmenty se přenáší mezi uzly. Konečný součet se převede do vašeho koncového bodu. 

Tento dotaz zpracovává mírně více než 100 KB dat. Objem dat zpracovaných pro tento dotaz se zaokrouhluje na 10 MB, jak je uvedeno v části [Zaokrouhlení](#rounding) tohoto článku.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak optimalizovat dotazy pro výkon, najdete v tématu [osvědčené postupy pro fond SQL bez serveru](best-practices-sql-on-demand.md).
