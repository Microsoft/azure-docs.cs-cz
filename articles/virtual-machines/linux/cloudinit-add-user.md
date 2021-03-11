---
title: Použití Cloud-init k přidání uživatele do virtuálního počítače se systémem Linux v Azure
description: Jak pomocí Cloud-init přidat uživatele k virtuálnímu počítači se systémem Linux během vytváření pomocí Azure CLI
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2c459965f2eb29a469ac90fdeb42107d1dbcf86a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559405"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Použití Cloud-init k přidání uživatele do virtuálního počítače se systémem Linux v Azure
V tomto článku se dozvíte, jak pomocí [Cloud-init](https://cloudinit.readthedocs.io) přidat uživatele na virtuální počítač (VM) nebo Virtual Machine Scale Sets (VMSS) v době zřizování v Azure. Tento skript Cloud-init se po prvním spuštění spustí, jakmile se prostředky zřídí v Azure. Další informace o nativním fungování Cloud-init v Azure a podporovaných distribuce pro Linux najdete v tématu [Přehled Cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Přidání uživatele k virtuálnímu počítači pomocí Cloud-init
Jedním z prvních úkolů na jakémkoli novém virtuálním počítači se systémem Linux je přidání dalšího uživatele, aby se předešlo používání *kořenového adresáře*. Klíče SSH jsou osvědčené postupy pro zabezpečení a použitelnost. Klíče se přidají do souboru *~/.ssh/authorized_keys* pomocí tohoto skriptu Cloud-init.

Pokud chcete přidat uživatele k virtuálnímu počítači se systémem Linux, vytvořte v aktuálním prostředí soubor s názvem *cloud_init_add_user.txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_add_user.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Přesvědčte se, zda je celý soubor Cloud-init zkopírován správně, zejména první řádek.  Pro zjednodušení příkladu je třeba zadat vlastní veřejný klíč (například obsah *~/.ssh/id_rsa. pub*) pro hodnotu `ssh-authorized-keys:` – tady se zkrátila.

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
> Soubor #cloud-config zahrnuje `- default` zahrnutý parametr. Tím se uživatel připojí k existujícímu administrativnímu uživateli vytvořenému během zřizování. Pokud vytvoříte uživatele bez `- default` parametru – bude přepsán automaticky generovaný uživatel s rolí správce vytvořený platformou Azure. 

Před nasazením této image je potřeba vytvořit skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) a zadejte soubor Cloud-init následujícím `--custom-data cloud_init_add_user.txt` způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazeného ve výstupu z předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Pokud chcete potvrdit, že se uživatel přidal do virtuálního počítače a zadaných skupin, zobrazte obsah souboru */etc/Group* následujícím způsobem:

```bash
cat /etc/group
```

Následující příklad výstupu ukazuje, že uživatel z *cloud_init_add_user.txt* soubor byl přidán do virtuálního počítače a do příslušné skupiny:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Další kroky
Další příklady cloudových inicializací změn konfigurace najdete v následujících tématech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
