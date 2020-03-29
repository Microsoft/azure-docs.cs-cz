---
title: Instalace MySQL na virtuální počítač OpenSUSE v Azure
description: Naučte se instalovat MySQL na virtuální mašitu OpenSUSE Linux v Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 0d3f0a61da3654c31c99cfac43c86b081876f700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944578"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalace MySQL na virtuální stroj s OpenSUSE Linuxem v Azure

[MySQL](https://www.mysql.com) je populární, open-source SQL databáze. Tento výukový program vám ukáže, jak vytvořit virtuální stroj se systémem OpenSUSE Linux, pak nainstalovat MySQL.


Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Vytvoření virtuálního počítače s OpenSUSE Linuxem

Nejprve vytvořte skupinu prostředků. V tomto příkladu je skupina prostředků s názvem *mySQSUSEResourceGroup* a je vytvořena v oblasti *USA – východ.*

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Vytvořte virtuální hod. V tomto příkladu virtuální ho s názvem *myVM* a velikost virtuálního počítače je *Standard_D2s_v3*, ale měli byste zvolit [velikost virtuálního počítače](sizes.md) si myslíte, že je nejvhodnější pro vaše úlohy.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Musíte také přidat pravidlo do skupiny zabezpečení sítě povolit provoz přes port 3306 pro MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Budete používat SSH pro připojení k virtuálnímu provozu. V tomto příkladu je veřejná IP adresa virtuálního počítačů *10.111.112.113*. Při vytváření virtuálního počítačů se ve výstupu zobrazí ip adresa.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aktualizace virtuálního virtuálního mísy
 
Po připojení k virtuálnímu virtuálnímu zařízení nainstalujte aktualizace systému a opravy. 
   
```bash
sudo zypper update
```

Podle pokynů aktualizujte virtuální počítač.

## <a name="install-mysql"></a>Instalace MySQL 


Nainstalujte MySQL ve Virtuálním mísu přes SSH. Podle potřeby odpovídejte na výzvy.

```bash
sudo zypper install mysql
```
 
Nastavte MySQL spustit, když se systém spustí. 

```bash
sudo systemctl enable mysql
```
Ověřte, zda je mysql povoleno.

```bash
systemctl is-enabled mysql
```

To by se mělo vrátit: povoleno.

Restartujte server.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL heslo

Po instalaci je kořenové heslo MySQL ve výchozím nastavení prázdné. Spusťte **mysql\_bezpečné\_instalační** skript pro zabezpečení MySQL. Skript vás vyzve ke změně hesla ke kořenovému adresáři MySQL, odebrání anonymních uživatelských účtů, zakázání vzdáleného přihlášení kořenového adresáře, odebrání testovacích databází a opětovnénačtení tabulky oprávnění. 

Po restartování serveru znovu ssh na virtuální počítač.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Přihlaste se k MySQL

Nyní se můžete přihlásit a zadat výzvu MySQL.

```bash  
mysql -u root -p
```
Tím se přepnete na výzvu MySQL, kde můžete vydávat příkazy SQL pro interakci s databází.

Nyní vytvořte nového uživatele MySQL.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Středník (;) na konci řádku je rozhodující pro ukončení příkazu.


## <a name="create-a-database"></a>Vytvoření databáze


Vytvořte databázi `mysqluser` a udělte uživateli oprávnění.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Uživatelská jména a hesla databáze jsou používány pouze skripty, které se připojují k databázi.  Názvy uživatelských účtů databáze nemusí nutně představovat skutečné uživatelské účty v systému.

Povolte přihlášení z jiného počítače. V tomto příkladu je ip adresa počítače povolit přihlášení z *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Chcete-li ukončit nástroj pro správu databáze MySQL, zadejte:

```    
quit
```


## <a name="next-steps"></a>Další kroky
Podrobnosti o MySQL naleznete v [dokumentaci MySQL](https://dev.mysql.com/doc).




