---
title: Přehled Azure SQL Databaseho škálování
description: Tento článek popisuje úroveň služby vCore v modelu nakupování na základě základů v nástroji Azure SQL Database a vysvětluje, jak se liší od úrovní služeb Pro obecné účely a Pro důležité obchodní informace.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 38402d6ccf5c5582fff878ad60bf1c9fd4a07118
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687323"
---
# <a name="hyperscale-service-tier"></a>Hyperškálování úrovně služby

Azure SQL Database vychází z architektury SQL Server databázového stroje, která je upravena pro cloudové prostředí s cílem zajistit 99,99% dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají v Azure SQL Database:
- Pro obecné účely/Standard 
-  Hyperškálování
-  Pro důležité obchodní informace/Premium

Úroveň služby pro škálování na úrovni služeb v Azure SQL Database je nejnovější úrovní služeb v rámci nákupního modelu založeného na vCore. Tato úroveň služby je vysoce škálovatelná úroveň výkonu úložiště a výpočetní prostředí, která využívá architekturu Azure k horizontálnímu navýšení kapacity úložiště a výpočetních prostředků pro Azure SQL Database, a to nad rámec limitů, které jsou k dispozici pro Pro obecné účely a firmy. Kritické úrovně služeb.

> 
> [!NOTE]
> Podrobnosti o Pro obecné účely a Pro důležité obchodní informace vrstvách služeb v nákupním modelu založeném na vCore najdete v tématu [pro obecné účely](sql-database-service-tier-general-purpose.md) a [pro důležité obchodní informace](sql-database-service-tier-business-critical.md) úrovně služeb. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Jaké jsou možnosti škálování

Úroveň služby pro škálování na úrovni služeb v Azure SQL Database poskytuje tyto další funkce:

- Podpora až 100 TB velikosti databáze
- Skoro okamžité zálohování databáze (na základě snímků souborů uložených v úložišti objektů BLOB v Azure) bez ohledu na velikost bez vstupně-výstupních operací na výpočetních prostředcích  
- Rychlá obnova databáze k určitému bodu v čase (na základě snímků souborů) v minutách, ne hodin nebo dnů (nejedná se o velikost datové operace)
- Vyšší celkový výkon z důvodu vyšší propustnosti protokolů a rychlejšího potvrzování transakcí bez ohledu na objemy dat
- Rychlé horizontální navýšení kapacity – můžete zřídit jeden nebo víc uzlů jen pro čtení pro přesměrování zatížení a použít jako aktivní pohotovostní režim.
- Rychlé horizontální navýšení kapacity – můžete v konstantním čase škálovat výpočetní prostředky tak, aby vyhovovaly velkým objemům úloh, a to v případě potřeby a následně škálovat výpočetní prostředky zpátky, pokud to nepotřebujete.

Úroveň služby pro škálování na úrovni služeb odstraňuje spoustu praktických limitů tradičně zobrazených v cloudových databázích. V případě, že většina ostatních databází omezuje prostředky dostupné v jednom uzlu, nemají databáze v úrovni služby škálování žádná taková omezení. Díky flexibilní architektuře úložiště roste úložiště podle potřeby. Ve skutečnosti se databáze s škálovatelným škálováním nevytvářejí s definovanou maximální velikostí. Databáze s větší škálou roste podle potřeby a účtuje se jenom za kapacitu, kterou používáte. V případě úloh náročných na čtení nabízí úroveň služby s vysokým škálováním v případě potřeby další repliky pro čtení, které jsou potřeba k přerozdělení zátěží pro čtení.

Navíc se čas potřebný k vytvoření záloh databáze nebo horizontální navýšení nebo snížení kapacity už neváže na objem dat v databázi. Databáze s škálovatelným škálováním se dají zálohovat prakticky okamžitě. Můžete také škálovat databázi v desítkách terabajtů v řádu minut. Díky tomu budete mít obavy z toho, že budete mít v zabalené možnosti počáteční konfigurace.

Další informace o velikostech výpočtů pro úroveň služby technologie škálování na úrovni služeb najdete v tématu [Vlastnosti vrstvy služeb](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kdo by měl uvažovat o úrovni služby škálování na úrovni služeb

Úroveň služby technologie webscale je určena pro většinu obchodních úloh, protože poskytuje skvělou flexibilitu a vysoký výkon díky nezávisle škálovatelným výpočetním a úložným prostředkům. Díky možnosti automatického škálování úložiště o velikosti až 100 TB je vhodným způsobem pro zákazníky, kteří:

- Mít velké databáze v místním prostředí a chtějí modernizovat své aplikace tím, že se přesunou do cloudu
- Jsou již v cloudu a jsou omezeny omezením maximální velikosti databáze na jiné úrovně služby (1-4 TB).
- Mít menší databáze, ale vyžadují rychlé vertikální a horizontální výpočetní škálování, vysoký výkon, okamžité zálohování a rychlé obnovení databáze.

Úroveň služby s technologií OLTP podporuje širokou škálu SQL Server úloh, od čistě k čistě analýzám, ale je primárně optimalizovaná pro úlohy OLTP a hybridní transakce a zpracování HTAP (Analytical Processing).

> [!IMPORTANT]
> Elastické fondy nepodporují úroveň služby škálování na úrovni služeb.

## <a name="hyperscale-pricing-model"></a>Cenový model s škálovatelným škálováním

Úroveň služby Vcore je k dispozici pouze v [modelu](sql-database-service-tiers-vcore.md). Pro zarovnávání s novou architekturou se cenový model mírně liší od Pro obecné účely nebo Pro důležité obchodní informace úrovně služeb:

- **Výpočetní**prostředí:

  Cena za výpočetní jednotku ve vašem měřítku je na jednu repliku. [Zvýhodněné hybridní využití Azureová](https://azure.microsoft.com/pricing/hybrid-benefit/) cena se použije pro automatické čtení replik škálování. Ve výchozím nastavení vytvoříme primární repliku a jednu repliku jen pro čtení na databázi s více instancemi.  Uživatelé můžou upravit celkový počet replik, včetně primární z 1-5.

- **Úložiště**:

  Při konfiguraci databáze v rámci škálování není nutné zadávat maximální velikost dat. Ve vrstvě s hyperškálováním se účtuje úložiště pro databázi podle skutečného využití. Úložiště se automaticky přiděluje mezi 10 GB a 100 TB, v přírůstcích, které se dynamicky upravují mezi 10 GB a 40 GB.  

Další informace o cenách na úrovni služby najdete v tématu [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="distributed-functions-architecture"></a>Architektura distribuovaných funkcí

Na rozdíl od tradičních databázových strojů, které mají centralizované všechny funkce pro správu dat v jednom umístění nebo procesu (a to i tak, aby se v současnosti volaly distribuované databáze v produkčním prostředí, mají více kopií monolitické Data Engine), jsou oddělené databáze na úrovni. modul pro zpracování dotazů, ve kterém se liší sémantika různých datových motorů, od komponent, které poskytují dlouhodobé úložiště a trvanlivost dat. Tímto způsobem můžete kapacitu úložiště hladce škálovat podle potřeby (počáteční cíl je 100 TB). Repliky jen pro čtení sdílejí stejné součásti úložiště, takže není potřeba žádná kopie dat, aby bylo možné spustit novou čitelnou repliku. 

Následující diagram znázorňuje různé typy uzlů v databázi s škálovatelným škálováním:

![Architektura](./media/sql-database-hyperscale/hyperscale-architecture2.png)

Databáze v rámci škálování obsahuje následující různé typy komponent:

### <a name="compute"></a>Compute

Výpočetní uzel je místo, kde se nachází relační modul, takže se objeví všechny jazykové prvky, zpracování dotazů a tak dále. Všechny interakce uživatelů s databází škálování na úrovni služeb probíhají prostřednictvím těchto výpočetních uzlů. Výpočetní uzly mají mezipaměti založené na SSD (s RBPEXm rozšířením fondu vyrovnávací paměti v předchozím diagramu) k minimalizaci počtu síťových přenosů, které jsou nutné k načtení stránky dat. Je k dispozici jeden primární výpočetní uzel, ve kterém jsou zpracovávány všechny úlohy a zápisy čtení a transakcí. K dispozici je jeden nebo více sekundárních výpočetních uzlů, které fungují jako aktivní pohotovostní uzly pro účely převzetí služeb při selhání, a také fungovat jako výpočetní uzly jen pro čtení pro přesměrování zpracování úloh čtení (Pokud je tato funkce požadovaná).

### <a name="page-server"></a>Server stránky

Stránkové servery jsou systémy, které představují modul úložiště s horizontálním škálováním na víc systémů.  Každý server stránky je zodpovědný za podmnožinu stránek v databázi.  U každého stránkového serveru ovládací prvky mezi 128 GB a 1 TB dat. Na více než jednom serveru stránky nejsou sdílena žádná data (mimo repliky, které jsou uchovávány pro redundanci a dostupnost). Úkolem serverového serveru je obsloužit stránky databáze pro výpočetní uzly na vyžádání a udržovat stránky aktualizované jako transakce aktualizace dat. Stránky serverů jsou stále aktuální díky přehrání záznamů protokolu z protokolovací služby. Stránky serverů také udržují mezipaměti založené na SSD za účelem zvýšení výkonu. Dlouhodobé uchovávání datových stránek je v Azure Storage pro další spolehlivost.

### <a name="log-service"></a>Protokolová služba

Protokolová služba přijímá záznamy protokolu z primární repliky služby COMPUTE, uchovává je v odolné mezipaměti a předávají záznamy protokolu na zbytek výpočetních replik (takže může aktualizovat jejich mezipaměti) i na relevantní stránky, aby se data mohla aktualizovat. Některé. Tímto způsobem se všechny změny dat z primární repliky COMPUTE šíří prostřednictvím služby protokolování na všechny sekundární výpočetní repliky a servery stránky. Nakonec jsou záznamy protokolu odesílány do dlouhodobého úložiště v Azure Storage, což je prakticky nekonečné úložiště úložiště. Tento mechanismus odebere nutnost častého zkracování protokolů. Protokolová služba má také místní mezipaměť pro urychlení přístupu k záznamům protokolu.

### <a name="azure-storage"></a>Úložiště Azure

Azure Storage obsahuje všechny datové soubory v databázi. Stránky serverů udržují datové soubory v Azure Storage aktuálním stavu. Toto úložiště se používá pro účely zálohování a také pro replikaci mezi oblastmi Azure. Zálohy jsou implementovány pomocí snímků úložiště datových souborů. Operace obnovení využívající snímky jsou rychlé bez ohledu na velikost dat. Data je možné obnovit do libovolného bodu v čase v rámci doby uchovávání záloh databáze.

## <a name="backup-and-restore"></a>Zálohování a obnovení

Zálohy jsou založené na snímku souborů, takže jsou skoro okamžité. Oddělení úložiště a výpočtů umožňuje přesunout operaci zálohování/obnovení do vrstvy úložiště, aby se snížilo zatížení primární repliky služby Compute. V důsledku toho zálohování databáze nemá vliv na výkon primárního výpočetního uzlu; Podobně se obnovení provádí vrácením do snímků souborů a jako taková není velikost datové operace. Obnovení je operace s konstantním časem a dokonce i více než terabajt databází může být obnoveno v řádu minut, nikoli hodin nebo dnů. Vytvoření nových databází obnovením existující zálohy také využívá tuto funkci: vytváření kopií databáze na stejném logickém serveru pro účely vývoje nebo testování, a to i z databází s velikostí terabajtů, je doable během několika minut.

## <a name="scale-and-performance-advantages"></a>Výhody škálování a výkonu

Díky možnosti rychlého zprovoznění dalších výpočetních uzlů jen pro čtení a architektury škálování je možné využít významné možnosti škálování pro čtení a můžou také uvolnit primární výpočetní uzel pro poskytování dalších požadavků na zápis. Také je možné rychle škálovat nebo snížit kapacitu výpočetních uzlů v důsledku architektury sdíleného úložiště architektury s architekturou škálování.

## <a name="create-a-hyperscale-database"></a>Vytvoření databáze s škálovatelným škálováním

Databázi škálování na více systému je možné vytvořit pomocí [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) nebo rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Databáze s škálovatelným škálováním jsou dostupné jenom pomocí [nákupního modelu založeného na Vcore](sql-database-service-tiers-vcore.md).

Následující příkaz T-SQL vytvoří databázi s měřítkem. V příkazu `CREATE DATABASE` musíte zadat jak edici, tak i cíl služby. Seznam platných cílů služeb najdete v tématu [omezení prostředků](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier-for-provisioned-compute) .

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Tím se vytvoří databáze Gen5 s využitím hardwaru s 4 jádry.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrace existujícího Azure SQL Database na úroveň služby technologie škálování na úrovni služeb

Stávající databáze SQL Azure můžete přesunout do škálování pomocí [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Tato možnost je v současnosti jednosměrnou migrací. Databáze nemůžete přesouvat z škálování na jinou úroveň služby, a to i z exportu a importu dat. V případě potřeby konceptu (POCs) doporučujeme vytvořit kopii produkčních databází a migrovat kopii do škálování na velká. Migrace stávající databáze SQL Azure do vrstvy škálování je velikost datové operace.

Následující příkaz T-SQL přesune databázi do vrstvy služby s škálováním na úrovni služeb. V příkazu `ALTER DATABASE` musíte zadat jak edici, tak i cíl služby.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Připojení k replice v databázi s škálováním na úrovni čtení

V databázích s škálovatelným škálováním argument `ApplicationIntent` v připojovacím řetězci, který poskytuje klient, určuje, jestli je připojení směrované do repliky zápisu nebo do sekundární repliky jen pro čtení. Pokud je `ApplicationIntent` nastavená na `READONLY` a databáze nemá sekundární repliku, připojení se přesměruje na primární repliku a výchozí chování `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Sekundární repliky s škálovatelným škálováním jsou všechny identické a používají stejný cíl na úrovni služby jako primární replika. Pokud je k dispozici více než jedna sekundární replika, bude zatížení distribuováno napříč všemi dostupnými sekundárními. Jednotlivé sekundární repliky se aktualizují nezávisle, takže různé repliky by mohly mít v souvislosti s primární replikou odlišnou latenci dat.

## <a name="database-high-availability-in-hyperscale"></a>Vysoká dostupnost databáze v měřítku

Stejně jako u všech ostatních úrovní služeb zajišťuje ochranná škála odolnost dat pro potvrzené transakce bez ohledu na dostupnost repliky Compute. Rozsah výpadku, který se stane nedostupnou primární replikou, závisí na typu převzetí služeb při selhání (plánované vs. neplánované) a na přítomnosti aspoň jedné sekundární repliky. V plánovaném převzetí služeb při selhání (tj. události údržby) systém vytvoří novou primární repliku před spuštěním převzetí služeb při selhání nebo použije existující sekundární repliku jako cíl převzetí služeb při selhání. V neplánovaném převzetí služeb při selhání (tj. selhání hardwaru primární repliky) systém používá sekundární repliku jako cíl převzetí služeb při selhání, pokud existuje, nebo vytvoří novou primární repliku z fondu dostupné výpočetní kapacity. V druhém případě je doba výpadku delší než v důsledku dalších kroků potřebných k vytvoření nové primární repliky.

Informace o smlouvě SLA pro škálování na úrovni najdete v tématu [SLA pro Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Zotavení po havárii pro databáze s škálovatelným škálováním

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Obnovení databáze v rámci škálování na jiné geografické úrovni
Pokud potřebujete obnovit Azure SQL Database DB škálování do jiné oblasti, než na kterou je aktuálně hostovaná, jako součást operace zotavení po havárii nebo v podrobnostech, přemístění nebo z jiného důvodu, je primární metodou provést geografickou obnovu databáze.  To zahrnuje přesně stejný postup jako u toho, co byste použili k obnovení jakékoli jiné databáze SQL AZURE do jiné oblasti:
1. Pokud ještě nemáte příslušný server, vytvořte SQL Database Server v cílové oblasti.  Tento server by měl vlastnit stejné předplatné jako původní (zdrojový) Server.
2. Postupujte podle pokynů v tématu [geografické obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) stránky na stránce týkající se obnovení databází Azure SQL z automatických záloh.

> [!NOTE]
> Vzhledem k tomu, že zdroj a cíl jsou v samostatných oblastech, nemůže databáze sdílet snímkové úložiště se zdrojovou databází jako v negeografických obnoveních, což je kompletní velmi rychle.  V případě geografického obnovení databáze s měřítkem dat se bude jednat o velikost operace, i když je cíl v spárované oblasti geograficky replikovaného úložiště.  To znamená, že při geografickém obnovení bude čas odpovídat velikosti databáze, která se obnovuje.  Pokud je cíl v spárované oblasti, kopie bude v datovém centru, které bude výrazně rychlejší než dlouhé místo na internetu, ale bude stále kopírovat všechny bity.

## <a name=regions></a>Dostupné oblasti

Azure SQL Database úroveň škálování je aktuálně dostupná v následujících oblastech:

- Austrálie – východ
- Austrálie – jihovýchod
- Brazílie – jih
- Střední Kanada
- Střední USA
- Čína – východ 2
- Čína – sever 2
- Východní Asie
- Východ USA
- Východní USA 2
- Francie – střed
- Japonsko – východ
- Japonsko – západ
- Jižní Korea – střed
- Jižní Korea – jih
- Středoseverní USA
- Severní Evropa
- Jižní Afrika – sever
- Středojižní USA
- Jihovýchodní Asie
- Spojené království – jih
- Spojené království – západ
- Západní Evropa
- Západní USA
- Západní USA 2

Pokud chcete vytvořit databázi s škálovatelným škálováním v oblasti, která není uvedená jako podporovaná, můžete odeslat požadavek na registraci prostřednictvím Azure Portal. Pracujeme na rozbalení seznamu podporovaných oblastí, takže se prosím vraťte k seznamu nejnovější oblasti.

Chcete-li požádat o možnost vytvořit databáze na úrovni škálování v oblastech, které nejsou uvedeny:

1. Přejít na [okno Azure Help and Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Klikněte na [ **novou žádost o podporu** .](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Okno pomoc a podpora Azure](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. Jako **typ problému**vyberte **omezení služby a předplatné (kvóty)** .

4. Vyberte předplatné, které chcete použít k vytvoření databáze.

5. Jako **typ kvóty**vyberte **SQL Database** .

6. Klikněte na **Další: řešení**

1. Klikněte na **zadat podrobnosti** .

    ![Podrobnosti o problému](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. Vyberte **typ kvóty SQL Database**: **Další žádost o kvótu** .

9. Vyplňte následující šablonu:

    ![Podrobnosti kvóty](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    V šabloně zadejte tyto informace:

    > Požadavek na vytvoření SQL Database v rámci Azure s měřítkem v nové oblasti<br/> Oblast: [vyplňte požadovanou oblast.]  <br/>
    > SKU COMPUTE/celkový počet jader včetně čitelných replik <br/>
    > Odhadované množství TB 
    >

10. Zvolte **Severity C** (Závažnost C).

11. Vyberte odpovídající způsob kontaktu a vyplňte podrobnosti.

12. Klikněte na **Uložit** a **pokračovat** .

## <a name="known-limitations"></a>Známá omezení
Jedná se o aktuální omezení úrovně služby škálování na úrovni služeb (GA).  Aktivně pracujeme na odebrání tolika těchto omezení, co je možné.

| Problém | Popis |
| :---- | :--------- |
| Podokno Správa zálohování na logickém serveru nezobrazuje databáze s škálovatelným škálováním, které se budou filtrovat z SQL serveru.  | Vlastní škálování má samostatnou metodu pro správu záloh a jako takové dlouhodobé uchovávání a nastavení uchovávání záloh v čase se nevztahují nebo neověřují. Proto se databáze s škálovatelným škálováním nezobrazí v podokně Správa zálohování. |
| Obnovení k určitému bodu v čase | Jakmile se databáze migruje do vrstvy služby s vlastním škálováním, obnovení k určitému bodu v čase před migrací se nepodporuje.|
| Obnovení databáze bez škálování na Hypserscale a naopak | Nemůžete obnovit databázi škálování v databázi s neškálovatelnými škálováními, ani nemůžete obnovit databázi s neškálovatelným škálováním do databáze v rámci škálování na více databází.|
| Pokud má databáze minimálně jeden datový soubor větší než 1 TB, migrace se nezdařila | V některých případech je možné tento problém obejít tak, že velké soubory zmenšíte na méně než 1 TB. Pokud migrujete databázi používanou během procesu migrace, ujistěte se, že žádný soubor nezíská větší velikost než 1 TB. Pomocí následujícího dotazu určete velikost databázových souborů. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| MI | Služba Azure SQL Database Managed instance se v současné době nepodporuje u databází s podporou škálování na více instancí. |
| Elastické fondy |  Elastické fondy se v současnosti nepodporují u SQL Databaseho škálování.|
| Migrace do škálování je momentálně jednosměrnou operací. | Jakmile se databáze migruje do škálování, nedá se migrovat přímo na úroveň služby, která není na úrovni služby. V současné době jediný způsob, jak migrovat databázi z velkého měřítka do neškálovatelného škálování, je exportovat a importovat pomocí souboru BACPAC nebo jiných technologií pro přesun dat (hromadné kopírování, Azure Data Factory, Azure Databricks, SSIS atd.).|
| Migrace databází pomocí trvalých objektů v paměti | Pro škálování podporuje pouze netrvalé objekty v paměti (typy tabulek, nativní aktualizace SPs a funkce).  Trvalé tabulky v paměti a další objekty je nutné vyřadit a znovu vytvořit jako objekty, které nejsou v paměti, před migrací databáze na úroveň služby pro škálování na úrovni služby.|
| Sledování změn | Nemůžete ještě konfigurovat a používat Change Tracking s databázemi Azure SQL s škálovatelným škálováním. |
| Geografická replikace  | U Azure SQL Databaseho škálování se ještě nedá konfigurovat geografickou replikaci. |
| Kopie databáze | Ještě nemůžete použít kopii databáze k vytvoření nové databáze ve službě Azure SQL s škálovatelným škálováním. |
| Integrace TDE/integrace | Transparentní šifrování databáze pomocí Azure Key Vault (obecně označované jako BYOK) ještě není podporované pro Azure SQL Database škálování, ale TDE se spravovanými klíči služby se plně podporuje. |
|Funkce inteligentní databáze | S výjimkou možnosti "vynutit plán" nejsou všechny ostatní možnosti automatického ladění zatím podporovány v rámci škálování: možnosti mohou být povoleny, ale nebudou zde učiněna žádná doporučení ani akce. |

## <a name="next-steps"></a>Další kroky

- Nejčastější dotazy týkající se škálování najdete v tématu [Nejčastější dotazy týkající se škálování na úrovni](sql-database-service-tier-hyperscale-faq.md).
- Informace o úrovních služeb najdete v tématu [úrovně služeb](sql-database-service-tiers.md) .
- Informace o omezeních na úrovni serveru a předplatného najdete v tématu [Přehled omezení prostředků na logickém serveru](sql-database-resource-limits-logical-server.md) .
- Omezení modelu nákupu pro izolovanou databázi najdete v tématu [Azure SQL Database omezení pro nákupní model založený na Vcore pro jednu databázi](sql-database-vcore-resource-limits-single-databases.md).
- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](sql-database-features.md).
