---
title: Konfigurace replikace dat – Azure Database for MariaDB
description: Tento článek popisuje, jak nastavit Replikace vstupních dat v Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: ed1031bd45ca4f1223de9c0bce2053a393e3a2c6
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665119"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurace Replikace vstupních dat v Azure Database for MariaDB

Tento článek popisuje, jak nastavit [replikace vstupních dat](concepts-data-in-replication.md) v Azure Database for MariaDB konfigurací zdrojového serveru a serveru repliky. V tomto článku se předpokládá, že máte několik předchozích zkušeností s MariaDB servery a databázemi.

Pokud chcete ve službě Azure Database for MariaDB vytvořit repliku, [replikace vstupních dat](concepts-data-in-replication.md) synchronizuje data ze zdrojového serveru MariaDB místně, do virtuálních počítačů (VM) nebo do cloudových databázových služeb. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MariaDB. Další informace o replikaci binlog najdete v tématu [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

Před provedením kroků v tomto článku zkontrolujte [omezení a požadavky](concepts-data-in-replication.md#limitations-and-considerations) na replikaci dat.

> [!NOTE]
> Pokud je váš zdrojový Server verze 10,2 nebo novější, doporučujeme nastavit Replikace vstupních dat pomocí [globálního ID transakce](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Tento článek obsahuje odkazy na _podřízený_ termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Vytvoření serveru MariaDB pro použití jako repliky

1. Vytvořte nový Azure Database for MariaDB Server (například replica.mariadb.database.azure.com). Server je server repliky v Replikace vstupních dat.

    Další informace o vytváření serverů najdete v tématu [vytvoření serveru Azure Database for MariaDB pomocí Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Je nutné vytvořit server Azure Database for MariaDB v cenové úrovni optimalizované pro Pro obecné účely nebo paměť.

2. Vytváření identických uživatelských účtů a odpovídajících oprávnění

    Uživatelské účty se nereplikují ze zdrojového serveru na server repliky. Chcete-li poskytnout uživatelům přístup k serveru repliky, je nutné ručně vytvořit všechny účty a odpovídající oprávnění na nově vytvořeném serveru Azure Database for MariaDB.

3. Přidejte IP adresu zdrojového serveru k pravidlům brány firewall repliky. 

   Pomocí webu [Azure Portal](howto-manage-firewall-portal.md) nebo [Azure CLI](howto-manage-firewall-cli.md) aktualizujte pravidla brány firewall.

## <a name="configure-the-source-server"></a>Konfigurace zdrojového serveru

Následující kroky připravují a konfigurují místně hostovaný Server MariaDB, na virtuálním počítači nebo v cloudové databázové službě pro Replikace vstupních dat. Server MariaDB je zdrojem v Replikace vstupních dat.

1. Než budete pokračovat, zkontrolujte [požadavky primárního serveru](concepts-data-in-replication.md#requirements) . 

2. Ujistěte se, že zdrojový server umožňuje příchozí i odchozí provoz na portu 3306 a že má zdrojový server **veřejnou IP adresu**, služba DNS je veřejně přístupná nebo má plně kvalifikovaný název domény (FQDN). 

   Otestujte připojení ke zdrojovému serveru tak, že se pokusíte připojit z nástroje, jako je třeba příkazový řádek MySQL hostovaný na jiném počítači nebo [Azure Cloud Shell](../cloud-shell/overview.md) k dispozici v Azure Portal.

   Pokud má vaše organizace přísné zásady zabezpečení a neumožňuje všem IP adresám na zdrojovém serveru povolit komunikaci z Azure na zdrojový server, můžete k určení IP adresy serveru Azure Database for MariaDB použít následující příkaz.

   1. Přihlaste se ke svému Azure Database for MariaDB pomocí nástroje, jako je třeba příkaz MySQL Command line.
   2. Spusťte následující dotaz.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Níže je uvedený ukázkový výstup:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Ukončete z příkazového řádku MySQL.
   4. Pro získání IP adresy spusťte níže v nástroji příkazového testu.

      ```bash
      ping <output of step 2b>
      ```

      Například:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Nakonfigurujte pravidla brány firewall zdrojového serveru tak, aby zahrnovala IP adresu předchozího kroku na portu 3306.

   > [!NOTE]
   > Tato IP adresa se může změnit v důsledku operací údržby nebo nasazení. Tato metoda připojení je určena jenom pro zákazníky, kteří nemůžou dovolit všem IP adresám na portu 3306.

3. Zapněte binární protokolování.

    Pokud chcete zjistit, jestli je na primárním počítači povolené binární protokolování, zadejte následující příkaz:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud proměnná [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) vrátí hodnotu `ON` , binární protokolování je na vašem serveru povolené.

   Pokud `log_bin` vrátí hodnotu `OFF` , upravte soubor **My. CNF** tak, aby se `log_bin=ON` zapíná binární protokolování. Aby se změna projevila, restartujte server.

4. Nakonfigurujte nastavení zdrojového serveru.

    Replikace vstupních dat vyžaduje, aby byl parametr `lower_case_table_names` konzistentní mezi zdrojovým serverem a serverem repliky. `lower_case_table_names`Parametr je ve výchozím nastavení nastaven na `1` Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Vytvořte novou roli replikace a nastavte oprávnění.

   Na zdrojovém serveru vytvořte uživatelský účet, který je nakonfigurovaný s oprávněními replikace. Účet můžete vytvořit pomocí příkazů SQL nebo MySQL Workbench. Pokud plánujete replikaci s protokolem SSL, je nutné tuto specifikaci zadat při vytváření uživatelského účtu.

   Informace o přidávání uživatelských účtů na zdrojovém serveru najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Pomocí následujících příkazů může nová role replikace přistupovat ke zdroji z libovolného počítače, nikoli jenom k počítači, který je hostitelem samotného zdroje. Pro tento přístup zadejte **syncuser \@ '% '** v příkazu pro vytvoření uživatele.

   Další informace o dokumentaci k MariaDB najdete v tématu [Určení názvů účtů](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Příkaz SQL**

   - Replikace s protokolem SSL

       Chcete-li pro všechna připojení uživatelů vyžadovat protokol SSL, zadejte následující příkaz pro vytvoření uživatele:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikace bez SSL

       Pokud pro všechna připojení není vyžadován protokol SSL, zadejte následující příkaz pro vytvoření uživatele:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Pokud chcete vytvořit roli replikace v aplikaci MySQL Workbench, vyberte v podokně **Správa** možnost **Uživatelé a oprávnění**. Pak vyberte **Přidat účet**.

   ![Uživatelé a oprávnění](./media/howto-data-in-replication/users_privileges.png)

   Do pole **přihlašovací jméno** zadejte uživatelské jméno.

   ![Synchronizovat uživatele](./media/howto-data-in-replication/syncuser.png)

   Vyberte panel **role pro správu** a potom v seznamu **globálních oprávnění** vyberte **replikace podřízená**. Pokud chcete vytvořit roli replikace, vyberte **použít** .

   ![Replikace podřízených](./media/howto-data-in-replication/replicationslave.png)

6. Nastavte zdrojový server na režim jen pro čtení.

   Než vypíšete databázi, musí být server umístěn v režimu jen pro čtení. V režimu jen pro čtení nemůže zdroj zpracovat žádné transakce zápisu. Aby se zabránilo dopadu na firmu, naplánujte v době mimo špičku okno s oprávněním jen pro čtení.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Získá aktuální binární název a posun souboru protokolu.

   Chcete-li určit aktuální binární název a posun souboru protokolu, spusťte příkaz [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .

   ```sql
   show master status;
   ```

   Výsledky by měly vypadat podobně jako v následující tabulce:

   ![Výsledky hlavního stavu](./media/howto-data-in-replication/masterstatus.png)

   Poznamenejte si název binárního souboru, protože se bude používat v pozdějších krocích.

8. Získejte GTID pozici (volitelné, nutné pro replikaci s GTID).

   Spusťte funkci [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) , abyste získali GTID pozici pro odpovídající název a posun souboru binlog.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>Výpis a obnovení zdrojového serveru

1. Vypíše všechny databáze ze zdrojového serveru.

   Pomocí mysqldump vypíšete všechny databáze ze zdrojového serveru. Není nutné vypsat knihovnu MySQL a knihovnu testů.

    Další informace najdete v tématu [Výpis a obnovení](howto-migrate-dump-restore.md).

2. Nastavte zdrojový server na režim pro čtení a zápis.

   Po dokončení databáze změňte zdrojový MariaDB Server zpátky na režim pro čtení a zápis.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Obnovte soubor s výpisem paměti na novém serveru.

   Obnovte soubor s výpisem paměti na server vytvořený ve službě Azure Database for MariaDB. Postup obnovení souboru s výpisem paměti na server MariaDB najdete v tématu [Výpis paměti & obnovení](howto-migrate-dump-restore.md) .

   Pokud je soubor s výpisem paměti velký, nahrajte ho do virtuálního počítače v Azure ve stejné oblasti jako server repliky. Obnovte ji na Azure Database for MariaDB Server z virtuálního počítače.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>Propojte zdrojový server a server repliky a spusťte Replikace vstupních dat

1. Nastavte zdrojový server.

   Všechny funkce Replikace vstupních dat jsou prováděny uloženými procedurami. Všechny postupy najdete v [replikace vstupních dat uložených procedurách](reference-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench.

   Pokud chcete propojit dva servery a spustit replikaci, přihlaste se k cílovému serveru repliky ve službě Azure DB pro MariaDB. V dalším kroku nastavte externí instanci jako zdrojový server pomocí `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` uložené procedury nebo na serveru Azure DB pro MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   nebo

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: název hostitele zdrojového serveru
   - master_user: uživatelské jméno pro zdrojový Server
   - master_password: heslo pro zdrojový Server
   - master_log_file: název souboru binárního protokolu se spustí. `show master status`
   - master_log_pos: binární umístění protokolu se spouští. `show master status`
   - master_gtid_pos: pozice GTID ze spuštění `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: kontext certifikátu certifikační autority. Pokud nepoužíváte protokol SSL, předejte prázdný řetězec. *


    * Doporučujeme předat parametr master_ssl_ca jako proměnnou. Další informace najdete v následujících příkladech.

   **Příklady**

   - Replikace s protokolem SSL

       Vytvořte proměnnou `@cert` spuštěním následujících příkazů:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikace s protokolem SSL je nastavená mezi zdrojovým serverem hostovaným v doméně companya.com a serverem repliky hostovaným v Azure Database for MariaDB. Tato uložená procedura se spouští na replice.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - Replikace bez SSL

       Replikace bez SSL je nastavená mezi zdrojovým serverem hostovaným v doméně companya.com a serverem repliky hostovaným v Azure Database for MariaDB. Tato uložená procedura se spouští na replice.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Spusťte replikaci.

   `mysql.az_replication_start`Pro spuštění replikace zavolejte uloženou proceduru.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Zkontroluje stav replikace.

   Voláním [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) příkazu na serveru repliky zobrazte stav replikace.

   ```sql
   show slave status;
   ```

   Pokud `Slave_IO_Running` a `Slave_SQL_Running` jsou ve stavu `yes` a hodnota `Seconds_Behind_Master` je `0` , replikace funguje. `Seconds_Behind_Master` Určuje, jak pozdě je replika. Pokud hodnota není `0` , replika zpracovává aktualizace.

4. Aktualizujte odpovídající proměnné serveru pro zajištění bezpečnější replikace dat (vyžaduje se jenom pro replikaci bez GTID).

    Z důvodu nativního omezení replikace v MariaDB je nutné nastavit  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) a [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) proměnné pro replikaci bez scénáře GTID.

    Zkontrolujte proměnné serveru repliky `sync_master_info` a ujistěte se `sync_relay_log_info` , že je replikace dat stabilní, a nastavte proměnné na `1` .

## <a name="other-stored-procedures"></a>Jiné uložené procedury

### <a name="stop-replication"></a>Zastavení replikace

Pokud chcete zastavit replikaci mezi zdrojovým serverem a serverem repliky, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Odebrat vztah replikace

Pokud chcete odebrat relaci mezi zdrojovým serverem a serverem repliky, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Přeskočit chybu replikace

Pokud chcete přeskočit chybu replikace a povolení replikace, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [replikace vstupních dat](concepts-data-in-replication.md) pro Azure Database for MariaDB.
