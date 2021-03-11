---
title: Použití Cloud-init ke konfiguraci odkládacího oddílu na virtuálním počítači se systémem Linux
description: Jak pomocí Cloud-init nakonfigurovat odkládací oddíl na virtuálním počítači Linux během vytváření pomocí Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: b9f4adc4e1e980db2af4fcc20b3a4492309c89f3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559371"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Použití Cloud-init ke konfiguraci odkládacího oddílu na virtuálním počítači se systémem Linux
V tomto článku se dozvíte, jak pomocí [Cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat odkládací oddíl u různých distribucí systému Linux. Odkládací oddíl byl tradičně nakonfigurovaný agentem pro Linux (WALA) na základě toho, která distribuce je vyžaduje.  Tento dokument vám pomůže sestavovat proces vytváření swapového oddílu na vyžádání během doby zřízení pomocí Cloud-init.  Další informace o tom, jak nativně funguje Cloud-init v Azure a podporované distribuce Linux, najdete v článku [Přehled Cloud-init](using-cloud-init.md) .

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Vytvoření odkládacího oddílu pro image založené na Ubuntu
Ve výchozím nastavení v Azure Image Galerie Ubuntu nevytváří swapové oddíly. Povolení konfigurace prohozeného oddílu během zřizování virtuálního počítače pomocí Cloud-init – Podívejte se na [AzureSwapPartitions dokument](https://wiki.ubuntu.com/AzureSwapPartitions) na wikiwebu Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Vytvoření odkládacího oddílu pro image založené na Red Hat a CentOS

V aktuálním prostředí vytvořte soubor s názvem *cloud_init_swappart.txt* a vložte následující konfiguraci. V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud_init_swappart.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Přesvědčte se, zda je celý soubor Cloud-init zkopírován správně, zejména první řádek.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Před nasazením této image je potřeba vytvořit skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nyní vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) a zadejte soubor Cloud-init následujícím `--custom-data cloud_init_swappart.txt` způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Ověřit vytvoření odkládacího oddílu
SSH na veřejnou IP adresu vašeho virtuálního počítače zobrazeného ve výstupu z předchozího příkazu. Zadejte vlastní **publicIpAddress** následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Až se SSH'ed do virtuálního počítače, podívejte se, jestli se odkládací oddíl vytvořil.

```bash
swapon -s
```

Výstup z tohoto příkazu by měl vypadat takto:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Máte-li existující bitovou kopii Azure s nakonfigurovaným odkládacím oddílem a chcete změnit konfiguraci odkládacího oddílu pro nové image, odeberte existující odkládací oddíl. Další podrobnosti najdete v dokumentu "přizpůsobení imagí pro zřízení pomocí Cloud-init".

## <a name="next-steps"></a>Další kroky
Další příklady cloudových inicializací změn konfigurace najdete v následujících tématech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
