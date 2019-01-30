---
title: Instalace MySQL na virtuálním počítači OpenSUSE v Azure | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat MySQL na počítač OpenSUSE Linux VMirtual v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: za-rhoads
ms.openlocfilehash: 98eb331fbd82ff718b01c99afd6840e3c2252777
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227848"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalace MySQL do virtuálního počítače se spuštěným OpenSUSE Linuxem v Azure

[MySQL](http://www.mysql.com) je Oblíbené, open source databáze SQL. V tomto kurzu se dozvíte, jak k vytvoření virtuálního počítače se systémem OpenSUSE Linux, potom nainstalovat MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Vytvoření virtuálního počítače se systémem OpenSUSE Linux

Nejprve vytvořte skupinu prostředků. V tomto příkladu je skupina prostředků s názvem *mySQSUSEResourceGroup* a vytvoří se v *USA – východ* oblasti.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Vytvoření virtuálního počítače. V tomto příkladu je virtuální počítač s názvem *myVM* a velikost virtuálního počítače je *Standard_D2s_v3*, ale měli byste zvolit [velikost virtuálního počítače](sizes.md) si myslíte, že je nejvhodnější pro vaši úlohu.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Také je potřeba přidat pravidlo skupiny zabezpečení sítě umožňující provoz přes port 3306 for MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

SSH budete používat pro připojení k virtuálnímu počítači. V tomto příkladu se veřejná IP adresa virtuálního počítače je *10.111.112.113*. Při vytváření virtuálního počítače, můžete zobrazit IP adresa ve výstupu.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aktualizujte virtuální počítač
 
Po připojení k virtuálnímu počítači, nainstalujte opravy a aktualizace systému. 
   
```bash
sudo zypper update
```

Postupujte podle pokynů k aktualizaci vašeho virtuálního počítače.

## <a name="install-mysql"></a>Instalace MySQL 


Instalace MySQL na virtuálním počítači přes SSH. Odpověď na výzvu podle potřeby.

```bash
sudo zypper install mysql
```
 
Nastavení MySQL, která se spustí při spuštění systému. 

```bash
sudo systemctl enable mysql
```
Ověřte, zda je povoleno MySQL.

```bash
systemctl is-enabled mysql
```

Měla by se vrátit: povoleno.

Restartujte server.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Heslo uživatele MySQL

Po dokončení instalace kořenového hesla MySQL je ve výchozím nastavení prázdné. Spustit **mysql\_zabezpečené\_instalace** skript k zabezpečení MySQL. Skript vyzve k změnit kořenového hesla MySQL, odeberte anonymní uživatelské účty, zakažte vzdálený kořen přihlášení, odebrat test databáze a znovu načíst tabulky oprávnění. 

Jakmile se server nerestartuje, ssh k virtuálnímu počítači znovu.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Přihlaste se k MySQL

Nyní můžete přihlásit a zadejte na příkazovém řádku MySQL.

```bash  
mysql -u root -p
```
Toto přepne vás do příkazového řádku MySQL kde můžete zadávat příkazy SQL pro interakci s databází.

Teď vytvořte nového uživatele MySQL.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Středníkem (;) na konci řádku je zásadní pro ukončení příkazu.


## <a name="create-a-database"></a>Vytvoření databáze


Vytvořit databázi a udělit `mysqluser` uživatelská oprávnění.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Databáze uživatelská jména a hesla používají pouze skripty s připojením k databázi.  Názvy uživatelských účtů databáze nutně nepředstavuje skutečný uživatelských účtů v systému.

Povolte přihlášení z jiného počítače. V tomto příkladu je IP adresa počítače povolit přihlášení z *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Ukončete nástroj pro správu databáze MySQL, zadejte:

```    
quit
```


## <a name="next-steps"></a>Další postup
Podrobnosti o MySQL, najdete v článku [dokumentace ke službě MySQL](http://dev.mysql.com/doc).




