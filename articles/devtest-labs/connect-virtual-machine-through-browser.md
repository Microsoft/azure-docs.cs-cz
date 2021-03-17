---
title: Připojení k virtuálním počítačům pomocí prohlížeče – Azure | Microsoft Docs
description: Přečtěte si, jak se připojit k virtuálním počítačům přes prohlížeč.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 352fc5fd3ff53a00d9f62966ecf21417ad898706
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288057"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Připojení k virtuálním počítačům přes prohlížeč 

DevTest Labs se integruje s [Azure bastionu](../bastion/index.yml), což vám umožní připojit se k virtuálním počítačům přes prohlížeč. Informace o tom, jak povolit tuto funkci v DevTest Labs, najdete v tématu [Povolení připojení prohlížeče na virtuálních počítačích testovacího prostředí](enable-browser-connection-lab-virtual-machines.md).

Po povolení *připojení prohlížeče* k virtuálním počítačům můžou uživatelé testovacího prostředí přistupovat přes prohlížeč.  

## <a name="create-a-lab-virtual-machine"></a>Vytvořit virtuální počítač testovacího prostředí

Nejdřív je potřeba vytvořit virtuální počítač testovacího prostředí ve virtuální síti s nakonfigurovaným bastionu. Vyberte druhou **podsíť** , kterou jste vytvořili, nikoli AzureBastionSubnet. Virtuální síť můžete vybrat během vytváření virtuálního počítače tak, že na kartě **Upřesnit nastavení** kliknete.

![Vytvoření virtuálního počítače](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Spustit virtuální počítač v prohlížeči

Po vytvoření virtuálního počítače ho můžete spustit v prohlížeči kliknutím na tlačítko *připojit k prohlížeči* a zadáním uživatelského jména a hesla pro tento počítač.  

![Spustit v prohlížeči](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Další kroky

[Přidání virtuálního počítače do testovacího prostředí v Azure DevTest Labs](devtest-lab-add-vm.md)
