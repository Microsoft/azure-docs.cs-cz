---
title: Obnovte přístup na virtuální počítač Azure Linux | Microsoft Docs
description: Jak spravovat administrativní uživatele a obnovte přístup na virtuální počítače s Linuxem pomocí rozšíření VMAccess a 2.0 rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 15fab2b4f83b86227a3a2327669861b5adc8d3c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Spravovat administrativní uživatele, SSH a zkontrolujte nebo opravte disky na virtuální počítače s Linuxem pomocí rozšíření VMAccess 2.0 rozhraní příkazového řádku Azure
Disk ve virtuálním počítačům s Linuxem se zobrazuje chyby. Nějakým způsobem resetování hesla kořenového virtuálním počítačům s Linuxem nebo omylem odstraněné svůj privátní klíč SSH. V takovém případě zpět v dny v datovém centru potřebovali byste existuje jednotky a pak otevřete KVM získat z konzoly serveru. Rozšíření Azure VMAccess si můžete představit jako že KVM přepínačů, která umožňuje přístup ke konzole resetovat přístup do systému Linux nebo provést údržbu na úrovni disku.

Tento článek ukazuje, jak používat Azure rozšíření VMAccess k kontrola opravit disk, obnovte přístup uživatele, spravovat účty administrativních uživatelů nebo aktualizovat konfiguraci SSH na Linux. K provedení těchto kroků můžete také využít [Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Způsoby použití rozšíření VMAccess
Existují dva způsoby, které můžete rozšíření VMAccess na virtuální počítače Linux:

* Použití Azure CLI 2.0 a požadované parametry.
* [Použít nezpracované soubory JSON, které zpracovávají rozšíření VMAccess](#use-json-files-and-the-vmaccess-extension) a potom na.

Následující příklady použití [uživatele virtuálního počítače az](/cli/azure/vm/user) příkazy. K provedení těchto kroků, budete potřebovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

## <a name="update-ssh-key"></a>Aktualizovat klíč SSH
Následující příklad aktualizuje klíč SSH pro uživatele `azureuser` ve virtuálním počítači s názvem `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Poznámka:** `az vm user update` příkaz připojí nový veřejný klíč text, který `~/.ssh/authorized_keys` souboru pro uživatele správce ve virtuálním počítači. To nemá nahradit nebo odeberte nějaké existující klíče SSH. To nebude odebrat předchozí klíče nastavit v době nasazení nebo následné aktualizace prostřednictvím rozšíření VMAccess.

## <a name="reset-password"></a>Resetování hesla
Následující příklad resetuje heslo pro uživatele `azureuser` ve virtuálním počítači s názvem `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Restartujte SSH
V následujícím příkladu restartuje démon procesu SSH a konfiguraci SSH obnovíte výchozí hodnoty na virtuálním počítači s názvem `myVM`:

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Vytvořit uživatele správce nebo sudo
Následující příklad vytvoří uživatele s názvem `myNewUser` s **sudo** oprávnění. Účet používá klíč SSH pro ověřování na virtuální počítač s názvem `myVM`. Tato metoda je určena můžete znovu získat přístup k virtuálnímu počítači v případě, že aktuální přihlašovací údaje jsou ztratíte nebo zapomenete. Jako osvědčený postup, účtů s **sudo** oprávnění by měla být omezená.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Odstranit uživatele
Následující příklad odstraní uživatele s názvem `myNewUser` ve virtuálním počítači s názvem `myVM`:

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Použít soubory JSON a rozšíření VMAccess
Následující příklady použití nezpracované soubory JSON. Použití [nastavení rozšíření virtuálního az](/cli/azure/vm/extension#az_vm_extension_set) pak volat souborů JSON. Tyto soubory JSON je možné také volat z šablony Azure. 

### <a name="reset-user-access"></a>Obnovte uživatele přístup
Pokud jste ztratili přístup ke kořenové na virtuálním počítačům s Linuxem, můžete spustit skript VMAccess k aktualizaci klíče SSH uživatele nebo heslo.

Pokud chcete aktualizovat veřejný klíč SSH uživatele, vytvořte soubor s názvem `update_ssh_key.json` a přidat nastavení v následujícím formátu. Dosaďte svoje vlastní hodnoty `username` a `ssh_key` parametry:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Pokud chcete resetovat heslo uživatele, vytvořte soubor s názvem `reset_user_password.json` a přidat nastavení v následujícím formátu. Dosaďte svoje vlastní hodnoty `username` a `password` parametry:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Restartujte SSH
Pokud chcete restartovat démon procesu SSH a obnovit výchozí hodnoty v konfiguraci SSH, vytvořte soubor s názvem `reset_sshd.json`. Přidejte do něj následující obsah:

```json
{
  "reset_ssh": true
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Spravovat administrativní uživatele

Chcete-li vytvořit uživatele s **sudo** oprávnění, která používá klíč SSH pro ověřování, vytvořte soubor s názvem `create_new_user.json` a přidat nastavení v následujícím formátu. Dosaďte svoje vlastní hodnoty `username` a `ssh_key` parametry. Tato metoda je určena můžete znovu získat přístup k virtuálnímu počítači v případě, že aktuální přihlašovací údaje jsou ztratíte nebo zapomenete. Jako osvědčený postup, účtů s **sudo** oprávnění by měla být omezená.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
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

Spuštění skriptu VMAccess pomocí:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Zkontrolujte nebo opravte disku
Použitím VMAccess můžete také zkontrolujte a opravte disk, který jste přidali do virtuálního počítače s Linuxem.

Zkontrolujte a pak Opravte disk, vytvořte soubor s názvem `disk_check_repair.json` a přidat nastavení v následujícím formátu. Nahraďte vlastní hodnotu pro název `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Další postup
Aktualizace Linux pomocí rozšíření VMAccess Azure je jedna z metod k provádění změn v spuštěného virtuálního počítače s Linuxem. Nástroje, například cloudové init a šablony Azure Resource Manager můžete také upravit virtuálním počítačům s Linuxem na spuštění.

[Rozšíření virtuálního počítače a funkce pro Linux](extensions-features.md)

[Vytváření šablon Azure Resource Manager pomocí rozšíření virtuálního počítače s Linuxem](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí init cloudu](using-cloud-init.md)

