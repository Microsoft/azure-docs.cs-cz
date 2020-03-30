---
title: Synchronizace dat
description: Tento přehled představuje Azure SQL Data Sync
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74422522"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronizace dat ve více cloudových a místních databázích pomocí synchronizace dat SQL

SQL Data Sync je služba postavená na Azure SQL Database, která umožňuje synchronizovat data, která vyberete obousměrně mezi více databází SQL a instancí SQL Serveru.

> [!IMPORTANT]
> Azure SQL Data Sync v tuto chvíli nepodporuje spravovanou instanci Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Kdy použít synchronizaci dat

Synchronizace dat je užitečná v případech, kdy je potřeba data aktualizovat v několika databázích Azure SQL nebo databázích SQL Serveru. Zde jsou hlavní případy použití pro synchronizaci dat:

- **Hybridní synchronizace dat:** Pomocí synchronizace dat můžete udržovat data synchronizovaná mezi místními databázemi a databázemi Azure SQL a povolit hybridní aplikace. Tato funkce může oslovit zákazníky, kteří zvažují přechod do cloudu a chtěli byste umístit některé jejich aplikace v Azure.
- **Distribuované aplikace:** V mnoha případech je výhodné oddělit různé úlohy v různých databázích. Například pokud máte velké produkční databáze, ale také je potřeba spustit vytváření sestav nebo analytické úlohy na tato data, je užitečné mít druhou databázi pro tuto další úlohu. Tento přístup minimalizuje dopad na výkon na produkční úlohy. Pomocí synchronizace dat můžete tyto dvě databáze synchronizovat.
- **Globálně distribuované aplikace:** Mnoho podniků zahrnuje několik regionů a dokonce i několik zemí/oblastí. Chcete-li minimalizovat latenci sítě, je nejlepší mít data v oblasti blízko vás. Díky synchronizaci dat můžete snadno synchronizovat databáze v oblastech po celém světě.

Synchronizace dat není upřednostňovaným řešením pro následující scénáře:

| Scénář | Některá doporučená řešení |
|----------|----------------------------|
| Zotavení po havárii | [Geograficky redundantní zálohy Azure](sql-database-automated-backups.md) |
| Měřítko čtení | [Použití replik jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení (preview)](sql-database-read-scale-out.md) |
| ETL (OLTP až OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) nebo [integrační služby SQL Serveru](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrace z místního SQL Serveru do Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Přehled synchronizace dat SQL

Synchronizace dat je založena na konceptu skupiny synchronizace. Skupina synchronizace je skupina databází, kterou chcete synchronizovat.

Synchronizace dat používá k synchronizaci dat topologii rozbočovače a paprsku. Jednu z databází ve skupině synchronizace definujete jako databázi rozbočovače. Zbytek databází jsou členské databáze. Synchronizace probíhá pouze mezi centrem a jednotlivými členy.

- **Hub databáze** musí být Azure SQL database.
- **Členské databáze** mohou být buď databáze SQL, místní databáze SQL Serveru nebo instance SQL Serveru na virtuálních počítačích Azure.
- **Synchronizační databáze** obsahuje metadata a protokol pro synchronizaci dat. Databáze synchronizace musí být databáze Azure SQL, která se nachází ve stejné oblasti jako databáze hubu. Databáze synchronizace je vytvořena zákazníkem a vlastněna zákazníkem.

> [!NOTE]
> Pokud používáte místní databázi jako databázi členů, je nutné [nainstalovat a nakonfigurovat místního agenta synchronizace](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizace dat mezi databázemi](media/sql-database-sync-data/sync-data-overview.png)

Skupina synchronizace má následující vlastnosti:

- **Schéma synchronizace** popisuje, která data jsou synchronizována.
- **Směr synchronizace** může být obousměrný nebo může natékat pouze v jednom směru. To znamená, že směr synchronizace může být *Hub na člena*, nebo člen do *centra*, nebo obojí.
- **Interval synchronizace** popisuje, jak často dochází k synchronizaci.
- **Zásady řešení konfliktů** jsou zásady na úrovni skupiny, které mohou být *výhry centra nebo* členské *výhry*.

## <a name="how-does-data-sync-work"></a>Jak funguje synchronizace dat

- **Změny dat sledování:** Synchronizace dat sleduje změny pomocí aktivačních událostí vložení, aktualizace a odstranění. Změny jsou zaznamenány v odstačné tabulce v databázi uživatelů. Všimněte si, že BULK INSERT nespustí aktivační události ve výchozím nastavení. Pokud není zadán FIRE_TRIGGERS, nespustí se žádné aktivační události vložení. Přidejte možnost FIRE_TRIGGERS, aby synchronizace dat mohla tyto vložení sledovat. 
- **Synchronizace dat:** Synchronizace dat je navržena v modelu Hub a Spoke. Centrum se synchronizuje s každým členem jednotlivě. Změny z centra se stáhnou do člena a potom se změny z člena nahrají do centra.
- **Řešení konfliktů:** Synchronizace dat poskytuje dvě možnosti řešení konfliktů, *výhry hubů* nebo *vítězství členů*.
  - Pokud vyberete *Hub vyhraje*, změny v centru vždy přepsat změny v členu.
  - Pokud vyberete *členské vyhrává*, změny v člen přepsat změny v rozbočovači. Pokud existuje více než jeden člen, konečná hodnota závisí na tom, který člen synchronizuje jako první.

## <a name="compare-data-sync-with-transactional-replication"></a>Porovnat synchronizaci dat s transakční replikací

| | Synchronizace dat | Transakční replikace |
|---|---|---|
| Výhody | - Aktivní-aktivní podpora<br/>- Obousměrný mezi místní a Azure SQL Database | - Nižší latence<br/>- Transakční konzistence<br/>- Opětovné použití existující topologie po migraci |
| Nevýhody | - 5 minut nebo více latence<br/>- Žádná transakční konzistence<br/>- Vyšší dopad na výkon | - Nelze publikovat z Azure SQL Database jednu databázi nebo sdružené databáze<br/>- Vysoké náklady na údržbu |

## <a name="get-started-with-sql-data-sync"></a>Začínáme se synchronizací dat SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Nastavení synchronizace dat na webu Azure Portal

- [Nastavení Synchronizace dat SQL Azure](sql-database-get-started-sql-data-sync.md)
- Agent synchronizace dat – [agent synchronizace dat pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Nastavení synchronizace dat pomocí PowerShellu

- [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
- [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Projděte si doporučené postupy pro synchronizaci dat

- [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Něco se pokazilo?

- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzistentnost a výkon

### <a name="eventual-consistency"></a>Konečná konzistence

Vzhledem k tomu, že synchronizace dat je založená na aktivační události, transakční konzistence není zaručena. Společnost Microsoft zaručuje, že všechny změny jsou provedeny nakonec a že synchronizace dat nezpůsobí ztrátu dat.

### <a name="performance-impact"></a>Dopad na výkon

Synchronizace dat používá aktivační události vložení, aktualizace a odstranění ke sledování změn. Vytvoří postranní tabulky v databázi uživatelů pro sledování změn. Tyto aktivity sledování změn mají vliv na zatížení databáze. Vyhodnoťte úroveň služeb a v případě potřeby upgradujte.

Zřizování a zrušení zřizování během vytváření, aktualizace a odstraňování skupiny synchronizace může mít také vliv na výkon databáze.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Požadavky a omezení

### <a name="general-requirements"></a>Obecné požadavky

- Každá tabulka musí mít primární klíč. Neměňte hodnotu primárního klíče v libovolném řádku. Pokud máte změnit hodnotu primárního klíče, odstraňte řádek a znovu jej vytvořte s novou hodnotou primárního klíče.

> [!IMPORTANT]
> Změna hodnoty existujícího primárního klíče bude mít za následek následující chybné chování:
> - Data mezi centrem a členem mohou být ztracena, i když synchronizace nehlásí žádný problém.
> - Synchronizace může selhat, protože tabulka sledování má neexistující řádek ze zdroje z důvodu změny primárního klíče.

- Izolace snímku musí být povolená. Další informace najdete v tématu [Izolace snímku na SQL Serveru](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Obecná omezení

- Tabulka nemůže mít sloupec identity, který není primárním klíčem.
- Primární klíč nemůže mít následující datové typy: sql_variant, binární, varbinary, image, xml.
- Buďte opatrní při použití následujících datových typů jako primární klíč, protože podporovaná přesnost je pouze na druhý: čas, datetime, datetime2, datetimeoffset.
- Názvy objektů (databáze, tabulky a sloupce) nesmí obsahovat tečku tisknutelných znaků (.), levou hranatou závorku ([) nebo pravou hranatou závorku (]).
- Ověřování azure active directory není podporováno.
- Tabulky se stejným názvem, ale s různým schématem (například dbo.customers a sales.customers) nejsou podporovány.
- Sloupce s uživatelem definovanými datovými typy nejsou podporovány.

#### <a name="unsupported-data-types"></a>Nepodporované datové typy

- Filestream
- SQL/CLR UDT
- XmlSchemaCollection (podporováno XML)
- Kurzor, RowVersion, Časové razítko, Hierarchyid

#### <a name="unsupported-column-types"></a>Nepodporované typy sloupců

Synchronizace dat nemůže synchronizovat sloupce jen pro čtení nebo sloupce generované systémem. Například:

- Vypočítané sloupce.
- Systémem generované sloupce pro časové tabulky.

#### <a name="limitations-on-service-and-database-dimensions"></a>Omezení rozměrů služeb a databáze

| **Dimenze**                                                  | **Limit**              | **Řešení**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximální počet skupin synchronizace, do kterých může databáze patřit.       | 5                      |                             |
| Maximální počet koncových bodů v jedné skupině synchronizace              | 30                     |                             |
| Maximální počet místních koncových bodů v jedné skupině synchronizace. | 5                      | Vytvoření více skupin synchronizace |
| Názvy databází, tabulek, schémat u a sloupců                       | 50 znaků na název |                             |
| Tabulky v synchronizační skupině                                          | 500                    | Vytvoření více skupin synchronizace |
| Sloupce v tabulce ve skupině synchronizace                              | 1000                   |                             |
| Velikost řádku dat v tabulce                                        | 24 Mb                  |                             |
| Minimální interval synchronizace                                           | 5 minut              |                             |

> [!NOTE]
> Může být až 30 koncových bodů v jedné skupině synchronizace, pokud existuje pouze jedna skupina synchronizace. Pokud existuje více než jedna skupina synchronizace, celkový počet koncových bodů ve všech skupinách synchronizace nesmí překročit 30. Pokud databáze patří do více skupin synchronizace, počítá se jako více koncových bodů, nikoli jeden.

## <a name="faq-about-sql-data-sync"></a>Nejčastější dotazy k synchronizaci dat sql

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Kolik stojí služba SYNCHRONIZACE dat SQL

Za samotnou službu synchronizace dat SQL se neplatí žádný poplatek. Stále však shromažďujete poplatky za přenos dat pro přesun dat do a z instance databáze SQL. Další informace naleznete v tématu [sql database pricing](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Které oblasti podporují synchronizaci dat

Synchronizace dat SQL je k dispozici ve všech oblastech.

### <a name="is-a-sql-database-account-required"></a>Je vyžadován účet databáze SQL

Ano. Musíte mít účet databáze SQL pro hostování databáze hubů.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Lze pomocí synchronizace dat používat pouze k synchronizaci mezi místními databázemi serveru SQL Server.

Ne přímo. Můžete synchronizovat mezi SQL Server místní databáze nepřímo, ale vytvořením databáze Hub v Azure a potom přidáním místnídatabáze do skupiny synchronizace.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Lze pomocí synchronizace dat synchronizovat mezi databázemi SQL, které patří do různých předplatných

Ano. Můžete synchronizovat mezi databázemi SQL, které patří do skupin prostředků vlastněných různými předplatnými.

- Pokud předplatná patří do stejného klienta a máte oprávnění ke všem předplatným, můžete nakonfigurovat skupinu synchronizace na webu Azure Portal.
- V opačném případě budete muset použít PowerShell přidat členy synchronizace, které patří do různých předplatných.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Můžu pomocí synchronizace dat synchronizovat mezi databázemi SQL, které patří do různých cloudů (jako je Azure Public Cloud a Azure China 21Vianet).

Ano. Můžete synchronizovat mezi databázemi SQL, které patří do různých cloudů, budete muset použít PowerShell přidat členy synchronizace, které patří do různých předplatných.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Lze použít synchronizaci dat k osivu dat z produkční databáze do prázdné databáze a potom je synchronizovat.

Ano. Vytvořte schéma ručně v nové databázi skriptováním z originálu. Po vytvoření schématu přidejte tabulky do skupiny synchronizace, abyste data zkopírovali a udrželi synchronizované.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Mám použít SQL Data Sync k zálohování a obnovení databází

K vytvoření zálohy dat se nedoporučuje používat synchronizaci dat SQL. Nelze zálohovat a obnovit do určitého bodu v čase, protože synchronizace synchronizace dat SQL nejsou verzí. Kromě toho SQL Data Sync nezálohuje jiné objekty SQL, jako jsou uložené procedury a neprovádí ekvivalent operace obnovení rychle.

Pro jednu techniku doporučeného zálohování najdete [v tématu Kopírování databáze Azure SQL](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Může synchronizace dat synchronizovat šifrované tabulky a sloupce

- Pokud databáze používá vždy šifrované, můžete synchronizovat pouze tabulky a sloupce, které *nejsou* šifrované. Šifrované sloupce nelze synchronizovat, protože synchronizace dat nemůže data dešifrovat.
- Pokud sloupec používá šifrování na úrovni sloupce (CLE), můžete jej synchronizovat, pokud je velikost řádku menší než maximální velikost 24 Mb. Synchronizace dat považuje sloupec zašifrovaný klíčem (CLE) za normální binární data. Chcete-li dešifrovat data o ostatních členech synchronizace, musíte mít stejný certifikát.

### <a name="is-collation-supported-in-sql-data-sync"></a>Je v synchronizaci dat SQL podporováno řazení

Ano. Synchronizace dat SQL podporuje řazení v následujících scénářích:

- Pokud vybrané tabulky schématu synchronizace ještě nejsou ve vašem centru nebo v databázích členů, pak při nasazení skupiny synchronizace služba automaticky vytvoří odpovídající tabulky a sloupce s nastavením řazení vybraným v prázdných cílových databázích.
- Pokud tabulky, které mají být synchronizovány již existují v rozbočovači a členské databáze, SQL Data Sync vyžaduje, aby sloupce primárního klíče mají stejné řazení mezi hub a členské databáze úspěšně nasadit skupinu synchronizace. Neexistují žádná omezení řazení na sloupce než sloupce primárního klíče.

### <a name="is-federation-supported-in-sql-data-sync"></a>Je federace podporována v synchronizaci dat SQL

Kořenová databáze federace lze použít ve službě synchronizace dat SQL bez omezení. Koncový bod federované databáze nelze přidat do aktuální verze synchronizace dat SQL.

## <a name="next-steps"></a>Další kroky

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizace schématu synchronizované databáze

Je třeba aktualizovat schéma databáze ve skupině synchronizace? Změny schématu nejsou automaticky replikovány. Některá řešení naleznete v následujících článcích:

- [Automatizace replikace změn schématu v azure SQL data sync](sql-database-update-sync-schema.md)
- [Aktualizace schématu synchronizace ve stávající skupině synchronizace pomocí PowerShellu](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorování a odstraňování potíží

Je synchronizace dat SQL podle očekávání? Informace o aktivitě a řešení problémů naleznete v následujících článcích:

- [Monitorování synchronizace dat Azure SQL pomocí protokolů Azure Monitoru](sql-database-sync-monitor-oms.md)
- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Další informace o Azure SQL Database

Další informace o databázi SQL najdete v následujících článcích:

- [Přehled služby SQL Database](sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
