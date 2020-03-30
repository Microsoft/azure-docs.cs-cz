---
title: Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services (Script) | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit virtuální počítač šablony s více virtuálními počítači uvnitř.  Jinými slovy povolte vnořenou virtualizaci na virtuálním počítači šablony ve službě Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503033"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services pomocí skriptu

Vnořená virtualizace umožňuje vytvořit prostředí s více virtuálními počítači uvnitř virtuálního počítače šablony testovacího prostředí. Publikování šablony poskytne každému uživateli v testovacím prostředí virtuální počítač nastavený s více virtuálními počítači v něm.  Další informace o vnořené virtualizaci a službách Azure Lab services najdete [v tématu Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Postup v tomto článku se zaměřuje na nastavení vnořené virtualizace pro Windows Server 2016 nebo Windows Server 2019. Budete používat skript k nastavení šablony počítače s Hyper-V.  Následující kroky vás provedou používáním [skriptů Hyper-V služby Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)Services .

>[!IMPORTANT]
>Vyberte **Velké (vnořené virtualizace)** nebo **Střední (vnořené virtualizace)** pro velikost virtuálního počítače při vytváření testovacího prostředí.  Vnořená virtualizace nebude fungovat jinak.  

## <a name="run-script"></a>Spuštění skriptu

1. Pokud používáte aplikaci Internet Explorer, `https://github.com` bude pravděpodobně pravděpodobně třeba přidat do seznamu důvěryhodných serverů.
    1. Otevřete Internet Explorer.
    1. Vyberte ikonu ozubeného kola a zvolte **možnosti Internetu**.  
    1. Po zobrazení dialogového okna **Možnosti Internetu** vyberte **Možnosti zabezpečení**, vyberte **Důvěryhodné servery**, klepněte na tlačítko **Weby.**
    1. Po zobrazení dialogového okna `https://github.com` **Důvěryhodné weby** přidejte do seznamu důvěryhodné weby a vyberte **Zavřít**.

        ![Důvěryhodné servery](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Stáhněte si soubory úložiště Git, jak je uvedeno v následujících krocích.
    1. Přejděte [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)na .
    1. Klikněte na tlačítko **Klonovat nebo Stáhnout.**
    1. Klepněte na **tlačítko Stáhnout soubor ZIP**.
    1. Extrahování souboru ZIP

    >[!TIP]
    >Můžete také klonovat úložiště Git [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)na adrese .

1. Spusťte **PowerShell** v režimu **správce.**
1. V okně PowerShellpřejděte do složky se staženým skriptem. Pokud procházíte z horní složky souborů úložiště, je skript `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`umístěn na adrese .
1. Možná budete muset změnit zásady spuštění úspěšně spustit skript. Spusťte následující příkaz:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Spusťte skript:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skript může vyžadovat restartování počítače. Postupujte podle pokynů ze skriptu a znovu spusťte skript, dokud **script dokončen** je vidět ve výstupu.
1. Nezapomeňte obnovit zásady provádění. Spusťte následující příkaz:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Závěr

Nyní je váš počítač šablony připraven k vytvoření virtuálních počítačů Hyper-V. Pokyny k vytvoření virtuálních počítačů [v technologii Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) najdete v tématu Vytvoření virtuálního počítače v hyperv. Další informace o dostupných operačních systémech a softwaru naleznete v [Centru hodnocení společnosti Microsoft.](https://www.microsoft.com/evalcenter/)  

## <a name="next-steps"></a>Další kroky

Další kroky jsou společné pro nastavení libovolného testovacího prostředí.

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavení plánu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mailové registrační odkazy pro studenty](how-to-configure-student-usage.md#send-invitations-to-users)