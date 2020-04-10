---
title: Poznámky k verzi
description: Informace o nových funkcích a vylepšeních ve službě Azure SQL Database a v dokumentaci k databázi Azure SQL Database
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: sstein
ms.openlocfilehash: b677fd7fe2b14e1c42443478a887ddfa2481dfbf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011442"
---
# <a name="sql-database-release-notes"></a>Poznámky k verzi databáze SQL

Tento článek uvádí funkce databáze SQL, které jsou aktuálně ve verzi Public Preview. Aktualizace a vylepšení databáze SQL naleznete v tématu [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Aktualizace a vylepšení dalších služeb Azure najdete v [tématu Aktualizace služeb](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkce ve verzi Public Preview

### <a name="single-database"></a>[Jednotná databáze](#tab/single-database)

| Funkce | Podrobnosti |
| ---| --- |
| Nové generace hardwaru řady Fsv2 a Řady M| Další informace naleznete v [tématu Hardware generations](sql-database-service-tiers-vcore.md#hardware-generations).|
| Zrychlené obnovení databáze s jednotlivými databázemi a elastické fondy | Další informace naleznete [v tématu Accelerated Database Recovery](sql-database-accelerated-database-recovery.md).|
|Přibližný počet odlišných|Další informace naleznete [v tématu Přibližný počet odlišných](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Dávkový režim na Rowstore (pod úrovní kompatibility 150)|Další informace naleznete [v tématu Dávkový režim v rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Zjišťování a klasifikace dat  |Další informace naleznete v [tématu Azure SQL Database a zjišťování dat datového skladu SQL & klasifikace](sql-database-data-discovery-and-classification.md).|
| Úlohy elastické databáze | Další informace naleznete v [tématu Vytváření, konfigurace a správa elastických úloh](elastic-jobs-overview.md). |
| Elastické dotazy | Další informace naleznete v [tématu Přehled elastického dotazu](sql-database-elastic-query-overview.md). |
| Elastické transakce | [Distribuované transakce napříč cloudovými databázemi](sql-database-elastic-transactions-overview.md). |
|Zpětná vazba grantu paměti (řádkový režim) (pod úrovní kompatibility 150)|Další informace naleznete v [tématu Feedback grant u paměti (řádkový režim).](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
| Editor dotazů na webu Azure Portal |Další informace najdete [v tématu Použití editoru dotazů SQL na webu Azure Portal pro připojení a dotazování na data](sql-database-connect-query-portal.md).|
| R služby / strojové učení s jednotlivými databázemi a elastické fondy |Další informace najdete v tématu [Machine Learning Services v Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|SQL Analytics|Další informace najdete v [tématu Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Proměnná tabulka Odložená kompilace (pod úrovní kompatibility 150)|Další informace naleznete v [tématu Tabulka proměnná odložená kompilace](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

| Funkce | Podrobnosti |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Fondy instancí</a> | Pohodlný a nákladově efektivní způsob migrace menších instancí SQL do cloudu. |
| <a href="https://aka.ms/managed-instance-aadlogins">Objekty serveru Azure AD na úrovni instance (přihlášení)</a> | Vytvořte přihlášení na úrovni serveru pomocí <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">příkazu CREATE LOGIN FROM EXTERNAL PROVIDER.</a> |
| [Transakční replikace](sql-database-managed-instance-transactional-replication.md) | Replikujte změny z tabulek do jiných databází umístěných v klientech spravované instance, jednoduché databáze nebo SQL Server instance nebo aktualizovat tabulky při změně některých řádků v jiných spravovaných insposací nebo SQL Server instance. Další informace naleznete [v tématu Konfigurace replikace v databázi spravovaných instancí Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Detekce hrozeb |Další informace naleznete [v tématu Konfigurace zjišťování hrozeb ve spravované instanci Azure SQL Database](sql-database-managed-instance-threat-detection.md).|

---

## <a name="managed-instance---new-features-and-known-issues"></a>Spravovaná instance – nové funkce a známé problémy

### <a name="managed-instance-h2-2019-updates"></a>Aktualizace spravované instance H2 2019

- [Konfigurace podsítě podporovaná servisem](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Bezpečný a pohodlný způsob správy konfigurace podsítě, kde řídíte datový provoz při spravované instanci, zajišťuje nepřetržitý tok provozu správy
- [Transparentní šifrování dat (TDE) s bring your own key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) umožňuje bring-your-own-key (BYOK) scénář pro ochranu dat v klidovém stavu a umožňuje organizacím oddělit povinnosti správy pro klíče a data.
- [Skupiny s automatickým převzetím služeb při selhání](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umožňují replikovat všechny databáze z primární instance do sekundární instance v jiné oblasti.
- Nakonfigurujte chování spravované instance pomocí [příznaků globálního trasování](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aktualizace spravované instance H1 2019

V modelu nasazení spravované instance v H1 2019 jsou povoleny následující funkce:
  - Podpora předplatného s <a href="https://aka.ms/sql-mi-visual-studio-subscribers">měsíčním kreditem Azure pro předplatitele Visual Studia</a> a zvýšenými [místními limity](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Podpora pro <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 a SharePoint 2019 </a> a <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Vytvářejte instance pomocí <a href="https://aka.ms/managed-instance-collation">řazení na úrovni serveru</a> a časového <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">pásma</a> podle vašeho výběru.
  - Spravované instance jsou nyní chráněny <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">vestavěnou bránou firewall</a>.
  - Nakonfigurujte instance tak, aby používaly [veřejné koncové body](sql-database-managed-instance-public-endpoint-configure.md), [přepsání](sql-database-connectivity-architecture.md#connection-policy) připojení proxy pro lepší výkon sítě, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuální jádra při generování hardwaru Gen5</a> nebo <a href="https://aka.ms/managed-instance-configurable-backup-retention">Konfigurace uchovávání záloh až 35 dní</a> pro obnovení bodu v čase. Dlouhodobé uchovávání záloh (až 10 let) stále není povoleno, takže můžete jako alternativu použít <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">zálohy pouze</a> pro kopírování.
  - Nové funkce umožňují <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geografické obnovení databáze do jiného datového centra pomocí prostředí PowerShell</a>, [přejmenování databáze](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [odstranění virtuálního clusteru](sql-database-managed-instance-delete-virtual-cluster.md).
  - Nová integrovaná [role přispěvatele instance](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) umožňuje oddělení povinnosti (SoD) dodržování zásad zabezpečení a dodržování podnikových standardů.
  - Spravovaná instance je dostupná v následujících oblastech Azure Government do GA (US Gov Texas, US Gov Arizona) a také v Číně – sever 2 a Čína – východ 2. Je také k dispozici v následujících veřejných oblastech: Austrálie – střed, Austrálie – střed 2, Brazílie – jih, Francie – jih, SAE Central, SAE – sever, Jižní Afrika – sever, Jižní Afrika – západ.

### <a name="known-issues"></a>Známé problémy

|Problém  |Datum zjištění  |Status  |Datum vyřešení  |
|---------|---------|---------|---------|
|[Oprávnění pro skupinu prostředků, která nejsou použita pro spravovanou instanci](#permissions-on-resource-group-not-applied-to-managed-instance)|Únor 2020|Má řešení||
|[Omezení ručního převzetí služeb při selhání prostřednictvím portálu pro skupiny převzetí služeb při selhání](#limitation-of-manual-failover-via-portal-for-failover-groups)|Leden 2020|Má řešení||
|[Role agenta SQL vyžadují explicitní oprávnění spustit pro přihlášení bez sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Prosinec 2019|Má řešení||
|[Úlohy agenta SQL mohou být přerušeny restartováním procesu agenta.](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Prosinec 2019|Žádné řešení|Březen 2020|
|[Přihlášení a uživatelé AAD nejsou podporovány v SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Listopad 2019|Žádné řešení||
|[Nejsou použity limity paměti OLTP v paměti v paměti.](#in-memory-oltp-memory-limits-are-not-applied)|Říjen 2019|Má řešení||
|[Při pokusu o odebrání souboru, který není prázdný, byla vrácena chyba](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Říjen 2019|Má řešení||
|[Změnit úroveň služby a vytvořit operace instancí jsou blokovány probíhajícím obnovením databáze.](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Září 2019|Má řešení||
|[Po převzetí služeb při selhání může být nutné překonfigurovat správce prostředků na úrovni služby Business Critical.](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Září 2019|Má řešení||
|[Dialogová okna služby zprostředkovatele služeb mezi databázemi musí být po upgradu úrovně služby znovu inicializována.](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Srpen 2019|Má řešení||
|[Impersonifikace typů přihlášení Azure AD není podporována](#impersonification-of-azure-ad-login-types-is-not-supported)|Červenec 2019|Žádné řešení||
|[@queryparametr není v sp_send_db_mail podporován](#-parameter-not-supported-in-sp_send_db_mail)|Duben 2019|Žádné řešení||
|[Transakční replikace musí být po geografickém převzetí služeb při selhání překonfigurována.](#transactional-replication-must-be-reconfigured-after-geo-failover)|Březen 2019|Žádné řešení||
|[Dočasná databáze se používá během operace OBNOVENÍ.](#temporary-database-is-used-during-restore-operation)||Má řešení||
|[Struktura a obsah TEMPDB jsou znovu vytvořeny](#tempdb-structure-and-content-is-re-created)||Žádné řešení||
|[Překročení úložného prostoru s malými databázovými soubory](#exceeding-storage-space-with-small-database-files)||Má řešení||
|[Hodnoty GUID zobrazené namísto názvů databází](#guid-values-shown-instead-of-database-names)||Má řešení||
|[Protokoly chyb nejsou trvalé.](#error-logs-arent-persisted)||Žádné řešení||
|[Obor transakce ve dvou databázích v rámci stejné instance není podporován.](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Má řešení||
|[Moduly CLR a propojené servery někdy nemohou odkazovat na místní ADRESU IP](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Má řešení||
|Konzistence databáze není ověřena pomocí DBCC CHECKDB po obnovení databáze z úložiště objektů blob Azure.||Vyřešeno|Listopad 2019|
|Obnovení databáze v době v čase z úrovně Business Critical na úroveň Obecné účely nebude úspěšné, pokud zdrojová databáze obsahuje objekty OLTP v paměti.||Vyřešeno|Říjen 2019|
|Funkce Database Mail s externími poštovními servery (mimo Azure) pomocí zabezpečeného připojení||Vyřešeno|Říjen 2019|
|Obsažené databáze nejsou ve spravované instanci podporovány.||Vyřešeno|Srpen 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Oprávnění pro skupinu prostředků, která nejsou použita pro spravovanou instanci

Role RBAC přispěvatele spravované instance při použití na skupinu prostředků (RG) není použita pro spravovanou instanci a nemá žádný vliv.

**Řešení:** Nastavení role přispěvatele spravované instance pro uživatele na úrovni předplatného.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Omezení ručního převzetí služeb při selhání prostřednictvím portálu pro skupiny převzetí služeb při selhání

Pokud skupina převzetí služeb při selhání zahrnuje instance v různých předplatných Azure nebo skupinách prostředků, ruční převzetí služeb při selhání nelze inicializovat z primární instance ve skupině převzetí služeb při selhání.

**Řešení:** Iniciujte převzetí služeb při selhání prostřednictvím portálu z geograficky sekundární instance.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Role agenta SQL vyžadují explicitní oprávnění spustit pro přihlášení bez sysadmin

Pokud non-sysadmin přihlášení jsou přidány do některé z [sql agent pevné databázové role](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), existuje problém, ve kterém explicitní execute oprávnění musí být udělena hlavní uložené procedury pro tyto přihlašovací údaje do práce. Pokud dojde k tomuto problému, zobrazí se chybová zpráva "Oprávnění SPUSTIT bylo odepřeno u objektu <object_name> (Microsoft SQL Server, Chyba: 229)".

**Řešení**: Po přidání přihlášení do některé z sql agent pevné databázové role: SQLAgentUserRole, SQLAgentReaderRole nebo SQLAgentOperatorRole, pro každý z přihlášení přidaných do těchto rolí spustit níže T-SQL skript explicitně udělit oprávnění EXECUTE uložené procedury uvedené.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Úlohy agenta SQL mohou být přerušeny restartováním procesu agenta.

SQL Agent vytvoří novou relaci při každém spuštění úlohy, postupně zvyšuje spotřebu paměti. Aby nedošlo k dosažení limitu vnitřní paměti, který by blokoval provádění naplánovaných úloh, bude proces agenta restartován, jakmile jeho spotřeba paměti dosáhne prahové hodnoty. Může dojít k přerušení provádění úloh spuštěných v okamžiku restartování.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Nejsou použity limity paměti OLTP v paměti v paměti.

Business Critical service-tier nebude správně použít [maximální limity paměti pro objekty optimalizované pro paměť](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) v některých případech. Spravovaná instance může umožnit zatížení používat více paměti pro operace OLTP v paměti, což může ovlivnit dostupnost a stabilitu instance. V paměti OLTP dotazy, které dosahují limitů nemusí selhat okamžitě. Tento problém bude brzy vyřešen. Dotazy, které používají více paměti OLTP v paměti v paměti se nezdaří dříve, pokud dosáhnou [omezení](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Řešení:** [Monitorování využití úložiště OLTP v paměti](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) pomocí aplikace SQL Server Management [Studio,](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) abyste zajistili, že úloha nepoužívá více než dostupnou paměť. Zvyšte limity paměti, které závisí na počtu virtuálních jader, nebo optimalizujte úlohu tak, aby používala méně paměti.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Při pokusu o odebrání souboru, který není prázdný, byla vrácena chyba

Instance SERVERU SQL Server/Spravovaná [neumožňují uživateli vyřadit soubor, který není prázdný](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Pokud se pokusíte odebrat neprázdný `ALTER DATABASE REMOVE FILE` datový soubor `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` pomocí příkazu, chyba nebude okamžitě vrácena. Spravovaná instance se bude snažit soubor přetáhnout a `Internal server error`operace se nezdaří po 30 min s .

**Řešení:** Odeberte obsah souboru pomocí `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` příkazu. Pokud se jedná o jediný soubor ve skupině souborů, budete muset odstranit data z tabulky nebo oddílu přidruženého k této skupině souborů před zmenšením souboru a volitelně načíst tato data do jiné tabulky nebo oddílu.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Změnit úroveň služby a vytvořit operace instancí jsou blokovány probíhajícím obnovením databáze.

Průběžné `RESTORE` prohlášení, proces migrace dat a integrované obnovení času v bodu bude blokovat aktualizaci úrovně služby nebo změnit velikost existující instance a vytvářet nové instance, dokud proces obnovení neskončí. Proces obnovení zablokuje tyto operace ve spravovaných instancích a fondech instancí ve stejné podsíti, kde je spuštěn proces obnovení. Instance ve fondech instancí nejsou ovlivněny. Vytvoření nebo změna operací úrovně služby se nezdaří nebo časový limit - budou pokračovat po dokončení nebo zrušení procesu obnovení.

**Řešení**: Počkejte, až proces obnovení skončí, nebo zrušit proces obnovení, pokud vytvoření nebo aktualizace operace vrstvy služby má vyšší prioritu.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Po převzetí služeb při selhání může být nutné překonfigurovat správce prostředků na úrovni služby Business Critical.

[Funkce Správce prostředků,](/sql/relational-databases/resource-governor/resource-governor) která umožňuje omezit prostředky přiřazené k zatížení uživatele, může nesprávně klasifikovat některé zatížení uživatelů po převzetí služeb při selhání nebo uživatelem iniciované změně úrovně služby (například změna maximální velikosti virtuálního jádra nebo maximální instance).

**Řešení:** Spouštějte `ALTER RESOURCE GOVERNOR RECONFIGURE` pravidelně nebo jako součást úlohy agenta SQL, která provádí úlohu SQL při spuštění instance, pokud používáte [správce prostředků](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Dialogová okna služby zprostředkovatele služeb mezi databázemi musí být po upgradu úrovně služby znovu inicializována.

Dialogová okna služby zprostředkovatele mezi databázemi zastaví doručování zpráv do služeb v jiných databázích po změně operace vrstvy služby. Zprávy **nejsou ztraceny** a lze je nalézt ve frontě odesílatele. Jakákoli změna velikosti virtuálních jader nebo velikosti úložiště instance ve spravované instanci způsobí `service_broke_guid` změnu hodnoty v zobrazení [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) pro všechny databáze. Všechny `DIALOG` vytvořené pomocí [begin dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) prohlášení, které odkazuje zprostředkovatelé služeb v jiné databázi přestane doručování zpráv do cílové služby.

**Řešení:** Zastavte všechny aktivity, které používají dialogové konverzace služby služby mezi databázemi před aktualizací úrovně služby a znovu inicializovat je po. Pokud jsou zbývající zprávy, které jsou nedoručené po změně vrstvy služby, přečtěte si zprávy ze zdrojové fronty a znovu je odešlete do cílové fronty.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Impersonifikace typů přihlášení Azure AD není podporována

Zosobnění `EXECUTE AS USER` pomocí `EXECUTE AS LOGIN` nebo následující objekty AAD není podporována:
-   Aliased AAD uživatelů. V tomto případě `15517`je vrácena následující chyba .
- AAD přihlášení a uživatelé na základě aplikací AAD nebo instančních objektů. V tomto případě `15517` jsou vráceny následující chyby a `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametr není v sp_send_db_mail podporován

Parametr `@query` v [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedury nefunguje.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transakční replikace musí být po geografickém převzetí služeb při selhání překonfigurována.

Pokud je transakční replikace povolena v databázi ve skupině s automatickým převzetím služeb při selhání, musí správce spravované instance vyčistit všechny publikace na staré primární a překonfigurovat je na nové primární po převzetí služeb při selhání do jiné oblasti. Další podrobnosti najdete v [tématu Replikace.](sql-database-managed-instance-transact-sql-information.md#replication)

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Přihlášení a uživatelé AAD nejsou podporovány v SSDT

Datové nástroje SQL Serveru plně nepodporují přihlášení a uživatele služby Azure Active directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Dočasná databáze se používá během operace OBNOVENÍ.

Při obnovení databáze na spravované instanci služba obnovení nejprve vytvoří prázdnou databázi s požadovaným názvem pro přidělení názvu instance. Po určité době bude tato databáze zrušena a obnovení skutečné databáze bude spuštěno. Databáze, která je ve stavu *Obnovení* bude dočasně mít náhodnou hodnotu GUID namísto názvu. Dočasný název bude po dokončení procesu `RESTORE` obnovení změněn na požadovaný název zadaný v příkazu. V počáteční fázi může uživatel přistupovat k prázdné databázi a dokonce vytvářet tabulky nebo načítat data v této databázi. Tato dočasná databáze bude zrušena při spuštění služby obnovení druhé fáze.

**Řešení**: Nepřistupujte k databázi, kterou obnovujete, dokud neuvidíte, že je obnovení dokončeno.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura a obsah TEMPDB jsou znovu vytvořeny

Databáze `tempdb` je vždy rozdělena do 12 datových souborů a strukturu souboru nelze změnit. Maximální velikost na soubor nelze změnit a nové soubory `tempdb`nelze přidat do aplikace . `Tempdb`je vždy znovu vytvořen jako prázdná databáze při spuštění instance nebo `tempdb` převzetí služby při selhání a všechny změny provedené v aplikaci nebudou zachovány.

### <a name="exceeding-storage-space-with-small-database-files"></a>Překročení úložného prostoru s malými databázovými soubory

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`a `RESTORE DATABASE` příkazy může selhat, protože instance může dosáhnout limitu úložiště Azure.

Každá spravovaná instance pro obecné účely má až 35 TB úložiště vyhrazeného pro místo na disku Azure Premium. Každý databázový soubor je umístěn na samostatný fyzický disk. Velikost disku může být 128 GB, 256 GB, 512 GB, 1 TB nebo 4 TB. Nevyužité místo na disku se neúčtuje, ale celkový součet velikostí disku Azure Premium nesmí překročit 35 TB. V některých případech spravovaná instance, která nepotřebuje celkem 8 TB, může překročit limit 35 TB Azure na velikost úložiště z důvodu vnitřní fragmentace.

Instance spravované pro obecné účely může mít například jeden velký soubor o velikosti 1,2 TB umístěný na 4 TB disku. Může mít také 248 souborů s velikostí 1 GB, které jsou umístěny na samostatných 128 GB disky. V tomto příkladu:

- Celková velikost přiděleného úložiště disku je 1 x 4 TB + 248 x 128 GB = 35 TB.
- Celkový vyhrazený prostor pro databáze v instanci je 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Tento příklad ukazuje, že za určitých okolností může spravovaná instance kvůli určité distribuci souborů dosáhnout limitu 35 TB, který je vyhrazen pro připojený disk Azure Premium, pokud to neočekáváte.

V tomto příkladu existující databáze i nadále fungovat a může růst bez problémů, pokud nejsou přidány nové soubory. Nové databáze nelze vytvořit ani obnovit, protože pro nové diskové jednotky není dostatek místa, a to i v případě, že celková velikost všech databází nedosáhne limitu velikosti instance. Chyba, která je vrácena v tomto případě není jasné.

Počet [zbývajících souborů](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) můžete identifikovat pomocí systémových zobrazení. Pokud tohoto limitu dosáhnete, zkuste [vyprázdnit a odstranit některé menší soubory pomocí příkazu DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) nebo přepnout na [úroveň Business Critical, která tento limit nemá](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Hodnoty GUID zobrazené namísto názvů databází

Několik systémových zobrazení, čítačů výkonu, chybových zpráv, událostí XEvents a položek protokolu chyb zobrazuje identifikátory databáze GUID namísto skutečných názvů databází. Nespoléhejte na tyto identifikátory GUID, protože jsou v budoucnu nahrazeny skutečnými názvy databází.

**Řešení:** Použití zobrazení sys.databases k překladu skutečného názvu databáze z názvu fyzické databáze určeného ve formě identifikátorů databáze GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Protokoly chyb nejsou trvalé.

Protokoly chyb, které jsou k dispozici ve spravované instanci, nejsou trvalé a jejich velikost není zahrnuta v maximálním limitu úložiště. Pokud dojde k převzetí služeb při selhání, mohou být protokoly chyb automaticky vymazány. V historii protokolu chyb mohou být mezery, protože spravovaná instance byla několikrát přesunuta na několika virtuálních počítačích.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Obor transakce ve dvou databázích v rámci stejné instance není podporován.

Třída `TransactionScope` v rozhraní .NET nefunguje, pokud jsou dva dotazy odeslány do dvou databází v rámci stejné instance pod stejným rozsahem transakce:

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

Přestože tento kód pracuje s daty v rámci stejné instance, vyžaduje MSDTC.

**Řešení:** Pomocí [sqlconnection.changedatabase(string)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) použijte jinou databázi v kontextu připojení namísto použití dvou připojení.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduly CLR a propojené servery někdy nemohou odkazovat na místní ADRESU IP

Moduly CLR umístěné ve spravované instanci a propojených serverech nebo distribuované dotazy, které odkazují na aktuální instanci, někdy nemohou vyřešit IP adresu místní instance. Tato chyba je přechodný problém.

**Řešení:** Pokud je to možné, použijte kontextová připojení v modulu CLR.

## <a name="updates"></a>Aktualizace

Seznam aktualizací a vylepšení databáze SQL naleznete v tématu [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Aktualizace a vylepšení všech služeb Azure najdete v [tématu Aktualizace služeb](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Příspěvky k obsahu

Informace o tom, jak přispět k dokumentaci k databázi Azure SQL Database, najdete v [průvodci přispěvateli dokumentů](https://docs.microsoft.com/contribute/).
