---
title: Cluster MariaDB (MySQL) spuštění v Azure | Dokumentace Microsoftu
description: Vytvoření MariaDB + clusteru Galera MySQL na virtuálních počítačích Azure
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
ms.openlocfilehash: 2cdc58a827f696d62e6240b90202ee04ce371d07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426849"
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>Cluster MariaDB (MySQL): kurz pro Azure
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manageru](../../../resource-manager-deployment-model.md) a classic. Tento článek se týká modelu nasazení Classic. Společnost Microsoft doporučuje, aby většina nových nasazení používala model Azure Resource Manageru.

> [!NOTE]
> MariaDB Enterprise cluster je nyní k dispozici na webu Azure Marketplace. Nová nabídka automaticky nasadí cluster MariaDB Galera na Azure Resource Manageru. Používejte nové nabídky z [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/).
>
>

V tomto článku se dozvíte, jak vytvořit více hlavních databází [Galera](http://galeracluster.com/products/) cluster [MariaDBs](https://mariadb.org/en/about/) (robustní, škálovatelné a spolehlivé což je náhrada MySQL) v prostředí s vysokou dostupností v Azure virtuální počítače.

## <a name="architecture-overview"></a>Přehled architektury
Tento článek popisuje, jak provést následující kroky:

- Vytvoření clusteru se třemi uzly.
- Oddělení datových disků z disku s operačním systémem.
- Vytvoření datové disky v nastavení diskového pole RAID-0/prokládané zvýšit vstupně-výstupních operací.
- Azure Load Balancer použijte k vyrovnávání zatížení pro tři uzly.
- Chcete-li minimalizovat opakující práce, vytvořit image virtuálního počítače, který obsahuje MariaDB + Galera a použijte ji k vytvoření další virtuální počítače clusteru.

![Architektura systému](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> Toto téma používá [rozhraní příkazového řádku Azure](../../../cli-install-nodejs.md) nástroje, takže nezapomeňte si je stáhnout a připojit je ke svému předplatnému Azure podle pokynů. Pokud potřebujete odkaz na příkazů dostupných v Azure CLI, přečtěte si [reference k příkazům rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Budete také muset [vytvoření klíče SSH pro ověřování] a poznamenejte si umístění soubor .pem.
>
>

## <a name="create-the-template"></a>Vytvoření šablony
### <a name="infrastructure"></a>Infrastruktura
1. Vytvořte skupinu vztahů pro uložení prostředků najednou.

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
1. Vytvořte virtuální síť.

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
1. Vytvoření účtu úložiště pro hostování všech našich disků. Více než 40 vytížený disky by neměly umístit na stejný účet úložiště, aby se zabránilo dosažení 20 000 IOPS limit účtu úložiště. V takovém případě budete také nižší než toto omezení všechno, co budete uložit na stejný účet pro zjednodušení.

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
1. Vyhledejte název image virtuálního počítače CentOS 7.

        azure vm image list | findstr CentOS
   Výstup bude vypadat přibližně `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`.

   Tento název použijte v následujícím kroku.
1. Vytvořit šablonu virtuálního počítače a /path/to/key.pem nahraďte cestou k uložení klíče SSH generované .pem.

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
1. Čtyři 500 GB datové disky připojte k virtuálnímu počítači pro použití v konfiguraci RAID.

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
1. Použití SSH se přihlásit do šablony virtuálního počítače, který jste vytvořili na mariadbhatemplate.cloudapp.net:22 a připojit pomocí privátní klíč.

### <a name="software"></a>Software
1. Získejte kořenový adresář.

        sudo su

1. Nainstalujte podporu RAID:

    a. Nainstalujte mdadm.

              yum install mdadm

    b. Vytvořte konfiguraci 0/stripe EXT4 systémem souborů.

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. Vytvoření adresáře přípojného bodu.

              mkdir /mnt/data
    d. Získat identifikátor UUID vytvořeného zařízení RAID.

              blkid | grep /dev/md0
    e. Upravte /etc/fstab.

              vi /etc/fstab
    f. Přidat zařízení do povolte automatické připojení na restartování, nahradíte hodnotě získané z předchozí identifikátor UUID **blkid** příkazu.

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. Připojte nový oddíl.

              mount /mnt/data

1. Nainstalujte MariaDB.

    a. Vytvořte soubor MariaDB.repo.

                vi /etc/yum.repos.d/MariaDB.repo

    b. Zadejte soubor úložiště s následujícím obsahem:

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. Aby nedocházelo ke konfliktům, odeberte existující přípony a mariadb knihovny.

           yum remove postfix mariadb-libs-*
    d. Nainstalujte MariaDB s Galera.

           yum install MariaDB-Galera-server MariaDB-client galera

1. Zařízení s blokovým RAID přesune do adresáře dat MySQL.

    a. Zkopírujte aktuální adresář MySQL do nového umístění a odebrat původní adresář.

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. Odpovídajícím způsobem nastavte oprávnění pro nový adresář.

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. Vytvořte symlink, který ukazuje původní adresář do nového umístění v oddílu RAID.

           ln -s /mnt/data/mysql /var/lib/mysql

1. Protože [SELinux dochází ke kolizím s operací clusteru](http://galeracluster.com/documentation-webpages/configuration.html#selinux), je nutné zakázat pro aktuální relaci. Upravit `/etc/selinux/config` zakázat pro následné restartování.

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
1. Ověřte spuštění MySQL.

   a. Spusťte MySQL.

           service mysql start
   b. Zabezpečením instalace MySQL, nastavte kořenové heslo, odeberte anonymním uživatelům zakázat vzdálený kořen přihlášení a odeberte testovací databázi.

           mysql_secure_installation
   c. Vytvořte uživatele databáze pro operace clusteru a volitelně pro vaše aplikace.

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. Zastavte MySQL.

            service mysql stop
1. Vytvoření konfigurace zástupný symbol.

   a. Upravte konfiguraci MySQL, abyste mohli vytvořit zástupný symbol pro nastavení clusteru. Nenahrazují **`<Variables>`** nebo zrušte komentář u nyní. Který se stane po vytvoření virtuálního počítače z této šablony.

            vi /etc/my.cnf.d/server.cnf
   b. Upravit **[galera]** části a smažte ho.

   c. Upravit **[mariadb]** oddílu.

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
1. Požadované porty v bráně firewall otevřít pomocí FirewallD na CentOS 7.

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA TIS: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * PŘÍKAZ RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * Znovu načte brány firewall: `firewall-cmd --reload`

1. Optimalizaci výkonu. Další informace najdete v tématu [strategie pro optimalizaci výkonu](optimize-mysql.md).

   a. Upravte konfigurační soubor MySQL znovu.

            vi /etc/my.cnf.d/server.cnf
   b. Upravit **[mariadb]** části a připojte následujícím obsahem:

   > [!NOTE]
   > Doporučujeme tuto innodb\_vyrovnávací paměti\_pool_size je 70 procent společností z žebříčku paměti Virtuálního počítače. V tomto příkladu to je nastavená na 2.45 GB pro médium virtuálního počítače Azure s 3,5 GB paměti RAM.
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
1. Zastavte MySQL, zakažte službu MySQL ve spuštění při spuštění, aby nedošlo k narušení clusteru při přidávání uzlu a zrušení zřízení počítače.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
1. Zachycení virtuálního počítače na portálu. (V současné době [vydat #1268 v nástrojích příkazového řádku Azure](https://github.com/Azure/azure-xplat-cli/issues/1268) popisuje fakt, že Image nezachytává nástroje Azure CLI nezachytí připojenými datovými disky.)

    a. Vypněte počítač prostřednictvím portálu.

    b. Klikněte na tlačítko **zachycení** a zadejte název bitové kopie jako **bitové kopie mariadb galera**. Zadejte popis a zkontrolujte "Spustil(a) jsem waagent."
      
      ![Zachytit virtuální počítač](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>Vytvoření clusteru
Vytvořte tři virtuální počítače pomocí šablony vytvoří a potom nakonfigurujte a spusťte clusteru.

1. Vytvoření prvního virtuálního počítače CentOS 7 z bitové kopie mariadb galera image, kterou jste vytvořili, zadejte následující informace:

 - Název virtuální sítě: mariadbvnet
 - Podsíť: mariadb
 - Počítač velikost: střední
 - Název cloudové služby: mariadbha (nebo jakýkoli název, který chcete získat přístup prostřednictvím mariadbha.cloudapp.net)
 - Název počítače: mariadb1
 - Uživatelské jméno: azureuser
 - Přístup přes SSH: povoleno
 - Předávání SSH soubor .pem certifikátu a nahraďte /path/to/key.pem cestu, kde je uložený klíč SSH generované .pem.

   > [!NOTE]
   > Následující příkazy jsou rozdělit přes více řádků pro přehlednost, ale měli byste zadat každou na jeden řádek.
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
1. Připojení ke cloudové službě mariadbha vytvořte dva další virtuální počítače. Změňte název virtuálního počítače a SSH port na není v konfliktu s jiným virtuálním počítačům ve stejné cloudové službě jedinečný port.

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
1. Budete potřebovat k získání interní IP adresy každé tři virtuální počítače na další krok:

    ![Získání IP adresy](./media/mariadb-mysql-cluster/IP.png)
1. Použití SSH se přihlásit na třech virtuálních počítačích a upravte konfigurační soubor na každém z nich.

        sudo vi /etc/my.cnf.d/server.cnf

    Zrušením komentáře u **`wsrep_cluster_name`** a **`wsrep_cluster_address`** odebráním **#** na začátku řádku.
    Kromě toho nahradit **`<ServerIP>`** v **`wsrep_node_address`** a **`<NodeName>`** v **`wsrep_node_name`** s Virtuálního počítače IP adresu a název, a zrušte komentář také tyto řádky.
1. Start clusteru na MariaDB1 a nechat běžet při spuštění.

        sudo service mysql bootstrap
        chkconfig mysql on
1. Spusťte MySQL na MariaDB2 a MariaDB3 a nechat běžet při spuštění.

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>Nástroj pro vyrovnávání zatížení clusteru
Když vytvoříte Clusterované virtuální počítače, jste je přidali do skupiny dostupnosti volá clusteravset zajistěte, aby byly umístit na různé doménami selhání a aktualizačními doménami a tento Azure nikdy udělá údržby na všechny počítače najednou. Tato konfigurace splňuje požadavky na to, že Azure smlouvu o úrovni služeb (SLA).

Teď použijte nástroj pro vyrovnávání zatížení Azure k vyrovnávání požadavků mezi třemi uzly.

Spusťte následující příkazy na svém počítači pomocí rozhraní příkazového řádku Azure.

Struktura parametry příkazu je: `azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Rozhraní příkazového řádku nastaví interval test paměti nástroje pro vyrovnávání zatížení do 15 sekund, což může být trochu příliš dlouhý. Změnit na portálu v části **koncové body** pro všechny virtuální počítače.

![Upravit koncový bod](./media/mariadb-mysql-cluster/Endpoint.PNG)

Vyberte **znovu nakonfigurovat sady s vyrovnáváním zatížení**.

![Změna konfigurace sady s vyrovnáváním zatížení](./media/mariadb-mysql-cluster/Endpoint2.PNG)

Změna **Interval sběru dat** na 5 sekund a uložte provedené změny.

![Interval testu změny](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>Ověření clusteru
Těžkou práci provádí. Cluster by měl být nyní přístupný na adrese `mariadbha.cloudapp.net:3306`, které hladce a efektivně narazí zatížení požadavků nástroje pro vyrovnávání a tras mezi tři virtuální počítače.

Pomocí svého oblíbeného klienta MySQL připojte, nebo se připojit z jednoho z virtuálních počítačů určených k ověření, že je tento cluster funguje.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Vytvořte databázi a přidejte do ní nějaká data.

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Vrací databáze, kterou jste vytvořili v následující tabulce:

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili MariaDB třemi uzly + Galera clusteru s vysokou dostupností v Azure virtuální počítače spuštěné CentOS 7. Virtuální počítače jsou zatížení vyrovnávaném pomocí vyrovnávání zatížení Azure.

Můžete chtít podívat na [další způsob, jak cluster MySQL v Linuxu](mysql-cluster.md) a způsoby, jak [optimalizovat a testování výkonu MySQL na virtuálních počítačích Azure Linux](optimize-mysql.md).

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
[Vytvoření klíče SSH pro ověřování]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
