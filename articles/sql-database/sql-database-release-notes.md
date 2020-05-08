---
title: Poznámky k verzi
description: Seznamte se s novými funkcemi a vylepšeními služby Azure SQL Database a v dokumentaci k Azure SQL Database
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: sstein
ms.openlocfilehash: aa8d1634c015f338053a4d167db34ef0b5a83505
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801684"
---
# <a name="sql-database-release-notes"></a>Poznámky k verzi SQL Database

V tomto článku jsou uvedené SQL Database funkce, které jsou aktuálně ve verzi Public Preview. SQL Database aktualizacích a vylepšeních najdete v článku [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Aktualizace a vylepšení dalších služeb Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkce ve verzi Public Preview

### <a name="single-database"></a>[Samostatná databáze](#tab/single-database)

| Funkce | Podrobnosti |
| ---| --- |
| Nové generace hardwaru řady Fsv2-Series a M-Series| Informace najdete v tématu [hardwarové generace](sql-database-service-tiers-vcore.md#hardware-generations).|
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
|SQL Analytics|Informace najdete v tématu [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Odložená kompilace proměnné tabulky (pod úrovní kompatibility 150)|Informace naleznete v tématu [odložená kompilace proměnné tabulky](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

| Funkce | Podrobnosti |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Fondy instancí</a> | Pohodlný a cenově výhodný způsob migrace menších instancí SQL do cloudu. |
| <a href="https://aka.ms/managed-instance-aadlogins">Instance objektů zabezpečení serveru Azure AD na úrovni instance (přihlášení)</a> | Vytvořte přihlášení na úrovni serveru pomocí příkazu <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">vytvořit přihlášení z externího poskytovatele</a> . |
| [Transakční replikace](sql-database-managed-instance-transactional-replication.md) | Změny v tabulkách replikujte do jiných databází umístěných na spravovaných instancích, v izolovaných databázích nebo instancích SQL Server, nebo aktualizujte tabulky, když dojde ke změně některých řádků v jiných spravovaných instancích nebo instanci SQL Server. Informace najdete v tématu [Konfigurace replikace v databázi spravované instance Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Detekce hrozeb |Informace najdete v tématu [konfigurace detekce hrozeb v Azure SQL Database Managed instance](sql-database-managed-instance-threat-detection.md).|
| Dlouhodobé uchovávání záloh | Informace najdete v tématu [Konfigurace dlouhodobého uchovávání záloh v Azure SQL Database Managed instance](sql-database-managed-instance-long-term-backup-retention-configure.md), která je aktuálně ve verzi Public Preview. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Spravovaná instance – nové funkce a známé problémy

### <a name="managed-instance-h2-2019-updates"></a>Aktualizace spravované instance H2 2019

- [Konfigurace podsítě s podporou služby](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Zabezpečený a pohodlný způsob, jak spravovat konfiguraci podsítě, kde kontrolujete přenos dat, zatímco spravovaná instance zajišťuje nepřetržitý tok provozu správy
- [Transparentní šifrování dat (TDE) s Bring Your Own keyem (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) umožňuje použít scénář BYOK (Přineste si vlastní klíč) pro ochranu dat v klidovém umístění a umožňuje organizacím oddělit povinnosti správy pro klíče a data.
- [Skupiny s automatickým převzetím služeb při selhání](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umožňují replikovat všechny databáze z primární instance do sekundární instance v jiné oblasti.
- Nakonfigurujte chování spravované instance pomocí [příznaků globálních trasování](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aktualizace spravované instance H1 2019

V modelu nasazení Managed instance v rámci H1 2019 jsou povoleny následující funkce:
  - Podpora předplatných s <a href="https://aka.ms/sql-mi-visual-studio-subscribers">měsíčním kreditem Azure pro předplatitele sady Visual Studio</a> a zvýšená [regionální omezení](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Podpora pro <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 a SharePoint 2019 </a> a <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Vytvořte instance s <a href="https://aka.ms/managed-instance-collation">kolací na úrovni serveru</a> a <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">časovou zónou</a> dle vašeho výběru.
  - Spravované instance jsou teď chráněné pomocí <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">integrované brány firewall</a>.
  - Nakonfigurujte instance tak, aby používaly [veřejné koncové body](sql-database-managed-instance-public-endpoint-configure.md), připojení [přepsání proxy serveru](sql-database-connectivity-architecture.md#connection-policy) , aby se dosáhlo lepšího výkonu sítě, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuální jádra na generaci hardwaru Gen5</a> nebo <a href="https://aka.ms/managed-instance-configurable-backup-retention">Konfigurace uchovávání záloh až do 35 dnů</a> pro obnovení k bodu v čase. [Dlouhodobé uchovávání záloh](sql-database-long-term-retention.md#managed-instance-support) (až 10 let) je aktuálně ve verzi Public Preview omezeno.  
  - Nové funkce umožňují <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geograficky obnovit databázi do jiného datového centra pomocí prostředí PowerShell</a>, [Přejmenovat databázi](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [odstranit virtuální cluster](sql-database-managed-instance-delete-virtual-cluster.md).
  - Nová Vestavěná [role přispěvatele instance](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) umožňuje oddělení povinností (SOD) dodržovat zásady zabezpečení a dodržování předpisů v podnikových normách.
  - Spravovaná instance je k dispozici v následujících oblastech Azure Government pro GA (US Gov – Texas, US Gov – Arizona) a také v Čína – sever 2 a Čína – východ 2. Je také k dispozici v následujících veřejných oblastech: Austrálie – střed, Austrálie – střed 2, Brazílie – jih, Francie – jih, Spojené arabské emiráty střed, Spojené arabské emiráty sever, Jižní Afrika sever, Jižní Afrika – západ.

### <a name="known-issues"></a>Známé problémy

|Problém  |Datum zjištění  |Status  |Datum vyřešení  |
|---------|---------|---------|---------|
|[Agent přestane reagovat při úpravách, zakázání nebo povolení stávajících úloh.](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Květen 2020|Automaticky zmírňované| |
|[Oprávnění pro skupinu prostředků neplatí pro spravovanou instanci](#permissions-on-resource-group-not-applied-to-managed-instance)|Únor 2020|Má alternativní řešení| |
|[Omezení ručního převzetí služeb při selhání prostřednictvím portálu pro skupiny převzetí služeb](#limitation-of-manual-failover-via-portal-for-failover-groups)|Leden 2020|Má alternativní řešení| |
|[Role agenta SQL potřebují explicitní oprávnění EXECUTE pro přihlášení jiná než sysadmin.](#in-memory-oltp-memory-limits-are-not-applied)|DEC 2019|Má alternativní řešení| |
|[Úlohy agenta SQL je možné přerušit restartováním procesu agenta.](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|DEC 2019|Vyřešeno|Březen 2020|
|[Přihlášení AAD a uživatelé nejsou v SSDT podporované.](#aad-logins-and-users-are-not-supported-in-ssdt)|Listopadu 2019|Žádné alternativní řešení| |
|[Limity OLTP paměti v paměti se nepoužívají.](#in-memory-oltp-memory-limits-are-not-applied)|Říjen 2019|Má alternativní řešení| |
|[Při pokusu o odebrání neprázdného souboru se vrátila chybná chyba.](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Říjen 2019|Má alternativní řešení| |
|[Probíhající obnovení databáze blokuje změnu úrovně služby a operací vytváření instancí.](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|SEP 2019|Má alternativní řešení| |
|[Po převzetí služeb při selhání může být potřeba změnit správce prostředků u Pro důležité obchodní informace úrovně služeb](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|SEP 2019|Má alternativní řešení| |
|[Dialogová okna mezidatabázového Service Broker se musí po upgradu na úrovni služby znovu inicializovat.](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Srpna 2019|Má alternativní řešení| |
|[Impersonification typů přihlášení Azure AD se nepodporuje.](#impersonification-of-azure-ad-login-types-is-not-supported)|Července 2019|Žádné alternativní řešení| |
|[@queryparametr není v sp_send_db_mail podporován.](#-parameter-not-supported-in-sp_send_db_mail)|Duben 2019|Žádné alternativní řešení| |
|[Po geografickém převzetí služeb při selhání je potřeba znovu nakonfigurovat transakční replikaci.](#transactional-replication-must-be-reconfigured-after-geo-failover)|Březen 2019|Žádné alternativní řešení| |
|[Během operace obnovení se používá dočasná databáze.](#temporary-database-is-used-during-restore-operation)||Má alternativní řešení| |
|[Struktura a obsah TEMPDB se znovu vytvoří.](#tempdb-structure-and-content-is-re-created)||Žádné alternativní řešení| |
|[Překročení úložného prostoru s malými databázovými soubory](#exceeding-storage-space-with-small-database-files)||Má alternativní řešení| |
|[Hodnoty GUID, které se zobrazují místo názvů databází](#guid-values-shown-instead-of-database-names)||Má alternativní řešení| |
|[Protokoly chyb nejsou trvalé](#error-logs-arent-persisted)||Žádné alternativní řešení| |
|[Obor transakce ve dvou databázích v rámci stejné instance není podporovaný.](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Má alternativní řešení|Březen 2020|
|[Moduly CLR a propojené servery někdy nemůžou odkazovat na místní IP adresu.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Má alternativní řešení| |
|Po obnovení databáze z Azure Blob Storage konzistence databáze nebyla ověřena pomocí příkazu DBCC CHECKDB.| |Vyřešeno|Listopadu 2019|
|Obnovení databáze v čase z Pro důležité obchodní informace úrovně do Pro obecné účely úrovně nebude úspěšné, pokud zdrojová databáze obsahuje objekty OLTP v paměti.| |Vyřešeno|Říjen 2019|
|Databázová pošta funkce s externími poštovními servery (ne Azure) pomocí zabezpečeného připojení| |Vyřešeno|Říjen 2019|
|Obsažené databáze nejsou ve spravované instanci podporované.| |Vyřešeno|Srpna 2019|

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agent přestane reagovat při úpravách, zakázání nebo povolení stávajících úloh.

V některých případech úpravy existující úlohy, zakázání nebo povolení může způsobit, že agent přestane reagovat. Problém se automaticky sníží při detekci, která má za následek restartování procesu agenta.

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Oprávnění pro skupinu prostředků neplatí pro spravovanou instanci

Role RBAC přispěvatele spravované instance, pokud se používá pro skupinu prostředků (RG), se nepoužije na spravovanou instanci a nemá žádný vliv.

**Alternativní řešení**: nastavte roli přispěvatele spravované instance pro uživatele na úrovni předplatného.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Omezení ručního převzetí služeb při selhání prostřednictvím portálu pro skupiny převzetí služeb

Pokud skupina převzetí služeb při selhání zahrnuje mezi instancemi v různých předplatných Azure nebo skupinách prostředků, ruční převzetí služeb při selhání nejde iniciovat z primární instance ve skupině převzetí služeb

**Alternativní řešení**: zahajte převzetí služeb při selhání prostřednictvím portálu z instance geografické sekundární instance.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Role agenta SQL potřebují explicitní oprávnění EXECUTE pro přihlášení jiná než sysadmin.

Pokud se do kterékoli z [pevných databázových rolí SQL agenta](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)přidají přihlášení jiného typu než sysadmin, existuje problém, ve kterém je potřeba udělit explicitní oprávnění ke spuštění hlavním uloženým procedurám, aby tato přihlášení fungovala. V případě výskytu tohoto problému se zobrazí chybová zpráva "oprávnění EXECUTE bylo odepřeno pro objekt <object_name> (Microsoft SQL Server, chyba: 229)".

**Alternativní řešení**: Když přidáte přihlášení do některé z pevných databázových rolí agenta SQL: SQLAgentUserRole, SQLAgentReaderRole nebo role SQLAgentOperatorRole, pro každé přihlášení přidané k těmto rolím se spustí skript T-SQL, který explicitně udělí oprávnění ke spouštění uložených procedurám uvedeným v seznamu.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Úlohy agenta SQL je možné přerušit restartováním procesu agenta.

**(Vyřešeno v březnu 2020)** SQL Agent vytvoří novou relaci při každém spuštění úlohy a postupně zvyšuje spotřebu paměti. Aby nedošlo k překročení limitu interní paměti, který by blokoval provádění plánovaných úloh, proces agenta se restartuje, jakmile jeho spotřeba dosáhne prahové hodnoty. Výsledkem může být přerušení provádění úloh spuštěných v okamžiku restartování.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Limity OLTP paměti v paměti se nepoužívají.

Služba Pro důležité obchodní informace Service – úroveň v některých případech nebude správně používat [maximální limity paměti pro paměťově optimalizované objekty](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) . Spravovaná instance může povolit zatížení pro využití více paměti pro OLTP operace v paměti, což může mít vliv na dostupnost a stabilitu instance. Dotazy OLTP v paměti, které dosáhnou limitu, nemusí okamžitě selhat. Tento problém bude brzy vyřešen. Dotazy, které používají více paměti OLTP v paměti, selžou dříve, pokud dosáhnou [omezení](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Alternativní řešení:** [sledujte využití úložiště v paměti OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) pomocí [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) a ujistěte se, že zatížení nepoužívá více než dostupnou paměť. Zvyšte limit paměti, který závisí na počtu virtuální jádra, nebo Optimalizujte úlohy tak, aby používaly méně paměti.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Při pokusu o odebrání neprázdného souboru se vrátila chybná chyba.

SQL Server/spravovaná instance [nedovoluje uživateli vyřadit neprázdný soubor](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Pokud se pokusíte odebrat neprázdný datový soubor pomocí `ALTER DATABASE REMOVE FILE` příkazu, Chyba `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` se okamžitě nevrátí. Spravovaná instance bude pokračovat v pokusu o vyřazení souboru a operace po 30min s `Internal server error`se nezdaří.

**Alternativní řešení**: Odeberte obsah souboru pomocí `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` příkazu. Pokud se jedná o jediný soubor ve skupině souborů, musíte před zmenšením souboru odstranit data z tabulky nebo oddílu přidruženého k této skupině souborů a případně tato data načíst do jiné tabulky nebo oddílu.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Probíhající obnovení databáze blokuje změnu úrovně služby a operací vytváření instancí.

Průběžný `RESTORE` příkaz, proces migrace dat a integrované obnovení k časovému okamžiku zablokuje aktualizaci úrovně služby nebo změny velikosti existující instance a vytváření nových instancí až do dokončení procesu obnovení. Proces obnovení zablokuje tyto operace ve spravovaných instancích a fondech instancí ve stejné podsíti, kde je spuštěn proces obnovení. Instance v fondech instancí nejsou ovlivněny. Operace vytvoření nebo změny vrstvy služeb nebudou úspěšné ani po vypršení časového limitu – budou pokračovat až po dokončení nebo zrušení procesu obnovení.

**Alternativní řešení**: Počkejte, než se dokončí proces obnovení, nebo zrušte proces obnovení, pokud má operace vytvoření nebo aktualizace vrstvy služby vyšší prioritu.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Po převzetí služeb při selhání může být potřeba změnit správce prostředků u Pro důležité obchodní informace úrovně služeb

Funkce [Správce prostředků](/sql/relational-databases/resource-governor/resource-governor) , která umožňuje omezit prostředky přiřazené k uživatelskému zatížení, může nesprávně klasifikovat určitou úlohu uživatelů po převzetí služeb při selhání nebo uživatelem iniciované změny úrovně služby (například změna maximální velikosti úložiště Vcore nebo maximálního počtu instancí).

**Alternativní řešení**: `ALTER RESOURCE GOVERNOR RECONFIGURE` spouštějte pravidelně nebo jako součást úlohy agenta SQL, která spustí úlohu SQL při spuštění instance, pokud používáte [Správce zdrojů](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Dialogová okna mezidatabázového Service Broker se musí po upgradu na úrovni služby znovu inicializovat.

Dialogy Service Broker mezi databázemi ukončí doručování zpráv do služeb v jiných databázích po provedení operace změny úrovně služby. Zprávy nejsou **ztraceny** a je možné je najít ve frontě odesílatelů. Jakákoli změna velikosti úložiště virtuální jádra nebo instance ve spravované instanci způsobí, že `service_broke_guid` se hodnota v zobrazení [Sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) změní pro všechny databáze. Jakékoli `DIALOG` vytvoření pomocí příkazu [Begin dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , který odkazuje na zprostředkovatele služby v jiné databázi, přestane předávat zprávy cílové službě.

**Alternativní řešení:** Před aktualizací úrovně služby zastavte všechny aktivity, které používají konverzaci mezi Service Brokermi databázemi, a potom je znovu inicializujte. Pokud jsou zbývající zprávy nedoručené po změně úrovně služeb, přečtěte si zprávy ze zdrojové fronty a znovu je odešlete do cílové fronty.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Impersonification typů přihlášení Azure AD se nepodporuje.

Zosobnění pomocí `EXECUTE AS USER` nebo `EXECUTE AS LOGIN` z následujících objektů zabezpečení AAD není podporované:
-    Uživatelé AAD s aliasem V tomto případě `15517`se vrátí následující chyba.
- Přihlášení AAD a uživatelé na základě aplikací AAD nebo instančních objektů. V tomto případě `15517` se vrátí následující chyby a `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametr není v sp_send_db_mail podporován.

`@query` Parametr v proceduře [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) nefunguje.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Po geografickém převzetí služeb při selhání je potřeba znovu nakonfigurovat transakční replikaci.

Pokud je transakční replikace povolená v databázi ve skupině automatického převzetí služeb při selhání, musí správce spravované instance vyčistit všechny publikace na staré primární primární databázi a po převzetí služeb při selhání do jiné oblasti je znovu nakonfigurovat na nové primární úrovni. Další podrobnosti najdete v tématu [replikace](sql-database-managed-instance-transact-sql-information.md#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Přihlášení AAD a uživatelé nejsou v SSDT podporované.

Nástroje SQL Server Data Tools plně nepodporují přihlášení a uživatele Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Během operace obnovení se používá dočasná databáze.

Když se databáze na spravované instanci obnovuje, služba obnovení nejprve vytvoří prázdnou databázi s požadovaným názvem k přidělení názvu v instanci. Po určité době bude tato databáze vyřazena a bude spuštěna obnova skutečné databáze. Databáze, ve které je stav *obnovení* , bude mít dočasné místo názvu hodnotu NÁHODNÉho identifikátoru GUID. Po dokončení procesu obnovení bude dočasný název změněn na požadovaný název zadaný `RESTORE` v příkazu. V počáteční fázi může uživatel přistupovat k prázdné databázi a dokonce vytvářet tabulky nebo načítat data v této databázi. Tato dočasná databáze se vynechá, když služba obnovení spustí druhou fázi.

**Alternativní řešení**: Neprovádějte přístup k databázi, kterou obnovujete, dokud neuvidíte, že obnovení bylo dokončeno.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura a obsah TEMPDB se znovu vytvoří.

`tempdb` Databáze je vždy rozdělena do 12 datových souborů a struktura souborů nemůže být změněna. Maximální velikost na soubor nelze změnit a nelze přidat nové soubory do `tempdb`. `Tempdb`je vždy znovu vytvořen jako prázdná databáze při spuštění nebo převzetí služeb při selhání a jakékoli změny provedené v `tempdb` nezůstanou zachovány.

### <a name="exceeding-storage-space-with-small-database-files"></a>Překročení úložného prostoru s malými databázovými soubory

`CREATE DATABASE`příkazy `ALTER DATABASE ADD FILE`, a `RESTORE DATABASE` mohou selhat, protože instance může dosáhnout limitu Azure Storage.

Každá Pro obecné účely spravovaná instance má až 35 TB úložiště rezervovaného pro místo na disku Azure Premium. Každý databázový soubor je umístěn na samostatném fyzickém disku. Velikosti disků můžou být 128 GB, 256 GB, 512 GB, 1 TB nebo 4 TB. Nevyužité místo na disku se neúčtuje, ale celkový součet velikostí disků Azure Premium nesmí překročit 35 TB. V některých případech může spravovaná instance, která nepotřebuje 8 TB celkem, překročit 35 TB Azure na velikost úložiště kvůli vnitřní fragmentaci.

Například Pro obecné účely spravovaná instance může mít jeden velký soubor o velikosti 1,2 TB umístěný na 4 TB disku. Může taky mít 248 souborů o velikosti 1 GB, která je umístěná na samostatných discích 128-GB. V tomto příkladu:

- Celková přidělená velikost diskového úložiště je 1 × 4 TB + 248 × 128 GB = 35 TB.
- Celkové rezervované místo pro databáze v instanci je 1 × 1,2 TB + 248 × 1 GB = 1,4 TB.

Tento příklad ilustruje, že za určitých okolností, v důsledku konkrétní distribuce souborů, může spravovaná instance dosáhnout limitu 35-TB, který je vyhrazený pro připojenou jednotku Azure Premium, pokud ji neočekáváte.

V tomto příkladu existující databáze fungují i nadále a můžou růst bez jakýchkoli potíží, pokud se nepřidá nové soubory. Nové databáze nelze vytvořit ani obnovit, protože není dostatek místa pro nové diskové jednotky, ani v případě, že celková velikost všech databází nedosáhne limitu velikosti instance. Chyba, která se vrátí v tomto případě, není jasná.

[Počet zbývajících souborů můžete identifikovat](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) pomocí systémových zobrazení. Pokud dosáhnete tohoto limitu, zkuste [vyprázdnit a odstranit některé menší soubory pomocí příkazu DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) nebo přepněte na [pro důležité obchodní informaceovou vrstvu, která nemá toto omezení](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Hodnoty GUID, které se zobrazují místo názvů databází

Několik systémových zobrazení, čítače výkonu, chybové zprávy, XEvents a položky protokolu chyb zobrazují identifikátory databáze identifikátorů GUID místo skutečných názvů databází. Nespoléhejte na tyto identifikátory GUID, protože se v budoucnu nahrazují skutečnými názvy databází.

**Alternativní řešení**: v zobrazení sys. databases můžete vyřešit skutečný název databáze z fyzického názvu databáze, který je zadaný ve formě identifikátorů GUID databáze.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Protokoly chyb nejsou trvalé

Protokoly chyb, které jsou k dispozici ve spravované instanci, nejsou trvale uložené a jejich velikost není zahrnuta v maximálním limitu úložiště. Pokud dojde k převzetí služeb při selhání, můžou se protokoly chyb automaticky vymazat. V historii protokolu chyb můžou být mezery, protože se spravovaná instance na několika virtuálních počítačích přesunula několikrát.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Obor transakce ve dvou databázích v rámci stejné instance není podporovaný.

**(Vyřešeno v březnu 2020)** `TransactionScope` Třída v rozhraní .NET nefunguje, pokud jsou dva dotazy odesílány do dvou databází v rámci stejné instance v rámci stejného oboru transakce:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Alternativní řešení (nepotřebné od března 2020):** Použijte [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) pro použití jiné databáze v kontextu připojení namísto použití dvou připojení.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduly CLR a propojené servery někdy nemůžou odkazovat na místní IP adresu.

Moduly CLR umístění do spravované instance a propojené servery nebo distribuované dotazy, které odkazují na aktuální instanci, někdy nemůžou přeložit IP adresu místní instance. Tato chyba je přechodný problém.

**Alternativní řešení:** Pokud je to možné, použijte připojení kontextu v modulu CLR.

## <a name="updates"></a>Aktualizace

Seznam SQL Database aktualizací a vylepšení najdete v tématu [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Aktualizace a vylepšení pro všechny služby Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Příspěvky k obsahu

Pokud chcete přispět k dokumentaci Azure SQL Database, přečtěte si [příručku pro přispěvatele docs](https://docs.microsoft.com/contribute/).
