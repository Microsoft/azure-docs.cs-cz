---
title: Technologie v paměti
description: Technologie Azure SQL Database In-Memory výrazně zlepšují výkon transakčních a analytických úloh.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73810268"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimalizace výkonu pomocí technologií v paměti v databázi SQL

Technologie v paměti v Azure SQL Database umožňují zlepšit výkon vaší aplikace a potenciálně snížit náklady na databázi. 

## <a name="when-to-use-in-memory-technologies"></a>Kdy používat technologie v paměti

Pomocí technologií v paměti v Azure SQL Database můžete dosáhnout zlepšení výkonu s různými úlohami:

- **Transakční** (online transakční zpracování (OLTP)), kde většina požadavků čte nebo aktualizuje menší sadu dat (například operace CRUD).
- **Analytické** (online analytické zpracování (OLAP)), kde většina dotazů má složité výpočty pro účely vykazování, s určitým počtem dotazů, které načítají a připojují data k existujícím tabulkám (tzv. hromadné načítání) nebo odstraňují data z tabulek. 
- **Smíšené** (hybridní transakce/analytické zpracování (HTAP)), kde jsou dotazy OLTP i OLAP prováděny na stejné sadě dat.

Technologie v paměti mohou zlepšit výkon těchto úloh udržováním dat, která by měla být zpracována do paměti, pomocí nativní kompilace dotazů nebo pokročilého zpracování, jako je dávkové zpracování a pokyny SIMD, které jsou k dispozici na základního hardwaru. 

## <a name="overview"></a>Přehled

Azure SQL Database má následující technologie v paměti:
- *[V paměti OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* zvyšuje počet transakcí za sekundu a snižuje latenci pro zpracování transakcí. Scénáře, které využívají IN-Memory OLTP jsou: zpracování transakcí s vysokou propustností, jako je obchodování a hraní her, ingestování dat z událostí nebo zařízení IoT, ukládání do mezipaměti, načítání dat a dočasné scénáře proměnných tabulek a tabulek.
- *Clustered columnstore indexy* snížit vaše nároky na úložiště (až 10 krát) a zlepšit výkon pro vytváření sestav a analytických dotazů. Můžete jej použít s tabulkami faktů v datových tržišti, aby se vešly více dat do databáze a zlepšit výkon. Můžete ji také použít s historickými daty v provozní databázi k archivaci a k dotazování až 10krát více dat.
- *Nonclustered columnstore indexy* pro HTAP vám pomohou získat přehled o vaší firmě v reálném čase prostřednictvím přímého dotazování na provozní databázi, aniž byste museli spouštět nákladný proces extrakce, transformace a načítání (ETL) a čekat na naplnění datového skladu. Nonclustered columnstore indexy umožňují rychlé provádění analytických dotazů v databázi OLTP a zároveň snižuje dopad na provozní zatížení.
- *Indexy clusterového úložiště sloupců optimalizované pro paměť* pro HTAP umožňují provádět rychlé zpracování transakcí a *současně* velmi rychle spouštět analytické dotazy na stejná data.

Indexy columnstore a OLTP v paměti jsou součástí produktu SQL Server od roku 2012 a 2014. Azure SQL Database a SQL Server sdílejí stejnou implementaci technologií v paměti. Do budoucna nové funkce pro tyto technologie jsou vydány v Azure SQL Database první před jejich vydáním v SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Výhody technologie v paměti

Díky efektivnějšímu zpracování dotazů a transakcí vám technologie v paměti také pomohou snížit náklady. Obvykle není nutné upgradovat cenovou úroveň databáze k dosažení zvýšení výkonu. V některých případech můžete dokonce snížit cenovou úroveň, zatímco stále vidíte zlepšení výkonu s technologiemi v paměti.

Zde jsou dva příklady toho, jak oltp v paměti pomohl výrazně zlepšit výkon:

- Pomocí OLTP v paměti, [Quorum Business Solutions byl schopen zdvojnásobit své zatížení při zlepšování DTU o 70 %](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU znamená *databázovou transakční jednotku*a zahrnuje měření spotřeby zdrojů.
- Následující video ukazuje významné zlepšení spotřeby prostředků s ukázkovou úlohou: [V paměti OLTP v Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Další informace najdete v příspěvku blogu: [OlTP v paměti v blogu Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Technologie v paměti jsou dostupné v databázích AZURE SQL úrovně Premium a Business Critical a v elastických fondech Premium.

Následující video vysvětluje potenciální zvýšení výkonu s technologiemi v paměti v Azure SQL Database. Nezapomeňte, že zvýšení výkonu, které vidíte vždy závisí na mnoha faktorech, včetně povahy úlohy a data, přístup vzor databáze a tak dále.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Tento článek popisuje aspekty indexů OLTP v paměti a columnstore, které jsou specifické pro Azure SQL Database a také obsahují ukázky:

- Uvidíte dopad těchto technologií na úložiště a omezení velikosti dat.
- Uvidíte, jak spravovat pohyb databází, které používají tyto technologie mezi různými cenovými úrovněmi.
- Uvidíte dvě ukázky, které ilustrují použití OLTP v paměti, stejně jako columnstore indexy v Azure SQL Database.

Další informace naleznete v tématu:

- [Scénáře přehledu oltp v paměti a použití](https://msdn.microsoft.com/library/mt774593.aspx) (obsahuje odkazy na případové studie zákazníka a informace, které můžete začít)
- [Dokumentace pro OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)
- [Průvodce indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridní transakční/analytické zpracování (HTAP), známé také jako [provozní analýza v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>OLTP v paměti

Technologie OLTP v paměti poskytuje extrémně rychlé operace přístupu k datům tím, že uchovává všechna data v paměti. Používá také specializované indexy, nativní kompilaci dotazů a přístup k datům bez zámku ke zlepšení výkonu úlohy OLTP. Data OLTP v paměti lze uspořádat dvěma způsoby:

- **Formát rowstore optimalizovaný pro paměť,** kde každý řádek je samostatný objekt paměti. Jedná se o klasický formát OLTP v paměti optimalizovaný pro vysoce výkonné úlohy OLTP. Existují dva typy tabulek optimalizovaných pro paměť, které lze použít ve formátu rowstore optimalizovaného pro paměť:
  - *Trvalé tabulky* (SCHEMA_AND_DATA), kde jsou po restartování serveru zachovány řádky umístěné v paměti. Tento typ tabulek se chová jako tradiční tabulka rowstore s dalšími výhodami optimalizace v paměti.
  - *Netrvalé tabulky* (SCHEMA_ONLY), kde řádky nejsou zachovány po restartování. Tento typ tabulky je určen pro dočasná data (například nahrazení dočasných tabulek) nebo tabulky, ve kterých je třeba rychle načíst data před jejich přesunutím do některé trvalé tabulky (tzv. pracovní tabulky).
- **Formát columnstore optimalizované ho s optimalizovaným** pro paměť, kde jsou data uspořádána ve sloupcovém formátu. Tato struktura je určena pro scénáře HTAP, kde je třeba spustit analytické dotazy na stejné datové struktuře, kde je spuštěna vaše úloha OLTP.

> [!Note]
> Technologie OLTP v paměti je určena pro datové struktury, které se mohou plně nasytit v paměti. Vzhledem k tomu, že data v paměti nelze převést na disk, ujistěte se, že používáte databázi, která má dostatek paměti. Další podrobnosti najdete [v tématu Velikost dat a uzávěr úložiště pro OLTP v paměti.](#data-size-and-storage-cap-for-in-memory-oltp)

Rychlý primer na IN-Memory OLTP: [Úvodní start 1: In-Memory OLTP Technologie pro rychlejší T-SQL výkon](https://msdn.microsoft.com/library/mt694156.aspx) (další článek, který vám pomůže začít)

Podrobná videa o technologiích:

- [Oltp v paměti v Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (který obsahuje ukázku výhod výkonu a kroky k reprodukci těchto výsledků sami)
- [Videa OLTP v paměti: Co to je a kdy/jak ji používat](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Existuje programový způsob, jak pochopit, zda daná databáze podporuje oltp v paměti. Můžete provést následující dotaz Transact-SQL:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Pokud dotaz vrátí **hodnotu 1**, je v této databázi podporován protokol OLTP v paměti. Následující dotazy identifikují všechny objekty, které je třeba odebrat před přechodem databáze na standardní/základní:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Velikost dat a uzávěr úložiště pro OLTP v paměti

Oltp v paměti obsahuje tabulky optimalizované pro paměť, které se používají pro ukládání uživatelských dat. Tyto tabulky jsou nutné, aby se vešly do paměti. Vzhledem k tomu, že spravujete paměť přímo ve službě SQL Database, máme koncept kvóty pro uživatelská data. Tato myšlenka se označuje jako *úložiště OLTP v paměti*.

Každá podporovaná cenová úroveň jedné databáze a každá cenová úroveň elastického fondu zahrnuje určité množství úložiště OLTP v paměti. Viz [Limity prostředků založených na DTU – jedna databáze](sql-database-dtu-resource-limits-single-databases.md), Omezení prostředků založených na [DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků založených na virtuálních jádrech – limity prostředků založené na single databases](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založených na virtuálních jádrech – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Do limitu úložiště OLTP v paměti se započítávají následující položky:

- Aktivní řádky uživatelských dat v tabulkách optimalizovaných pro paměť a proměnných tabulek. Všimněte si, že staré verze řádků se do zakončení nezapočítávají.
- Indexy v tabulkách optimalizovaných pro paměť.
- Provozní režie operací ALTER TABLE.

Pokud stisknete omezení, zobrazí se chyba mimo kvótu a již nebudete moci vkládat nebo aktualizovat data. Chcete-li tuto chybu zmírnit, odstraňte data nebo zvyšte cenovou úroveň databáze nebo fondu.

Podrobnosti o monitorování využití úložiště OLTP v paměti a konfiguraci výstrah při téměř dosažení limitu naleznete [v tématu Sledování úložiště v paměti](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>O elastických bazénech

S elastické fondy úložiště OLTP v paměti je sdílena ve všech databázích ve fondu. Proto použití v jedné databázi může potenciálně ovlivnit jiné databáze. Dvě skutečnosti snižující závažnost rizika pro toto jsou:

- Konfigurace `Max-eDTU` nebo `MaxvCore` pro databáze, která je nižší než eDTU nebo počet virtuálních jader pro fond jako celek. Toto maximum omezuje využití úložiště OLTP v paměti v libovolné databázi ve fondu na velikost, která odpovídá počtu eDTU.
- Konfigurace `Min-eDTU` nebo `MinvCore` který je větší než 0. Toto minimum zaručuje, že každá databáze ve fondu má množství dostupné úložiště OLTP v paměti, které odpovídá nakonfigurovanému `Min-eDTU` nebo `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Změna úrovní služeb databází, které používají technologie OLTP v paměti

Databázi nebo instanci můžete vždy upgradovat na vyšší úroveň, například z obecného účelu na důležité pro firmy (nebo standardní na prémiovou). Dostupné funkce a prostředky pouze zvýšit.

Ale snížení úrovně může negativně ovlivnit vaši databázi. Dopad je zvláště zřejmé, když downgrade z business critical na obecné účely (nebo Premium na standardní nebo základní) pokud databáze obsahuje objekty OLTP v paměti. Tabulky optimalizované pro paměť nejsou po přechodu na starší verzi k dispozici (i když zůstanou viditelné). Stejné aspekty platí, když snižujete cenovou úroveň elastického fondu nebo přesouváte databázi s technologiemi v paměti do standardního nebo základního elastického fondu.

> [!Important]
> Oltp v paměti není podporován na úrovni Obecné účely, Standard nebo Basic. Proto není možné přesunout databázi, která má všechny objekty OLTP v paměti na úroveň Standard nebo Basic.

Před přechodem databáze na standard/basic odeberte všechny tabulky optimalizované pro paměť a typy tabulek, stejně jako všechny nativně zkompilované moduly T-SQL. 

*Škálování prostředků v důležité obchodní vrstvě*: Data v tabulkách optimalizovaných pro paměť se musí vejít do úložiště OLTP v paměti, které je přidruženo k vrstvě databáze nebo spravované instanci, nebo jsou k dispozici v elastickém fondu. Pokud se pokusíte škálovat úroveň nebo přesunout databázi do fondu, který nemá dostatek dostupné úložiště OLTP v paměti, operace se nezdaří.

## <a name="in-memory-columnstore"></a>Úložiště sloupců v paměti

Technologie columnstore v paměti umožňuje ukládat a dotazovat velké množství dat v tabulkách. Technologie ColumnStore využívá formát úložiště dat založený na sloupcích a zpracování dávkových dotazů k dosažení zisku až 10násobku výkonu dotazů v úlohách OLAP oproti tradičnímu úložišti orientovanému na řádky. Můžete také dosáhnout zisky až 10 násobek komprese dat přes nekomprimované velikosti dat.
Existují dva typy columnstore modely, které můžete použít k uspořádání dat:

- **Clustered columnstore,** kde jsou všechna data v tabulce uspořádána ve sloupcovém formátu. V tomto modelu jsou všechny řádky v tabulce umístěny ve sloupcovém formátu, který vysoce komprimuje data a umožňuje provádět rychlé analytické dotazy a sestavy v tabulce. V závislosti na povaze dat může být velikost dat snížena 10x-100x. Clustered columnstore model také umožňuje rychlé požití velkého množství dat (hromadné zatížení), protože velké dávky dat větší než 100 kB řádky jsou komprimovány před jejich uložením na disk. Tento model je dobrou volbou pro klasické scénáře datového skladu. 
- **Neseskupené columnstore,** kde jsou data uložena v tradiční tabulce rowstore a je index ve formátu columnstore, který se používá pro analytické dotazy. Tento model umožňuje hybridní transakční analytick –zpracování(HTAP): možnost spouštět výkonné analýzy v reálném čase na transakční množiny. DOTAZY OLTP jsou spouštěny v tabulce rowstore, která je optimalizována pro přístup k malé sadě řádků, zatímco dotazy OLAP jsou spouštěny na indexu columnstore, který je lepší volbou pro skenování a analýzy. Optimalizátor dotazů Azure SQL Database dynamicky vybírá formát rowstore nebo columnstore na základě dotazu. Indexy columnstore bez clusterů nezmenšují velikost dat, protože původní sada dat je uložena v původní tabulce rowstore bez jakékoli změny. Velikost indexu dalšího columnstore by však měla být řádově menší než ekvivalentní index b-stromu.

> [!Note]
> Technologie columnstore v paměti uchovává pouze data potřebná pro zpracování v paměti, zatímco data, která se nevejdou do paměti, jsou uložena na disku. Proto množství dat ve strukturách columnstore v paměti může překročit množství dostupné paměti. 

Podrobné video o technologii:

- [Index columnstore: Videa analýzy v paměti z Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Velikost dat a úložiště pro indexy columnstore

Columnstore indexy nejsou nutné, aby se vešly do paměti. Proto pouze limit na velikost indexů je maximální celková velikost databáze, která je dokumentována v [nákupnímodel založený na DTU](sql-database-service-tiers-dtu.md) a na [základě virtuálních jader nákupní model](sql-database-service-tiers-vcore.md) články.

Při použití clusterovaných indexů columnstore se pro úložiště základní tabulky používá sloupcová komprese. Tato komprese může výrazně snížit nároky na úložiště vašich uživatelských dat, což znamená, že můžete umístit více dat do databáze. A komprese může být dále zvýšena s [sloupcovou archivní kompresí](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Množství komprese, které můžete dosáhnout, závisí na povaze dat, ale 10násobek komprese není neobvyklé.

Například pokud máte databázi s maximální velikostí 1 terabajt (TB) a dosáhnout 10 krát komprese pomocí columnstore indexy, můžete se vejde celkem 10 TB uživatelských dat v databázi.

Při použití indexy columnstore bez clusterů, základní tabulka je stále uložena v tradičním formátu rowstore. Proto úspory úložiště nejsou tak velké jako u clusterovaných indexů columnstore. Pokud však nahrazujete několik tradičních indexů bez clusterů indexy s jedním indexem columnstore, stále můžete zobrazit celkové úspory v stopách úložiště pro tabulku.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Změna úrovní služeb databází obsahujících indexy columnstore

*Přeřazení jedné databáze na základní nebo standardní* nemusí být možné, pokud je vaše cílová úroveň pod S3. Columnstore indexy jsou podporovány pouze na obchodní kritické/premium cenové úrovně a na úrovni Standard, S3 a vyšší a ne na úrovni Basic. Při přechodu databáze na nepodporovanou úroveň nebo úroveň, index columnstore přestane být k dispozici. Systém udržuje index columnstore, ale nikdy nevyužívá index. Pokud později upgradujete zpět na podporovanou úroveň nebo úroveň, index columnstore je okamžitě připraven k novému využití.

Pokud máte **index clusterovanécolumnstore,** celá tabulka přestane být po přechodu na starší verzi k dispozici. Proto doporučujeme přetažení všech *indexů clusterované* columnstore před přechodem databáze na nepodporovanou úroveň nebo úroveň.

> [!Note]
> Spravovaná instance podporuje indexy ColumnStore ve všech vrstvách.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka 1: Technologie OLTP v paměti pro rychlejší výkon T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Použití OLTP v paměti v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)
- [Úložiště OLTP monitoru v paměti](sql-database-in-memory-oltp-monitoring.md) pro oltp v paměti
- [Vyzkoušet funkce v paměti v Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Další zdroje

### <a name="deeper-information"></a>Hlubší informace

- [Zjistěte, jak kvorum zdvojnásobuje zatížení databáze klíčů při současném snížení DTU o 70 % pomocí oltp v paměti v databázi SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Oltp v paměti v příspěvku blogu databáze Azure SQL](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Další informace o oltp v paměti](https://msdn.microsoft.com/library/dn133186.aspx)
- [Další informace o indexech columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- [Další informace o provozních analýzách v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)
- Viz [Běžné vzory pracovního vytížení a aspekty migrace](https://msdn.microsoft.com/library/dn673538.aspx) (který popisuje vzory pracovního vytížení, kde oltp v paměti běžně poskytuje významné zvýšení výkonu)

### <a name="application-design"></a>Návrh aplikací

- [Oltp v paměti (optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Použití OLTP v paměti v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Nástroje

- [Portál Azure](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
