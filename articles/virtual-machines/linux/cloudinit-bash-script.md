---
title: Použití cloud-initu ke spuštění skriptu bash ve virtuálním počítači SIO v Azure
description: Jak používat cloud-init ke spuštění bash skriptu v virtuálním počítači SN Linuxu při vytváření pomocí Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969218"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Použití cloud-initu ke spuštění skriptu bash ve virtuálním počítači SIO v Azure
Tento článek ukazuje, jak pomocí [cloud-init](https://cloudinit.readthedocs.io) spustit existující bash skript na virtuálním počítači (VM) Linux nebo škálovací sady virtuálních strojů (VMSS) v době zřizování v Azure. Tyto skripty cloud-init se spustí při prvním spuštění, jakmile jsou prostředky zřízeny Azure. Další informace o tom, jak cloud-init funguje nativně v Azure a podporované distribuce Linuxu, najdete [v tématu cloud-init přehled](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Spuštění bash skript s cloud-init
S cloud-init nemusíte převádět stávající skripty do cloud-config, cloud-init přijímá více vstupních typů, z nichž jeden je bash skript.

Pokud jste ke spuštění skriptů používali rozšíření Azure pro vlastní skripty linuxu, můžete je migrovat a používat cloud-init. Rozšíření Azure však mají integrované vytváření sestav pro upozornění na selhání skriptu, nasazení image cloud-init neselže, pokud se nezdaří skript.

Chcete-li zobrazit tuto funkci v akci, vytvořte jednoduchý skript bash pro testování. Stejně jako soubor `#cloud-config` cloud-init, tento skript musí být místní, kde budete spouštět příkazy AzureCLI pro zřízení virtuálního počítače.  V tomto příkladu vytvořte soubor v prostředí Cloud Shell není v místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor simple_bash.sh` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1, abyste použili **editor nano.** Ujistěte se, že celý soubor cloud-init je zkopírován správně, zejména první řádek.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Před nasazením této bitové kopie je třeba vytvořit skupinu prostředků pomocí příkazu [az group create.](/cli/azure/group) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte virtuální virtuální ho s [az vm](/cli/azure/vm) `--custom-data simple_bash.sh` vytvořit a zadejte bash skript soubor u následovně:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Ověření spuštění skriptu bash
SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazené ve výstupu z předchozího příkazu. Zadejte svůj vlastní **publicIpAddress** takto:

```bash
ssh <publicIpAddress>
```

Změňte na adresář **/tmp** a ověřte, zda soubor myScript.txt existuje a zda je uvnitř příslušný text.  Pokud tomu tak není, můžete zkontrolovat **/var/log/cloud-init.log** další podrobnosti.  Vyhledejte následující položku:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Další kroky
Další příklady změn konfigurace cloud-init najdete v následujících tématech:
 
- [Přidání dalšího uživatele Linuxu do virtuálního počítače](cloudinit-add-user.md)
- [Spuštění správce balíčků pro aktualizaci existujících balíčků při prvním spuštění](cloudinit-update-vm.md)
- [Změna názvu místního hostitele virtuálního_kmontíva](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vložení klíčů](tutorial-automate-vm-deployment.md)
