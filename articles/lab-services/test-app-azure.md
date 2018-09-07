---
title: Testování aplikace v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit sdílenou složku v testovacím prostředí a připojit na místním počítači a virtuálního počítače v testovacím prostředí a pak nasaďte desktop nebo webové aplikace do sdílené složky a jejich testování.
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
ms.openlocfilehash: 099bdc25c27e264c3c7732243068307856840409
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028026"
---
# <a name="test-your-app-in-azure"></a>Testování aplikace v Azure 
Tento článek popisuje kroky pro testování vašich aplikací v Azure DevTest Labs pomocí. Nejprve nastavení sdílené složky v testovacím prostředí a připojit jako disk na svém místním vývojovém počítači a virtuálních počítačů v testovacím prostředí. Pak Nasaďte aplikaci do sdílené složky tak, aby aplikaci můžete spustit na virtuálním počítači v testovacím prostředí pomocí sady Visual Studio 2017.  

## <a name="prerequisites"></a>Požadavky 
1. [Předplatné Azure, vytvořte](https://azure.microsoft.com/free/) Pokud nemáte již účet máte a přihlaste se do [webu Azure portal](https://portal.azure.com).
2. Postupujte podle pokynů v [v tomto článku](devtest-lab-create-lab.md) k vytvoření testovacího prostředí pomocí Azure DevTest Labs. Připnete prostředí na řídicí panel tak, abyste mohli snadno najít ji při příštím přihlášení. Azure DevTest Labs umožňuje rychle vytvářet prostředky v rámci Azure tak, že plýtváním a kontrolou nad náklady. Další informace o službě DevTest Labs, naleznete v tématu [přehled](devtest-lab-overview.md). 
3. Vytvoření účtu služby Azure Storage ve skupině prostředků testovacího prostředí podle pokynů v [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md) článku. Na **vytvořit účet úložiště** stránce **použít existující** pro **skupiny prostředků**a vyberte **skupiny prostředků testovacího prostředí**. 
4. Vytvoření sdílené složky ve službě Azure storage podle pokynů v [vytvoření sdílené složky ve službě soubory Azure](../storage/files/storage-how-to-create-file-share.md) článku. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Připojení sdílené složky na místním počítači
1. Na místním počítači, použijte skript z [uložením Azure přihlašovací údaje sdílené složky souborů ve Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) část [sdílenými složkami Azure pomocí služby Windows](../storage/files/storage-how-to-use-files-windows.md) článku. 
2. Potom použijte `net use` příkaz pro připojení sdílené složky na svém počítači. Tady je ukázka příkazu: Zadejte název služby Azure storage a název sdílené složky, před spuštěním příkazu. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Vytvoření virtuálního počítače v testovacím prostředí
1. Na **sdílené** stránky, vyberte **skupiny prostředků** v nabídce navigace s popisem cesty v horní části. Zobrazí **skupiny prostředků** stránky. 
    
    ![Vyberte skupinu prostředků z nabídky navigace s popisem cesty](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na **skupiny prostředků** stránky, vyberte **lab** jste vytvořili ve službě DevTest Labs.

    ![Výběr testovacího prostředí](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na **DevTest Lab** stránky pro vaše testovací prostředí, vyberte **+ přidat** na panelu nástrojů. 

    ![Přidání tlačítka pro testovací prostředí](media/test-app-in-azure/add-button-in-lab.png)
4. Na **vyberte bázi** stránky, vyhledejte **smalldisk**a vyberte **[smalldisk] Windows serveru 2016 datového centra**. 

    ![Zvolte malý disk Windows serveru](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na **virtuálního počítače** určete, **název virtuálního počítače**, **uživatelské jméno**, **heslo**a vyberte **Create** .    
    
    ![Vytvoření virtuálního počítače stránky](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Připojení sdílené složky na svém virtuálním počítači
1. Po úspěšném vytvoření virtuálního počítače vyberte **virtuálního počítače** ze seznamu.    

    ![Vyberte testovací virtuální počítač](media/test-app-in-azure/select-lab-vm.png)
2. Vyberte **připojit** na panelu nástrojů pro připojení k virtuálnímu počítači. 
3. [Nainstalovat Azure PowerShell](https://azure.microsoft.com/downloads/) pomocí **nainstalovat Windows** odkaz v **nástroje příkazového řádku** oddílu. Další způsoby instalace Azure Powershellu najdete v části [v tomto článku](/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1).
4. Postupujte podle pokynů [připojení sdílené složky](#mount-the-file-share) oddílu. 

## <a name="publish-your-app-from-visual-studio"></a>Publikování aplikace ze sady Visual Studio
V této části publikování aplikace ze sady Visual Studio do testovacího virtuálního počítače v cloudu.

1. Vytvoření desktop nebo webové aplikace pomocí sady Visual Studio 2017.
2. Sestavení aplikace.
3. K publikování aplikace, klikněte pravým tlačítkem na projekt v **Průzkumníka řešení**a vyberte **publikovat**. 
4. V **Průvodci publikováním**, zadejte **jednotky** , který je namapovaný na sdílené složce.

    **Aplikace klasické pracovní plochy:**

    ![Aplikace klasické pracovní plochy](media/test-app-in-azure/desktop-app.png)

    **Webové aplikace:**

    ![Webová aplikace](media/test-app-in-azure/web-app.png)

1. Vyberte **Další** a po dokončení pracovního postupu publikování vyberte **Dokončit**. Po dokončení kroků průvodce, Visual Studio vytvoří aplikaci a publikuje ji do sdílené složky. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testovat aplikaci na testovacího virtuálního počítače v testovacím prostředí

1. Přejděte na stránku virtuálního počítače pro virtuální počítač v testovacím prostředí. 
2. Vyberte **Start** na panelu nástrojů můžete spustit virtuální počítač, pokud ji má ve stavu Zastaveno. Můžete nastavit zásady automatického spuštění a automatického vypínání pro váš virtuální počítač, aby se zabránilo spouštění a zastavování pokaždé, když. 
3. Vyberte **Connect** (Připojit).

    ![Stránce virtuálního počítače](media/test-app-in-azure/virtual-machine-page.png)
4. V rámci virtuálního počítače, spusťte **Průzkumníka souborů**a vyberte **tento počítač** najít sdílené složky.

    ![Najít sdílené složky na virtuálním počítači](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Z nějakého důvodu, pokud nemůžete najít sdílené složky ve vašem virtuálním počítači nebo na místním počítači, lze znovu připojit ho spuštěním `net use` příkazu. Můžete najít `net use` příkaz **připojit** Průvodce vaše **sdílené** na webu Azure Portal.
1. Otevření sdílené složky a zkontrolujte, jestli se aplikace, kterou jste nasadili ze sady Visual Studio. 

    ![Otevření sdílené složky na virtuálním počítači](media/test-app-in-azure/open-file-share.png)

    Nyní můžete používat a testování vaší aplikace v rámci testovacího virtuálního počítače, kterou jste vytvořili v Azure.

## <a name="next-steps"></a>Další postup
Naleznete v následujících článcích se naučíte používat virtuální počítače v testovacím prostředí. 

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
- [Restartujte virtuální počítač testovacího prostředí](devtest-lab-restart-vm.md)
- [Změna velikosti virtuálního počítače testovacího prostředí](devtest-lab-resize-vm.md)
