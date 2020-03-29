---
title: Přidání uživatele do virtuálního počítače s Linuxem v Azure pomocí cloud-initu
description: Jak pomocí cloud-initu přidat uživatele do virtuálního počítače s Linuxem během vytváření pomocí rozhraní příkazového příkazu Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: f1782bfe0c14e3b44703f89ec7f78590c1bb74c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969238"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Přidání uživatele do virtuálního počítače s Linuxem v Azure pomocí cloud-initu
Tento článek ukazuje, jak pomocí [cloud-init](https://cloudinit.readthedocs.io) přidat uživatele na virtuálním počítači (VM) nebo škálovací sady virtuálních strojů (VMSS) v době zřizování v Azure. Tento skript cloud-init se spustí při prvním spuštění, jakmile jsou prostředky zřízeny Azure. Další informace o tom, jak cloud-init funguje nativně v Azure a podporované distribuce Linuxu, najdete [v tématu přehled cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Přidání uživatele k virtuálnímu virtuálnímu virtuálnímu mněmu s cloud-init
Jedním z prvních úkolů na každém novém virtuálním počítači s Linuxem je přidat dalšího uživatele pro sebe, abyste se vyhnuli použití *kořenového adresáře*. SSH klíče jsou osvědčeným postupem pro zabezpečení a použitelnost. Klíče jsou přidány do souboru *~/.ssh/authorized_keys* pomocí tohoto skriptu cloud-init.

Chcete-li přidat uživatele do virtuálního počítače s Linuxem, vytvořte soubor v aktuálním prostředí s názvem *cloud_init_add_user.txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v prostředí Cloud Shell není v místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_add_user.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1, abyste použili **editor nano.** Ujistěte se, že celý soubor cloud-init je zkopírován správně, zejména první řádek.  Musíte zadat svůj vlastní veřejný klíč (například obsah *~/.ssh/id_rsa.pub)* pro hodnotu `ssh-authorized-keys:` - zde byla zkrácena, aby se zjednodušil příklad.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Soubor #cloud-config obsahuje `- default` zahrnutý parametr. To připojí uživatele k existujícímu uživateli správce vytvořenému během zřizování. Pokud vytvoříte uživatele `- default` bez parametru – automaticky generovaný uživatel správce vytvořený platformou Azure by byl přepsán. 

Před nasazením této bitové kopie je třeba vytvořit skupinu prostředků pomocí příkazu [az group create.](/cli/azure/group) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální hosti s [vytvořením virtuálního virtuálního virtuálního připojení a](/cli/azure/vm) zadejte soubor `--custom-data cloud_init_add_user.txt` cloud-init takto:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozího příkazu. Zadejte svůj vlastní **publicIpAddress** takto:

```bash
ssh <publicIpAddress>
```

Chcete-li potvrdit, že váš uživatel byl přidán do virtuálního počítače a zadané skupiny, zobrazte obsah souboru */etc/group* následujícím způsobem:

```bash
cat /etc/group
```

Následující ukázkový výstup ukazuje, že uživatel ze souboru *cloud_init_add_user.txt* byl přidán do virtuálního virtuálního uživatele a příslušné skupiny:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Další kroky
Další příklady změn konfigurace cloud-init najdete v následujících tématech:
 
- [Přidání dalšího uživatele Linuxu do virtuálního počítače](cloudinit-add-user.md)
- [Spuštění správce balíčků pro aktualizaci existujících balíčků při prvním spuštění](cloudinit-update-vm.md)
- [Změna názvu místního hostitele virtuálního_kmontíva](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vložení klíčů](tutorial-automate-vm-deployment.md)
