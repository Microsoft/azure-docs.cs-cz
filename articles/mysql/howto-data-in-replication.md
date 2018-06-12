---
title: Konfigurace replikace dat k replikaci dat do Azure databáze pro databázi MySQL.
description: Tento článek popisuje postup nastavení dat v replikace pro databázi Azure pro databázi MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: f9517cb552130e340310abc4affdad8bdadc26fe
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265747"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Postup konfigurace pro replikaci dat MySQL v Azure databáze

V tomto článku se dozvíte, jak nastavit replikaci dat v databázi Azure pro službu MySQL podle konfigurace primárním serverem a serverem repliky.

Tento článek předpokládá, že máte alespoň zkušenosti s MySQL servery a databáze.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Vytvoření databáze MySQL serveru má být použit jako repliky

1. Vytvořit novou databázi MySQL serveru Azure

   Vytvořit nový server databáze MySQL (např. "replica.mysql.database.azure.com"). Odkazovat na [vytvoření Azure databáze MySQL serveru pomocí portálu Azure](quickstart-create-mysql-server-database-using-azure-portal.md) pro vytvoření serveru. Tento server je v replikaci dat v serveru "replika".

   > [!IMPORTANT]
   > Tento server musí být vytvořený v obecné účely nebo k paměťově optimalizovaným cenové úrovně.
   > 

2. Vytvoření stejných uživatelských účtů a odpovídající oprávnění

   Uživatelské účty nejsou replikovány z primárního serveru na server repliky. Pokud plánujete poskytuje uživatelům přístup k serveru repliky, musíte ručně vytvořit všechny účty a odpovídající oprávnění v tomto nově vytvořený Azure databáze pro server databáze MySQL.

## <a name="configure-the-primary-server"></a>Konfigurace primárního serveru

1. Zapnout binární protokolování

   Zkontrolujte, pokud je binární protokolování povoleno na primárním spuštěním následujícího příkazu: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud proměnná [ `log_bin` ](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) se vrátí se hodnota "ON" binární protokolování je na serveru povoleno. 

   Pokud `log_bin` se vrátí hodnotu "OFF", zapnout binární protokolování úpravou souboru my.cnf, který `log_bin=ON` a restartujte váš server, aby tato změna se projeví.

2. Nastavení primárního serveru

   Data v replikaci vyžaduje parametr `lower_case_table_names` konzistentní mezi primárním serverem a repliky servery. Tento parametr je 1 ve výchozím nastavení ve službě Azure Database pro databázi MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Vytvoření nové role replikace a nastavení oprávnění

   Vytvoření uživatelského účtu na primárním serveru, který je nakonfigurovaný s oprávněními replikace. To lze provést prostřednictvím příkazy SQL nebo nástroje, jako je MySQL Workbench. Zvažte, zda chcete použít k replikaci pomocí protokolu SSL, jak to bude potřeba zadat při vytváření uživatele. V dokumentaci MySQL pochopit, jak [přidejte uživatelské účty,](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) na primárním serveru. 

   V níže uvedených příkazů je přístup k primární z libovolného počítače, ne jenom na počítač, který hostuje primární sám sebe vytvořili novou roli replikace. Děje se tak, že zadáte "syncuser@'%'" v příkazu create uživatele. Najdete v dokumentaci MySQL Další informace o [zadání názvy účtů](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Příkaz SQL**

   *Replikace pomocí protokolu SSL*

   Vyžadování protokolu SSL pro připojení všech uživatelů, použijte následující příkaz pro vytvoření uživatele: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikace bez protokolu SSL*

   Pokud není vyžadován pro všechna připojení protokol SSL, použijte následující příkaz pro vytvoření uživatele:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Chcete-li vytvořit roli replikace v MySQL Workbench, otevřete **uživatele a oprávnění** panelu z **správy** panelu. Potom klikněte na **přidat účet**. 
 
   ![Uživatelé a oprávnění](./media/howto-data-in-replication/users_privileges.png)

   Zadejte uživatelské jméno do **přihlašovací jméno** pole. 

   ![Synchronizace uživatelů](./media/howto-data-in-replication/syncuser.png)
 
   Klikněte na **správní role** panelu a potom vyberte **replikace podřízený** ze seznamu **globální oprávnění**. Potom klikněte na **použít** pro vytvoření role replikace.

   ![Podřízený replikace](./media/howto-data-in-replication/replicationslave.png)


4. Nastavení primární serveru do režimu jen pro čtení

   Před zahájením dump na databázi, server musí být umístěny v režimu jen pro čtení. V režimu jen pro čtení, bude primární se nepodařilo zpracovat jakékoli transakce zápisu. Posuďte dopad na váš podnik a naplánovat okno jen pro čtení v dobu mimo špičku v případě potřeby.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Získání názvu souboru binárního protokolu a odsazení

   Spustit [ `show master status` ](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) příkaz, abyste zjistili aktuální název souboru binárního protokolu a posun.
    
   ```sql
   show master status;
   ```
   Výsledek by se měl jako následující. Ujistěte se, jak se použije v dalších krocích, poznamenejte si název binárního souboru.

   ![Hlavní stav výsledky](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-primary-server"></a>Výpis stavu a obnovit primární server

1. Vypsat všechny databáze z primárního serveru

   Mysqldump můžete použít k databázím výpis z vaší primární. Podrobnosti najdete v části [Dump & Obnovit](concepts-migrate-dump-restore.md). Není nutné dump MySQL knihovny a testovací knihovnou.

2. Nastavte primární server do režimu pro čtení i zápis

   Jakmile má byly zálohované databáze, změňte primární MySQL server zpátky do režimu pro čtení a zápis.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Obnovení souboru s výpisem nový server

   Obnovte soubor výpisu serveru vytvořené v databázi Azure pro službu MySQL. Odkazovat na [Dump & Obnovit](concepts-migrate-dump-restore.md) pro obnovení souboru výpisu k serveru databáze MySQL. Pokud soubor výpisu velká, můžete ho nahrajte do virtuálního počítače v Azure v rámci stejné oblasti jako server repliky. Obnovte do databáze Azure pro server databáze MySQL z virtuálního počítače.

## <a name="link-primary-and-replica-servers-to-start-data-in-replication"></a>Odkaz primární a serverem repliky, která měla spustit replikaci dat v

1. Primární server sady

   Všechny funkce replikace dat v provádějí uložené procedury. Můžete najít všechny postupy v [Data v uložené procedury replikace](reference-data-in-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench. 

   K propojení dvou serverů a spuštění replikace, přihlášení k cílovému serveru repliky v Azure DB pro službu MySQL a nastavit externí instance jako primární server. To se provádí pomocí `mysql.az_replication_change_primary` uložené procedury v Azure DB pro server databáze MySQL.

   ```sql
   CALL mysql.az_replication_change_primary('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: název hostitele primárního serveru
   - master_user: uživatelské jméno pro primární server
   - master_password: heslo pro primární server
   - master_log_file: název souboru binárního protokolu spuštění `show master status`
   - master_log_pos: binární protokol pozice spuštění `show master status`
   - master_ssl_ca: certifikát certifikační Autority kontextu. Pokud nepoužíváte protokol SSL, předejte prázdný řetězec.
       - Doporučuje se tento parametr v předat jako proměnnou. Podívejte se na následující příklady Další informace.

   **Příklady**

   *Replikace pomocí protokolu SSL*

   Proměnná `@cert` je vytvořené spuštěním následujících příkazů MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replikace pomocí protokolu SSL je nastavený mezi primárním serverem hostované v doméně "SpolecnostA.cz" a server repliky hostované v Azure Database pro databázi MySQL. Tuto uloženou proceduru běží v replice. 

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikace bez protokolu SSL*

   Replikace bez protokolu SSL je nastavený mezi primárním serverem hostované v doméně "SpolecnostA.cz" a server repliky hostované v Azure Database pro databázi MySQL. Tuto uloženou proceduru běží v replice.

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

2. Spuštění replikace

   Volání `mysql.az_replication_start` uložené procedury k inicializaci replikace.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Zkontrolujte stav replikace

   Volání [ `show slave status` ](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) příkaz na serveru repliky a zobrazit stav replikace.
    
   ```sql
   show slave status;
   ```

   Pokud stav `Slave_IO_Running` a `Slave_SQL_Running` jsou "Ano" a hodnota `Seconds_Behind_Master` je "0", dobře funguje replikace. `Seconds_Behind_Master` Určuje, jak pozdní repliky. Pokud hodnota není "0", znamená to, že je replika zpracování aktualizace. 

## <a name="other-stored-procedures"></a>Ostatní uložené procedury

### <a name="stop-replication"></a>Zastavit replikaci

Na zastavení replikace mezi primárním serverem a repliky serveru, použijte následující uložené procedury:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Odeberte vztah replikace

Chcete-li odebrat vztah mezi primárním serverem a repliky serveru, použijte následující uložené procedury:

```sql
CALL mysql.az_replication_remove_primary;
```

### <a name="skip-replication-error"></a>Chyba replikace přeskočit

Pokud chcete přeskočit Chyba replikace a povolí replikaci, aby bylo možné pokračovat, použijte následující uložené procedury:
    
```sql
CALL mysql.az_replication_skip_counter;
```