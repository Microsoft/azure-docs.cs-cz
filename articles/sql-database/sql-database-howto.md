---
title: Jak nakonfigurovat databázi SQL Azure | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat a spravovat Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: f63f929f21d4df06e409784e2ccab611e951a1bc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477705"
---
# <a name="how-to-use-azure-sql-database"></a>Jak používat Azure SQL Database

V této části najdete různé průvodce, skripty a vysvětlení, které vám mohou pomoci při správě a konfiguraci Azure SQL Database. Můžete také vyhledat konkrétní návody pro [izolované databáze](sql-database-howto-single-database.md) a [Managed Instance](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Načtení dat

- [Zkopírujte izolovanou databázi nebo databázi ve fondu v rámci Azure](sql-database-copy.md)
- [Import databáze ze souboru BACPAC](sql-database-import.md)
- [Export databáze do souboru BACPAC](sql-database-export.md)
- [Načítání dat pomocí BCP](sql-database-load-from-csv-with-bcp.md)
- [Načítání dat pomocí ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Synchronizace dat

- [SQL Data Sync](sql-database-sync-data.md)
- [Agent synchronizace dat](sql-database-data-sync-agent.md)
- [Replikovat změny schématu](sql-database-update-sync-schema.md)
- [Monitorování pomocí OMS](sql-database-sync-monitor-oms.md)
- [Osvědčené postupy pro synchronizaci dat](sql-database-best-practices-data-sync.md)
- [Řešení potíží s synchronizace dat](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Sledování a ladění

- [Ruční ladění](sql-database-performance-guidance.md)
- [Monitorování výkonu pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md)
- [Monitorování výkonu pomocí úložiště dotazů](sql-database-operate-query-store.md)
- [Řešení potíží s výkonem Intelligent insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Použít protokol diagnostiky inteligentních přehledů](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorování místa na OLTP v paměti](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Rozšířené události

- [Rozšířené události](sql-database-xevent-db-diff-from-svr.md)
- [Store rozšířených událostí do souboru události](sql-database-xevent-code-event-file.md)
- [Store rozšířených událostí do vyrovnávací paměti aktualizační kanál](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Konfigurace funkcí

- [Konfigurace ověřování Azure AD](sql-database-aad-authentication-configure.md)
- [Konfigurace podmíněného přístupu](sql-database-conditional-access.md)
- [Vícefaktorové ověřování AAD](sql-database-ssms-mfa-authentication.md)
- [Konfigurace ověřování službou Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md)
- [Konfigurace zásad pro dočasné uchovávání informací](sql-database-temporal-tables-retention-policy.md)
- [Nakonfigurovat transparentní šifrování dat pomocí funkce BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Obměna klíčů BYOK transparentní šifrování dat](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Odebrat ochrana TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Konfigurace OLTP v paměti](sql-database-in-memory-oltp-migration.md)
- [Konfigurace služby Azure Automation](sql-database-manage-automation.md)

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
