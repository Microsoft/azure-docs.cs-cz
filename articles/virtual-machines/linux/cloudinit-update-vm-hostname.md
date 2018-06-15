---
title: Můžete nastavit název hostitele pro virtuální počítač s Linuxem v Azure pomocí cloudu init | Microsoft Docs
description: Jak používat cloudové init k přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí Azure CLI 2.0
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
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: a858a12ec81db7ae1c0a7b7cfea06fa2abdcdcc6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124020"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Můžete nastavit název hostitele pro virtuální počítač s Linuxem v Azure pomocí init cloudu
V tomto článku se dozvíte, jak používat [cloudu init](https://cloudinit.readthedocs.io) ke konfiguraci konkrétní název hostitele na virtuální počítač (VM) nebo virtuální počítač sadách škálování (VMSS) na zřizování čas v Azure. Tyto skripty cloudu init spustit při prvním spuštění počítače po prostředky se zřizují Azure. Další informace o cloudu init fungování nativně ve službě Azure a podporovaných distribucích systému Linux najdete v tématu [init cloudu – přehled](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Nastavte název hostitele s inicializací cloudu
Ve výchozím nastavení je název hostitele stejný jako název virtuálního počítače při vytváření nového virtuálního počítače v Azure.  Spuštění skriptu init cloudu – Chcete-li změnit tento výchozí název hostitele, při vytváření virtuálního počítače v Azure pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create), zadejte soubor init cloudu s `--custom-data` přepínače.  

Informace o procesu upgradu v akci, vytvořte soubor ve své aktuální prostředí s názvem *cloud_init_hostname.txt* a vložte následující konfigurace. V tomto příkladu vytvoření souboru v prostředí cloudu není na místním počítači. Můžete použít libovolný editor, které chcete. Zadáním příkazu `sensible-editor cloud_init_hostname.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 používat **nano** editor. Ujistěte se, že je soubor celou cloudu init zkopírován správně, obzvláště první řádek.  

```yaml
#cloud-config
hostname: myhostname
```

Než nasadíte tuto bitovou kopii, je nutné vytvořit skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create) příkaz. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte virtuální počítač s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) a určete soubor init cloudu s `--custom-data cloud_init_hostname.txt` následujícím způsobem:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Po vytvoření rozhraní příkazového řádku Azure obsahuje informace o virtuálním počítači. Použití `publicIpAddress` k SSH k virtuálnímu počítači. Zadejte vlastní adresu následujícím způsobem:

```bash
ssh <publicIpAddress>
```

Chcete-li zobrazit název virtuálního počítače, použijte `hostname` příkaz takto:

```bash
hostname
```

Virtuální počítač by měl sestav název hostitele jako tuto hodnotu nastavit v cloudu init souboru, jak ukazuje následující příklad výstupu:

```bash
myhostname
```

## <a name="next-steps"></a>Další postup
Mezi další cloudu init změny konfigurace naleznete v následujících tématech:
 
- [Přidání další uživatele Linux do virtuálního počítače](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky na při prvním spuštění](cloudinit-update-vm.md)
- [Změňte název místního hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfigurační soubory a vložit klíče](tutorial-automate-vm-deployment.md)