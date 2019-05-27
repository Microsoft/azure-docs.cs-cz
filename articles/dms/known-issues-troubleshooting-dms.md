---
title: Článek o řešení potíží s běžné problémy a chyby spojené s využitím Azure Database Migration Service – známé | Dokumentace Microsoftu
description: Další informace o tom, jak řešit běžné známé problémy a chyby spojené s využitím Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 5a7c6c4553f46e8a7308995e05d6c06c0eb10f27
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002214"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Řešení běžných problémů služby Azure Database Migration Service a chyb

Tento článek popisuje některé běžné problémy a chyby, které uživatelé Azure Database Migration Service můžete setkat. Tento článek také obsahuje informace o tom, jak vyřešit tyto problémy a chyby.

## <a name="migration-activity-in-queued-state"></a>Aktivita migrace ve stavu zařazení do fronty

Když vytvoříte nové aktivity v projektu Azure Database Migration Service, aktivity zůstanou ve stavu zařazení do fronty.

| Příčina         | Řešení |
| ------------- | ------------- |
| Tento problém nastane, pokud instance služby Azure Database Migration Service dosáhl maximální kapacity pro probíhající úlohy, které běží souběžně. Všechny nové aktivity je ve frontě, dokud nebude k dispozici kapacitu. | Ověření služby migrace dat instance má spouštění aktivit ve všech projektech. Můžete pokračovat k vytvoření nové aktivity, které se automaticky přidají do fronty ke spuštění. Poté, co bylo možné provést některý z existujících spouštění aktivit, spuštění další aktivity ve frontě a stav se změní na běžel automaticky. Není nutné provádět žádnou další akci pro zahájení migrace ve frontě aktivity.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximální počet databází vybraných pro migraci

Při vytvoření aktivity pro projekt migrace databáze pro přesun do Azure SQL Database nebo Azure SQL Database managed instance, dojde k následující chybě:

* **Chyba:** Chyba ověření nastavení migrace","errorDetail":"více než maximální počet "4" objekty 'Databází' byla vybrána pro migraci."

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba zobrazí, když jste vybrali víc než čtyř databází pro aktivitu jedna migrace. V současné době je omezená na čtyři databáze každou aktivitu migrace. | Vyberte čtyři nebo méně databází na aktivitu migrace. Pokud potřebujete více než čtyři provést paralelní migraci databází, zřídíte další instanci služby Azure Database Migration Service. V současné době každé předplatné jich podporuje až dvě instance služby Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Chyby při migraci MySQL do Azure MySQL s selhání obnovení

Při migraci z databáze MySQL do služby Azure Database for MySQL pomocí Azure Database Migration Service, aktivita migrace selže s následující chybou:

* **Chyba:** Chyba migrace databáze - úkolu "TaskID" bylo pozastaveno z důvodu chyby při obnovení po sobě jdoucích [n].

| Příčina         | Řešení |
| ------------- | ------------- |
| Této chybě může dojít, když uživatel migrací neobsahuje roli ReplicationAdmin a/nebo oprávnění REPLICATION CLIENT, REPLICATION REPLICA a SUPER (verze starší než MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Ujistěte se, [požadovaného oprávnění](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) pro uživatele se přesně nakonfigurovaný účet v Azure Database for MySQL – instance. Například následující postup platí pro vytvoření uživatele s názvem "migrateuser' požadovaná oprávnění:<br>1. Vytvořit uživatele migrateuser@'% "IDENTIFIKOVAT podle"secret"; <br>2. Udělení oprávnění všem na db_name.* k migrateuser'@'% identifikovaný "secret"; Opakujte tento krok pro udělení přístupu pro další databáze <br>3. Podřízený server udělení replikace na *.* k migrateuser'@'% identifikovaný "secret";<br>4. Udělení klientských replikace na *.* k migrateuser'@'% identifikovaný "secret";<br>5. Vyprázdnění oprávnění; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Chyba při pokusu o zastavení služby Azure Database Migration Service

Zobrazí se následující chyba při zastavování instancí služby Azure Database Migration Service:

* **Chyba:** Službě se nepodařilo zastavit. Chyba: {"Chyba": {"kód": "InvalidRequest", "zpráva": "aktuálně běží jedna nebo více aktivit. K zastavení služby, počkejte, dokud aktivity byly dokončeny nebo zastavte tyto aktivity ručně a zkuste to znovu. "}}

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba zobrazí, když instance služby, které vám při pokusu o zastavení obsahuje aktivity, které jsou pořád spuštěné nebo k dispozici v projektech pro migraci. <br><br><br><br><br><br> | Ujistěte se, že neexistují žádné aktivity spuštěné v instanci služby Azure Database Migration Service se snažíte zastavit. Můžete také odstranit aktivity nebo projekty před pokusem o zastavení služby. Následující kroky ukazují, jak odebrat projekty k vyčištění instance služby migrace tak, že odstraníte všechny spuštěné úlohy:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription - SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Chyba při obnovování databáze během migrace SQL ke službě Azure SQL DB mi

Když provádíte online migrace z SQL serveru do spravované instance Azure SQL Database, a jeho sdělení ostatním selže s následující chybou:

* **Chyba:** Pro Id 'operationId' operace se nepovedla operace obnovení. Kód "AuthorizationFailed" zpráva "klient 'clientId' s id objektu 'ID objektu' nemá autorizaci k provedení akce"Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read"rozsahu" /subscriptions/ subscriptionId'. '.

| Příčina         | Řešení    |
| ------------- | ------------- |
| Tato chyba označuje objektu zabezpečení aplikace, které se používají pro online migraci z SQL serveru do spravované instance Azure SQL Database mají nepřičítá oprávnění v rámci předplatného. Volání určitých rozhraní API s Managed Instance v současné době vyžadují toto oprávnění u předplatného pro operaci obnovení. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Použití `Get-AzureADServicePrincipal` rutiny prostředí PowerShell s `-ObjectId` z chybové zprávy k seznamu zobrazovaný název ID aplikace se používají k dispozici.<br><br> Ověření oprávnění k této aplikaci a ujistěte se má [role Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) na úrovni předplatného. <br><br> Azure Database Migration služby technický tým pracuje na omezení požadované přispívat přístup z aktuální roli v předplatném. Pokud máte obchodním požadavkem, který neumožňuje použití přispívat role, pokud potřebujete další pomoc požádejte podporu Azure. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Chyba při odstraňování síťové rozhraní přidružené k Azure Database Migration Service

Při pokusu o odstranění karty síťového rozhraní přidružené k Azure Database Migration Service, pokus o odstranění selže s touto chybou:

* **Chyba:** Nelze odstranit síťové rozhraní přidružené k Azure Database Migration Service z důvodu službu DMS využitím síťového adaptéru

| Příčina         | Řešení    |
| ------------- | ------------- |
| Tento problém se stane, když instance služby Azure Database Migration Service může stále existovat a pracovat s nimi síťové rozhraní <br><br><br><br><br><br><br><br> | Pokud chcete odstranit tuto síťovou kartu, odstraňte instanci služby DMS, který automaticky odstraní síťové karty používané službou.<br><br> **Důležité**: Ujistěte se, že instance služby Azure Database Migration Service odstraňuje nemá žádné běžící aktivity.<br><br> Po odstranění se všechny projekty a aktivity přidružené k instanci Azure Database Migration Service, můžete odstranit instance služby. Odstraňuje se služba v rámci je automaticky vymazána síťová karta použitá instancí služby. |

## <a name="connection-error-when-using-expressroute"></a>Chyba připojení při používání ExpressRoute

Při pokusu o připojení ke zdroji v Průvodci projekt Azure Database Migration service, připojení selže po delší časový limit Pokud zdroj používá pro připojení k ExpressRoute.

| Příčina         | Řešení    |
| ------------- | ------------- |
| Při použití [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [vyžaduje](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) tři koncových bodů služby v podsíti virtuální sítě, které jsou přidružená ke službě zřizování:<br> --Koncový bod služby Service Bus<br> --Koncový bod služby storage<br> --Cílit na koncový bod databázového (např. koncový bod SQL, koncového bodu služby Cosmos DB)<br><br><br><br><br> | [Povolit](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) koncové body služby vyžaduje pro připojení ExpressRoute mezi zdrojem a služba Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Chyba časového limitu při migraci databáze MySQL do Azure MySQL

Při migraci databáze MySQL do Azure Database for MySQL instanci prostřednictvím služby Azure Database Migration Service se migrace nezdaří s následující chybou vypršení časového limitu:

* **Chyba:** Chyba migrace databáze - Nepodařilo se načíst soubor - nepodařilo se spustit proces načítání souboru n RetCode: SQL_ERROR SqlState: HY000 NativeError: Zpráva 1205: [MySQL] [ovladač ODBC] [mysqld] zámek čekání vypršel časový limit; Zkuste restartovat transakce

| Příčina         | Řešení    |
| ------------- | ------------- |
| Tato chyba nastane, pokud migrace selže kvůli vypršení časového limitu čekání zámek během migrace. | Zvažte zvýšení hodnoty parametru server **"innodb_lock_wait_timeout"**. Nejvyšší povolená hodnota je 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Chyba při připojování ke zdroji systému SQL Server při používání dynamických portů nebo pojmenované instance

Při pokusu o připojení Azure Database Migration Service zdroje SQL Server, který běží na pojmenovanou instanci nebo dynamický port, připojení selže s touto chybou:

* **Chyba**: -1 - připojení SQL selhalo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a, SQL Server je nakonfigurován pro povolit vzdálená připojení. (poskytovatel: Rozhraní sítě systému SQL, chyba: 26 - Chyba při vyhledávání zadaného serveru či Instance)

| Příčina         | Řešení    |
| ------------- | ------------- |
| Tento problém nastane, když zdrojové instanci SQL serveru, který se pokusí připojit k Azure Database Migration Service má dynamický port nebo používá pojmenovanou instanci. Služba SQL Server Browser naslouchá na port UDP 1434 pro příchozí připojení k pojmenované instanci nebo při použití dynamický port. Dynamický port může změnit pokaždé, když služba SQL Server se restartuje. Můžete zkontrolovat dynamický port přiřazen k instanci prostřednictvím konfigurace sítě v nástroji SQL Server Configuration Manager.<br><br><br> |Ověřte, že služba Azure Database Migration Service můžete připojit k zdroji službu SQL Server Browser na UDP port 1434 a instanci systému SQL Server prostřednictvím dynamicky přiřazeného portu TCP podle potřeby. |

## <a name="additional-known-issues"></a>Další známé problémy

* [Známé problémy a migrace omezení online migraci do Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Známé problémy a migrace omezení online migrace do služby Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Známé problémy a migrace omezení online migrace do služby Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Další postup

* Zobrazit článek [prostředí PowerShell Azure Database Migration Service](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Zobrazit článek [postupy konfigurace parametrů serveru ve službě Azure Database for MySQL pomocí webu Azure portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Zobrazit článek [přehled požadavků na používání Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobrazit [– nejčastější dotazy o použití Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
