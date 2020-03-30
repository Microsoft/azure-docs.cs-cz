---
title: Oprava virtuálního počítače s Linuxem pomocí příkazů pro opravu virtuálního počítače Azure | Dokumenty společnosti Microsoft
description: Tento článek podrobně popisuje, jak pomocí příkazů opravy virtuálního počítače Azure k připojení disku k jinému virtuálnímu počítači Linuxu opravit všechny chyby a pak znovu vytvořit původní virtuální počítač.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 49fdfde402938ce8d0ee1b141a47e68c99c502e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796202"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Oprava virtuálního počítače s Linuxem pomocí příkazů pro opravu virtuálních počítačů Azure

Pokud váš virtuální počítač (VM) linuxv Azure narazí na chybu při spuštění nebo disku, budete muset provést zmírnění na samotném disku. Běžným příkladem by mohla být neúspěšná aktualizace aplikace, která brání úspěšnému spuštění virtuálního virtuálního movitého virtuálního movitého virtuálního softwaru. Tento článek podrobně popisuje, jak pomocí příkazů opravy virtuálního počítače Azure k připojení disku k jinému virtuálnímu počítači Linuxu opravit všechny chyby a pak znovu vytvořit původní virtuální počítač.

> [!IMPORTANT]
> Skripty v tomto článku platí jenom pro virtuální počítače, které používají [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Přehled procesu opravy

Teď můžete použít příkazy opravy virtuálního počítače Azure změnit disk operačního systému pro virtuální počítač a už nemusíte odstraňovat a znovu vytvořit virtuální počítač.

Chcete-li vyřešit problém s virtuálním ms, postupujte takto:

1. Spuštění služby Azure Cloud Shell
2. Spustit přidání/aktualizaci rozšíření az
3. Spuštění vytvoření opravy vm az
4. Provedení kroků ke zmírnění rizik
5. Spuštění obnovení opravy vm az

Další dokumentaci a pokyny naleznete [v tématu az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Příklad procesu opravy

1. Spuštění služby Azure Cloud Shell

   Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Obsahuje běžné nástroje Azure předinstalované a nakonfigurované pro použití s vaším účtem.

   Chcete-li otevřít prostředí cloudu, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete otevřít také na samostatné [https://shell.azure.com](https://shell.azure.com)kartě prohlížeče tak, že přejdete na .

   Vyberte **Kopírovat,** chcete-li zkopírovat bloky kódu, vložte kód do prostředí Cloud A vyberte **Enter,** abyste ho spouštěli.

   Pokud dáváte přednost místní instalaci a používání rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu ``az --version``. Pokud potřebujete nainstalovat nebo upgradovat své nastavení příkazového příkazu k nastavení Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Pokud je to poprvé, `az vm repair` co jste použili příkazy, přidejte rozšíření příkazového příkazu k opravě virtuálního času.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Pokud jste dříve `az vm repair` používali příkazy, použijte všechny aktualizace rozšíření o opravu virtuálního vav.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Spusťte `az vm repair create`. Tento příkaz vytvoří kopii disku operačního systému pro nefunkční virtuální počítače, vytvoří virtuální ho virtuálního počítače pro opravu a připojí disk.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Proveďte všechny potřebné kroky zmírnění na vytvořené opravy virtuálního počítači, pak přejděte ke kroku 5.

5. Spusťte `az vm repair restore`. Tento příkaz zamění opravený disk operačního systému s původním diskem operačního systému virtuálního počítače.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Ověření a povolení diagnostiky spouštění

Následující příklad umožňuje diagnostické rozšíření na ``myVMDeployed`` virtuálním počítači ``myResourceGroup``pojmenované ve skupině prostředků s názvem :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Další kroky

* Pokud máte problémy s připojením k virtuálnímu počítači, [přečtěte si článek Poradce při potížích s připojením RDP k virtuálnímu počítači Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete [v tématu Řešení problémů s připojením aplikací na virtuálních počítačích v Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Další informace o používání Správce prostředků najdete v [tématu Přehled Správce prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
