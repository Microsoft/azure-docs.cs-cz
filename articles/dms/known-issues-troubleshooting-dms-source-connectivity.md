---
title: Článek o známé problémy a chyby přidružené k připojení ke zdrojové databáze Azure Database Migration Service odstraňování potíží | Dokumentace Microsoftu
description: Další informace o řešení potíží s známé problémy a chyby přidružené k připojení ke zdrojové databáze Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462847"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Řešení potíží s DMS chyby při připojování ke zdrojové databáze

Následující článek obsahuje podrobné informace o tom, jak vyřešit potenciální problémy, které se mohou vyskytnout při připojování ke zdrojové databázi Azure Database Migration Service (DMS). Každý níže v části se týká konkrétního typu zdrojové databáze, výpis chybu můžete narazit společně se podrobnosti a odkazy na informace o řešení potíží s připojením.

## <a name="sql-server"></a>SQL Server

Potenciální problémy spojené s připojením k databázi systému SQL Server zdroje a tom, jak je řešit jsou uvedené v následující tabulce.

| Chyba         | Příčina a řešení potíží s podrobností |
| ------------- | ------------- |
| Připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, že je název instance správný a zda systém SQL Server je nakonfigurován pro povolení vzdáleného připojení.<br> | K této chybě dochází, pokud nelze službu najít zdrojový server. Chcete-li tento problém vyřešit, najdete v článku [Chyba při připojování ke zdroji systému SQL Server při používání dynamických portů nebo pojmenované instance](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Chyba 53** – připojení SQL selhalo. (Také, pro chybové kódy 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | K této chybě dochází, pokud služba se nemůže připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, najdete v následujících zdrojích informací a akci opakujte. <br><br>  [Interaktivní uživatelskou příručku k odstranění tohoto problému připojení](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Požadavky pro migraci SQL serveru do služby Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Požadavky pro migraci SQL serveru do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Chyba 18456** -přihlášení se nezdařilo.<br> | K této chybě dochází, pokud služba se nemůže připojit ke zdrojové databázi pomocí zadaných přihlašovacích údajů T-SQL. Chcete-li tento problém vyřešit, ověřte zadané přihlašovací údaje. Můžete také odkazovat na [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) nebo uvedené v poznámce dál toto řešení problémů s dokumenty tabulku a pak to zkuste znovu. |
| Hodnotu AccountName "{0}" k dispozici. Očekávaný formát pro AccountName je název_domény uživatelské_jméno<br> | K této chybě dochází, pokud uživatel vybere ověřování Windows, ale poskytuje uživatelské jméno v neplatném formátu. Chcete-li tento problém vyřešit, zadejte uživatelské jméno ve správném formátu pro ověřování Windows, nebo vyberte **ověřování SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Možné problémy spojené s připojením k databázi zdrojové AWS RDS MySQL a tom, jak je řešit jsou uvedené v následující tabulce.

| Chyba         | Příčina a řešení potíží s podrobností |
| ------------- | ------------- |
| **Chyby [2003]** [HY000] - připojení se nezdařilo. CHYBY [HY000] [MySQL] [ovladač ODBC x.x(w)] se nemůže připojit k serveru MySQL {server}. (10060) | K této chybě dochází, pokud ovladač ODBC pro MySQL nemůžete připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, najdete v poznámce dál v této tabulce uvedené řešení problémů s dokumenty a pak to zkuste znovu.<br> |
| **Chyby [2005]** [HY000] - připojení se nezdařilo. CHYBY [HY000] hostitele serveru [MySQL] [ovladač ODBC x.x(w)] Neznámý MySQL {server}. | K této chybě dochází, pokud služba nemůže najít zdrojový hostitel na vzdálené plochy Tento problém může být buď protože uvedené zdroj neexistuje nebo je nějaký problém s infrastrukturou vzdálené plochy. Chcete-li tento problém vyřešit, najdete v poznámce dál v této tabulce uvedené řešení problémů s dokumenty a pak to zkuste znovu.<br> |
| **Chyby [1045]** [HY000] - připojení se nezdařilo. CHYBY [HY000] [MySQL] [ovladač ODBC x.x(w)] přístup byl odepřen pro uživatele {user}“@“{server} (pomocí hesla: ANO) | K této chybě dochází, pokud ovladač ODBC pro MySQL nemůžete připojit ke zdrojovému serveru kvůli neplatným přihlašovacím údajům. Ověření přihlašovacích údajů, které jste zadali. Pokud potíže přetrvávají, ověřte, že tento zdrojový počítač má správné přihlašovací údaje. Budete muset obnovit heslo v konzole. Pokud tento problém přetrvává, najdete v poznámce dál v této tabulce uvedené řešení problémů s dokumenty a potom to zkuste znovu.<br> |
| **Chyby [9002]** [HY000] - připojení se nezdařilo. CHYBY [HY000] [MySQL] [ovladač ODBC x.x(w)] připojovací řetězec nemusí být správné. Navštivte portál pro odkazy.| K této chybě dochází, pokud připojení selhává kvůli problému s připojovacím řetězcem. Ověřte připojovací řetězec k dispozici je platný. Chcete-li tento problém vyřešit, najdete v poznámce dál v této tabulce uvedené řešení problémů s dokumenty a pak to zkuste znovu.<br> |
| **Chyba v binární protokolování. Proměnná binlog_format má hodnotu {value}. Změňte ji na "řádek".** | K této chybě dochází, pokud je chyba v binární protokolování. Proměnná binlog_format má nesprávnou hodnotu. Chcete-li tento problém vyřešit, změňte binlog_format ve skupině pro parametr "Řádek" a pak proveďte restart instance. Další informace najdete v tématu [binární možnosti protokolování a proměnné](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) nebo [soubory protokolu AWS RDS MySQL Database dokumentaci](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Další informace o řešení potíží s problémy spojené s připojením k databázi zdrojové AWS RDS MySQL najdete v článku na následujících odkazech:
> * [Řešení potíží s k připojení k vzdálené ploše Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak vyřešit problémy s připojením k Moje databáze instance Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Potenciální problémy spojené s připojením k databázi AWS RDS PostgreSQL zdroje a tom, jak je řešit jsou uvedené v následující tabulce.

| Chyba         | Příčina a řešení potíží s podrobností |
| ------------- | ------------- |
| **Chyby [101]** [08001] - připojení se nezdařilo. Chyba [08001] vypršel časový limit vypršel. | K této chybě dochází, pokud ovladač Postgres nemůže připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, najdete v poznámce dál v této tabulce uvedené řešení problémů s dokumenty a pak to zkuste znovu. |
| **Chyba: Parametr wal_level má hodnota {value}. Změňte ji na logický povolit replikaci.** | K této chybě dochází, pokud parametr wal_level má nesprávnou hodnotu. Chcete-li tento problém vyřešit, změňte rds.logical_replication ve skupině pro parametr na hodnotu 1 a restartujte instanci. Další informace najdete v tématu [požadavcích pro migraci do Azure PostgreSQL pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) nebo [PostgreSQL na Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Další informace o řešení potíží s problémy spojené s připojením k databázi AWS RDS PostgreSQL zdroje najdete v článku na následujících odkazech:
> * [Řešení potíží s k připojení k vzdálené ploše Amazon](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak vyřešit problémy s připojením k Moje databáze instance Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potenciálních problémů spojených s připojením ke zdrojové databáze systému SQL Server AWS RDS a tom, jak je řešit jsou uvedené v následující tabulce.

| Chyba         | Příčina a řešení potíží s podrobností |
| ------------- | ------------- |
| **Chyba 53** – připojení SQL selhalo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server nebyl nalezen nebo nebyl přístupný. Ověřte, že je název instance správný a zda systém SQL Server je nakonfigurován pro povolení vzdáleného připojení. (poskytovatel: Zprostředkovatel pojmenované kanály, chyba: 40 - nelze otevřít připojení k SQL serveru | K této chybě dochází, pokud služba se nemůže připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, najdete v poznámce dál v této tabulce uvedené řešení problémů s dokumenty a pak to zkuste znovu. |
| **Chyba 18456** -přihlášení se nezdařilo. Přihlášení uživatele {user} se nezdařilo | K této chybě dochází, pokud služba nemůže připojit k databázi zdrojové s přihlašovacími údaji T-SQL k dispozici. Chcete-li tento problém vyřešit, ověřte zadané přihlašovací údaje. Můžete také odkazovat na [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) nebo k řešení potíží uvedené v poznámce dál to dokumenty tabulky a zkuste to znovu. |
| **Chyba 87** – připojovací řetězec není platný. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, že je název instance správný a zda systém SQL Server je nakonfigurován pro povolení vzdáleného připojení. (poskytovatel: Rozhraní sítě systému SQL, chyba: 25 - připojovací řetězec není platný) | K této chybě dochází, pokud služba se nemůže připojit ke zdrojovému serveru z důvodu neplatný připojovací řetězec. Chcete-li tento problém vyřešit, ověřte připojovací řetězec k dispozici. Pokud se problém nevyřeší, najdete v poznámce dál v této tabulce uvedené řešení problémů s dokumenty a potom to zkuste znovu. |
| **Chyba: certifikát serveru není důvěryhodný.** Úspěšně bylo vytvořeno připojení k serveru, ale pak během procesu přihlašování došlo k chybě. (poskytovatel: Zprostředkovatel protokolu SSL, chyba: 0 – řetěz certifikátů byl vydán autoritou, která není důvěryhodná.) | K této chybě dochází, pokud certifikát používaný není důvěryhodné. Chcete-li tento problém vyřešit, musíte najít certifikát, který lze považovat za důvěryhodné a potom ji povolit na serveru. Alternativně můžete vybrat možnost důvěřovat certifikátu při připojování. Tuto akci provést jenom v případě, že jste obeznámeni s certifikátem používaným a že jí důvěřujete. <br> Připojení SSL, které jsou šifrované pomocí certifikátu podepsaného svým držitelem neposkytuje silné zabezpečení – jsou náchylné k útokům man-in-the-middle. Nespoléhejte na SSL pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojené k Internetu. <br> Další informace najdete v tématu [pomocí protokolu SSL s instancí databáze Microsoft SQL serveru](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) nebo [kurzu: Migrace vzdálené plochy SQL serveru do Azure s využitím DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Chyba 300** – uživatel nemá požadovaná oprávnění. VIEW SERVER STATE oprávnění bylo odepřeno pro objekt "{server}' databáze {databáze} | K této chybě dochází, pokud uživatel nemá oprávnění k provedení migrace. Chcete-li tento problém vyřešit, přečtěte si [udělení oprávnění serveru – Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) nebo [kurzu: Migrace vzdálené plochy SQL serveru do Azure s využitím DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) další podrobnosti. |

> [!NOTE]
> Další informace o řešení potíží týkající se připojení ke zdroji AWS RDS SQL serveru najdete v článku na následujících odkazech:
>
> * [Řešení chyb připojení k SQL Serveru](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Jak vyřešit problémy s připojením k Moje databáze instance Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Známé problémy

* [Známé problémy a migrace omezení online migraci do Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Známé problémy a migrace omezení online migrace do služby Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Známé problémy a migrace omezení online migrace do služby Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Další postup

* Zobrazit článek [prostředí PowerShell Azure Database Migration Service](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Zobrazit článek [postupy konfigurace parametrů serveru ve službě Azure Database for MySQL pomocí webu Azure portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Zobrazit článek [přehled požadavků na používání Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobrazit [– nejčastější dotazy o použití Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
