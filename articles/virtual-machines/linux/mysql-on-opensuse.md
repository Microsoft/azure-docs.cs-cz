---
title: Instalace MySQL na virtuálním počítači s OpenSUSE v Azure | Microsoft Docs
description: Naučte se instalovat MySQL na virtuální počítač s OpenSUSE Linux v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 223a5a5b77ded9eb26a5cf3f0003bc9d393615db
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300854"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalace MySQL do virtuálního počítače se spuštěným OpenSUSE Linuxem v Azure

[MySQL](https://www.mysql.com) je oblíbená otevřená zdrojová databáze SQL. V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s operačním systémem OpenSUSE Linux, a pak nainstalujte MySQL.


Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Vytvoření virtuálního počítače se systémem OpenSUSE Linux

Nejdřív vytvořte skupinu prostředků. V tomto příkladu má skupina prostředků název *mySQSUSEResourceGroup* a je vytvořená v oblasti *východní USA* .

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Vytvořte virtuální počítač. V tomto příkladu je virtuální počítač pojmenovaný *myVM* a velikost virtuálního počítače je *Standard_D2s_v3*, ale měli byste si vybrat [Velikost virtuálního počítače](sizes.md) , kterou považujete za nejvhodnější pro vaše zatížení.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Také je nutné přidat do skupiny zabezpečení sítě pravidlo pro povolení provozu přes port 3306 pro MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

K připojení k virtuálnímu počítači budete používat SSH. V tomto příkladu je veřejná IP adresa virtuálního počítače *10.111.112.113*. Po vytvoření virtuálního počítače můžete zobrazit IP adresu ve výstupu.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aktualizace virtuálního počítače
 
Až budete připojeni k virtuálnímu počítači, nainstalujte aktualizace systému a opravy. 
   
```bash
sudo zypper update
```

Podle pokynů aktualizujte virtuální počítač.

## <a name="install-mysql"></a>Instalace MySQL 


Nainstalujte si MySQL do virtuálního počítače přes SSH. Podle potřeby odpovězte na výzvy.

```bash
sudo zypper install mysql
```
 
Nastavte MySQL tak, aby se spouštěl při spuštění systému. 

```bash
sudo systemctl enable mysql
```
Ověřte, jestli je MySQL povolený.

```bash
systemctl is-enabled mysql
```

Mělo by se vrátit: Enabled.

Restartujte server.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Heslo MySQL

Po instalaci je kořenové heslo MySQL ve výchozím nastavení prázdné. Pro zabezpečení MySQL spusťte skript **MySQL @ no__t-1secure @ no__t-2installation** . Skript vás vyzve ke změně kořenového hesla MySQL, odebrání anonymních uživatelských účtů, zakázání vzdáleného kořenového přihlášení, odebrání testovacích databází a načtení tabulky oprávnění. 

Po restartování serveru se znovu připojte k virtuálnímu počítači přes SSH.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Přihlásit se k MySQL

Nyní se můžete přihlásit a zadat dotaz MySQL.

```bash  
mysql -u root -p
```
Tím se přepnete na výzvu MySQL, kde můžete vystavit příkazy SQL pro interakci s databází.

Nyní vytvořte nového uživatele MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Střední dvojtečka (;) na konci řádku je rozhodující pro ukončení příkazu.


## <a name="create-a-database"></a>Vytvoření databáze


Vytvořte databázi a udělte uživatelům oprávnění `mysqluser`.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Uživatelská jména a hesla databáze jsou používány pouze skripty připojujícími se k databázi.  Názvy uživatelských účtů databáze nutně nepředstavuje skutečné uživatelské účty v systému.

Povolit přihlášení z jiného počítače. V tomto příkladu se IP adresa počítače, ze kterého se povoluje přihlášení, *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Chcete-li ukončit nástroj pro správu databáze MySQL, zadejte:

```    
quit
```


## <a name="next-steps"></a>Další kroky
Podrobnosti o MySQL najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc).




