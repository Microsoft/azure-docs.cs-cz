---
title: Azure SQL Database technologií v paměti | Microsoft Docs
description: Technologie Azure SQL Database v paměti významně zlepšují výkon transakčních a analytických úloh.
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
ms.openlocfilehash: 325dda3695e796bc0814954d3bd69b9b340133b8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567961"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimalizujte výkon pomocí technologií v paměti v SQL Database

Technologie v paměti v Azure SQL Database umožňují zvýšit výkon aplikace a potenciálně snížit náklady na vaši databázi. 

## <a name="when-to-use-in-memory-technologies"></a>Kdy použít technologie v paměti

Pomocí technologií v paměti v Azure SQL Database můžete dosáhnout zlepšení výkonu s různými úlohami:

- **Transakční** (online zpracování transakcí (OLTP)), kde většina požadavků čte nebo aktualizuje menší sadu dat (například operace CRUD).
- **Analytické** (technologie OLAP (Online Analytical Processing)), kde většina dotazů má složité výpočty pro účely generování sestav, s určitým počtem dotazů, které načítají a připojují data do existujících tabulek (tedy označované jako hromadné zatížení), nebo odstraňují data z tabulek. 
- **Smíšené** (hybridní transakce/analytické zpracování (HTAP)), kde jsou dotazy OLTP a OLAP spouštěny na stejné sadě dat.

Technologie v paměti můžou zlepšit výkon těchto úloh tím, že udržuje data, která by se měla zpracovat do paměti, pomocí nativní kompilace dotazů nebo pokročilé zpracování, jako jsou dávkové zpracování, a pokyny k SIMD, které jsou k dispozici na základní hardware. 

## <a name="overview"></a>Přehled

Azure SQL Database má následující paměťové technologie:
- *[OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* zvyšuje počet transakcí za sekundu a snižuje latenci pro zpracování transakcí. Scénáře, které využívají OLTP v paměti, jsou: vysoce propustnost při zpracování transakcí, jako je například obchodování a hraní, příjem dat z událostí nebo zařízení IoT, ukládání do mezipaměti, načítání dat a dočasné scénáře tabulkových a tabulkových proměnných.
- *Clusterované indexy columnstore* snižují nároky na úložiště (až 10krát) a zlepšují výkon pro vytváření sestav a analytické dotazy. Můžete ji použít s tabulkami faktů ve vašich datech tržiště k tomu, aby se do databáze vešla více dat a vylepšila výkon. Můžete ho také použít s historickými daty v provozní databázi k archivaci a budete moct dotazovat až na 10 dalších dat.
- *Neclusterované indexy columnstore* pro HTAP vám pomůžou získat přehled o vaší firmě v reálném čase pomocí dotazování provozní databáze přímo, aniž by bylo nutné spouštět nákladný proces extrakce, transformace a načítání (ETL) a čekat na data. Sklad, který má být vyplněn. Neclusterované indexy columnstore umožňují rychlé spouštění analytických dotazů v databázi OLTP a současně snižuje dopad na provozní zatížení.
- *Clusterově optimalizované indexy columnstore* pro HTAP umožňují provádět rychlé zpracování transakcí a souběžně spouštějte analytické dotazy se stejnými daty.

Indexy columnstore i OLTP v paměti byly součástí SQL Server produktu od 2012 do 2014, v uvedeném pořadí. Azure SQL Database a SQL Server sdílejí stejnou implementaci technologií v paměti. Kromě toho se nové funkce pro tyto technologie vydávají v Azure SQL Database nejdřív, než se uvolní v SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Výhody technologie v paměti

Z důvodu efektivnějšího zpracování dotazů a transakcí vám také pomůžou snížit náklady. Obvykle není nutné upgradovat cenovou úroveň databáze, aby se dosáhlo zvýšení výkonu. V některých případech můžete dokonce snížit cenovou úroveň, a přitom stále vidět zvýšení výkonu s využitím technologií v paměti.

Tady jsou dva příklady, jak OLTP v paměti pomohl významně zlepšit výkon:

- Při použití OLTP v paměti [bylo řešení kvora schopno dvojnásobit své zatížení a zároveň zlepšuje DTU o 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU znamená *jednotku transakce databáze*a obsahuje měření spotřeby prostředků.
- Následující video demonstruje významné vylepšení spotřeby prostředků pomocí ukázkového zatížení: [OLTP v paměti na Azure SQL Database video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Další informace najdete v blogovém příspěvku: [OLTP v paměti v příspěvku Azure SQL Database na blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Technologie v paměti jsou dostupné v databázích Azure SQL úrovně Premium a Pro důležité obchodní informace a elastických fondech úrovně Premium.

Následující video vysvětluje potenciální nárůst výkonu pomocí technologií v paměti v Azure SQL Database. Mějte na paměti, že ke zvýšení výkonu, které vidíte, se vždy závisí na mnoha faktorech, včetně povahy zatížení a dat, vzoru přístupu databáze a tak dále.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Tento článek popisuje aspekty OLTP a indexů columnstore v paměti, které jsou specifické pro Azure SQL Database a také obsahuje ukázky:

- V omezeních velikosti úložiště a dat uvidíte dopad těchto technologií.
- Uvidíte, jak spravovat přesun databází, které používají tyto technologie mezi různými cenovými úrovněmi.
- Zobrazí se dvě ukázky, které ilustrují použití OLTP v paměti a také indexy columnstore v Azure SQL Database.

Další informace naleznete v tématu:

- [OLTP v paměti – přehled a scénáře použití](https://msdn.microsoft.com/library/mt774593.aspx) (obsahuje odkazy na Zákaznické případové studie a informace pro začátek)
- [Dokumentace k OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)
- [Průvodce indexy columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybridní transakční/analytické zpracování (HTAP), označované také jako [provozní analýza v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>OLTP v paměti

Technologie OLTP v paměti poskytuje extrémně rychlé operace přístupu k datům a zajišťuje tak všechna data v paměti. Používá také specializované indexy, nativní kompilaci dotazů a přístup k datům bez západek ke zvýšení výkonu OLTP úloh. Existují dva způsoby, jak uspořádat data OLTP v paměti:

- **Paměťově optimalizované formáty rowstore** , kde každý řádek je samostatný objekt paměti. Toto je klasický formát OLTP v paměti optimalizovaný pro vysoce výkonné OLTP úlohy. Existují dva typy paměťově optimalizovaných tabulek, které lze použít v paměťově optimalizovaném formátu rowstore:
  - *Trvalé tabulky* (SCHEMA_AND_DATA), kde jsou po restartování serveru zachované řádky uložené v paměti. Tento typ tabulek se chová jako tradiční tabulka rowstore s dalšími výhodami optimalizace v paměti.
  - *Tabulky, které nejsou odolné* (SCHEMA_ONLY), kde se po restartu neuchovávají řádky. Tento typ tabulky je navržený pro dočasná data (například nahrazení dočasných tabulek) nebo tabulky, ve kterých potřebujete rychle načíst data, než je přesunete do některé trvale uložené tabulky (takže se říká pracovní tabulky).
- Formát **columnstore optimalizovaný pro paměť** , ve kterém jsou data uspořádána ve sloupcovém formátu. Tato struktura je navržená pro HTAP scénáře, kdy potřebujete spouštět analytické dotazy ve stejné datové struktuře, ve které je spuštěná vaše úloha OLTP.

> [!Note]
> Technologie OLTP v paměti je navržená pro datové struktury, které se můžou plně nacházet v paměti. Vzhledem k tomu, že data v paměti nelze přesměrovat na disk, ujistěte se, že používáte databázi, která má dostatek paměti. Další podrobnosti najdete [v tématu velikost dat a limit úložiště pro OLTP v paměti](#data-size-and-storage-cap-for-in-memory-oltp) .

Rychlý úvod k OLTP v paměti: [Rychlý Start 1: Technologie OLTP v paměti pro rychlejší výkon](https://msdn.microsoft.com/library/mt694156.aspx) T-SQL (další článek, který vám pomůžete začít)

Podrobnější videa o technologiích:

- [OLTP v paměti v Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (obsahuje ukázku výhod výkonu a kroky pro reprodukování těchto výsledků)
- [OLTP videa v paměti: Co je a kdy/jak ho používat](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Existuje programový způsob, jak pochopit, jestli daná databáze podporuje OLTP v paměti. Můžete spustit následující dotaz Transact-SQL:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Pokud dotaz vrátí **1**, v této databázi se podporuje OLTP v paměti. Následující dotazy identifikují všechny objekty, které je třeba před přechodem na úroveň Standard/Basic odebrat.
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Velikost dat a limit úložiště pro OLTP v paměti

OLTP v paměti zahrnuje paměťově optimalizované tabulky, které se používají k ukládání uživatelských dat. Tyto tabulky jsou nutné pro přizpůsobení paměti. Vzhledem k tomu, že můžete spravovat paměť přímo ve službě SQL Database, máme koncept kvóty pro uživatelská data. Tento nápad se označuje jako *úložiště OLTP v paměti*.

Každá podporovaná cenová úroveň jedné databáze a každá cenová úroveň elastického fondu zahrnuje určité množství OLTP úložiště v paměti. Viz omezení [prostředků na základě DTU –](sql-database-dtu-resource-limits-single-databases.md)izolovaná databáze, [omezení prostředků založené na DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků založené na Vcore – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založené na Vcore – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Následující položky se počítají do limitu úložiště OLTP v paměti:

- Řádky aktivních uživatelských dat v paměťově optimalizovaných tabulkách a proměnných tabulek. Všimněte si, že staré verze řádků se nepočítají směrem k zakončení.
- Indexy na paměťově optimalizované tabulky.
- Provozní režie operací ALTER TABLE

Pokud máte limit, obdržíte chybu s nekvótou a už nebudete moct data vkládat ani aktualizovat. Chcete-li tuto chybu zmírnit, odstraňte data nebo zvyšte cenovou úroveň databáze nebo fondu.

Podrobnosti o monitorování využití úložiště v paměti OLTP a konfiguraci výstrah, když skoro vyrazíte na limit, najdete v tématu [monitorování úložiště v paměti](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>O elastických fondech

U elastických fondů je úložiště OLTP v paměti sdíleno napříč všemi databázemi ve fondu. Proto použití v jedné databázi může mít potenciálně vliv na ostatní databáze. Dvě zmírnění rizika jsou tato:

- `Max-eDTU` Nakonfigurujte nebo `MaxvCore` pro databáze, které jsou nižší než eDTU nebo počet Vcore pro fond jako celek. Toto je maximální využití úložiště OLTP v paměti ve všech databázích ve fondu na velikost, která odpovídá počtu eDTU.
- `Min-eDTU` Nakonfigurujte nebo `MinvCore` , který je větší než 0. Tato minimální záruka zaručuje, že každá databáze ve fondu má velikost dostupného úložiště OLTP v paměti, které odpovídá nakonfigurovaným `Min-eDTU` nebo. `vCore`

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Změna úrovní služeb databází, které používají technologie OLTP v paměti

Databázi nebo instanci můžete kdykoli upgradovat na vyšší úroveň, například z Pro obecné účely na Pro důležité obchodní informace (nebo do úrovně Premium). Dostupné funkce a prostředky se zvyšují.

Ale downgrade úrovně může mít negativní vliv na vaši databázi. Pokud vaše databáze obsahuje objekty OLTP v paměti, je to obzvláště zřejmé, když se downgrade z Pro důležité obchodní informace na Pro obecné účely (nebo Premium na Standard nebo Basic). Paměťově optimalizované tabulky nejsou po downgradi k dispozici (i když zůstanou viditelné). Stejné předpoklady platí při snížení cenové úrovně elastického fondu nebo při přesunu databáze s technologiemi v paměti do elastického fondu Standard nebo Basic.

> [!Important]
> OLTP v paměti se v úrovni Pro obecné účely, Standard a Basic nepodporuje. Proto není možné přesunout databázi, která má žádné objekty OLTP v paměti na úroveň Standard nebo Basic.

Před přechodem databáze na úroveň Standard/Basic odeberte všechny paměťově optimalizované tabulky a typy tabulek a také všechny nativně zkompilované moduly T-SQL. 

*Škálování prostředků na úrovni pro důležité obchodní informace*: Data v paměťově optimalizovaných tabulkách se musí vejít do úložiště OLTP v paměti, které je přidružené k úrovni databáze nebo spravované instance, nebo je k dispozici v elastickém fondu. Pokud se pokusíte škálovat vrstvu dolů nebo přesunout databázi do fondu, který nemá dost dostupného úložiště OLTP v paměti, operace se nezdařila.

## <a name="in-memory-columnstore"></a>Columnstore v paměti

Technologie columnstore v paměti umožňuje ukládat a dotazovat velké objemy dat v tabulkách. Technologie columnstore používá formát úložiště dat založeného na sloupcích a zpracování dávkového dotazování, aby bylo možné získat až desetkrát výkon dotazů v úlohách OLAP v případě tradičních sloupcově orientovaného úložiště. Pro komprimaci dat v nekomprimovaných datech můžete také dosáhnout nárůstu až desetkrát.
Existují dva typy modelů columnstore, které můžete použít k uspořádání dat:

- **Clusterovaný columnstore** , kde jsou všechna data v tabulce uspořádána ve sloupcovém formátu. V tomto modelu jsou všechny řádky v tabulce umístěny ve sloupcovém formátu, který silně komprimuje data a umožňuje provádět rychlé analytické dotazy a sestavy v tabulce. V závislosti na povaze dat se může velikost dat snížit 10x-100x. Clusterovaný model columnstore také umožňuje rychlou příjem velkých objemů dat (hromadné zatížení), protože velké dávky dat větší než 100 tisíc řádky jsou komprimovány před jejich uložením na disk. Tento model je dobrou volbou pro scénáře klasického datového skladu. 
- **Neclusterovaný columnstore** , kde jsou uložena data v tradiční tabulce rowstore a ve formátu columnstore, který se používá pro analytické dotazy, je index. Tento model umožňuje hybridní transakční a analytické zpracování (HTAP): schopnost spouštět provádění analýz v reálném čase na transakčních úlohách. Dotazy OLTP se spouštějí v tabulce rowstore, která je optimalizovaná pro přístup k malé sadě řádků, zatímco dotazy OLAP se spouštějí v indexu columnstore, který je lepší volbou pro kontroly a analýzy. Azure SQL Database Optimalizátor dotazů dynamicky zvolí rowstore nebo formát columnstore na základě dotazu. Neclusterované indexy columnstore nezmenšují velikost dat, protože původní sada dat je udržována v původní tabulce rowstore beze změny. Nicméně velikost dalšího indexu columnstore by měla být v rozsahu menším než ekvivalentní index B-Tree.

> [!Note]
> Technologie columnstore v paměti uchovává pouze data potřebná pro zpracování v paměti, zatímco data, která se nevejdou do paměti, jsou uložená na disku. Proto množství dat v strukturách columnstore v paměti může překročit množství dostupné paměti. 

Podrobné video o technologii:

- [Index columnstore: Videa k analýze v paměti z Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Velikost dat a úložiště pro indexy columnstore

Indexy columnstore se nevyžadují pro přizpůsobení paměti. Jediným limitem velikosti indexů je tedy maximální celková velikost databáze, která je dokumentována v [nákupních modelech založených na DTU](sql-database-service-tiers-dtu.md) a v článcích [pro nákupní modely založené na Vcore](sql-database-service-tiers-vcore.md) .

Při použití clusterovaných indexů columnstore se pro základní úložiště tabulek používá sloupcová komprese. Tato komprese může významně snížit nároky na úložiště vašich uživatelských dat, což znamená, že do databáze můžete přizpůsobovat další data. A komprimaci lze dále zvýšit pomocí [sloupcové komprese](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Velikost komprese, kterou můžete dosáhnout, závisí na povaze dat, ale na 10 časech komprese není neobvyklá.

Pokud máte například databázi s maximální velikostí 1 terabajt (TB) a dosáhnete 10 krát komprese pomocí indexů columnstore, můžete v databázi použít celkem 10 TB uživatelských dat.

Při použití neclusterovaných indexů columnstore je základní tabulka stále uložena v tradičním formátu rowstore. Proto úspory úložiště nejsou tak velké jako u clusterovaných indexů columnstore. Pokud ale nahrazujete několik tradičních neclusterovaných indexů s jedním indexem columnstore, můžete si i nadále zobrazit celkové úspory v kapacitě úložiště pro danou tabulku.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Změna úrovní služeb databází obsahujících indexy columnstore

Pokud je cílová úroveň nižší než S3, nemusí být *databáze na úrovni Basic nebo Standard* dostupná. Indexy columnstore jsou podporované jenom na cenové úrovni Pro důležité obchodní informace/Premium a na úrovni Standard, S3 a vyšší, a ne na úrovni Basic. Když provedete downgrade databáze na nepodporovanou úroveň nebo úroveň, váš index columnstore nebude k dispozici. Systém udržuje index columnstore, ale nikdy nevyužívá index. Pokud později upgradujete zpátky na podporovanou úroveň nebo úroveň, váš index columnstore je hned připravený k jeho využití.

Pokud máte **clusterovaný** index columnstore, celá tabulka po downgradu nebude k dispozici. Proto doporučujeme, abyste před přechodem databáze na nepodporovanou úroveň nebo úroveň vyřadíte všechny clusterované indexy columnstore.

> [!Note]
> Spravovaná instance podporuje indexy ColumnStore ve všech vrstvách.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Další postup

- [Rychlý Start 1: Technologie OLTP v paměti pro rychlejší výkon T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)
- [Použití OLTP v paměti v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)
- [Monitorování úložiště OLTP v paměti](sql-database-in-memory-oltp-monitoring.md) pro OLTP v paměti
- [Vyzkoušení funkcí v paměti v Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Další zdroje

### <a name="deeper-information"></a>Hlubší informace

- [Přečtěte si, jak kvorum podvoje úlohy klíčové databáze při snižování DTU o 70% s OLTP v paměti v SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [OLTP v paměti v příspěvku Azure SQL Database na blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Další informace o OLTP v paměti](https://msdn.microsoft.com/library/dn133186.aspx)
- [Další informace o indexech columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- [Další informace o provozní analýze v reálném čase](https://msdn.microsoft.com/library/dn817827.aspx)
- Podívejte se [na téma běžné vzorce úloh a důležité informace k migraci](https://msdn.microsoft.com/library/dn673538.aspx) (které popisují vzorce úloh, které OLTP v paměti často přináší výrazné zvýšení výkonu).

### <a name="application-design"></a>Návrh aplikací

- [OLTP v paměti (optimalizace v paměti)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Použití OLTP v paměti v existující aplikaci Azure SQL](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Nástroje

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
