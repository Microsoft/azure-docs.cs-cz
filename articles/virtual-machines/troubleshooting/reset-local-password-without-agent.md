---
title: Resetovat místní heslo Windows bez agenta Azure | Microsoft Docs
description: Resetování hesla místního uživatelského účtu systému Windows v případě, že Agent hosta Azure není nainstalovaný nebo funguje na virtuálním počítači
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: c7e6772799d98cd2997a1fe6b48efe1c7632cfaa
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598370"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetování místního hesla Windows pro offline virtuální počítač Azure
Místní heslo pro Windows virtuálního počítače v Azure můžete resetovat pomocí [Azure Portal nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) za předpokladu, že je nainstalovaný Agent hosta Azure. Tato metoda je hlavním způsobem, jak resetovat heslo pro virtuální počítač Azure. Pokud narazíte na problémy s agentem hosta Azure nereaguje nebo se nedaří nainstalovat po nahrání vlastní image, můžete heslo pro Windows resetovat ručně. Tento článek podrobně popisuje, jak resetovat heslo místního účtu připojením virtuálního disku zdrojového operačního systému k jinému virtuálnímu počítači. Kroky popsané v tomto článku se nevztahují na řadiče domény se systémem Windows. 

> [!WARNING]
> Tento postup použijte až jako poslední možnost. Vždy se pokuste resetovat heslo pomocí [Azure Portal nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nejdříve.

## <a name="overview-of-the-process"></a>Přehled procesu
Základní kroky pro provedení místního resetování hesla pro virtuální počítač s Windows v Azure, když není k dispozici žádný přístup k agentovi hosta Azure, je následující:

1. Zastavte ovlivněný virtuální počítač.
1. Vytvořte snímek pro disk s operačním systémem virtuálního počítače.
1. Z snímku vytvořte kopii disku s operačním systémem.
1. Připojte zkopírovaný disk s operačním systémem k jinému virtuálnímu počítači s Windows a pak na disku vytvořte nějaké konfigurační soubory. Soubory vám pomůžou resetovat heslo.
1. Odpojte a odpojte zkopírovaný disk s operačním systémem z virtuálního počítače pro řešení potíží.
1. Proměňte disk s operačním systémem pro ovlivněný virtuální počítač.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Podrobný postup pro virtuální počítač s nasazením Správce prostředků

> [!NOTE]
> Tento postup se nevztahuje na řadiče domény systému Windows. Funguje pouze na samostatném serveru nebo na serveru, který je členem domény.

Před pokusem o provedení následujících kroků se vždycky pokuste resetovat heslo pomocí [Azure Portal nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Než začnete, ujistěte se, že máte zálohu svého virtuálního počítače.

1. Položte si snímek disku s operačním systémem ovlivněného virtuálního počítače, vytvořte disk ze snímku a pak ho připojte k virtuálnímu počítači pro odstraňování potíží. Další informace najdete v tématu [řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal](troubleshoot-recovery-disks-portal-windows.md).
2. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy.
3. Vytvořte `gpt.ini` v `\Windows\System32\GroupPolicy` jednotce zdrojového virtuálního počítače (Pokud gpt.ini existuje, přejmenujte na gpt.ini. bak):
   
   > [!WARNING]
   > Ujistěte se, že jste omylem nevytvořili následující soubory v C:\Windows, což je jednotka operačního systému pro virtuální počítač pro řešení potíží. Na jednotce operačního systému vytvořte následující soubory pro zdrojový virtuální počítač, který je připojený jako datový disk.
   
   * Do `gpt.ini` souboru, který jste vytvořili, přidejte následující řádky:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini.png" alt-text="Vytvořit gpt.ini":::

4. Vytvořte `scripts.ini` v `\Windows\System32\GroupPolicy\Machine\Scripts\` . Ujistěte se, že jsou zobrazené skryté složky. V případě potřeby vytvořte `Machine` složky nebo `Scripts` . 
   
   * Do `scripts.ini` souboru, který jste vytvořili, přidejte následující řádky:
     
     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-1.png" alt-text="Vytvořit gpt.ini" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Vytvořit gpt.ini":::
   
    Při definování nového hesla musíte splnit požadavky nakonfigurované složitosti hesla pro váš virtuální počítač.

6. V Azure Portal odpojte disk od virtuálního počítače pro řešení potíží.

7. [Změňte disk s operačním systémem pro ovlivněný virtuální počítač](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Po spuštění nového virtuálního počítače se připojte k VIRTUÁLNÍmu počítači pomocí vzdálené plochy s novým heslem, které jste zadali ve `FixAzureVM.cmd` skriptu.

9. Z vzdálené relace k novému virtuálnímu počítači odeberte následující soubory pro vyčištění prostředí:
    
    * Z%windir%\System32\GroupPolicy\Machine\Scripts\Startup
      * odebrat FixAzureVM. cmd
    * Z%windir%\System32\GroupPolicy\Machine\Scripts
      * odebrat scripts.ini
    * Z%windir%\System32\GroupPolicy
      * Odeberte gpt.ini (Pokud gpt.ini existovala dříve a přejmenovali jste ji na gpt.ini. bak, přejmenujte soubor. bak zpět na gpt.ini).

## <a name="detailed-steps-for-classic-vm"></a>Podrobný postup pro klasický virtuální počítač

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Tento postup se nevztahuje na řadiče domény systému Windows. Funguje pouze na samostatném serveru nebo na serveru, který je členem domény.

Před pokusem o provedení následujících kroků se vždycky pokuste resetovat heslo pomocí [Azure Portal nebo Azure PowerShell](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) . Než začnete, ujistěte se, že máte zálohu svého virtuálního počítače. 

1. Odstraňte ovlivněný virtuální počítač v Azure Portal. Odstranění virtuálního počítače odstraní jenom metadata, referenční informace o virtuálním počítači v Azure. Virtuální disky se uchovávají při odstranění virtuálního počítače:
   
   * Vyberte virtuální počítač v Azure Portal a pak klikněte na *Odstranit*:
     
     :::image type="content" source="./media/reset-local-password-without-agent/delete-vm-classic.png" alt-text="Vytvořit gpt.ini":::

2. Připojte disk s operačním systémem zdrojového virtuálního počítače k virtuálnímu počítači pro řešení potíží. Virtuální počítač pro řešení potíží se musí nacházet ve stejné oblasti jako disk s operačním systémem zdrojového virtuálního počítače (například `West US` ):
   
   1. Vyberte virtuální počítač pro řešení potíží ve Azure Portal. Klikněte na *disky*  |  *připojit existující*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-existing-classic.png" alt-text="Vytvořit gpt.ini":::
     
   2. Vyberte *soubor VHD* a pak vyberte účet úložiště, který obsahuje váš zdrojový virtuální počítač:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-storage-account-classic.png" alt-text="Vytvořit gpt.ini":::
     
   3. Zaškrtněte políčko *Zobrazit účty klasického úložiště*a pak vyberte zdrojový kontejner. Zdrojový kontejner je obvykle *VHD*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-classic.png" alt-text="Vytvořit gpt.ini":::

      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png" alt-text="Vytvořit gpt.ini":::
     
   4. Vyberte virtuální pevný disk s operačním systémem, který se má připojit. Pro dokončení procesu klikněte na tlačítko *Vybrat* :
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png" alt-text="Vytvořit gpt.ini":::

   5. Kliknutím na OK připojte disk.

      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-okay-classic.png" alt-text="Vytvořit gpt.ini":::

3. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy a ujistěte se, že je disk s operačním systémem zdrojového virtuálního počítače viditelný:

   1. Vyberte virtuální počítač pro řešení potíží v Azure Portal a klikněte na *připojit*.

   2. Otevřete soubor RDP, který se stáhne. Zadejte uživatelské jméno a heslo virtuálního počítače pro řešení potíží.

   3. V Průzkumníku souborů vyhledejte datový disk, který jste připojili. Pokud virtuální pevný disk virtuálního počítače je jediným datovým diskem připojeným k virtuálnímu počítači pro řešení potíží, měl by to být jednotka F:.
     
      :::image type="content" source="./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png" alt-text="Vytvořit gpt.ini":::

4. Vytvořte `gpt.ini` v `\Windows\System32\GroupPolicy` jednotce zdrojového virtuálního počítače (Pokud `gpt.ini` existuje, přejmenujte na `gpt.ini.bak` ):
   
   > [!WARNING]
   > Ujistěte se, že jste v nástroji nechtěně nevytvořili následující soubory, a to `C:\Windows` na jednotce operačního systému pro virtuální počítač pro řešení potíží. Na jednotce operačního systému vytvořte následující soubory pro zdrojový virtuální počítač, který je připojený jako datový disk.
   
   * Do `gpt.ini` souboru, který jste vytvořili, přidejte následující řádky:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini-classic.png" alt-text="Vytvořit gpt.ini":::

5. Vytvořte `scripts.ini` v `\Windows\System32\GroupPolicy\Machine\Scripts\` . Ujistěte se, že jsou zobrazené skryté složky. V případě potřeby vytvořte `Machine` složky nebo `Scripts` .
   
   * Do `scripts.ini` souboru, který jste vytvořili, přidejte následující řádky:

     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-classic-1.png" alt-text="Vytvořit gpt.ini" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Vytvořit gpt.ini":::
   
    Při definování nového hesla musíte splnit požadavky nakonfigurované složitosti hesla pro váš virtuální počítač.

7. V Azure Portal odpojte disk od virtuálního počítače pro řešení potíží:
   
   1. Vyberte virtuální počítač pro řešení potíží v Azure Portal klikněte na *disky*.
   
   2. Vyberte datový disk připojený v kroku 2, klikněte na **Odpojit**a pak klikněte na **OK**.

     :::image type="content" source="./media/reset-local-password-without-agent/data-disks-classic.png" alt-text="Vytvořit gpt.ini":::
     
     :::image type="content" source="./media/reset-local-password-without-agent/detach-disk-classic.png" alt-text="Vytvořit gpt.ini":::

8. Vytvořte virtuální počítač z disku s operačním systémem zdrojového virtuálního počítače:
   
     :::image type="content" source="./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png" alt-text="Vytvořit gpt.ini":::

     :::image type="content" source="./media/reset-local-password-without-agent/choose-subscription-classic.png" alt-text="Vytvořit gpt.ini":::

     :::image type="content" source="./media/reset-local-password-without-agent/create-vm-classic.png" alt-text="Vytvořit gpt.ini":::

## <a name="complete-the-create-virtual-machine-experience"></a>Dokončení prostředí pro vytvoření virtuálního počítače

1. Po spuštění nového virtuálního počítače se připojte k VIRTUÁLNÍmu počítači pomocí vzdálené plochy s novým heslem, které jste zadali ve `FixAzureVM.cmd` skriptu.

2. Z vzdálené relace k novému virtuálnímu počítači odeberte následující soubory pro vyčištění prostředí:
    
    * Výsledkem `%windir%\System32\GroupPolicy\Machine\Scripts\Startup\`
      * odebrány `FixAzureVM.cmd`
    * Výsledkem `%windir%\System32\GroupPolicy\Machine\Scripts`
      * odebrány `scripts.ini`
    * Výsledkem `%windir%\System32\GroupPolicy`
      * odebrat `gpt.ini` (Pokud `gpt.ini` existovalo dřív a přejmenovali jste ho na `gpt.ini.bak` , přejmenujte `.bak` soubor zpátky na `gpt.ini` )

## <a name="next-steps"></a>Další kroky
Pokud se stále nemůžete připojit pomocí vzdálené plochy, přečtěte si [příručku pro odstraňování potíží s RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Podrobný průvodce odstraňováním potíží s](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) protokolem RDP hledá místo konkrétních kroků postupy řešení potíží. Můžete také [otevřít žádost o podporu Azure](https://azure.microsoft.com/support/options/) pro praktickou pomoc.
