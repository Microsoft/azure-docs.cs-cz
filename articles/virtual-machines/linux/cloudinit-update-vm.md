---
title: Použití cloud-initu v virtuálním počítači s Linuxem v Azure
description: Jak používat cloud-init k aktualizaci a instalaci balíčků ve virtuálním počítači SN Linuxu během vytváření pomocí azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969145"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Aktualizace a instalace balíčků ve virtuálním počítači s Linuxem v Azure pomocí cloud-initu
Tento článek ukazuje, jak používat [cloud-init](https://cloudinit.readthedocs.io) k aktualizaci balíčků na virtuálním počítači (VM) linuxového virtuálního počítače nebo škálovacísady virtuálních strojů v době zřizování v Azure. Tyto skripty cloud-init se spustí při prvním spuštění, jakmile jsou prostředky zřízeny Azure. Další informace o tom, jak cloud-init funguje nativně v Azure a podporované distribuce Linuxu, najdete [v tématu cloud-init přehled](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Aktualizace virtuálního virtuálního připojení pomocí cloud-initu
Z bezpečnostních důvodů můžete nakonfigurovat virtuální počítač tak, aby při prvním spuštění použil nejnovější aktualizace. Jako cloud-init funguje napříč různými distribucemi Linuxu, není třeba specifikovat `apt` nebo `yum` pro správce balíčků. Místo toho `package_upgrade` definujete a nechte proces cloud-init určit vhodný mechanismus pro distro, který se používá. Tento pracovní postup umožňuje používat stejné skripty cloud-init napříč distribucemi.

Chcete-li zobrazit proces upgradu v akci, vytvořte soubor v aktuálním prostředí s názvem *cloud_init_upgrade.txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v prostředí Cloud Shell není v místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_upgrade.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1, abyste použili **editor nano.** Ujistěte se, že celý soubor cloud-init je zkopírován správně, zejména první řádek.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Před nasazením této bitové kopie je třeba vytvořit skupinu prostředků pomocí příkazu [az group create.](/cli/azure/group) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální hosti s [vytvořením virtuálního virtuálního virtuálního připojení a](/cli/azure/vm) zadejte soubor `--custom-data cloud_init_upgrade.txt` cloud-init takto:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozího příkazu. Zadejte svůj vlastní **publicIpAddress** takto:

```bash
ssh <publicIpAddress>
```

Spusťte nástroj pro správu balíčků a zkontrolujte aktualizace.

```bash
sudo yum update
```

Jako cloud-init kontrolovány a nainstalované aktualizace při startu, by měly být žádné další aktualizace použít.  Zobrazí se proces aktualizace, počet změněných balíčků, `httpd` stejně `yum history` jako instalace spuštěním a zkontrolujte výstup podobný tomu níže.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Další kroky
Další příklady změn konfigurace cloud-init najdete v následujících tématech:
 
- [Přidání dalšího uživatele Linuxu do virtuálního počítače](cloudinit-add-user.md)
- [Spuštění správce balíčků pro aktualizaci existujících balíčků při prvním spuštění](cloudinit-update-vm.md)
- [Změna názvu místního hostitele virtuálního_kmontíva](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vložení klíčů](tutorial-automate-vm-deployment.md)
