---
title: Migrace pomocí výpisu a obnovení Azure Database for MariaDB
description: Tento článek popisuje dva běžné způsoby zálohování a obnovení databází v Azure Database for MariaDB pomocí nástrojů, jako jsou mysqldump, MySQL Workbench a PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 8f7cb0710c11e0db9628ad19e2ede7ff05a19f88
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664966"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrace databáze MariaDB do Azure Database for MariaDB pomocí výpisu a obnovení
Tento článek popisuje dva běžné způsoby zálohování a obnovení databází v Azure Database for MariaDB
- Výpis a obnovení z příkazového řádku (pomocí mysqldump) 
- Výpis a obnovení pomocí PHPMyAdmin

## <a name="before-you-begin"></a>Než začnete
Pokud chcete projít tento průvodce, musíte mít:
- [Vytvoření serveru Azure Database for MariaDB – Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- na počítači je nainstalovaný nástroj příkazového řádku [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) .
- Stažení aplikace MySQL Workbench [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) nebo jiného nástroje MySQL třetí strany, které umožňují příkazy k výpisu a obnovení.

## <a name="use-common-tools"></a>Použití běžných nástrojů
Pomocí běžných nástrojů a nástrojů, jako je MySQL Workbench nebo mysqldump, můžete vzdáleně připojit a obnovit data do Azure Database for MariaDB. Pomocí takových nástrojů na klientském počítači připojte k Azure Database for MariaDB připojení k Internetu. Použití šifrovaného připojení SSL pro osvědčené postupy zabezpečení najdete v tématu [Konfigurace připojení SSL v Azure Database for MariaDB](concepts-ssl-connection-security.md). Při migraci na Azure Database for MariaDB nemusíte soubory s výpisem paměti přesouvat do jakéhokoli zvláštního umístění v cloudu. 

## <a name="common-uses-for-dump-and-restore"></a>Běžné použití pro výpis a obnovení
Pomocí nástrojů MySQL, jako je mysqldump a mysqlpump, můžete vypsat a načíst databáze do serveru Azure Database for MariaDB v několika běžných scénářích. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Při migraci celé databáze používejte výpisy paměti databáze. Toto doporučení se drží při přesunu velkých objemů dat, nebo když chcete minimalizovat přerušení služby pro živé weby nebo aplikace. 
-  Při načítání dat do služby Azure Database for MariaDB se ujistěte, že všechny tabulky v databázi používají databázové jádro InnoDB. Azure Database for MariaDB podporuje jenom modul úložiště InnoDB, a proto nepodporuje alternativní úložné moduly. Pokud jsou vaše tabulky nakonfigurované s jinými úložnými moduly, převeďte je do formátu modulu InnoDB před migrací na Azure Database for MariaDB.
   Pokud máte například WordPress nebo WebApp pomocí tabulek MyISAM, nejprve tyto tabulky převeďte tak, že před obnovením do Azure Database for MariaDB převedete migraci do formátu InnoDB. Pomocí klauzule `ENGINE=InnoDB` Nastavte modul použitý při vytváření nové tabulky a potom přeneste data do kompatibilní tabulky před obnovením. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Abyste se při výpisu databází vyhnuli problémům s kompatibilitou, ujistěte se, že zdrojový i cílový systém používají stejnou verzi MariaDB. Pokud například váš stávající server MariaDB má verzi 10,2, měli byste migrovat na Azure Database for MariaDB nakonfigurovanou na run verze 10,2. `mysql_upgrade`Příkaz nefunguje na Azure Database for MariaDBm serveru a není podporovaný. Pokud potřebujete upgradovat přes MariaDB verze, nejdřív vypíšete nebo exportujte databázi nižší verze do vyšší verze MariaDB ve vašem vlastním prostředí. Potom spusťte `mysql_upgrade` příkaz, než se pokusíte o migraci do Azure Database for MariaDB.

## <a name="performance-considerations"></a>Otázky výkonu
Pro optimalizaci výkonu si všimněte těchto doporučení při dumpingu velkých databází:
-   `exclude-triggers`Při ukládání databází použijte možnost v mysqldump. Vylučte triggery ze souborů s výpisem paměti, aby nedocházelo k příkazům triggeru během obnovování dat. 
-   Pomocí `single-transaction` Možnosti nastavte režim izolace transakce na možnost opakované čtení a odešlete příkaz spustit transakci SQL serveru před výpisy dat. Dumpingové množství tabulek v rámci jedné transakce způsobí, že se během obnovování spotřebuje nějaké dodatečné úložiště. `single-transaction`Možnost a `lock-tables` možnost se vzájemně vylučují, protože uzamčené tabulky způsobují implicitní potvrzení všech čekajících transakcí. Pokud chcete vypsat velké tabulky, zkombinujte `single-transaction` možnost s `quick` možností. 
-   Použijte `extended-insert` syntaxi s více řádky, která obsahuje několik seznamů hodnot. Výsledkem je menší soubor výpisu paměti a při opětovném načtení souboru se urychlí vložení.
-  `order-by-primary`Při výpisu databází použijte možnost mysqldump, aby se data využívala v pořadí primárního klíče.
-   `disable-keys`Pokud chcete před načtením zakázat omezení cizího klíče, použijte možnost v mysqldump. Zakázání kontrol cizích klíčů poskytuje nárůst výkonu. Povolte omezení a ověřte data po zatížení, abyste zajistili referenční integritu.
-   V případě potřeby použijte dělené tabulky.
-   Načtěte data paralelně. Vyhněte se příliš moc paralelismu, protože by došlo k dosažení limitu prostředků a monitorování prostředků pomocí metrik dostupných v Azure Portal. 
-   Použijte `defer-table-indexes` možnost v mysqlpump při vytváření dumpingových databází, aby se vytvoření indexu stalo po načtení dat tabulky.
-   Zkopírujte záložní soubory do objektu blob Azure nebo do úložiště a proveďte obnovení z tohoto místa, což by mělo být mnohem rychlejší než provádění obnovení přes Internet.

## <a name="create-a-backup-file"></a>Vytvoření záložního souboru
Pokud chcete zálohovat stávající databázi MariaDB na místním serveru nebo na virtuálním počítači, spusťte následující příkaz pomocí mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

K dispozici jsou následující parametry:
- [uname] Uživatelské jméno databáze 
- dána Heslo pro vaši databázi (Všimněte si, že mezi-p a heslem není žádné místo) 
- dbname Název vaší databáze 
- [souborzálohy. SQL] název souboru pro zálohování databáze 
- [--opt] Možnost mysqldump 

Pokud například chcete zálohovat databázi s názvem ' TestDB ' na serveru MariaDB s uživatelským jménem ' testuser ' a bez hesla k souboru testdb_backup. SQL, použijte následující příkaz. Příkaz zálohuje `testdb` databázi do souboru s názvem `testdb_backup.sql` , který obsahuje všechny příkazy SQL potřebné k opětovnému vytvoření databáze. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Chcete-li vybrat konkrétní tabulky v databázi pro zálohování, uveďte názvy tabulek oddělené mezerami. Chcete-li například zálohovat pouze tabulky Tabulka1 a Tabulka2 z ' TestDB ', postupujte podle následujícího příkladu: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Chcete-li zálohovat více než jednu databázi najednou, použijte přepínač--Database a seznam názvů databází oddělených mezerami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Vytvoření databáze na cílovém serveru
Na cílovém Azure Database for MariaDB serveru vytvořte prázdnou databázi, do které chcete migrovat data. K vytvoření databáze použijte nástroj, jako je například MySQL Workbench. Databáze může mít stejný název jako databáze, která obsahuje dumpingová data, nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v **přehledu** Azure Database for MariaDB.

![Vyhledat informace o připojení v Azure Portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Přidejte informace o připojení do aplikace MySQL Workbench.

![Připojovací řetězec MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Obnovení databáze MariaDB
Po vytvoření cílové databáze můžete pomocí příkazu MySQL nebo MySQL Workbench obnovit data do konkrétní nově vytvořené databáze ze souboru s výpisem paměti.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
V tomto příkladu obnovte data do nově vytvořené databáze na cílovém serveru Azure Database for MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Export pomocí PHPMyAdmin
K exportu můžete použít nástroj Common Tool phpMyAdmin, který jste už možná nainstalovali místně ve vašem prostředí. Export databáze MariaDB pomocí PHPMyAdmin:
1. Otevřete phpMyAdmin.
2. Vyberte svou databázi. V seznamu na levé straně klikněte na název databáze. 
3. Klikněte na odkaz **exportovat** . Zobrazí se nová stránka pro zobrazení výpisu databáze.
4. V oblasti exportovat klikněte na odkaz **Vybrat vše** a vyberte tabulky v databázi. 
5. V oblasti možnosti SQL klikněte na příslušné možnosti. 
6. Klikněte na možnost **Uložit jako soubor** a odpovídající možnost komprese a potom klikněte na tlačítko **Přejít** . Zobrazí se dialogové okno s výzvou, abyste soubor uložili místně.

## <a name="import-using-phpmyadmin"></a>Import pomocí PHPMyAdmin
Import databáze je podobný exportu. Proveďte následující akce:
1. Otevřete phpMyAdmin. 
2. Na stránce instalace phpMyAdmin klikněte na **Přidat** a přidejte server Azure Database for MariaDB. Zadejte podrobnosti připojení a přihlašovací informace.
3. Vytvořte odpovídající pojmenovanou databázi a vyberte ji na levé straně obrazovky. Chcete-li přepsat existující databázi, klikněte na název databáze, zaškrtněte políčka vedle názvů tabulek a **Vyberte možnost Odstranit pro odstranění** existujících tabulek. 
4. Kliknutím na odkaz **SQL** zobrazíte stránku, kde můžete zadat příkazy SQL, nebo nahrát soubor SQL. 
5. K vyhledání databázového souboru použijte tlačítko **Procházet** . 
6. Kliknutím na tlačítko **Přejít** Exportujte zálohu, spusťte příkazy SQL a znovu vytvořte databázi.

## <a name="next-steps"></a>Další kroky
- [Připojte aplikace k Azure Database for MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
