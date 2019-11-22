---
title: Poznámky k verzi
description: Seznamte se s novými funkcemi a vylepšeními služby Azure SQL Database a v dokumentaci k Azure SQL Database
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: bdea9af04008ef68578ff8c136760f2493a2ae35
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279215"
---
# <a name="sql-database-release-notes"></a>Poznámky k verzi SQL Database

V tomto článku jsou uvedené SQL Database funkce, které jsou aktuálně ve verzi Public Preview. SQL Database aktualizacích a vylepšeních najdete v článku [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Aktualizace a vylepšení dalších služeb Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkce ve verzi Public Preview

### <a name="single-databasetabsingle-database"></a>[Samostatná databáze](#tab/single-database)

| Funkce | Podrobnosti |
| ---| --- |
| Nové generace hardwaru řady Fsv2-Series a M-Series| Informace najdete v tématu [hardwarové generace](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Privátní odkaz Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Privátní propojení zjednodušuje architekturu sítě a zabezpečuje propojení mezi koncovými body v Azure díky uchovávání dat v síti Azure. Eliminuje tak jejich vystavení ve veřejném internetu. Privátní propojení také umožňuje vytvářet a vykreslovat vlastní služby v Azure. |
| Urychlené obnovení databáze s izolovanými databázemi a elastickými fondy | Informace najdete v tématu [urychlení obnovení databáze](sql-database-accelerated-database-recovery.md).|
|Přibližný počet DISTINCT|Informace najdete v tématu [přibližný počet jedinečných](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Režim dávky na Rowstore (pod úrovní kompatibility 150)|Informace najdete v tématu [režim dávky v Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Zjišťování a klasifikace dat  |Informace najdete v tématu [Azure SQL Database a SQL Data Warehouse klasifikaci & zjišťování dat](sql-database-data-discovery-and-classification.md).|
| Úlohy elastické databáze | Informace najdete v tématu [vytváření, konfigurace a Správa elastických úloh](elastic-jobs-overview.md). |
| Elastické dotazy | Informace najdete v tématu [Přehled elastického dotazu](sql-database-elastic-query-overview.md). |
| Elastické transakce | [Distribuované transakce v cloudových databázích](sql-database-elastic-transactions-overview.md) |
|Zpětná vazba o udělení paměti (režim řádku) (v části úroveň kompatibility 150)|Informace najdete v článku o [zpětné vazbě přidělení paměti (režim řádku)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor dotazů v Azure Portal |Informace najdete v tématu [použití Editoru dotazů SQL Azure Portal k připojení a dotazování dat](sql-database-connect-query-portal.md).|
| Služba R Services/strojové učení s izolovanými databázemi a elastickými fondy |Informace najdete v tématu [Machine Learning Services v Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analýza SQL|Informace najdete v tématu [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Odložená kompilace proměnné tabulky (pod úrovní kompatibility 150)|Informace naleznete v tématu [odložená kompilace proměnné tabulky](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Spravovaná instance](#tab/managed-instance)

| Funkce | Podrobnosti |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Konfigurace podsítě s podporou služby</a> | Zabezpečený a pohodlný způsob, jak spravovat konfiguraci podsítě. |
| <a href="/azure/sql-database/sql-database-instance-pools">Fondy instancí</a> | Pohodlný a cenově výhodný způsob migrace menších instancí SQL do cloudu. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transparentní šifrování dat (TDE) s Bring Your Own Key (BYOK)</a> |Informace najdete v tématu [Azure SQL transparentní šifrování dat s klíči spravovanými zákazníkem v Azure Key Vault: Bring Your Own Key podpora](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Instance objektů zabezpečení serveru Azure AD na úrovni instance (přihlášení)</a> | Vytvořte přihlášení na úrovni serveru pomocí příkazu <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">vytvořit přihlášení z externího poskytovatele</a> . |
| [Transakční replikace](sql-database-managed-instance-transactional-replication.md) | Změny v tabulkách replikujte do jiných databází umístěných na spravovaných instancích, v izolovaných databázích nebo instancích SQL Server, nebo aktualizujte tabulky, když dojde ke změně některých řádků v jiných spravovaných instancích nebo instanci SQL Server. Informace najdete v tématu [Konfigurace replikace v databázi spravované instance Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Detekce hrozeb |Informace najdete v tématu [konfigurace detekce hrozeb v Azure SQL Database Managed instance](sql-database-managed-instance-threat-detection.md).|
| Znovu vytvořit vyřazené databáze se spravovanými instancemi |Informace najdete v tématu [opětovné vytvoření vyřazených databází ve spravované instanci Azure SQL](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>Nové funkce

### <a name="managed-instance-h2-2019-updates"></a>Aktualizace spravované instance H2 2019

- [Skupiny s automatickým převzetím služeb při selhání](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umožňují replikovat všechny databáze z primární instance do sekundární instance v jiné oblasti.
- Nakonfigurujte chování spravované instance pomocí [příznaků globálních trasování](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aktualizace spravované instance H1 2019

V modelu nasazení Managed instance v rámci H1 2019 jsou povoleny následující funkce:
  - Podpora předplatných s <a href="https://aka.ms/sql-mi-visual-studio-subscribers">měsíčním kreditem Azure pro předplatitele sady Visual Studio</a> a zvýšená [regionální omezení](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Podpora pro <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 a SharePoint 2019 </a> a <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Vytvořte instance s <a href="https://aka.ms/managed-instance-collation">kolací na úrovni serveru</a> a <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">časovou zónou</a> dle vašeho výběru.
  - Spravované instance jsou teď chráněné pomocí <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">integrované brány firewall</a>.
  - Nakonfigurujte instance tak, aby používaly [veřejné koncové body](sql-database-managed-instance-public-endpoint-configure.md), připojení [přepsání proxy serveru](sql-database-connectivity-architecture.md#connection-policy) , aby se dosáhlo lepšího výkonu sítě, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuální jádra na generaci hardwaru Gen5</a> nebo <a href="https://aka.ms/managed-instance-configurable-backup-retention">Konfigurace uchovávání záloh až do 35 dnů</a> pro obnovení k bodu v čase. Dlouhodobé uchovávání záloh (až 10 let) není pořád povolené, takže můžete jako alternativu použít <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">zálohy jenom pro kopírování</a> .
  - Nové funkce umožňují <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geograficky obnovit databázi do jiného datového centra pomocí prostředí PowerShell</a>, [Přejmenovat databázi](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [odstranit virtuální cluster](sql-database-managed-instance-delete-virtual-cluster.md).
  - Nová Vestavěná [role přispěvatele instance](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) umožňuje oddělení povinností (SOD) dodržovat zásady zabezpečení a dodržování předpisů v podnikových normách.
  - Spravovaná instance je k dispozici v následujících oblastech Azure Government pro GA (US Gov – Texas, US Gov – Arizona) a také v Čína – sever 2 a Čína – východ 2. Je také k dispozici v následujících veřejných oblastech: Austrálie – střed, Austrálie – střed 2, Brazílie – jih, Francie – jih, Spojené arabské emiráty střed, Spojené arabské emiráty sever, Jižní Afrika sever, Jižní Afrika – západ.

## <a name="fixed-known-issues"></a>Opravené známé problémy

- **Srpna 2019** – obsažené databáze jsou ve spravované instanci plně podporovány.
- **Oct 2019** – integrované obnovení databáze z pro důležité obchodní informace úrovně do pro obecné účely úrovně nebude úspěšné, pokud zdrojová databáze obsahuje objekty OLTP v paměti.
- **Říjen 2019** – funkce **databázová pošta** s externími poštovními servery (mimo Azure) e-mailových serverů pomocí zabezpečeného připojení.
- **Listopadu 2019** – konzistence databáze se ověřuje pomocí `DBCC CHECKDB` po obnovení databáze z Azure Blob Storage.

## <a name="updates"></a>Aktualizace

Seznam SQL Database aktualizací a vylepšení najdete v tématu [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Aktualizace a vylepšení pro všechny služby Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Přispívat k obsahu

Pokud chcete přispět k dokumentaci Azure SQL Database, přečtěte si [příručku pro přispěvatele docs](https://docs.microsoft.com/contribute/).
