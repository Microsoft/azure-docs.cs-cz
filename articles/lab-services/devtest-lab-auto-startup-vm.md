---
title: Konfigurace nastavení automatického spuštění virtuálního počítače v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat nastavení automatického spuštění pro virtuální počítače v testovacím prostředí. Toto nastavení umožňuje virtuální ms v testovacím prostředí automaticky spustit podle plánu.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807911"
---
# <a name="auto-startup-lab-virtual-machines"></a>Virtuální počítače testovacího prostředí pro automatické spuštění  
Azure DevTest Labs umožňuje nakonfigurovat virtuální počítače ve vašem testovacím prostředí tak, aby se automaticky sčítalo a vypínalo podle plánu. Informace o konfiguraci nastavení automatického vypínání najdete [v tématu Správa zásad automatického vypínání pro testovací prostředí v laboratořích Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Na rozdíl od automatického vypnutí, kde jsou zahrnuty všechny virtuální počítače, když je zásada zapnutá, zásady automatického spuštění vyžaduje, aby uživatel testovacího prostředí explicitně vybral virtuální počítač a přihlásil se k tomuto plánu. Tímto způsobem se snadno nedostanete do situace, kdy se nechtěné virtuální aplikace náhodně automaticky spustí a způsobí neočekávané výdaje.

Tento článek ukazuje, jak nakonfigurovat zásady automatického spuštění pro testovací prostředí.

## <a name="configure-autostart-settings-for-a-lab"></a>Konfigurace nastavení automatického spuštění testovacího prostředí 
1. Přejděte na domovskou stránku testovacího prostředí. 
2. V levé nabídce vyberte **Možnost Konfigurace a zásady.** 

    ![Nabídka Konfigurace a zásady](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na stránce **Konfigurace a zásady** postupujte takto:
    
    1. Vyberte **Zapnuto,** **chcete-li povolit** automatické spuštění virtuálních počítačů pro automatické spuštění, abyste povolili funkci automatického spuštění pro toto testovací prostředí. 
    2. Vpoli **Zahájení plánu** vyberte čas zahájení (například: 8:00:00). 
    3. Vyberte **časové pásmo,** které má být použito. 
    4. Vyberte **dny v týdnu,** kdy se mají virtuální počítačy automaticky spustit. 
    5. Potom vyberte **Uložit** na panelu nástrojů, abyste uložili nastavení. 

        ![Nastavení automatického spuštění](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Tato zásada automaticky nepoužije automatické spuštění pro všechny virtuální počítače v testovacím prostředí. Pokud se **chcete přihlásit k** jednotlivým virtuálním počítačům, přejděte na stránku virtuálního počítače a povolte **automatické spuštění** pro tento virtuální počítač.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Povolení automatického spuštění virtuálního virtuálního aplikace v testovacím prostředí
Následující postup poskytuje kroky pro přihlášení virtuálního virtuálního soudu do zásady automatického spuštění testovacího prostředí. 

1. Na domovské stránce testovacího prostředí vyberte **virtuální počítač** v seznamu Moje **virtuální počítače.** 

    ![Nabídka Konfigurace a zásady](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na stránce **Virtuální počítač** vyberte **automatické spuštění** v levé nabídce nebo v seznamu **Plány.** 

    ![Vybrat nabídku automatického startu](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na stránce **Automatické spuštění** vyberte **Zapnuto,** aby byla možnost **Povolit tomuto virtuálnímu počítači naplánováno na automatické spuštění.**

    ![Povolení automatického spuštění virtuálního virtuálního mísy](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Potom vyberte **Uložit** na panelu nástrojů, abyste nastavení uložili. 


## <a name="next-steps"></a>Další kroky
Další informace o zásadách automatického vypnutí konfigurace pro testovací prostředí najdete v tématu [Správa zásad automatického vypínání pro testovací prostředí v laboratořích Azure DevTest Labs.](devtest-lab-auto-shutdown.md)
