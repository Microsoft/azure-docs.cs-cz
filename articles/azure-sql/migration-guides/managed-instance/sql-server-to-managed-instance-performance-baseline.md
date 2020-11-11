---
title: SQL Server k analýze výkonu spravované instance SQL
description: Naučte se vytvářet a porovnávat základní údaje o výkonu při migraci SQL Serverch databází do spravované instance Azure SQL.
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: df53f4912108962e9d50400c4c2516aefaa50976
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496592"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Výkon migrace: SQL Server k analýze výkonu spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Vytvořte základní úroveň výkonu pro porovnání výkonu vašich úloh na spravované instanci SQL s původní úlohou běžící na SQL Server. 

## <a name="create-a-baseline"></a>Vytvořit směrný plán

V ideálním případě je výkon po migraci podobný nebo lepší, takže je důležité měřit a zaznamenávat hodnoty výkonu směrného plánu na zdroji a pak je porovnat s cílovým prostředím. Základní hodnota výkonu je sada parametrů, které definují průměrné zatížení zdroje. 

Vyberte sadu dotazů, které jsou důležité pro, a zástupce firemních úloh. Změřte a zdokumentujte minimální/průměrnou dobu trvání a využití procesoru pro tyto dotazy a také metriky výkonu na zdrojovém serveru, jako je průměrné nebo maximální využití procesoru, průměr/maximální latence v/v disku, propustnost, IOPS, průměr/maximum očekávané životnosti stránky a Průměrná velikost databáze tempdb. 

Následující zdroje informací mohou napomoci definování standardních hodnot výkonu: 

   - [Monitorovat využití procesoru ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Monitorovat využití paměti](/sql/relational-databases/performance-monitor/monitor-memory-usage)   a určíte velikost paměti využívané různými součástmi, jako je fond vyrovnávací paměti, mezipaměť plánu, fond úložiště sloupců, [OLTP v paměti](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage), atd. Kromě toho byste měli najít průměrnou a maximální hodnotu čítače výkonu životnosti stránky očekávané paměti. 
   - Monitorujte využití v/v disku u zdrojové SQL Server instance pomocí [Sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   zobrazení nebo [čítačů výkonu](/sql/relational-databases/performance-monitor/monitor-disk-usage). 
   - Monitorujte výkon úloh a dotazů pomocí zobrazení dynamické správy (nebo úložiště dotazů, pokud migrujete z SQL Server 2016 a novější). Identifikujte Průměrné trvání a využití procesoru u nejdůležitějších dotazů v rámci vašeho zatížení. 

Před migrací by se měly řešit všechny problémy s výkonem ve zdrojovém SQL Server. Migrace známých problémů do jakéhokoli nového systému může způsobit neočekávané výsledky a zrušení platnosti všech porovnání výkonu. 


## <a name="compare-performance"></a>Porovnání výkonu 

Po definování směrného plánu Porovnejte podobný výkon úloh na cílové spravované instanci SQL. Pro přesnost je důležité, aby prostředí spravované instance SQL bylo srovnatelné s SQL Server prostředí co nejvíce. 

Existují rozdíly v infrastruktuře správy SQL spravované instance, které by měly odpovídající výkon přesně nepravděpodobné. Některé dotazy můžou běžet rychleji, než se očekávalo, zatímco jiné můžou být pomalejší. Cílem tohoto porovnání je ověřit, že výkon úlohy ve spravované instanci odpovídá výkonu na SQL Server (průměrně) a identifikovat všechny kritické dotazy s výkonem, které neodpovídají původnímu výkonu. 

Výsledkem porovnání výkonu může být následující výsledek: 

- Výkon úloh na spravované instanci je zarovnán nebo lepší, než výkon úloh na zdrojovém SQL Server. V tomto případě jste úspěšně potvrdili, že migrace proběhla úspěšně. 

- Většina parametrů výkonu a dotazů v úlohách probíhá podle očekávání, s některými výjimkami, které vedou ke snížení výkonu. V takovém případě Identifikujte rozdíly a jejich důležitost. Pokud existují některé důležité dotazy s sníženým výkonem, prozkoumejte, zda se změnily příslušné plány SQL, nebo zda jsou dotazy v omezeních prostředků. To můžete zmírnit použitím některých doporučení pro kritické dotazy (například změnit úroveň kompatibility, starší mohutnost estimator), a to buď přímo, nebo pomocí vodítek plánů. Zajistěte, aby byly statistiky a indexy aktuální a měly v obou prostředích stejný ekvivalent. 

- Většina dotazů je ve srovnání s vaší zdrojovou instancí SQL Server pomalejší na spravované instanci. V takovém případě se pokuste určit hlavní příčiny rozdílů, jako je třeba [dosažení některých omezení prostředků](../../managed-instance/resource-limits.md#service-tier-characteristics) , jako jsou vstupně-výstupní operace, paměť nebo omezení přenosové rychlosti protokolu instancí. Pokud neexistují žádná omezení prostředků způsobující rozdíl, zkuste změnit úroveň kompatibility databáze nebo změnit nastavení databáze, jako je například odhad mohutnosti starší verze, a spusťte test znovu. Přečtěte si doporučení poskytovaná zobrazeními spravované instance nebo úložiště dotazů a Identifikujte dotazy s navráceným výkonem. 

Spravovaná instance SQL má vestavěnou funkci automatického naplánování, která je ve výchozím nastavení povolená. Tato funkce zajišťuje, že dotazy, které pracovaly v minulosti, nejsou v budoucnu degradované. Pokud tato funkce není povolená, spusťte úlohu se starým nastavením, aby se SQL Managed instance dozvěděla o standardních hodnotách výkonu. Pak tuto funkci povolte a znovu spusťte úlohu s novým nastavením. 

Proveďte změny v parametrech testu nebo upgradujte na vyšší úrovně služeb, abyste dosáhli optimální konfigurace pro výkon úloh, které vyhovují vašim potřebám. 

## <a name="monitor-performance"></a>Monitorování výkonu 

SQL Managed instance poskytuje pokročilé nástroje pro monitorování a řešení potíží a je vhodné je použít k monitorování výkonu vaší instance. Mezi klíčové metriky, které je potřeba monitorovat, patří: 

- Využití CPU u instance k určení, jestli počet virtuální jádra, který jste zřídili, je správná shoda pro vaše zatížení. 
- Očekávané životního cyklu stránky na spravované instanci, abyste zjistili, jestli potřebujete [Další paměť](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444).
-  Statistiky, jako je INSTANCE_LOG_GOVERNOR nebo PAGEIOLATCH, které identifikují vstupně-výstupní problémy s úložištěm, zejména na Pro obecné účely úrovni, kde možná budete muset předem přidělit soubory, abyste získali lepší vstupně-výstupní operace. 


## <a name="considerations"></a>Požadavky  

Při porovnávání výkonu Vezměte v úvahu následující skutečnosti: 

- Nastavení se shodují mezi zdrojem a cílem. Ověřte, že různá nastavení instance, databáze a databáze tempdb jsou ekvivalentní mezi oběma prostředími. Rozdíly v konfiguraci, úrovních kompatibility, nastaveních šifrování, příznakech trasování atd. můžou mít vliv na výkon. 

- Úložiště se konfiguruje podle [osvědčených postupů](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525). Například pro Pro obecné účely může být nutné předem přidělit velikost souborů pro zlepšení výkonu. 

- Existují [klíčové rozdíly v prostředí](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) , které mohou způsobit rozdíly v výkonu mezi spravovanou instancí a SQL Server. Identifikujte rizika relevantní pro vaše prostředí, která by mohla přispět k potížím s výkonem. 

- Úložiště dotazů a automatické ladění by měly být na spravované instanci SQL povolené, protože pomáhají měřit výkon úloh a automaticky zmírnit potenciální problémy s výkonem. 



## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nového prostředí spravované instance Azure SQL najdete v následujících zdrojích informací: 

- [Jak zjistit, proč se výkon úloh na spravované instanci Azure SQL liší od SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Klíčové příčiny rozdílů mezi výkonem spravované instance SQL a SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Doporučené postupy a požadavky na výkon úložiště pro spravovanou instanci Azure SQL (Pro obecné účely)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Monitorování výkonu v reálném čase pro spravovanou instanci Azure SQL (Toto je archivováno, je to zamýšlený cíl?)](https://docs.microsoft.com/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)
