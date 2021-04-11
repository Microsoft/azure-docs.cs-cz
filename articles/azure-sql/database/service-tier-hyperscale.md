---
title: Co je vrstva služby s škálovatelným škálováním?
description: Tento článek popisuje úroveň služby vCore v nákupním modelu založeném na základu v Azure SQL Database a vysvětluje, jak se liší od úrovní služeb Pro obecné účely a Pro důležité obchodní informace.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: 2e03b6fe189f11b6f8e855137438859360df686d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505395"
---
# <a name="hyperscale-service-tier"></a>Hyperškálování úrovně služby

Azure SQL Database vychází z architektury SQL Server databázového stroje, která je upravena pro cloudové prostředí s cílem zajistit 99,99% dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají v Azure SQL Database:

- Pro obecné účely/Standard
- Hyperškálování
- Pro důležité obchodní informace/Premium

Úroveň služby pro škálování na úrovni služeb v Azure SQL Database je nejnovější úrovní služeb v rámci nákupního modelu založeného na vCore. Tato úroveň služby je vysoce škálovatelná úroveň výkonu úložiště a výpočetní prostředí, která využívá architekturu Azure k horizontálnímu navýšení kapacity úložiště a výpočetních prostředků pro Azure SQL Database, a to nad rámec limitů, které jsou dostupné pro Pro obecné účely a Pro důležité obchodní informace úrovně služeb.

> [!NOTE]
>
> - Podrobnosti o Pro obecné účely a Pro důležité obchodní informace vrstvách služeb v nákupním modelu založeném na vCore najdete v tématu [pro obecné účely](service-tier-general-purpose.md) a [pro důležité obchodní informace](service-tier-business-critical.md) úrovně služeb. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](purchasing-models.md).
> - Úroveň služby webscale je v tuto chvíli dostupná jenom pro Azure SQL Database a ne pro Azure SQL Managed instance.

## <a name="what-are-the-hyperscale-capabilities"></a>Jaké jsou možnosti škálování

Úroveň služby pro škálování na úrovni služeb v Azure SQL Database poskytuje tyto další funkce:

- Podpora až 100 TB velikosti databáze
- Skoro okamžité zálohování databáze (na základě snímků souborů uložených v úložišti objektů BLOB v Azure) bez ohledu na velikost bez vstupně-výstupních operací na výpočetních prostředcích  
- Rychlá obnova databáze (na základě snímků souborů) v řádu minut a nikoli hodin nebo dnů (nejedná se o velikost datové operace)
- Vyšší celkový výkon z důvodu vyšší propustnosti protokolů a rychlejšího potvrzování transakcí bez ohledu na objemy dat
- Rychlé horizontální navýšení kapacity – můžete zřídit jeden nebo víc uzlů jen pro čtení pro přesměrování zatížení a použít jako aktivní pohotovostní režim.
- Rychlé horizontální navýšení kapacity – můžete v konstantním čase škálovat výpočetní prostředky tak, aby se v případě potřeby vešly na náročné úlohy, a pak v případě potřeby škálovat výpočetní prostředky zpátky.

Úroveň služby pro škálování na úrovni služeb odstraňuje spoustu praktických limitů tradičně zobrazených v cloudových databázích. V případě, že většina ostatních databází omezuje prostředky dostupné v jednom uzlu, nemají databáze v úrovni služby škálování žádná taková omezení. Díky flexibilní architektuře úložiště roste úložiště podle potřeby. Ve skutečnosti se databáze s škálovatelným škálováním nevytvářejí s definovanou maximální velikostí. Databáze s větší škálou roste podle potřeby a účtuje se jenom za kapacitu, kterou používáte. V případě úloh náročných na čtení nabízí úroveň služby s vysokým škálováním v případě potřeby další repliky pro čtení, které jsou potřeba k přerozdělení zátěží pro čtení.

Navíc se čas potřebný k vytvoření záloh databáze nebo horizontální navýšení nebo snížení kapacity už neváže na objem dat v databázi. Databáze s škálovatelným škálováním se dají zálohovat prakticky okamžitě. Můžete také škálovat databázi v desítkách terabajtů v řádu minut. Díky tomu budete mít obavy z toho, že budete mít v zabalené možnosti počáteční konfigurace.

Další informace o velikostech výpočtů pro úroveň služby technologie škálování na úrovni služeb najdete v tématu [Vlastnosti vrstvy služeb](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kdo by měl uvažovat o úrovni služby škálování na úrovni služeb

Úroveň služby technologie webscale je určena pro většinu obchodních úloh, protože poskytuje skvělou flexibilitu a vysoký výkon díky nezávisle škálovatelným výpočetním a úložným prostředkům. Díky možnosti automatického škálování úložiště až na 100 TB je vhodná volba pro zákazníky, kteří:

- Mít velké databáze v místním prostředí a chtějí modernizovat své aplikace tím, že se přesunou do cloudu
- Jsou již v cloudu a jsou omezeny omezením maximální velikosti databáze na jiné úrovně služby (1-4 TB).
- Mít menší databáze, ale vyžadují rychlé vertikální a horizontální výpočetní škálování, vysoký výkon, okamžité zálohování a rychlé obnovení databáze.

Úroveň služby s technologií OLTP podporuje širokou škálu SQL Server úloh, od čistě k čistě analýzám, ale je primárně optimalizovaná pro úlohy OLTP a hybridní transakce a zpracování HTAP (Analytical Processing).

> [!IMPORTANT]
> Elastické fondy nepodporují úroveň služby škálování na úrovni služeb.

## <a name="hyperscale-pricing-model"></a>Cenový model s škálovatelným škálováním

Úroveň služby Vcore je k dispozici pouze v [modelu](service-tiers-vcore.md). Pro zarovnávání s novou architekturou se cenový model mírně liší od Pro obecné účely nebo Pro důležité obchodní informace úrovně služeb:

- **Výpočetní** prostředí:

  Cena za výpočetní jednotku ve vašem měřítku je na jednu repliku. [Zvýhodněné hybridní využití Azureová](https://azure.microsoft.com/pricing/hybrid-benefit/) cena se použije pro automatické čtení replik škálování. Ve výchozím nastavení vytvoříme primární repliku a jednu repliku jen pro čtení na databázi s více instancemi.  Uživatelé můžou upravit celkový počet replik, včetně primární z 1-5.

- **Úložiště**:

  Při konfiguraci databáze v rámci škálování není nutné zadávat maximální velikost dat. V úrovni škálování se vám bude účtovat úložiště databáze na základě skutečného přidělení. Úložiště se automaticky přiděluje mezi 40 GB a 100 TB, a to v přírůstcích po 10 GB. V případě potřeby můžete v případě potřeby více datových souborů zvětšit. Databáze s škálovatelným škálováním je vytvořená s počáteční velikostí 10 GB a začne růst o 10 GB každých 10 minut, dokud nedosáhne velikosti 40 GB.

Další informace o cenách na úrovni služby najdete v tématu [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="distributed-functions-architecture"></a>Architektura distribuovaných funkcí

Na rozdíl od tradičních databázových strojů, které mají centralizované všechny funkce pro správu dat v jednom umístění nebo procesu (a to i tak, aby se v současnosti volaly distribuované databáze v produkčním prostředí, mají více kopií monolitické Data Engine), databáze s daty na více místech odděluje modul pro zpracování dotazů, kde se neliší sémantika různých datových modulů, od komponent, které poskytují dlouhodobé úložiště a odolnost Tímto způsobem můžete kapacitu úložiště hladce škálovat podle potřeby (počáteční cíl je 100 TB). Repliky jen pro čtení sdílejí stejné součásti úložiště, takže není potřeba žádná kopie dat, aby bylo možné spustit novou čitelnou repliku.

Následující diagram znázorňuje různé typy uzlů v databázi s škálovatelným škálováním:

![Architektura](./media/service-tier-hyperscale/hyperscale-architecture.png)

Databáze v rámci škálování obsahuje následující různé typy komponent:

### <a name="compute"></a>Compute

Výpočetní uzel je v místě, kde je relační modul životního prostředí. Toto je místo, kde dochází k jazyku, dotazu a zpracování transakcí. Všechny interakce uživatelů s databází škálování na úrovni služeb probíhají prostřednictvím těchto výpočetních uzlů. Výpočetní uzly mají mezipaměti založené na SSD (s RBPEXm rozšířením fondu vyrovnávací paměti v předchozím diagramu) k minimalizaci počtu síťových přenosů, které jsou nutné k načtení stránky dat. Je k dispozici jeden primární výpočetní uzel, ve kterém jsou zpracovávány všechny úlohy a zápisy čtení a transakcí. K dispozici je jeden nebo více sekundárních výpočetních uzlů, které fungují jako aktivní pohotovostní uzly pro účely převzetí služeb při selhání, a také fungovat jako výpočetní uzly jen pro čtení pro přesměrování zpracování úloh čtení (Pokud je tato funkce požadovaná).

Databázový stroj běžící na výpočetních uzlech se škálováním na více počítačích je stejný jako u jiných Azure SQL Database úrovní služeb. Když uživatelé komunikují s databázovým strojem na výpočetních uzlech s škálovatelným škálováním, je podporovaná plocha a chování stroje stejné jako u jiných úrovní služeb, s výjimkou [známých omezení](#known-limitations).

### <a name="page-server"></a>Server stránky

Stránkové servery jsou systémy, které představují modul úložiště s horizontálním škálováním na víc systémů.  Každý server stránky je zodpovědný za podmnožinu stránek v databázi.  Napředně každý Server Page ovládá buď až 128 GB, nebo až 1 TB dat. Na více než jednom serveru stránky nejsou sdílena žádná data (mimo repliky stránkového serveru, které jsou uchovávány pro zajištění redundance a dostupnosti). Úkolem serverového serveru je obsloužit stránky databáze pro výpočetní uzly na vyžádání a udržovat stránky aktualizované jako transakce aktualizace dat. Stránky serverů se udržují v aktuálním stavu díky přehrání záznamů protokolu z protokolovací služby. Stránky serverů také udržují pokrytí mezipamětí založených na SSD za účelem zvýšení výkonu. Dlouhodobé uchovávání datových stránek je v Azure Storage pro další spolehlivost.

### <a name="log-service"></a>Protokolová služba

Protokolová služba přijímá záznamy protokolu z primární repliky služby COMPUTE, uchovává je v odolné mezipaměti a předávají záznamy protokolu na zbytek výpočetních replik (takže může aktualizovat jejich mezipaměti) i na relevantní stránky, aby se data mohla aktualizovat. Tímto způsobem se všechny změny dat z primární repliky COMPUTE šíří prostřednictvím služby protokolování na všechny sekundární výpočetní repliky a servery stránky. Nakonec jsou záznamy protokolu odesílány do dlouhodobého úložiště v Azure Storage, což je prakticky nekonečné úložiště úložiště. Tento mechanismus odebere nutnost častého zkracování protokolů. Protokolová služba má také místní paměť a mezipaměti SSD pro urychlení přístupu k záznamům protokolu.

### <a name="azure-storage"></a>Azure Storage

Azure Storage obsahuje všechny datové soubory v databázi. Stránky serverů udržují datové soubory v Azure Storage aktuálním stavu. Toto úložiště se používá pro účely zálohování a také pro replikaci mezi oblastmi Azure. Zálohy jsou implementovány pomocí snímků úložiště datových souborů. Operace obnovení využívající snímky jsou rychlé bez ohledu na velikost dat. Data je možné obnovit do libovolného bodu v čase v rámci doby uchovávání záloh databáze.

## <a name="backup-and-restore"></a>Zálohování a obnovení

Zálohy jsou založené na snímku souborů, takže jsou skoro okamžité. Oddělení úložiště a výpočtů umožňuje přesunout operaci zálohování/obnovení do vrstvy úložiště, aby se snížilo zatížení primární repliky služby Compute. V důsledku toho zálohování databáze nemá vliv na výkon primárního výpočetního uzlu. Podobně se obnovení bodu v čase (PITR) provádí vrácením do snímků souborů a jako taková není velikost datové operace. Obnovení databáze v rámci škálování databáze ve stejné oblasti Azure je operace s konstantním časem a dokonce i více než jeden z nich může být obnoveno v řádu minut, nikoli hodin nebo dnů. Vytvoření nových databází obnovením existující zálohy také využívá tuto funkci: vytváření kopií databáze pro účely vývoje nebo testování, dokonce i z databází s více terabajty, je doable během několika minut.

Informace o geografickém obnovení databází s škálovatelným škálováním najdete v tématu [obnovení databáze v rámci škálování do jiné oblasti](#restoring-a-hyperscale-database-to-a-different-region).

## <a name="scale-and-performance-advantages"></a>Výhody škálování a výkonu

Díky možnosti rychlého zprovoznění dalších výpočetních uzlů jen pro čtení a architektury škálování je možné využít významné možnosti škálování pro čtení a můžou také uvolnit primární výpočetní uzel pro poskytování dalších požadavků na zápis. Také je možné rychle škálovat nebo snížit kapacitu výpočetních uzlů v důsledku architektury sdíleného úložiště architektury s architekturou škálování.

## <a name="create-a-hyperscale-database"></a>Vytvoření databáze s škálovatelným škálováním

Databázi škálování na více systému je možné vytvořit pomocí [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [PowerShellu](/powershell/module/azurerm.sql/new-azurermsqldatabase)nebo rozhraní příkazového [řádku](/cli/azure/sql/db#az-sql-db-create). Databáze s škálovatelným škálováním jsou dostupné jenom pomocí [nákupního modelu založeného na Vcore](service-tiers-vcore.md).

Následující příkaz T-SQL vytvoří databázi s měřítkem. V příkazu je nutné zadat jak edici, tak i cíl služby `CREATE DATABASE` . Seznam platných cílů služeb najdete v tématu [omezení prostředků](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) .

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Tím se vytvoří databáze s Gen5 na hardwaru se čtyřmi jádry.

## <a name="upgrade-existing-database-to-hyperscale"></a>Upgrade existující databáze na škálovatelný

Stávající databáze můžete v Azure SQL Database přesunout do škálování pomocí [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), [PowerShellu](/powershell/module/azurerm.sql/set-azurermsqldatabase)nebo rozhraní příkazového [řádku](/cli/azure/sql/db#az-sql-db-update). Tato možnost je v současnosti jednosměrnou migrací. Databáze nemůžete přesouvat z škálování na jinou úroveň služby, a to i z exportu a importu dat. V případě potřeby konceptu (POCs) doporučujeme vytvořit kopii produkčních databází a migrovat kopii do škálování na velká. Migrace stávající databáze v Azure SQL Database do vrstvy škálování je velikost datové operace.

Následující příkaz T-SQL přesune databázi do vrstvy služby s škálováním na úrovni služeb. V příkazu je nutné zadat jak edici, tak i cíl služby `ALTER DATABASE` .

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Připojení k replice v databázi s škálováním na úrovni čtení

V databázích s škálovatelnou velikostí `ApplicationIntent` argument v připojovacím řetězci, který poskytuje klient, určuje, zda je připojení směrováno do repliky zápisu nebo do sekundární repliky jen pro čtení. Pokud `ApplicationIntent` nastavení `READONLY` a databáze nemají sekundární repliku, připojení se přesměruje na primární repliku a výchozí `ReadWrite` chování.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Sekundární repliky s škálovatelným škálováním jsou všechny identické a používají stejný cíl na úrovni služby jako primární replika. Pokud je k dispozici více než jedna sekundární replika, bude zatížení distribuováno napříč všemi dostupnými sekundárními. Jednotlivé sekundární repliky se aktualizují nezávisle. Proto různé repliky můžou mít odlišnou latenci dat vzhledem k primární replice.

## <a name="database-high-availability-in-hyperscale"></a>Vysoká dostupnost databáze v měřítku

Stejně jako u všech ostatních úrovní služeb zajišťuje ochranná škála odolnost dat pro potvrzené transakce bez ohledu na dostupnost repliky Compute. Rozsah výpadku, který se stane nedostupnou primární replikou, závisí na typu převzetí služeb při selhání (plánované vs. neplánované) a na přítomnosti aspoň jedné sekundární repliky. V plánovaném převzetí služeb při selhání (tj. události údržby) systém vytvoří novou primární repliku před spuštěním převzetí služeb při selhání nebo použije existující sekundární repliku jako cíl převzetí služeb při selhání. V neplánovaném převzetí služeb při selhání (tj. selhání hardwaru primární repliky) systém používá sekundární repliku jako cíl převzetí služeb při selhání, pokud existuje, nebo vytvoří novou primární repliku z fondu dostupné výpočetní kapacity. V druhém případě je doba výpadku delší než v důsledku dalších kroků potřebných k vytvoření nové primární repliky.

Informace o smlouvě SLA pro škálování na úrovni najdete v tématu [SLA pro Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Zotavení po havárii pro databáze s škálovatelným škálováním

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Obnovení databáze v rámci škálování na jiné oblasti

Pokud potřebujete obnovit databázi v prostředí Azure SQL Database do jiné oblasti, než je ta, která je aktuálně hostovaná v rámci operace zotavení po havárii, přemístění, přemístění nebo jakéhokoli jiného důvodu, je primární metodou provést geografickou obnovu databáze. To zahrnuje přesně stejný postup jako v případě, že byste použili k obnovení jakékoli jiné databáze v SQL Database do jiné oblasti:

1. Pokud ještě nemáte příslušný server, vytvořte [Server](logical-servers.md) v cílové oblasti.  Tento server by měl vlastnit stejné předplatné jako původní (zdrojový) Server.
2. Postupujte podle pokynů uvedených v tématu [geografické obnovení](./recovery-using-backups.md#geo-restore) stránky stránky při obnovení databáze v Azure SQL Database z automatických záloh.

> [!NOTE]
> Vzhledem k tomu, že zdroj a cíl jsou v samostatných oblastech, nemůže databáze sdílet snímkové úložiště se zdrojovou databází jako v negeografických obnoveních, což dokončí rychle bez ohledu na velikost databáze. V případě geografického obnovení databáze s měřítkem dat se bude jednat o velikost operace, i když je cíl v spárované oblasti geograficky replikovaného úložiště. Proto bude geografické obnovení trvat v čase úměrné velikosti obnovené databáze. Pokud je cíl v spárované oblasti, přenos dat bude v rámci oblasti, která bude výrazně rychlejší než přenos dat mezi oblastmi, ale bude stále i operací velikosti dat.

## <a name="available-regions"></a><a name=regions></a>Dostupné oblasti

Azure SQL Database vrstva škálování je dostupná ve všech oblastech, ale ve výchozím nastavení povolená v následujících oblastech uvedených níže. Pokud chcete vytvořit databázi v rámci škálování na úrovni služby v oblasti, ve které není ve výchozím nastavení povolené škálování na úrovni služby, můžete odeslat požadavek na registraci prostřednictvím Azure Portal. Pokyny najdete v tématu [zvýšení kvóty žádostí o Azure SQL Database](quota-increase-request.md) . Při odesílání vaší žádosti postupujte podle následujících pokynů:

- Použijte typ kvóty SQL Database [přístupu k oblasti](quota-increase-request.md#region) .
- V popisu přidejte skladové položky COMPUTE/celkový počet jader včetně čitelných replik a určete, že požadujete kapacitu s měřítkem.
- Také zadejte projekci celkové velikosti všech databází v čase v TB.

Povolené oblasti:
- Austrálie – východ
- Austrálie – jihovýchod
- Austrálie – střed
- Brazílie – jih
- Střední Kanada
- Kanada – východ
- USA – střed
- Čína – východ 2
- Čína – sever 2
- Východní Asie
- East US
- Východní USA 2
- Francie – střed
- Německo – středozápad
- Japonsko – východ
- Japonsko – západ
- Jižní Korea – střed
- Jižní Korea – jih
- USA – středosever
- Severní Evropa
- Norsko – východ
- Norsko – západ
- Jižní Afrika – sever
- Středojižní USA
- Southeast Asia
- Švýcarsko – západ
- Spojené království – jih
- Spojené království – západ
- US DoD – střed
- US DoD – východ
- US Govt Arizona
- US Govt Texas
- USA – středozápad
- West Europe
- USA – západ
- Západní USA 2

## <a name="known-limitations"></a>Známá omezení

Jedná se o aktuální omezení úrovně služby škálování na úrovni služeb (GA).  Aktivně pracujeme na odebrání tolika těchto omezení, co je možné.

| Problém | Description |
| :---- | :--------- |
| Podokno Správa zálohování serveru nezobrazuje databáze s škálovatelnými škálováními. Budou filtrovány ze zobrazení.  | Vlastní škálování má samostatnou metodu pro správu záloh, takže nastavení uchovávání Long-Term a nastavení uchovávání záloh v určitém časovém okamžiku neplatí. Proto se databáze s škálovatelným škálováním nezobrazí v podokně Správa zálohování.<br><br>Pro databáze migrované do škálování z jiných Azure SQL Database úrovní služeb se zálohy před migrací uchovávají po dobu [uchovávání záloh](automated-backups-overview.md#backup-retention) zdrojové databáze. Tyto zálohy lze použít k [obnovení](recovery-using-backups.md#programmatic-recovery-using-automated-backups) zdrojové databáze k určitému bodu v čase před migrací.|
| Obnovení k určitému bodu v čase | Nemůžete obnovit databázi s neškálovatelným škálováním jako databázi s škálovatelnými škálováními a databázi s měřítkem ve formátu. V případě databáze bez škálování na úrovni služby, která byla migrována do škálování, změnou její úrovně služeb, obnovení do bodu v čase před migrací a v rámci doby uchovávání záloh databáze je [programově](recovery-using-backups.md#programmatic-recovery-using-automated-backups)podporována. Obnovená databáze nebude škálovatelná. |
| Při změně Azure SQL Database úrovně služby na škálovatelné, operace dojde k chybě, pokud má databáze nějaké datové soubory větší než 1 TB. | V některých případech je možné tento problém obejít tak, že velké soubory [zmenšíte](file-space-manage.md#shrinking-data-files) na méně než 1 TB předtím, než se pokusíte změnit úroveň služby na škálování. Pomocí následujícího dotazu určete aktuální velikost databázových souborů. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Spravovaná instance SQL | Spravovaná instance Azure SQL se v současné době nepodporuje u databází s podporou škálování na více instancí. |
| Elastické fondy |  Elastické fondy se v současné době nepodporují s měřítkem.|
| Migrace do škálování je momentálně jednosměrnou operací. | Jakmile se databáze migruje do škálování, nejde ji migrovat přímo na úroveň služby, která není na úrovni služby. V současné době jediný způsob, jak migrovat databázi z velkého měřítka do neškálovatelného škálování, je exportovat a importovat pomocí souboru BacPac nebo jiných technologií pro přesun dat (hromadné kopírování, Azure Data Factory, Azure Databricks, SSIS atd.). BacPac exportujte/Azure Portal importujte z prostředí PowerShell pomocí rutiny [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) nebo [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport)z Azure CLI pomocí příkazového řádku [AZ SQL DB export](/cli/azure/sql/db#az-sql-db-export) a [AZ SQL DB import](/cli/azure/sql/db#az-sql-db-import)a z [REST API](/rest/api/sql/) se nepodporuje. Import/export BacPac pro menší databáze s více škálováními (až 200 GB) se podporuje pomocí SSMS a [SqlPackage](/sql/tools/sqlpackage) verze 18,4 a novější. Pro větší databáze může BacPac export/import trvat delší dobu a může dojít k selhání z různých důvodů.|
| Migrace databází s In-Memory objekty OLTP | Měřítko podporuje podmnožinu objektů In-Memory OLTP, včetně paměťově optimalizovaných typů tabulek, proměnných tabulky a nativně kompilovaných modulů. Pokud se ale v databázi, kterou migrujete, vyskytuje libovolný druh In-Memory objektů OLTP, migrace z úrovně Premium a Pro důležité obchodní informace na škálování služby se nepodporuje. Chcete-li migrovat takovou databázi do škálování, je nutné vyřadit všechny In-Memory objekty OLTP a jejich závislosti. Po migraci databáze je možné tyto objekty znovu vytvořit. Odolné a netrvanlivé paměťově optimalizované tabulky se v současné době nepodporují a musí se měnit na diskové tabulky.|
| Geografická replikace  | Pro Azure SQL Database škálování na úrovni služby ještě nemůžete konfigurovat geografickou replikaci. |
| Kopie databáze | Kopie databáze na škále je teď ve verzi Public Preview. |
| Funkce inteligentní databáze | S výjimkou možnosti "vynutit plán" nejsou všechny ostatní možnosti automatického ladění zatím podporovány v rámci škálování: možnosti mohou být povoleny, ale nebudou zde učiněna žádná doporučení ani akce. |
| Query Performance Insights | Dotazy na výkon dotazů se v současné době nepodporují pro databáze s škálovatelnými škálováními. |
| Zmenšit databázi | Příkaz DBCC SHRINKDATABASE nebo DBCC SHRINKFILE není aktuálně podporován pro databáze s měřítkem. |
| Kontrola integrity databáze | Příkaz DBCC CHECKDB není v současné době podporován pro databáze s měřítkem. Příkaz DBCC CHECKFILEGROUP a DBCC CHECKTABLE lze použít jako alternativní řešení. Podrobnosti o správě integrity dat v Azure SQL Database najdete v tématu [Integrita dat v Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) . |

## <a name="next-steps"></a>Další kroky

- Nejčastější dotazy týkající se škálování najdete v tématu [Nejčastější dotazy týkající se škálování na úrovni](service-tier-hyperscale-frequently-asked-questions-faq.md).
- Informace o úrovních služeb najdete v tématu [úrovně služeb](purchasing-models.md) .
- Informace o omezeních na úrovni serveru a předplatného najdete v tématu [Přehled omezení prostředků na serveru](resource-limits-logical-server.md) .
- Omezení modelu nákupu pro izolovanou databázi najdete v tématu [Azure SQL Database omezení pro nákupní model založený na Vcore pro jednu databázi](resource-limits-vcore-single-databases.md).
- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](features-comparison.md).
