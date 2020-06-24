---
title: Konfigurace nastavení autostart pro virtuální počítač v Azure DevTest Labs | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení autostart pro virtuální počítače v testovacím prostředí. Toto nastavení umožňuje, aby se virtuální počítače v testovacím prostředí automaticky spustily podle plánu.
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896470"
---
# <a name="auto-startup-lab-virtual-machines"></a>Virtuální počítače pro automatické spuštění laboratoře  
Azure DevTest Labs umožňuje nakonfigurovat virtuální počítače v testovacím prostředí tak, aby se automaticky spustily a vypnuly na základě plánu. Informace o konfiguraci nastavení pro automatické vypnutí najdete [v tématu Správa zásad pro automatické vypínání pro testovací prostředí v Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Na rozdíl od automatické vypnutí, kde jsou všechny virtuální počítače zahrnuté při zapnutí zásady, zásada automatické spuštění vyžaduje, aby uživatel testovacího prostředí explicitně vybral virtuální počítač a přihlášený k tomuto plánu. Tímto způsobem se nebudete moct snadno spouštět do situace, kdy se nechtěné automatické spouštění nežádoucích virtuálních počítačů a způsobují neočekávané útraty.

V tomto článku se dozvíte, jak nakonfigurovat zásady pro automatické spouštění pro testovací prostředí.

## <a name="configure-autostart-settings-for-a-lab"></a>Konfigurace nastavení autostart pro testovací prostředí 
1. Přejděte na domovskou stránku vašeho testovacího prostředí. 
2. V nabídce vlevo vyberte **Konfigurace a zásady** . 

    ![Nabídka konfigurace a zásady](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na stránce **Konfigurace a zásady** proveďte následující kroky:
    
    1. Vyberte **zapnuto** , pokud chcete, aby se **virtuálním počítačům naplánovalo automatické spuštění funkce automatického** spuštění pro toto testovací prostředí. 
    2. Vyberte počáteční čas (například: 8:00:00 dop.) pro pole **plán zahájení** . 
    3. Vyberte **časové pásmo** , které se má použít. 
    4. Vyberte **dny v týdnu** , ve kterých se musí automaticky spustit virtuální počítače. 
    5. Pak na panelu nástrojů vyberte **Uložit** a uložte nastavení. 

        ![Nastavení autostart](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Tato zásada automaticky nepoužije automatické spuštění na všechny virtuální počítače v testovacím prostředí. Pokud se chcete **Přihlásit** k jednotlivým virtuálním počítačům, klikněte na stránku virtuální počítač a povolte **automatické spuštění** tohoto virtuálního počítače.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Povolit automatické spuštění pro virtuální počítač v testovacím prostředí
Následující postup vám poskytne postup, jak na virtuálním počítači začínat zásady pro automatické spuštění testovacího prostředí. 

1. Na domovské stránce testovacího prostředí vyberte **virtuální počítač** v seznamu **moje virtuální počítače** . 

    ![Nabídka konfigurace a zásady](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na stránce **virtuální počítač** vyberte v nabídce vlevo nebo v seznamu **plány** možnost **automatické spuštění** . 

    ![Výběr nabídky pro automatické spuštění](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na stránce **autostart** vyberte **zapnuto** pro možnost pro **Naplánování automatického spuštění tohoto virtuálního počítače** .

    ![Povolit automatické spuštění pro virtuální počítač](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Pak na panelu nástrojů vyberte **Uložit** a uložte nastavení. 


## <a name="next-steps"></a>Další kroky
Další informace o zásadách pro automatické vypnutí konfigurace pro testovací prostředí najdete [v tématu Správa zásad pro automatické vypínání v testovacím prostředí v Azure DevTest Labs](devtest-lab-auto-shutdown.md)
