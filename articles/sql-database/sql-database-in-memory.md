---
title: Azure SQL Database začleňování paměťových technologií | Dokumentace Microsoftu
description: Azure SQL Database začleňování paměťových technologií výrazně zlepšit výkon transakční a analytické úlohy.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: d2602b79db0950043cc84bbd1626f7c8ec6f402a
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714385"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimalizace výkonu pomocí v začleňování paměťových technologií ve službě SQL Database

V začleňování paměťových technologií ve službě Azure SQL Database vám umožní zlepšit výkon vaší aplikace a potenciálně snížili množství náklady na vaši databázi. S použitím technologií v paměti ve službě Azure SQL Database, můžete dosáhnout zlepšení výkonu pomocí různých úloh:
- **Transakční** (online zpracování transakcí (OLTP)) kde většinu požadavků, číst a aktualizovat menší sadu dat (například operace CRUD).
- **Analytické** (online analytického zpracování (OLAP)) kdy většina dotazů, které mají složité výpočty pro generování sestav účely s počtem dotazy, které načíst a připojovat data do existující tabulky (tak jako hromadné načtení) nebo odstranit data z tabulek. 
- **Smíšené** (hybridní transakce/analytického zpracování (HTAP)) ve kterých se spouští dotazy OLTP a OLAP na stejnou sadu data.

V začleňování paměťových technologií může zlepšit výkon z těchto úloh udržováním data, která by se měly zpracovat do paměti pomocí nativní kompilace dotazu, nebo pokročilé zpracování těchto jako dávkové zpracování a SIMD pokyny, které jsou k dispozici na základní hardware.

Azure SQL Database má následující v začleňování paměťových technologií:
- *[OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)*  zvyšuje počet transakcí za sekundu a snižuje latenci a zpracování transakcí. Scénáře využívající OLTP v paměti jsou: zpracování například obchodní a hry, příjem dat ze zařízení IoT, ukládání do mezipaměti, načtení dat a dočasné tabulky a scénáře proměnné tabulky nebo událostí vysokou propustnost transakcí.
- *Clusterované indexy columnstore* snížit nároky na úložiště (až 10 x) a zlepšit výkon pro dotazy analýz a generování sestav. Můžete ji pomocí tabulky faktů v datových tržištích přizpůsobit další data v databázi a zlepšit výkon. Také vám pomůže ho s využitím historických dat v provozní databázi archivovat a mít možnost provádět dotazy až 10krát další data.
- *Neclusterovaných indexů columnstore* pro HTAP umožňují v reálném čase přehledy o svoji obchodní pozici prostřednictvím dotazování provozní databáze přímo, bez nutnosti provádět náročné extrakce, transformace a načítání (ETL) proces a počkejte datový sklad, který se má naplnit. Neclusterovaných indexů columnstore povolit rychlé spouštění analytických dotazů v databázi s online zpracováním transakcí a snižuje nutnost vliv na provozní úlohy.
- *Paměťově optimalizované Clusterované indexy columnstore* pro HTAP umožňuje provádět zpracování rychlé transakcí a získat *souběžně* velmi rychle spouštění analytických dotazů na stejná data.

Indexy columnstore a OLTP v paměti byly součástí produktu SQL Server 2012 a 2014, v uvedeném pořadí. Azure SQL Database a SQL Server sdílet stejnou implementaci technologií v paměti. Od této chvíle, nové možnosti pro tyto technologie jsou všeobecně dostupné ve službě Azure SQL Database nejprve, před jejich uvedením v systému SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Výhody technologie v paměti

Z důvodu efektivnější dotazu a zpracování transakcí v začleňování paměťových technologií také pomoci snížit náklady. Obvykle není nutné upgradovat cenovou úroveň databáze, kterou chcete dosáhnout zvýšení výkonu. V některých případech dokonce je možné snížit cenovou úroveň a současně stále se zobrazuje vylepšení výkonu díky paměťovým technologiím.

Tady jsou dva příklady jak pomohl OLTP v paměti k výraznému zlepšení výkonu:

- S použitím OLTP v paměti [kvora podniková řešení se podařilo dvakrát svoje úlohy při současném zvyšování Dtu o 70 %](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU znamená *jednotky transakcí databáze*, a obsahuje měření využití prostředků.
- Toto video ukazuje přináší značné vylepšení v spotřeba prostředků se ukázky pracovního vytížení: [OLTP v paměti v Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - Další informace najdete v blogovém příspěvku: [OLTP v paměti v příspěvku na blogu Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
>  
>  V začleňování paměťových technologií jsou k dispozici v databázích Azure SQL úrovně Premium a pro důležité obchodní informace a elastické fondy úrovně Premium.

Následující video vysvětluje potenciální zvýšení výkonu se v začleňování paměťových technologií ve službě Azure SQL Database. Mějte na paměti, zvýšení výkonu, který se zobrazí vždy závislá na mnoha faktorech, včetně povaze úloh a data, vzory přístupu databáze a tak dále.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Tento článek popisuje aspekty OLTP v paměti a columnstore indexy, které jsou specifické pro Azure SQL Database a také obsahuje ukázky:
- Zobrazí se vám dopad těchto technologií na omezení velikosti úložiště a data.
- Uvidíte jak spravovat přesunu databází, které používají tyto technologie mezi různé cenové úrovně.
- Uvidíte dvě ukázky, které ilustrují použití OLTP v paměti, stejně jako indexy columnstore Azure SQL Database.

Další informace naleznete v tématu:
- [Přehled OLTP v paměti a scénáře použití](https://msdn.microsoft.com/library/mt774593.aspx) (včetně odkazů na Zákaznické případové studie a informace, abyste mohli začít)
- [Dokumentace pro OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)
- [Průvodce indexy Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridní transakční a analytické zpracování (HTAP), označované také jako [provozní analýzy v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>OLTP v paměti

Technologie OLTP v paměti poskytuje operacemi přístupu k datům extrémně rychlé udržováním všechna data v paměti. Využívá také specializované indexy, nativní kompilace dotazů a blokátory data přistupovat ke zlepšení výkonu úlohy OLTP. Existují dva způsoby, jak uspořádat data OLTP v paměti:
- **Paměťově optimalizované rowstore** formát, kde každý řádek je samostatná paměťová objektu. Toto je classic formát OLTP v paměti optimalizované pro úlohy OLTP s vysokým výkonem. Existují dva typy paměťově optimalizované tabulky, které lze použít v paměťově optimalizovaných rowstore formátu:
  - *Trvalý tabulky* (SCHEMA_AND_DATA) kde řádků umístěný v paměti jsou zachovány po restartování serveru. Tento typ tabulky se chová jako tradiční rowstore tabulku s další výhody optimalizace v paměti.
  - *Tabulky non-durable* (SCEMA_ONLY) Pokud jsou řádky není zachována po restartování. Tento typ tabulky je určen pro dočasná data (například nahrazení dočasné tabulky), nebo tabulky, které je potřeba rychle načíst data, teprve potom přejděte na některé trvalou tabulku (tzv. pracovních tabulek).
- **Paměťově optimalizované columnstore** formát, kde jsou data uspořádána ve sloupcovém formátu. Tato struktura je určená pro HTAP scénáře, které je potřeba spouštět analytické dotazy na stejnou strukturu dat, ve kterém je spuštěná vaše úloha OLTP.

> [!Note]
> Technologie OLTP v paměti je určená pro datové struktury, které můžete plně jsou umístěné v paměti. Vzhledem k tomu, že data v paměti nejde převedených na disk, ujistěte se, že používáte databáze, která má dostatek paměti. Zobrazit [limit velikosti a skladování dat pro OLTP v paměti](#data-size-and-storage-cap-for-in-memory-oltp) další podrobnosti.

Rychlý úvod do na OLTP v paměti: [Rychlý start 1: Technologie OLTP v paměti pro rychlejší výkon T-SQL](https://msdn.microsoft.com/library/mt694156.aspx) (jiného článku vám pomůžou začít)

Podrobná videa o technologiích:

- [OLTP v paměti ve službě Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (která obsahuje ukázky přinese zlepšení výkonu a kroky pro reprodukci tyto výsledky sami)
- [OLTP v paměti videa: Co je a v případě/jak ho použít](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Existuje programový způsob, jak pochopit, jestli podporuje danou databázi OLTP v paměti. Můžete spustit následující dotaz jazyka Transact-SQL:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Pokud dotaz vrací **1**, OLTP v paměti je podporováno v této databázi. Následující dotazy Identifikujte všechny objekty, které je třeba odebrat dříve, než databáze může být downgradovat na Standard nebo Basic:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limit velikosti a skladování dat pro OLTP v paměti

OLTP v paměti obsahuje paměťově optimalizované tabulky, které se používají k ukládání dat uživatelů. Tyto tabulky jsou nutné přizpůsobit v paměti. Vzhledem k tomu, že budete spravovat paměti přímo ve službě SQL Database, máme koncept kvótu pro data uživatelů. Tento nápad se označuje jako *úložiště OLTP v paměti*.

Každý podporovaný izolované databáze cenová úroveň a každý elastický fond cenová úroveň zahrnuje množství úložiště OLTP v paměti. V tématu [omezení prostředků založený na DTU – izolované databáze](sql-database-dtu-resource-limits-single-databases.md), [omezení prostředků založený na DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).

Následující položky se počítají vaše kapacita úložiště OLTP v paměti:

- Aktivní uživatel řádky dat v paměťově optimalizovaných tabulkách a proměnné tabulky. Všimněte si, že stará verze řádků se nepočítají směrem k zakončení.
- Indexy na paměťově optimalizovaných tabulkách.
- Provozní režie operace ALTER TABLE.

Pokud dosáhnete zakončení, obdržíte chybu limit kvóty a nejste již moci vložit nebo aktualizovat data. Pro zmírnění této chyby, odstraňte data nebo zvýšení cenové úrovně služby databáze nebo fondu.

Podrobnosti o sledování využití úložiště OLTP v paměti a konfiguraci výstrah, až se dostanete téměř zakončení najdete v tématu [monitorování v paměti úložiště](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Informace o elastických fondů

S elastickými fondy úložiště OLTP v paměti se sdílí napříč všemi databázemi ve fondu. Využití v jedné databázi proto může potenciálně ovlivnit ostatní databáze. Jsou dva způsoby zmírnění rizik pro toto:

- Konfigurace `Max-eDTU` nebo `MaxvCore` pro databáze, které je nižší než počet eDTU nebo – vCore pro fond jako celek. Toto maximum caps využití úložiště OLTP v paměti, všechny databáze ve fondu, aby velikost, která odpovídá počtu eDTU.
- Konfigurace `Min-eDTU` nebo `MinvCore` , který je větší než 0. Tohle minimum zaručuje, že každá databáze ve fondu má velikost dostupného úložiště OLTP v paměti, která odpovídá nakonfigurované `Min-eDTU` nebo `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Změna úrovně služby databáze, které využívají technologie OLTP v paměti

Vždy upgradem databáze nebo instance na vyšší úroveň, například z obecné účely pro důležité obchodní informace (nebo Standard na Premium). Dostupné funkce a prostředky pouze zvýšit.

Ale Downgrade na úrovni může mít negativní vliv na vaši databázi. Dopad je zřejmé, zejména při downgradu z pro důležité obchodní informace pro obecné účely (nebo Premium na úroveň Standard nebo Basic) Pokud databáze obsahuje objekty OLTP v paměti. Paměťově optimalizované tabulky nejsou k dispozici po downgrade (i v případě, že zůstávají viditelné). Platí stejné aspekty při současném snižování cenové úrovně služby elastického fondu, nebo přesunutí databáze s začleňování paměťových technologií do Standard nebo Basic elastického fondu.

> [!Important]
> OLTP v paměti se nepodporuje na úrovni obecné účely, Standard nebo Basic. Proto není možné přesunout databázi, která obsahuje všechny objekty OLTP v paměti na úroveň Standard nebo Basic.

Než spustíte downgrade databáze Standard nebo Basic, odeberte všechny paměťově optimalizovaných tabulkách a typů tabulek, jakož i všechny nativně Kompilované moduly T-SQL. 

*Škálování dolů prostředky na úrovni pro důležité obchodní informace*: Data v paměťově optimalizovaných tabulkách musí vejít do úložiště OLTP v paměti, který je spojen s vrstvou database nebo spravované Instance nebo je k dispozici v elastickém fondu. Pokud se pokusíte o vertikální snížení kapacity na úroveň nebo přesunutí databáze do fondu, který nemá dostatek dostupného úložiště OLTP v paměti se operace nezdaří.

## <a name="in-memory-columnstore"></a>Index columnstore v paměti

Technologie indexu columnstore v paměti je povolení k ukládání a dotazování velkých objemů dat v tabulkách. Technologie indexu Columnstore používá formát úložiště dat založeného na sloupcích a dávkové zpracování dotazů k dosažení získat až 10 x výkonu dotazů v úlohy OLAP oproti tradičním úložištím založenému na záznamech. Můžete také dosáhnout zisky až 10krát komprese dat přes velikost nekomprimovaných dat.
Existují dva typy columnstore modelů, které slouží k uspořádání dat:
- **Clusterované columnstore** kde všechna data v tabulce jsou uspořádány ve sloupcovém formátu. V tomto modelu se umístí všechny řádky v tabulce ve sloupcovém formátu, který vysoce komprimuje data a můžete spustit rychlé analytické dotazy a sestavy v tabulce. V závislosti na povaze vašich dat, velikost dat může být snížený 10 x-100 x. Clusterované columnstore model umožňuje také rychlý příjem velkého objemu dat (hromadné načtení) od velké sady dat větší než 100 tisíc řádků jsou komprimované, než jsou uloženy na disku. Tento model je vhodný pro scénáře classic datového skladu. 
- **Jiné Clusterované columnstore** kde jsou data uložená v tabulce tradiční rowstore a je index ve formátu columnstore, který se používá pro analytické dotazy. Tento model umožňuje hybridní transakční analytického zpracování (HTAP): umožňuje spouštění výkonných analýz v reálném čase v transakčním zatížením. OLTP dotazy se spouštějí na rowstore tabulku, která je optimalizovaná pro přístup k malého počtu řádků, zatímco OLAP dotazy se spouštějí na index columnstore, který je vhodnější pro kontroly a analýzy. Azure SQL Database Query optimalizace dynamicky vybere rowstore nebo columnstore formát na základě dotazu. Indexů columnstore clusteru bez není snížit množství dat, protože původní sady dat se ukládají v původní tabulce rowstore bez jakékoli změny. Velikost indexu columnstore další musí však být v pořadí podle velikosti menší než ekvivalentní indexu B-stromu.

> [!Note]
> Technologie indexu columnstore v paměti sleduje pouze data, která je potřebná pro zpracování v paměti, při uložení dat, která se nevejdou do paměti na disk. Objem dat ve strukturách columnstore v paměti proto může překročit množství dostupné paměti. 

Podrobný videu o technologii:
- [Columnstore Index: Videa analýzu v paměti z Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Velikost dat a úložiště v případě indexů columnstore

Indexy Columnstore není nutné přizpůsobit v paměti. Proto pouze limit velikosti indexy, které je maximální celkovou velikost databáze, která je popsána v [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) článků.

Při použití Clusterované indexy columnstore úložiště se sloupcovou strukturou komprese se používá pro základní tabulky úložiště. Tato komprese může výrazně snížit nároky na úložiště dat uživatele, což znamená, že může obsahovat další data v databázi. A komprese můžete dál zvýšit požadavkem [úložiště se sloupcovou strukturou archivace komprese](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Komprese, které můžete dosáhnout závisí na charakteru dat, ale není, 10krát komprese.

Například pokud máte databáze s maximální velikostí 1 terabajtů (TB) a dosáhnout 10krát komprese pomocí indexů columnstore, lze zobrazit celkem 10 TB dat uživatele v databázi.

Při použití neclusterovaných indexů columnstore v základní tabulce zůstanou uložena v tradiční rowstore formátu. Proto nejsou tak velká jako v clusterovaných indexech columnstore úspory úložiště. Pokud nahrazujete počet tradiční neclusterovaných indexů s indexem columnstore jeden, uvidíte stále celkové úspory v nároky na úložiště pro tabulku.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Změna úrovně služby databáze, které obsahují indexů Columnstore

*Downgrade izolovanou databázi na Basic nebo Standard* nemusí být možné, pokud je vaše cílové úrovně nižší než S3. Indexy Columnstore jsou podporovány pouze na cenové úrovni obchodní kritický nebo Premium a na úrovni Standard S3 a výše a ne na úrovni Basic. Pokud spustíte downgrade databáze na úrovni nebo nepodporovaná úroveň, stane nedostupným indexu columnstore. Systém udržuje indexu columnstore, ale nikdy využívá index. Pokud upgradujete později zpět na podporovanou úroveň a úroveň, indexu columnstore je okamžitě připravené k využít znovu.

Pokud máte **Clusterované** columnstore index celé tabulky přestane být k dispozici po downgrade. Proto doporučujeme vyřaďte všechny *Clusterované* indexy columnstore předtím, než spustíte downgrade databáze na úrovni nebo nepodporovaná úroveň.

> [!Note]
> Managed Instance podporuje Columstore indexů ve všech úrovních.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Další postup

- [Rychlý start 1: Technologie OLTP v paměti pro dosažení vyššího výkonu T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [OLTP v paměti pro použití v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Úložiště OLTP v paměti monitorování](sql-database-in-memory-oltp-monitoring.md) pro OLTP v paměti

- [Vyzkoušet si funkce v paměti ve službě Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Další materiály

#### <a name="deeper-information"></a>Podrobnější informace.

- [Zjistěte, jak kvorum zdvojnásobuje klíčové databázové úlohy při současném snižování DTU o 70 % s OLTP v paměti ve službě SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP v paměti v příspěvku na blogu Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Další informace o OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)

- [Seznamte se s indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Další informace o provozní analýzy v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

- Zobrazit [běžné vzory úlohy a důležité informace o migraci](https://msdn.microsoft.com/library/dn673538.aspx) (které popisuje vzory zatížení kde OLTP v paměti obvykle poskytují významného zvýšení výkonu)

#### <a name="application-design"></a>Návrh aplikace

- [Paměti OLTP (Optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)

- [OLTP v paměti pro použití v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Nástroje

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
