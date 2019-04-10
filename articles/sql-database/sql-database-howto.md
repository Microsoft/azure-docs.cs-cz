---
title: Jak nakonfigurovat databázi SQL Azure | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat a spravovat Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7db9c6400ac7d235153a59965e34e30d9b809a81
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359690"
---
# <a name="how-to-use-azure-sql-database"></a>Jak používat Azure SQL Database

V této části najdete různé průvodce, skripty a vysvětlení, které vám mohou pomoci při správě a konfiguraci Azure SQL Database. Můžete také vyhledat konkrétní návody pro [izolované databáze](sql-database-howto-single-database.md) a [Managed Instance](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Načtení dat

- [Zkopírujte izolovanou databázi nebo databázi ve fondu v rámci Azure](sql-database-copy.md)
- [Import databáze z BACPAC](sql-database-import.md)
- [Export databáze do BACPAC](sql-database-export.md)
- [Načítání dat pomocí BCP](sql-database-load-from-csv-with-bcp.md)
- [Načítání dat pomocí ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Synchronizace dat

- [Synchronizace dat SQL](sql-database-sync-data.md)
- [Agent synchronizace dat](sql-database-data-sync-agent.md)
- [Replikace změn schématu](sql-database-update-sync-schema.md)
- [Monitorování pomocí OMS](sql-database-sync-monitor-oms.md)
- [Osvědčené postupy pro synchronizaci dat](sql-database-best-practices-data-sync.md)
- [Řešení potíží se synchronizací dat](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Sledování a ladění

- [Ruční ladění](sql-database-performance-guidance.md)
- [Monitorování výkonu pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md)
- [Monitorování výkonu pomocí úložiště dotazů](sql-database-operate-query-store.md)
- [Řešení potíží s výkonem s využitím služby Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Použití diagnostických protokolů Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorování prostoru OLTP v paměti](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Rozšířené události

- [Rozšířené události](sql-database-xevent-db-diff-from-svr.md)
- [Store rozšířených událostí do souboru události](sql-database-xevent-code-event-file.md)
- [Store rozšířených událostí do vyrovnávací paměti aktualizační kanál](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurace funkcí

- [Konfigurace ověřování Azure AD](sql-database-aad-authentication-configure.md)
- [Konfigurace podmíněného přístupu](sql-database-conditional-access.md)
- [Vícefaktorové ověřování AAD](sql-database-ssms-mfa-authentication.md)
- [Konfigurace vícefaktorového ověřování](sql-database-ssms-mfa-authentication-configure.md)
- [Konfigurace zásad dočasného uchovávání informací](sql-database-temporal-tables-retention-policy.md)
- [Transparentní šifrování dat s využitím služby BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Obměna klíčů BYOK transparentního šifrování dat](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Odebrání ochrany transparentního šifrování dat](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurace OLTP v paměti](sql-database-in-memory-oltp-migration.md)
- [Konfigurovat Azure Automation](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Vývoj aplikací

- [Připojení](sql-database-libraries.md)
- [Použití konektoru Spark](sql-database-spark-connector.md)
- [Ověření aplikace](sql-database-client-id-keys.md)
- [Chybové zprávy](sql-database-develop-error-messages.md)
- [Použití dávkování pro zajištění lepšího výkonu](sql-database-use-batching-to-improve-performance.md)
- [Pokyny pro připojení](sql-database-connectivity-issues.md)
- [Aliasy DNS](dns-alias-overview.md)
- [Nastavení DNS alias prostředí PowerShell](dns-alias-powershell.md)
- [Porty – ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C a C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Návrh aplikací

- [Návrh pro zotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Návrh pro elastické fondy](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Návrh pro upgrady aplikací](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Návrh aplikací SaaS s více tenanty

- [Způsoby návrhu SaaS](saas-tenancy-app-design-patterns.md)
- [Indexer videa SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpečení aplikací SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Další postup

- Další informace o [postupy příručky pro spravované instance](sql-database-howto-managed-instance.md).
- Další informace o [provede postupy pro izolované databáze](sql-database-howto-single-database.md).
