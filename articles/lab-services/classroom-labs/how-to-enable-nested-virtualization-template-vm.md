---
title: Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services.
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
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981992"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services
Tento článek popisuje, jak nastavit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services. Vnořená virtualizace se používá v Azure Lab Services, když každý student ve třídě vyžaduje více počítačů.
 
## <a name="considerations"></a>Požadavky
Před nastavením testovacího prostředí s vnořenou virtualizací je zde několik věcí, které je potřeba vzít v úvahu.

- Při vytváření nového testovacího prostředí vyberte **střední (vnořená virtualizace)** nebo **velké** velikosti pro velikost virtuálního počítače. Tyto velikosti virtuálních počítačů podporují vnořenou virtualizaci. 
- Vyberte velikost, která bude poskytovat dobrý výkon pro hostitele i klientské virtuální počítače.  Mějte na paměti, že při použití virtualizace musí být zvolená velikost dostačující pro nejen jeden počítač, ale hostitel i všechny klientské počítače, které musí být spuštěny souběžně.
- Klientské virtuální počítače nebudou mít přístup k prostředkům Azure, jako jsou servery DNS ve službě Azure Virtual Network.
- Hostitelský virtuální počítač vyžaduje Instalační program, aby mohl klientský počítač mít připojení k Internetu. 
- Klientské virtuální počítače jsou licencované jako nezávislé počítače. Informace o licencování pro operační systémy a produkty společnosti Microsoft najdete v tématu věnovaném [licencování společnosti Microsoft](https://www.microsoft.com/licensing/default) . Před nastavením počítače šablony se podívejte na licenční smlouvy pro jakýkoli jiný software, který se používá.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Povolit vnořenou virtualizaci na virtuálním počítači šablony
Postup v této části se zaměřuje na nastavení vnořené virtualizace pro Windows Server 2016 nebo Windows Server 2019. K nastavení počítače s šablonou Hyper-V použijete skript. Pro automatizované řešení se podívejte na téma skripty ve [službě Lab Services technologie Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). Následující postup vás provede postupem použití skriptu.

1. Pokud používáte Internet Explorer, bude pravděpodobně nutné přidat `https://github.com` do seznamu důvěryhodných serverů. 
    1. Otevřete Internet Explorer.
    1. Vyberte ikonu ozubeného kolečka a zvolte **Možnosti Internetu**.  
    1. Jakmile se zobrazí dialogové okno **Možnosti Internetu** , vyberte **zabezpečení**, vyberte **Důvěryhodné servery**, klikněte na tlačítko **servery** .
    1. Až se zobrazí dialogové okno **Důvěryhodné servery** , přidejte `https://github.com` do seznamu důvěryhodných webů a vyberte **Zavřít**.

        ![Důvěryhodné servery](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Stáhněte si soubory úložiště Git, jak je uvedeno v následujícím postupu.  Alternativně lze úložiště Git klonovat z [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git). 
    1. Přejít na [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Klikněte na tlačítko ***klonovat nebo stáhnout** .
    1. Klikněte na **Stáhnout soubor zip**.
    1. Extrakce souboru ZIP
1. Spusťte **PowerShell** v režimu **správce** .
1. V okně PowerShellu přejděte do složky se staženým skriptem. Pokud přecházíte z horní složky souborů úložiště, skript se nachází na `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
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
Viz následující články:

- [Jak povolit vnořenou virtualizaci na virtuálním počítači Azure](../../virtual-machines/windows/nested-virtualization.md) 
- [Instalace role Hyper-V na Windows serveru](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Skripty služby Lab Services technologie Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
