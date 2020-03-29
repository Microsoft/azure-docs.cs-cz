---
title: Jak otestovat aplikaci v Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit sdílenou složku v testovacím prostředí a připojit ji k místnímu počítači a virtuálnímu počítači v testovacím prostředí a pak nasadit desktopové nebo webové aplikace do sdílené složky a otestovat je.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65872390"
---
# <a name="test-your-app-in-azure"></a>Testování aplikace v Azure 
Tento článek obsahuje postup testování aplikace v Azure pomocí DevTest Labs. Nejprve nastavíte sdílenou složku v rámci testovacího prostředí a připojíte ji jako jednotku na místním vývojovém počítači a virtuální ms uvnitř testovacího prostředí. Potom použijete Visual Studio 2019 k nasazení aplikace do sdílené složky, abyste ji mohli spustit na virtuálním počítači v testovacím prostředí.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky 
1. [Vytvořte předplatné Azure,](https://azure.microsoft.com/free/) pokud ho ještě nemáte, a přihlaste se na [Portál Azure](https://portal.azure.com).
2. Podle pokynů v [tomto článku](devtest-lab-create-lab.md) vytvořte testovací prostředí pomocí Azure DevTest Labs. Připněte testovací prostředí na řídicí panel, abyste ho při příštím přihlášení snadno našli. Azure DevTest Labs umožňuje rychle vytvářet prostředky v rámci Azure minimalizací plýtvání a řízením nákladů. Další informace o devTest Labs najdete v [tématu přehled](devtest-lab-overview.md). 
3. Vytvořte účet Úložiště Azure ve skupině prostředků testovacího prostředí podle pokynů v článku [Vytvořit účet úložiště.](../storage/common/storage-create-storage-account.md) Na stránce **Vytvořit účet úložiště** vyberte Použít **existující** pro **skupinu prostředků**a vyberte **skupinu prostředků testovacího prostředí**. 
4. Vytvořte sdílenou složku ve svém úložišti Azure podle pokynů v článku [Vytvořit sdílenou složku v](../storage/files/storage-how-to-create-file-share.md) článku Soubory Azure. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Připojení sdílené složky v místním počítači
1. V místním počítači použijte skript z [trvalého sdílení souborů Azure pověření v](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) části Windows použití sdílené [složky Azure s Windows](../storage/files/storage-how-to-use-files-windows.md) článku. 
2. Potom pomocí `net use` příkazu připojte sdílenou složku do počítače. Tady je ukázkový příkaz: Před spuštěním příkazu zadejte název úložiště Azure a název sdílené složky. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Vytvoření virtuálního počítače v testovacím prostředí
1. Na stránce **Sdílení souborů** vyberte **skupinu prostředků** v nabídce s popisem cesty nahoře. Zobrazí se stránka **Skupina prostředků.** 
    
    ![Vybrat skupinu prostředků z nabídky s popisem cesty](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na stránce **Skupina prostředků** vyberte **testovací prostředí,** které jste vytvořili v devTest Labs.

    ![Výběr testovacího prostředí](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na stránce **DevTest Lab** pro testovací prostředí vyberte **+ Přidat** na panelu nástrojů. 

    ![Tlačítko Přidat pro testovací prostředí](media/test-app-in-azure/add-button-in-lab.png)
4. Na stránce **Zvolit základní stránku** vyhledejte **smalldisk**a vyberte **[smalldisk] Windows Server 2016 Data Center**. 

    ![Volba malého disku windows server](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na stránce **Virtuální počítač** zadejte **název virtuálního počítače**, uživatelské **jméno**, **heslo**a vyberte **Vytvořit**.    
    
    ![Vytvoření stránky virtuálního počítače](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Připojení sdílené složky na virtuálním počítači
1. Po úspěšném vytvoření virtuálního počítače vyberte **virtuální počítač** ze seznamu.    

    ![Výběr virtuálního virtuálního soudu testovacího prostředí](media/test-app-in-azure/select-lab-vm.png)
2. Vyberte **Připojit** na panelu nástrojů pro připojení k virtuálnímu počítači. 
3. [Nainstalujte Azure PowerShell](/powershell/azure/install-az-ps).
4. Postupujte podle pokynů v části Připojit sdílenou složku. 

## <a name="publish-your-app-from-visual-studio"></a>Publikování aplikace z Visual Studia
V této části publikujete aplikaci z Visual Studia do testovacího virtuálního počítače v cloudu.

1. Vytvořte desktopovou nebo webovou aplikaci pomocí Visual Studia 2019.
2. Sestavte aplikaci.
3. Pokud chcete aplikaci publikovat, klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**a vyberte **Publikovat**. 
4. V **Průvodci publikováním**zadejte **jednotku,** která je namapována na sdílenou složku.

    **Aplikace Pro stolní počítače:**

    ![Desktopová aplikace](media/test-app-in-azure/desktop-app.png)

    **Webová aplikace:**

    ![Webová aplikace](media/test-app-in-azure/web-app.png)

1. Chcete-li dokončit pracovní postup publikování, vyberte **možnost Další** a vyberte **dokončit**. Po dokončení kroků průvodce visual studio vytvoří aplikaci a publikuje ji do sdílené složky. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testování aplikace na testovacím virtuálním počítači v testovacím prostředí

1. Přejděte na stránku virtuálního počítače pro váš virtuální počítač v testovacím prostředí. 
2. Vyberte **Spustit** na panelu nástrojů a spusťte virtuální počítač, pokud je v zastaveném stavu. Můžete nastavit zásady automatického spuštění a automatického vypnutí pro váš virtuální počítač, abyste se pokaždé vyhnuli spuštění a zastavení. 
3. Vyberte **Connect** (Připojit).

    ![Stránka virtuálního počítače](media/test-app-in-azure/virtual-machine-page.png)
4. Ve virtuálním počítači spusťte **Průzkumníka souborů**a vyberte **Tento počítač,** abyste našli sdílenou složku.

    ![Najít sdílenou složku na virtuálním počítači](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Z jakéhokoli důvodu, pokud se vám nedaří najít sdílenou složku ve virtuálním počítači `net use` nebo v místním počítači, můžete ji znovu připojit spuštěním příkazu. Příkaz najdete `net use` v Průvodci **připojením** **sdílení souborů** na webu Azure Portal.
1. Otevřete sdílenou složku a potvrďte, že se vám zobrazí aplikace, kterou jste nasadili z Visual Studia. 

    ![Otevřít sdílenou složku na virtuálním počítači](media/test-app-in-azure/open-file-share.png)

    Teď můžete přistupovat a testovat svou aplikaci v testovacím virtuálním počítači, který jste vytvořili v Azure.

## <a name="next-steps"></a>Další kroky
V následujících článcích se dozvíte, jak používat virtuální mích v testovacím prostředí. 

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
- [Restartování virtuálního počítače testovacího prostředí](devtest-lab-restart-vm.md)
- [Změna velikosti virtuálního počítače testovacího prostředí](devtest-lab-resize-vm.md)
