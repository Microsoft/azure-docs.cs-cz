---
title: Úvod do FreeBSD v Azure | Microsoft Docs
description: Další informace o použití FreeBSD virtuální počítače v Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: 9c7cf223eab3e989436e12c39b122f2aee7619a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906656"
---
# <a name="introduction-to-freebsd-on-azure"></a>Úvod do FreeBSD v Azure
Toto téma obsahuje přehled spuštěným virtuálním počítačem FreeBSD v Azure.

## <a name="overview"></a>Přehled
FreeBSD pro Microsoft Azure je operační systém pokročilé počítače použít k power moderní serverů, stolních počítačů a vložených platformy.

Microsoft Corporation je zpřístupnění bitové kopie FreeBSD v Azure pomocí [agenta hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) předem nakonfigurované. V současné době jsou následující verze FreeBSD nabízí jako obrázky společností Microsoft:

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0 – verze
- UVOLNĚNÍ FreeBSD 11.1

Agent je zodpovědná za komunikaci mezi FreeBSD virtuálních počítačů a prostředků infrastruktury Azure pro operace, jako je například zřizování virtuálních počítačů při prvním použití (uživatelské jméno, heslo nebo klíč SSH, název hostitele, atd.) a povolení funkce pro selektivní rozšíření virtuálního počítače.

Jako u budoucích verzích FreeBSD strategie je Udržujte aktuální stav a zpřístupní nejnovější verze krátce po jsou publikovány nástrojem FreeBSD verze technickému týmu.

## <a name="deploying-a-freebsd-virtual-machine"></a>Nasazení virtuálního počítače FreeBSD
Nasazení virtuálního počítače FreeBSD je jednoduchý proces pomocí bitovou kopii z Azure Marketplace z portálu Azure:

- [FreeBSD 10.3 v Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 v Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)
- [FreeBSD 11.1 v Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>Vytvoření virtuálního počítače FreeBSD prostřednictvím rozhraní příkazového řádku Azure 2.0 na FreeBSD
Nejdřív je potřeba nainstalovat [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) i když následující příkaz na počítači FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Pokud bash není nainstalovaný na počítači FreeBSD, spusťte následující příkaz před instalací. 

```bash
sudo pkg install bash
```

Pokud python není nainstalovaný na počítači FreeBSD, spusťte následující příkazy před instalací. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Během instalace se zobrazí výzva `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Pokud odpovíte `y` a zadejte `/etc/rc.conf` jako `a path to an rc file to update`, splňujete problém `ERROR: [Errno 13] Permission denied`. Chcete-li vyřešit tento problém, byste měli udělit zápis přímo na aktuální uživatel proti souboru `etc/rc.conf`.

Nyní můžete přihlásit Azure a vytvořit FreeBSD virtuálního počítače. Dole je příklad k vytvoření virtuálního počítače s FreeBSD 11.0. Můžete také přidat parametr `--public-ip-address-dns-name` s globálně jedinečného názvu DNS pro nově vytvořený veřejnou IP adresu. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Potom můžete přihlásit k virtuálnímu počítači FreeBSD prostřednictvím ip adresy, které vytisknout ve výstupu výše nasazení. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Rozšíření virtuálního počítače pro FreeBSD
Toto jsou podporované rozšíření virtuálního počítače v FreeBSD.

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) rozšíření můžete:

* Resetování hesla původního uživatele sudo.
* Vytvořte nového uživatele sudo s zadané heslo.
* Nastavte klíč veřejný hostitele s zadaný klíč.
* Resetujte veřejný hostitele klíči poskytovaném při zřizování virtuálních počítačů, pokud klíč hostitele není k dispozici.
* Otevřít port SSH (22) a obnovení sshd_config Pokud reset_ssh nastavena na hodnotu true.
* Odeberte stávající uživatele.
* Zkontrolujte disky.
* Přidání disku opravte.

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) rozšíření můžete:

* Pokud je zadán, stahovat vlastní skripty z Azure Storage nebo veřejného externího úložiště (například Githubu).
* Spusťte skript vstupní bod.
* Vnořené příkazy podpory.
* Automaticky převeďte nového řádku styl systému Windows v prostředí a skriptů Python.
* Automaticky odeberte BOM v prostředí a skriptů Python.
* Chrání citlivá data v CommandToExecute.

> [!NOTE]
> Virtuální počítač FreeBSD podporuje pouze verzi CustomScript 1.x nyní.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Ověřování: uživatelská jména, hesla a klíče SSH
Při vytváření virtuálního počítače FreeBSD pomocí portálu Azure, je nutné zadat uživatelské jméno, heslo nebo veřejný klíč SSH.
Uživatelská jména pro nasazení virtuálního počítače FreeBSD v Azure nesmí shodovat s názvy účtů systému (UID < 100) již existuje ve virtuálním počítači ("root", např.).
V současné době je podporován pouze se RSA klíč SSH. Víceřádkový klíč SSH musí začínat řetězcem `---- BEGIN SSH2 PUBLIC KEY ----` a končit `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Získání oprávnění superuživatele
Uživatelský účet, který je zadán během nasazení instance virtuálních počítačů v Azure je privilegovaný účet. V publikované image FreeBSD byl nainstalován balíček sudo.
Poté, co jste přihlášeni prostřednictvím tento uživatelský účet, můžete spustit příkazy jako kořenová pomocí syntaxe příkazu.

```
$ sudo <COMMAND>
```

Kořenové prostředí můžete volitelně můžete získat pomocí `sudo -s`.

## <a name="known-issues"></a>Známé problémy
[Agenta hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.2 má [známý problém] (https://github.com/Azure/WALinuxAgent/pull/517) , která způsobí selhání přidělení pro virtuální počítač FreeBSD v Azure. Oprava zaznamenaná [agenta hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.3 a pozdějších verzích. 

## <a name="next-steps"></a>Další postup
* Přejděte na [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) vytvoření FreeBSD virtuálního počítače.
