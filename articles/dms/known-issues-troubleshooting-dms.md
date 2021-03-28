---
title: Běžné problémy – Azure Database Migration Service
description: Přečtěte si, jak řešit běžné známé problémy a chyby spojené s používáním Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: ce53e8a77186f96801879e5c9d8f8c65809470d0
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639804"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Řešení běžných problémů s Azure Database Migration Service a chyb

Tento článek popisuje některé běžné problémy a chyby, které Azure Database Migration Service uživatelé mohou nacházet v rámci. Článek obsahuje také informace o tom, jak tyto problémy a chyby vyřešit.

> [!NOTE]
> Komunikace bez posunu
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na _podřízený_ text. [Průvodce stylem Microsoft pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) se tímto způsobem rozpoznává jako vyloučené slovo. Toto slovo se v tomto článku používá kvůli konzistenci, protože je aktuálně slovo, které se zobrazuje v softwaru. Když se software aktualizuje, aby se odebralo slovo, aktualizuje se tento článek na zarovnání.
>

## <a name="migration-activity-in-queued-state"></a>Aktivita migrace ve stavu zařazeném do fronty

Když vytvoříte nové aktivity v Azure Database Migration Service projektu, aktivity zůstanou ve stavu zařazeném do fronty.

| Příčina         | Řešení |
| ------------- | ------------- |
| K tomuto problému dochází, když Azure Database Migration Service instance dosáhla maximální kapacity pro probíhající úlohy, které se souběžně spouštějí. Jakékoli nové aktivity jsou zařazené do fronty, dokud nebude kapacita k dispozici. | Ověří, jestli instance služby migrace dat spouští aktivity napříč projekty. Můžete dál vytvářet nové aktivity, které se automaticky přidají do fronty ke spuštění. Jakmile se dokončí jakákoli existující spuštěná činnost, spustí se další aktivita ve frontě a stav se automaticky změní na stav spuštěno. Pro spuštění migrace aktivity zařazené do fronty není nutné provádět žádnou další akci.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximální počet databází vybraných pro migraci

Při vytváření aktivity pro projekt migrace databáze pro přesun do Azure SQL Database nebo spravované instance Azure SQL došlo k následující chybě:

* **Chyba**: Chyba ověření nastavení migrace "," errorDetail ":" pro migraci bylo vybráno více než maximální počet objektů ' databáze '. "

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, pokud jste pro jednu migrační aktivitu vybrali více než čtyři databáze. V současnosti jsou jednotlivé aktivity migrace omezené na čtyři databáze. | Vyberte čtyři nebo méně databází na jednu aktivitu migrace. Pokud potřebujete migrovat více než čtyři databáze paralelně, zajistěte další instanci Azure Database Migration Service. V současné době každé předplatné podporuje až dvě instance Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Chyby při migraci MySQL do Azure MySQL s chybami obnovení

Když migrujete z MySQL na Azure Database for MySQL pomocí Azure Database Migration Service, aktivita migrace se nezdařila s následující chybou:

* **Chyba**: Chyba migrace databáze – úloha ' taskid ' byla pozastavena z důvodu neúspěšného selhání obnovení: [n].

| Příčina         | Řešení |
| ------------- | ------------- |
| K této chybě může dojít, pokud uživatel, který provádí migraci, chybí ReplicationAdmin role nebo oprávnění klienta replikace, REPLIKy replikace a SUPER (verze starší než MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Ujistěte se, že jsou požadovaná [oprávnění](./tutorial-mysql-azure-mysql-online.md#prerequisites) pro uživatelský účet přesně nakonfigurovaná na instanci Azure Database for MySQL. Pomocí následujících kroků můžete například vytvořit uživatele s názvem "migrateuser" s požadovanými oprávněními:<br>1. Vytvořte migrateuser@ uživatele% identifikovaný klíčovým klíčem; <br>2. udělte všechna oprávnění pro db_name. * do ' migrateuser ' @ '% ' identifikovaný ' Secret '; zopakováním tohoto kroku udělíte přístup k více databázím. <br>3. udělte replikaci na podřízeném počítači *.* do ' migrateuser ' @ '% ' identifikovaný ' Secret ';<br>4. udělte klienta replikace na *.* do ' migrateuser ' @ '% ' identifikovaný ' Secret ';<br>5. vyprázdnit oprávnění; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Při pokusu o zastavení Azure Database Migration Service došlo k chybě.

Při zastavování instance Azure Database Migration Service se zobrazí následující chyba:

* **Chyba**: zastavení služby se nezdařilo. Chyba: {'error':{'code':'InvalidRequest','message':'Aktuálně běží jedna nebo více aktivit. Pokud chcete službu zastavit, počkejte, až se aktivity dokončí, nebo tyto aktivity zastavte ručně a zkuste to znovu.}}

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, když instance služby, kterou se pokoušíte zastavit, zahrnuje aktivity, které jsou pořád spuštěné nebo přítomné v projektech migrace. <br><br><br><br><br><br> | Zajistěte, aby v instanci Azure Database Migration Service, kterou se pokoušíte zastavit, neběžely žádné aktivity. Před pokusem o zastavení služby můžete také odstranit aktivity nebo projekty. Následující postup ukazuje, jak odebrat projekty pro vyčištění instance služby migrace odstraněním všech spuštěných úloh:<br>1. Install-Module-Name migrace AzureRM. <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-Subscription " \<subName> " <br> 4. Remove-AzureRmDataMigrationProject-name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Při pokusu o spuštění Azure Database Migration Service došlo k chybě.

Při spuštění instance Azure Database Migration Service se zobrazí následující chyba:

* **Chyba**: spuštění služby se nezdařilo. Chyba: {' errorDetail ': službu se nepovedlo spustit, obraťte se prosím na podporu Microsoftu.}

| Příčina         | Řešení |
| ------------- | ------------- |
| Tato chyba se zobrazí, když předchozí instance neproběhla interně. Tato chyba se vyskytuje zřídka a technický tým je o tom vědom. <br> | Odstraňte instanci služby, kterou nemůžete spustit, a pak ji zajistěte, aby ji nahradila nová. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Chyba při obnovování databáze při migraci SQL do spravované instance Azure SQL DB

Při provádění online migrace z SQL Server do spravované instance Azure SQL dojde k chybě přímou migraci s následující chybou:

* **Chyba**: operace obnovení pro ID operace operationId se nezdařila. Kód "AuthorizationFailed", zpráva "klient" clientId "s ID objektu" objectId "nemá autorizaci k provedení akce" Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/Read "Over"/subscriptions/subscriptionId ".

| Příčina         | Řešení    |
| ------------- | ------------- |
| Tato chyba znamená, že instanční objekt, který se používá pro online migraci z SQL Server do spravované instance SQL, nemá oprávnění přispívat k tomuto předplatnému. Některá volání rozhraní API se spravovanými instancemi v současné době vyžadují toto oprávnění u předplatného pro operaci obnovení. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Pomocí `Get-AzureADServicePrincipal` rutiny prostředí PowerShell, která `-ObjectId` je k dispozici z chybové zprávy, zobrazte seznam zobrazovaných názvů používaných ID aplikace.<br><br> Ověřte oprávnění k této aplikaci a ujistěte se, že má [roli Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) na úrovni předplatného. <br><br> Tým Azure Database Migration Service Engineering pracuje na omezení požadovaného přístupu z aktuální role Contribute v předplatném. Pokud máte obchodní požadavek, který nepovoluje použití role přispívání, požádejte o další pomoc podporu Azure. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Při odstraňování síťového rozhraní přidruženého k Azure Database Migration Service došlo k chybě.

Když se pokusíte odstranit síťovou kartu přidruženou k Azure Database Migration Service, pokus o odstranění se nezdaří s touto chybou:

* **Chyba**: síťovou kartu přidruženou k Azure Database Migration Service nelze odstranit, protože služba DMS využívá síťovou kartu.

| Příčina         | Řešení    |
| ------------- | ------------- |
| K tomuto problému dochází, když může být instance Azure Database Migration Service stále přítomná a spotřebovává síťovou kartu. <br><br><br><br><br><br><br><br> | Pokud chcete tuto síťovou kartu odstranit, odstraňte instanci služby DMS, která automaticky odstraní síťovou kartu, kterou používá služba.<br><br> **Důležité**: Ujistěte se, že odstraňovaná instance Azure Database Migration Service nemá žádné spuštěné aktivity.<br><br> Po odstranění všech projektů a aktivit přidružených k instanci Azure Database Migration Service můžete instanci služby odstranit. Síťové rozhraní používané instancí služby se automaticky vyčistí jako součást odstranění služby. |

## <a name="connection-error-when-using-expressroute"></a>Chyba připojení při použití ExpressRoute

Pokud se pokoušíte připojit ke zdroji v průvodci projektem služby Azure Database Migration Service a pokud zdroj k připojení využívá ExpressRoute, připojení po uplynutí prodlouženého časového limitu selže.

| Příčina         | Řešení    |
| ------------- | ------------- |
| Při použití [ExpressRoute](https://azure.microsoft.com/services/expressroute/) [vyžaduje](./tutorial-sql-server-to-azure-sql.md) Azure Database Migration Service zřízení tří koncových bodů služby v podsíti Virtual Network přidružené ke službě:<br> --Service Bus koncový bod<br> – Koncový bod úložiště<br> --Koncový bod cílové databáze (například koncový bod SQL, koncový bod Cosmos DB)<br><br><br><br><br> | [Povolte](./tutorial-sql-server-to-azure-sql.md) pro připojení ExpressRoute mezi zdrojem a Azure Database Migration Service požadované koncové body služby. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Při migraci databáze MySQL do Azure DB pro MySQL se zobrazí chyba časového limitu čekání na uzamčení.

Když migrujete databázi MySQL do instance Azure Database for MySQL přes Azure Database Migration Service, migrace se nezdařila s následující chybou vypršení časového limitu pro čekání na uzamčení:

* **Chyba**: Chyba migrace databáze – nepovedlo se načíst soubor – nepovedlo se spustit proces načtení pro soubor n RetCode: SQL_ERROR SQLSTATE: HY000 NativeError: 1205 zpráva: [MySQL] [ovladač ODBC] [mysqld] překročení časového limitu čekání na zámek; Zkuste restartovat transakci.

| Příčina         | Řešení    |
| ------------- | ------------- |
| K této chybě dojde v případě, že migrace selhala kvůli vypršení časového limitu čekání na uzamčení. | Zvažte zvýšení hodnoty parametru serveru **' innodb_lock_wait_timeout '**. Nejvyšší povolená hodnota je 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Při připojování ke zdrojovému SQL Server při použití dynamického portu nebo pojmenované instance došlo k chybě.

Když se pokusíte připojit Azure Database Migration Service k SQL Servermu zdroji, který běží na pojmenované instanci nebo na dynamickém portu, připojení se nezdařilo s touto chybou:

* **Chyba**:-1-připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je SQL Server nakonfigurovaná tak, aby povolovala vzdálená připojení. (poskytovatel: síťová rozhraní SQL, chyba: 26 – Chyba při hledání zadaného serveru nebo instance)

| Příčina         | Řešení    |
| ------------- | ------------- |
| K tomuto problému dochází, když je instance zdrojového SQL Server, ke které se Azure Database Migration Service pokusí připojit, buď dynamický port, nebo používá pojmenovanou instanci. Služba SQL Server Browser naslouchá na portu UDP 1434 pro příchozí připojení k pojmenované instanci nebo při použití dynamického portu. Dynamický port se může změnit při každém restartování služby SQL Server. Dynamický port přiřazený k instanci můžete ověřit prostřednictvím konfigurace sítě v SQL Server Configuration Manager.<br><br><br> |Ověřte, že Azure Database Migration Service se může připojit ke zdrojové SQL Server Browser službě na portu UDP 1434 a instanci SQL Server prostřednictvím dynamicky přiřazeného portu TCP. |

## <a name="additional-known-issues"></a>Další známé problémy

* [Známé problémy/omezení migrace pro online migrace Azure SQL Database](./index.yml)
* [Známé problémy/omezení migrace pro online migrace Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Známé problémy/omezení migrace pro online migrace Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Další kroky

* Podívejte se na článek [Azure Database Migration Service PowerShell](/powershell/module/azurerm.datamigration#data_migration).
* Seznamte se s [postupem konfigurace parametrů serveru v Azure Database for MySQL pomocí Azure Portal](../mysql/howto-server-parameters.md).
* Seznam [požadavků pro použití Azure Database Migration Service](./pre-reqs.md)najdete v článku Přehled požadavků.
* Přečtěte si [Nejčastější dotazy týkající se použití Azure Database Migration Service](./faq.md).