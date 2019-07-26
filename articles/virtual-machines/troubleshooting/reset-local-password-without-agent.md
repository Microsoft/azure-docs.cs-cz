---
title: Resetovat místní heslo Windows bez agenta Azure | Microsoft Docs
description: Resetování hesla místního uživatelského účtu systému Windows v případě, že Agent hosta Azure není nainstalovaný nebo funguje na virtuálním počítači
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 5354ebc8c25125f86a0208382d176c84372cadc1
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369867"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetovat místní heslo Windows pro virtuální počítač Azure offline
Místní heslo pro Windows virtuálního počítače v Azure můžete resetovat pomocí [Azure Portal nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) za předpokladu, že je nainstalovaný Agent hosta Azure. Tato metoda je hlavním způsobem, jak resetovat heslo pro virtuální počítač Azure. Pokud narazíte na problémy s agentem hosta Azure nereaguje nebo se nedaří nainstalovat po nahrání vlastní image, můžete heslo pro Windows resetovat ručně. Tento článek podrobně popisuje, jak resetovat heslo místního účtu připojením virtuálního disku zdrojového operačního systému k jinému virtuálnímu počítači. Kroky popsané v tomto článku se nevztahují na řadiče domény se systémem Windows. 

> [!WARNING]
> Tento postup použijte až jako poslední možnost. Vždy se pokuste resetovat heslo pomocí [Azure Portal nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nejdříve.

## <a name="overview-of-the-process"></a>Přehled procesu
Základní kroky pro provedení místního resetování hesla pro virtuální počítač s Windows v Azure, když není k dispozici žádný přístup k agentovi hosta Azure, je následující:

1. Odstraňte zdrojový virtuální počítač. Virtuální disky jsou zachovány.

2. Připojte disk s operačním systémem zdrojového virtuálního počítače k jinému VIRTUÁLNÍmu počítači ve stejném umístění v rámci vašeho předplatného Azure. Tento virtuální počítač se označuje jako virtuální počítač pro řešení potíží.

3. Pomocí virtuálního počítače pro řešení potíží vytvořte některé konfigurační soubory na disku s operačním systémem zdrojového virtuálního počítače.

4. Odpojte disk s operačním systémem virtuálního počítače od virtuálního počítače pro řešení potíží.

5. Použijte šablonu Správce prostředků k vytvoření virtuálního počítače pomocí původního virtuálního disku.

6. Po spuštění nového virtuálního počítače budou konfigurační soubory, které jste vytvořili, aktualizovat heslo požadovaného uživatele.

> [!NOTE]
> Můžete automatizovat následující procesy:
>
> - Vytvoření virtuálního počítače pro řešení potíží
> - Připojení disku s operačním systémem
> - Opětovné vytvoření původního virtuálního počítače
> 
> Provedete to tak, že použijete [skripty pro obnovení virtuálních počítačů Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Pokud se rozhodnete použít skripty pro obnovení virtuálního počítače Azure, můžete v části podrobný postup použít následující postup:
> 1. Přeskočte kroky 1 a 2 pomocí skriptů k připojení disku s operačním systémem ovlivněného virtuálního počítače k virtuálnímu počítači pro obnovení.
> 2. Použijte postup 3 – 6 pro uplatnění rizik.
> 3. Přeskočte kroky 7 – 9 pomocí skriptů pro opětovné sestavení virtuálního počítače.
> 4. Postupujte podle kroků 10 a 11.

## <a name="detailed-steps-for-resource-manager"></a>Podrobný postup pro Správce prostředků

> [!NOTE]
> Tento postup se nevztahuje na řadiče domény systému Windows. Funguje pouze na samostatném serveru nebo na serveru, který je členem domény.

Před pokusem o provedení následujících kroků se vždycky pokuste resetovat heslo pomocí [Azure Portal nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Než začnete, ujistěte se, že máte zálohu svého virtuálního počítače. 

1. Odstraňte ovlivněný virtuální počítač v Azure Portal. Odstranění virtuálního počítače odstraní jenom metadata, referenční informace o virtuálním počítači v Azure. Virtuální disky se uchovávají při odstranění virtuálního počítače:
   
   * Vyberte virtuální počítač v Azure Portal klikněte na *Odstranit*:
     
     ![Odstranit existující virtuální počítač](./media/reset-local-password-without-agent/delete-vm.png)

2. Připojte disk s operačním systémem zdrojového virtuálního počítače k virtuálnímu počítači pro řešení potíží. Virtuální počítač pro řešení potíží se musí nacházet ve stejné oblasti jako disk s operačním systémem zdrojového virtuálního `West US`počítače (například):
   
   1. Vyberte virtuální počítač pro řešení potíží ve Azure Portal. Klikněte na *disky* | *připojit existující*:
     
     ![Připojit stávající disk](./media/reset-local-password-without-agent/disks-attach-existing.png)
     
   2. Vyberte *soubor VHD* a pak vyberte účet úložiště, který obsahuje váš zdrojový virtuální počítač:
     
     ![Vybrat účet úložiště](./media/reset-local-password-without-agent/disks-select-storage-account.png)
     
   3. Vyberte zdrojový kontejner. Zdrojový kontejner je obvykle *VHD*:
     
     ![Vybrat kontejner úložiště](./media/reset-local-password-without-agent/disks-select-container.png)
     
   4. Vyberte virtuální pevný disk s operačním systémem, který se má připojit. Pro dokončení procesu klikněte na tlačítko *Vybrat* :
     
     ![Vybrat zdrojový virtuální disk](./media/reset-local-password-without-agent/disks-select-source-vhd.png)

3. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy a ujistěte se, že je disk s operačním systémem zdrojového virtuálního počítače viditelný:
   
   1. Vyberte virtuální počítač pro řešení potíží v Azure Portal a klikněte na *připojit*.

   2. Otevřete soubor RDP, který se stáhne. Zadejte uživatelské jméno a heslo virtuálního počítače pro řešení potíží.

   3. V Průzkumníku souborů vyhledejte datový disk, který jste připojili. Pokud virtuální pevný disk virtuálního počítače je jediným datovým diskem připojeným k virtuálnímu počítači pro řešení potíží, měl by to být jednotka F:.
     
     ![Zobrazit připojený datový disk](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer.png)

4. Vytvořte `gpt.ini`na jednotce zdrojového virtuálního počítače (pokud existuje GPT. ini, přejmenujte na GPT. ini. bak): `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > Ujistěte se, že jste omylem nevytvořili následující soubory v C:\Windows, což je jednotka operačního systému pro virtuální počítač pro řešení potíží. Na jednotce operačního systému vytvořte následující soubory pro zdrojový virtuální počítač, který je připojený jako datový disk.
   
   * Do `gpt.ini` souboru, který jste vytvořili, přidejte následující řádky:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Vytvořit GPT. ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

5. Vytvořte `scripts.ini` v `\Windows\System32\GroupPolicy\Machines\Scripts\`. Ujistěte se, že jsou zobrazené skryté složky. V `Machine` případě potřeby vytvořte složky nebo `Scripts` .
   
   * Do `scripts.ini` souboru, který jste vytvořili, přidejte následující řádky:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Vytvoření skriptů. ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

6. `FixAzureVM.cmd` `<username>` Vytvořte pomocí následujícího obsahu, nahraďte a `<newpassword>` vlastními hodnotami: `\Windows\System32`
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Vytvoření FixAzureVM. cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Při definování nového hesla musíte splnit požadavky nakonfigurované složitosti hesla pro váš virtuální počítač.

7. V Azure Portal odpojte disk od virtuálního počítače pro řešení potíží:
   
   1. Vyberte virtuální počítač pro řešení potíží v Azure Portal klikněte na *disky*.

   2. Vyberte datový disk připojený v kroku 2, klikněte na *Odpojit*:
     
     ![Odpojit disk](./media/reset-local-password-without-agent/detach-disk.png)

8. Než vytvoříte virtuální počítač, získejte identifikátor URI na zdrojový disk s operačním systémem:
   
   1. Vyberte účet úložiště v Azure Portal klikněte na *objekty blob*.

   2. Vyberte kontejner. Zdrojový kontejner je obvykle *VHD*:
     
     ![Výběr objektu BLOB účtu úložiště](./media/reset-local-password-without-agent/select-storage-details.png)
     
   3. Vyberte zdrojový virtuální pevný disk s operačním systémem virtuálního počítače a klikněte na tlačítko *Kopírovat* vedle názvu *adresy URL* :
     
     ![Kopírovat identifikátor URI disku](./media/reset-local-password-without-agent/copy-source-vhd-uri.png)

9. Vytvořte virtuální počítač z disku s operačním systémem zdrojového virtuálního počítače:
   
   1. [Tuto šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) použijte k vytvoření virtuálního počítače ze specializovaného virtuálního pevného disku. Kliknutím na `Deploy to Azure` toto tlačítko otevřete Azure Portal s podrobnostmi, které jste vyplnili v šabloně.

   2. Pokud chcete zachovat všechna předchozí nastavení virtuálního počítače, vyberte *Upravit šablonu* a zadejte svou stávající virtuální síť, podsíť, síťový adaptér nebo veřejnou IP adresu.

   3. Do textového pole parametr vložte identifikátor URI vašeho zdrojového virtuálního pevného disku, který jste získali v předchozím kroku: `OSDISKVHDURI`
     
     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/create-new-vm-from-template.png)

10. Po spuštění nového virtuálního počítače se připojte k virtuálnímu počítači pomocí vzdálené plochy s novým heslem, které jste zadali ve `FixAzureVM.cmd` skriptu.

11. Z vzdálené relace k novému virtuálnímu počítači odeberte následující soubory pro vyčištění prostředí:
    
    * From %windir%\System32
      * odebrat FixAzureVM. cmd
    * From %windir%\System32\GroupPolicy\Machine\Scripts
      * odebrat skripty. ini
    * From %windir%\System32\GroupPolicy
      * Odstraňte GPT. ini (Pokud soubor GPT. ini existoval dříve a přejmenovali jste ho na GPT. ini. bak, přejmenujte soubor. bak zpátky na GPT. ini).

## <a name="detailed-steps-for-classic-vm"></a>Podrobný postup pro klasický virtuální počítač

> [!NOTE]
> Tento postup se nevztahuje na řadiče domény systému Windows. Funguje pouze na samostatném serveru nebo na serveru, který je členem domény.

Před pokusem o provedení následujících kroků se vždycky pokuste resetovat heslo pomocí [Azure Portal nebo Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) . Než začnete, ujistěte se, že máte zálohu svého virtuálního počítače. 

1. Odstraňte ovlivněný virtuální počítač v Azure Portal. Odstranění virtuálního počítače odstraní jenom metadata, referenční informace o virtuálním počítači v Azure. Virtuální disky se uchovávají při odstranění virtuálního počítače:
   
   * Vyberte virtuální počítač v Azure Portal a pak klikněte na *Odstranit*:
     
     ![Odstranit existující virtuální počítač](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Připojte disk s operačním systémem zdrojového virtuálního počítače k virtuálnímu počítači pro řešení potíží. Virtuální počítač pro řešení potíží se musí nacházet ve stejné oblasti jako disk s operačním systémem zdrojového virtuálního `West US`počítače (například):
   
   1. Vyberte virtuální počítač pro řešení potíží ve Azure Portal. Klikněte na *disky* | *připojit existující*:
     
      ![Připojit stávající disk](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Vyberte *soubor VHD* a pak vyberte účet úložiště, který obsahuje váš zdrojový virtuální počítač:
     
      ![Vybrat účet úložiště](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Zaškrtněte políčko *Zobrazit účty klasického úložiště*a pak vyberte zdrojový kontejner. Zdrojový kontejner je obvykle *VHD*:
     
      ![Vybrat kontejner úložiště](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Vybrat kontejner úložiště](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Vyberte virtuální pevný disk s operačním systémem, který se má připojit. Pro dokončení procesu klikněte na tlačítko *Vybrat* :
     
      ![Vybrat zdrojový virtuální disk](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Kliknutím na OK připojte disk.

      ![Připojit stávající disk](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy a ujistěte se, že je disk s operačním systémem zdrojového virtuálního počítače viditelný:

   1. Vyberte virtuální počítač pro řešení potíží v Azure Portal a klikněte na *připojit*.

   2. Otevřete soubor RDP, který se stáhne. Zadejte uživatelské jméno a heslo virtuálního počítače pro řešení potíží.

   3. V Průzkumníku souborů vyhledejte datový disk, který jste připojili. Pokud virtuální pevný disk virtuálního počítače je jediným datovým diskem připojeným k virtuálnímu počítači pro řešení potíží, měl by to být jednotka F:.
     
      ![Zobrazit připojený datový disk](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Vytvořte `gpt.ini` `gpt.ini` `gpt.ini.bak`v `\Windows\System32\GroupPolicy` jednotce zdrojového virtuálního počítače (pokud existuje, přejmenujte na):
   
   > [!WARNING]
   > Ujistěte se, že jste v `C:\Windows`nástroji nechtěně nevytvořili následující soubory, a to na jednotce operačního systému pro virtuální počítač pro řešení potíží. Na jednotce operačního systému vytvořte následující soubory pro zdrojový virtuální počítač, který je připojený jako datový disk.
   
   * Do `gpt.ini` souboru, který jste vytvořili, přidejte následující řádky:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Vytvořit GPT. ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Vytvořte `scripts.ini` v `\Windows\System32\GroupPolicy\Machines\Scripts\`. Ujistěte se, že jsou zobrazené skryté složky. V `Machine` případě potřeby vytvořte složky nebo `Scripts` .
   
   * Do `scripts.ini` souboru, který jste vytvořili, přidejte následující řádky:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Vytvoření skriptů. ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `FixAzureVM.cmd` `<username>` Vytvořte pomocí následujícího obsahu, nahraďte a `<newpassword>` vlastními hodnotami: `\Windows\System32`
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Vytvoření FixAzureVM. cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Při definování nového hesla musíte splnit požadavky nakonfigurované složitosti hesla pro váš virtuální počítač.

7. V Azure Portal odpojte disk od virtuálního počítače pro řešení potíží:
   
   1. Vyberte virtuální počítač pro řešení potíží v Azure Portal klikněte na *disky*.
   
   2. Vyberte datový disk připojený v kroku 2, klikněte na *Odpojit:* a pak klikněte na *OK*.

     ![Odpojit disk](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Odpojit disk](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Vytvořte virtuální počítač z disku s operačním systémem zdrojového virtuálního počítače:
   
     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Dokončení prostředí pro vytvoření virtuálního počítače

1. Po spuštění nového virtuálního počítače se připojte k virtuálnímu počítači pomocí vzdálené plochy s novým heslem, které jste zadali ve `FixAzureVM.cmd` skriptu.

2. Z vzdálené relace k novému virtuálnímu počítači odeberte následující soubory pro vyčištění prostředí:
    
    * Výsledkem`%windir%\System32`
      * odebrány`FixAzureVM.cmd`
    * Výsledkem`%windir%\System32\GroupPolicy\Machine\Scripts`
      * odebrány`scripts.ini`
    * Výsledkem`%windir%\System32\GroupPolicy`
      * odebrat `gpt.ini` (Pokud `gpt.ini` existovalo dřív a přejmenovali jste `gpt.ini.bak` `.bak` ho na, přejmenujte `gpt.ini`soubor zpátky na)

## <a name="next-steps"></a>Další postup
Pokud se stále nemůžete připojit pomocí vzdálené plochy, přečtěte si příručku pro [odstraňování potíží s RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Podrobný průvodce odstraňováním potíží s](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) protokolem RDP hledá místo konkrétních kroků postupy řešení potíží. Můžete také [otevřít žádost o podporu Azure](https://azure.microsoft.com/support/options/) pro praktickou pomoc.
