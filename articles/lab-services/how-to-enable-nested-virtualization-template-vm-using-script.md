---
title: Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services (skript) | Microsoft Docs
description: Přečtěte si, jak vytvořit virtuální počítač šablony s více virtuálními počítači v rámci pomocí skriptu.  Jinými slovy, povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251491"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services pomocí skriptu

Vnořená virtualizace umožňuje vytvořit prostředí s několika virtuálními počítači v rámci virtuálních počítačů šablon testovacího prostředí. Publikování šablony poskytne každého uživatele v testovacím prostředí s virtuálním počítačem nastaveným s několika virtuálními počítači v rámci něj.  Další informace o vnořené virtualizaci a Azure Lab Services najdete v tématu [Povolení vnořené virtualizace na virtuálním počítači šablony v Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Postup v tomto článku se zaměřuje na nastavení vnořené virtualizace pro Windows Server 2016, Windows Server 2019 nebo Windows 10. K nastavení počítače s šablonou Hyper-V použijete skript.  Následující postup vás provede postupem použití [skriptů technologie Hyper-V služby testovacího prostředí](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Při vytváření testovacího prostředí vyberte možnost **Velká (vnořená virtualizace)** nebo **střední (vnořená virtualizace)** pro velikost virtuálního počítače.  Vnořená virtualizace nebude jinak fungovat.  

## <a name="run-script"></a>Spuštění skriptu

1. Pokud používáte Internet Explorer, bude pravděpodobně nutné přidat `https://github.com` seznam důvěryhodných serverů.
    1. Otevřete Internet Explorer.
    1. Vyberte ikonu ozubeného kolečka a zvolte **Možnosti Internetu**.  
    1. Jakmile se zobrazí dialogové okno **Možnosti Internetu** , vyberte **zabezpečení**, vyberte **Důvěryhodné servery**, klikněte na tlačítko **servery** .
    1. Jakmile se zobrazí dialogové okno **Důvěryhodné servery** , přidejte `https://github.com` seznam důvěryhodných webů a vyberte **Zavřít**.

        ![Důvěryhodné servery](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Stáhněte si soubory úložiště Git, jak je uvedeno v následujícím postupu.
    1. Přejít na  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) .
    1. Klikněte na tlačítko **klonovat nebo stáhnout** .
    1. Klikněte na **Stáhnout soubor zip**.
    1. Extrakce souboru ZIP

    >[!TIP]
    >Úložiště Git můžete také klonovat na adrese [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Spusťte **PowerShell** v režimu **správce** .
1. V okně PowerShellu přejděte do složky se staženým skriptem. Pokud přecházíte z horní složky souborů úložiště, skript se nachází na adrese `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
1. Možná budete muset změnit zásady spouštění na úspěšné spuštění skriptu. Spusťte následující příkaz:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Spusťte skript:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skript může vyžadovat restartování počítače. Postupujte podle pokynů ze skriptu a znovu spusťte skript, dokud se ve výstupu nezobrazí **dokončený skript** .
1. Nezapomeňte resetovat zásady spouštění. Spusťte následující příkaz:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Závěr

Teď je váš počítač šablon připravený k vytváření virtuálních počítačů s technologií Hyper-V. Pokyny k vytváření virtuálních počítačů s technologií Hyper-V najdete v tématu [Vytvoření virtuálního počítače v technologii Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Podívejte se také na téma [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) , kde najdete dostupné operační systémy a software.  

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)