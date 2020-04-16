---
title: Přehled hyperškálování databáze Azure SQL | Dokumenty společnosti Microsoft
description: Tento článek popisuje úroveň služby Hyperscale v nákupním modelu založeném na virtuálních jádrech v Azure SQL Database a vysvětluje, jak se liší od úrovní služeb obecné účely a důležité pro podnikání.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 074a28af8c80c109dbe97306900e8f00618e435a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411683"
---
# <a name="hyperscale-service-tier"></a>Hyperškálování úrovně služby

Azure SQL Database je založen na architektuře SQL Server Database Engine, která je upravena pro cloudové prostředí, aby byla zajištěna 99,99% dostupnost i v případě selhání infrastruktury. Existují tři architektury modely, které se používají v Azure SQL Database:
- Obecný účel/standard 
-  Hyperškálování
-  Kritické pro podnikání/prémiové

Úroveň služby Hyperscale v Azure SQL Database je nejnovější úroveň služeb v nákupním modelu založeném na virtuálních jádrech. Tato úroveň služeb je vysoce škálovatelná úroveň výkonu úložiště a výpočetního výkonu, která využívá architekturu Azure k škálování prostředků úložiště a výpočetních prostředků pro Azure SQL Database podstatně nad rámec limitů dostupných pro úrovně služeb Obecné účely a Důležité pro podnikání.

> 
> [!NOTE]
> Podrobnosti o úrovních služeb obecné účely a důležité pro podnikání v nákupním modelu založeném na virtuálních jádrech najdete v tématu Úrovně služeb [obecné účely](sql-database-service-tier-general-purpose.md) a Důležité [pro podnikání.](sql-database-service-tier-business-critical.md) Porovnání nákupního modelu založeného na virtuálních jádrech s nákupním modelem založeným na DTU najdete v [tématu Nákupní modely a prostředky azure SQL Database](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Jaké jsou funkce Hyperscale

Úroveň služby Hyperscale v Azure SQL Database poskytuje následující další funkce:

- Podpora až 100 TB velikosti databáze
- Téměř okamžité zálohování databáze (na základě snímků souborů uložených v úložišti objektů Blob Azure) bez ohledu na velikost bez dopadu vi na výpočetní prostředky  
- Rychlé obnovení databáze (na základě snímků souborů) v minutách, nikoli v hodinách nebo dnech (nikoli o velikosti datové operace)
- Vyšší celkový výkon díky vyšší propustnostprotokolu a rychlejším časům potvrzení transakcí bez ohledu na objemy dat
- Rychlé horizontální navýšení kapacity – můžete zřídit jeden nebo více uzlů jen pro čtení pro snižování zátěže čtení a pro použití jako pohotovostní režimy
- Rychlé škálování – můžete v konstantním čase vertikálně navýšit kapacitu výpočetních prostředků tak, aby vyhovovaly velkým úlohám v případě potřeby, a pak škálovat výpočetní prostředky zpět dolů, když to není potřeba.

Úroveň služby Hyperscale odstraňuje mnoho praktických omezení tradičně vidět v cloudových databázích. Kde většina ostatních databází jsou omezeny prostředky, které jsou k dispozici v jednom uzlu, databáze ve vrstvě služby Hyperscale nemají žádná taková omezení. Díky flexibilní úložné architektuře se úložiště podle potřeby rozrůstá. Ve skutečnosti hyperškálovací databáze nejsou vytvořeny s definovanou maximální velikostí. Databáze Hyperscale se podle potřeby zvětšuje – a fakturuje se vám pouze kapacita, kterou používáte. Pro úlohy náročné na čtení vrstvy služby Hyperscale poskytuje rychlé škálování zřazením další chod repliky pro čtení podle potřeby pro snižování zátěže čtení úloh.

Navíc čas potřebný k vytvoření záloh y databáze nebo vertikálně navýšit nebo snížit kapacitu již není vázána na objem dat v databázi. Hyperškálované databáze lze zálohovat prakticky okamžitě. Můžete také škálovat databázi v desítkách terabajtů nahoru nebo dolů během několika minut. Tato funkce vás osvobozuje od obav o boxování v počáteční volby konfigurace.

Další informace o velikosti výpočetních prostředků pro úroveň služby Hyperscale najdete v tématu [Vlastnosti úrovně služby](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kdo by měl zvážit úroveň služby Hyperscale

Úroveň služby Hyperscale je určena pro většinu obchodních úloh, protože poskytuje velkou flexibilitu a vysoký výkon s nezávisle škálovatelnými výpočetními prostředky a prostředky úložiště. Díky možnosti automatického škálování úložiště až na 100 TB je to skvělá volba pro zákazníky, kteří:

- Měj velké databáze v místním prostředí a chtějí modernizovat své aplikace přechodem do cloudu
- Jsou již v cloudu a jsou omezeny omezení maximální velikost databáze jiných úrovní služeb (1–4 TB)
- Mít menší databáze, ale vyžadují rychlé vertikální a horizontální výpočetní škálování, vysoký výkon, okamžité zálohování a rychlé obnovení databáze.

Úroveň služby Hyperscale podporuje širokou škálu úloh SQL Serveru, od čistého OLTP až po čistou analýzu, ale je primárně optimalizována pro úlohy OLTP a hybridní transakce a analytické zpracování (HTAP).

> [!IMPORTANT]
> Elastické fondy nepodporují úroveň služby Hyperscale.

## <a name="hyperscale-pricing-model"></a>Cenový model hyperškálování

Úroveň služby Hyperscale je k dispozici pouze v [modelu virtuálních jader](sql-database-service-tiers-vcore.md). Chcete-li zarovnat s novou architekturou, cenový model se mírně liší od úrovně služeb obecné účely nebo důležité pro podnikání:

- **Vypočítat**:

  Výpočetní jednotková cena hyperškálování je za repliku. Cena [hybridnívýhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) se použije pro automatické čtení replik škálovacístupnice. Ve výchozím nastavení vytvoříme primární repliku a jednu repliku jen pro čtení na databázi Hyperscale.  Uživatelé mohou upravit celkový počet replik včetně primární z 1-5.

- **Skladování**:

  Při konfiguraci databáze Hyperscale není nutné zadávat maximální velikost dat. Ve vrstvě hyperškálování se vám bude účtovat úložiště pro vaši databázi na základě skutečného přidělení. Úložiště se automaticky přiděluje mezi 40 GB a 100 TB v krocích po 10 GB. V případě potřeby může růst více datových souborů současně. Databáze Hyperscale je vytvořena s počáteční velikostí 10 GB a začne růst o 10 GB každých 10 minut, dokud nedosáhne velikosti 40 GB.

Další informace o cenách hyperškálování najdete v tématu [Azure SQL Database Pricing](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architektura distribuovaných funkcí

Na rozdíl od tradičních databázových strojů, které centralizovaly všechny funkce správy dat v jednom umístění/procesu (i takzvané distribuované databáze v produkčním prostředí mají dnes více kopií monolitického datového stroje), databáze Hyperscale odděluje modul pro zpracování dotazů, kde se sémantika různých datových motorů liší od komponent, které poskytují dlouhodobé ukládání a trvanlivost dat. Tímto způsobem lze kapacitu úložiště plynule škálovat podle potřeby (počáteční cíl je 100 TB). Repliky jen pro čtení sdílejí stejné součásti úložiště, takže k skopírování nové čitelné repliky není potřeba žádná kopie dat. 

Následující diagram znázorňuje různé typy uzlů v databázi Hyperscale:

![Architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Databáze Hyperscale obsahuje následující různé typy součástí:

### <a name="compute"></a>Služba Compute

Výpočetní uzel je místo, kde relační modul žije, takže všechny prvky jazyka, zpracování dotazů a tak dále, dojít. Všechny interakce uživatele s hyperškálovatnou databází probíhají prostřednictvím těchto výpočetních uzlů. Výpočetní uzly mají mezipaměti založené na SSD (označené RBPEX - rozšíření fondu odolné vyrovnávací paměti v předchozím diagramu), aby se minimalizoval počet síťových zpátečních cest potřebných k načtení stránky dat. Existuje jeden primární výpočetní uzel, kde jsou zpracovány všechny úlohy pro čtení a zápis. Existuje jeden nebo více sekundárnívýpočetníu uzly, které fungují jako horké pohotovostní uzly pro účely převzetí služeb při selhání, stejně jako fungovat jako výpočetní uzly jen pro čtení pro snižování zatížení čtení (pokud je tato funkce žádoucí).

### <a name="page-server"></a>Stránkový server

Stránkovací servery jsou systémy představující modul úložiště s horizontálním navýšením kapacity.  Každý stránkový server je zodpovědný za podmnožinu stránek v databázi.  Nominálně každý stránkový server řídí mezi 128 GB a 1 TB dat. Žádná data jsou sdílena na více než jeden stránkový server (mimo repliky, které jsou uchovávány pro redundanci a dostupnost). Úkolem stránkovacího serveru je obsluhovat databázové stránky na základě výpočetních uzlů na vyžádání a udržovat stránky aktualizované při aktualizaci dat transakcí. Stránkovací servery jsou průběžně aktuální přehráváním záznamů protokolu ze služby protokolu. Stránkovací servery také udržují mezipaměti založené na SSD pro zvýšení výkonu. Dlouhodobé ukládání datových stránek se uchovává ve službě Azure Storage pro další spolehlivost.

### <a name="log-service"></a>Služba protokolu

Služba protokolu přijímá záznamy protokolu z primární výpočetní repliky, zachová je v trvalé mezipaměti a předá záznamy protokolu ostatním výpočetním replikám (aby mohly aktualizovat své mezipaměti) a také příslušné stránkové servery, aby tam mohla být data aktualizována. Tímto způsobem jsou všechny změny dat z primární výpočetní repliky šířeny prostřednictvím služby protokolu do všech sekundárních výpočetních replik a stránkových serverů. Nakonec záznamy protokolu jsou vytlačeny do dlouhodobého úložiště ve službě Azure Storage, což je prakticky nekonečné úložiště úložiště. Tento mechanismus odstraňuje potřebu časté zkrácení protokolu. Služba protokolu má také místní mezipaměť pro urychlení přístupu k záznamům protokolu.

### <a name="azure-storage"></a>Úložiště Azure

Azure Storage obsahuje všechny datové soubory v databázi. Stránkovací servery udržují datové soubory ve službě Azure V aktuálním stavu. Toto úložiště se používá pro účely zálohování, stejně jako pro replikaci mezi oblastmi Azure. Zálohy jsou implementovány pomocí snímků úložiště datových souborů. Operace obnovení pomocí snímků jsou rychlé bez ohledu na velikost dat. Data lze obnovit do libovolného bodu v čase v rámci doby uchování zálohy databáze.

## <a name="backup-and-restore"></a>Zálohování a obnovení

Zálohy jsou založeny na snímcích souborů, a proto jsou téměř okamžité. Oddělení úložiště a výpočetních prostředků umožňuje stlačování operace zálohování a obnovení do vrstvy úložiště, aby se snížilo zatížení zpracování primární výpočetní repliky. V důsledku toho zálohování databáze nemá vliv na výkon primárnívýpočetní uzel; podobně obnovení se provádí návratem k snímek souboru a jako takové nejsou velikost operace dat. Obnovení je operace s konstantním časem a dokonce i víceterabajtové databáze lze obnovit během několika minut namísto hodin nebo dnů. Vytvoření nových databází obnovením existující zálohy také využívá této funkce: vytváření databázových kopií pro účely vývoje nebo testování, dokonce i databází velikosti terabajtů, je proveditelné během několika minut.

## <a name="scale-and-performance-advantages"></a>Výhody škálování a výkonu

Díky možnosti rychlého rozběhnutí dalších výpočetních uzlů jen pro čtení umožňuje architektura Hyperscale významné možnosti škálování čtení a může také uvolnit primární výpočetní uzel pro obsluhu více požadavků na zápis. Výpočetní uzly lze také rychle škálovat nahoru/dolů díky architektuře sdíleného úložiště architektury Hyperscale.

## <a name="create-a-hyperscale-database"></a>Vytvoření hyperškálovací databáze

Databázi Hyperscale lze vytvořit pomocí [portálu Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) nebo [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Hyperškálované databáze jsou k dispozici pouze pomocí [nákupního modelu založeného na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Následující příkaz T-SQL vytvoří databázi Hyperscale. V příkazu je nutné zadat `CREATE DATABASE` cíl edice i služby. Seznam platných cílů služby naleznete v [omezeních zdrojů.](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4)

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Tím se vytvoří databáze Hyperscale na hardwaru Gen5 se 4 jádry.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrace existující databáze Azure SQL na úroveň služby Hyperscale

Stávající databáze Azure SQL můžete přesunout do hyperškálování pomocí [portálu Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). V tuto chvíli se jedná o jednosměrnou migraci. Databáze z hyperškálování nelze přesunout do jiné úrovně služby než exportem a importem dat. Pro koncepty (POC) doporučujeme vytvořit kopii produkčních databází a migraci kopie do hyperškálování. Migrace existující databáze Azure SQL na úroveň Hyperscale je velikost datové operace.

Následující příkaz T-SQL přesune databázi do úrovně služby Hyperscale. V příkazu je nutné zadat `ALTER DATABASE` cíl edice i služby.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Připojení k replice databáze Hyperscale v měřítku pro čtení

V databázích Hyperscale `ApplicationIntent` argument v připojovacím řetězci poskytnutém klientem určuje, zda je připojení směrováno do repliky zápisu nebo do sekundární repliky jen pro čtení. Pokud `ApplicationIntent` sada `READONLY` a databáze nemá sekundární repliku, připojení bude směrováno na primární repliku a výchozí `ReadWrite` chování.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Sekundární repliky hyperškálování jsou identické a používají stejný cíl úrovně služby jako primární replika. Pokud je k dispozici více než jedna sekundární replika, úloha je distribuována ve všech dostupných sekundárních. Každá sekundární replika je aktualizována nezávisle, proto různé repliky mohou mít různé latence dat vzhledem k primární replice.

## <a name="database-high-availability-in-hyperscale"></a>Databáze vysoká dostupnost v hyperškálovat

Stejně jako ve všech ostatních úrovních služeb hyperškálování zaručuje odolnost dat pro potvrzené transakce bez ohledu na dostupnost výpočetní repliky. Rozsah prostojů z důvodu, že primární replika přestane být k dispozici, závisí na typu převzetí služeb při selhání (plánované vs. neplánované) a na přítomnosti alespoň jedné sekundární repliky. V plánované mno žaho (tj. události údržby) systém buď vytvoří novou primární repliku před zahájením převzetí služeb při selhání, nebo použije existující sekundární repliku jako cíl převzetí služeb při selhání. Při neplánovaném převzetí služeb při selhání (tj. selhání hardwaru v primární replice) systém používá sekundární repliku jako cíl převzetí služeb při selhání, pokud existuje, nebo vytvoří novou primární repliku z fondu dostupné výpočetní kapacity. V druhém případě je doba prostojů delší z důvodu dalších kroků potřebných k vytvoření nové primární repliky.

Soubor SLA hyperškálování najdete v článku [SLA pro Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Zotavení po havárii pro hyperškálovatdatabáze

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Obnovení hyperškálovací databáze do jiné zeměpisné polohy
Pokud potřebujete obnovit databázi Azure SQL Database Hyperscale DB do jiné oblasti, než je ta, ve které je aktuálně hostovaná, jako součást operace zotavení po havárii nebo procházení, přemístění nebo z jakéhokoli jiného důvodu, primární metodou je geografické obnovení databáze.  To zahrnuje přesně stejné kroky jako to, co byste použili k obnovení jakékoli jiné AZURE SQL DB do jiné oblasti:
1. Pokud tam ještě nemáte příslušný server, vytvořte v cílové oblasti databázový server Azure SQL.  Tento server by měl být vlastněn stejným předplatným jako původní (zdrojový) server.
2. Postupujte podle pokynů v tématu [geografického obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) stránky o obnovení databází Azure SQL z automatického zálohování.

> [!NOTE]
> Vzhledem k tomu, že zdroj a cíl jsou v samostatných oblastech, databáze nemůže sdílet úložiště snímků se zdrojovou databází jako v jiných geografických obnoveních, které se dokončí velmi rychle. V případě geografické obnovení databáze Hyperscale bude operace velikosti dat, i v případě, že cíl je ve spárované oblasti geograficky replikovanéúložiště.  To znamená, že provedení geografické obnovení bude nějakou dobu trvat úměrně velikosti obnovené databáze.  Pokud je cíl ve spárované oblasti, kopie bude v rámci oblasti, která bude výrazně rychlejší než kopie mezi oblastmi, ale stále bude velikost dat operace.

## <a name="available-regions"></a><a name=regions></a>Dostupné oblasti

Úroveň Hyperscale azure sql database je momentálně dostupná v následujících oblastech:

- Austrálie – východ
- Austrálie – jihovýchod
- Brazílie – jih
- Střední Kanada
- USA – střed
- Čína východ 2
- Čína Sever 2
- Východní Asie
- USA – východ
- Východní nás 2
- Francie – střed
- Japonsko – východ
- Japonsko – západ
- Jižní Korea – střed
- Jižní Korea – jih
- USA – středosever
- Severní Evropa
- Jižní Afrika – sever
- USA – středojih
- Jihovýchodní Asie
- Spojené království – jih
- Spojené království – západ
- Západní Evropa
- USA – západ
- USA – západ 2

Pokud chcete vytvořit databázi Hyperscale v oblasti, která není uvedena jako podporovaná, můžete odeslat žádost o připojení prostřednictvím portálu Azure. Pokyny najdete v [tématu Požadavek zvýšení kvóty pro Azure SQL Database](quota-increase-request.md) pro pokyny. Při odesílání žádosti použijte následující pokyny:

- Použijte typ kvóty SQL [požadavku na další kvótu.](quota-increase-request.md#other)
- V podrobnostech textu přidejte výpočetní skladovou položku/celkové jader včetně čitelných replik.
- Zadejte také odhadovanou tb.

## <a name="known-limitations"></a>Známá omezení

Jedná se o aktuální omezení úrovně služby Hyperscale od GA.  Aktivně pracujeme na odstranění co nejvíce z těchto omezení, jak je to možné.

| Problém | Popis |
| :---- | :--------- |
| Podokno Spravovat zálohy pro logický server nezobrazuje databáze hyperškálování, budou filtrovány ze zobrazení.  | Hyperscale má samostatnou metodu pro správu záloh a jako takové dlouhodobé uchovávání a bod v nastavení uchovávání záloh času neplatí / jsou zneplatněny. V souladu s tím hyperškálování databáze nezobrazí v podokně Spravovat zálohování. |
| Obnovení k určitému bodu v čase | Databázi Hyperscale můžete obnovit do databáze, která není hyperškálovat, v rámci období uchování databáze bez stupně hyperškálování. Databázi, která není hyperškálovat, nelze obnovit do databáze Hyperscale.|
| Pokud databáze obsahuje jeden nebo více datových souborů větších než 1 TB, migrace se nezdaří | V některých případech může být možné tento problém vyřešit zmenšením velké soubory být menší než 1 TB. Pokud migrace databáze používá během procesu migrace, ujistěte se, že žádný soubor dostane větší než 1 TB. Velikost databázových souborů můžete určit pomocí následujícího dotazu. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| MI | Spravovanou instanci Azure SQL Database není aktuálně podporovaná s databázemi Hyperscale. |
| Elastické fondy |  Elastické fondy nejsou aktuálně podporovány pomocí hyperškálování databáze SQL.|
| Migrace do hyperškálování je v současné době jednosměrná operace. | Jakmile je databáze migrována do hyperškálování, nelze ji migrovat přímo na úroveň služby jinénež Hyperscale. V současné době je jediným způsobem, jak migrovat databázi z Hyperscale do jiného než hyperškálování, export ovat a importovat pomocí souboru BACPAC nebo jiných technologií přesunu dat (hromadné kopírování, Azure Data Factory, Azure Databricks, SSIS atd.)|
| Migrace databází s trvalými objekty v paměti | Hyperscale podporuje pouze netrvalé objekty v paměti (typy tabulek, nativní sps a funkce).  Trvalé tabulky v paměti a další objekty musí být vynechány a znovu vytvořeny jako objekty bez paměti před migrací databáze na úroveň služby Hyperscale.|
| Geografická replikace  | Geografickou replikaci pro Hyperscale databáze Azure SQL zatím nelze nakonfigurovat. |
| Kopírování databáze | K vytvoření nové databáze v Azure SQL Hyperscale zatím nelze použít kopírování databáze. |
| Integrace TDE/AKV | Transparentní šifrování databáze pomocí trezoru klíčů Azure (běžně označované jako Bring-Your-Own-Key nebo BYOK) ještě není podporováno pro Azure SQL Database Hyperscale, ale TDE s service managed keys je plně podporována. |
|Inteligentní databázové funkce | S výjimkou možnosti "Vynutit plán" nejsou všechny ostatní možnosti automatického ladění v hyperškálování ještě podporovány: možnosti se mohou jevit jako povolené, ale nebudou provedena žádná doporučení ani akce. |
|Query Performance Insights | Přehledy výkonu dotazů nejsou aktuálně podporovány pro databáze Hyperscale. |
| Zmenšit databázi | DBCC SHRINKDATABASE nebo DBCC SHRINKFILE není aktuálně podporována pro hyperškálovací databáze. |
| Kontrola integrity databáze | DBCC CHECKDB není aktuálně podporována pro hyperškálovatdatabáze. Podrobnosti o správě integrity dat v Azure SQL Database najdete v článku [Integrita dat v Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) Database. |

## <a name="next-steps"></a>Další kroky

- Nejčastější dotazy k hyperškálování najdete [v tématu Nejčastější dotazy týkající se hyperškálování](sql-database-service-tier-hyperscale-faq.md).
- Informace o úrovních služeb najdete v [tématu Úrovně služeb](sql-database-service-tiers.md)
- Informace o limitech na úrovni serveru a předplatného najdete v [tématu Přehled omezení prostředků na logickém serveru.](sql-database-resource-limits-logical-server.md)
- Omezení nákupních modelů pro jednu databázi najdete v [tématu Limity nákupního modelu na základě virtuálních jader Azure SQL Database pro jednu databázi](sql-database-vcore-resource-limits-single-databases.md).
- Funkce a seznam porovnání naleznete v tématu [běžné funkce SQL](sql-database-features.md).
