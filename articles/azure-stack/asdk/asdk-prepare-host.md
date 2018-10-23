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
ms.date: 10/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dd02dfe37a1566a3121b3571f72d964621330cd0
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647302"
---
# <a name="prepare-the-asdk-host-computer"></a>Příprava hostitelském počítači ASDK
Před instalací ASDK v hostitelském počítači, musí být ASDK prostředí připravené k instalaci. Když development kit hostitelský počítač připravený, se spustí z pevného disku virtuálního počítače CloudBuilder.vhdx zahájíte ASDK nasazení.

## <a name="prepare-the-development-kit-host-computer"></a>Příprava hostitelském počítači development kit
Před instalací ASDK v hostitelském počítači, musí být připravené prostředí ASDK hostitelského počítače.
1. Přihlaste se jako místní správce na vývojovém počítači hostitele kit.
2. Ujistěte se, že CloudBuilder.vhdx soubor byl přesunut na kořenové jednotce C:\ (C:\CloudBuilder.vhdx).
3. Spusťte následující skript, který stáhnout instalační soubor development kit (asdk installer.ps1) z [úložiště nástroje pro Azure Stack GitHub](https://github.com/Azure/AzureStack-Tools) k **C:\AzureStack_Installer** složky na vaše Development kit hostitelském počítači:

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

6. Na **volitelná nastavení** místního správce zadejte informace o účtu pro hostitelský počítač development kit a potom klikněte na tlačítko **Další**. Můžete také zadat hodnoty pro následující volitelné nastavení:
  - **ComputerName**: Tato možnost nastaví název hostitele development kit. Název musí splňovat požadavky na plně kvalifikovaný název domény a musí být maximálně 15 znaků nebo méně. Výchozí hodnota je náhodný název generované Windows.
  - **Konfigurace statické IP**: Nastaví nasazení tak, aby používal statickou IP adresu. Jinak Pokud instalační program restartuje do cloudbuilder.vhx, síťová rozhraní jsou konfigurovaný server DHCP.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Pokud nezadáte přihlašovací údaje místního správce v tomto kroku, bude nutné přímo nebo KVM přístup k hostiteli po restartování počítače jako součást nastavování development kit.

7. Pokud jste v předchozím kroku zvolili konfiguraci statické IP adresy, musíte se nyní:
    - Vyberte síťový adaptér. Ujistěte se, že můžete připojit k adaptéru před kliknutím na **Další**.
    - Ujistěte se, že **IP adresu**, **brány**, a **DNS** hodnoty jsou správné a potom klikněte na **Další**.
13. Klikněte na tlačítko **Další** zahájíte proces přípravy.
14. Při přípravě označuje **dokončeno**, klikněte na tlačítko **Další**.
15. Klikněte na tlačítko **restartovat** přihlásit cloudbuilder.vhdx hostitelský počítač development kit a [pokračovat v procesu nasazení](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>Další postup
[Nainstalujte ASDK](asdk-install.md)
