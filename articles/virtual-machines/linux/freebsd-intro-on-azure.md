---
title: Úvod do Linuxu na Azure
description: Informace o používání virtuálních počítačů FreeBSD v Azure
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: fe64418e254289a29aafd155b92396082bff5b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945088"
---
# <a name="introduction-to-freebsd-on-azure"></a>Úvod do Linuxu na Azure
Tento článek obsahuje přehled spuštění virtuálního počítače FreeBSD v Azure.

## <a name="overview"></a>Přehled
FreeBSD pro Microsoft Azure je pokročilý počítačový operační systém používaný k napájení moderních serverů, stolních počítačů a vestavěných platforem.

Microsoft Corporation zpřístupňuje image FreeBSD v Azure s předem nakonfigurovaným [agentem hosta virtuálního počítače Azure.](https://github.com/Azure/WALinuxAgent/) V současné době jsou následující verze FreeBSD nabízeny jako obrázky společností Microsoft:

- [FreeBSD 10.4 na Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 na Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [FreeBSD 12.0 na Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

Agent je zodpovědný za komunikaci mezi virtuálním počítačem FreeBSD a infrastrukturou Azure pro operace, jako je zřizování virtuálního počítače při prvním použití (uživatelské jméno, heslo nebo klíč SSH, název hostitele atd.) a povolení funkcí pro selektivní rozšíření virtuálních zařízení.

Pokud jde o budoucí verze FreeBSD, strategií je zůstat aktuální a zpřístupnit nejnovější verze krátce poté, co je zveřejní technický tým FreeBSD.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Vytvoření virtuálního počítače FreeBSD prostřednictvím azure cli na FreeBSD
Nejprve je třeba nainstalovat [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) i když následující příkaz na počítači FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Pokud bash není na vašem FreeBSD počítači nainstalován, spusťte následující příkaz před instalací. 

```bash
sudo pkg install bash
```

Pokud python není na vašem FreeBSD počítači nainstalován, spusťte před instalací následující příkazy. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Během instalace budete dotázáni `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. `y` Pokud odpovíte `/etc/rc.conf` a `a path to an rc file to update`zadáte jako , `ERROR: [Errno 13] Permission denied`můžete se s problémem setkat . Chcete-li tento problém vyřešit, měli byste udělit právo zápisu aktuálnímu uživateli proti souboru `etc/rc.conf`.

Teď se můžete přihlásit do Azure a vytvořit si virtuální počítač FreeBSD. Níže je uveden příklad pro vytvoření virtuálního virtuálního ms FreeBSD 11.0. Můžete také přidat `--public-ip-address-dns-name` parametr s globálně jedinečným názvem DNS pro nově vytvořenou veřejnou IP adresu. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Pak se můžete přihlásit ke svému virtuálnímu počítači FreeBSD prostřednictvím IP adresy, která se vytiskla ve výstupu výše uvedeného nasazení. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Rozšíření virtuálních virtuálních montovek pro FreeBSD
Ve FreeBSD jsou podporována rozšíření virtuálních montovny.

### <a name="vmaccess"></a>Přístup virtuálního připojení
Rozšíření [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) může:

* Resetujte heslo původního uživatele sudo.
* Vytvořte nového uživatele sudo se zadaným heslem.
* Nastavte klíč veřejného hostitele s daným klíčem.
* Pokud není k dispozici klíč hostitele, který je k dispozici, obnovte klíč veřejného hostitele poskytovaný během zřizování virtuálních zařízení.
* Otevřete port SSH (22) a obnovte sshd_config, pokud je reset_ssh nastavena na hodnotu true.
* Odeberte existujícího uživatele.
* Zkontrolujte disky.
* Opravte přidaný disk.

### <a name="customscript"></a>Vlastní script
Rozšíření [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) může:

* Pokud je k dispozici, stáhněte si přizpůsobené skripty z Azure Storage nebo externího veřejného úložiště (například GitHub).
* Spusťte skript vstupního bodu.
* Podpora inline příkazů.
* Automaticky převeďte nový řádek ve stylu windows v prostředí a skriptech Pythonu.
* Automaticky odeberte kusovník ve skriptech prostředí a Pythonu.
* Chraňte citlivá data v příkazu CommandToExecute.

> [!NOTE]
> FreeBSD VM podporuje pouze CustomScript verze 1.x nyní.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Ověřování: uživatelská jména, hesla a klíče SSH
Při vytváření virtuálního počítače FreeBSD pomocí portálu Azure musíte zadat uživatelské jméno, heslo nebo veřejný klíč SSH.
Uživatelská jména pro nasazení virtuálního počítače FreeBSD v Azure se nesmí shodovat s názvy systémových účtů (UID <100), které jsou už ve virtuálním počítači k dispozici (například "root").
V současné době je podporován pouze klíč RSA SSH. Víceřádkový klíč SSH `---- BEGIN SSH2 PUBLIC KEY ----` musí `---- END SSH2 PUBLIC KEY ----`začínat a končit klávesou .

## <a name="obtaining-superuser-privileges"></a>Získání oprávnění superuživatele
Uživatelský účet, který je určen během nasazení instance virtuálního počítače v Azure, je privilegovaný účet. Balíček sudo byl nainstalován v publikované masce FreeBSD.
Po přihlášení prostřednictvím tohoto uživatelského účtu můžete spouštět příkazy jako root pomocí syntaxe příkazu.

```
$ sudo <COMMAND>
```

Volitelně můžete získat kořenové `sudo -s`prostředí pomocí .

## <a name="known-issues"></a>Známé problémy
Agent [hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.2 má známý [problém,](https://github.com/Azure/WALinuxAgent/pull/517) který způsobuje selhání zřízení pro virtuální počítač FreeBSD v Azure. Oprava byla zachycena [agentem hosta virtuálního počítače Azure](https://github.com/Azure/WALinuxAgent/) verze 2.2.3 a novějšími verzemi. 

## <a name="next-steps"></a>Další kroky
* Přejděte na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) a vytvořte virtuální počítač FreeBSD.
