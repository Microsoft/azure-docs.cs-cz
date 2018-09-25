---
title: Použití cloud-init k aktualizaci a nainstalujte balíčky do virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Použití cloud-init k aktualizaci a nainstalujte balíčky do virtuálního počítače s Linuxem během vytváření pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: 84fab18d4e1f385f8770db52b18ac85151f48afd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988049"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Použití cloud-init k aktualizaci a nainstalujte balíčky do virtuálního počítače s Linuxem v Azure
V tomto článku se dozvíte, jak používat [cloud-init](https://cloudinit.readthedocs.io) aktualizovat balíčky v Linuxu virtuálních počítačů (VM) nebo virtuální počítač škálovací nastaví (VMSS) při zřizování času v Azure. Tyto skripty cloud-init spustit při prvním spuštění, jakmile se zřizují prostředky Azure. Další informace o tom, jak funguje cloud-init nativně v Azure a podporovaných distribucích systému Linux, najdete v části [přehled cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Aktualizovat virtuální počítač s nástrojem cloud-init
Z bezpečnostních důvodů můžete nakonfigurovat virtuální počítač, který při prvním spuštění instalace nejnovějších aktualizací. Jak funguje cloud-init v jiné distribuce Linuxu, není potřeba specifikovat `apt` nebo `yum` pro správce balíčků. Místo toho můžete definovat `package_upgrade` a umožní určit vhodný mechanismus pro distribuce používá proces cloud-init. Tento pracovní postup můžete použít stejné skripty cloud-init v distribucích.

Pokud chcete zobrazit procesu upgradu v akci, vytvořte soubor v aktuálním prostředí *cloud_init_upgrade.txt* a vložte do něj následující konfiguraci. V tomto příkladu vytvoření souboru ve službě Cloud Shell není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_upgrade.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 použít **nano** editoru. Ujistěte se, že se soubor celý cloud-init zkopíroval správně, zejména první řádek.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Před nasazením této bitové kopie, je potřeba vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az_group_create) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az_vm_create) a zadejte soubor cloud-init s `--custom-data cloud_init_upgrade.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Připojte přes SSH k veřejné IP adresu vašeho virtuálního počítače uvedené ve výstupu předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Spusťte nástroj pro správu balíčků a vyhledat aktualizace.

```bash
sudo yum update
```

Cloud-init zkontrolovat a nainstalovat aktualizace při spuštění, měla by existovat žádná další aktualizace použít.  Naleznete v tématu proces aktualizace, počet balíčků upravený, jakož i instalaci `httpd` spuštěním `yum history` a prohlédněte si výstup podobný následující.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Další postup
Příklady cloud-init další změny konfigurace najdete tady:
 
- [Přidání dalších uživatelů Linuxu na virtuální počítač](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky při prvním spuštění](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizovat konfigurační soubory a klíče pro vložení](tutorial-automate-vm-deployment.md)