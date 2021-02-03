---
title: Úvod do Linuxu na Azure
description: Informace o používání virtuálních počítačů s FreeBSD v Azure
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: ced29c3222b7f4f5d7cf5f9e472c420c47341786
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493239"
---
# <a name="introduction-to-freebsd-on-azure"></a>Úvod do Linuxu na Azure
Tento článek poskytuje přehled o spuštění virtuálního počítače s FreeBSD v Azure.

## <a name="overview"></a>Přehled
FreeBSD for Microsoft Azure je pokročilý počítačový operační systém, který slouží k napájení moderních serverů, stolních počítačů a integrovaných platforem.

Microsoft Corporation zpřístupňuje image FreeBSD v Azure s předem nakonfigurovaným [agentem hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) . V současné době jsou k dispozici následující verze FreeBSD jako image od Microsoftu:

- FreeBSD 10,4 na Azure Marketplace
- FreeBSD 11,2 na Azure Marketplace
- FreeBSD 12,0 na Azure Marketplace

Agent zodpovídá za komunikaci mezi virtuálním počítačem FreeBSD a prostředky infrastruktury Azure pro operace, jako je například zřízení virtuálního počítače při prvním použití (uživatelské jméno, heslo nebo klíč SSH, název hostitele atd.) a povolení funkcí pro selektivní rozšíření virtuálních počítačů.

Stejně jako v budoucích verzích FreeBSD je strategie aktuální a v krátké době jsou k dispozici nejnovější verze, které jsou publikovány týmem vydanými technickým vydáním FreeBSD.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Vytvoření virtuálního počítače s FreeBSD prostřednictvím rozhraní příkazového řádku Azure v FreeBSD
Nejdřív je potřeba nainstalovat rozhraní příkazového [řádku Azure CLI](/cli/azure/get-started-with-azure-cli) , i když na počítači FreeBSD tento příkaz

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Pokud na vašem počítači FreeBSD není nainstalovaný bash, spusťte před instalací následující příkaz. 

```bash
sudo pkg install bash
```

Pokud Python není nainstalovaný na vašem počítači s FreeBSD, spusťte před instalací následující příkazy. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Během instalace se zobrazí výzva `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` . Pokud odpovíte `y` a zadáte `/etc/rc.conf` jako `a path to an rc file to update` , může se tento problém vyhovět `ERROR: [Errno 13] Permission denied` . Chcete-li tento problém vyřešit, měli byste tomuto souboru udělit právo k zápisu pro aktuálního uživatele `etc/rc.conf` .

Nyní se můžete přihlásit k Azure a vytvořit virtuální počítač s FreeBSD. Níže je příklad vytvoření virtuálního počítače s FreeBSD 11,0. Můžete také přidat parametr `--public-ip-address-dns-name` s globálně jedinečným názvem DNS pro nově vytvořenou veřejnou IP adresu. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Pak se můžete k VIRTUÁLNÍmu počítači s FreeBSD přihlašovat prostřednictvím IP adresy, která se vytiskla ve výstupu výše uvedeného nasazení. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Rozšíření virtuálních počítačů pro FreeBSD
Níže jsou podporovaná rozšíření virtuálních počítačů v FreeBSD.

### <a name="vmaccess"></a>VMAccess
Rozšíření [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) může:

* Resetujte heslo původního uživatele sudo.
* Vytvořte nového uživatele sudo se zadaným heslem.
* Nastavte klíč veřejného hostitele s daným klíčem.
* Pokud není zadaný hostitelský klíč, resetujte klíč veřejného hostitele poskytnutý při zřizování virtuálních počítačů.
* Otevřete port SSH (22) a obnovte sshd_config, pokud je reset_ssh nastaven na hodnotu true.
* Odeberte existujícího uživatele.
* Ověřte disky.
* Opravte přidaný disk.

### <a name="customscript"></a>CustomScript
Rozšíření [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) může:

* Pokud je tato služba k dispozici, Stáhněte si vlastní skripty z Azure Storage nebo externí veřejné úložiště (například GitHub).
* Spusťte skript vstupního bodu.
* Podporují vložené příkazy.
* Automatické převedení stylu Windows na nový styl ve skriptech Shell a Pythonu
* Automatické odebrání kusovníku ve skriptech prostředí a Pythonu
* Chraňte citlivá data v CommandToExecute.

> [!NOTE]
> Virtuální počítač FreeBSD podporuje teď jenom CustomScript verze 1. x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Ověřování: uživatelská jména, hesla a klíče SSH
Když vytváříte virtuální počítač s FreeBSD pomocí Azure Portal, je nutné zadat uživatelské jméno, heslo nebo veřejný klíč SSH.
Uživatelská jména pro nasazení virtuálního počítače s FreeBSD v Azure nesmí odpovídat názvům systémových účtů (UID <100), které už ve virtuálním počítači existují (například root).
V současné době se podporuje jenom klíč RSA SSH. Víceřádkový klíč SSH musí začínat řetězcem `---- BEGIN SSH2 PUBLIC KEY ----` a končit na `---- END SSH2 PUBLIC KEY ----` .

## <a name="obtaining-superuser-privileges"></a>Získání uživatelských oprávnění
Uživatelský účet, který je zadaný během nasazování instance virtuálního počítače v Azure, je privilegovaný účet. Balíček sudo byl nainstalován v publikované imagi FreeBSD.
Po přihlášení pomocí tohoto uživatelského účtu můžete spustit příkazy jako kořen pomocí syntaxe příkazu.

```
$ sudo <COMMAND>
```

Můžete volitelně získat kořenové prostředí pomocí `sudo -s` .

## <a name="known-issues"></a>Známé problémy
[Agent hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.2 má [známý problém](https://github.com/Azure/WALinuxAgent/pull/517) , který způsobuje selhání zřizování pro virtuální počítač FreeBSD v Azure. Oprava byla zachycena [agentem hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.3 a novějších verzí. 

## <a name="next-steps"></a>Další kroky
* Pokud chcete vytvořit virtuální počítač s FreeBSD, otevřete [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/thefreebsdfoundation.freebsd-12_2?tab=Overview) .
