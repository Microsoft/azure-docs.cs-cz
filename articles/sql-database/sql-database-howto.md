---
title: Konfigurace a správa
description: Přečtěte si, jak nakonfigurovat a spravovat Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209425"
---
# <a name="how-to-use-azure-sql-database"></a>Jak používat Azure SQL Database

V této části najdete různé příručky, skripty a vysvětlení, které vám pomůžou spravovat a konfigurovat azure SQL database. Můžete také najít konkrétní návody pro [jednu databázi](sql-database-howto-single-database.md) a [spravovanou instanci](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Načtení dat

- [Kopírování jedné databáze nebo sdružené databáze v rámci Azure](sql-database-copy.md)
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
- [Monitorování výkonu pomocí úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Řešení potíží s výkonem s využitím služby Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Použití diagnostických protokolů Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorování prostoru OLTP v paměti](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Rozšířené události

- [Rozšířené události](sql-database-xevent-db-diff-from-svr.md)
- [Uložit rozšířené události do souboru událostí](sql-database-xevent-code-event-file.md)
- [Uložit rozšířené události do kruhové vyrovnávací paměti](sql-database-xevent-code-ring-buffer.md)

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
- [Konfigurace automatizace Azure](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Vývoj aplikací

- [Připojení](sql-database-libraries.md)
- [Použití konektoru Spark](sql-database-spark-connector.md)
- [Ověřit aplikaci](sql-database-client-id-keys.md)
- [Použití dávkování pro lepší výkon](sql-database-use-batching-to-improve-performance.md)
- [Pokyny pro připojení](sql-database-connectivity-issues.md)
- [Aliasy DNS](dns-alias-overview.md)
- [Nastavení aliasu DNS PowerShell](dns-alias-powershell.md)
- [Porty – ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C a C ++](sql-database-develop-cplusplus-simple.md)
- [Aplikace excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Návrh aplikací

- [Návrh pro zotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Návrh pro elastické fondy](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Návrh pro upgrady aplikací](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Návrh víceklientských aplikací SaaS

- [Způsoby návrhu SaaS](saas-tenancy-app-design-patterns.md)
- [Indexer videa SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpečení aplikací SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [postupech pro spravované instance](sql-database-howto-managed-instance.md).
- Další informace o [postupech pro jednotlivé databáze](sql-database-howto-single-database.md).
