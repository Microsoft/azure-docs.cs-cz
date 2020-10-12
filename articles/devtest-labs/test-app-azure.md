---
title: Jak testovat aplikaci v Azure | Microsoft Docs
description: Naučte se, jak vytvořit sdílenou složku v testovacím prostředí a připojit ji na místní počítač a virtuální počítač v testovacím prostředí, a potom do sdílené složky nasadit desktopové a webové aplikace a otestovat je.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2dbbf349da4e352fe20a22db03cc9063d801990
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282241"
---
# <a name="test-your-app-in-azure"></a>Testování aplikace v Azure 
Tento článek popisuje kroky pro testování vaší aplikace v Azure pomocí DevTest Labs. Nejdřív nastavíte sdílenou složku v testovacím prostředí a připojíte ji jako jednotku na místním vývojovém počítači a virtuální počítač v testovacím prostředí. Pak použijete Visual Studio 2019 k nasazení aplikace do sdílené složky, abyste mohli aplikaci spustit na virtuálním počítači v testovacím prostředí.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky 
1. [Vytvořte si předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte, a přihlaste se [Azure Portal](https://portal.azure.com).
2. Postupujte podle pokynů v [tomto článku](devtest-lab-create-lab.md) a vytvořte testovací prostředí pomocí Azure DevTest Labs. Připněte testovací prostředí na řídicí panel, abyste ho mohli snadno najít při příštím přihlášení. Azure DevTest Labs vám umožní rychle vytvářet prostředky v rámci Azure minimalizací odpadů a řízení nákladů. Další informace o DevTest Labs najdete v tématu [Přehled](devtest-lab-overview.md). 
3. Podle pokynů v článku [Vytvoření účtu úložiště](../storage/common/storage-account-create.md) vytvořte účet Azure Storage ve skupině prostředků testovacího prostředí. Na stránce **vytvořit účet úložiště** vyberte **použít existující** pro **skupinu prostředků**a vyberte **skupinu prostředků testovacího prostředí**. 
4. Pomocí pokynů v článku [Vytvoření sdílené složky v Azure Files](../storage/files/storage-how-to-create-file-share.md) vytvořte sdílenou složku ve službě Azure Storage. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Připojte sdílenou složku na místním počítači.
1. V místním počítači použijte skript z připojení ke sdílené [složce Azure](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) v tématu [použití sdílené složky Azure s Windows](../storage/files/storage-how-to-use-files-windows.md) . 
2. Pak pomocí `net use` příkazu připojte sdílenou složku na svém počítači. Tady je ukázkový příkaz: před spuštěním příkazu zadejte název úložiště Azure a jeho název sdílené složky. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Vytvoření virtuálního počítače v testovacím prostředí
1. Na stránce **sdílení souborů** vyberte **skupinu prostředků** v nabídce s popisem cesty v horní části. Zobrazí se stránka **skupiny prostředků** . 
    
    ![Vybrat skupinu prostředků z nabídky s popisem cesty](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na stránce **Skupina prostředků** vyberte **testovací prostředí** , které jste vytvořili v DevTest Labs.

    ![Výběr testovacího prostředí](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na stránce **testovací prostředí DevTest** pro testovací prostředí vyberte **+ Přidat** na panelu nástrojů. 

    ![Přidat tlačítko pro testovací prostředí](media/test-app-in-azure/add-button-in-lab.png)
4. Na stránce **Zvolte základní** stránku vyhledejte **smalldisk**a vyberte **[Smalldisk] datové centrum Windows Server 2016**. 

    ![Výběr malého disku Windows serveru](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na stránce **virtuální počítač** zadejte **název virtuálního počítače**, **uživatelské jméno**, **heslo**a vyberte **vytvořit**.    
    
    ![Stránka vytvořit virtuální počítač](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Připojte sdílenou složku na svém VIRTUÁLNÍm počítači.
1. Po úspěšném vytvoření virtuálního počítače vyberte tento **virtuální počítač** ze seznamu.    

    ![Vyberte virtuální počítač testovacího prostředí.](media/test-app-in-azure/select-lab-vm.png)
2. Pokud se chcete připojit k virtuálnímu počítači, vyberte **připojit** na panelu nástrojů. 
3. [Nainstalujte prostředí Azure PowerShell](/powershell/azure/install-az-ps).
4. Postupujte podle pokynů v části připojení sdílení souborů. 

## <a name="publish-your-app-from-visual-studio"></a>Publikování aplikace ze sady Visual Studio
V této části publikujete aplikaci ze sady Visual Studio do testovacího virtuálního počítače v cloudu.

1. Pomocí sady Visual Studio 2019 vytvořte pracovní plochu nebo webovou aplikaci.
2. Sestavte aplikaci.
3. Pokud chcete aplikaci publikovat, klikněte pravým tlačítkem na projekt v **Průzkumník řešení**a vyberte **publikovat**. 
4. V **Průvodci publikování**zadejte **jednotku** , která je namapovaná na sdílenou složku.

    **Aplikace klasické pracovní plochy:**

    ![Desktopová aplikace](media/test-app-in-azure/desktop-app.png)

    **Webová aplikace:**

    ![Webová aplikace](media/test-app-in-azure/web-app.png)

1. Kliknutím na tlačítko **Další** dokončete pracovní postup publikování a vyberte **Dokončit**. Po dokončení kroků Průvodce aplikace Visual Studio vytvoří aplikaci a publikuje ji do sdílené složky. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testování aplikace na testovacím VIRTUÁLNÍm počítači v testovacím prostředí

1. Přejděte na stránku virtuálního počítače pro váš virtuální počítač v testovacím prostředí. 
2. Pokud je virtuální počítač v zastaveném stavu, vyberte **Spustit** na panelu nástrojů. Pro virtuální počítač můžete nastavit zásady automatického spouštění a automatického vypínání, abyste se vyhnuli spouštění a zastavování. 
3. Vyberte **Připojit**.

    ![Stránka virtuálního počítače](media/test-app-in-azure/virtual-machine-page.png)
4. V rámci virtuálního počítače spusťte **Průzkumníka souborů**a vyberte **Tento počítač** , abyste našli svoji sdílenou složku.

    ![Najít sdílenou složku na virtuálním počítači](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Pokud nemůžete najít sdílenou složku na virtuálním počítači nebo na místním počítači, můžete ji z nějakého důvodu znovu připojit spuštěním `net use` příkazu. Příkaz najdete v `net use` průvodci **připojením** **sdílené složky** v Azure Portal.
1. Otevřete sdílenou složku a ověřte, že se vám zobrazí aplikace, kterou jste nasadili ze sady Visual Studio. 

    ![Otevřít sdílenou složku na virtuálním počítači](media/test-app-in-azure/open-file-share.png)

    Teď můžete aplikaci používat a testovat v rámci testovacího virtuálního počítače, který jste vytvořili v Azure.

## <a name="next-steps"></a>Další kroky
V následujících článcích se dozvíte, jak používat virtuální počítače v testovacím prostředí. 

- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)
- [Restartování testovacího virtuálního počítače](devtest-lab-restart-vm.md)
- [Změna velikosti virtuálního počítače testovacího prostředí](devtest-lab-resize-vm.md)
