---
title: Resetování přístupu k virtuálním počítači Azure s Linuxem | Dokumentace Microsoftu
description: Jak spravovat administrativní uživatele a resetování přístupu na virtuální počítače s Linuxem pomocí rozšíření VMAccess a rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: 71aecc1748e70e2119b1f54c21a0f705afc5d5d0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731304"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Spravovat administrativní uživatelé, SSH a kontrola nebo opravte disky na virtuální počítače s Linuxem pomocí rozšíření VMAccess pomocí Azure CLI
## <a name="overview"></a>Přehled
Disk ve virtuálním počítačům s Linuxem se zobrazují se chyby. Nějakým způsobem resetování kořenového hesla pro virtuální počítač s Linuxem nebo odstranění privátního klíče SSH. V takovém případě zpět v dny v datovém centru musíte existuje a pak otevřete KVM získat z konzoly serveru. Rozšíření Azure VMAccess můžete představit jako tohoto KVM přepínače, který umožňuje přístup ke konzole obnovit přístup k Linuxu nebo provést údržbu na úrovni disku.

V tomto článku se dozvíte, jak pomocí rozšíření Azure VMAccess zkontrolovat nebo opravě disku, obnovit přístup uživatelů, spravovat účty administrativních uživatelů nebo aktualizovat konfiguraci SSH v Linuxu, když jsou spuštěné jako virtuální počítače Azure Resource Manageru. Pokud potřebujete ke správě virtuálních počítačů modelu Classic - můžete postupujte podle pokynů uvedených v [classic dokumentaci k virtuálním počítačům](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Pokud používáte rozšíření VMAccess resetovat heslo k vašemu virtuálnímu počítači po instalaci rozšíření AAD přihlášení, budete muset znovu spustit rozšíření přihlašovacích AAD opětovné povolení AAD přihlášení pro váš počítač.

## <a name="prerequisites"></a>Požadavky
### <a name="operating-system"></a>Operační systém

Rozšíření přístupu virtuálních počítačů můžete spustit proti těmto distribucí systému Linux:

| Distribuce | Verze |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS a 12.04 LTS |
| Debian | Debian 7.9 +, 8.2 + |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| SuSE | 11 a 12 |
| OpenSuse | openSUSE přestupné 42.2 + |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Způsoby, jak použít rozšíření VMAccess
Existují dva způsoby, můžete použít rozšíření VMAccess na vaše virtuální počítače s Linuxem:

* Pomocí Azure CLI a požadované parametry.
* [Používat nezpracované soubory JSON, které zpracovávají rozšíření VMAccess](#use-json-files-and-the-vmaccess-extension) a pak na ně.

Následující příklady používají [az vm uživatele](/cli/azure/vm/user) příkazy. Pokud chcete tento postup, musíte na nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Aktualizovat klíč SSH
Následující příklad aktualizuje klíč SSH pro uživatele `azureuser` na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **POZNÁMKA:** `az vm user update` Příkaz přidá nový veřejný klíč text, který se `~/.ssh/authorized_keys` souboru pro uživatele s rolí správce ve virtuálním počítači. Není to nahraďte nebo odstraňte nějaké existující klíče SSH. Nedojde k odebrání předchozí klíče nastavit v době nasazení nebo následné aktualizace prostřednictvím rozšíření VMAccess.

## <a name="reset-password"></a>Resetování hesla
Následující příklad resetuje heslo pro uživatele `azureuser` na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Restartujte SSH
Následující příklad restartuje proces démon programu SSH a konfiguraci SSH obnovíte výchozí hodnoty na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Vytvořit uživatele pro správu/sudo
Následující příklad vytvoří uživatele s názvem `myNewUser` s **sudo** oprávnění. Tento účet používá klíč SSH pro ověřování na virtuálním počítači s názvem `myVM`. Tato metoda je určena můžete získat přístup k virtuálnímu počítači v případě, že aktuální přihlašovací údaje jsou ztráty či zapomenutí. Jako osvědčený postup pro účty s **sudo** oprávnění by měla být omezena.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Odstranění uživatele
Následující příklad odstraní uživatele s názvem `myNewUser` na virtuálním počítači s názvem `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Použít soubory JSON a rozšíření VMAccess
Následující příklady používají nezpracovaných souborů JSON. Použití [sada rozšíření az vm](/cli/azure/vm/extension) pak volat souborů JSON. Tyto soubory JSON můžete také volat z šablony Azure. 

### <a name="reset-user-access"></a>Resetování přístupu uživatelů
Pokud jste ztratili přístup do kořenového adresáře na virtuální počítač s Linuxem, můžete spustit skript VMAccess aktualizovat klíč SSH nebo hesla uživatele.

Pokud chcete aktualizovat veřejný klíč SSH uživatele, vytvořte soubor s názvem `update_ssh_key.json` a přidat nastavení v následujícím formátu. Nahraďte vlastními hodnotami pro `username` a `ssh_key` parametry:

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

Pokud chcete resetovat heslo uživatele, vytvořte soubor s názvem `reset_user_password.json` a přidat nastavení v následujícím formátu. Nahraďte vlastními hodnotami pro `username` a `password` parametry:

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

### <a name="restart-ssh"></a>Restartujte SSH
Pokud chcete restartovat proces démon programu SSH a konfiguraci SSH obnovíte výchozí hodnoty, vytvořte soubor s názvem `reset_sshd.json`. Přidejte následující obsah:

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

### <a name="manage-administrative-users"></a>Spravovat administrativní uživatele

Chcete-li vytvořit uživatele s **sudo** oprávnění, která používá klíč SSH pro ověřování, vytvořte soubor s názvem `create_new_user.json` a přidat nastavení v následujícím formátu. Nahraďte vlastními hodnotami pro `username` a `ssh_key` parametry. Tato metoda je určena můžete získat přístup k virtuálnímu počítači v případě, že aktuální přihlašovací údaje jsou ztráty či zapomenutí. Jako osvědčený postup pro účty s **sudo** oprávnění by měla být omezena.

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

Pokud chcete odstranit uživatele, vytvořte soubor s názvem `delete_user.json` a přidejte následující obsah. Nahraďte vlastní hodnoty `remove_user` parametr:

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

### <a name="check-or-repair-the-disk"></a>Kontrola nebo opravte disku
Použitím VMAccess můžete také zkontrolovat a opravit disk, který jste přidali do virtuálního počítače s Linuxem.

Zkontrolujte a opravte disk, vytvořte soubor s názvem `disk_check_repair.json` a přidat nastavení v následujícím formátu. Nahraďte vlastní hodnotu pro název `repair_disk`:

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
## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
