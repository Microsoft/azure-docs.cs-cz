---
title: Problémy s připojením zdrojových databází
titleSuffix: Azure Database Migration Service
description: Přečtěte si, jak řešit známé problémy nebo chyby spojené s připojením služby Migrace databáze Azure ke zdrojovým databázím.
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
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297104"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Řešení chyb DMS při připojování ke zdrojovým databázím

V následujícím článku jsou uvedeny podrobnosti o tom, jak řešit potenciální problémy, se kterými se můžete setkat při připojování služby Azure Database Migration Service (DMS) ke zdrojové databázi. Každá část níže se týká určitého typu zdrojové databáze a uvádí chybu, se kterou se můžete setkat, spolu s podrobnostmi a odkazy na informace o řešení potíží s připojením.

## <a name="sql-server"></a>SQL Server

Potenciální problémy spojené s připojením ke zdrojové databázi serveru SQL Server a jejich adresování jsou uvedeny v následující tabulce.

| Chyba         | Podrobnosti o příčině a odstraňování potíží |
| ------------- | ------------- |
| Připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je server SQL Server nakonfigurován tak, aby umožňoval vzdálená připojení.<br> | K této chybě dochází, pokud služba nemůže najít zdrojový server. Problém vyřešíte v článku [Chyba při připojování ke zdrojovému serveru SQL Server při použití dynamického portu nebo pojmenované instance](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Chyba 53** – připojení SQL se nezdařilo. (Také pro chybové kódy 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | K této chybě dochází, pokud se služba nemůže připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, přečtěte si následující zdroje a akci opakujte. <br><br>  [Interaktivní uživatelská příručka k řešení problému s připojením](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Požadavky pro migraci serveru SQL Server do databáze Azure SQL](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Požadavky pro migraci SQL Serveru do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Chyba 18456** - Přihlášení se nezdařilo.<br> | K této chybě dochází, pokud se služba nemůže připojit ke zdrojové databázi pomocí poskytnutých pověření T-SQL. Chcete-li tento problém vyřešit, ověřte zadaná pověření. Můžete také odkazovat na [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) nebo na dokumenty pro řešení potíží uvedené v poznámce pod touto tabulkou a akci opakujte. |
| Byla poskytnuta nepoškozená hodnota _Name_účtu .{0} Očekávaný formát pro AccountName je DomainName\UserName<br> | K této chybě dochází, pokud uživatel vybere ověřování systému Windows, ale zadává uživatelské jméno v neplatném formátu. Chcete-li tento problém vyřešit, zadejte uživatelské jméno ve správném formátu pro ověřování systému Windows nebo vyberte **možnost Ověřování SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potenciální problémy spojené s připojením ke zdrojové databázi AWS RDS MySQL a jejich řešení jsou uvedeny v následující tabulce.

| Chyba         | Podrobnosti o příčině a odstraňování potíží |
| ------------- | ------------- |
| **Chyba [2003]**[HY000] - připojení se nezdařilo. CHYBA [HY000] [MySQL][OVLADAČ ODBC x.x(w] Nelze se připojit k serveru MySQL na serveru {server} (10060) | K této chybě dochází, pokud se ovladač MySQL ODBC nemůže připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, podívejte se do dokumentů pro řešení potíží uvedených v poznámce pod touto tabulkou a akci opakujte.<br> |
| **Chyba [2005]**[HY000] - připojení se nezdařilo. CHYBA [HY000] [MySQL][OVLADAČ ODBC x.x(w] Neznámý hostitel serveru MySQL '{server}' | K této chybě dochází, pokud služba nemůže najít zdrojového hostitele v rds. Problém může být buď proto, že uvedený zdroj neexistuje nebo došlo k potížím s infrastrukturou RDS. Chcete-li tento problém vyřešit, podívejte se do dokumentů pro řešení potíží uvedených v poznámce pod touto tabulkou a akci opakujte.<br> |
| **Chyba [1045]**[HY000] - připojení se nezdařilo. CHYBA [HY000] [MySQL][OVLADAČ ODBC x.x(w] Přístup byl odepřen uživateli {user}'@'{server}' (pomocí hesla: ANO) | K této chybě dochází, pokud ovladač MySQL ODBC nelze připojit ke zdrojovému serveru z důvodu neplatných pověření. Ověřte zadané pověření. Pokud problém přetrvává, ověřte, zda zdrojový počítač má správná pověření. Možná budete muset obnovit heslo v konzole. Pokud se s tímto problémem stále setkáváte, přečtěte si dokumenty pro řešení potíží uvedené v poznámce pod touto tabulkou a akci opakujte.<br> |
| **Chyba [9002]**[HY000] - připojení se nezdařilo. CHYBA [HY000] [MySQL][OVLADAČ ODBC x.x(w] Připojovací řetězec nemusí být správný. Navštivte portál pro reference.| K této chybě dochází, pokud připojení selhává z důvodu problému s připojovacím řetězcem. Ověřte, zda je poskytnutý připojovací řetězec platný. Chcete-li tento problém vyřešit, podívejte se do dokumentů pro řešení potíží uvedených v poznámce pod touto tabulkou a akci opakujte.<br> |
| **Chyba v binárním protokolování. Proměnná binlog_format má hodnotu {value}. Změňte jej na "řádek".** | K této chybě dochází, pokud je chyba v binární protokolování; proměnná binlog_format má nesprávnou hodnotu. Chcete-li tento problém vyřešit, změňte binlog_format ve skupině parametrů na ŘÁDEK a potom instanci restartujte. Další informace naleznete v dokumentaci k [binárním možnostem protokolování a proměnným](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) nebo [k dokumentaci k souborům protokolu databáze AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Další informace o řešení potíží souvisejících s připojením ke zdrojové databázi AWS RDS MySQL naleznete v následujících zdrojích:
> * [Řešení potíží s problémy s připojením Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak vyřeším problémy s připojením k instanci databáze Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Potenciální problémy spojené s připojením ke zdrojové databázi AWS RDS PostgreSQL a jejich řešení jsou uvedeny v následující tabulce.

| Chyba         | Podrobnosti o příčině a odstraňování potíží |
| ------------- | ------------- |
| **Chyba [101]**[08001] - připojení se nezdařilo. Vypršel časový limit chyby [08001]. | K této chybě dochází, pokud se ovladač Postgres nemůže připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, podívejte se do dokumentů pro řešení potíží uvedených v poznámce pod touto tabulkou a akci opakujte. |
| **Chyba: Parametr wal_level má hodnotu {value}. Chcete-li povolit replikaci, změňte jej na logický.** | K této chybě dochází, pokud parametr wal_level má nesprávnou hodnotu. Chcete-li tento problém vyřešit, změňte soubor rds.logical_replication ve skupině parametrů na hodnotu 1 a potom instanci restartujte. Další informace najdete [v tématu Pre-předpoklady pro migraci do Azure PostgreSQL pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) nebo [PostgreSQL na Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Další informace o řešení potíží souvisejících s připojením ke zdrojové databázi AWS RDS PostgreSQL naleznete v následujících zdrojích:
> * [Řešení potíží s problémy s připojením Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Jak vyřeším problémy s připojením k instanci databáze Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potenciální problémy spojené s připojením ke zdrojové databázi AWS RDS SQL Server a jejich adresování jsou uvedeny v následující tabulce.

| Chyba         | Podrobnosti o příčině a odstraňování potíží |
| ------------- | ------------- |
| **Chyba 53** – připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server nebyl nalezen nebo nebyl přístupný. Ověřte, zda je název instance správný a zda je server SQL Server nakonfigurován tak, aby umožňoval vzdálená připojení. (Zprostředkovatel: Zprostředkovatel pojmenovaných kanálů, chyba: 40 - Nelze otevřít připojení k serveru SQL Server | K této chybě dochází, pokud se služba nemůže připojit ke zdrojovému serveru. Chcete-li tento problém vyřešit, podívejte se do dokumentů pro řešení potíží uvedených v poznámce pod touto tabulkou a akci opakujte. |
| **Chyba 18456** - Přihlášení se nezdařilo. Přihlášení se nezdařilo pro uživatele {user}' | K této chybě dochází, pokud se služba nemůže připojit ke zdrojové databázi s pověřeními T-SQL. Chcete-li tento problém vyřešit, ověřte zadaná pověření. Můžete také odkazovat na [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) nebo na dokumenty pro řešení potíží uvedené v poznámce pod touto tabulkou a akci opakujte. |
| **Chyba 87** - Připojovací řetězec není platný. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je server SQL Server nakonfigurován tak, aby umožňoval vzdálená připojení. (poskytovatel: SQL Network Interfaces, error: 25 - Připojovací řetězec není platný) | K této chybě dochází, pokud se služba nemůže připojit ke zdrojovému serveru z důvodu neplatného připojovacího řetězce. Chcete-li tento problém vyřešit, ověřte poskytnutý připojovací řetězec. Pokud problém přetrvává, podívejte se do dokumentů pro řešení potíží uvedených v poznámce pod touto tabulkou a akci opakujte. |
| **Chyba – Certifikát serveru není důvěryhodný.** Připojení bylo úspěšně navázáno se serverem, ale během procesu přihlášení došlo k chybě. (Zprostředkovatel: Zprostředkovatel SSL, chyba: 0 - Řetěz certifikátů byl vydán orgánem, který není důvěryhodný.) | K této chybě dochází, pokud použitý certifikát není důvěryhodný. Chcete-li tento problém vyřešit, musíte najít certifikát, který lze považovat za důvěryhodný, a poté jej povolit na serveru. Případně můžete při připojování vybrat možnost Důvěřovat certifikátu. Tuto akci provádějte pouze v případě, že jste obeznámeni s použitým certifikátem a důvěřujete mu. <br> Připojení TLS, která jsou šifrována pomocí certifikátu podepsaného svým držitelem, neposkytují silné zabezpečení – jsou náchylná k útokům prostředníkem. Nespoléhejte na protokol TLS pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojeny k Internetu. <br> Další informace naleznete [v tématu Použití ssl s instancí Microsoft SQL Server DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) nebo [kurz: Migrace serveru RDS SQL Server do Azure pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Chyba 300** - Uživatel nemá požadovaná oprávnění. Oprávnění VIEW STAVU SERVERU bylo odepřeno u objektu {server}, databáze {database}. | K této chybě dochází, pokud uživatel nemá oprávnění k provedení migrace. Chcete-li tento problém vyřešit, podívejte se na [oprávnění serveru GRANT – Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) nebo [kurz: Migrace serveru RDS SQL Server do Azure pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) pro další podrobnosti. |

> [!NOTE]
> Další informace o řešení potíží souvisejících s připojením ke zdroji AWS RDS SQL Server naleznete v následujících zdrojích:
>
> * [Řešení chyb připojení k SQL Serveru](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Jak vyřeším problémy s připojením k instanci databáze Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Známé problémy

* [Známé problémy a omezení migrace s online migrací do Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Známé problémy/omezení migrace s online migrací do databáze Azure pro MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Známé problémy/omezení migrace s online migrací do databáze Azure pro PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Další kroky

* Podívejte se na článek [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Podívejte se na článek [Jak nakonfigurovat parametry serveru v Azure Database for MySQL pomocí portálu Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Prohlédněte si článek [Přehled předpokladů pro používání služby Migrace databáze Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Podívejte se na [nejčastější dotazy týkající se používání služby Migrace databáze Azure](https://docs.microsoft.com/azure/dms/faq).
