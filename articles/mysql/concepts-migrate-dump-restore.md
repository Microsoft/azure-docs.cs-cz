---
title: Migrace pomocí výpisu a obnovení Azure Database for MySQL
description: Tento článek popisuje dva běžné způsoby zálohování a obnovení databází v Azure Database for MySQL pomocí nástrojů, jako jsou mysqldump, MySQL Workbench a PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 7cc18980d1dddc33ddf98f06de70449dee22e2ac
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484589"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrace databáze MySQL do služby Azure Database for MySQL pomocí výpisu a obnovení.

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Tento článek popisuje dva běžné způsoby zálohování a obnovení databází v Azure Database for MySQL
- Výpis a obnovení z příkazového řádku (pomocí mysqldump)
- Výpis a obnovení pomocí PHPMyAdmin

## <a name="before-you-begin"></a>Než začnete
Pokud chcete projít tento průvodce, musíte mít:
- [Vytvoření serveru Azure Database for MySQL – Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- na počítači je nainstalovaný nástroj příkazového řádku [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) .
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) nebo jiný nástroj MySQL třetí strany, který umožňuje příkazy výpisu a obnovení.

> [!TIP]
> Pokud chcete migrovat velké databáze s velikostmi databáze více než 1 TBs, můžete zvážit použití nástrojů komunity jako **mydumper/myloader** , které podporují paralelní export a import. Naučte [se migrovat velké databáze MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).


## <a name="common-use-cases-for-dump-and-restore"></a>Běžné použití – případy výpisu a obnovení

Nejběžnější případy použití:

- **Přesun od jiného poskytovatele spravované služby** : Většina poskytovatelů spravované služby nemusí poskytnout přístup k fyzickému souboru úložiště z důvodu zabezpečení, takže je jedinou možností, jak provést migraci, je logické zálohování a obnovení.
- **Migrace z místního prostředí nebo virtuálního počítače** – Azure Database for MySQL nepodporuje obnovení fyzických záloh, což umožňuje jako jediný přístup logické zálohování a obnovení.
- **Přesunutí úložiště záloh z místně redundantního na geograficky redundantní úložiště** – Azure Database for MySQL umožňuje, aby se konfigurace místně redundantního úložiště nebo geograficky redundantního úložiště pro zálohování mohla povolit jenom během vytváření serveru. Po zřízení serveru nemůžete změnit možnost redundance úložiště zálohování. Aby bylo možné přesunout úložiště záloh z místně redundantního úložiště do geograficky redundantního úložiště, jedinou možností je vypsat a obnovit. 
-  **Migrace z alternativních úložných motorů do InnoDB** -Azure Database for MySQL podporuje jenom InnoDB modul úložiště, a proto nepodporuje alternativní úložné moduly. Pokud jsou vaše tabulky nakonfigurované s jinými úložnými moduly, převeďte je do formátu modulu InnoDB před migrací na Azure Database for MySQL.

    Pokud máte například WordPress nebo WebApp pomocí tabulek MyISAM, nejprve tyto tabulky převeďte tak, že před obnovením do Azure Database for MySQL převedete migraci do formátu InnoDB. Pomocí klauzule `ENGINE=InnoDB` Nastavte modul použitý při vytváření nové tabulky a potom přeneste data do kompatibilní tabulky před obnovením.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - Abyste se při výpisu databází vyhnuli problémům s kompatibilitou, ujistěte se, že zdrojový i cílový systém používají stejnou verzi MySQL. Pokud například váš stávající Server MySQL má verzi 5,7, měli byste migrovat na Azure Database for MySQL nakonfigurovaná tak, aby běžela verze 5,7. `mysql_upgrade`Příkaz nefunguje na Azure Database for MySQLm serveru a není podporovaný. 
> - Pokud potřebujete upgradovat v rámci verze MySQL, nejdřív vypsat nebo exportovat databázi nižší verze do vyšší verze MySQL ve vlastním prostředí. Potom spusťte `mysql_upgrade` příkaz, než se pokusíte o migraci do Azure Database for MySQL.

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
-   Použijte `skip-definer` možnost v mysqlpump k vynechání klauzule definice a zabezpečení SQL z příkazů CREATE pro zobrazení a uložené procedury.  Když znovu načtete soubor s výpisem paměti, vytvoří objekty, které používají výchozí definice a hodnoty zabezpečení SQL.
-   Zkopírujte záložní soubory do objektu blob Azure nebo do úložiště a proveďte obnovení z tohoto místa, což by mělo být mnohem rychlejší než provádění obnovení přes Internet.

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Vytvoření databáze na cílovém serveru Azure Database for MySQL
Na cílovém Azure Database for MySQL serveru vytvořte prázdnou databázi, do které chcete migrovat data. K vytvoření databáze použijte nástroj, jako je například MySQL Workbench nebo mysql.exe. Databáze může mít stejný název jako databáze, která obsahuje dumpingová data, nebo můžete vytvořit databázi s jiným názvem.

Pokud se chcete připojit, vyhledejte informace o připojení v **přehledu** Azure Database for MySQL.

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="Vyhledat informace o připojení v Azure Portal":::

Přidejte informace o připojení do aplikace MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="Vyhledat informace o připojení v Azure Portal":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>Příprava cílového serveru Azure Database for MySQL pro rychlé načítání dat
Chcete-li připravit cílový Azure Database for MySQL server pro rychlejší načítání dat, je třeba změnit následující parametry serveru a konfiguraci.
- max_allowed_packet – nastavte na 1073741824 (tj. 1 GB), aby se předešlo problémům s přetečením kvůli dlouhým řádkům.
- slow_query_log – pro vypnutí protokolu pomalého dotazu je nastavené na vypnuto. Tím se eliminuje režie způsobená pomalým protokolováním dotazů během načítání dat.
- query_store_capture_mode – pro vypnutí úložiště dotazů nastavte na hodnotu žádné. Tím se eliminuje režie způsobená aktivitami vzorkování podle úložiště dotazů.
- innodb_buffer_pool_size – navýšení kapacity serveru na 32 paměťově optimalizované SKU z cenové úrovně portálu během migrace, aby se zvýšila innodb_buffer_pool_size. Innodb_buffer_pool_size se dá zvýšit jenom tak, že se pro Azure Database for MySQL server škáluje výpočetní výkon.
- innodb_io_capacity & innodb_io_capacity_max-li z parametrů serveru v Azure Portal ke zvýšení využití vstupně-výstupních operací k optimalizaci rychlosti migrace, přejděte na 9000.
- innodb_write_io_threads & innodb_write_io_threads-li pro zvýšení rychlosti migrace v parametrech serveru v Azure Portal hodnotu 4, změňte hodnotu na 4.
- Horizontální navýšení kapacity úložiště – IOPs pro Azure Database for MySQL server se postupně zvyšují s nárůstem úrovně úložiště. V případě rychlejšího načítání možná budete chtít zvýšit úroveň úložiště a zvýšit tak zřízenou vstupně-výstupní operace. Mějte na paměti, že úložiště lze škálovat pouze nahoru, ne dolů.

Po dokončení migrace můžete vrátit zpět parametry serveru a konfiguraci výpočetních vrstev na předchozí hodnoty.

## <a name="dump-and-restore-using-mysqldump-utility"></a>Výpis a obnovení pomocí nástroje mysqldump

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Vytvoření záložního souboru z příkazového řádku pomocí mysqldump
Pokud chcete zálohovat existující databázi MySQL na místním serveru nebo na virtuálním počítači, spusťte následující příkaz:
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

K dispozici jsou následující parametry:
- [uname] Uživatelské jméno databáze
- dána Heslo pro vaši databázi (Všimněte si, že mezi-p a heslem není žádné místo)
- dbname Název vaší databáze
- [souborzálohy. SQL] název souboru pro zálohování databáze
- [--opt] Možnost mysqldump

Pokud například chcete zálohovat databázi s názvem ' TestDB ' na serveru MySQL s uživatelským jménem ' testuser ' a bez hesla k souboru testdb_backup. SQL, použijte následující příkaz. Příkaz zálohuje `testdb` databázi do souboru s názvem `testdb_backup.sql` , který obsahuje všechny příkazy SQL potřebné k opětovnému vytvoření databáze. Ujistěte se, že uživatelské jméno testuser má alespoň oprávnění SELECT pro dumpingové tabulky, zobrazit zobrazení pro dumpingové pohledy, aktivovat pro dumpingové triggery a UZAMKNOUT tabulky, pokud není použita možnost--Single-Transaction.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Nyní spusťte mysqldump k vytvoření zálohy databáze. `testdb`

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

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Obnovení databáze MySQL pomocí příkazového řádku nebo MySQL Workbench
Po vytvoření cílové databáze můžete pomocí příkazu MySQL nebo MySQL Workbench obnovit data do konkrétní nově vytvořené databáze ze souboru s výpisem paměti.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
V tomto příkladu obnovte data do nově vytvořené databáze na cílovém serveru Azure Database for MySQL.

Tady je příklad, jak použít tento **MySQL** pro **jeden server** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Tady je příklad, jak použít tento **MySQL** pro **flexibilní Server** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Výpis a obnovení pomocí PHPMyAdmin
Pomocí těchto kroků vypíšete a obnovíte databázi pomocí PHPMyadmin.

> [!NOTE]
> V případě jednoho serveru musí být uživatelské jméno v tomto formátu, ' username@servername ', ale pro flexibilní Server můžete použít ' ' username ', pokud použijete ' username@servername ' pro flexibilní Server, připojení se nezdaří.

### <a name="export-with-phpmyadmin"></a>Export pomocí PHPMyadmin
K exportu můžete použít nástroj Common Tool phpMyAdmin, který jste už možná nainstalovali místně ve vašem prostředí. Export databáze MySQL pomocí PHPMyAdmin:
1. Otevřete phpMyAdmin.
2. Vyberte svou databázi. V seznamu na levé straně klikněte na název databáze.
3. Klikněte na odkaz **exportovat** . Zobrazí se nová stránka pro zobrazení výpisu databáze.
4. V oblasti exportovat klikněte na odkaz **Vybrat vše** a vyberte tabulky v databázi.
5. V oblasti možnosti SQL klikněte na příslušné možnosti.
6. Klikněte na možnost **Uložit jako soubor** a odpovídající možnost komprese a potom klikněte na tlačítko **Přejít** . Zobrazí se dialogové okno s výzvou, abyste soubor uložili místně.

### <a name="import-using-phpmyadmin"></a>Import pomocí PHPMyAdmin
Import databáze je podobný exportu. Proveďte následující akce:
1. Otevřete phpMyAdmin.
2. Na stránce instalace phpMyAdmin klikněte na **Přidat** a přidejte server Azure Database for MySQL. Zadejte podrobnosti připojení a přihlašovací informace.
3. Vytvořte odpovídající pojmenovanou databázi a vyberte ji na levé straně obrazovky. Chcete-li přepsat existující databázi, klikněte na název databáze, zaškrtněte políčka vedle názvů tabulek a **Vyberte možnost Odstranit pro odstranění** existujících tabulek.
4. Kliknutím na odkaz **SQL** zobrazíte stránku, kde můžete zadat příkazy SQL, nebo nahrát soubor SQL.
5. K vyhledání databázového souboru použijte tlačítko **Procházet** .
6. Kliknutím na tlačítko **Přejít** Exportujte zálohu, spusťte příkazy SQL a znovu vytvořte databázi.

## <a name="known-issues"></a>Známé problémy
V případě známých problémů, tipů a triky vám doporučujeme podívat se na náš [blog techcommunity](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912).

## <a name="next-steps"></a>Další kroky
- [Připojte aplikace k Azure Database for MySQL](./howto-connection-string.md).
- Další informace o migraci databází do Azure Database for MySQL najdete v [Průvodci migrací databáze](https://aka.ms/datamigration).
- Pokud chcete migrovat velké databáze s velikostmi databáze více než 1 TBs, můžete zvážit použití nástrojů komunity jako **mydumper/myloader** , které podporují paralelní export a import. Naučte [se migrovat velké databáze MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
