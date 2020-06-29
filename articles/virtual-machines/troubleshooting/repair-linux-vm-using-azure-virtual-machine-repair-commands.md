---
title: Oprava virtuálního počítače se systémem Linux pomocí příkazů pro opravu virtuálního počítače Azure | Microsoft Docs
description: Tento článek podrobně popisuje, jak pomocí příkazů pro opravu virtuálních počítačů Azure připojit disk k jinému virtuálnímu počítači se systémem Linux a opravit případné chyby a pak znovu sestavit původní virtuální počítač.
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
ms.openlocfilehash: a7357ef3b0151096746e37bddd235f0db53baed7
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85444807"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Oprava virtuálního počítače s Linuxem pomocí příkazů pro opravu virtuálních počítačů Azure

Pokud váš virtuální počítač se systémem Linux v Azure najde chybu při spuštění nebo disku, může být nutné provést zmírnění na samotném disku. Běžným příkladem může být neúspěšná aktualizace aplikace, která brání úspěšnému spuštění virtuálního počítače. Tento článek podrobně popisuje, jak pomocí příkazů pro opravu virtuálních počítačů Azure připojit disk k jinému virtuálnímu počítači se systémem Linux a opravit případné chyby a pak znovu sestavit původní virtuální počítač.

> [!IMPORTANT]
> * Skripty v tomto článku se vztahují pouze na virtuální počítače, které používají [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
> * Ke spuštění skriptu se vyžaduje odchozí připojení z virtuálního počítače (port 443).
> * V jednom okamžiku může běžet jenom jeden skript.
> * Běžící skript nelze zrušit.
> * Maximální doba, kterou může skript běžet, je 90 minut, po jejichž uplynutí vyprší časový limit.
> * U virtuálních počítačů, které používají Azure Disk Encryption, se podporují jenom spravované disky šifrované pomocí šifrování s jedním průchozím řetězcem (s KEK nebo bez nich).

## <a name="repair-process-overview"></a>Přehled procesu opravy

Pomocí příkazů pro opravu virtuálního počítače Azure teď můžete změnit disk s operačním systémem pro virtuální počítač a už nebudete muset virtuální počítač odstranit a znovu vytvořit.

Pomocí těchto kroků můžete vyřešit potíže s virtuálním počítačem:

1. Spuštění služby Azure Cloud Shell
2. Spuštění AZ Extension Add/Update
3. Spuštění AZ VM opravit Create
4. Provést kroky pro zmírnění rizika
5. Spuštění AZ VM opravit Restore

Další dokumentaci a pokyny najdete v tématu [AZ VM Repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Příklad procesu opravy

1. Spuštění služby Azure Cloud Shell

   Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Zahrnuje běžné nástroje Azure, které jsou předinstalované a nakonfigurované pro použití s vaším účtem.

   Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v pravém horním rohu bloku kódu. Můžete také otevřít Cloud Shell na samostatné kartě prohlížeče, a to tak, že kliknete na [https://shell.azure.com](https://shell.azure.com) .

   Vyberte **Kopírovat** pro zkopírování bloků kódu, poté vložte kód do Cloud Shell a vyberte **ENTER** pro spuštění.

   Pokud dáváte přednost místní instalaci a používání rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu ``az --version``. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
   
   Pokud se potřebujete přihlásit k Cloud Shell s jiným účtem, než jste aktuálně přihlášení k webu Azure Portal, můžete použít ``az login`` [AZ Login reference](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login).  K přepínání mezi předplatnými přidruženými k vašemu účtu můžete použít ``az account set --subscription`` [odkaz AZ Account set reference](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set).

2. Pokud příkazy použijete poprvé `az vm repair` , přidejte rozšíření rozhraní příkazového řádku pro opravu virtuálního počítače.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Pokud jste už použili `az vm repair` příkazy, aplikujte všechny aktualizace na rozšíření VM-Repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Spusťte `az vm repair create`. Tento příkaz vytvoří kopii disku s operačním systémem pro virtuální počítač bez funkčního fungování, vytvoří opravný virtuální počítač v nové skupině prostředků a připojí kopii disku s operačním systémem.  Opravný virtuální počítač bude mít stejnou velikost a oblast jako zadaný virtuální počítač, který není funkční. Skupina prostředků a název virtuálního počítače použité ve všech krocích budou pro virtuální počítač bez funkčního fungování. Pokud váš virtuální počítač používá Azure Disk Encryption příkaz se pokusí odemknout zašifrovaný disk, aby byl přístupný při připojení k opravnému virtuálnímu počítači.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Na vytvořeném virtuálním počítači opravy proveďte všechny nezbytné kroky zmírnění a pak pokračujte krokem 5.

5. Spusťte `az vm repair restore`. Tento příkaz zahodí opravený disk s operačním systémem s původním diskem s operačním systémem virtuálního počítače. Zde použitá skupina prostředků a název virtuálního počítače jsou pro virtuální počítač, který není v kroku 3 použit.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Ověření a povolení diagnostiky spouštění

Následující příklad aktivuje diagnostické rozšíření na virtuálním počítači s názvem ``myVMDeployed`` ve skupině prostředků s názvem ``myResourceGroup`` :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Další kroky

* Pokud máte problémy s připojením k vašemu VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením RDP k virtuálnímu počítači Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálních počítačích v Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Další informace o použití Správce prostředků naleznete v tématu [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
