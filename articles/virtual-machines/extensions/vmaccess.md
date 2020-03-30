---
title: Obnovení přístupu k virtuálnímu počítači Azure Linux
description: Jak spravovat administrativní uživatele a resetovat přístup na virtuálních počítačích S IP pomocí rozšíření VMAccess a nastavení příkazového uživatelského příkazu Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: bd9dc05a84a4ee54fce40e6c88e87ac90bfee8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250358"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Správa administrativních uživatelů, SSH a kontrola nebo oprava disků na virtuálních počítačích SB s Linuxem pomocí rozšíření VMAccess pomocí azure cli
## <a name="overview"></a>Přehled
Disk na virtuálním počítači s Linuxem zobrazuje chyby. Nějak resetujete kořenové heslo pro váš virtuální počítač SN Linux nebo omylem smažete soukromý klíč SSH. Pokud k tomu došlo ve dnech datového centra, budete muset jet tam a pak otevřít KVM se dostat na server konzoly. Rozšíření Azure VMAccess si můžete myslet jako na přepínač KVM, který umožňuje přístup ke konzole pro resetování přístupu k Linuxu nebo údržbu na úrovni disku.

Tento článek ukazuje, jak pomocí rozšíření Azure VMAccess ke kontrole nebo opravě disku, obnovení přístupu uživatelů, správě uživatelských účtů pro správu nebo aktualizaci konfigurace SSH na Linuxu, když běží jako virtuální počítače Azure Resource Manager. Pokud potřebujete spravovat klasické virtuální počítače - můžete postupovat podle pokynů v [klasické dokumentaci k virtuálním počítačům](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Pokud používáte rozšíření VMAccess k resetování hesla virtuálního počítače po instalaci rozšíření pro přihlášení AAD, budete muset znovu spustit rozšíření pro přihlášení AAD, abyste znovu povolili přihlášení AAD pro váš počítač.

## <a name="prerequisites"></a>Požadavky
### <a name="operating-system"></a>Operační systém

Rozšíření Přístupu k virtuálním počítači lze spustit proti těmto linuxovým distribucím:

| Distribuce | Version |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS a 12,04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6,7+, 7,1+ |
| Oracle Linux | 6.4+, 7.0+ |
| Suse | 11 a 12 |
| Opensuse | openSUSE Leap 42.2+ |
| CentOS | Centos 6,3+, 7,0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Způsoby použití rozšíření VMAccess
Rozšíření VMAccess můžete na virtuálních počítačích s Linuxem použít dvěma způsoby:

* Použijte rozhraní příkazového řádku Azure a požadované parametry.
* [Použijte nezpracované soubory JSON, které proces rozšíření VMAccess proces](#use-json-files-and-the-vmaccess-extension) a pak jednat.

Následující příklady používají uživatelské příkazy [az vm.](/cli/azure/vm/user) K provedení těchto kroků potřebujete nejnovější [azure cli](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Aktualizace klíče SSH
Následující příklad aktualizuje klíč SSH `azureuser` pro uživatele na `myVM`virtuálním počítači s názvem :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **POZNÁMKA:** Příkaz `az vm user update` připojí nový text veřejného `~/.ssh/authorized_keys` klíče do souboru pro uživatele správce na virtuálním počítači. Tím se nenahradí ani neodebere žádné existující klíče SSH. Tím se neodeberou předchozí klíče nastavené v době nasazení nebo následné aktualizace prostřednictvím rozšíření VMAccess.

## <a name="reset-password"></a>Resetování hesla
Následující příklad obnoví heslo pro `azureuser` uživatele na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Restartování SSH
Následující příklad restartuje daemon SSH a obnoví konfiguraci SSH na `myVM`výchozí hodnoty na virtuálním počítači s názvem :

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Vytvoření uživatele s oprávněním správce nebo sudo
Následující příklad vytvoří uživatele `myNewUser` s názvem **s oprávněními sudo.** Účet používá klíč SSH pro ověřování na `myVM`virtuálním počítači s názvem . Tato metoda je navržena tak, aby vám pomohla znovu získat přístup k virtuálnímu počítači v případě, že jsou ztracena nebo zapomenuta aktuální pověření. Jako osvědčený postup by měly být omezeny účty s **oprávněními sudo.**

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Odstranění uživatele
Následující příklad odstraní uživatele `myNewUser` s názvem na `myVM`virtuálním počítači :

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Použití souborů JSON a rozšíření VMAccess
Následující příklady používají nezpracované soubory JSON. Pomocí [sady rozšíření az vm](/cli/azure/vm/extension) pak volat soubory JSON. Tyto soubory JSON lze také volat ze šablon Azure. 

### <a name="reset-user-access"></a>Obnovení přístupu uživatelů
Pokud jste ztratili přístup ke kořenovému adresáři na virtuálním počítači s Linuxem, můžete spustit skript VMAccess a aktualizovat klíč nebo heslo SSH uživatele.

Chcete-li aktualizovat veřejný klíč SSH uživatele, `update_ssh_key.json` vytvořte soubor s názvem a přidejte nastavení v následujícím formátu. Nahraďte své `username` vlastní `ssh_key` hodnoty pro a parametry:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Spusťte skript VMAccess pomocí:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Chcete-li obnovit uživatelské heslo, `reset_user_password.json` vytvořte soubor s názvem a přidejte nastavení v následujícím formátu. Nahraďte své `username` vlastní `password` hodnoty pro a parametry:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Spusťte skript VMAccess pomocí:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Restartování SSH
Chcete-li restartovat daemon SSH a obnovit výchozí hodnoty `reset_sshd.json`konfigurace SSH, vytvořte soubor s názvem . Přidejte následující obsah:

```json
{
  "reset_ssh": true
}
```

Spusťte skript VMAccess pomocí:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Správa administrativních uživatelů

Chcete-li vytvořit uživatele s oprávněními **sudo,** který používá `create_new_user.json` klíč SSH pro ověřování, vytvořte soubor s názvem a přidejte nastavení v následujícím formátu. Nahraďte vlastní `username` hodnoty `ssh_key` parametry a. Tato metoda je navržena tak, aby vám pomohla znovu získat přístup k virtuálnímu počítači v případě, že jsou ztracena nebo zapomenuta aktuální pověření. Jako osvědčený postup by měly být omezeny účty s **oprávněními sudo.**

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Spusťte skript VMAccess pomocí:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Chcete-li odstranit uživatele, `delete_user.json` vytvořte soubor s názvem a přidejte následující obsah. Nahraďte parametr `remove_user` vlastní hodnotou:

```json
{
  "remove_user":"myNewUser"
}
```

Spusťte skript VMAccess pomocí:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Kontrola nebo oprava disku
Pomocí aplikace VMAccess můžete také zkontrolovat a opravit disk, který jste přidali do virtuálního počítače s Linuxem.

Chcete-li disk zkontrolovat a poté `disk_check_repair.json` opravit, vytvořte soubor s názvem a přidejte nastavení v následujícím formátu. Nahraďte vlastní hodnotu `repair_disk`názvem :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Spusťte skript VMAccess pomocí:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z portálu Azure a pomocí azure cli. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí příkazu Příkaz příkazu Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
