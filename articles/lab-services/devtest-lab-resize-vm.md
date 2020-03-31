---
title: Změna velikosti virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs
description: Zjistěte, jak změnit velikost virtuálního počítače (VM) v Azure DevTest Labs na základě vašich potřeb pro výkon procesoru, sítě nebo disku.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756173"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Změna velikosti virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs
Jednou z důležitých funkcí virtuálních počítačů Azure je, že umožňuje změnit velikost virtuálního počítače (VM) na základě vašich potřeb pro výkon procesoru, sítě nebo disku. Azure DevTest Labs podporuje tuto funkci pro virtuální počítače v testovacím prostředí. Funkce změny velikosti dodržuje zásady testovacího prostředí pro povolené velikosti virtuálních počítače v testovacím prostředí. To znamená, že můžete změnit velikost virtuálního počítače na povolené velikosti v testovacím prostředí. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Postup změny velikosti virtuálního počítače v testovacím prostředí 
Pokud chcete změnit velikost virtuálního počítače v testovacím prostředí v Azure DevTest Labs, postupujte takto: 

> [!NOTE]
> Pokud jste připojeni k virtuálnímu počítači prostřednictvím relace vzdálené plochy (RDP), uložte svou práci a odpojte se od virtuálního počítače před jeho změnami velikosti.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte testovací prostředí, které obsahuje virtuální hod, který chcete změnit velikost.  
4. V levém panelu vyberte **Moje virtuální počítače**. 
5. Ze seznamu virtuálních měn vyberte virtuální ho.
6. Pokud je virtuální počítač spuštěný, vyberte na panelu nástrojů možnost **Zastavit.** Zkontrolujte stav operace v okně **Oznámení.** Počkejte, až se virtuální jazyk zastaví, a zavřete okno **Oznámení.** 

    ![Zastavení virtuálního počítače](media/devtest-lab-resize-vm/stop-vm.png)
1. Na stránce Virtuální počítač pro virtuální počítač vyberte **velikost** v části **NASTAVENÍ** v levé nabídce.

    ![Nabídka Velikost](media/devtest-lab-resize-vm/size-menu.png)
1. V okně **Zvolit velikost** procházejte a vyberte velikost virtuálního počítače a klikněte na **Vybrat**.     
1. Zkontrolujte stav operace změny velikosti v okně **Oznámení.**

    ![Změna stavu velikosti](media/devtest-lab-resize-vm/resize-status.png)
10. Po úspěšné operaci změny velikosti zavřete okno **Oznámení.** 
11. V levé nabídce vyberte **Přehled** a na panelu nástrojů vyberte **Restartovat,** chcete-li virtuální počítač restartovat. 

## <a name="next-steps"></a>Další kroky
Podrobné informace o funkci změny velikosti podporované virtuálními počítači Azure najdete v [tématu Změna velikosti virtuálních počítačů](https://azure.microsoft.com/blog/resize-virtual-machines/).


