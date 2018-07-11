---
title: Nastavení MySQL na virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat MySQL stack na virtuální počítač s Linuxem (Ubuntu a Red Hat řada operačního systému) v Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: c8043064ac1df40eaa31ae56e9ec31c0152e0130
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934259"
---
# <a name="how-to-install-mysql-on-azure"></a>Jak nainstalovat MySQL v Azure
V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat MySQL na virtuálním počítači Azure s Linuxem.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Instalace MySQL na virtuálním počítači
> [!NOTE]
> Musíte již mít na virtuálním počítači Microsoft Azure s Linuxem k dokončení tohoto kurzu. Podrobnosti najdete [Linuxový virtuální počítač Azure kurz](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) k vytvoření a nastavení virtuálního počítače s Linuxem pomocí `mysqlnode` jako název virtuálního počítače a `azureuser` jako uživatel, než budete pokračovat.
> 
> 

V takovém případě použijte 3306 port jako port pro MySQL.  

Připojte se k Linuxový virtuální počítač vytvořený pomocí putty. Pokud je při prvním použití Linuxový virtuální počítač Azure, najdete informace o použití klienta putty připojení k virtuálnímu počítači s Linuxem [tady](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

K instalaci MySQL5.6 jako příklad v tomto článku budeme používat úložiště balíčků. Ve skutečnosti MySQL5.6 má další vylepšení výkonu než MySQL5.5.  Další informace o [tady](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Postup instalace MySQL5.6 na Ubuntu
Virtuální počítač s Linuxem s Ubuntu v Azure použijeme tady.

* Krok 1: Instalace MySQL serveru 5.6 přepnout na `root` uživatele:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Nainstalujte mysql server 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Během instalace zobrazí se dialogového okna okno poping až ask vám umožní nastavit kořenového hesla MySQL níže a budete potřebovat nastavit heslo tady.
  
    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Zadejte heslo znovu pro potvrzení.

    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Krok 2: Přihlášení na Server MySQL
  
    Po dokončení instalace serveru MySQL se spustí automaticky službu MySQL. MySQL Server se můžete přihlásit `root` uživatele.
    Používá následující příkaz k přihlášení a zadání hesla.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Krok 3: Správa spuštěnou službu MySQL
  
    (a) získat stav služby MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) spustit službu MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) zastavit službu MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) restartujte službu MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Jak nainstalovat MySQL v řadě Red Hat OS jako CentOS, Oracle Linux
Virtuální počítač s Linuxem s CentOS nebo Oracle Linux použijeme tady.

* Krok 1: Přidejte úložiště MySQL Yumu přepínač na `root` uživatele:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Stáhněte a nainstalujte balíček verze MySQL:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Krok 2: Upravte následující soubor úložiště MySQL pro stahování balíčku MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Aktualizujte jednotlivé hodnoty tohoto souboru níže:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Krok 3: Instalace MySQL z MySQL – úložiště nainstalovat MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Balíček MySQL RPM a všechny související balíčky se nainstalují.
* Krok 4: Správa spuštěnou službu MySQL
  
    (a) zkontrolujte stav služby serveru MySQL:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) zkontrolujte, zda je spuštěn výchozí port MySQL server:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) spusťte MySQL server:

           #[root@mysqlnode ~]#service mysqld start

    (d) zastavte MySQL server:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL sada chcete spustit po spuštění provozu systému:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Jak nainstalovat MySQL v SUSE Linuxu
Virtuální počítač s Linuxem pomocí OpenSUSE použijeme tady.

* Krok 1: Stáhněte a nainstalujte MySQL Server
  
    Přepnout na `root` uživatele provede následující příkaz:  
  
           #sudo su -
  
    Stáhněte a nainstalujte MySQL balíčku:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Krok 2: Správa spuštěnou službu MySQL
  
    (a) zkontrolujte stav serveru MySQL:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) zkontrolujte, zda výchozí port serveru MySQL:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) spusťte MySQL server:

           #[root@mysqlnode ~]# rcmysql start

    (d) zastavte MySQL server:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL sada chcete spustit po spuštění provozu systému:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Dalším krokem
Najít další využití a informace týkající se MySQL [tady](https://www.mysql.com/).

