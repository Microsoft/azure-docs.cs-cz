---
title: "Příprava na hostitelském počítači Azure zásobníku Development Kit (ASDK) | Microsoft Docs"
description: "Popisuje postup přípravy na hostitelském počítači Azure zásobníku Development Kit (ASDK) pro instalaci ASDK."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8b1a6298ab32dc364aa1543e4a8d5db47b02a098
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="prepare-the-asdk-host-computer"></a>Příprava na hostitelském počítači ASDK
Před instalací ASDK na hostitelském počítači, musí být připraveny ASDK prostředí pro instalaci. Když připravil development kit hostitelský počítač se spustí z pevného disku virtuálního počítače CloudBuilder.vhdx zahájíte ASDK nasazení.

## <a name="prepare-the-development-kit-host-computer"></a>Příprava hostitelský počítač development kit
Před instalací ASDK na hostitelském počítači, musí být připraveno prostředí ASDK hostitele počítače.
1. Přihlaste se jako místní správce k počítači development kit hostitele.
2. Ujistěte se, že CloudBuilder.vhdx soubor byl přesunut do kořenového adresáře jednotky C:\ (C:\CloudBuilder.vhdx).
3. Spusťte následující skript pro stažení instalačního souboru development kit (asdk-installer.ps1) z [nástroje úložiště GitHub zásobník Azure](https://github.com/Azure/AzureStack-Tools) k **C:\AzureStack_Installer** složky na vaše hostitelský počítač Development kit:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Ze konzolu se zvýšenými oprávněními prostředí PowerShell, spusťte **C:\AzureStack_Installer\asdk-installer.ps1** skript a potom klikněte na **Příprava prostředí**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Na **vyberte Cloudbuilder vhdx** stránka instalační program, vyhledejte a vyberte **cloudbuilder.vhdx** soubor, který stažené a rozbalené v [předchozí kroky](asdk-download.md). Na této stránce můžete rovněž, Volitelně můžete povolit **přidejte ovladače** zaškrtávací políčko, pokud potřebujete přidat další ovladače k hostitelskému počítači development kit. Klikněte na **Další**.  

    ![](media/asdk-prepare-host/2.PNG)

6. Na **volitelné nastavení** zadejte místní správce informace o účtu pro vývoj kit hostitelský počítač a potom klikněte na **Další**. Můžete také zadat hodnoty pro následující volitelné nastavení:
  - **ComputerName**: Tento parametr nastaví název hostitele development kit. Název musí být v souladu s požadavky na plně kvalifikovaný název domény a musí být maximálně 15 znaků nebo méně. Výchozí hodnota je náhodný název generované systémem Windows.
  - **Časové pásmo**: Nastaví časové pásmo pro hostitele development kit. Výchozí hodnota je (UTC-8:00) Tichomoří (USA a Kanada).
  - **Konfiguraci statické IP adresy**: Nastaví nasazení tak, aby používal statickou adresu IP. Jinak když instalační program restartuje do cloudbuilder.vhx, rozhraní sítě jsou nakonfigurované s DHCP.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Pokud nemáte zadejte přihlašovací údaje místního správce v tomto kroku, budete potřebovat přímo nebo KVM přístup k hostiteli po restartování počítače jako součást nastavení development kit.

7. Pokud jste v předchozím kroku vybrali konfiguraci statické IP adresy, musíte se teď:
    - Vyberte síťový adaptér. Ujistěte se, zda se můžete připojit k adaptéru před kliknutím na **Další**.
    - Ujistěte se, že **IP adresu**, **brány**, a **DNS** hodnoty jsou správné a potom klikněte na **Další**.
13. Klikněte na tlačítko **Další** ke spuštění procesu přípravy.
14. Určuje, kdy přípravy **dokončeno**, klikněte na tlačítko **Další**.
15. Klikněte na tlačítko **restartovat nyní** development kit hostitelský počítač spustit s cloudbuilder.vhdx a [pokračovat v procesu nasazení](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>Další postup
[Nainstalujte ASDK](asdk-install.md)