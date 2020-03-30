---
title: Běžné problémy – služba migrace databáze Azure
description: Přečtěte si, jak řešit běžné známé problémy nebo chyby spojené s používáním služby Migrace databáze Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649103"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Poradce při potížích s běžnými problémy a chybami služby Migrace databáze Azure

Tento článek popisuje některé běžné problémy a chyby, které uživatelé služby Migrace databáze Azure mohou narazit. Článek také obsahuje informace o tom, jak tyto problémy a chyby vyřešit.

## <a name="migration-activity-in-queued-state"></a>Aktivita migrace ve stavu ve frontě

Při vytváření nových aktivit v projektu služby migrace databáze Azure zůstanou aktivity ve stavu ve frontě.

| Příčina         | Řešení |
| ------------- | ------------- |
| K tomuto problému dochází, když instance služby migrace databáze Azure dosáhla maximální kapacity pro probíhající úlohy, které souběžně spustit. Všechny nové aktivity jsou zařazeny do fronty, dokud nebude kapacita k dispozici. | Ověření instance služby migrace dat má spuštěné aktivity napříč projekty. Můžete pokračovat ve vytváření nových aktivit, které se automaticky přidají do fronty pro spuštění. Jakmile se dokončí některá z existujících spuštěných aktivit, spustí se další aktivita ve frontě a stav se automaticky změní na stav spuštění. K zahájení migrace aktivity ve frontě nemusíte provádět žádné další akce.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximální počet databází vybraných pro migraci

Při vytváření aktivity pro projekt migrace databáze pro přesun do azure sql database nebo instance spravované službou Azure SQL Database dojde k následující chybě:

* **Chyba**: Chyba ověření nastavení migrace", "errorDetail":"Pro migraci bylo vybráno více než maximální číslo '4' objektů "Databáze".

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, pokud jste vybrali více než čtyři databáze pro jednu aktivitu migrace. V současné době je každá migrace omezena na čtyři databáze. | Vyberte čtyři nebo méně databází na jednu aktivitu migrace. Pokud potřebujete migrovat více než čtyři databáze paralelně, zřídit jinou instanci služby Migrace databáze Azure. V současné době každé předplatné podporuje až dvě instance služby Migrace databáze Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Chyby pro migraci MySQL do Azure MySQL s selháním obnovení

Při migraci z MySQL do databáze Azure pro MySQL pomocí služby migrace do databáze Azure se aktivita migrace nezdaří s následující chybou:

* **Chyba**: Chyba migrace databáze – Úloha TaskID byla pozastavena z důvodu [n] následných selhání obnovení.

| Příčina         | Řešení |
| ------------- | ------------- |
| K této chybě může dojít, pokud uživateli, který provádí migraci, chybí role ReplicationAdmin nebo oprávnění replikačního klienta, replikace replikace a super (verze starší než MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Ujistěte se, [že požadovaná oprávnění](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) pro uživatelský účet jsou nakonfigurovány přesně na azure databáze pro mysql instance. Následující kroky lze například provést k vytvoření uživatele s názvem "migrateuser" s požadovanými oprávněními:<br>1. VYTVOŘIT UŽIVATELE migrateuser@"%" identifikovaných "tajným"; <br>2. Udělit všechna oprávnění na db_name.* "migrateuser'@'%' označeného "tajným tajemstvím"; opakujte tento krok, abyste udělili přístup k více databázím <br>3. Udělit replikaci slave na *.* "migrateuser'@'%" označeného "tajným";<br>4. Udělit klienta replikace *v* . "migrateuser'@'%" označeného "tajným";<br>5. Flush oprávnění; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Při pokusu o zastavení služby migrace databáze Azure došlo k chybě.

Při zastavení instance služby Migrace databáze Azure se zobrazí následující chyba:

* **Chyba**: Službě se nepodařilo zastavit. Chyba: {'error':{'code':'InvalidRequest','message':'Aktuálně běží jedna nebo více aktivit. Chcete-li službu zastavit, počkejte, dokud aktivity nebudou dokončeny, nebo ručně ukončit tyto aktivity a akci opakujte.'}}

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, když instance služby, kterou se pokoušíte zastavit, zahrnuje aktivity, které jsou stále spuštěny nebo se nacházejí v projektech migrace. <br><br><br><br><br><br> | Ujistěte se, že v instanci služby migrace databáze Azure, kterou se pokoušíte zastavit, neběží žádné aktivity. Můžete také odstranit aktivity nebo projekty před pokusem o zastavení služby. Následující kroky ilustrují, jak odebrat projekty k vyčištění instance služby migrace odstraněním všech spuštěných úloh:<br>1. Instalační modul -Název AzureRM.DataMigration <br>2. Přihlašovací účet AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4. Remove-AzureRmDataMigrationProject \<-Name název projektu \<název> -ResourceGroupName rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Při pokusu o spuštění služby Migrace databáze Azure došlo k chybě.

Při spuštění instance služby Migrace databáze Azure se zobrazí následující chyba:

* **Chyba**: Spuštění služby se nezdaří. Chyba: {'errorDetail':'Služba se nepodařilo spustit, obraťte se na podporu společnosti Microsoft'}

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, když předchozí instance interně selhala. K této chybě dochází zřídka a technický tým si je vědom. <br> | Odstraňte instanci služby, kterou nelze spustit, a potom zřiďte novou, která ji nahradí. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Při migraci spravované instance SQL DB spravované ho došlo k chybě při migraci instance spravované sql db.

Při provádění online migrace z SQL Serveru do instance spravované službou Azure SQL Database se přeplává hodnota nezdaří s následující chybou:

* **Chyba**: Obnovení operace se nezdařilo pro id operace "operationId". Kód AuthorizationFailed, zpráva "Client Id" s id objektu 'objectId' nemá oprávnění k provedení akce Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read v oboru '/subscriptions/subscriptioni/subscriptionId'.'.

| Příčina         | Řešení    |
| ------------- | ------------- |
| Tato chyba označuje, že objekt zabezpečení aplikace používaný pro online migraci z SQL Serveru do spravované instance Azure SQL Database nemá oprávnění k příspěvku k předplatnému. Některá volání rozhraní API se spravovanou instancí v současné době vyžadují toto oprávnění pro odběr pro operaci obnovení. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Pomocí `Get-AzureADServicePrincipal` rutiny prostředí PowerShell, která `-ObjectId` je k dispozici v chybové zprávě, uveďte zobrazovaný název používaného ID aplikace.<br><br> Ověřte oprávnění k této aplikaci a ujistěte se, že má [roli přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) na úrovni předplatného. <br><br> Inženýrský tým služby Migrace databáze Azure pracuje na omezení požadovaného přístupu z aktuální role contribute v předplatném. Pokud máte obchodní požadavek, který neumožňuje použití role contribute, obraťte se na podporu Azure pro další pomoc. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Při odstraňování nic přidruženého ke službě migrace databáze Azure došlo k chybě.

Při pokusu o odstranění karty síťového rozhraní přidružené ke službě migrace databáze Azure se pokus o odstranění nezdaří s touto chybou:

* **Chyba:** Nelze odstranit nic přidruženou ke službě Migrace databáze Azure z důvodu služby DMS využívající nic

| Příčina         | Řešení    |
| ------------- | ------------- |
| K tomuto problému dochází, když instance služby migrace databáze Azure může být stále k dispozici a spotřebovává nic. <br><br><br><br><br><br><br><br> | Chcete-li odstranit tuto nic, odstraňte instanci služby DMS, která automaticky odstraní nic používanou službou.<br><br> **Důležité:** Ujistěte se, že instance služby migrace databáze Azure, která se odstraňuje, nemá žádné spuštěné aktivity.<br><br> Po odstranění všech projektů a aktivit přidružených k instanci služby Migrace databáze Azure můžete odstranit instanci služby. Nic používané instance služby je automaticky vyčištěna jako součást odstranění služby. |

## <a name="connection-error-when-using-expressroute"></a>Chyba připojení při použití ExpressRoute

Pokud se pokoušíte připojit ke zdroji v průvodci projektem služby Azure Database Migration Service a pokud zdroj k připojení využívá ExpressRoute, připojení po uplynutí prodlouženého časového limitu selže.

| Příčina         | Řešení    |
| ------------- | ------------- |
| Při použití [ExpressRoute](https://azure.microsoft.com/services/expressroute/) [vyžaduje](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) služba Migrace databáze Azure zřízení tří koncových bodů služby v podsíti virtuální sítě přidružené ke službě:<br> -- Koncový bod sběrnice service bus<br> -- Koncový bod úložiště<br> -- Cílový koncový bod databáze (např. koncový bod SQL, koncový bod Cosmos DB)<br><br><br><br><br> | [Povolte](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) koncové body požadované služby pro připojení ExpressRoute mezi zdrojem a službou Migrace databáze Azure. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Při migraci databáze MySQL do Azure DB pro MySQL došlo k chybě časového času uzamčení časového času čekání

Při migraci databáze MySQL do instance Databáze Azure pro MySQL prostřednictvím služby Migrace databáze Azure se migrace nezdaří s následující chybou časového času čekání zámku:

* **Chyba**: Chyba migrace databáze - Nepodařilo se načíst soubor - Nepodařilo se spustit proces načítání souboru 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Zpráva: [MySQL][ODBC Driver][mysqld] Časový limit čekání zámku byl překročen; zkuste restartovat transakci

| Příčina         | Řešení    |
| ------------- | ------------- |
| K této chybě dochází, když se migrace nezdaří z důvodu časového omezení čekání zámku během migrace. | Zvažte zvýšení hodnoty parametru serveru **'innodb_lock_wait_timeout'**. Nejvyšší povolená hodnota je 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Při použití dynamického portu nebo pojmenované instance došlo k chybě při připojování ke zdrojovému serveru SQL Server.

Při pokusu o připojení služby Migrace databáze Azure ke zdroji SERVERU SQL Server, který běží na pojmenované instanci nebo dynamickém portu, se připojení nezdaří s touto chybou:

* **Chyba**: -1 - Připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je server SQL Server nakonfigurován tak, aby umožňoval vzdálená připojení. (poskytovatel: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)

| Příčina         | Řešení    |
| ------------- | ------------- |
| K tomuto problému dochází, když zdrojová instance SERVERU SQL Server, ke které se služba Migrace databáze Azure pokusí připojit, má dynamický port nebo používá pojmenovanou instanci. Služba SQL Server Browser naslouchá portu UDP 1434 pro příchozí připojení k pojmenované instanci nebo při použití dynamického portu. Dynamický port se může změnit při každém restartování služby serveru SQL Server. Dynamický port přiřazený instanci můžete zkontrolovat prostřednictvím konfigurace sítě ve správci konfigurace serveru SQL Server.<br><br><br> |Ověřte, zda se služba Migrace databáze Azure může připojit ke zdrojové službě SQL Server Browser na portu UDP 1434 a instanci SERVERU SQL Server prostřednictvím dynamicky přiřazeného portu TCP. |

## <a name="additional-known-issues"></a>Další známé problémy

* [Známé problémy a omezení migrace s online migrací do Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Známé problémy/omezení migrace s online migrací do databáze Azure pro MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Známé problémy/omezení migrace s online migrací do databáze Azure pro PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Další kroky

* Podívejte se na článek [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Podívejte se na článek [Jak nakonfigurovat parametry serveru v Azure Database for MySQL pomocí portálu Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Prohlédněte si článek [Přehled předpokladů pro používání služby Migrace databáze Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Podívejte se na [nejčastější dotazy týkající se používání služby Migrace databáze Azure](https://docs.microsoft.com/azure/dms/faq).
