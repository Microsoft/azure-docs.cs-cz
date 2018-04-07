---
title: Obnovte přístup na virtuálních počítačích Azure Linux pomocí rozšíření VMAccess | Microsoft Docs
description: Obnovte přístup na virtuálních počítačích Azure Linux pomocí rozšíření VMAccess.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 5fb130fc2e448f3cbc648991ea6bebd5795bc78b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Spravovat uživatele, SSH a zkontrolujte nebo opravte disky na virtuálních počítačích Azure Linux rozšíření VMAccess pomocí Azure CLI 1.0
Tento článek ukazuje, jak používat rozšíření VMAcesss Azure zkontrolujte nebo opravit disk, obnovte přístup uživatele, Správa uživatelských účtů nebo obnovit konfiguraci SSHD v systému Linux. Tento článek vyžaduje:

* účet Azure ([získejte bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/))
* [rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) s přihlášením `azure login`.
* Rozhraní příkazového řádku Azure *musí být v* režimu Azure Resource Manager`azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands)– naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy
Existují dva způsoby, jak používat VMAccess na virtuální počítače Linux:

* Pomocí Azure CLI 1.0 a požadované parametry.
* Pomocí nezpracované soubory JSON, které VMAccess procesy a potom na.

Pro oddíl rychlý příkaz přidáme 1.0 rozhraní příkazového řádku Azure používat `azure vm reset-access` metoda. V následujících příkladech nahraďte hodnoty, které obsahují "Příklad" s hodnotami ze svého vlastního prostředí.

## <a name="create-a-resource-group-and-linux-vm"></a>Vytvoření skupiny prostředků a virtuální počítač s Linuxem
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Vytvoření Debian virtuálního počítače
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Resetování hesla kořenového
Resetování hesla kořenového:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Obnovení klíče SSH
Chcete-li obnovit klíč SSH nekořenovými uživatele:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Vytvořit uživatele
Chcete-li vytvořit uživateli:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Odebrání uživatele
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Resetování SSHD
Chcete-li obnovit konfiguraci SSHD:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Podrobný postup
### <a name="vmaccess-defined"></a>VMAccess definované:
Disk ve virtuálním počítačům s Linuxem se zobrazuje chyby. Nějakým způsobem resetování hesla kořenového virtuálním počítačům s Linuxem nebo omylem odstraněné svůj privátní klíč SSH. V takovém případě zpět v dny v datovém centru potřebovali byste existuje jednotky a pak otevřete KVM získat z konzoly serveru. Rozšíření Azure VMAccess si můžete představit jako že KVM přepínačů, která umožňuje přístup ke konzole resetovat přístup do systému Linux nebo provést údržbu na úrovni disku.

Podrobný postup přidáme dlouho tvar VMAccess, který používá nezpracované soubory JSON.  Tyto soubory VMAccess JSON je možné také volat z šablony Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Použitím VMAccess zkontrolujte nebo opravte disku virtuálního počítače s Linuxem
Pomocí VMAccess můžete provést fsck spustit na disku v rámci vašeho virtuálního počítače s Linuxem.  Můžete také provést kontrolu disku a disku opravit pomocí VMAccess.

Kontrolovat, a pak opravte disku použijte tento skript VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Použitím VMAccess k resetování uživatelský přístup do systému Linux
Pokud jste ztratili přístup ke kořenové na virtuálním počítačům s Linuxem, můžete spustit skript VMAccess k resetování hesla root.

Pokud chcete resetovat hesla kořenového, použijte tento skript VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Pokud chcete resetovat klíč SSH nekořenovými uživatele, použijte tento skript VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Použití VMAccess ke správě uživatelských účtů v systému Linux
VMAccess je skript jazyka Python, který lze použít ke správě uživatelů na virtuálním počítačům s Linuxem bez přihlášení a pomocí příkazu "sudo" nebo kořenového účtu.

Chcete-li vytvořit uživatele, použijte tento skript VMAccess:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Chcete-li odstranit uživatele, použijte tento skript VMAccess:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Použití VMAccess k resetování konfigurace SSHD
Pokud provádět změny konfigurace SSHD virtuální počítače Linux a zavřete připojení SSH před ověřováním změny, může být zakázaný z SSH'ing zpět v.  VMAccess umožňuje obnovit konfiguraci SSHD známé platné konfigurace bez právě přihlášen prostřednictvím SSH.

Chcete-li obnovit konfiguraci SSHD můžete použít tento skript VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Spuštění skriptu VMAccess pomocí:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Další postup
Aktualizace Linux používá rozšíření VMAccess Azure je jedna z metod k provádění změn v spuštěného virtuálního počítače s Linuxem.  Nástroje, například cloudové init a šablon Azure můžete taky upravit virtuálním počítačům s Linuxem na spuštění.

[O rozšíření virtuálního počítače a funkce](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Vytváření šablon Azure Resource Manager pomocí rozšíření virtuálního počítače s Linuxem](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí init cloudu](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

