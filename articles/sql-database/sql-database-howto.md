---
title: Jak nakonfigurovat databázi SQL Azure | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat a spravovat Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440503"
---
# <a name="how-to-use-azure-sql-database"></a>Jak používat Azure SQL Database

V této části najdete různé průvodce, skripty a vysvětlení, které vám mohou pomoci při správě a konfiguraci Azure SQL Database. Můžete také vyhledat konkrétní návody pro [izolované databáze](sql-database-howto-single-database.md) a [Managed Instance](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Načtení dat

- [Zkopírujte izolované databáze v rámci Azure](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Import databáze ze souboru BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Export databáze do souboru BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Načítání dat pomocí BCP](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Načítání dat pomocí ADF](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Synchronizace dat

- [Synchronizace dat SQL](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Agent synchronizace dat](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Replikovat změny schématu](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Monitorování pomocí OMS](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Osvědčené postupy pro synchronizaci dat](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Řešení potíží s synchronizace dat](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Sledování a ladění

-  [Ruční ladění](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Monitorování výkonu pomocí zobrazení dynamické správy](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Monitorování výkonu pomocí úložiště dotazů](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Řešení potíží s výkonem Intelligent insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Použít protokol diagnostiky inteligentních přehledů](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Monitorování místa na OLTP v paměti](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Rozšířené události

- [Rozšířené události](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Store rozšířených událostí do souboru události](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Store rozšířených událostí do vyrovnávací paměti aktualizační kanál](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Konfigurace funkcí

- [Konfigurace ověřování Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Konfigurace podmíněného přístupu](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Vícefaktorové ověřování AAD](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Konfigurace ověřování službou Multi-Factor Authentication](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Konfigurace zásad pro dočasné uchovávání informací](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [Nakonfigurovat transparentní šifrování dat pomocí funkce BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [Obměna klíčů BYOK transparentní šifrování dat](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Odebrat ochrana TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Konfigurace OLTP v paměti](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Konfigurace služby Azure Automation](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Vývoj aplikací

- [Připojení](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Použití konektoru Spark](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Ověření aplikace](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Chybové zprávy](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [Použití dávkování pro zajištění lepšího výkonu](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Pokyny pro připojení](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [Aliasy DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [Nastavení DNS alias prostředí PowerShell](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Porty – ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C a C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Návrh aplikací

- [Návrh pro zotavení po havárii](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Návrh pro elastické fondy](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Návrh pro upgrady aplikací](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Návrh aplikací SaaS s více tenanty

- [Způsoby návrhu SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [Indexer videa SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [Zabezpečení aplikací SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>Další postup
- Další informace o [postupy provede ve spravované instanci](sql-database-howto-managed-instance.md).
- Další informace o [postupy provede v izolované databáze](sql-database-howto-single-database.md).
