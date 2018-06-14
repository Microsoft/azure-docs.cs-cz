---
title: Instalace databáze MySQL na OpenSUSE virtuální počítač v Azure | Microsoft Docs
description: Další informace pro instalaci databáze MySQL na počítači OpenSUSE Linux VMirtual v Azure.
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
ms.date: 01/22/2018
ms.author: cynthn
ms.openlocfilehash: 88bd895cb3a384f1ada0394fe2da206aca86b981
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2018
ms.locfileid: "28001175"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Instalace MySQL do virtuálního počítače se spuštěným OpenSUSE Linuxem v Azure

[MySQL](http://www.mysql.com) je Oblíbené databáze SQL open source. V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Linuxem OpenSUSE a pak nainstalujte MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, je třeba Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Vytvoření virtuálního počítače se systémem OpenSUSE Linux

Nejprve vytvořte skupinu prostředků. V tomto příkladu jsme vytváření názvů skupině prostředků *mySQSUSEResourceGroup* a vytvoříte ji v *východní USA* oblast.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Vytvoření virtuálního počítače. V tomto příkladu jsme vytváření názvů virtuálních počítačů *Můjvp*. Také jsme budete používat velikost virtuálního počítače *Standard_D2s_v3*, ale měli byste vybrat [velikost virtuálního počítače](sizes.md) si myslíte, že je nejvhodnější pro vaše úlohy.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Musíte taky přidat pravidlo na skupinu zabezpečení sítě umožňuje provoz přes port 3306 pro databázi MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

SSH budete používat pro připojení k virtuálnímu počítači. V tomto příkladu veřejnou IP adresu virtuálního počítače je *10.111.112.113*. Při vytváření virtuálního počítače, můžete zobrazit IP adresu ve výstupu.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Aktualizace virtuálního počítače
 
Poté, co jste připojení k virtuálnímu počítači, nainstalujte opravy a aktualizace systému. 
   
```bash
sudo zypper update
```

Postupujte podle pokynů k aktualizaci virtuálního počítače.

## <a name="install-mysql"></a>Instalace MySQL 


Nainstalujte MySQL ve virtuálním počítači přes protokol SSH. Odpovězte na podle potřeby vyzve k zadání.

```bash
sudo zypper install mysql
```
 
Nastavit MySQL spustit při spuštění systému. 

```bash
sudo systemctl enable mysql
```
Ověřte, zda je povoleno MySQL.

```bash
systemctl is-enabled mysql
```

To by měla vrátit: povoleno.


## <a name="mysql-password"></a>Heslo MySQL

Po instalaci MySQL kořenové heslo je prázdné ve výchozím nastavení. Spustit **mysql\_zabezpečené\_instalace** skript k zabezpečení MySQL. Skript vyzve k změnit kořenové heslo MySQL, odeberte anonymní uživatelské účty, zakažte kořenové vzdáleného přihlášení, odebrat testovací databáze a znovu načíst tabulky oprávnění. 


```bash
mysql_secure_installation
```

## <a name="log-in-to-mysql"></a>Přihlaste se k MySQL

Nyní můžete přihlásit a zadejte řádku MySQL.

```bash  
mysql -u root -p
```
To přepínačů můžete do příkazového řádku MySQL kde můžete použít příkazy SQL pro interakci s databází.

Teď vytvořte nového uživatele databáze MySQL.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Oddělte středníkem (;) na konci řádku je zásadní pro ukončení příkazu.


## <a name="create-a-database"></a>Vytvoření databáze


Vytvoření databáze a udělit `mysqluser` oprávnění uživatele.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Databáze uživatelská jména a hesla používají pouze skripty s připojením k databázi.  Názvy databáze uživatelských účtů nutně nepředstavuje skutečný uživatelské účty v systému.

Povolte přihlášení z jiného počítače. V tomto příkladu je IP adresa počítače, který chcete přihlásit z *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Ukončete nástroj pro správu databáze MySQL, zadejte:

```    
quit
```


## <a name="next-steps"></a>Další postup
Podrobnosti o MySQL najdete v tématu [MySQL dokumentaci](http://dev.mysql.com/doc/index-topic.html).




