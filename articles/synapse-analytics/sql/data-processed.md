---
title: Data zpracovaná s fondem SQL bez serveru
description: Tento dokument popisuje, jak se zpracovávají data při dotazování na data v Azure Storage pomocí fondu SQL bez serveru.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424031"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Data zpracovaná s neserverovým fondem SQL ve službě Azure synapse Analytics

Zpracovaná data jsou množství dat, která jsou dočasně uložena v systému při provádění dotazu a jsou tvořena:

- Objem dat načtených z úložiště – to zahrnuje:
  - Množství čtených dat při čtení dat
  - Množství čtených dat při čtení metadat (pro formáty souborů obsahující metadata, jako je Parquet)
- Množství dat v mezilehlých výsledcích – data přenesená mezi uzly během provádění dotazu, včetně přenosu dat do koncového bodu v nekomprimovaném formátu. 
- Množství dat zapsaných do úložiště – Pokud použijete CETAS k exportu sady výsledků do úložiště, bude se vám účtovat za zapsané bajty a množství zpracovaných dat pro vybranou součást CETAS.

Čtení souborů z úložiště je vysoce optimalizované a používá:

- Předběžné načítání – což může snížit režii množství čtených dat. Pokud dotaz přečte celý soubor, nebude nijak režie. Pokud je soubor částečně načtený, jako v prvních N dotazech, bude se při předběžném načítání číst trochu více dat.
- Optimalizovaný analyzátor CSV – Pokud při čtení souborů CSV použijete PARSER_VERSION = "2.0", výsledkem bude mírné zvýšení množství dat načtených z úložiště.  Optimalizovaný analyzátor CSV čte soubory paralelně v blocích se stejnou velikostí. Nezaručujeme, že bloky budou obsahovat celé řádky. Aby bylo zajištěno, že jsou všechny řádky analyzovány, budou čteny i malé fragmenty sousedících bloků dat, což zvyšuje množství režie.

## <a name="statistics"></a>Statistika

Optimalizátor dotazů fondu SQL bez serveru spoléhá na statistiku pro generování optimálních plánů spouštění dotazů. Statistiku můžete vytvořit ručně nebo je automaticky vytvořit bez serveru SQL fondu bez serveru. V obou případech se Statistika vytvoří spuštěním samostatného dotazu, který vrátí konkrétní sloupec v zadané vzorkovací frekvenci. Tento dotaz má přidružené množství zpracovaných dat.

Pokud spouštíte stejný nebo jakýkoli jiný dotaz, který by měl těžit z vytvořených statistik, budou se v případě potřeby znovu používat statistiky a pro vytváření statistik nebudou zpracována žádná další data.

Vytváření statistik pro sloupec Parquet bude mít za následek, že se ze souborů přečtou pouze relevantní sloupce. Vytváření statistik pro sloupec CSV bude mít za následek čtení a analýzu celých souborů.

## <a name="rounding"></a>Zaokrouhlení

Množství zpracovaných dat se zaokrouhlí na jeden dotaz na nejbližších MB, přičemž pro každý dotaz se zpracuje minimálně 10 MB dat.

## <a name="what-is-not-included-in-data-processed"></a>Co není součástí zpracovaných dat

- Metadata na úrovni serveru (jako přihlašování, role, přihlašovací údaje na úrovni serveru)
- Databáze, které ve svém koncovém bodu vytvoříte jako tyto databáze obsahují jenom metadata (například uživatele, role, schémata, zobrazení, vložené TVF, uložené procedury, přihlašovací údaje v oboru databáze, externí zdroje dat, formáty externích souborů, externí tabulky).
  - Použijete-li odvození schématu, budou fragmenty souborů načteny, aby byly odvozeny názvy sloupců a datové typy.
- Příkazy DDL s výjimkou vytvoření statistik budou zpracovávat data ze služby Storage na základě zadaného ukázkového procenta.
- Dotazy jenom na metadata

## <a name="reduce-amount-of-data-processed"></a>Snížení objemu zpracovaných dat

Můžete optimalizovat množství zpracovaných dat na dotazování a získat lepší výkon díky dělení a převodu dat do komprimovaného sloupcového formátu, jako je Parquet.

## <a name="examples"></a>Příklady

Řekněme, že existují dvě tabulky, z nichž každá má stejná data v pěti sloupcích stejné velikosti:

- population_csv tabulky s použitím 5 TB souborů CSV
- population_parquet tabulka, která se zálohuje o 1 TB souborů Parquet – Tato tabulka je menší než předchozí, protože Parquet obsahuje komprimovaná data.
- very_small_csv tabulce s 100 KB souborů CSV

**Dotaz #1** : Vyberte Sum (naplnění) z population_csv

Tento dotaz načte a analyzuje celé soubory a získá hodnoty pro sloupec populace. Uzly budou zpracovávat fragmenty této tabulky, součet populace pro jednotlivé fragmenty se přenese mezi uzly a Konečný součet se přenese do vašeho koncového bodu. Tento dotaz zpracuje 5 TB dat Plus malým režijním nákladům při přenosu částek fragmentů.

**Dotaz #2** : Vyberte Sum (naplnění) z population_parquet

Dotazování komprimovaných formátů a sloupcově orientovaných formátů, jako je Parquet, má za následek čtení méně dat než v předchozím dotazu, protože fond SQL bez serveru načte jeden komprimovaný sloupec místo celého souboru. V tomto případě bude načteno 0,2 TB (pět sloupců se stejnou velikostí, 0,2 TB každého). Uzly budou zpracovávat fragmenty této tabulky, součet populace pro jednotlivé fragmenty se přenese mezi uzly a Konečný součet se přenese do vašeho koncového bodu. Tento dotaz zpracuje 0,2 TB Plus malým režijním nákladům na převod součtů fragmentů.

**Dotaz #3** : SELECT * FROM population_parquet

Tento dotaz načte všechny sloupce a převede všechna data v nekomprimovaném formátu. Pokud je kompresní formát 5:1, proces bude mít 6 TB, protože bude přečten 1 TB + přenos 5 TB nekomprimovaných dat.

**Dotaz #4** : Vyberte počet (*) z very_small_csv

Tento dotaz načte celé soubory. Celková velikost souborů v úložišti pro tuto tabulku je 100 KB. Uzly budou zpracovávat fragmenty této tabulky. součet pro každý fragment se přenese mezi uzly a Konečný součet se přenese do vašeho koncového bodu. Tento dotaz zpracuje o něco více než 100 KB dat. Množství zpracovaných dat pro tento dotaz se zaokrouhlí na 10 MB, jak je uvedeno v [Zaokrouhlení](#rounding).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak optimalizovat dotazy na výkon, podívejte se na [osvědčené postupy pro fond SQL bez serveru](best-practices-sql-on-demand.md).
