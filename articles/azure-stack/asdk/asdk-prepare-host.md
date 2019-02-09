---
title: Příprava hostitelském počítači Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Popisuje postup instalace ASDK Příprava hostitelském počítači Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 36012c023025b8304dfaf9cc63997f600ef6cbe8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962719"
---
# <a name="prepare-the-asdk-host-computer"></a>Příprava hostitelském počítači ASDK
Než nainstalujete ASDK v hostitelském počítači ASDK hostitele musí být připravené k instalaci. Když development kit hostitelský počítač připravený, se spustí z pevného disku virtuálního počítače CloudBuilder.vhdx zahájíte ASDK nasazení.

## <a name="prepare-the-development-kit-host-computer"></a>Příprava hostitelském počítači development kit
Před instalací ASDK v hostitelském počítači, musí být připravené prostředí ASDK hostitelského počítače.
1. Přihlaste se jako místní správce na vývojovém počítači hostitele kit.
2. Ujistěte se, že CloudBuilder.vhdx soubor byl přesunut na kořenové jednotce C:\ (C:\CloudBuilder.vhdx).
3. Spusťte následující skript, který stáhnout instalační soubor development kit (asdk installer.ps1) z [úložiště nástroje pro Azure Stack GitHub](https://github.com/Azure/AzureStack-Tools) k **C:\AzureStack_Installer** složky na vaše Development kit hostitelském počítači:

   > [!IMPORTANT]
   > Nezapomeňte si stáhnout soubor asdk installer.ps1 pokaždé, když instalujete ASDK. Časté změny probíhají tohoto skriptu a její nejnovější verze by měla sloužit pro každé nasazení ASDK. Starší verze souboru, který nemusí fungovat s nejnovější verzí.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. V konzole Powershellu se zvýšenými oprávněními spusťte **C:\AzureStack_Installer\asdk-installer.ps1** skript a potom klikněte na tlačítko **Příprava prostředí**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Na **vyberte Cloudbuilder vhdx** stránce instalačního programu, vyhledejte a vyberte **cloudbuilder.vhdx** soubor, který jste stažené a rozbalené v [v předchozích krocích](asdk-download.md). Na této stránce můžete také volitelně povolit **přidat ovladače** zaškrtávací políčko, pokud je potřeba přidat další ovladače k hostitelskému počítači development kit. Klikněte na **Další**.  

    ![](media/asdk-prepare-host/2.PNG)

6. Na **volitelná nastavení** místního správce zadejte informace o účtu pro hostitelský počítač development kit a potom klikněte na tlačítko **Další**.<br><br>Pokud nezadáte přihlašovací údaje místního správce v tomto kroku, bude nutné přímo nebo KVM přístup k hostiteli po restartování počítače jako součást nastavování development kit.

   ![](media/asdk-prepare-host/3.PNG)

    Můžete také zadat hodnoty pro následující volitelné nastavení:
    - **ComputerName**: Tato možnost nastaví název hostitele development kit. Název musí splňovat požadavky na plně kvalifikovaný název domény a musí být maximálně 15 znaků nebo méně. Výchozí hodnota je náhodný název generované Windows.
    - **Konfigurace statické IP**: Nastaví nasazení tak, aby používal statickou IP adresu. Jinak Pokud instalační program restartuje do cloudbuilder.vhdx, síťová rozhraní jsou konfigurovaný server DHCP. Pokud se rozhodnete použít konfiguraci statické IP adresy, další možnosti se zobrazí, kde zároveň musíte:
      - Vyberte síťový adaptér. Ujistěte se, že můžete připojit k adaptéru před kliknutím na **Další**.
      - Zkontrolujte, zda je zobrazeno **IP adresu**, **brány**, a **DNS** hodnoty jsou správné a potom klikněte na **Další**.
13. Klikněte na tlačítko **Další** zahájíte proces přípravy.
14. Při přípravě označuje **dokončeno**, klikněte na tlačítko **Další**.

    ![](media/asdk-prepare-host/4.PNG)

15. Klikněte na tlačítko **restartovat** přihlásit cloudbuilder.vhdx hostitelský počítač development kit a [pokračovat v procesu nasazení](asdk-install.md).

    ![](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Další postup
[Nainstalujte ASDK](asdk-install.md)
