---
title: Konfigurace & Správa odkazů na obsah
titleSuffix: Azure SQL Managed Instance
description: Referenční příručka obsahu, která vás učí, jak nakonfigurovat a spravovat spravovanou instanci Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: bc0c30e234310a4ee1d013d7a11ca556edd071c6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041113"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Referenční dokumentace obsahu spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto článku najdete odkaz na obsah různých průvodců, skriptů a vysvětlení, které vám pomůžou se správou a konfigurací spravované instance Azure SQL.

## <a name="load-data"></a>Načtení dat

- [Migrace na spravovanou instanci Azure SQL](migrate-to-instance-from-sql-server.md) – Přečtěte si o doporučeném procesu migrace a nástrojích pro migraci do spravované instance Azure SQL.
- [Migrace TDE certifikátu na spravovanou instanci Azure SQL](tde-certificate-migrate.md) – Pokud je vaše databáze SQL Server chráněná pomocí transparentního šifrování dat (TDE), budete muset migrovat certifikát, který může spravovaná instance SQL použít k dešifrování zálohy, kterou chcete obnovit v Azure.
- [Import databáze z BACPAC](../database/database-import.md)
- [Export databáze do BACPAC](../database/database-export.md)
- [Načítání dat pomocí BCP](../load-from-csv-with-bcp.md)
- [Načítání dat pomocí ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Konfigurace sítě

- [Určení velikosti podsítě](vnet-subnet-determine-size.md) Vzhledem k tomu, že se po nasazení spravované instance SQL nedá velikost podsítě změnit, je potřeba vypočítat rozsah IP adres, které se vyžadují pro počet a typy spravovaných instancí SQL, které plánujete nasadit do podsítě. 
- [Vytvořit novou virtuální síť a podsíť](virtual-network-subnet-create-arm-template.md) Nakonfigurujte virtuální síť a podsíť podle [požadavků sítě popsaných tady](connectivity-architecture-overview.md#network-requirements) . 
- [Konfigurace existující virtuální sítě a podsítě](vnet-existing-add-subnet.md) Ověřte požadavky sítě a nakonfigurujte stávající virtuální síť a podsíť pro nasazení spravované instance SQL. 
- [Konfigurace vlastního DNS](custom-dns-configure.md) Konfigurace vlastního serveru DNS pro udělení přístupu externích prostředků k vlastním doménám ze spravované instance SQL prostřednictvím propojeného serveru e-mailových profilů databáze. 
- [Synchronizace konfigurace sítě](azure-app-sync-network-configuration.md) Aktualizujte plán konfigurace sítě, pokud po [integraci aplikace do Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md) nemůžete navázat připojení.
- [Najít IP adresu koncového bodu správy](management-endpoint-find-ip-address.md) Určení veřejného koncového bodu, který spravovaná instance SQL používá pro účely správy. 
- [Ověření integrované ochrany brány firewall](management-endpoint-verify-built-in-firewall.md) Ověřte, zda spravovaná instance SQL umožňuje provoz pouze v nezbytných portech a dalších integrovaných pravidlech brány firewall. 
- [Připojit aplikace](connect-application-instance.md) Přečtěte si o různých vzorech pro připojení aplikací ke spravované instanci SQL.

## <a name="feature-configuration"></a>Konfigurace funkcí

- [Konfigurace ověřování Azure AD](../database/authentication-aad-configure.md)
- [Konfigurace podmíněného přístupu](../database/conditional-access-configure.md)
- [Vícefaktorové ověřování AAD](../database/authentication-mfa-ssms-overview.md)
- [Konfigurace vícefaktorového ověřování](../database/authentication-mfa-ssms-configure.md)
- [Konfigurace zásad dočasného uchovávání informací](../database/temporal-tables-retention-policy.md)
- [Transparentní šifrování dat s využitím služby BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Obměna klíčů BYOK transparentního šifrování dat](../database/transparent-data-encryption-byok-key-rotation.md)
- [Odebrání ochrany transparentního šifrování dat](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurace OLTP v paměti](../in-memory-oltp-configure.md)
- [Konfigurace Azure Automation](../database/automation-manage.md)
- [Transakční replikace](replication-between-two-instances-configure-tutorial.md) umožňuje replikovat data mezi spravovanými instancemi Azure SQL nebo z místních SQL Server do spravované instance SQL a naopak.
- [Konfigurace detekce hrozeb](threat-detection-configure.md) – [detekce hrozeb](../database/threat-detection-overview.md) je integrovaná funkce spravované instance Azure SQL, která detekuje různé potenciální útoky, jako je například vkládání SQL nebo přístup z podezřelých umístění. 
- [Vytváření výstrah](alerts-create.md) vám umožní nastavit upozornění na monitorované metriky, jako je využití procesoru, spotřeba úložiště, IOPS a další pro SPRAVOVANOU instanci SQL. 

## <a name="monitoring-and-tuning"></a>Sledování a ladění

- [Ruční ladění](../database/performance-guidance.md)
- [Monitorování výkonu pomocí zobrazení dynamické správy](../database/monitoring-with-dmvs.md)
- [Monitorování výkonu pomocí úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Řešení potíží s výkonem s využitím služby Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Použití diagnostických protokolů Intelligent Insights](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitorování prostoru OLTP v paměti](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Rozšířené události

- [Rozšířené události](../database/xevent-db-diff-from-svr.md)
- [Ukládat rozšířené události do souboru událostí](../database/xevent-code-event-file.md)
- [Ukládání rozšířených událostí do kruhové vyrovnávací paměti](../database/xevent-code-ring-buffer.md)

## <a name="develop-applications"></a>Vývoj aplikací

- [Připojení](../database/connect-query-content-reference-guide.md#libraries)
- [Použití konektoru Spark](../../cosmos-db/spark-connector.md)
- [Ověření aplikace](../database/application-authentication-get-client-id-keys.md)
- [Použití dávkování pro lepší výkon](../performance-improve-use-batching.md)
- [Pokyny pro připojení](../database/troubleshoot-common-connectivity-issues.md)
- [Aliasy DNS](../database/dns-alias-overview.md)
- [Nastavení PowerShellu aliasu DNS](../database/dns-alias-powershell-create.md)
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

Začněte tím, že [nasadíte spravovanou instanci SQL](instance-create-quickstart.md) .
