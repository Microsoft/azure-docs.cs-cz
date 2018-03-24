---
title: Migraci databáze MySQL pomocí výpis nebo obnovení v databázi Azure pro databázi MySQL
description: Tento článek vysvětluje dvě běžné způsoby zálohování a obnovení databází v Azure Database pro databázi MySQL, pomocí nástrojů, jako je mysqldump, MySQL Workbench a PHPMyAdmin.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: ef35ee881923c69d41b79fd6cb8464c695c614f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migraci databáze MySQL do Azure Database pro databázi MySQL pomocí výpisu a obnovení
Tento článek vysvětluje dvě běžné způsoby zálohování a obnovení databází v Azure Database pro databázi MySQL
- Výpis a obnovení z příkazového řádku (pomocí mysqldump) 
- Výpis stavu a obnovení pomocí PHPMyAdmin 

## <a name="before-you-begin"></a>Než začnete
Na krok tímto průvodcem postupy, musíte mít:
- [Vytvoření Azure databáze MySQL serveru – portál Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) na počítači nainstalovaný nástroj příkazového řádku.
- MySQL Workbench [MySQL Workbench Stáhnout](https://dev.mysql.com/downloads/workbench/), Toad, Navicat nebo jiný nástroj třetí strany, MySQL, výpisů a obnovení příkazy.

## <a name="use-common-tools"></a>Běžné nástroje pro použití
Použijte běžné nástroje a nástroje, například MySQL Workbench, mysqldump, Toad nebo Navicat vzdáleně připojit a obnovení dat do Azure databáze pro databázi MySQL. Pomocí těchto nástrojů na klientský počítač s připojením k Internetu pro připojení k databázi Azure pro databázi MySQL. Používat připojení šifrovaná protokolem SSL pro osvědčené postupy zabezpečení, viz také [připojení konfigurace protokolu SSL ve službě Azure Database pro databázi MySQL](concepts-ssl-connection-security.md). Není nutné k přesunutí souborů výpisu do libovolného umístění, speciální cloudu při migraci na Azure Database pro databázi MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Běžná použití pro výpis a obnovení
MySQL nástroje například mysqldump a mysqlpump k výpisu a zatížení databáze do databáze MySQL Azure můžete používat v několika běžné scénáře. V dalších scénářích, můžete použít [Import a Export](concepts-migrate-import-export.md) přístupu místo.

- Použít databázi vypíše při migraci celé databáze. Toto doporučení obsahuje při přesunu velké množství dat MySQL, nebo pokud chcete minimalizovat přerušení poskytování služeb pro fungujícími weby nebo aplikace. 
-  Ujistěte se, že všechny tabulky v databázi použít modul InnoDB úložiště při načítání dat do Azure databáze pro databázi MySQL. Azure databáze pro databázi MySQL podporuje pouze stroj InnoDB úložiště a proto nepodporuje moduly alternativní úložiště. Pokud vaše tabulky jsou nakonfigurované další moduly úložiště, je převeďte do formátu modul InnoDB před migrací do Azure Database pro databázi MySQL.
   Například pokud máte WordPress nebo WebApp pomocí tabulky MyISAM, nejprve převeďte těchto tabulek a migrujte do formátu InnoDB před obnovením do Azure Database pro databázi MySQL. Použijte klauzuli `ENGINE=InnoDB` nastavit modul použít při vytváření nové tabulky, potom přenést data do tabulky kompatibilní ještě před obnovením. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Abyste předešli problémům s kompatibilitou, zkontrolujte, zda že stejnou verzi databáze MySQL se používá na zdrojovém a cílovém systému při vypsání databáze. Například pokud je existující server MySQL verzi 5.7, pak měli byste migrovat na Azure Database pro databázi MySQL, které jsou nakonfigurovány na spuštění verzi 5.7. `mysql_upgrade` Příkaz nefunguje v databázi Azure pro server databáze MySQL a není podporována. Pokud potřebujete k upgradu mezi verzemi MySQL, nejprve dump nebo exportovat nižší verze databáze na vyšší verzi MySQL ve svém vlastním prostředí. Spusťte `mysql_upgrade`, před migrací do Azure Database pro databázi MySQL.

## <a name="performance-considerations"></a>Otázky výkonu
Za účelem optimalizace výkonu, vzít v úvahu tyto aspekty při vypsání velké databáze:
-   Použití `exclude-triggers` možnost v mysqldump při vypsání databáze. Vyloučení ze souborů výpisu paměti, aby se zabránilo příkazy aktivační událost aktivuje během obnovení dat aktivační události. 
-   Použití `single-transaction` možnost nastavit režimu izolace transakce na REPEATABLE READ a odešle příkazu START transakce SQL serveru před vypsání data. Vypsání mnoho tabulek v rámci jedné transakce způsobí, že některé dodatečné úložiště, který se má používat při obnovení. `single-transaction` Možnost a `lock-tables` možnost se vzájemně vylučují, protože ZÁMEK tabulky způsobí, že všechny čekající transakcí, které budou potvrzeny implicitně. Výpis stavu velké tabulky kombinovat `single-transaction` možnost s `quick` možnost. 
-   Použití `extended-insert` více řádků syntaxi, která obsahuje několik seznamů hodnot. To má za následek menší soubor výpisu a urychluje vložení, když je znovu načíst soubor.
-  Použití `order-by-primary` možnost v mysqldump při vypsání databáze, tak, aby data je vytvořena v primární klíče pořadí.
-   Použití `disable-keys` možnost v mysqldump při vypsání data, zakázat omezení cizích klíčů před zatížení. Zakázání cizího klíče kontroly poskytuje větší zvýšení výkonu. Povolte omezení a ověřit data po zatížení zajistit referenční integrity.
-   Dělené tabulky v případě nutnosti použijte.
-   Načtení dat paralelně. Vyhněte se příliš mnoho paralelismus, který by vést k dosáhl limitu prostředků a sledování prostředků pomocí metriky, které jsou k dispozici na portálu Azure. 
-   Použití `defer-table-indexes` možnost v mysqlpump při vypsání databáze, takže vytvoření indexu se stane po načtení dat tabulky.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Vytvoření záložního souboru z příkazového řádku pomocí mysqldump
Pokud chcete zálohovat existující databázi MySQL na serveru místní, nebo ve virtuálním počítači, spusťte následující příkaz: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Zadejte parametry jsou:
- [uname] Vaše uživatelské jméno databáze 
- [předat] Heslo pro vaši databázi (Všimněte si, není mezera mezi -p a heslo) 
- [dbname] Název databáze 
- [backupfile.sql] název souboru pro zálohování databáze 
- [--opt] Možnost mysqldump 

Například pokud chcete zálohovat databáze s názvem 'testdb' na serveru databáze MySQL s uživatelským jménem 'testuser' a bez hesla do souboru testdb_backup.sql, použijte následující příkaz. Příkaz zálohuje `testdb` databáze do souboru s názvem `testdb_backup.sql`, která obsahuje všechny příkazy potřebné k vytvoření nové databáze SQL. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Pokud chcete vybrat konkrétní tabulky v databázi zálohovat, seznam názvy tabulek, oddělené mezerami. Například pokud chcete zálohovat pouze tabulky1 a tabulky2 tabulky z 'testdb', postupujte v tomto příkladu: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Chcete-li více než jednu databázi zálohovat najednou, použijte--databáze přepínače a seznamu názvy databází, oddělené mezerami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Chcete-li v jednom okamžiku zálohovat všechny databáze na serveru, měli byste použít možnost – všechny databáze.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Vytvoření databáze v cílové databázi Azure pro server databáze MySQL
Vytvořte prázdnou databázi v cílové databázi Azure pro server databáze MySQL, ve které chcete migrovat data. Pomocí nástroje, jako je MySQL Workbench, Toad nebo Navicat k vytvoření databáze. Databáze může mít stejný název jako databáze, která je obsažená dumpingových dat nebo můžete vytvořit databázi s jiným názvem.

Se připojí, vyhledejte informace o připojení v **přehled** Azure databáze pro databázi MySQL.

![Vyhledat informace o připojení na portálu Azure](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Přidáte informace o připojení do vaší databáze MySQL Workbench.

![MySQL Workbench připojovací řetězec](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Obnovení databáze MySQL pomocí příkazového řádku nebo MySQL Workbench
Po vytvoření cílové databázi, můžete použít příkaz mysql nebo MySQL Workbench k obnovení dat do nově vytvořené databáze ze souboru s výpisem konkrétní.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
V tomto příkladu obnovte data do nově vytvořené databáze v cílové databázi Azure pro server databáze MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportovat pomocí PHPMyAdmin
Pokud chcete exportovat, můžete použít běžný phpMyAdmin nástroj, který může již byl nainstalován místně ve vašem prostředí. Export databáze MySQL pomocí PHPMyAdmin:
- Otevřete phpMyAdmin.
- Vyberte svou databázi. Klikněte na název databáze v seznamu na levé straně. 
- Klikněte **exportovat** odkaz. Chcete-li zobrazit výpis databáze, zobrazí se nová stránka.
- V oblasti exportu, klikněte na **Vybrat vše** odkaz vybrat tabulky v databázi. 
- V oblasti možnosti SQL klikněte na požadované možnosti. 
- Klikněte na tlačítko **uložit jako soubor** možnost a odpovídající komprese a potom klikněte na **přejděte** tlačítko. Dialogové okno by se zobrazit požadavkem, abyste uložte soubor místně.

## <a name="import-using-phpmyadmin"></a>Import pomocí PHPMyAdmin
Import vaše databáze je podobná export. Proveďte následující akce:
- Otevřete phpMyAdmin. 
- Na stránce instalace phpMyAdmin klikněte na tlačítko **přidat** přidat databáze Azure pro server databáze MySQL. Zadejte podrobnosti připojení a přihlašovací údaje.
- Vytvoření příslušně pojmenovaných databáze a vyberte jej na levé straně obrazovky. Přepsání existující databázi, klikněte na název databáze, vyberte všechny zaškrtnutí políček vedle názvů tabulek a vyberte **vyřadit** odstranit existující tabulky. 
- Klikněte **SQL** odkaz na zobrazení stránky, kde můžete zadat v příkazech SQL nebo nahrát soubor SQL. 
- Použití **Procházet** tlačítko Najít databázový soubor. 
- Klikněte **přejděte** tlačítko Exportovat zálohování, spuštěním příkazů SQL a znovu vytvořit databázi.

## <a name="next-steps"></a>Další postup
[Připojení aplikace k databázi Azure pro databázi MySQL](./howto-connection-string.md)
