---
title: Nastavení databáze MySQL na virtuální počítač s Linuxem v Azure | Microsoft Docs
description: Informace o instalaci zásobníku MySQL na virtuální počítač s Linuxem (Ubuntu nebo RedHat rodiny operačního systému) v Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: iainfou
ms.openlocfilehash: d91f8cf8455a60d3e0afb2f209ba07933bcdee1c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-install-mysql-on-azure"></a>Jak nainstalovat MySQL v Azure
V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat MySQL na virtuální počítač Azure s Linuxem.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Nainstalujte MySQL na virtuálním počítači
> [!NOTE]
> Musíte už mít virtuálního počítače Microsoft Azure se systémem Linux k dokončení tohoto kurzu. Najdete v tématu [kurzu virtuální počítač Azure s Linuxem](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) k vytvoření a nastavení virtuálního počítače s Linuxem pomocí `mysqlnode` jako název virtuálního počítače a `azureuser` jako uživatel, než budete pokračovat.
> 
> 

V takovém případě 3306 port použijte jako MySQL port.  

Připojte k systému Linux vytvořené prostřednictvím putty virtuálních počítačů. Pokud je při prvním použití virtuálního počítače s Linuxem Azure, naleznete v části použití klienta putty připojit k virtuální počítač s Linuxem [zde](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

K instalaci MySQL5.6 jako příklad v tomto článku budeme používat úložiště balíčku. Ve skutečnosti MySQL5.6 má další zlepšování výkonu než MySQL5.5.  Další informace [zde](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Postup instalace MySQL5.6 v Ubuntu
Virtuální počítač s Linuxem s Ubuntu z Azure použijeme sem.

* Krok 1: Instalace MySQL serveru 5.6 přepínač tak, aby `root` uživatele:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Mysql-server 5.6 nainstalujte:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Během instalace zobrazí se dialogové okno okno poping než zeptejte se, že vám umožní nastavit MySQL kořenové heslo níže a můžete potřebovat nastavit heslo sem.
  
    ![Bitové kopie](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Zadejte heslo znovu pro potvrzení.

    ![Bitové kopie](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Krok 2: Server MySQL přihlášení
  
    Po dokončení instalace serveru MySQL, spustí se automaticky službu MySQL. Můžete se přihlásit MySQL Server s `root` uživatele.
    Použití níže příkaz heslo k přihlášení a vstup.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Krok 3: Spravujte spuštěnou službu MySQL
  
    (a) získat stav službu MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) spustit službu MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) zastavit službu MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) restartujte službu MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Postup instalace MySQL Red Hat operačních systémů jako CentOS, Oracle Linux
Virtuální počítač s Linuxem pomocí CentOS nebo Oracle Linux použijeme sem.

* Krok 1: Přidání úložiště MySQL Yum přepínače k `root` uživatele:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Stáhněte a nainstalujte balíček MySQL verze:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Krok 2: Upravte následující soubor povolit úložiště MySQL pro stažení balíčku MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Aktualizujte každý hodnotu tohoto souboru do níže:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Krok 3: Instalace MySQL z úložiště MySQL MySQL nainstalovat:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Balíček MySQL RPM a všechny související balíčky budou nainstalovány.
* Krok 4: Správa spuštěnou službu MySQL
  
    (a) zkontrolujte stav služby MySQL serveru:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) zkontrolujte, zda je spuštěn výchozí port MySQL server:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) spusťte server, MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) zastavení MySQL serveru:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL sady při spuštění systému spouštěcí up:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Postup instalace databáze MySQL na SUSE Linux
Virtuální počítač s Linuxem pomocí OpenSUSE použijeme sem.

* Krok 1: Stáhněte a nainstalujte MySQL Server
  
    Přepnout na `root` uživatele prostřednictvím následující příkaz:  
  
           #sudo su -
  
    Stáhněte a nainstalujte balíček MySQL:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Krok 2: Správa spuštěnou službu MySQL
  
    (a) zkontrolujte stav serveru MySQL:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) zkontrolujte, zda výchozí port serveru MySQL:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) spusťte server, MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) zastavení MySQL serveru:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL sady při spuštění systému spouštěcí up:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Další krok
Najít další využití a informace týkající se MySQL [zde](https://www.mysql.com/).

