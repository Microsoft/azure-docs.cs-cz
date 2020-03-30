---
title: Migrace pomocí výpisu a obnovení – Azure Database for MySQL
description: Tento článek vysvětluje dva běžné způsoby zálohování a obnovení databází v databázi Azure pro MySQL pomocí nástrojů, jako je mysqldump, MySQL Workbench a PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: b15da2aa83231bfdc8732995888349b06ab56d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163773"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrace databáze MySQL do služby Azure Database for MySQL pomocí výpisu a obnovení.
Tento článek vysvětluje dva běžné způsoby zálohování a obnovení databází v databázi Azure pro MySQL
- Výpis a obnovení z příkazového řádku (pomocí mysqldump) 
- Výpis a obnovení pomocí PHPMyAdmin 

## <a name="before-you-begin"></a>Než začnete
Chcete-li projít tento návod, musíte mít:
- [Vytvoření databáze Azure pro server MySQL – portál Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) příkaz-line nástroj nainstalován na stroji.
- MySQL Workbench [MySQL Workbench Stáhnout](https://dev.mysql.com/downloads/workbench/) nebo jiný nástroj MySQL třetí strany dělat výpis a obnovení příkazů.

## <a name="use-common-tools"></a>Použití běžných nástrojů
Pomocí běžných nástrojů a nástrojů, jako je MySQL Workbench nebo mysqldump, můžete vzdáleně připojit a obnovit data do Databáze Azure pro MySQL. Pomocí těchto nástrojů v klientském počítači s připojením k internetu se můžete připojit k databázi Azure pro MySQL. Pro nejlepší postupy zabezpečení použijte šifrované připojení SSL, najdete v tématu [Konfigurace připojení SSL v Azure Database for MySQL](concepts-ssl-connection-security.md). Při migraci do Azure Database pro MySQL není nutné přesunout soubory s výpisem stavu paměti do žádného zvláštního umístění cloudu. 

## <a name="common-uses-for-dump-and-restore"></a>Běžné použití pro výpis a obnovení
Můžete použít MySQL nástroje, jako je mysqldump a mysqlpump k výpisu a načtení databází do databáze Azure MySQL v několika běžných scénářích. V jiných scénářích můžete místo toho použít přístup [importu a exportu.](concepts-migrate-import-export.md)

- Při migraci celé databáze použijte výpisy databáze. Toto doporučení platí při přesunu velkého množství dat MySQL nebo pokud chcete minimalizovat přerušení služby pro živé weby nebo aplikace. 
-  Při načítání dat do služby Azure Database for MySQL se ujistěte, že všechny tabulky v databázi používají databázové jádro InnoDB. Azure Database for MySQL podporuje jenom modul úložiště InnoDB a proto nepodporuje alternativní moduly úložiště. Pokud jsou vaše tabulky nakonfigurované s jinými moduly úložiště, převeďte je před migrací do Databáze Azure pro MySQL do formátu modulu InnoDB.
   Například pokud máte WordPress nebo WebApp pomocí tabulek MyISAM, nejprve převést tyto tabulky migrací do formátu InnoDB před obnovením do databáze Azure pro MySQL. Pomocí klauzule `ENGINE=InnoDB` nastavte modul použitý při vytváření nové tabulky a potom přenést data do kompatibilní tabulky před obnovením. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Abyste se při výpisu databází vyhnuli problémům s kompatibilitou, ujistěte se, že zdrojový i cílový systém používají stejnou verzi MySQL. Pokud je například váš stávající server MySQL verze 5.7, měli byste migrovat do Azure Database for MySQL nakonfigurované pro spuštění verze 5.7. Příkaz `mysql_upgrade` nefunguje v databázi Azure pro server MySQL a není podporován. Pokud potřebujete upgradovat napříč verzemi MySQL, nejprve vyložit nebo exportovat nižší verzi databáze do vyšší verze MySQL ve vašem vlastním prostředí. Pak `mysql_upgrade`spusťte , před pokusem o migraci do databáze Azure pro MySQL.

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
-   Použijte `skip-definer` možnost v mysqlpump vynechat definer a SQL SECURITY klauzule z příkazy create pro zobrazení a uložené procedury.  Při opětovném načtení souboru s výpisem stavu paměti vytvoří objekty, které používají výchozí hodnoty DEFINER a SQL SECURITY.
-   Zkopírujte záložní soubory do objektu blob/store Azure a proveďte obnovení odtud, což by mělo být mnohem rychlejší než provádění obnovení přes Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Vytvoření záložního souboru z příkazového řádku pomocí mysqldump
Chcete-li zálohovat existující databázi MySQL na místním místním serveru nebo ve virtuálním počítači, spusťte následující příkaz: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry, které mají být poskytnuty, jsou:
- [uname] Uživatelské jméno databáze 
- To je v pořádku. Heslo pro vaši databázi (všimněte si, že mezi -p a heslem není mezera) 
- [dbname] Název databáze 
- [backupfile.sql] Název souboru pro zálohování databáze 
- [--opt] Možnost mysqldump 

Chcete-li například zálohovat databázi s názvem "testdb" na serveru MySQL s uživatelským jménem "testuser" a bez hesla k souboru testdb_backup.sql, použijte následující příkaz. Příkaz zálohuje `testdb` databázi do `testdb_backup.sql`souboru s názvem , který obsahuje všechny příkazy SQL potřebné k opětovnému vytvoření databáze. 

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

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Vytvoření databáze na cílové databázi Azure pro server MySQL
Vytvořte prázdnou databázi na cílové azure databázi pro server MySQL, kde chcete migrovat data. K vytvoření databáze použijte nástroj, například MySQL Workbench. Databáze může mít stejný název jako databáze, která obsahuje vyřazovaná data nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v **přehledu** databáze Azure pro MySQL.

![Vyhledání informací o připojení na webu Azure Portal](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Přidejte informace o připojení do pracovní plochy MySQL.

![Připojovací řetězec Pracovní ho pracovního stolu MySQL](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Obnovte databázi MySQL pomocí příkazového řádku nebo MySQL Workbench
Po vytvoření cílové databáze můžete pomocí příkazu mysql nebo MySQL Workbench obnovit data do konkrétní nově vytvořené databáze ze souboru výpisu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
V tomto příkladu obnovte data do nově vytvořené databáze na cílové azure databázi pro server MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Export pomocí PHPMyAdmin
Chcete-li exportovat, můžete použít společný nástroj phpMyAdmin, který jste již nainstalovali lokálně ve vašem prostředí. Exportovat databázi MySQL pomocí PHPMyAdmin:
1. Otevřete phpMyAdmin.
2. Vyberte databázi. Klikněte na název databáze v seznamu vlevo. 
3. Klikněte na odkaz **Exportovat.** Zobrazí se nová stránka pro zobrazení výpisu databáze.
4. V oblasti Export klikněte na odkaz **Vybrat vše** a vyberte tabulky v databázi. 
5. V oblasti možností SQL klepněte na příslušné možnosti. 
6. Klepněte na možnost **Uložit jako soubor** a odpovídající možnost komprese a potom klepněte na tlačítko **Přejít.** Mělo by se zobrazit dialogové okno s výzvou k místnímu uložení souboru.

## <a name="import-using-phpmyadmin"></a>Import pomocí PHPMyAdmin
Import databáze je podobný exportu. Proveďte následující akce:
1. Otevřete phpMyAdmin. 
2. Na stránce nastavení phpMyAdmin klikněte na **Přidat** a přidejte databázi Azure pro server MySQL. Zadejte údaje o připojení a přihlašovací údaje.
3. Vytvořte vhodně pojmenovanou databázi a vyberte ji na levé straně obrazovky. Chcete-li přepsat existující databázi, klepněte na název databáze, zaškrtněte všechna zaškrtávací políčka vedle názvů tabulek a vyberte **Přetažení,** chcete-li odstranit existující tabulky. 
4. Kliknutím na odkaz **SQL** zobrazíte stránku, na které můžete zadat příkazy SQL nebo nahrát soubor SQL. 
5. Pomocí tlačítka **procházet** vyhledejte soubor databáze. 
6. Kliknutím na tlačítko **Přejít** exportujte zálohu, spusťte příkazy SQL a znovu vytvořte databázi.

## <a name="next-steps"></a>Další kroky
- [Připojte aplikace k Azure Database for MySQL](./howto-connection-string.md).
- Další informace o migraci databází do databáze Azure pro MySQL najdete v [průvodci migrací databáze](https://aka.ms/datamigration).
