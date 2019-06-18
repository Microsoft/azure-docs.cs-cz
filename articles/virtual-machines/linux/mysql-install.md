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
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158442"
---
# <a name="how-to-install-mysql-on-azure"></a>Jak nainstalovat MySQL v Azure
V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat MySQL na virtuálním počítači Azure s Linuxem.


> [!NOTE]
> Musíte již mít na virtuálním počítači Microsoft Azure s Linuxem k dokončení tohoto kurzu. Podrobnosti najdete [Linuxový virtuální počítač Azure kurz](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) k vytvoření a nastavení virtuálního počítače s Linuxem pomocí `mysqlnode` jako název virtuálního počítače a `azureuser` jako uživatel, než budete pokračovat.
> 
> 

V takovém případě použijte 3306 port jako port pro MySQL.  

K instalaci MySQL5.6 jako příklad v tomto článku budeme používat úložiště balíčků. Ve skutečnosti MySQL5.6 má další vylepšení výkonu než MySQL5.5.  Další informace o [tady](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Nainstalujte MySQL5.6 na Ubuntu
Budeme používat systém Linux virtuálního počítače s Ubuntu.


### <a name="install-mysql"></a>Instalace MySQL

Nainstalovat MySQL 5.6 serveru při přechodu `root` uživatele:

```bash  
sudo su -
```

Nainstalujte mysql server 5.6:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
Během instalace zobrazí se dialogové okno se zobrazí na vás vyzve k nastavení MySQL kořenové heslo a zde potřebovat nastavit heslo.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Zadejte heslo znovu pro potvrzení.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Přihlášení
  
Po dokončení instalace serveru MySQL se spustí automaticky službu MySQL. Můžete se přihlásit k serveru MySQL se `root` uživatele a zadejte své heslo.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>Správa služby MySQL

Získání stavu služby MySQL

```bash   
service mysql status
```
  
Start MySQL Service

```bash  
service mysql start
```
  
Zastavit službu MySQL

```bash  
service mysql stop
```
  
Restartujte službu MySQL

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Install MySQL on Red Hat OS, CentOS, Oracle Linux
Virtuální počítač s Linuxem s CentOS nebo Oracle Linux použijeme tady.

### <a name="add-the-mysql-yum-repository"></a>Přidejte úložiště MySQL yum
    
Přepnout na `root` uživatele:

```bash  
sudo su -
```

Stáhněte a nainstalujte balíček verze MySQL:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>Povolit úložiště MySQL
Upravte následující soubor úložiště MySQL pro stahování balíčku MySQL5.6.

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Aktualizujte jednotlivé hodnoty tohoto souboru níže:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>Instalace MySQL 

Instalace MySQL z úložiště.

```bash  
yum install mysql-community-server
```
  
Balíček MySQL RPM a všechny související balíčky se nainstalují.


## <a name="manage-the-mysql-service"></a>Správa služby MySQL
  
Zkontrolujte stav služby serveru MySQL:

```bash  
service mysqld status\
```
  
Zkontrolujte, zda je spuštěn výchozí port MySQL server:

```bash  
netstat  –tunlp|grep 3306
```

Spusťte MySQL server:

```bash
service mysqld start
```

Zastavte MySQL server:

```bash
service mysqld stop
```

Nastavení MySQL chcete spustit po spuštění provozu systému:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>Instalace MySQL v SUSE Linuxu

Virtuální počítač s Linuxem pomocí OpenSUSE použijeme tady.

### <a name="download-and-install-mysql-server"></a>Stáhněte a nainstalujte MySQL Server
  
Přepnout na `root` uživatele provede následující příkaz:  

```bash  
sudo su -
```
  
Stáhněte a nainstalujte MySQL balíčku:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>Správa služby MySQL
  
Zkontrolujte stav serveru MySQL:

```bash  
rcmysql status
```
  
Zkontrolujte, zda výchozí port serveru MySQL:

```bash  
netstat  –tunlp|grep 3306
```

Spusťte MySQL server:

```bash
rcmysql start
```

Zastavte MySQL server:

```bash
rcmysql stop
```

Nastavení MySQL chcete spustit po spuštění provozu systému:

```bash
insserv mysql
```

## <a name="next-step"></a>Další krok
Další informace najdete v tématu [MySQL](https://www.mysql.com/) webu.

