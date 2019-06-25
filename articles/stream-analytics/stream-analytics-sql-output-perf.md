---
title: Výstup Azure Stream Analytics ke službě Azure SQL Database
description: Seznamte se s odesíláním dat do SQL Azure z Azure Stream Analytics a dosáhnout vyšší propustnosti zápisu.
services: stream-analytics
author: chetanmsft
ms.author: chetanmsft
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: 4be73554df0b6bddaafe3910c80c855e127d79f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771647"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Výstup Azure Stream Analytics ke službě Azure SQL Database

Tento článek popisuje tipy, jak dosahovat lepšího výkonu, propustnosti zápis při načítání dat do databáze SQL Azure pomocí Azure Stream Analytics.

Výstup SQL ve službě Azure Stream Analytics podporuje zápis paralelně jako možnost. Tato možnost umožňuje [plně paralelní](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie, kde jsou více oddílů výstupu zápisu do cílové tabulky paralelní úlohy. Povolením této možnosti v Azure Stream Analytics ale nemusí být dostatečné pro dosažení vyšší propustnost, protože výrazně závisí na vaší konfiguraci databáze SQL Azure a schéma tabulky. Volba indexů, clustering klíč, faktor plnění index a komprese mít vliv na dobu načítání tabulek. Další informace o tom, jak optimalizovat vaše databáze SQL Azure ke zlepšení dotazu a načtení založené na interní srovnávací testy výkonu najdete v tématu [SQL database – Průvodce výkonem](../sql-database/sql-database-performance-guidance.md). Řazení zápisů není zaručeno, že při zápisu paralelní k databázi SQL Azure.

Tady jsou některé konfigurace v rámci jednotlivých služeb, který může pomoct zlepšit celkovou propustnost vašeho řešení.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Dědit dělení** – tento SQL výstupní konfigurační možnost umožňuje dědění schéma rozdělení oddílů z předchozího kroku dotazu nebo vstupní. Tuto funkci povolíte, zápis do tabulky na disku a má [plně paralelní](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie pro vaši úlohu očekávat lepší propustnost. Tato dělení již automaticky stane pro mnoho dalších [výstupy](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Zamykání tabulek (TABLOCK) se vypne taky pro Hromadná vložení vytvořené pomocí této možnosti.

> [!NOTE] 
> Po víc než 8 vstupní oddíly se dědění vstup schéma vytváření oddílů nemusí být vhodnou volbou. Tuto horní mez bylo zjištěno u tabulky se sloupcem jedinou identitu a clusterovaného indexu. V takovém případě zvažte použití [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 v dotazu, s ohledem na počet modulů pro zápis výstupu. Na základě schématu a volba indexů, vaše připomínky se můžou lišit.

- **Velikost dávky** -konfiguraci výstupu SQL můžete zadat maximální velikost dávky v závislosti na povaze vašich cílové tabulky/úloh výstup Azure Stream Analytics SQL. Velikost dávky je maximální počet záznamů, které se odešle s každou hromadné vložení transakce. Clusterované indexy columnstore, batch velikostí kolem [100 tisíc](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) povolit další paralelizace, minimální protokolování a zamykání optimalizace. V tabulkách s použitím disku 10 tis. (výchozí) nebo nižší může být taky ideální pro vaše řešení, větší velikosti dávky můžou aktivovat uzamknout eskalace během operace hromadného vložení.

- **Vstupní zpráva ladění** – Pokud jste optimalizovalo pomocí dědit velikost vytváření oddílů a služby batch, zvýšení počtu vstupních událostí za zprávy na oddíl pomáhá další doručením (push) do propustnost zápisu. Vstupní zpráva ladění umožňuje velikosti dávky v rámci Azure Stream Analytics bude až po zadanou velikost dávky; tím se zvýší propustnost. Toho lze dosáhnout pomocí [komprese](stream-analytics-define-inputs.md) nebo zvýšení velikosti vstupní zprávy do centra událostí nebo objekt Blob.

## <a name="sql-azure"></a>SQL Azure

- **Dělené tabulky a indexy** – použití [dělené](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) tabulky SQL a dělené indexy v tabulce se stejným sloupcem jako klíč oddílu (například PartitionId) může výrazně snížit konfliktů mezi oddíly během zápisu. Pro dělenou tabulku, budete muset vytvořit [oddílů](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) a [schéma oddílu](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) na primární skupinu souborů. Při načítání nových dat to bude také zvýšit dostupnost existující data. Limit vstupně-výstupních operací protokolu může spuštění na základě počtu oddílů, které jde navýšit o upgrade SKU.

- **Vyhněte se narušení jedinečné klíče** – Pokud se zobrazí [více klíčů porušení upozornění](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output) v protokolu aktivit Azure Stream Analytics, zkontrolujte úlohy není ovlivněn porušení omezení unique, které by mohly nastat během obnovení případy. Toho se lze vyvarovat nastavením [Ignorovat\_DUP\_klíč](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output) možnost v indexů.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory a tabulky v paměti

- **Tabulka v paměti jako dočasnou tabulku** – [tabulky v paměti](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) umožňují načtení dat velmi vysokorychlostní ale data se musí vejít do paměti. Srovnávací testy zobrazit hromadné načítání z tabulky v paměti do tabulky na disku je přibližně 10 x rychlejší než přímo hromadné vložení pomocí jednoho zapisovatele do tabulky na disku se sloupcem identity a clusterovaného indexu. Chcete-li využívají tento výkon hromadné vložení, nastavte [úlohu kopírování pomocí Azure Data Factory](../data-factory/connector-azure-sql-database.md) , který kopíruje data z tabulky v paměti do tabulky na disku.

## <a name="avoiding-performance-pitfalls"></a>Jak se vyhnout problémům s výkonem
Hromadné vložení dat je mnohem rychlejší než načítání dat pomocí jediné operace vložení, protože opakované režijní náklady na přenos dat, analýze příkazu insert, spuštění příkazu a vydávání záznam transakce je vyloučeno. Místo toho efektivnější cesta se používá na stroji úložiště Streamovat data. Náklady na instalační program tuto cestu je však mnohem vyšší než vložit jeden příkaz v tabulce na základě disku. Hranice rentability nastane obvykle přibližně 100 řádků, nad kterou hromadné načítání je téměř vždy více efektivní. 

Pokud je počet příchozích událostí nízká, ho můžete snadno vytvořit velikosti dávky nižší než 100 řádků, které umožňuje hromadné vložení neefektivní a využívá příliš mnoho místa na disku. Chcete-li toto omezení obejít, proveďte jednu z těchto akcí:
* Vytvoření INSTEAD OF [aktivační událost](/sql/t-sql/statements/create-trigger-transact-sql) použít jednoduchý vložení pro každý řádek.
* Použijte dočasné tabulky v paměti, jak je popsáno v předchozí části.

Další takové situaci dojde při zápisu do indexu columnstore neclusterovaný (NCCI), kde menší Hromadná vložení můžete vytvořit příliš mnoho segmentů, které může dojít k selhání index. V takovém případě doporučujeme místo toho použít s indexem Columnstore clusteru.

## <a name="summary"></a>Souhrn

Stručně řečeno, funkce oddílů výstup ve službě Azure Stream Analytics pro výstup SQL zarovnané paralelizace svou práci s dělené tabulky v SQL Azure by vám měl dát propustnost výrazné vylepšení. Využití služby Azure Data Factory k orchestraci přesouvání dat z tabulky v paměti do tabulek na disku můžete poskytnout řádově zvýšení propustnosti. Pokud je to proveditelné, zvýšení hustoty zpráva může být také hlavním faktorem zlepšuje celkovou propustnost.
