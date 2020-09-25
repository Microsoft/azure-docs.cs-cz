---
title: Konfigurace & Správa odkazů na obsah
titleSuffix: Azure SQL Managed Instance
description: Referenční příručka obsahu, která vás učí, jak nakonfigurovat a spravovat spravovanou instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: 332b5198f046d0096e95e79e27cbc49408e6d136
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325194"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Referenční dokumentace obsahu spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto článku najdete odkaz na obsah různých průvodců, skriptů a vysvětlení, které vám pomůžou se správou a konfigurací spravované instance Azure SQL.

## <a name="load-data"></a>Načtení dat

- [Migrace do spravované instance Azure SQL](migrate-to-instance-from-sql-server.md): Přečtěte si o doporučeném procesu migrace a nástrojích pro migraci do spravované instance Azure SQL.
- [Migrace certifikátu TDE do spravované instance Azure SQL](tde-certificate-migrate.md): Pokud je vaše databáze SQL Server chráněná pomocí transparentního šifrování dat (TDE), budete muset migrovat certifikát, který spravovaná instance SQL může použít k dešifrování zálohy, kterou chcete obnovit v Azure.
- [Import databáze z BACPAC](../database/database-import.md)
- [Export databáze do BACPAC](../database/database-export.md)
- [Načítání dat pomocí BCP](../load-from-csv-with-bcp.md)
- [Načtení dat pomocí služby Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Konfigurace sítě

- [Určení velikosti podsítě](vnet-subnet-determine-size.md): vzhledem k tomu, že se po nasazení spravované instance SQL nedá změnit velikost podsítě, je potřeba vypočítat rozsah IP adres, které se vyžadují pro počet a typy spravovaných instancí, které plánujete nasadit do podsítě. 
- [Vytvoření nové virtuální sítě a podsítě](virtual-network-subnet-create-arm-template.md): Nakonfigurujte virtuální síť a podsíť podle [požadavků sítě](connectivity-architecture-overview.md#network-requirements). 
- [Konfigurace existující virtuální sítě a podsítě](vnet-existing-add-subnet.md): ověřte požadavky sítě a nakonfigurujte stávající virtuální síť a podsíť pro nasazení spravované instance SQL. 
- [Konfigurace vlastního DNS](custom-dns-configure.md): Konfigurace vlastního serveru DNS pro udělení přístupu externích prostředků k vlastním doménám ze spravované instance SQL prostřednictvím odkazovaného serveru e-mailových profilů databáze. 
- [Synchronizace konfigurace sítě](azure-app-sync-network-configuration.md): Pokud nemůžete navázat připojení po [integraci aplikace s virtuální sítí Azure](../../app-service/web-sites-integrate-with-vnet.md), aktualizujte plán konfigurace sítě.
- [Najít IP adresu koncového bodu správy](management-endpoint-find-ip-address.md): Určete veřejný koncový bod, který spravovaná instance SQL používá pro účely správy. 
- [Ověření integrované ochrany brány firewall](management-endpoint-verify-built-in-firewall.md): Ověřte, jestli je v systému SQL Managed instance povoleno provoz pouze v nezbytných portech a dalších integrovaných pravidlech brány firewall. 
- [Připojit aplikace](connect-application-instance.md): Seznamte se s různými vzory pro připojení aplikací k spravované instanci SQL.

## <a name="feature-configuration"></a>Konfigurace funkcí

- [Konfigurace ověřování Azure AD](../database/authentication-aad-configure.md)
- [Konfigurace podmíněného přístupu](../database/conditional-access-configure.md)
- [Vícefaktorové ověřování Azure AD](../database/authentication-mfa-ssms-overview.md)
- [Konfigurace vícefaktorového ověřování](../database/authentication-mfa-ssms-configure.md)
- [Konfigurace dočasné zásady uchovávání informací](../database/temporal-tables-retention-policy.md)
- [Transparentní šifrování dat s využitím služby BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Obměna klíčů BYOK transparentního šifrování dat](../database/transparent-data-encryption-byok-key-rotation.md)
- [Odebrání ochrany TDE](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurace OLTP v paměti](../in-memory-oltp-configure.md)
- [Konfigurace Azure Automation](../database/automation-manage.md)
- [Transakční replikace](replication-between-two-instances-configure-tutorial.md) umožňuje replikovat data mezi spravovanými instancemi nebo z SQL Server místně do SQL Managed instance a naopak.
- [Konfigurace detekce hrozeb](threat-detection-configure.md) – [detekce hrozeb](../database/threat-detection-overview.md) je integrovaná funkce spravované instance Azure SQL, která detekuje různé potenciální útoky, jako je například vkládání SQL nebo přístup z podezřelých umístění. 
- [Vytváření výstrah](alerts-create.md) vám umožní nastavit výstrahy pro monitorované metriky, jako je využití procesoru, spotřeba prostoru úložiště, IOPS a další pro SPRAVOVANOU instanci SQL. 

## <a name="monitoring-and-tuning"></a>Sledování a ladění

- [Ruční ladění](../database/performance-guidance.md)
- [Monitorování výkonu pomocí zobrazení dynamické správy](../database/monitoring-with-dmvs.md)
- [Monitorování výkonu pomocí úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Řešení potíží s výkonem s využitím služby Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Použití diagnostického protokolu Intelligent Insights](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitorování OLTP místa v paměti](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Rozšířené události

- [Rozšířené události](../database/xevent-db-diff-from-svr.md)
- [Ukládání rozšířených událostí do souboru události](../database/xevent-code-event-file.md)
- [Ukládání rozšířených událostí do kruhové vyrovnávací paměti](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Zobrazení výstrah

- [Vytváření upozornění pro spravovanou instanci](alerts-create.md)

## <a name="operations"></a>Operace

- [Uživatelsky iniciované ruční převzetí služeb při selhání na spravované instanci SQL](user-initiated-failover.md)

## <a name="develop-applications"></a>Vývoj aplikací

- [Připojení](../database/connect-query-content-reference-guide.md#libraries)
- [Použití konektoru Spark](../../cosmos-db/spark-connector.md)
- [Ověření aplikace](../database/application-authentication-get-client-id-keys.md)
- [Použití dávkování pro lepší výkon](../performance-improve-use-batching.md)
- [Pokyny pro připojení](../database/troubleshoot-common-connectivity-issues.md)
- [Aliasy DNS](../database/dns-alias-overview.md)
- [Nastavení aliasu DNS pomocí PowerShellu](../database/dns-alias-powershell-create.md)
- [Porty – ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C a C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Návrh aplikací

- [Návrh pro zotavení po havárii](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Návrh pro elastické fondy](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Návrh pro upgrady aplikací](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Návrh aplikací SaaS pro více tenantů

- [Způsoby návrhu SaaS](../database/saas-tenancy-app-design-patterns.md)
- [Indexer videa SaaS](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Zabezpečení aplikací SaaS](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Další kroky

Začněte tím, že [nasadíte spravovanou instanci SQL](instance-create-quickstart.md).
