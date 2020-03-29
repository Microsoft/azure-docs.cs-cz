---
title: Migrace s výpisem a obnovením – databáze Azure pro MariaDB
description: Tento článek vysvětluje dva běžné způsoby zálohování a obnovení databází v azure databázi pro MariaDB pomocí nástrojů, jako je mysqldump, MySQL Workbench a PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 72735e83af97fde8377e27daa45501704ef5a3c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164538"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrace databáze MariaDB do databáze Azure pro MariaDB pomocí výpisu a obnovení
Tento článek vysvětluje dva běžné způsoby zálohování a obnovení databází v azure databázi pro MariaDB
- Výpis a obnovení z příkazového řádku (pomocí mysqldump) 
- Výpis a obnovení pomocí PHPMyAdmin

## <a name="before-you-begin"></a>Než začnete
Chcete-li projít tento návod, musíte mít:
- [Vytvoření databáze Azure pro server MariaDB – portál Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) příkaz-line nástroj nainstalován na stroji.
- MySQL Workbench [MySQL Workbench Stáhnout](https://dev.mysql.com/downloads/workbench/) nebo jiný nástroj MySQL třetí strany dělat výpis a obnovení příkazů.

## <a name="use-common-tools"></a>Použití běžných nástrojů
Pomocí běžných nástrojů a nástrojů, jako je MySQL Workbench nebo mysqldump vzdáleně připojit a obnovit data do databáze Azure pro MariaDB. Pomocí těchto nástrojů v klientském počítači s připojením k internetu se můžete připojit k databázi Azure pro MariaDB. Pro osvědčené postupy zabezpečení použijte šifrované připojení SSL, najdete v tématu [Konfigurace připojení SSL v Azure Database for MariaDB](concepts-ssl-connection-security.md). Při migraci do azure database pro MariaDB není nutné přesunout soubory s výpisem stavu paměti do žádného zvláštního umístění v cloudu. 

## <a name="common-uses-for-dump-and-restore"></a>Běžné použití pro výpis a obnovení
Můžete použít MySQL nástroje, jako je mysqldump a mysqlpump k výpisu a načtení databází do databáze Azure pro MariaDB serveru v několika běžných scénářích. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Při migraci celé databáze použijte výpisy databáze. Toto doporučení platí při přesunutí velkého množství dat nebo pokud chcete minimalizovat přerušení služby pro živé weby nebo aplikace. 
-  Při načítání dat do služby Azure Database for MariaDB se ujistěte, že všechny tabulky v databázi používají databázové jádro InnoDB. Azure Database for MariaDB podporuje jenom modul úložiště InnoDB a proto nepodporuje alternativní moduly úložiště. Pokud jsou vaše tabulky nakonfigurované s jinými moduly úložiště, převeďte je před migrací do databáze Azure pro MariaDB do formátu modulu InnoDB.
   Například pokud máte WordPress nebo WebApp pomocí tabulek MyISAM, nejprve převést tyto tabulky migrací do formátu InnoDB před obnovením do databáze Azure pro MariaDB. Pomocí klauzule `ENGINE=InnoDB` nastavte modul použitý při vytváření nové tabulky a potom přenést data do kompatibilní tabulky před obnovením. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Abyste se při výpisu databází vyhnuli problémům s kompatibilitou, ujistěte se, že zdrojový i cílový systém používají stejnou verzi MariaDB. Pokud je například váš stávající server MariaDB verze 10.2, měli byste migrovat do Azure Database for MariaDB nakonfigurované pro spuštění verze 10.2. Příkaz `mysql_upgrade` nefunguje v databázi Azure pro server MariaDB a není podporován. Pokud potřebujete upgradovat napříč verzemi MariaDB, nejprve vypíšete nebo exportujte nižší verzi databáze do vyšší verze MariaDB ve vašem vlastním prostředí. Pak `mysql_upgrade`spusťte , před pokusem o migraci do databáze Azure pro MariaDB.

## <a name="performance-considerations"></a>Otázky výkonu
Chcete-li optimalizovat výkon, všimněte si těchto úvah při ukládání velkých databází:
-   Použijte `exclude-triggers` možnost v mysqldump při ukládání databází. Vylučte aktivační události ze souborů s výpisem stavu paměti, abyste se vyhnuli spouštění příkazů aktivační ch aktivační ch odtéto události během obnovení dat. 
-   Pomocí `single-transaction` možnosti nastavte režim izolace transakce na opakovatelné čtení a odešle příkaz START TRANSACTION SQL na server před vykládkou dat. Ukládání mnoho tabulek v rámci jedné transakce způsobí, že některé další úložiště, které mají být spotřebovány během obnovení. Možnost `single-transaction` a `lock-tables` možnost se vzájemně vylučují, protože LOCK TABLES způsobí, že všechny čekající transakce, které mají být potvrzeny implicitně. Chcete-li vyložit velké tabulky, zkombinujte `single-transaction` možnost s `quick` možností. 
-   `extended-insert` Použijte víceřádkovou syntaxi, která obsahuje několik seznamů HODNOT. Výsledkem je menší soubor s výpisem stavu paměti a urychluje vložení při opětovném načtení souboru.
-  Použijte `order-by-primary` možnost v mysqldump při ukládání databází tak, aby data jsou skriptována v pořadí primárního klíče.
-   Použijte `disable-keys` možnost v mysqldump při ukládání dat, zakázat omezení cizího klíče před načtením. Zakázání kontrol y cizího klíče poskytuje zvýšení výkonu. Povolte omezení a ověřte data po zatížení, abyste zajistili referenční integritu.
-   V případě potřeby použijte rozdělené tabulky.
-   Načtení dat paralelně. Vyhněte se příliš mnoho paralelismu, který by způsobil, že byste dosáhli limitu prostředků a monitorování prostředků pomocí metriky dostupné na webu Azure Portal. 
-   Použijte `defer-table-indexes` možnost v mysqlpump při ukládání databází, tak, aby vytváření indexu se stane po načtení dat tabulky.
-   Zkopírujte záložní soubory do objektu blob/store Azure a proveďte obnovení odtud, což by mělo být mnohem rychlejší než provádění obnovení přes Internet.

## <a name="create-a-backup-file"></a>Vytvoření záložního souboru
Chcete-li zálohovat existující databázi MariaDB na místním místním serveru nebo ve virtuálním počítači, spusťte následující příkaz pomocí mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry, které mají být poskytnuty, jsou:
- [uname] Uživatelské jméno databáze 
- To je v pořádku. Heslo pro vaši databázi (všimněte si, že mezi -p a heslem není mezera) 
- [dbname] Název databáze 
- [backupfile.sql] Název souboru pro zálohování databáze 
- [--opt] Možnost mysqldump 

Chcete-li například zálohovat databázi s názvem "testdb" na serveru MariaDB s uživatelským jménem "testuser" a bez hesla k souboru testdb_backup.sql, použijte následující příkaz. Příkaz zálohuje `testdb` databázi do `testdb_backup.sql`souboru s názvem , který obsahuje všechny příkazy SQL potřebné k opětovnému vytvoření databáze. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Chcete-li vybrat konkrétní tabulky v databázi, které chcete zálohovat, uveďte názvy tabulek oddělené mezerami. Chcete-li například zálohovat pouze tabulky table1 a tabulky 2 z "testdb", postupujte podle tohoto příkladu: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Chcete-li zálohovat více než jednu databázi najednou, použijte přepínač --database a uveďte názvy databází oddělené mezerami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Vytvoření databáze na cílovém serveru
Vytvořte prázdnou databázi na cílové databázi Azure pro server MariaDB, kde chcete migrovat data. K vytvoření databáze použijte nástroj, například MySQL Workbench. Databáze může mít stejný název jako databáze, která obsahuje vyřazovaná data nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v **přehledu** databáze Azure pro MariaDB.

![Vyhledání informací o připojení na webu Azure Portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Přidejte informace o připojení do pracovní plochy MySQL.

![Připojovací řetězec Pracovní ho pracovního stolu MySQL](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Obnovení databáze MariaDB
Po vytvoření cílové databáze můžete pomocí příkazu mysql nebo MySQL Workbench obnovit data do konkrétní nově vytvořené databáze ze souboru výpisu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
V tomto příkladu obnovit data do nově vytvořené databáze na cílové azure databáze pro server MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Export pomocí PHPMyAdmin
Chcete-li exportovat, můžete použít společný nástroj phpMyAdmin, který jste již nainstalovali lokálně ve vašem prostředí. Export databáze MariaDB pomocí PHPMyAdmin:
1. Otevřete phpMyAdmin.
2. Vyberte databázi. Klikněte na název databáze v seznamu vlevo. 
3. Klikněte na odkaz **Exportovat.** Zobrazí se nová stránka pro zobrazení výpisu databáze.
4. V oblasti Export klikněte na odkaz **Vybrat vše** a vyberte tabulky v databázi. 
5. V oblasti možností SQL klepněte na příslušné možnosti. 
6. Klepněte na možnost **Uložit jako soubor** a odpovídající možnost komprese a potom klepněte na tlačítko **Přejít.** Mělo by se zobrazit dialogové okno s výzvou k místnímu uložení souboru.

## <a name="import-using-phpmyadmin"></a>Import pomocí PHPMyAdmin
Import databáze je podobný exportu. Proveďte následující akce:
1. Otevřete phpMyAdmin. 
2. Na stránce nastavení phpMyAdmin klikněte na **Přidat** a přidejte databázi Azure pro server MariaDB. Zadejte údaje o připojení a přihlašovací údaje.
3. Vytvořte vhodně pojmenovanou databázi a vyberte ji na levé straně obrazovky. Chcete-li přepsat existující databázi, klepněte na název databáze, zaškrtněte všechna zaškrtávací políčka vedle názvů tabulek a vyberte **Přetažení,** chcete-li odstranit existující tabulky. 
4. Kliknutím na odkaz **SQL** zobrazíte stránku, na které můžete zadat příkazy SQL nebo nahrát soubor SQL. 
5. Pomocí tlačítka **procházet** vyhledejte soubor databáze. 
6. Kliknutím na tlačítko **Přejít** exportujte zálohu, spusťte příkazy SQL a znovu vytvořte databázi.

## <a name="next-steps"></a>Další kroky
- [Připojte aplikace k Azure Database pro MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
