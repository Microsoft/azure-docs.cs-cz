---
title: Výstup Azure Stream Analytics do Azure SQL Database
description: Získejte informace o odchozích datech do SQL Azure z Azure Stream Analytics a dosáhněte vyšší propustnosti zápisu.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: f68f973882af28d80b3a27bc4591c5ee932404a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443603"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Výstup Azure Stream Analytics do Azure SQL Database

Tento článek popisuje tipy pro dosažení lepší hodování zápisu výkon při načítání dat do databáze SQL Azure pomocí Azure Stream Analytics.

Výstup SQL ve Službě Azure Stream Analytics podporuje paralelní zápis jako možnost. Tato možnost umožňuje [plně paralelní](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie úlohy, kde více výstupních oddílů zapisují do cílové tabulky paralelně. Povolení této možnosti ve službě Azure Stream Analytics však nemusí být dostatečné k dosažení vyšších propustností, protože výrazně závisí na konfiguraci databáze SQL Azure a schématu tabulek. Výběr indexů, clustering klíč, faktor plnění indexu a komprese mají vliv na čas načtení tabulek. Další informace o optimalizaci databáze SQL Azure za účelem zlepšení výkonu dotazů a načítání na základě interních srovnávacích hodnot naleznete v [tématu Pokyny k výkonu databáze SQL](../sql-database/sql-database-performance-guidance.md). Řazení zápisů není zaručena při zápisu paralelně k databázi SQL Azure.

Zde jsou některé konfigurace v rámci každé služby, které mohou pomoci zlepšit celkovou propustnost vašeho řešení.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Zdědit dělení** – tato možnost konfigurace výstupu SQL umožňuje dědění schéma dělení předchozí krok dotazu nebo vstup. S tímto povoleno, zápis do tabulky založené na disku a mají [plně paralelní](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie pro vaši úlohu, očekávat, že lepší propustnosti. Toto dělení již automaticky probíhá u mnoha dalších [výstupů](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Zamykání stolů (TABLOCK) je také zakázáno pro hromadné vložky vyrobené s touto volbou.

> [!NOTE] 
> Pokud existuje více než 8 vstupníoddíly, dědění vstupní schéma dělení nemusí být vhodnou volbou. Tento horní limit byl pozorován v tabulce s jedním sloupcem identity a seskupeným indexem. V takovém případě zvažte použití [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 v dotazu explicitně určit počet zapisovačů výstupu. Na základě schématu a výběru indexů se vaše pozorování mohou lišit.

- **Velikost dávky** – konfigurace výstupu SQL umožňuje zadat maximální velikost dávky ve výstupu SQL Služby Azure Stream Analytics na základě povahy cílové tabulky/pracovního vytížení. Velikost dávky je maximální počet záznamů odeslaných s každou transakcí hromadného vložení. V clusterovaných indexech columnstore velikosti dávek kolem [100 kM](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) umožňují další paralelizace, minimální protokolování a optimalizace uzamčení. V tabulkách založených na disku může být pro vaše řešení optimální 10 kB (výchozí) nebo nižší, protože vyšší velikosti dávky mohou při hromadných vloženích vyvolat eskalaci zámků.

- **Optimalizace vstupních zpráv** – pokud jste optimalizovali pomocí dědění oddílů a velikosti dávky, zvýšení počtu vstupních událostí na zprávu na oddíl pomáhá dále posunout propustnost zápisu. Optimalizace vstupních zpráv umožňuje velikosti dávek v rámci Azure Stream Analytics až po zadanou velikost dávky, čímž se zlepší propustnost. Toho lze dosáhnout pomocí [komprese](stream-analytics-define-inputs.md) nebo zvýšení velikosti vstupní zprávy v EventHub nebo Blob.

## <a name="sql-azure"></a>SQL Azure

- **Dělená tabulka a indexy** – použití [rozdělené](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabulky SQL a rozdělených indexů v tabulce se stejným sloupcem jako klíč oddílu (například PartitionId) může výrazně snížit konflikty mezi oddíly během zápisů. Pro rozdělenou tabulku budete muset vytvořit [funkci oddílu](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) a [schéma oddílů](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) v primární skupině souborů. Tím se také zvýší dostupnost existujících dat při načítání nových dat. Limit vo protokolu může být přístupů na základě počtu oddílů, které lze zvýšit upgradem skladové položky.

- **Vyhněte se porušení jedinečných klíčů** – Pokud se v protokolu aktivit Azure Stream Analytics zoáhne [více zpráv upozornění na narušení zabezpečení klíčů,](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) ujistěte se, že vaše úloha není ovlivněna jedinečnými porušeními omezení, ke kterým pravděpodobně dojde během případů obnovení. Tomu se lze vyhnout nastavením [možnosti IGNORE\_DUP\_KEY](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) na indexech.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory a tabulky v paměti

- **Tabulka v paměti jako dočasná tabulka** – [tabulky v paměti](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) umožňují velmi vysoké zatížení dat, ale data se musí vejít do paměti. Srovnávací testy ukazují hromadné načítání z tabulky v paměti do tabulky založené na disku je asi 10 krát rychlejší než přímé hromadné vkládání pomocí jednoho zapisovače do tabulky založené na disku se sloupcem identity a seskupeným indexem. Chcete-li využít tento výkon hromadného vložení, nastavte [úlohu kopírování pomocí Azure Data Factory,](../data-factory/connector-azure-sql-database.md) která kopíruje data z tabulky v paměti do tabulky založené na disku.

## <a name="avoiding-performance-pitfalls"></a>Jak se vyhnout úskalí mno žílacích výkonu
Hromadné vkládání dat je mnohem rychlejší než načítání dat s jedním vložením, protože je zabráněno opakované režii přenosu dat, analýzy příkazu insert, spuštění příkazu a vydání záznamu transakce. Místo toho efektivnější cesta se používá do modulu úložiště pro streamování dat. Náklady na nastavení této cesty jsou však mnohem vyšší než jeden příkaz insert v tabulce založené na disku. Bod rentability je obvykle kolem 100 řádků, za nimiž je hromadné načítání téměř vždy efektivnější. 

Pokud je rychlost příchozích událostí nízká, může snadno vytvořit velikosti dávek nižší než 100 řádků, což umožňuje hromadné vkládání neefektivní a využívá příliš mnoho místa na disku. Chcete-li toto omezení obejít, můžete provést jednu z těchto akcí:
* Vytvořte [aktivační událost](/sql/t-sql/statements/create-trigger-transact-sql) MÍSTO PRO použití jednoduché vložky pro každý řádek.
* Použijte tabulku teploty V paměti, jak je popsáno v předchozí části.

Jiný takový scénář nastane při zápisu do indexu columnstore (NCCI), kde menší hromadné vloží můžete vytvořit příliš mnoho segmentů, které mohou dojít k chybě indexu. V tomto případě doporučujeme použít index clusterovanécolumnstore místo.

## <a name="summary"></a>Souhrn

Stručně řečeno, s dělený výstup funkce v Azure Stream Analytics pro výstup SQL, zarovnané paralelizace vaší úlohy s rozdělenou tabulkou v SQL Azure by vám významné vylepšení propustnost. Využití Azure Data Factory pro orchestraci přesunu dat z tabulky V paměti do tabulek založených na disku může poskytnout zvýšení propustnosti pořadí. Pokud je to možné, zlepšení hustoty zpráv může být také hlavním faktorem při zlepšování celkové propustnosti.
