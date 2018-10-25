---
title: Úvod do Linuxu v Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: 9c3815f0083d049d9b4baed8e360f5927fcd3d69
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025678"
---
# <a name="introduction-to-freebsd-on-azure"></a>Úvod do Linuxu na Azure
Tento článek obsahuje přehled systémem FreeBSD virtuální počítač v Azure.

## <a name="overview"></a>Přehled
FreeBSD pro Microsoft Azure je pokročilým operačního systému používá power moderní serverů, stolních počítačů a vkládat platformy.

Microsoft Corporation dosahuje imagí FreeBSD k dispozici v Azure s [Agent hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) předem nakonfigurovaná. V současné době nabízíme tyto verze FreeBSD jako imagí od Microsoftu:

- FreeBSD 10.3 – verze
- FreeBSD 10.4 – verze
- FreeBSD 11.1 – verze

Agent zodpovídá za komunikaci mezi FreeBSD virtuální počítač a prostředky infrastruktury Azure pro operace, jako je zřizování virtuálního počítače při prvním použití (uživatelské jméno, heslo nebo klíč SSH, název hostitele, atd.) a povolení funkce pro selektivní rozšíření virtuálních počítačů.

Jako u budoucích verzích FreeBSD strategie je aktuální a zpřístupní nejnovější vydané verzi krátce po k publikování aplikací technický tým FreeBSD vydání.

## <a name="deploying-a-freebsd-virtual-machine"></a>Nasazení virtuálního počítače FreeBSD
Nasazení virtuálního počítače FreeBSD je jednoduchý proces, pomocí image z Azure Marketplace na webu Azure Portal:

- [FreeBSD 10.3 na Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103)
- [FreeBSD 10.4 na Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.1 na Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Vytvoření virtuálního počítače FreeBSD prostřednictvím rozhraní příkazového řádku Azure na FreeBSD
Nejdřív je potřeba nainstalovat [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ale následující příkaz na počítači FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Pokud na svém počítači FreeBSD není nainstalovaná prostředí bash, spusťte následující příkaz, před instalací. 

```bash
sudo pkg install bash
```

Pokud na svém počítači FreeBSD není nainstalován python, spusťte následující příkazy před instalací. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Při instalaci, zobrazí se výzva `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Pokud odpovíte `y` a zadejte `/etc/rc.conf` jako `a path to an rc file to update`, bude vyhovovat problém `ERROR: [Errno 13] Permission denied`. Chcete-li vyřešit tento problém, byste měli udělit zápis zprava aktuálního uživatele u souboru `etc/rc.conf`.

Nyní můžete přihlásit k Azure a vytvoření virtuálního počítače FreeBSD. Tady je příklad k vytvoření virtuálního počítače s FreeBSD 11.0. Můžete také přidat parametr `--public-ip-address-dns-name` globálně jedinečným názvem DNS pro nově vytvořenou veřejnou IP adresu. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Potom můžete přihlásit ke svému virtuálnímu počítači FreeBSD prostřednictvím ip adresy, která vytiskne ve výstupu výše nasazení. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Rozšíření virtuálních počítačů pro FreeBSD
Toto jsou podporované rozšíření virtuálních počítačů v FreeBSD.

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) rozšíření můžete:

* Resetovat heslo uživatele původního sudo.
* Vytvoření nového uživatele sudo se zadaným heslem.
* Nastavte klíč veřejné hostitele s klíčem zadaný.
* Resetovat veřejný hostitel key zadaný během zřizování virtuálních počítačů, pokud není k dispozici klíč hostitele.
* Otevřít port SSH (22) a obnovení sshd_config Pokud reset_ssh je nastavena na hodnotu true.
* Odeberte existujícího uživatele.
* Kontrola disků.
* Opravte přidaný disk.

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) rozšíření můžete:

* Pokud je zadán, stáhněte z Azure Storage nebo externího veřejného úložiště (například GitHub) vlastní skripty.
* Spusťte skript vstupního bodu.
* Podporují vložené příkazy.
* Automaticky převeďte obsahuje znak nového řádku ve stylu Windows shell a Python skripty.
* Automaticky odeberte BOM v prostředí shell a Python skripty.
* Ochrana citlivých dat během CommandToExecute.

> [!NOTE]
> FreeBSD virtuální počítač podporuje pouze CustomScript verzi 1.x nyní.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Ověřování: uživatelská jména, hesla a klíčů SSH
Při vytváření FreeBSD virtuální počítač pomocí webu Azure portal, je nutné zadat uživatelské jméno, heslo nebo veřejný klíč SSH.
Uživatelská jména k nasazení FreeBSD virtuální počítač v Azure nesmí shodovat s názvy systémových účtů (UID < 100) již existuje ve virtuálním počítači ("root", například).
V současné době se podporuje pouze RSA SSH klíč. Víceřádkový klíč SSH musí začínat řetězcem `---- BEGIN SSH2 PUBLIC KEY ----` a na konci `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Získání oprávnění superuživatele
Uživatelský účet, který je určen během nasazení instancí virtuálních počítačů v Azure je privilegovaného účtu. Balíček sudo nainstalovaný v publikované image FreeBSD.
Po jste přihlášeni pomocí účtu uživatele, můžete spouštět příkazy jako uživatel root pomocí syntaxe příkazu.

```
$ sudo <COMMAND>
```

Volitelně můžete získat kořenového prostředí s využitím `sudo -s`.

## <a name="known-issues"></a>Známé problémy
[Agent hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.2 má [známý problém](https://github.com/Azure/WALinuxAgent/pull/517) , která způsobuje selhání zřizování pro FreeBSD virtuální počítač v Azure. Oprava byla zachycena [Agent hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.3 a novějších verzí. 

## <a name="next-steps"></a>Další postup
* Přejděte na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111) vytvořit FreeBSD virtuální počítač.
