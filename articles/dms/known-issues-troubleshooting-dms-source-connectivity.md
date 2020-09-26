---
title: Problémy připojující zdrojové databáze
titleSuffix: Azure Database Migration Service
description: Přečtěte si, jak řešit známé problémy/chyby spojené s připojením Azure Database Migration Service ke zdrojovým databázím.
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
ms.openlocfilehash: 2db941edef93b1e836e82753a6d6016adb977e65
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322593"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Řešení chyb DMS při připojování ke zdrojovým databázím

Následující článek poskytuje podrobné informace o tom, jak řešit potenciální problémy, se kterými se můžete setkat při připojování Azure Database Migration Service (DMS) ke zdrojové databázi. Každá část níže odkazuje na konkrétní typ zdrojové databáze. zobrazí se chyba, která se může nacházet společně s podrobnostmi, a odkazy na informace o tom, jak řešit potíže s připojením.

## <a name="sql-server"></a>SQL Server

Potenciální problémy spojené s připojením ke zdrojové SQL Server databázi a jejich řešení jsou uvedené v následující tabulce.

| Chyba         | Příčina a podrobnosti odstraňování potíží |
| ------------- | ------------- |
| Připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je SQL Server nakonfigurovaná tak, aby povolovala vzdálená připojení.<br> | K této chybě dojde, pokud služba nemůže najít zdrojový server. Chcete-li vyřešit tento problém, přečtěte si článek [Chyba připojení ke zdrojovému SQL Server při použití dynamického portu nebo pojmenované instance](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Chyba 53** -připojení SQL se nezdařilo. (Také pro kódy chyb 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | K této chybě dojde, pokud se služba nemůže připojit ke zdrojovému serveru. Pokud chcete tento problém vyřešit, přečtěte si následující zdroje a zkuste to znovu. <br><br>  [Interaktivní uživatelská příručka pro řešení potíží s připojením](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Předpoklady pro migraci SQL Server do Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Předpoklady pro migraci SQL Server do spravované instance Azure SQL](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Chyba 18456** : přihlášení se nezdařilo.<br> | K této chybě dojde, pokud se služba nemůže připojit ke zdrojové databázi pomocí zadaných přihlašovacích údajů T-SQL. Pokud chcete problém vyřešit, ověřte zadané přihlašovací údaje. Můžete také použít [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) nebo k dokumentům pro řešení problémů, které jsou uvedeny v poznámce pod touto tabulkou, a akci opakovat. |
| Poskytla se chybná hodnota účtu {0} . Očekávaný formát pro účet Account je název_domény \ uživatelské_jméno.<br> | K této chybě dojde, pokud uživatel vybere ověřování systému Windows, ale uživatelské jméno bude v neplatném formátu. Pokud chcete tento problém vyřešit, zadejte uživatelské jméno ve správném formátu pro ověřování systému Windows nebo vyberte **ověřování SQL**. |

## <a name="aws-rds-mysql"></a>AWS VP MySQL

Potenciální problémy spojené s připojením ke zdrojové databázi AWS VP MySQL a jejich řešení jsou uvedené v následující tabulce.

| Chyba         | Příčina a podrobnosti odstraňování potíží |
| ------------- | ------------- |
| **Chyba [2003]**[HY000] – připojení selhalo. Chyba [HY000] [MySQL] [ovladač ODBC x. x (w)] se nemůže připojit k serveru MySQL na serveru {Server} (10060) | K této chybě dojde, když se ovladač MySQL ODBC nemůže připojit ke zdrojovému serveru. Chcete-li vyřešit tento problém, přečtěte si dokumenty k řešení problémů, které jsou uvedeny v poznámce pod touto tabulkou, a akci opakujte.<br> |
| **Chyba [2005]**[HY000] – připojení selhalo. Chyba [HY000] [MySQL] [ODBC x. x (w) Driver] Neznámý hostitel serveru MySQL {server} | K této chybě dojde, pokud služba nemůže najít zdrojového hostitele v rámci služby RDS. Příčinou může být to, že uvedený zdroj neexistuje nebo se jedná o problém s infrastrukturou VP. Chcete-li vyřešit tento problém, přečtěte si dokumenty k řešení problémů, které jsou uvedeny v poznámce pod touto tabulkou, a akci opakujte.<br> |
| **Chyba [1045]**[HY000] – připojení selhalo. Chyba [HY000] [MySQL] [ODBC x. x (w) ovladač] přístup byl odepřen pro uživatele {User} @ {Server} (použití hesla: Ano). | K této chybě dochází, když se ovladač MySQL ODBC nemůže připojit ke zdrojovému serveru z důvodu neplatných přihlašovacích údajů. Ověřte přihlašovací údaje, které jste zadali. Pokud se problém opakuje, ověřte, zda má zdrojový počítač správné přihlašovací údaje. Je možné, že budete muset resetovat heslo v konzole nástroje. Pokud stále dochází k problému, přečtěte si dokumenty pro řešení problémů uvedené v poznámce níže v této tabulce a akci opakujte.<br> |
| **Chyba [9002]**[HY000] – připojení selhalo. Chyba [HY000] [MySQL] [ODBC x. x (w) Driver] připojovací řetězec nemusí být správný. Odkazy najdete na portálu.| K této chybě dochází, pokud selhalo připojení z důvodu problému s připojovacím řetězcem. Ověřte, zda je zadaný připojovací řetězec platný. Chcete-li vyřešit tento problém, přečtěte si dokumenty k řešení problémů, které jsou uvedeny v poznámce pod touto tabulkou, a akci opakujte.<br> |
| **Chyba v binárním protokolování. Proměnná binlog_format má hodnotu {Value}. Změňte prosím tuto hodnotu na ' Row '.** | K této chybě dochází, pokud dojde k chybě v binárním protokolování; Proměnná binlog_format má nesprávnou hodnotu. Chcete-li tento problém vyřešit, změňte binlog_format ve skupině parametrů na možnost "řádek" a poté instanci restartujte. Další informace najdete v dokumentaci k [možnostem binárního protokolování a proměnným](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) nebo v [dokumentaci k souborům protokolu databáze AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Další informace o řešení potíží souvisejících s připojením ke zdrojové databázi AWS VP MySQL najdete v následujících zdrojích informací:
> * [Řešení potíží s připojením služby Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Návody vyřešit problémy s připojením k instanci databáze Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS VP PostgreSQL

Potenciální problémy spojené s připojením ke zdrojové databázi AWS VP PostgreSQL a jejich řešení jsou uvedené v následující tabulce.

| Chyba         | Příčina a podrobnosti odstraňování potíží |
| ------------- | ------------- |
| **Chyba [101]**[08001] – připojení selhalo. Vypršel časový limit pro chybu [08001]. | K této chybě dojde, když se ovladač Postgres nemůže připojit ke zdrojovému serveru. Chcete-li vyřešit tento problém, přečtěte si dokumenty k řešení problémů, které jsou uvedeny v poznámce pod touto tabulkou, a akci opakujte. |
| **Chyba: parametr wal_level má hodnotu {Value}. Pokud chcete povolení replikace, změňte prosím tuto hodnotu na logická.** | K této chybě dochází, pokud parametr wal_level má nesprávnou hodnotu. Chcete-li tento problém vyřešit, změňte skupinu RDS. logical_replication ve skupině parametr na 1 a pak instanci restartujte. Další informace najdete v tématu [požadavky na migraci do Azure PostgreSQL pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) nebo [POSTGRESQL na Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Další informace o řešení potíží souvisejících s připojením ke zdrojové databázi AWS VP PostgreSQL najdete v následujících zdrojích informací:
> * [Řešení potíží s připojením služby Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Návody vyřešit problémy s připojením k instanci databáze Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potenciální problémy spojené s připojením ke zdrojové AWS databázi VP SQL Server a jejich řešení jsou uvedené v následující tabulce.

| Chyba         | Příčina a podrobnosti odstraňování potíží |
| ------------- | ------------- |
| **Chyba 53** -připojení SQL se nezdařilo. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo není dostupný. Ověřte, zda je název instance správný a zda je SQL Server nakonfigurovaná tak, aby povolovala vzdálená připojení. (Zprostředkovatel: Zprostředkovatel pojmenovaných kanálů, chyba: 40 – nelze otevřít připojení k SQL Server | K této chybě dojde, pokud se služba nemůže připojit ke zdrojovému serveru. Chcete-li vyřešit tento problém, přečtěte si dokumenty k řešení problémů, které jsou uvedeny v poznámce pod touto tabulkou, a akci opakujte. |
| **Chyba 18456** : přihlášení se nezdařilo. Přihlášení uživatele {User} se nezdařilo. | K této chybě dojde, pokud se služba nemůže připojit ke zdrojové databázi pomocí zadaných přihlašovacích údajů T-SQL. Pokud chcete problém vyřešit, ověřte zadané přihlašovací údaje. Můžete se také podívat na [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) nebo na dokumenty pro řešení potíží uvedené v poznámce pod touto tabulkou a zkusit to znovu. |
| **Chyba 87** : připojovací řetězec není platný. Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je SQL Server nakonfigurovaná tak, aby povolovala vzdálená připojení. (poskytovatel: síťová rozhraní SQL, chyba: 25 připojovací řetězec není platný.) | K této chybě dojde, pokud se služba nemůže připojit ke zdrojovému serveru z důvodu neplatného připojovacího řetězce. Pokud chcete problém vyřešit, ověřte poskytnutý připojovací řetězec. Pokud se problém nevyřeší, přečtěte si dokumenty popsané v poznámce níže v této tabulce a akci opakujte. |
| **Chyba: certifikát serveru není důvěryhodný.** Připojení k serveru bylo úspěšně navázáno, ale během procesu přihlašování došlo k chybě. (poskytovatel: poskytovatel SSL, chyba: 0 – řetěz certifikátů byl vydán nedůvěryhodnou autoritou.) | K této chybě dochází, pokud se použitý certifikát nepovažuje za důvěryhodný. Pro vyřešení tohoto problému potřebujete najít certifikát, který může být důvěryhodný, a pak ho na serveru povolit. Případně můžete vybrat možnost certifikát důvěryhodnosti při připojování. Tuto akci proveďte pouze v případě, že jste obeznámeni s použitým certifikátem a důvěřujete mu. <br> Připojení TLS zašifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení – jsou náchylná k útokům prostředníkem. Nespoléhá se na protokol TLS pomocí certifikátů podepsaných držitelem v produkčním prostředí nebo na serverech, které jsou připojené k Internetu. <br> Další informace najdete v tématu [použití protokolu SSL s instancí Microsoft SQL Server DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) nebo [kurzem: migrace programu RDS SQL Server do Azure pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Chyba 300** – uživatel nemá požadovaná oprávnění. Oprávnění zobrazit stav serveru bylo odepřeno pro objekt {Server}, databázi {Database}. | K této chybě dochází, pokud uživatel nemá oprávnění k provedení migrace. Pokud chcete tento problém vyřešit, přečtěte si téma [udělení oprávnění serveru – Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) nebo [kurz: migrace SQL Server RDS do Azure pomocí DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) pro další podrobnosti. |

> [!NOTE]
> Další informace o řešení potíží souvisejících s připojením ke zdroji AWS VP SQL Server najdete v následujících zdrojích informací:
>
> * [Řešení chyb připojení k SQL Serveru](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Návody vyřešit problémy s připojením k instanci databáze Amazon RDS?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Známé problémy

* [Známé problémy/omezení migrace pro online migrace Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Známé problémy/omezení migrace pro online migrace Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Známé problémy/omezení migrace pro online migrace Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Další kroky

* Podívejte se na článek [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Seznamte se s [postupem konfigurace parametrů serveru v Azure Database for MySQL pomocí Azure Portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Seznam [požadavků pro použití Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)najdete v článku Přehled požadavků.
* Přečtěte si [Nejčastější dotazy týkající se použití Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
