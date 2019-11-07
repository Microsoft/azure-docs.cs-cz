---
title: Synchronizace dat SQL Azure
description: Tento přehled zavádí Azure Synchronizace dat SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 925977edf267510399dc631f0d0efe5fc1941803
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687048"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronizace dat napříč několika cloudy a místními databázemi pomocí Synchronizace dat SQL

Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč několika databázemi SQL a instancemi služby SQL Server.

> [!IMPORTANT]
> Azure Synchronizace dat SQL v tuto **chvíli nepodporuje spravovanou** instanci Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Kdy použít synchronizaci dat

Synchronizace dat je užitečná v případech, kdy je třeba data udržovat v aktuálním stavu napříč několika databázemi SQL Azure nebo databázemi SQL Server. Tady jsou hlavní případy použití pro synchronizaci dat:

- **Synchronizace hybridních dat:** Díky synchronizaci dat můžete udržovat data synchronizovaná mezi místními databázemi a databázemi SQL Azure a povolit tak hybridní aplikace. Tato schopnost může vydávat potíže zákazníkům, kteří zvažuje přesun do cloudu a chtějí do Azure umístit některé z jeho aplikací.
- **Distribuované aplikace:** V mnoha případech je výhodné oddělit různé úlohy napříč různými databázemi. Například pokud máte rozsáhlou provozní databázi, ale budete muset pro tato data spustit také úlohu vytváření sestav nebo analýzy, je vhodné mít pro toto další zatížení druhou databázi. Tento přístup minimalizuje dopad na výkon na produkční úlohy. Synchronizaci dat můžete použít, chcete-li tyto dvě databáze uchovat synchronizované.
- **Globálně distribuované aplikace:** Řada firem zahrnuje několik oblastí a dokonce i několik zemí nebo oblastí. Abyste minimalizovali latenci sítě, je nejlepší mít data v oblasti, která je blízko vás. Díky synchronizaci dat můžete snadno uchovávat databáze v oblastech po celém světě.

Synchronizace dat není preferovaným řešením pro následující scénáře:

| Scénář | Některá doporučená řešení |
|----------|----------------------------|
| Zotavení po havárii | [Geograficky redundantní zálohy Azure](sql-database-automated-backups.md) |
| Čtení stupnice | [Použití replik jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení (Preview)](sql-database-read-scale-out.md) |
| ETL (OLTP až OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) nebo [služba SSIS (SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrace z místního SQL Server do Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Přehled Synchronizace dat SQL

Synchronizace dat je založena na konceptu skupiny synchronizace. Skupina synchronizace je skupina databází, které chcete synchronizovat.

Synchronizace dat používá k synchronizaci dat topologii hvězdicové a Paprskové topologie. Jednu z databází ve skupině synchronizace definujete jako databázi centra. Ostatní databáze jsou členské databáze. Synchronizace probíhá pouze mezi rozbočovačem a jednotlivými členy.

- **Databáze centra** musí být Azure SQL Database.
- **Členské databáze** můžou být databáze SQL, místní SQL Server databáze nebo instance SQL Server na virtuálních počítačích Azure.
- **Synchronizovaná databáze** obsahuje metadata a protokol pro synchronizaci dat. Synchronizovaná databáze musí být Azure SQL Database nacházející se ve stejné oblasti jako databáze centra. Synchronizovaná databáze je vytvořená zákazníkem a vlastněné zákazníkem.

> [!NOTE]
> Pokud používáte místní databázi jako členskou databázi, je nutné [nainstalovat a nakonfigurovat místního agenta synchronizace](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizace dat mezi databázemi](media/sql-database-sync-data/sync-data-overview.png)

Skupina synchronizace má následující vlastnosti:

- **Schéma synchronizace** popisuje, která data se synchronizují.
- **Směr synchronizace** může být obousměrný nebo může tok pouze v jednom směru. To znamená, že směr synchronizace může být z *rozbočovače na člena*nebo z *člena do středu nebo do*obou.
- **Interval synchronizace** popisuje, jak často dochází k synchronizaci.
- **Zásada řešení konfliktů** je zásada na úrovni skupiny, kterou je možné vytvořit jako *rozbočovač WINS* nebo *Členové služby WINS*.

## <a name="how-does-data-sync-work"></a>Jak synchronizace dat funguje

- **Sledování změn dat:** Synchronizace dat sleduje změny pomocí aktivačních událostí vložení, aktualizace a odstranění. Změny jsou zaznamenány v tabulce na straně uživatele v uživatelské databázi. Všimněte si, že BULK INSERT ve výchozím nastavení neaktivují triggery. Pokud není zadaný FIRE_TRIGGERS, nespustí se žádné triggery vložení. Přidejte možnost FIRE_TRIGGERS, aby synchronizace dat mohla sledovat tato vložení. 
- **Synchronizují se data:** Synchronizace dat je navržena v modelu hvězdicového a paprskového modelu. Centrum se synchronizuje s každým členem zvlášť. Změny z centra se stáhnou do člena a změny od tohoto člena se nahrají do centra.
- **Řeší se konflikty:** Synchronizace dat poskytuje dvě možnosti pro řešení konfliktů, *centrum WINS* nebo *člena služby WINS*.
  - Pokud vyberete možnost *centrum služby WINS*, změny v centru budou vždy přepisovat změny v členu.
  - Pokud vyberete možnost *Členové serveru WINS*, změny ve členovi přepíšou změny v centru. Pokud existuje více než jeden člen, bude konečná hodnota záviset na tom, který člen se nejprve synchronizuje.

## <a name="compare-data-sync-with-transactional-replication"></a>Porovnání synchronizace dat s transakční replikací

| | Synchronizace dat | Transakční replikace |
|---|---|---|
| Výhody | – Aktivní – aktivní podpora<br/>– Obousměrné mezi místními a Azure SQL Database | – Nižší latence<br/>– Transakční konzistence<br/>-Opětovné použití existující topologie po migraci |
| Nevýhody | – 5 min nebo větší latence<br/>-Žádná transakční konzistence<br/>– Vyšší dopad na výkon | -Nelze publikovat z Azure SQL Database izolované databáze nebo databáze ve fondu<br/>– Náklady vysoké údržby |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Začínáme se synchronizací dat SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Nastavení synchronizace dat v Azure Portal

- [Nastavení Synchronizace dat SQL Azure](sql-database-get-started-sql-data-sync.md)
- Agent synchronizace dat – [Agent synchronizace dat pro Azure synchronizace dat SQL](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Nastavení synchronizace dat pomocí PowerShellu

- [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
- [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Projděte si osvědčené postupy pro synchronizaci dat

- [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Došlo k chybě.

- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzistence a výkon

#### <a name="eventual-consistency"></a>Konečná konzistence

Vzhledem k tomu, že synchronizace dat je založená na triggeru, není zaručena transakční konzistence. Společnost Microsoft zaručuje, že všechny změny budou provedeny nakonec a že synchronizace dat nezpůsobí ztrátu dat.

#### <a name="performance-impact"></a>Dopad na výkon

Synchronizace dat sleduje změny pomocí aktivačních událostí vložení, aktualizace a odstranění. Vytvoří v uživatelské databázi vedlejší tabulky pro sledování změn. Tyto aktivity sledování změn mají dopad na zatížení vaší databáze. V případě potřeby vyhodnoťte úroveň služby a upgradujte.

Zřizování a rušení zřizování během vytváření skupiny synchronizace, aktualizace a odstraňování může mít vliv i na výkon databáze. 

## <a name="sync-req-lim"></a>Požadavky a omezení

### <a name="general-requirements"></a>Obecné požadavky

- Každá tabulka musí mít primární klíč. Neměňte hodnotu primárního klíče na žádném řádku. Pokud je nutné změnit hodnotu primárního klíče, odstraňte řádek a vytvořte jej znovu s novou hodnotou primárního klíče. 

> [!IMPORTANT]
> Změna hodnoty existujícího primárního klíče bude mít za následek následující chybné chování:   
>   - Data mezi centrem a členy mohou být ztracena, i když synchronizace neoznamuje žádné potíže.
> - Synchronizace může selhat, protože tabulka sledování má neexistující řádek ze zdroje z důvodu změny primárního klíče.

- Izolace snímku musí být povolená. Další informace najdete v tématu [Izolace snímku na SQL Serveru](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Obecná omezení

- Tabulka nemůže mít sloupec identity, který není primárním klíčem.
- Primární klíč nemůže mít následující datové typy: sql_variant, binary, varbinary, image, XML. 
- Buďte opatrní při použití následujících datových typů jako primárního klíče, protože podporovaná přesnost je pouze sekundy: Time, DateTime, datetime2, DateTimeOffset.
- Názvy objektů (databáze, tabulky a sloupce) nesmí obsahovat tečky tisknutelné znaky (.), levou hranatou závorku ([) nebo pravou hranatou závorku (]).
- Ověřování Azure Active Directory se nepodporuje.
- Tabulky se stejným názvem, ale různými schématy (například dbo. Customers a Sales. Customers), nejsou podporovány.
- Sloupce s uživatelsky definovanými datovými typy nejsou podporované.

#### <a name="unsupported-data-types"></a>Nepodporované datové typy

- Souborem
- SQL/CLR – UDT
- XMLSchemacollection (podporuje XML)
- Kurzor, RowVersion, timestamp, hierarchyid

#### <a name="unsupported-column-types"></a>Nepodporované typy sloupců

Synchronizace dat nemůže synchronizovat sloupce generované jen pro čtení ani systémem. Příklad:

- Vypočítané sloupce.
- Systémem generované sloupce pro dočasné tabulky.

#### <a name="limitations-on-service-and-database-dimensions"></a>Omezení pro služby a dimenze databáze

| **Použijí**                                                      | **Omezení**              | **Alternativní řešení**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximální počet skupin synchronizace, ke kterým může patřit žádná databáze.       | 5                      |                             |
| Maximální počet koncových bodů v jedné skupině synchronizace              | 30                     |                             |
| Maximální počet místních koncových bodů v jedné skupině synchronizace. | 5                      | Vytvoření více skupin synchronizace |
| Názvy databází, tabulek, schémat a sloupců                       | 50 znaků na název |                             |
| Tabulky ve skupině synchronizace                                          | 500                    | Vytvoření více skupin synchronizace |
| Sloupce v tabulce ve skupině synchronizace                              | 1000                   |                             |
| Velikost řádku dat v tabulce                                        | 24 MB                  |                             |
| Minimální interval synchronizace                                           | 5 minut              |                             |
|||
> [!NOTE]
> V jedné skupině synchronizace může být až 30 koncových bodů, pokud je k dispozici jenom jedna skupina synchronizace. Pokud existuje více než jedna skupina synchronizace, celkový počet koncových bodů napříč všemi skupinami synchronizace nesmí překročit 30. Pokud databáze patří do více skupin synchronizace, počítá se jako několik koncových bodů, nikoli jedna.

## <a name="faq-about-sql-data-sync"></a>Nejčastější dotazy týkající se Synchronizace dat SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Kolik stojí Služba Synchronizace dat SQL

Za samotnou službu Synchronizace dat SQL se neúčtují žádné poplatky.  Nicméně se vám stále účtují poplatky za přenos dat při přesunu dat do a z vaší SQL Database instance. Další informace najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Které oblasti podporují synchronizaci dat

Synchronizace dat SQL je k dispozici ve všech oblastech.

### <a name="is-a-sql-database-account-required"></a>Je vyžadován účet SQL Database

Ano. Pro hostování databáze centra musíte mít účet SQL Database.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Můžu použít synchronizaci dat pro synchronizaci mezi SQL Server jenom v místních databázích

Ne přímo. Synchronizaci mezi místními databázemi můžete SQL Server nepřímo, ale vytvořením databáze centra v Azure a následným přidáním místních databází do skupiny synchronizace.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Můžu použít synchronizaci dat pro synchronizaci mezi databázemi SQL, které patří do různých předplatných

Ano. Můžete synchronizovat mezi databázemi SQL, které patří do skupin prostředků vlastněných různými předplatnými.

- Pokud předplatná patří ke stejnému tenantovi a máte oprávnění ke všem předplatným, můžete skupinu synchronizace nakonfigurovat v Azure Portal.
- V opačném případě je nutné použít PowerShell k přidání členů synchronizace, kteří patří do různých předplatných.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Můžu použít synchronizaci dat pro synchronizaci mezi databázemi SQL, které patří do různých cloudů (například veřejný cloud Azure a Azure Čína)

Ano. Můžete synchronizovat mezi databázemi SQL, které patří do různých cloudů. k přidání členů synchronizace, kteří patří do různých předplatných, musíte použít PowerShell.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Můžu použít synchronizaci dat k osazení dat z mé provozní databáze do prázdné databáze a pak je synchronizovat

Ano. Vytvořte schéma ručně v nové databázi skriptováním z původní. Po vytvoření schématu přidejte tabulky do skupiny synchronizace a zkopírujte data a udržujte je synchronizovaná.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Mám použít Synchronizace dat SQL k zálohování a obnovení mých databází

Nedoporučuje se používat Synchronizace dat SQL k vytvoření zálohy dat. Nemůžete zálohovat a obnovovat k určitému bodu v čase, protože synchronizace Synchronizace dat SQL nejsou ve verzi. Kromě toho Synchronizace dat SQL nezálohují jiné objekty SQL, například uložené procedury, a neprovádí operaci obnovení rychlou akcí.

Jednu z doporučených postupů zálohování najdete v tématu [kopírování databáze SQL Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Může synchronizovat data synchronizace šifrovaných tabulek a sloupců

- Pokud databáze používá Always Encrypted, můžete synchronizovat pouze ty tabulky a sloupce, které *nejsou šifrované.* Šifrované sloupce nemůžete synchronizovat, protože synchronizace dat nemůže data dešifrovat.
- Pokud sloupec používá šifrování na úrovni sloupce (CLE), můžete sloupec synchronizovat, pokud je velikost řádku menší než maximální velikost 24 MB. Synchronizace dat zachází s sloupcem šifrovaným klíčem (CLE) jako s normálními binárními daty. Chcete-li dešifrovat data u jiných členů synchronizace, je nutné mít stejný certifikát.

### <a name="is-collation-supported-in-sql-data-sync"></a>Je kolace podporovaná v Synchronizace dat SQL

Ano. Synchronizace dat SQL podporuje kolaci v následujících scénářích:

- Pokud vybrané tabulky schématu synchronizace ještě nejsou ve vašich hub nebo členských databázích, služba při nasazení skupiny synchronizace automaticky vytvoří odpovídající tabulky a sloupce s nastavením řazení vybraným v prázdných cílových databázích.
- Pokud tabulky, které mají být synchronizovány, již existují v databázi hub i členské databáze, Synchronizace dat SQL vyžaduje, aby sloupce primárního klíče měly stejnou kolaci mezi databázemi hub a Members k úspěšnému nasazení skupiny synchronizace. Pro jiné sloupce než sloupce primárního klíče neexistují žádná omezení kolace.

### <a name="is-federation-supported-in-sql-data-sync"></a>Je ve Synchronizace dat SQL podporovaná federace

Kořenová databáze federace se dá ve službě Synchronizace dat SQL použít bez jakýchkoli omezení. Nelze přidat koncový bod federované databáze do aktuální verze Synchronizace dat SQL.

## <a name="next-steps"></a>Další kroky

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizace schématu synchronizované databáze

Je nutné aktualizovat schéma databáze ve skupině synchronizace? Změny schématu se nereplikují automaticky. Některá řešení najdete v následujících článcích:

- [Automatizace replikace změn schématu v Azure Synchronizace dat SQL](sql-database-update-sync-schema.md)
- [Použití PowerShellu k aktualizaci schématu synchronizace v existující skupině synchronizace](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorování a odstraňování potíží

Je Synchronizace dat SQL provádění podle očekávání? Pokud chcete monitorovat činnost a řešit problémy, přečtěte si následující články:

- [Monitorování Synchronizace dat SQL Azure pomocí protokolů Azure Monitor](sql-database-sync-monitor-oms.md)
- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Další informace o Azure SQL Database

Další informace o SQL Database najdete v následujících článcích:

- [Přehled služby SQL Database](sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
