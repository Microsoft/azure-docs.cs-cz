---
title: Zpráva k vydání verze Azure SQL Database | Dokumentace Microsoftu
description: Další informace o nové funkce a vylepšení ve službě Azure SQL Database a v dokumentaci k Azure SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: 04c19c9a8603fee46914eb94b50c4f97e34e9c42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070219"
---
# <a name="sql-database-release-notes"></a>Zpráva k vydání verze SQL Database

Tento článek obsahuje seznam nových funkcí a vylepšení ve službě SQL Database a v dokumentaci k SQL Database. Vylepšení služby SQL Database, najdete v článku také [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Vylepšení k jiným službám Azure, najdete v části [aktualizací služby](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkce ve verzi public preview

| Funkce | Podrobnosti |
| ---| --- |
| Obnovení databáze akcelerovanými pomocí izolovaných databází a elastických fondů | Informace najdete v tématu [zapnuto zrychlené obnovení databáze](sql-database-accelerated-database-recovery.md).|
|Přibližný počet jedinečných|Informace najdete v tématu [přibližné Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Režim služby batch na Rowstore (pod úroveň kompatibility 150)|Informace najdete v tématu [režimu služby Batch na Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Zjišťování a klasifikace dat  |Informace najdete v tématu [Azure SQL Database a SQL Data Warehouse zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md).|
| Úlohy elastické databáze | Informace najdete v tématu [vytvoření, konfigurace a správa elastických úloh](elastic-jobs-overview.md). |
| Elastické dotazy | Informace najdete v tématu [elastický dotaz přehled](sql-database-elastic-query-overview.md). |
| Elastické transakce | [Distribuované transakce v cloudových databázích](sql-database-elastic-transactions-overview.md). |
|Paměť zpětnou vazbu přidělení (řádek v režimu) (pod úroveň kompatibility 150)|Informace najdete v tématu [zpětnou vazbu přidělení paměti (řádek v režimu)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor dotazů na webu Azure Portal |Informace najdete v tématu [připojení a dotazování dat pomocí editoru dotazů SQL webu Azure portal](sql-database-connect-query-portal.md).|
| R services / machine learning s izolovaných databází a elastických fondů |Informace najdete v tématu [služby Machine Learning v Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Znovu vytvořte vynechaných databází s spravované instance |Informace najdete v tématu [znovu vytvořit vyřadit databází ve spravované instanci SQL Azure](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replikace se spravované instance |Informace najdete v tématu [konfigurace replikace databáze spravované instance Azure SQL Database](replication-with-sql-database-managed-instance.md).|
| Bezserverová výpočetní úroveň | Informace najdete v tématu [bez serveru SQL Database (preview)](sql-database-serverless.md).|
|SQL Analytics|Informace najdete v tématu [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Tabulka proměnných odložené kompilace (pod úroveň kompatibility 150)|Informace najdete v tématu [kompilace odložené proměnné tabulky](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Detekce hrozeb spravované instance |Informace najdete v tématu [konfigurovat detekce hrozeb ve službě Azure SQL Database managed instance](sql-database-managed-instance-threat-detection.md).|
| Transparentní šifrování dat (TDE) s přenést Your Own Key (BYOK) pomocí spravované instance |Informace najdete v tématu [Azure SQL transparentního šifrování dat pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault: Your Own Key podpoře](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="june-2019"></a>2019 dne

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| 4 virtuální jádra managed instance na generace hardwaru Gen5| Další informace naleznete v tématu https://aka.ms/four-cores-sql-mi-update. |
| Spravovaná Instance – podpora pro předplatná s měsíční kredit Azure pro předplatitele sady Visual Studio | Další informace naleznete v tématu https://aka.ms/sql-mi-visual-studio-subscribers. |
|Větší místní omezení pro spravovanou instanci| Další informace najdete v tématu [omezení místních prostředků](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
| &nbsp; |


## <a name="may-2019"></a>. Května 2019.

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
|Úroveň služby Hyperškálovatelného dočkalo všeobecné dostupnosti| Další informace najdete v tématu [Hyperškálovatelného vrstvy služby pro až 100 TB](sql-database-service-tier-hyperscale.md) a [získejte výkonné škálování úloh Azure database s Hyperškálováním](https://azure.microsoft.com/blog/get-high-performance-scaling-for-your-azure-database-workloads-with-hyperscale/).|
|Úroveň výpočetní prostředí vydané ve veřejné verzi preview | Další informace najdete v tématu [bez serveru SQL Database (preview)](sql-database-serverless.md).|
|Podpora časové pásmo pro spravované instance dočkala všeobecné dostupnosti |Další informace najdete v tématu [časové pásmo v Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md).|
|Kolací instance se dočkala všeobecné dostupnosti spravované instance |Informace najdete v tématu [vytvoření spravované instance Azure SQL Database pomocí Powershellu pomocí šablony Azure Resource Manageru](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md).|
| Pro databáze pomocí nákupní model založený na virtuálních jádrech zvýšit rychlost protokol transakcí a cíl vstupně-výstupních operací| Další informace najdete v tématu [prostředků omezuje pro izolované databáze pomocí nákupní model založený na virtuálních jádrech](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) a [prostředků omezuje pro izolované databáze pomocí nákupní model založený na DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases).
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
| Velkokapacitní služby úrovně dokumentace aktualizované verze GA| Další informace najdete v tématu [Hyperškálovatelného vrstvy služby pro až 100 TB](sql-database-service-tier-hyperscale.md).|
|Dokumentace úroveň výpočetní prostředí vydané s verzí public preview| Další informace najdete v tématu [bez serveru SQL Database (preview)](sql-database-serverless.md).|
| &nbsp; |

## <a name="april-2019"></a>2019\. dubna

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| Veřejné koncové body pro spravovanou instanci ve verzi public preview| Další informace najdete v tématu [pomocí Azure SQL Database managed instance bezpečně s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).|
| Podpora časové pásmo pro managed instance ve verzi public preview| Další informace najdete v tématu [časové pásmo v Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md).|
| Zabezpečení databází Azure SQL Database pomocí spravované identity ve veřejné verzi preview druhé vydání| Zobrazit [zabezpečení databází Azure SQL Database pomocí spravované identity zjednodušila](https://azure.microsoft.com/blog/securing-azure-sql-databases-with-managed-identities-just-got-easier/).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
| Veřejné koncové body pro spravovanou instanci ve verzi public preview| Další informace najdete v tématu [pomocí Azure SQL Database managed instance bezpečně s veřejným koncovým bodem](sql-database-managed-instance-public-endpoint-securely.md).|
| Podpora časové pásmo pro managed instance ve verzi public preview| Další informace najdete v tématu [časové pásmo v Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md). |
| Zásady správného řízení prostředků ve službě Azure SQL Database | Další informace najdete v tématu [zásady správného řízení prostředků ve službě Azure SQL Database](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). || &nbsp; |

## <a name="march-2019"></a>2019\. března

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| Podpora škálování pro čtení pro službu Azure SQL Database se dočkalo všeobecné dostupnosti | Další informace najdete v tématu [horizontální navýšení kapacity pro čtení](sql-database-read-scale-out.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
| Přidání protokolu limity pro izolované databáze|Další informace najdete v tématu [jedno omezení prostředků vCore databáze](sql-database-vcore-resource-limits-single-databases.md).|
| Přidání protokolu limity pro elastické fondy a databáze ve fondu|Další informace najdete v tématu [vCore prostředků omezení Elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).|
| Přidání transakce protokolu míra zásad správného řízení| Přidat nový obsah pro [transakční protokol míra zásad správného řízení](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Aktualizace ukázky Powershellu pro izolované databáze a elastických fondů použít az.sql modul | Další informace najdete v tématu [ukázky Powershellu pro izolované databáze a elastické fondy](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>. Února 2019

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
|Vytváření obnovitelného indexu online je teď obecně dostupná| Další informace najdete v tématu [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Spravovaná instance podporu pro směrovací tabulky vylepšené| Další informace najdete v tématu [požadavky na síťovou](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Přejmenování databáze nepodporuje spravované instance | Další podrobnosti najdete v tématu [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) a [proceduru sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) syntaxe.|
|Databáze SQL jako zdroje referenčních dat pro Stream Analytics. | Další informace najdete v tématu [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) a [Azure Stream Analytics teď podporuje Azure SQL Database jako referenčního datového vstupu](https://azure.microsoft.com/blog/azure-stream-analytics-now-supports-azure-sql-database-as-reference-data-input/).|
|Data Migration Assistant přidává podporu pro spravovanou instanci. |Další informace najdete v tématu [co je nového v DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|SQL Server Pomocníka s migrací přidává podporu pro vyhodnocení připravenosti na cíl pro spravovanou instanci. | Další informace najdete v tématu [SQL Server Pomocníka s migrací](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Služby dat migrace podporuje migraci z Amazon vzdálené plochy do spravované instance | Další informace najdete v tématu [kurzu: Migrace vzdálené plochy SQL serveru do Azure SQL Database nebo spravované instance Azure SQL Database online pomocí DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
|Přidání managed instance vyjasnění možnost nasazení|Aktualizovat řada článků o vysvětlení použitelnost pro izolované databáze, elastický fond a možnosti nasazení spravované instance. |
|Aktualizace databáze tempdb velikosti nákupní model založený na DTU | Další informace najdete v tématu [databáze Tempdb ve službě SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Aktualizované import a export souboru bacpac pro podporu spravované instance| Další informace najdete v tématu [importovat ze souboru BACPAC](sql-database-import.md) a [exportovat do souboru BACPAC](sql-database-export.md). |
| &nbsp; |

## <a name="january-2019"></a>2019\. ledna

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
| Členitost další možnosti pro výpočetní prostředky | Úrovně u služeb pro obecné účely a pro důležité obchodní informace [izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) nyní obsahují další možnosti podrobné výpočetní prostředky.|
| Zobrazení záznamů auditu pro spravovanou instanci na webu Azure Portal | Zobrazení [záznamy pro spravované instance auditu](sql-database-managed-instance-auditing.md) ve službě Azure portal teď podporuje.|
| Funkce detekce hrozeb zálohy přejmenován na Pokročilé zabezpečení dat | Funkce detekce hrozeb zálohy přejmenován na [rozšířené zabezpečení dat](sql-advanced-threat-protection.md) pro izolované databáze, elastické fondy a spravované instance. |
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
| Spravované instance a transakční replikace | Přidání článku o používání [transakční replikace s spravované instance](replication-with-sql-database-managed-instance.md). |
| Přidání služby Azure AD s kurzem spravované instance | To [Azure AD s spravovanou instanci](sql-database-managed-instance-aad-security-tutorial.md) kurzu se dozvíte budete muset nakonfigurovat a otestovat managed instance zabezpečení pomocí přihlášení Azure AD. |
| Aktualizovaný obsah pro automatizaci úloh pomocí skriptů příkazů jazyka Transact-SQL | Aktualizovat a vyjasnění obsah pro používání [úlohy automatizace s použitím příkazů jazyka Transact-SQL skriptů](sql-database-job-automation-overview.md) pro izolované databáze, elastické fondy a spravované instance. |
| Zabezpečení obsahu pro aktualizaci spravované instance | Aktualizovat a vyjasnění obsah [model zabezpečení pro spravované instance](sql-database-security-overview.md)a kontrastní pomocí model zabezpečení pro izolované databáze a elastické fondy. |
| Aktualizovat všechny rychlých startů a kurzů | Všechny rychlých startů a kurzů v [dokumentaci](https://docs.microsoft.com/azure/sql-database) byly aktualizovány a jsme aktualizovali tak, že se shodují se změnami na webu Azure Portal. |
| Přehled příručky přidání rychlý start | Přidání úvodní Přehled příručky pro [izolované databáze](sql-database-quickstart-guide.md) a [spravované instance](sql-database-managed-instance-quickstart-guide.md). |
| Přidání databáze SQL Glosář termínů | To [termíny glosáře](sql-database-glossary-terms.md) článek obsahuje konečný seznam podmínek SQL Database a odkazy na primární konceptuální stránky, která vysvětluje výraz v kontextu. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Přispívat k obsahu vylepšení

Sada dokumentace k Azure SQL je open source. Práce v otevřeném prostředí poskytuje několik výhod:

- Plánování úložiště opensourcových umožňuje získávat zpětnou vazbu o tom, které dokumenty jsou nejvíce žádané.
- Úložiště opensourcových zkontrolujte v publikovat nejužitečnější obsah naší nové verzi.
- Otevřít zdroj úložiště aktualizovat v programu open, aby bylo snazší průběžné vylepšování obsahu.

Abyste mohli přispívat k obsahu dokumentace ke službě Azure SQL Database, najdete v článku [Přehled Průvodce pro přispěvatele Microsoftu Docs](https://docs.microsoft.com/contribute/). Možnosti uživatele na [docs.microsoft.com](https://docs.microsoft.com/) integruje [Githubu](https://github.com/) pracovní postupy přímo na to bylo ještě jednodušší. Začněte tím, že [úpravami dokumentu, který si prohlížíte](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Případně můžete pomoct s [revizemi nových témat](https://docs.microsoft.com/contribute/#review-open-prs), nebo [vytvářením užitečných položek problémů](https://docs.microsoft.com/contribute/#create-quality-issues).
