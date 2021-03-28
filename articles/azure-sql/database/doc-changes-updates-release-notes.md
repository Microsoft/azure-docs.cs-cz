---
title: Co je nového
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Přečtěte si o nových funkcích a vylepšeních dokumentace pro Azure SQL Database & spravované instance SQL.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: sstein
ms.openlocfilehash: 9827a40b2ebc91c17ad7b5457259b8d82565edee
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640081"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Co je nového v Azure SQL Database & spravované instance SQL?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V tomto článku jsou uvedené Azure SQL Database a funkce spravované instance Azure SQL, které jsou momentálně ve verzi Public Preview. Aktualizace a vylepšení pro SQL Database a SQL spravované instance najdete v tématu [SQL Database & aktualizace služby Managed instance SQL](https://azure.microsoft.com/updates/?product=sql-database). Aktualizace a vylepšení dalších služeb Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Co je nového

Dokumentace pro Azure SQL Database a Azure SQL Managed instance byla rozdělena do samostatných oddílů. Aktualizovali jsme také, jak odkazujeme na spravovanou instanci z *Azure SQL Database spravované instance* do *Azure SQL Managed instance*.

Provedli jsme to proto, že některé funkce a funkce se výrazně liší mezi izolovanou databází a spravovanou instancí a je stále náročnější vysvětlovat komplexní drobné odlišnosti mezi Azure SQL Database a Azure SQL Managed instance v jednotlivých sdílených článcích.

Toto vyjasnění mezi různými produkty Azure SQL by mělo zjednodušit a zjednodušit proces práce s databázovým strojem SQL Server v Azure, ať už se jedná o jedinou spravovanou databázi v Azure SQL Database, plně podrobnějším spravovanou instanci, která hostuje více databází ve spravované instanci Azure SQL, nebo známý místní SQL Server produkt hostovaný na virtuálním počítači v Azure.

Vezměte v úvahu, že se jedná o probíhající práci, a ne každý článek ještě nebyl aktualizován. Například dokumentace k příkazům jazyka Transact-SQL (T-SQL), uloženým procedurám a mnoha funkcím sdíleným mezi Azure SQL Database a Azure SQL Managed instance ještě není dokončená, takže vám děkujeme za vaši trpělivost, abyste mohli dál vyjasnit obsah. 

Tato tabulka nabízí rychlé porovnání změny v terminologii: 


|**Nový termín**  | **Předchozí termín**  |**Vysvětlení** |
|---------|---------|---------|
|**Spravovaná instance Azure SQL** | Azure SQL Database *spravovaná instance*| Azure SQL Managed instance je vlastní produkt v rámci řady Azure SQL, nikoli jenom možnost nasazení v rámci Azure SQL Database. | 
|**Azure SQL Database**|Azure SQL Database *samostatná databáze*| Pokud není výslovně uvedeno jinak, název produktu Azure SQL Database zahrnuje jak izolované databáze, tak databáze nasazené do elastického fondu. |
|**Azure SQL Database**|Azure SQL Database *elastický fond*| Pokud není výslovně uvedeno jinak, název produktu Azure SQL Database zahrnuje jak izolované databáze, tak databáze nasazené do elastického fondu.  |
|**Azure SQL Database** |Azure SQL Database | I když termín zůstává stejný, vztahuje se nyní jenom na nasazení s izolovanými databázemi a elastickými fondy a nezahrnuje spravovanou instanci. |
| **Azure SQL**| – | To se týká řady SQL Serverch produktů databázového stroje, které jsou k dispozici v Azure: Azure SQL Database, Azure SQL Managed instance a SQL Server na virtuálních počítačích Azure. | 

## <a name="features-in-public-preview"></a>Funkce ve verzi Public Preview

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Funkce | Podrobnosti |
| ---| --- |
| Úlohy elastické databáze (Preview) | Informace najdete v tématu [vytváření, konfigurace a Správa elastických úloh](elastic-jobs-overview.md). |
| Elastické dotazy | Informace najdete v tématu [Přehled elastického dotazu](elastic-query-overview.md). |
| Elastické transakce | [Distribuované transakce v cloudových databázích](elastic-transactions-overview.md) |
| Editor dotazů v Azure Portal |Informace najdete v tématu [použití Editoru dotazů SQL Azure Portal k připojení a dotazování dat](connect-query-portal.md).|
|SQL Analytics|Informace najdete v tématu [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Spravovaná instance Azure SQL](#tab/managed-instance)

| Funkce | Podrobnosti |
| ---| --- |
| [Distribuované transakce](/azure/azure-sql/database/elastic-transactions-overview) | Distribuované transakce napříč spravovanými instancemi. |
| [Fondy instancí](/azure/sql-database/sql-database-instance-pools) | Pohodlný a cenově výhodný způsob migrace menších instancí SQL do cloudu. |
| [Instance objektů zabezpečení serveru Azure AD na úrovni instance (přihlášení)](/sql/t-sql/statements/create-login-transact-sql) | Vytvořte přihlašovací údaje na úrovni instance pomocí příkazu [vytvořit přihlášení z externího poskytovatele](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . |
| [Transakční replikace](../managed-instance/replication-transactional-overview.md) | Proveďte replikaci změn z tabulek do jiných databází ve spravované instanci SQL, SQL Database nebo SQL Server. Nebo aktualizujte tabulky, když dojde ke změně některých řádků v jiných instancích spravované instance SQL nebo SQL Server. Informace najdete v tématu [Konfigurace replikace ve spravované instanci Azure SQL](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Detekce hrozeb |Informace najdete v tématu [konfigurace detekce hrozeb ve spravované instanci Azure SQL](../managed-instance/threat-detection-configure.md).|
| Dlouhodobé uchovávání záloh | Informace najdete v tématu [Konfigurace dlouhodobého uchovávání záloh ve spravované instanci Azure SQL](../managed-instance/long-term-backup-retention-configure.md), která je aktuálně ve verzi Public Preview s omezením. |

---

## <a name="new-features"></a>Nové funkce

### <a name="sql-managed-instance-h2-2019-updates"></a>Aktualizace spravované instance SQL H2 2019

- [Konfigurace podsítě s podporou služby](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) je bezpečný a pohodlný způsob, jak spravovat konfiguraci podsítě, ve které řídíte přenos dat, zatímco služba SQL Managed instance zajišťuje nepřetržitý tok provozu správy.
- [Transparentní šifrování dat (TDE) s Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) umožňuje použít scénář BYOK (Přineste si vlastní klíč) pro ochranu dat v klidovém umístění a umožňuje organizacím oddělit povinnosti správy pro klíče a data.
- [Skupiny s automatickým převzetím služeb při selhání](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umožňují replikovat všechny databáze z primární instance do sekundární instance v jiné oblasti.
- [Příznaky globálního trasování](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) umožňují konfigurovat chování spravované instance SQL.

### <a name="sql-managed-instance-h1-2019-updates"></a>Aktualizace spravované instance SQL H1 2019

V modelu nasazení Managed instance SQL ve H1 2019 jsou povolené následující funkce:
  - Podpora předplatných s <a href="/azure/azure-sql/managed-instance/resource-limits"> měsíčním kreditem Azure pro předplatitele sady Visual Studio </a> a zvýšená [regionální omezení](../managed-instance/resource-limits.md#regional-resource-limitations).
  - Podpora pro <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> sharepoint 2016 a sharepoint 2019 </a> a <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central. </a>
  - Vytvořte spravovanou instanci s <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">kolací na úrovni instance</a> a zvoleným <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">časovým pásmem</a> .
  - Spravované instance jsou teď chráněné pomocí [integrované brány firewall](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Nakonfigurujte spravovanou instanci SQL tak, aby používala [veřejné koncové body](../managed-instance/public-endpoint-configure.md), připojení [přepsání proxy serveru](connectivity-architecture.md#connection-policy) , aby se dosáhlo lepšího výkonu sítě, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 virtuální jádra na generaci hardwaru Gen5</a> nebo <a href="/azure/azure-sql/database/automated-backups-overview">Konfigurace uchovávání záloh až 35 dní</a> pro obnovení k bodu v čase. [Dlouhodobé uchovávání záloh](long-term-retention-overview.md) (až 10 let) je aktuálně ve verzi Public Preview.  
  - Nové funkce umožňují <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geograficky obnovit databázi do jiného datového centra pomocí prostředí PowerShell</a>, [Přejmenovat databázi](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [odstranit virtuální cluster](../managed-instance/virtual-cluster-delete.md).
  - Nová Vestavěná [role přispěvatele instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) umožňuje oddělení povinností (SOD) dodržovat zásady zabezpečení a dodržování předpisů v podnikových normách.
  - Spravovaná instance SQL je k dispozici v následujících oblastech Azure Government pro GA (US Gov – Texas, US Gov – Arizona) a v Čína – sever 2 a Čína – východ 2. Je také k dispozici v následujících veřejných oblastech: Austrálie – střed, Austrálie – střed 2, Brazílie – jih, Francie – jih, Spojené arabské emiráty střed, Spojené arabské emiráty sever, Jižní Afrika sever, Jižní Afrika – západ.

## <a name="known-issues"></a>Známé problémy

|Problém  |Datum zjištění  |Status  |Datum vyřešení  |
|---------|---------|---------|---------|
|[Procedura sp_send_dbmail může dojít k přechodnému selhání při @query použití parametru](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Leden 2021|Má alternativní řešení||
|[Distribuované transakce se dají provést po odebrání spravované instance ze skupiny důvěryhodných serverů.](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Říjen 2020|Má alternativní řešení||
|[Po operaci škálování spravované instance se nedají provést distribuované transakce.](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Říjen 2020|Má alternativní řešení||
|[Bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql) v Azure SQL a `BACKUP` / `RESTORE` příkazu ve spravované instanci nejde použít k ověření ve službě Azure Storage službu Azure AD Manage identity.|SEP 2020|Má alternativní řešení||
|[Instanční objekt nemá přístup k Azure AD a integrace](#service-principal-cannot-access-azure-ad-and-akv)|Srpna 2020|Má alternativní řešení||
|[Obnovení ručního zálohování bez KONTROLNÍho SOUČTu může selhat](#restoring-manual-backup-without-checksum-might-fail)|Květen 2020|Vyřešeno|Červen 2020|
|[Agent přestane reagovat při úpravách, zakázání nebo povolování stávajících úloh.](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Květen 2020|Vyřešeno|Červen 2020|
|[Oprávnění pro skupinu prostředků neplatí pro spravovanou instanci SQL.](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Únor 2020|Vyřešeno|Listopadu 2020|
|[Omezení ručního převzetí služeb při selhání prostřednictvím portálu pro skupiny převzetí služeb](#limitation-of-manual-failover-via-portal-for-failover-groups)|Leden 2020|Má alternativní řešení||
|[Role agenta SQL musí mít explicitní oprávnění SPUSTIT pro jiná přihlášení než správce systému](#in-memory-oltp-memory-limits-are-not-applied)|DEC 2019|Má alternativní řešení||
|[Úlohy agenta SQL je možné přerušit restartováním procesu agenta.](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|DEC 2019|Vyřešeno|Březen 2020|
|[Přihlášení a uživatelé Azure AD nejsou v SSDT podporované.](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Listopadu 2019|Žádné alternativní řešení||
|[Limity OLTP paměti v paměti se nepoužívají.](#in-memory-oltp-memory-limits-are-not-applied)|Říjen 2019|Má alternativní řešení||
|[Při pokusu o odebrání neprázdného souboru se vrátila chybná chyba.](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Říjen 2019|Má alternativní řešení||
|[Probíhající obnovení databáze blokuje změnu úrovně služby a operací vytváření instancí.](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|SEP 2019|Má alternativní řešení||
|[Po převzetí služeb při selhání může být potřeba změnit správce prostředků u Pro důležité obchodní informace úrovně služeb](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|SEP 2019|Má alternativní řešení||
|[Dialogová okna mezidatabázového Service Broker se musí po upgradu na úrovni služby znovu inicializovat.](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Srpna 2019|Má alternativní řešení||
|[Zosobnění typů přihlašovacích údajů Azure AD se nepodporuje.](#impersonation-of-azure-ad-login-types-is-not-supported)|Července 2019|Žádné alternativní řešení||
|[@query parametr není v sp_send_db_mail podporován.](#-parameter-not-supported-in-sp_send_db_mail)|Duben 2019|Vyřešeno|Leden 2021|
|[Po geografickém převzetí služeb při selhání je potřeba znovu nakonfigurovat transakční replikaci.](#transactional-replication-must-be-reconfigured-after-geo-failover)|Březen 2019|Žádné alternativní řešení||
|[Během operace obnovení se používá dočasná databáze.](#temporary-database-is-used-during-restore-operation)||Má alternativní řešení||
|[Struktura a obsah TEMPDB se znovu vytvoří.](#tempdb-structure-and-content-is-re-created)||Žádné alternativní řešení||
|[Překročení úložného prostoru s malými databázovými soubory](#exceeding-storage-space-with-small-database-files)||Má alternativní řešení||
|[Hodnoty GUID, které se zobrazují místo názvů databází](#guid-values-shown-instead-of-database-names)||Má alternativní řešení||
|[Protokoly chyb nejsou trvalé](#error-logs-arent-persisted)||Žádné alternativní řešení||
|[Obor transakce ve dvou databázích v rámci stejné instance není podporovaný.](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Má alternativní řešení|Březen 2020|
|[Moduly CLR a propojené servery někdy nemůžou odkazovat na místní IP adresu.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Má alternativní řešení||
|Po obnovení databáze z Azure Blob Storage konzistence databáze nebyla ověřena pomocí příkazu DBCC CHECKDB.||Vyřešeno|Listopadu 2019|
|Obnovení databáze v čase z Pro důležité obchodní informace úrovně do Pro obecné účely úrovně nebude úspěšné, pokud zdrojová databáze obsahuje objekty OLTP v paměti.||Vyřešeno|Říjen 2019|
|Funkce databázového e-mailu s externími poštovními servery (mimo Azure) pomocí zabezpečeného připojení||Vyřešeno|Říjen 2019|
|Obsažené databáze nejsou ve spravované instanci SQL podporovány.||Vyřešeno|Srpna 2019|

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Procedura sp_send_dbmail může dojít k přechodnému selhání při @query použití parametru

Procedura sp_send_dbmail může být při použití parametru přechodným selháním `@query` . Pokud k tomuto problému dojde, pokaždé, když se spustí procedura sp_send_dbmail, dojde k chybě `Msg 22050, Level 16, State 1` a zprávě `Failed to initialize sqlcmd library with error number -2147467259` . Aby bylo možné tuto chybu zobrazit správně, procedura by měla být volána s výchozí hodnotou 0 pro parametr `@exclude_query_output` , jinak nebude tato chyba šířena.
Tento problém je způsobený známou chybou, která souvisí s tím, jak sp_send_dbmail používá zosobnění a sdružování připojení.
Chcete-li tento problém obejít, zabalit kód pro odesílání e-mailů do logiky opakování, která spoléhá na výstupní parametr `@mailitem_id` . Pokud se spuštění nezdaří, hodnota parametru bude NULL, což značí, že by se pro úspěšné odeslání e-mailu měla zavolat sp_send_dbmail. Tady je příklad této logiky opakování.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Distribuované transakce se dají provést po odebrání spravované instance ze skupiny důvěryhodných serverů.

[Skupiny důvěryhodnosti serverů](../managed-instance/server-trust-group-overview.md) slouží k navázání vztahu důvěryhodnosti mezi spravovanými instancemi, které jsou předpokladem pro provádění [distribuovaných transakcí](./elastic-transactions-overview.md). Po odebrání spravované instance ze skupiny důvěryhodných serverů nebo odstranění skupiny stále budete moci provádět distribuované transakce. Existuje alternativní řešení, které můžete použít, abyste se ujistili, že jsou distribuované transakce zakázané a že je [uživatelem iniciované ruční převzetí služeb při selhání](../managed-instance/user-initiated-failover.md) ve spravované instanci.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Po operaci škálování spravované instance se nedají provést distribuované transakce.

Operace škálování spravované instance, které zahrnují změnu úrovně služby nebo počet virtuální jádra, obnoví nastavení skupiny důvěryhodných serverů na back-endu a zakáže spuštěné [distribuované transakce](./elastic-transactions-overview.md). Jako alternativní řešení odstraňte a vytvořte novou [skupinu důvěryhodných serverů](../managed-instance/server-trust-group-overview.md) na Azure Portal.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>Příkazy BULK INSERT a BACKUP/Restore nemůžou používat spravovanou identitu pro přístup k Azure Storage.

Příkazy hromadného vložení, zálohování a obnovení nelze použít `DATABASE SCOPED CREDENTIAL` se spravovanou identitou pro ověření v Azure Storage. Jako alternativní řešení přepněte na ověřování pomocí SDÍLENÉHO PŘÍSTUPového podpisu. Následující příklad nebude fungovat v Azure SQL (databáze i spravovaná instance):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Alternativní řešení**: k [ověření úložiště použijte sdílený přístupový podpis](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage).

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Instanční objekt nemá přístup k Azure AD a integrace

V některých případech může existovat problém s instančním objektem, který se používá pro přístup ke službám Azure AD a Azure Key Vault (integrace). Výsledkem je, že tento problém se týká využití ověřování Azure AD a transparentního šifrování databáze (TDE) pomocí spravované instance SQL. Může se jednat o problém s přerušovaným připojením nebo nemůže spustit příkazy, jako je vytvoření přihlášení/uživatele od externího poskytovatele nebo spuštění jako přihlašovací jméno nebo uživatel. Nastavení TDE s klíčem spravovaným zákazníkem na nové spravované instanci SQL Azure nemusí v některých případech fungovat.

**Alternativní řešení**: Pokud chcete zabránit tomu, aby k tomuto problému došlo na spravované instanci SQL před provedením jakýchkoli příkazů aktualizace, nebo pokud jste už tento problém nastavili po příkazech aktualizovat, přejděte na Azure Portal, přístup k [oknu Správce služby Active Directory](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal)spravované instance SQL. Ověřte, jestli se vám zobrazí chybová zpráva "spravovaná instance vyžaduje instanční objekt pro přístup k Azure Active Directory. Pokud chcete vytvořit instanční objekt, klikněte sem. Pokud jste narazili na tuto chybovou zprávu, klikněte na ni a postupujte podle podrobných pokynů, dokud se tato chyba nevyřeší.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>Obnovení ručního zálohování bez KONTROLNÍho SOUČTu může selhat

V některých případech se nemusí obnovit ruční zálohování databází, které byly vytvořeny na spravované instanci bez KONTROLNÍho SOUČTu. V takových případech zkuste zálohu znovu obnovit, dokud neproběhne úspěšně.

**Alternativní řešení**: proveďte ruční zálohování databází ve spravovaných instancích pomocí POVOLENého KONTROLNÍho součtu.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Agent přestane reagovat při úpravách, zakázání nebo povolování stávajících úloh.

Za určitých okolností může změna, zakázání nebo povolení existující úlohy způsobit, že agent přestane reagovat. Problém se při detekci automaticky sníží a výsledkem je restartování procesu agenta.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Oprávnění pro skupinu prostředků neplatí pro spravovanou instanci SQL.

Když se role Azure spravované instance SQL použije na skupinu prostředků (RG), nepoužije se na spravovanou instanci SQL a nemá žádný vliv.

**Alternativní řešení**: pro uživatele na úrovni předplatného nastavte roli Přispěvatel spravované instance SQL.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Omezení ručního převzetí služeb při selhání prostřednictvím portálu pro skupiny převzetí služeb

Pokud skupina převzetí služeb při selhání zahrnuje různé instance v různých předplatných Azure nebo skupinách prostředků, ruční převzetí služeb při selhání nejde iniciovat z primární instance ve skupině převzetí služeb při selhání.

**Alternativní řešení**: zahajte převzetí služeb při selhání prostřednictvím portálu z instance geografické sekundární instance.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Role agenta SQL musí mít explicitní oprávnění SPUSTIT pro jiná přihlášení než správce systému

Pokud se do jakékoli [pevné databázové role SQL Agent](/sql/ssms/agent/sql-server-agent-fixed-database-roles)přidají přihlášení jiného typu než sysadmin, existuje problém, ve kterém je potřeba udělit explicitní oprávnění ke spuštění hlavním uloženým procedurám, aby tato přihlášení fungovala. V případě výskytu tohoto problému se zobrazí chybová zpráva "oprávnění EXECUTE bylo odepřeno pro objekt <object_name> (Microsoft SQL Server, chyba: 229)".

**Alternativní řešení**: po přidání přihlašovacích údajů do pevné databázové role SQL agenta (SQLAgentUserRole, SQLAgentReaderRole nebo role SQLAgentOperatorRole) pro každé přihlášení přidané k těmto rolím spusťte níže uvedený skript T-SQL, který explicitně udělí oprávnění ke spouštění uložených procedurám uvedeným v seznamu.

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

**(Vyřešeno v březnu 2020)** SQL Agent vytvoří novou relaci při každém spuštění úlohy a postupně zvyšuje spotřebu paměti. Aby nedošlo k překročení limitu interní paměti, což by mohlo blokovat provádění plánovaných úloh, proces agenta se restartuje, jakmile jeho spotřeba dosáhne prahové hodnoty. Výsledkem může být přerušení provádění úloh spuštěných v okamžiku restartování.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Limity OLTP paměti v paměti se nepoužívají.

Úroveň služby Pro důležité obchodní informace nebude v některých případech správně používat [maximální limity paměti pro paměťově optimalizované objekty](../managed-instance/resource-limits.md#in-memory-oltp-available-space) . Spravovaná instance SQL může povolit zatížení pro využití více paměti pro OLTP operace v paměti, což může mít vliv na dostupnost a stabilitu instance. Dotazy OLTP v paměti, které dosáhnou limitu, nemusí okamžitě selhat. Tento problém bude brzy vyřešen. Dotazy, které používají více paměti OLTP v paměti, selžou dříve, pokud dosáhnou [omezení](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Alternativní řešení**: [sledujte využití úložiště v paměti OLTP](../in-memory-oltp-monitor-space.md) pomocí [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) a ujistěte se, že zatížení nevyužívá více než dostupnou paměť. Zvyšte limit paměti, který závisí na počtu virtuální jádra, nebo Optimalizujte úlohy tak, aby používaly méně paměti.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Při pokusu o odebrání neprázdného souboru se vrátila chybná chyba.

SQL Server a spravované instance SQL [neumožňují uživateli vyřadit neprázdný soubor](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Pokud se pokusíte odebrat neprázdný datový soubor pomocí `ALTER DATABASE REMOVE FILE` příkazu, chyba se `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` okamžitě nevrátí. Spravovaná instance SQL bude pokračovat v pokusu o vyřazení souboru a operace skončí po 30 minutách od `Internal server error` .

**Alternativní řešení**: Odeberte obsah souboru pomocí `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` příkazu. Pokud se jedná o jediný soubor ve skupině souborů, budete muset před zmenšením souboru odstranit data z tabulky nebo oddílu přidruženého k této skupině souborů a volitelně načíst tato data do jiné tabulky nebo oddílu.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Probíhající obnovení databáze blokuje změnu úrovně služby a operací vytváření instancí.

Průběžný `RESTORE` příkaz, proces migrace dat a integrované obnovení k určitému bodu v čase zablokuje aktualizaci úrovně služby nebo změny velikosti existující instance a vytváření nových instancí až do dokončení procesu obnovení. 

Proces obnovení bude blokovat tyto operace ve spravovaných instancích a fondech instancí ve stejné podsíti, ve které je proces obnovení spuštěn. Instance v fondech instancí nejsou ovlivněny. Operace vytvoření nebo změny vrstvy služeb nebudou úspěšné nebo vypršel časový limit. Po dokončení nebo zrušení procesu obnovení budou pokračovat.

**Alternativní řešení**: Počkejte na dokončení procesu obnovení, nebo zrušte proces obnovení, pokud má operace vytvoření nebo aktualizace na úrovni služby vyšší prioritu.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Po převzetí služeb při selhání může být potřeba změnit správce prostředků u Pro důležité obchodní informace úrovně služeb

Funkce [Správce prostředků](/sql/relational-databases/resource-governor/resource-governor) , která umožňuje omezit prostředky přiřazené k úloze uživatele, může po převzetí služeb při selhání nebo na základě uživatelsky iniciované změny úrovně služby (například změna maximální velikosti úložiště Vcore nebo maxima instance úložiště) omezit jejich zatížení.

**Alternativní řešení**: spouštějte `ALTER RESOURCE GOVERNOR RECONFIGURE` pravidelně nebo jako součást úlohy agenta SQL, která spustí úlohu SQL při spuštění instance, pokud používáte [Správce zdrojů](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Dialogová okna mezidatabázového Service Broker se musí po upgradu na úrovni služby znovu inicializovat.

Dialogy Service Broker mezi databázemi ukončí doručování zpráv do služeb v jiných databázích po provedení operace změny úrovně služby. Zprávy nejsou *ztraceny* a lze je najít ve frontě odesílatele. Jakákoli změna velikosti úložiště virtuální jádra nebo instance ve spravované instanci SQL způsobí, že se `service_broke_guid` v zobrazení [Sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) změní hodnota pro všechny databáze. Všechny `DIALOG` vytvořené pomocí příkazu [Begin dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , který odkazuje na zprostředkovatele služby v jiné databázi, přestanou doručování zpráv cílové službě.

**Alternativní řešení**: před aktualizací vrstvy služeb zastavte všechny aktivity, které používají konverzaci mezi Service Brokermi databázemi, a potom je znovu inicializujte. Pokud jsou zbývající zprávy nedoručeny po změně úrovně služby, přečtěte si zprávy ze zdrojové fronty a znovu je odešlete do cílové fronty.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Zosobnění typů přihlašovacích údajů Azure AD se nepodporuje.

Zosobnění pomocí `EXECUTE AS USER` nebo `EXECUTE AS LOGIN` z následujících objektů zabezpečení Azure Active Directory (Azure AD) není podporované:
-   Uživatelé Azure AD s aliasy V tomto případě se vrátí následující chyba: `15517` .
- Přihlašovací údaje a uživatele služby Azure AD založené na aplikacích nebo instančních objektech Azure AD. V tomto případě se vrátí následující chyby: `15517` a `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parametr není v sp_send_db_mail podporován.

`@query`Parametr v proceduře [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) nefunguje.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Po geografickém převzetí služeb při selhání je potřeba znovu nakonfigurovat transakční replikaci.

Pokud je transakční replikace povolená v databázi ve skupině automatického převzetí služeb při selhání, musí správce spravované instance SQL vyčistit všechny publikace na staré primární primární databázi a po převzetí služeb při selhání do jiné oblasti je znovu nakonfigurovat na nové primární úrovni. Další informace najdete v tématu [replikace](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Přihlášení a uživatelé Azure AD nejsou v SSDT podporované.

Nástroje SQL Server Data Tools plně nepodporují přihlášení a uživatele služby Azure AD.

### <a name="temporary-database-is-used-during-restore-operation"></a>Během operace obnovení se používá dočasná databáze.

Při obnovování databáze ve spravované instanci SQL vytvoří služba obnovení nejprve prázdnou databázi s požadovaným názvem k přidělení názvu v instanci. Po určité době bude tato databáze vyřazena a bude spuštěno obnovení skutečné databáze. 

Databáze, ve které je *obnoven stav obnovení* , bude dočasně mít náhodnou hodnotu identifikátoru GUID místo názvu. Po dokončení procesu obnovení bude dočasný název změněn na požadovaný název uvedený v `RESTORE` příkazu. 

V úvodní fázi může uživatel získat přístup k prázdné databázi a dokonce vytvořit tabulky nebo načíst data v této databázi. Tato dočasná databáze se vynechá, když služba obnovení spustí druhou fázi.

**Alternativní řešení**: Neprovádějte přístup k databázi, kterou obnovujete, dokud neuvidíte, že obnovení bylo dokončeno.

### <a name="tempdb-structure-and-content-is-re-created"></a>Struktura a obsah TEMPDB se znovu vytvoří.

`tempdb`Databáze je vždy rozdělena do 12 datových souborů a struktura souborů nemůže být změněna. Maximální velikost na soubor nelze změnit a nelze přidat nové soubory do `tempdb` . `Tempdb` je vždy znovu vytvořen jako prázdná databáze při spuštění nebo převzetí služeb při selhání a jakékoli změny provedené v nezůstanou `tempdb` zachovány.

### <a name="exceeding-storage-space-with-small-database-files"></a>Překročení úložného prostoru s malými databázovými soubory

`CREATE DATABASE``ALTER DATABASE ADD FILE`příkazy, a `RESTORE DATABASE` mohou selhat, protože instance může dosáhnout limitu Azure Storage.

Každá Pro obecné účely instance spravované instance SQL má kapacitu úložiště až 35 TB vyhrazenou pro místo na disku Azure Premium. Každý databázový soubor je umístěn na samostatném fyzickém disku. Velikosti disků můžou být 128 GB, 256 GB, 512 GB, 1 TB nebo 4 TB. Nevyužité místo na disku se neúčtuje, ale celkový součet velikostí disků Azure Premium nesmí překročit 35 TB. V některých případech může spravovaná instance, která nepotřebuje 8 TB celkem, překročit 35 TB Azure na velikost úložiště kvůli vnitřní fragmentaci.

Například Pro obecné účely instance spravované instance SQL může mít jeden velký soubor o velikosti 1,2 TB umístěný na 4 TB disku. Také může mít 248 souborů o velikosti 1 GB, které jsou umístěny na samostatných discích 128-GB. V tomto příkladu:

- Celková přidělená velikost diskového úložiště je 1 × 4 TB + 248 × 128 GB = 35 TB.
- Celkové rezervované místo pro databáze v instanci je 1 × 1,2 TB + 248 × 1 GB = 1,4 TB.

Tento příklad ukazuje, že za určitých okolností, kvůli konkrétní distribuci souborů, může instance spravované instance SQL dosáhnout limitu 35-TB, který je vyhrazený pro připojený disk Azure Premium, pokud ho neočekáváte.

V tomto příkladu existující databáze fungují i nadále a můžou růst bez jakýchkoli potíží, pokud se nepřidá nové soubory. Nové databáze nelze vytvořit ani obnovit, protože není dostatek místa pro nové diskové jednotky, ani v případě, že celková velikost všech databází nedosáhne limitu velikosti instance. Chyba, která se vrátí v tomto případě, není jasná.

[Počet zbývajících souborů můžete identifikovat](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) pomocí systémových zobrazení. Pokud dosáhnete tohoto limitu, zkuste [vyprázdnit a odstranit některé menší soubory pomocí příkazu DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) nebo přepněte na [pro důležité obchodní informaceovou vrstvu, která nemá toto omezení](../managed-instance/resource-limits.md#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Hodnoty GUID, které se zobrazují místo názvů databází

Několik systémových zobrazení, čítače výkonu, chybové zprávy, XEvents a položky protokolu chyb zobrazují identifikátory databáze identifikátorů GUID místo skutečných názvů databází. Nespoléhejte na tyto identifikátory GUID, protože se v budoucnu nahrazují skutečnými názvy databází.

**Alternativní řešení**: pomocí zobrazení sys. databases vyřešte skutečný název databáze z fyzického názvu databáze, který je určený ve formě identifikátorů databáze identifikátorů GUID:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Protokoly chyb nejsou trvalé

Protokoly chyb, které jsou k dispozici ve spravované instanci SQL, nejsou trvalé a jejich velikost není zahrnutá do maximálního limitu úložiště. Pokud dojde k převzetí služeb při selhání, můžou se protokoly chyb automaticky vymazat. V historii protokolu chyb můžou být mezery, protože spravovaná instance SQL se na několika virtuálních počítačích přesunula několikrát.

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

**Alternativní řešení (nepotřebné od března 2020)**: pomocí [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) použijte k použití jiné databáze v kontextu připojení místo použití dvou připojení.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Moduly CLR a propojené servery někdy nemůžou odkazovat na místní IP adresu.

Moduly CLR v SQL Managed instance a odkazovaných serverech nebo distribuovaných dotazech, které odkazují na aktuální instanci, někdy nemůžou přeložit IP adresu místní instance. Tato chyba je přechodný problém.

**Alternativní řešení**: Pokud je to možné, použijte připojení kontextu v modulu CLR.

## <a name="updates"></a>Aktualizace

Seznam SQL Database aktualizací a vylepšení najdete v tématu [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Aktualizace a vylepšení pro všechny služby Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Příspěvky k obsahu

Informace o tom, jak přispět k dokumentaci k Azure SQL, najdete v [příručce pro přispěvatele dokumentů](/contribute/).
