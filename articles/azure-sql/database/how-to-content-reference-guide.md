---
title: Konfigurace & Správa odkazů na obsah
description: Vyhledejte odkaz na obsah, který vás seznámí s konfigurací a správou Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791762"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Konfigurace a Správa odkazu na obsah-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto článku najdete odkaz na obsah různých průvodců, skriptů a vysvětlení, které vám pomůžou se správou a konfigurací Azure SQL Database. 

## <a name="load-data"></a>Načtení dat

- [Migrace do služby SQL Database](migrate-to-database-from-sql-server.md)
- Naučte se, jak [spravovat SQL Database po migraci](manage-data-after-migrating-to-database.md).
- [Kopírování databáze](database-copy.md)
- [Import databáze z BACPAC](database-import.md)
- [Export databáze do BACPAC](database-export.md)
- [Načítání dat pomocí BCP](../load-from-csv-with-bcp.md)
- [Načítání dat pomocí ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Konfigurace funkcí

- [Konfigurace ověřování Azure Active Directory (Azure AD)](authentication-aad-configure.md)
- [Konfigurace podmíněného přístupu](conditional-access-configure.md)
- [Vícefaktorové ověřování Azure AD](authentication-mfa-ssms-overview.md)
- [Konfigurace Multi-Factor Authentication](authentication-mfa-ssms-configure.md)
- [Konfigurace zásad dočasného uchovávání informací](temporal-tables-retention-policy.md)
- [Transparentní šifrování dat s využitím služby BYOK](transparent-data-encryption-byok-configure.md)
- [Obměna klíčů BYOK transparentního šifrování dat](transparent-data-encryption-byok-key-rotation.md)
- [Odebrání ochrany transparentního šifrování dat](transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurace OLTP v paměti](../in-memory-oltp-configure.md)
- [Konfigurace Azure Automation](automation-manage.md)
- [Nakonfigurujte transakční replikaci](replication-to-sql-database.md) pro replikaci data mezi databázemi.
- [Nakonfigurujte detekci hrozeb](threat-detection-configure.md) , abyste umožnili Azure SQL Database identifikovat podezřelé aktivity, jako je například injektáže SQL nebo přístup z podezřelých umístění.
- [Nakonfigurujte dynamické maskování dat](dynamic-data-masking-configure-portal.md) pro ochranu citlivých dat.
- [Nakonfigurujte uchovávání záloh](long-term-backup-retention-configure.md) pro databázi, abyste zachovali zálohy na Azure Blob Storage. 
- [Nakonfigurujte geografickou replikaci](active-geo-replication-overview.md) a udržujte repliku databáze v jiné oblasti.
- [Nakonfigurujte zabezpečení pro geografické repliky](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Monitorování a optimalizace databáze

- [Ruční ladění](performance-guidance.md)
- [Monitorování výkonu pomocí zobrazení dynamické správy](monitoring-with-dmvs.md)
- [Monitorování výkonu pomocí úložiště dotazů](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Povolením automatického ladění](automatic-tuning-enable.md) umožníte Azure SQL Database optimalizovat výkon svého zatížení.
- [Povolením e-mailových oznámení pro automatické ladění](automatic-tuning-email-notifications-configure.md) získáte informace o doporučeních pro ladění.
- [Použijte doporučení k výkonu](database-advisor-find-recommendations-portal.md) a optimalizujte databázi.
- [Vytvořte výstrahy](alerts-insights-configure-portal.md) , abyste získali oznámení od Azure SQL Database.
- [Řešení potíží s](troubleshoot-common-errors-issues.md) připojením, pokud si všimnete nějakých problémů s připojením mezi aplikacemi a databází. [Pro problémy s připojením](resource-health-to-troubleshoot-connectivity.md)můžete také použít Resource Health.
- [Řešení potíží s výkonem s využitím služby Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Spravujte místo v souboru](file-space-manage.md) , abyste mohli monitorovat využití úložiště ve vaší databázi.
- [Použití diagnostických protokolů Intelligent Insights](intelligent-insights-use-diagnostics-log.md)
- [Monitorování prostoru OLTP v paměti](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Rozšířené události

- [Rozšířené události](xevent-db-diff-from-svr.md)
- [Ukládat rozšířené události do souboru událostí](xevent-code-event-file.md)
- [Ukládání rozšířených událostí do kruhové vyrovnávací paměti](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Dotazování na distribuovaná data

- [Dotazování vertikálně dělených dat](elastic-query-getting-started-vertical.md) napříč více databázemi.
- [Sestava napříč datovou vrstvou s více instancemi](elastic-query-horizontal-partitioning.md)
- [Dotaz napříč tabulkami s různými schématy](elastic-query-vertical-partitioning.md)

### <a name="data-sync"></a>Synchronizace dat

- [Synchronizace dat SQL](sql-data-sync-data-sql-server-sql-database.md)
- [Agent synchronizace dat](sql-data-sync-agent-overview.md)
- [Replikace změn schématu](sql-data-sync-update-sync-schema.md)
- [Monitorování pomocí OMS](./monitor-tune-overview.md)
- [Osvědčené postupy pro synchronizaci dat](sql-data-sync-best-practices.md)
- [Řešení potíží se synchronizací dat](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Úlohy elastické databáze

- [Vytvoření a Správa](elastic-jobs-powershell-create.md) Elastic Database úlohy pomocí prostředí PowerShell.
- [Vytvoření a Správa](elastic-jobs-tsql-create-manage.md) Elastic Database úlohy pomocí jazyka Transact-SQL.
- [Migrujte ze staré elastické úlohy](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Sharding databází

- [Upgradujte klientskou knihovnu elastické databáze](elastic-scale-upgrade-client-library.md).
- [Vytvoření aplikace horizontálně dělené](elastic-scale-get-started.md)
- [Dotazování horizontálně horizontálně dělené dat](elastic-query-getting-started.md).
- Spusťte [dotazy multi-horizontálních oddílů](elastic-scale-multishard-querying.md).
- [Přesune data horizontálně dělené](elastic-scale-configure-deploy-split-and-merge.md).
- [Nakonfigurujte zabezpečení](elastic-scale-split-merge-security-configuration.md) v databázi horizontálních oddílů.
- [Přidá horizontálních oddílů](elastic-scale-add-a-shard.md) k aktuální sadě horizontálních oddílů databáze.
- [Opravte problémy s mapou horizontálních oddílů](elastic-database-recovery-manager.md).
- [Migrujte horizontálně DĚLENÉ DB](elastic-convert-to-use-elastic-tools.md).
- [Vytvořte čítače](elastic-database-perf-counters.md).
- [Použijte Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) k dotazování horizontálně dělené dat.
- [Použijte dapperem Framework](elastic-scale-working-with-dapper.md) pro dotazování dat horizontálně dělené.

## <a name="develop-applications"></a>Vývoj aplikací

- [Připojení](connect-query-content-reference-guide.md#libraries)
- [Použití konektoru Spark](spark-connector.md)
- [Ověření aplikace](application-authentication-get-client-id-keys.md)
- [Použití dávkování pro lepší výkon](../performance-improve-use-batching.md)
- [Pokyny pro připojení](troubleshoot-common-connectivity-issues.md)
- [Aliasy DNS](dns-alias-overview.md)
- [Nastavení PowerShellu aliasu DNS](dns-alias-powershell-create.md)
- [Porty – ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C a C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Návrh aplikací

- [Návrh pro zotavení po havárii](designing-cloud-solutions-for-disaster-recovery.md)
- [Návrh pro elastické fondy](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Návrh pro upgrady aplikací](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Návrh aplikací SaaS (multi-tenant software jako služba)

- [Způsoby návrhu SaaS](saas-tenancy-app-design-patterns.md)
- [Indexer videa SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpečení aplikací SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [průvodcích pro Azure SQL Managed instance](../managed-instance/how-to-content-reference-guide.md) .