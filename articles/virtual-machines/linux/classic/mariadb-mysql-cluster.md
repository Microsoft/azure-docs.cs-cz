---
title: Spustit cluster MariaDB (MySQL) v Azure | Microsoft Docs
description: Vytvoření MariaDB + Galera MySQL clusteru na virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: sabbour
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 5438bfb75abaac2bed55a76b38f69790f7fc87fa
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>MariaDB (MySQL) clusteru: kurz pro Azure
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager](../../../resource-manager-deployment-model.md) a classic. Tento článek se týká modelu nasazení Classic. Společnost Microsoft doporučuje, aby většina nových nasazení používala model Azure Resource Manager.

> [!NOTE]
> MariaDB Enterprise clusteru je teď dostupná v Azure Marketplace. Nová nabídka automaticky nasadí cluster MariaDB Galera na Azure Resource Manager. Měli byste použít novou nabídku z [Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/).
>
>

V tomto článku se dozvíte, jak vytvořit více hlavní [Galera](http://galeracluster.com/products/) cluster [MariaDBs](https://mariadb.org/en/about/) (robustní, škálovatelnou a spolehlivé drop-in nahrazení pro databázi MySQL) pro práci v prostředí s vysokou dostupností na virtuálních počítačích Azure.

## <a name="architecture-overview"></a>Přehled architektury
Tento článek popisuje, jak provést následující kroky:

- Vytvořte tři uzly clusteru.
- Oddělení datových disků z disku operačního systému.
- Vytvoření datových disků RAID-0 nebo rozdělená nastavení zvýšit IOPS.
- Vyrovnávání zatížení Azure použijte k vyrovnávání zatížení pro tři uzly.
- Chcete-li minimalizovat opakovaných pracovní, vytvořte image virtuálního počítače, který obsahuje MariaDB + Galera a ji použít k vytvoření dalších clusteru virtuálních počítačů.

![Architektura systému](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> Toto téma používá [rozhraní příkazového řádku Azure](../../../cli-install-nodejs.md) nástroje, proto si je stáhnout a připojte je k předplatnému Azure podle pokynů. Pokud potřebujete odkaz s příkazy, které jsou k dispozici v Azure CLI, najdete v článku [reference k příkazům rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Budete také muset [vytvoření klíče SSH pro ověřování] a poznamenejte si umístění soubor .pem.
>
>

## <a name="create-the-template"></a>Vytvoření šablony
### <a name="infrastructure"></a>Infrastruktura
1. Vytvořte skupinu vztahů pro uložení prostředky společně.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. Vytvořte virtuální síť.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. Vytvořte účet úložiště pro hostování všech našich disků. Více než 40 vytíženou disky by neměly umístit na stejný účet úložiště, aby se zabránilo stiskne 20 000 limit účet úložiště IOPS. V takovém případě jste dobře nižší než toto omezení, takže budete všechno, co uložit na stejný účet pro jednoduchost.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. Najděte název bitové kopie virtuálního počítače CentOS 7.

        azure vm image list | findstr CentOS
   Výstup bude podobný `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Použijte tento název v dalším kroku.
5. Vytvořit šablonu virtuálního počítače a nahraďte /path/to/key.pem cestu, kde je uložený klíč SSH generovaného .pem.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. Připojte čtyři dat 500 GB disky na virtuální počítač pro použití v konfiguraci RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. Použití SSH se přihlásit k šabloně virtuálního počítače, který jste vytvořili na mariadbhatemplate.cloudapp.net:22 a připojit pomocí soukromého klíče.

### <a name="software"></a>Software
1. Získejte kořenový adresář.

        sudo su

2. Instalace podpory RAID:

    a. Nainstalujte mdadm.

              yum install mdadm

    b. Vytvořte konfiguraci 0/stripe EXT4 systémem souborů.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Vytvoření adresáře přípojného bodu.

              mkdir /mnt/data
    d. Získat identifikátor UUID nově vytvořený zařízení RAID.

              blkid | grep /dev/md0
    e. Upravte /etc/fstab.

              vi /etc/fstab
    f. Přidat zařízení povolit automatické připojování při restartování, nahraďte hodnotou získané z předchozí identifikátor UUID **blkid** příkaz.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Připojte nový oddíl.

              mount /mnt/data

3. Nainstalujte MariaDB.

    a. Vytvořte soubor MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Zadejte soubor úložiště s následujícím obsahem:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Aby nedocházelo ke konfliktům, odeberte existující operátory a mariadb knihovny.

           yum remove postfix mariadb-libs-*
    d. Nainstalujte MariaDB s Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

4. Přesuňte do adresáře dat MySQL do zařízení s blokovým RAID.

    a. Zkopírujte aktuální adresář MySQL do nového umístění a odebrat starý adresář.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Nastavte oprávnění pro nový adresář odpovídajícím způsobem.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Vytvořte symlink, který odkazuje adresáři původního do nového umístění v oddílu RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

5. Protože [SELinux naruší operací clusteru](http://galeracluster.com/documentation-webpages/configuration.html#selinux), je nutné zakázat pro aktuální relaci. Upravit `/etc/selinux/config` zakázat pro následné restartování.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. Ověření spuštění MySQL.

   a. Spusťte MySQL.

           service mysql start
   b. Zabezpečení instalace MySQL, nastavit kořenové heslo, odeberte anonymním uživatelům zakázat kořenové vzdálené přihlášení a odebrat databázi testu.

           mysql_secure_installation
   c. Vytvořte uživatele v databázi pro operace clusteru a volitelně pro vaše aplikace.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Zastavte MySQL.

            service mysql stop
7. Vytvoření konfigurace zástupný symbol.

   a. Upravte konfiguraci databáze MySQL vytvořit zástupný symbol pro nastavení clusteru. Nepřepisovat existující **`<Variables>`** nebo zrušte komentář u teď. Který se stane po vytvoření virtuálního počítače z této šablony.

            vi /etc/my.cnf.d/server.cnf
   b. Upravit **[galera]** části a vyčistit ho.

   c. Upravit **[mariadb]** části.

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. Otevřete požadované porty v bráně firewall pomocí FirewallD na CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Znovu načtete brány firewall: `firewall-cmd --reload`

9. Optimalizujte výkon systému. Další informace najdete v tématu [strategie ladění výkonu](optimize-mysql.md).

   a. Upravte konfigurační soubor MySQL znovu.

            vi /etc/my.cnf.d/server.cnf
   b. Upravit **[mariadb]** části a připojte následující obsah:

   > [!NOTE]
   > Doporučujeme, abyste tento innodb\_vyrovnávací paměti\_pool_size je 70 procent paměti Virtuálního počítače. V tomto příkladu ho je nastavená na 2.45 GB pro střední virtuální počítač Azure s 3.5 GB paměti RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. Zastavit MySQL, zakázat službu MySQL z spuštěna při spuštění, aby se zabránilo přerušení clusteru při přidávání uzlu a zrušení zřízení na počítač.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. Zachycení virtuálního počítače přes portál. (V současné době [vydání #1268 v nástrojích příkazového řádku Azure CLI](https://github.com/Azure/azure-xplat-cli/issues/1268) popisuje skutečnost, že obrazů z nástrojů příkazového řádku Azure není zachycen připojené datových disků.)

    a. Vypněte počítač prostřednictvím portálu.

    b. Klikněte na tlačítko **zaznamenat** a zadejte název bitové kopie jako **mariadb-galera image**. Zadejte popis a zkontrolujte "I jste spustili příkaz waagent."
      
      ![Zachytit virtuální počítač](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Vytvoření clusteru
Vytvořte tři virtuální počítače pomocí šablony vytvořili a potom nakonfigurovat a spustit clusteru.

1. Vytvořte první virtuální počítač CentOS 7 z bitové kopie mariadb galera bitové kopie, kterou jste vytvořili, poskytuje následující informace:

 - Název virtuální sítě: mariadbvnet
 - Podsítě: mariadb
 - Počítač velikost: střední
 - Název cloudové služby: mariadbha (nebo jakýkoli název, který chcete přistupovat prostřednictvím mariadbha.cloudapp.net)
 - Název počítače: mariadb1
 - Uživatelské jméno: azureuser
 - Přístup SSH: povoleno
 - Předávání soubor .pem certifikátu SSH a nahraďte /path/to/key.pem cestu, kde je uložený klíč SSH generovaného .pem.

   > [!NOTE]
   > Následující příkazy jsou rozděleny na více řádků pro přehlednost, ale měli zadejte každou na jeden řádek.
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. Připojte se ke cloudové službě mariadbha vytvořte dva další virtuální počítače. Změňte název virtuálního počítače a portu SSH na není v konfliktu se ostatní virtuální počítače v rámci stejné cloudové služby jedinečný port.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  Pro MariaDB3:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. Budete muset získat interní IP adresu každého ze tří virtuálních počítačů pro další krok:

    ![Získání IP adresy](./media/mariadb-mysql-cluster/IP.png)
4. Použití SSH k přihlášení na tři virtuální počítače a upravovat soubor konfigurace na každý z nich.

        sudo vi /etc/my.cnf.d/server.cnf

    Zrušením komentáře u **`wsrep_cluster_name`** a **`wsrep_cluster_address`** odebráním **#** na začátek řádku.
    Kromě toho nahradit **`<ServerIP>`** v **`wsrep_node_address`** a **`<NodeName>`** v **`wsrep_node_name`** s Virtuálního počítače IP adresou a name, a zrušte komentář u také tyto řádky.
5. Start clusteru na MariaDB1 a nechat ji spustit při spuštění.

        sudo service mysql bootstrap
        chkconfig mysql on
6. Spusťte MySQL na MariaDB2 a MariaDB3 a nechat ji spustit při spuštění.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Nástroj pro vyrovnávání zatížení clusteru
Při vytváření clusteru virtuálních počítačů, přidat je do skupiny dostupnosti názvem clusteravset zajistit, že jejich umístění v různých doménách selhání a aktualizace, a že Azure nikdy nemá údržby na všech počítačích najednou. Tato konfigurace splňuje požadavky na podporu Azure smlouvu o úrovni služeb (SLA).

Teď použijte nástroj pro vyrovnávání zatížení Azure k vyrovnávání požadavků mezi tři uzly.

Spusťte následující příkazy v počítači pomocí rozhraní příkazového řádku Azure.

Struktura parametry příkazu je: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Rozhraní příkazového řádku nastaví interval testu nástroje pro vyrovnávání zatížení na 15 sekund, což může být trochu příliš dlouhý. Změnit portálu v části **koncové body** pro všechny virtuální počítače.

![Upravit koncový bod](./media/mariadb-mysql-cluster/Endpoint.PNG)

Vyberte **překonfigurovat sady vyrovnáváním zatížení**.

![Znovu nakonfigurujte sadu s vyrovnáváním zatížení](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Změna **Interval sběru dat** na 5 sekund a uložte změny.

![Interval kontroly změn](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Ověření clusteru
Provádí náročné práce. Cluster musí být nyní přístupná na `mariadbha.cloudapp.net:3306`, které efektivně a bez obtíží dotkne zatížení požadavky vyrovnávání a směrování mezi tři virtuální počítače.

Vaše oblíbené klient MySQL použijte připojení nebo připojení z jednoho z virtuálních počítačů k ověření, že je tento cluster funguje.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Pak vytvořte databázi a naplnit určitými daty.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Vrátí databázi, kterou jste vytvořili v následující tabulce:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili tři uzly MariaDB + Galera vysoce dostupný cluster v Azure virtuální počítače spuštěné CentOS 7. Virtuální počítače jsou zatížení vyrovnávaném pomocí vyrovnávání zatížení Azure.

Můžete se podívat na [jiný způsob, jak cluster MySQL v systému Linux](mysql-cluster.md) a způsoby, jak [optimalizace a testování výkonu databáze MySQL na virtuálních počítačích Azure Linux](optimize-mysql.md).

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[vytvoření klíče SSH pro ověřování]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
