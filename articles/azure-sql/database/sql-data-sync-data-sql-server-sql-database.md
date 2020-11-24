---
title: Co je Synchronizace dat SQL pro Azure?
description: Tento přehled zavádí Synchronizace dat SQL pro Azure, který umožňuje synchronizaci dat napříč několika cloudy a místními databázemi.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: c77001707eda7c208ad19a014a1f0cff2b85b25d
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95736472"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Co je Synchronizace dat SQL pro Azure?

Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč více databázemi v místním prostředí i v cloudu. 

> [!IMPORTANT]
> Azure Synchronizace dat SQL v tuto chvíli nepodporuje spravovanou instanci SQL Azure.


## <a name="overview"></a>Přehled 

Synchronizace dat je založena na konceptu skupiny synchronizace. Skupina synchronizace je skupina databází, které chcete synchronizovat.

Synchronizace dat používá k synchronizaci dat topologii hvězdicové a Paprskové topologie. Jednu z databází ve skupině synchronizace definujete jako databázi centra. Ostatní databáze jsou členské databáze. Synchronizace probíhá pouze mezi rozbočovačem a jednotlivými členy.

- **Databáze centra** musí být Azure SQL Database.
- **Členské databáze** mohou být buď databáze v Azure SQL Database nebo v instancích SQL Server.
- **Databáze metadat synchronizace** obsahuje metadata a protokol pro synchronizaci dat. Databáze metadat synchronizace musí být Azure SQL Database umístěná ve stejné oblasti jako databáze centra. Databáze metadat synchronizace je vytvořená zákazníkem a vlastníci. Pro každou oblast a předplatné můžete mít jenom jednu databázi s metadaty synchronizace. Databázi metadat synchronizace nelze odstranit ani přejmenovat, pokud existují skupiny synchronizace nebo agenti synchronizace. Společnost Microsoft doporučuje vytvořit novou, prázdnou databázi pro použití jako databázi metadat synchronizace. Synchronizace dat vytvoří tabulky v této databázi a spustí časté úlohy.

> [!NOTE]
> Pokud používáte místní databázi jako členskou databázi, je nutné [nainstalovat a nakonfigurovat místního agenta synchronizace](sql-data-sync-sql-server-configure.md#add-on-prem).

![Synchronizace dat mezi databázemi](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Skupina synchronizace má následující vlastnosti:

- **Schéma synchronizace** popisuje, která data se synchronizují.
- **Směr synchronizace** může být obousměrný nebo může tok pouze v jednom směru. To znamená, že směr synchronizace může být z *rozbočovače na člena* nebo z *člena do středu nebo do* obou.
- **Interval synchronizace** popisuje, jak často dochází k synchronizaci.
- **Zásada řešení konfliktů** je zásada na úrovni skupiny, kterou je možné vytvořit jako *rozbočovač WINS* nebo *Členové služby WINS*.

## <a name="when-to-use"></a>Kdy je použít

Synchronizace dat je užitečná v případech, kdy je potřeba udržovat data v různých databázích ve Azure SQL Database nebo SQL Server. Tady jsou hlavní případy použití pro synchronizaci dat:

- **Synchronizace hybridních dat:** Díky synchronizaci dat můžete udržovat data synchronizovaná mezi vašimi databázemi v SQL Server a Azure SQL Database povolit hybridní aplikace. Tato schopnost může vydávat potíže zákazníkům, kteří zvažuje přesun do cloudu a chtějí do Azure umístit některé z jeho aplikací.
- **Distribuované aplikace:** V mnoha případech je výhodné oddělit různé úlohy napříč různými databázemi. Například pokud máte rozsáhlou provozní databázi, ale budete muset pro tato data spustit také úlohu vytváření sestav nebo analýzy, je vhodné mít pro toto další zatížení druhou databázi. Tento přístup minimalizuje dopad na výkon na produkční úlohy. Synchronizaci dat můžete použít, chcete-li tyto dvě databáze uchovat synchronizované.
- **Globálně distribuované aplikace:** Řada firem zahrnuje několik oblastí a dokonce i několik zemí nebo oblastí. Abyste minimalizovali latenci sítě, je nejlepší mít data v oblasti, která je blízko vás. Díky synchronizaci dat můžete snadno uchovávat databáze v oblastech po celém světě.

Synchronizace dat není preferovaným řešením pro následující scénáře:

| Scénář | Některá doporučená řešení |
|----------|----------------------------|
| Zotavení po havárii | [Geograficky redundantní zálohy Azure](automated-backups-overview.md) |
| Čtení stupnice | [Použití replik jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení (Preview)](read-scale-out.md) |
| ETL (OLTP až OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) nebo [služba SSIS (SQL Server Integration Services)](/sql/integration-services/sql-server-integration-services) |
| Migrace z SQL Server do Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||



## <a name="how-it-works"></a>Jak to funguje

- **Sledování změn dat:** Synchronizace dat sleduje změny pomocí aktivačních událostí vložení, aktualizace a odstranění. Změny jsou zaznamenány v tabulce na straně uživatele v uživatelské databázi. Všimněte si, že BULK INSERT ve výchozím nastavení neaktivují triggery. Pokud není zadaný FIRE_TRIGGERS, nespustí se žádné triggery vložení. Přidejte možnost FIRE_TRIGGERS, aby synchronizace dat mohla sledovat tato vložení. 
- **Synchronizují se data:** Synchronizace dat je navržena v modelu hvězdicového a paprskového modelu. Centrum se synchronizuje s každým členem zvlášť. Změny z centra se stáhnou do člena a změny od tohoto člena se nahrají do centra.
- **Řeší se konflikty:** Synchronizace dat poskytuje dvě možnosti pro řešení konfliktů, *centrum WINS* nebo *člena služby WINS*.
  - Pokud vyberete možnost *centrum služby WINS*, změny v centru budou vždy přepisovat změny v členu.
  - Pokud vyberete možnost *Členové serveru WINS*, změny ve členovi přepíšou změny v centru. Pokud existuje více než jeden člen, bude konečná hodnota záviset na tom, který člen se nejprve synchronizuje.

## <a name="compare-with-transactional-replication"></a>Porovnání s transakční replikací

| | Synchronizace dat | Transakční replikace |
|---|---|---|
| **Výhody** | – Aktivní – aktivní podpora<br/>– Obousměrné mezi místními a Azure SQL Database | – Nižší latence<br/>– Transakční konzistence<br/>-Opětovné použití existující topologie po migraci <br/>– Podpora spravované instance Azure SQL |
| **Nevýhody** | -Žádná transakční konzistence<br/>– Vyšší dopad na výkon | -Nelze publikovat z Azure SQL Database <br/>– Náklady vysoké údržby |

## <a name="get-started"></a>Začínáme 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Nastavení synchronizace dat v Azure Portal

- [Nastavení synchronizace dat SQL v Azure](sql-data-sync-sql-server-configure.md)
- Agent synchronizace dat – [Agent synchronizace dat pro Azure synchronizace dat SQL](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Nastavení synchronizace dat pomocí PowerShellu

- [Použití PowerShellu k synchronizaci mezi několika databázemi v Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Použití PowerShellu k synchronizaci mezi databází v Azure SQL Database a databázemi v instanci SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Projděte si osvědčené postupy pro synchronizaci dat

- [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Došlo k chybě.

- [Řešení potíží se Synchronizací dat SQL Azure](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Konzistence a výkon

### <a name="eventual-consistency"></a>Případná konzistence

Vzhledem k tomu, že synchronizace dat je založená na triggeru, není zaručena transakční konzistence. Společnost Microsoft zaručuje, že všechny změny budou provedeny nakonec a že synchronizace dat nezpůsobí ztrátu dat.

### <a name="performance-impact"></a>Dopad na výkon

Synchronizace dat sleduje změny pomocí aktivačních událostí vložení, aktualizace a odstranění. Vytvoří v uživatelské databázi vedlejší tabulky pro sledování změn. Tyto aktivity sledování změn mají dopad na zatížení vaší databáze. V případě potřeby vyhodnoťte úroveň služby a upgradujte.

Zřizování a rušení zřizování během vytváření skupiny synchronizace, aktualizace a odstraňování může mít vliv i na výkon databáze.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Požadavky a omezení

### <a name="general-requirements"></a>Obecné požadavky

- Každá tabulka musí mít primární klíč. Neměňte hodnotu primárního klíče na žádném řádku. Pokud je nutné změnit hodnotu primárního klíče, odstraňte řádek a vytvořte jej znovu s novou hodnotou primárního klíče.

> [!IMPORTANT]
> Změna hodnoty existujícího primárního klíče bude mít za následek následující chybné chování:
> - Data mezi centrem a členy mohou být ztracena, i když synchronizace neoznamuje žádné potíže.
> - Synchronizace může selhat, protože tabulka sledování má neexistující řádek ze zdroje z důvodu změny primárního klíče.

- Pro členy synchronizace i pro centrum musí být povolena izolace snímku. Další informace najdete v tématu [Izolace snímku na SQL Serveru](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Obecná omezení

- Tabulka nemůže mít sloupec identity, který není primárním klíčem.
- Tabulka musí obsahovat clusterovaný index, aby bylo možné použít synchronizaci dat.
- Primární klíč nemůže mít následující datové typy: sql_variant, binary, varbinary, image, XML.
- Buďte opatrní při použití následujících datových typů jako primárního klíče, protože podporovaná přesnost je pouze sekundy: Time, DateTime, datetime2, DateTimeOffset.
- Názvy objektů (databáze, tabulky a sloupce) nesmí obsahovat tečky tisknutelné znaky (.), levou hranatou závorku ([) nebo pravou hranatou závorku (]).
- Název tabulky nesmí obsahovat tisknutelné znaky:! "# $% ' () * +-Space
- Ověřování Azure Active Directory se nepodporuje.
- Pokud jsou tabulky se stejným názvem, ale s jiným schématem (například dbo. Customers and Sales. Customers), je možné přidat do synchronizace pouze jednu z tabulek.
- Sloupce s datovými typy User-Defined se nepodporují.
- Přesun serverů mezi různými předplatnými není podporován. 

#### <a name="unsupported-data-types"></a>Nepodporované datové typy

- Souborem
- SQL/CLR – UDT
- XMLSchemacollection (podporuje XML)
- Kurzor, RowVersion, timestamp, hierarchyid

#### <a name="unsupported-column-types"></a>Nepodporované typy sloupců

Synchronizace dat nemůže synchronizovat sloupce generované jen pro čtení ani systémem. Například:

- Vypočítané sloupce.
- Systémem generované sloupce pro dočasné tabulky.

#### <a name="limitations-on-service-and-database-dimensions"></a>Omezení pro služby a dimenze databáze

| **Dimenze**                                                  | **Omezení**              | **Alternativní řešení**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximální počet skupin synchronizace, ke kterým může patřit žádná databáze.       | 5                      |                             |
| Maximální počet koncových bodů v jedné skupině synchronizace              | 30                     |                             |
| Maximální počet místních koncových bodů v jedné skupině synchronizace. | 5                      | Vytvoření více skupin synchronizace |
| Názvy databází, tabulek, schémat a sloupců                       | 50 znaků na název |                             |
| Tabulky ve skupině synchronizace                                          | 500                    | Vytvoření více skupin synchronizace |
| Sloupce v tabulce ve skupině synchronizace                              | 1000                   |                             |
| Velikost řádku dat v tabulce                                        | 24 MB                  |                             |

> [!NOTE]
> V jedné skupině synchronizace může být až 30 koncových bodů, pokud je k dispozici jenom jedna skupina synchronizace. Pokud existuje více než jedna skupina synchronizace, celkový počet koncových bodů napříč všemi skupinami synchronizace nesmí překročit 30. Pokud databáze patří do více skupin synchronizace, počítá se jako několik koncových bodů, nikoli jedna.

### <a name="network-requirements"></a>Požadavky sítě

Po navázání skupiny synchronizace se musí služba synchronizace dat připojit k databázi centra. V době, kdy vytváříte skupinu synchronizace, musí mít server SQL Azure ve svém nastavení následující konfiguraci `Firewalls and virtual networks` :

 * *Odepření přístupu k veřejné síti* musí být nastavené na *vypnuto*.
 * *Povolte službám a prostředkům Azure přístup k tomuto serveru* musí být nastavené na *Ano*, nebo musíte vytvořit pravidla protokolu IP pro [IP adresy používané službou synchronizace dat](network-access-controls-overview.md#data-sync).

Jakmile skupinu synchronizace vytvoříte a zřídíte, můžete tato nastavení zakázat. Agent synchronizace se připojí přímo k databázi centra a pomocí [pravidel IP adres brány firewall](firewall-configure.md) serveru nebo [privátních koncových bodů](private-endpoint-overview.md) můžete agentům přístup k serveru centrálního přístupu dovolit.

> [!NOTE]
> Pokud změníte nastavení schématu skupiny synchronizace, budete muset službě synchronizace dat umožnit přístup k serveru znovu, aby bylo možné databázi centra znovu zřídit.

## <a name="faq-about-sql-data-sync"></a>Nejčastější dotazy týkající se Synchronizace dat SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Kolik stojí Služba Synchronizace dat SQL

Za službu Synchronizace dat SQL sám se neúčtují žádné poplatky. Stále ale budete shromažďovat poplatky za přenos dat z SQL Database instance a z ní. Další informace najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Které oblasti podporují synchronizaci dat

Synchronizace dat SQL je k dispozici ve všech oblastech.

### <a name="is-a-sql-database-account-required"></a>Je vyžadován účet SQL Database

Ano. Pro hostování databáze centra musíte mít účet SQL Database.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Můžu použít synchronizaci dat pro synchronizaci mezi SQL Servermi databázemi

Ne přímo. Je možné synchronizovat mezi SQL Servermi databázemi nepřímo, ale vytvořením databáze centra v Azure a následným přidáním místních databází do skupiny synchronizace.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Můžu použít synchronizaci dat pro synchronizaci mezi databázemi v SQL Database, které patří do různých předplatných.

Ano. Můžete synchronizovat mezi databázemi patřícími do skupin prostředků vlastněných různými předplatnými.

- Pokud předplatná patří ke stejnému tenantovi a máte oprávnění ke všem předplatným, můžete skupinu synchronizace nakonfigurovat v Azure Portal.
- V opačném případě je nutné použít PowerShell k přidání členů synchronizace, kteří patří do různých předplatných.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Můžu použít synchronizaci dat pro synchronizaci mezi databázemi v SQL Database, které patří do různých cloudů (například veřejný cloud Azure a Azure Čína 21Vianet).

Ano. Můžete synchronizovat mezi databázemi, které patří do různých cloudů. K přidání členů synchronizace, kteří patří do různých předplatných, musíte použít PowerShell.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Můžu použít synchronizaci dat k osazení dat z mé provozní databáze do prázdné databáze a pak je synchronizovat

Ano. Vytvořte schéma ručně v nové databázi skriptováním z původní. Po vytvoření schématu přidejte tabulky do skupiny synchronizace a zkopírujte data a udržujte je synchronizovaná.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Mám použít Synchronizace dat SQL k zálohování a obnovení mých databází

Nedoporučuje se používat Synchronizace dat SQL k vytvoření zálohy dat. Nemůžete zálohovat a obnovovat k určitému bodu v čase, protože synchronizace Synchronizace dat SQL nejsou ve verzi. Kromě toho Synchronizace dat SQL nezálohují jiné objekty SQL, například uložené procedury, a neprovádí operaci obnovení rychle.

Jednu z doporučených postupů zálohování najdete [v tématu kopírování databáze v Azure SQL Database](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Může synchronizovat data synchronizace šifrovaných tabulek a sloupců

- Pokud databáze používá Always Encrypted, můžete synchronizovat pouze ty tabulky a sloupce, které *nejsou šifrované.* Šifrované sloupce nemůžete synchronizovat, protože synchronizace dat nemůže data dešifrovat.
- Pokud sloupec používá Column-Level šifrování (CLE), můžete sloupec synchronizovat, pokud je velikost řádku menší než maximální velikost 24 MB. Synchronizace dat zachází s sloupcem šifrovaným klíčem (CLE) jako s normálními binárními daty. Chcete-li dešifrovat data u jiných členů synchronizace, je nutné mít stejný certifikát.

### <a name="is-collation-supported-in-sql-data-sync"></a>Je kolace podporovaná v Synchronizace dat SQL

Ano. Synchronizace dat SQL podporuje kolaci v následujících scénářích:

- Pokud vybrané tabulky schématu synchronizace ještě nejsou ve vašich hub nebo členských databázích, služba při nasazení skupiny synchronizace automaticky vytvoří odpovídající tabulky a sloupce s nastavením řazení vybraným v prázdných cílových databázích.
- Pokud tabulky, které mají být synchronizovány, již existují v databázi hub i členské databáze, Synchronizace dat SQL vyžaduje, aby sloupce primárního klíče měly stejnou kolaci mezi databázemi hub a Members k úspěšnému nasazení skupiny synchronizace. Pro jiné sloupce než sloupce primárního klíče neexistují žádná omezení kolace.

### <a name="is-federation-supported-in-sql-data-sync"></a>Je ve Synchronizace dat SQL podporovaná federace

Kořenová databáze federace se dá ve službě Synchronizace dat SQL použít bez jakýchkoli omezení. Nelze přidat koncový bod federované databáze do aktuální verze Synchronizace dat SQL.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Můžu použít synchronizaci dat pro synchronizaci dat exportovaných z Dynamics 365 pomocí funkce Přineste si vlastní databázi (BYOD)?

Funkce Dynamics 365 Přineste si vlastní databázi umožňuje správcům exportovat datové entity z aplikace do své vlastní Microsoft Azure SQL Database. Synchronizaci dat lze použít k synchronizaci těchto dat do jiných databází, pokud jsou exportována data pomocí **přírůstkového nabízeného oznámení** (úplné nabízené oznámení není podporováno) a **možnost aktivační události v cílové databázi** je nastavena na **hodnotu Ano**.

## <a name="next-steps"></a>Další kroky

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizace schématu synchronizované databáze

Je nutné aktualizovat schéma databáze ve skupině synchronizace? Změny schématu se nereplikují automaticky. Některá řešení najdete v následujících článcích:

- [Automatizace replikace změn schématu pomocí Synchronizace dat SQL v Azure](./sql-data-sync-update-sync-schema.md)
- [Aktualizace schématu synchronizace ve stávající skupině synchronizace pomocí PowerShellu](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitorování a odstraňování potíží

Je Synchronizace dat SQL podle očekávání? Pokud chcete monitorovat činnost a řešit problémy, přečtěte si následující články:

- [Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor](./monitor-tune-overview.md)
- [Řešení potíží se Synchronizací dat SQL Azure](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Další informace o Azure SQL Database

Další informace o Azure SQL Database najdete v následujících článcích:

- [Přehled služby SQL Database](sql-database-paas-overview.md)
- [Správa životního cyklu databáze](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
