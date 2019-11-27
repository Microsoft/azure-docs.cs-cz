---
title: Synchronizace dat
description: Tento přehled zavádí synchronizace dat SQL Azure
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
ms.openlocfilehash: 1ee2efbb8aebfc2f1a94c89edef6166898946d8a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422522"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronizace dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL

Synchronizace dat SQL je služba založená na Azure SQL Database, která umožňuje synchronizovat data, která jste vybrali obousměrně napříč několika databázemi SQL a instance systému SQL Server.

> [!IMPORTANT]
> Azure Synchronizace dat SQL v tuto chvíli nepodporuje spravovanou instanci Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Kdy použít synchronizace dat

Synchronizace dat je užitečná v případech, kdy je potřeba aktualizovat data napříč několika databázemi SQL Azure nebo databázemi SQL Server. Tady jsou hlavní svědectví pro synchronizaci dat:

- **Synchronizace hybridních dat:** Díky synchronizaci dat můžete udržovat data synchronizovaná mezi místními databázemi a databázemi SQL Azure a povolit tak hybridní aplikace. Tato funkce může odvolat zákazníkům, kteří jsou zvažujete Přesun do cloudu a chcete poskládali z jejich aplikace v Azure.
- **Distribuované aplikace:** V mnoha případech je výhodné oddělit různé úlohy napříč různými databázemi. Například pokud máte velké provozní databáze, ale budete potřebovat ke spuštění úlohy vytváření sestav, nebo analýzy těchto dat, je užitečné mít druhý databáze pro tento další úlohy. Tento přístup minimalizuje dopad na výkon na vaše produkční úlohy. Synchronizace dat můžete ponechat tyto dvě databáze synchronizované.
- **Globálně distribuované aplikace:** Řada firem zahrnuje několik oblastí a dokonce i několik zemí nebo oblastí. Kvůli minimalizaci latence sítě, je nejvhodnější pro vaše data v oblasti blízko vás. Pomocí synchronizace dat se snadnou vejdou databází v oblastech po celém světě synchronizované.

Synchronizace dat není preferovaným řešením pro následující scénáře:

| Scénář | Některé doporučená řešení. |
|----------|----------------------------|
| Zotavení po havárii | [Geograficky redundantní zálohy Azure](sql-database-automated-backups.md) |
| Škálování pro čtení | [Použití replik jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení (Preview)](sql-database-read-scale-out.md) |
| ETL (OLTP na OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) nebo [služba SSIS (SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrace z místního SQL serveru do Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Přehled Synchronizace dat SQL

Synchronizace dat vychází kolem koncepce skupině synchronizace. Skupina synchronizace se skupinou databází, které se mají synchronizovat.

Synchronizace dat používá k synchronizaci dat hvězdicové topologii. Definujte jedné z databází ve skupině synchronizace jako databáze centra. Zbytek databáze je databází člena. Synchronizace nastávají jenom mezi centra a jednotlivé členy.

- **Databáze centra** musí být Azure SQL Database.
- **Členské databáze** můžou být databáze SQL, místní SQL Server databáze nebo instance SQL Server na virtuálních počítačích Azure.
- **Synchronizovaná databáze** obsahuje metadata a protokol pro synchronizaci dat. Synchronizovaná databáze musí být Azure SQL Database nacházející se ve stejné oblasti jako databáze centra. Synchronizace databáze je zákazník vytvořil a vlastněné zákazníkem.

> [!NOTE]
> Pokud používáte místní databázi jako členskou databázi, je nutné [nainstalovat a nakonfigurovat místního agenta synchronizace](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizace dat mezi databázemi](media/sql-database-sync-data/sync-data-overview.png)

Skupina synchronizace má následující vlastnosti:

- **Schéma synchronizace** popisuje, která data se synchronizují.
- **Směr synchronizace** může být obousměrný nebo může tok pouze v jednom směru. To znamená, že směr synchronizace může být z *rozbočovače na člena*nebo z *člena do středu nebo do*obou.
- **Interval synchronizace** popisuje, jak často dochází k synchronizaci.
- **Zásada řešení konfliktů** je zásada na úrovni skupiny, kterou je možné vytvořit jako *rozbočovač WINS* nebo *Členové služby WINS*.

## <a name="how-does-data-sync-work"></a>Jak synchronizace dat funguje

- **Sledování změn dat:** Synchronizace dat sleduje změny pomocí aktivačních událostí vložení, aktualizace a odstranění. Změny se zaznamenávají v postranní tabulky v databázi uživatelů. Všimněte si, že BULK INSERT ve výchozím nastavení neaktivují triggery. Pokud není zadaný FIRE_TRIGGERS, nespustí se žádné triggery vložení. Přidáte možnost požadováno tak synchronizaci dat můžete sledovat tyto operace vložení. 
- **Synchronizují se data:** Synchronizace dat je navržena v modelu hvězdicového a paprskového modelu. Centrum synchronizuje s každý člen jednotlivě. Změny z centra se stáhnou do člena a pak se nahrají změny od člena k rozbočovači.
- **Řeší se konflikty:** Synchronizace dat poskytuje dvě možnosti pro řešení konfliktů, *centrum WINS* nebo *člena služby WINS*.
  - Pokud vyberete možnost *centrum služby WINS*, změny v centru budou vždy přepisovat změny v členu.
  - Pokud vyberete možnost *Členové serveru WINS*, změny ve členovi přepíšou změny v centru. Pokud existuje více než jednoho člena, konečná hodnota závisí na člen, který synchronizuje poprvé.

## <a name="compare-data-sync-with-transactional-replication"></a>Porovnání synchronizace dat s transakční replikací

| | Synchronizace dat | Transakční replikace |
|---|---|---|
| Výhody | – Aktivní – aktivní podpora<br/>– Obousměrné mezi místními a Azure SQL Database | – Nižší latence<br/>– Transakční konzistence<br/>-Opětovné použití existující topologie po migraci |
| Nevýhody | – 5 min nebo větší latence<br/>-Žádná transakční konzistence<br/>– Vyšší dopad na výkon | -Nelze publikovat z Azure SQL Database izolované databáze nebo databáze ve fondu<br/>– Náklady vysoké údržby |

## <a name="get-started-with-sql-data-sync"></a>Začínáme se synchronizací dat SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Nastavení synchronizace dat na webu Azure Portal

- [Nastavení Synchronizace dat SQL Azure](sql-database-get-started-sql-data-sync.md)
- Agent synchronizace dat – [Agent synchronizace dat pro Azure synchronizace dat SQL](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Nastavení synchronizace dat s využitím Powershellu

- [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
- [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Přečtěte si osvědčené postupy pro synchronizaci dat

- [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Došlo k chybě.

- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzistencí a výkonem

### <a name="eventual-consistency"></a>Konečná konzistence

Vzhledem k tomu, že synchronizace dat je založená na triggeru, není zaručena transakční konzistence. Společnost Microsoft zaručuje, že všechny změny budou provedeny nakonec a že synchronizace dat nezpůsobí ztrátu dat.

### <a name="performance-impact"></a>Dopad na výkon

Synchronizace dat se používají vložení, aktualizace a odstranění aktivační události ke sledování změn. Vytvoří na straně tabulky v databázi uživatelů pro řešení change tracking. Tyto aktivity sledování změn mít vliv na vaše databázové úlohy. Vyhodnocení vaší úrovně služby a upgradovat v případě potřeby.

Zřizování a rušení zřízení během vytváření skupiny synchronizace, aktualizace a odstranění může mít také vliv na výkon databáze.

## <a name="sync-req-lim"></a>Požadavky a omezení

### <a name="general-requirements"></a>Obecné požadavky

- Každá tabulka musí obsahovat primární klíč. Neměňte hodnotu primární klíč do libovolného řádku. Pokud budete muset změnit hodnotu primárního klíče, řádek odstranit a znovu ho vytvořte s novou hodnotu primárního klíče.

> [!IMPORTANT]
> Změna hodnoty existujícího primárního klíče bude mít za následek následující chybné chování:
> - Data mezi centrem a členy mohou být ztracena, i když synchronizace neoznamuje žádné potíže.
> - Synchronizace může selhat, protože tabulka sledování má neexistující řádek ze zdroje z důvodu změny primárního klíče.

- Izolace snímku musí být povolená. Další informace najdete v tématu [Izolace snímku na SQL Serveru](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Obecná omezení

- Tabulka nemůže mít sloupec identity, který není primárním klíčem.
- Primární klíč nemůže mít následující datové typy: sql_variant, binary, varbinary, image, XML.
- Buďte opatrní při použití těchto datových typů jako primární klíč, protože podporované přesnost je jenom na druhý: čas, datum a čas, datetime2, datetimeoffset.
- Názvy objektů (databáze, tabulky a sloupce) nesmí obsahovat tečky tisknutelné znaky (.), levou hranatou závorku ([) nebo pravou hranatou závorku (]).
- Ověřování Azure Active Directory se nepodporuje.
- Tabulky se stejným názvem, ale různými schématy (například dbo. Customers a Sales. Customers), nejsou podporovány.
- Sloupce s uživatelsky definovanými datovými typy nejsou podporované.

#### <a name="unsupported-data-types"></a>Nepodporované datové typy

- FileStream
- SQL/CLR UDT
- Kolekci XMLSchemaCollection (XML podporováno)
- Kurzor, RowVersion, časové razítko, Hierarchyid

#### <a name="unsupported-column-types"></a>Typy nepodporovanému sloupci

Synchronizace dat nelze synchronizovat jen pro čtení nebo systémem generované sloupce. Příklad:

- Vypočítané sloupce.
- Systémem generovaných sloupce pro dočasné tabulky.

#### <a name="limitations-on-service-and-database-dimensions"></a>Omezení týkající se služby a databáze dimenze

| **Použijí**                                                  | **Omezení**              | **Alternativní řešení**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximální počet skupin synchronizace všech databází může patřit do.       | 5                      |                             |
| Maximální počet koncových bodů ve skupině jedním synchronizačním              | 30                     |                             |
| Maximální počet místní koncové body ve skupině jedním synchronizačním. | 5                      | Vytvořte více skupin synchronizace |
| Názvy databáze, tabulky, schéma a sloupce                       | 50 znaky na název |                             |
| Tabulky ve skupině synchronizace                                          | 500                    | Vytvořte více skupin synchronizace |
| Sloupce v tabulce v skupinu synchronizace                              | 1000                   |                             |
| Velikost dat řádku v tabulce                                        | 24 Mb                  |                             |
| Interval synchronizace minimální                                           | 5 minut              |                             |

> [!NOTE]
> Může existovat maximálně 30 koncové body ve skupině jedním synchronizačním dojde pouze jednu skupinu synchronizace. Pokud existuje více než jednu skupinu synchronizace, celkový počet koncových bodů ve všech skupinách synchronizace může mít maximálně 30. Pokud databáze náleží do několika skupin synchronizace, počítá se jako několik koncových bodů, není jednou.

## <a name="faq-about-sql-data-sync"></a>Nejčastější dotazy týkající se synchronizace dat SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Kolik stojí Služba Synchronizace dat SQL

Za službu Synchronizace dat SQL sám se neúčtují žádné poplatky. Stále ale budete shromažďovat poplatky za přenos dat z SQL Database instance a z ní. Další informace najdete v tématu [SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Které oblasti podporují synchronizaci dat

Synchronizace dat SQL je k dispozici ve všech oblastech.

### <a name="is-a-sql-database-account-required"></a>Je vyžadován účet SQL Database

Ano. Musíte mít účet databáze SQL k hostování databáze centra.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Můžu použít synchronizaci dat pro synchronizaci mezi SQL Server jenom v místních databázích

Ne přímo. Můžete synchronizovat mezi databází systému SQL Server v místním nepřímo, ale vytváří se Centrum databáze v Azure, a následným přidáním místních databází do skupiny synchronizace.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Můžu použít synchronizaci dat pro synchronizaci mezi databázemi SQL, které patří do různých předplatných

Ano. Můžete synchronizovat mezi databázemi SQL, který patří do skupiny prostředků, které jsou vlastníkem různých předplatných.

- Pokud předplatná patří do stejného tenanta a máte oprávnění pro všechna předplatná, můžete nakonfigurovat skupinu synchronizace na portálu Azure portal.
- V opačném případě budete muset pomocí prostředí PowerShell můžete přidat členy synchronizace, které patří do různých předplatných.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Můžu použít synchronizaci dat pro synchronizaci mezi databázemi SQL, které patří do různých cloudů (například veřejný cloud Azure a Azure Čína 21Vianet).

Ano. Můžete synchronizovat mezi databázemi SQL, do které patří do různých cloudů, je nutné použít prostředí PowerShell přidat členy synchronizace, které patří do různých předplatných.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Můžu použít synchronizaci dat k osazení dat z mé provozní databáze do prázdné databáze a pak je synchronizovat

Ano. Vytvoření schématu ručně v nové databázi pomocí skriptů z původní. Po vytvoření schématu tabulky přidáte do skupiny synchronizace pro kopírování dat a zachování jejich synchronizované.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Mám použít Synchronizace dat SQL k zálohování a obnovení mých databází

Nedoporučuje se používat Synchronizace dat SQL k vytvoření zálohy dat. Nemůžete zálohovat a obnovovat k určitému bodu v čase, protože synchronizace Synchronizace dat SQL nejsou ve verzi. Kromě toho Synchronizace dat SQL nezálohují jiné objekty SQL, například uložené procedury, a neprovádí operaci obnovení rychle.

Jednu z doporučených postupů zálohování najdete v tématu [kopírování databáze SQL Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Může synchronizovat data synchronizace šifrovaných tabulek a sloupců

- Pokud databáze používá Always Encrypted, můžete synchronizovat pouze ty tabulky a sloupce, které *nejsou šifrované.* Šifrované sloupce nelze synchronizovat, protože synchronizace dat nemůže data dešifrovat.
- Pokud se sloupec používá šifrování sloupců (Vymazat), můžete synchronizovat sloupce, velikost řádku je menší než maximální velikost 24 Mb. Synchronizace dat zpracuje sloupec jako normální binární data zašifrovaná pomocí klíče (Vymazat). Dešifrovat data na ostatní členy synchronizace, musíte mít stejný certifikát.

### <a name="is-collation-supported-in-sql-data-sync"></a>Je kolace podporovaná v Synchronizace dat SQL

Ano. Synchronizace dat SQL podporuje kolaci v následujících scénářích:

- Pokud vybrané tabulky schématu synchronizace ještě nejsou ve vašich hub nebo členských databázích, služba při nasazení skupiny synchronizace automaticky vytvoří odpovídající tabulky a sloupce s nastavením řazení vybraným v prázdných cílových databázích.
- Pokud tabulky, které chcete synchronizovat, už existují v centra a členské databáze, synchronizaci dat SQL vyžaduje, primární klíč sloupců stejnou kolaci mezi databázemi centra a člen úspěšně nasadit skupinu synchronizace. Neexistují žádná omezení řazení pro sloupce kromě sloupce primárních klíčů.

### <a name="is-federation-supported-in-sql-data-sync"></a>Je ve Synchronizace dat SQL podporovaná federace

Kořenová databáze federace je možné ve službě synchronizace dat SQL bez omezení. Nelze přidat koncový bod federované databáze do aktuální verze Synchronizace dat SQL.

## <a name="next-steps"></a>Další kroky

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizovat schéma synchronizace databáze

Budete muset aktualizovat schéma databáze ve skupině synchronizace? Změny schématu se nereplikují automaticky. Některá řešení najdete v následujících článcích:

- [Automatizace replikace změn schématu v Azure Synchronizace dat SQL](sql-database-update-sync-schema.md)
- [Použití PowerShellu k aktualizaci schématu synchronizace v existující skupině synchronizace](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorování a odstraňování potíží

Je Synchronizace dat SQL podle očekávání? Monitorování a řešení potíží, najdete v následujících článcích:

- [Monitorování Synchronizace dat SQL Azure pomocí protokolů Azure Monitor](sql-database-sync-monitor-oms.md)
- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Další informace o databázi SQL Database služby Azure

Další informace o službě SQL Database najdete v následujících článcích:

- [Přehled služby SQL Database](sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
