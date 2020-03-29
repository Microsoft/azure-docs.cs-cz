---
title: Resetování místního hesla systému Windows bez agenta Azure | Dokumenty společnosti Microsoft
description: Jak obnovit heslo místního uživatelského účtu systému Windows, když agent hosta Azure není nainstalovaný nebo funguje na virtuálním počítači
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
ms.openlocfilehash: becbf88aeda164f7d916cbc1f1ace89262cc1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921619"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetování místního hesla Windows pro offline virtuální počítač Azure
Místní heslo pro Windows virtuálního počítače v Azure můžete resetovat pomocí [portálu Azure nebo Azure PowerShellu](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) za předpokladu, že je nainstalovaný agent hosta Azure. Tato metoda je primární způsob, jak obnovit heslo pro virtuální počítač Azure. Pokud narazíte na problémy s agentem hosta Azure neodpovídá nebo nepodaří nainstalovat po nahrání vlastní image, můžete ručně obnovit heslo systému Windows. Tento článek podrobně popisuje, jak obnovit heslo místního účtu připojením zdrojového virtuálního disku operačního systému k jinému virtuálnímu počítači. Kroky popsané v tomto článku se nevztahují na řadiče domény systému Windows. 

> [!WARNING]
> Tento postup použijte až jako poslední možnost. Vždy se nejprve pokuste obnovit heslo pomocí [portálu Azure nebo Azure PowerShellu.](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="overview-of-the-process"></a>Přehled procesu
Základní kroky pro provedení místního resetování hesla pro virtuální počítač s Windows v Azure, když neexistuje žádný přístup k agentovi hosta Azure, jsou následující:

1. Zastavte ovlivněný virtuální virtuální ms.
1. Vytvořte snímek pro disk operačního systému virtuálního počítače.
1. Vytvořte kopii disku operačního systému ze snímku.
1. Připojte a připojte zkopírovaný disk operačního systému k jinému virtuálnímu počítači systému Windows a vytvořte na disku některé konfigurační soubory. Soubory vám pomohou obnovit heslo.
1. Odpojte a odpojte zkopírovaný disk operačního systému od virtuálního počítače pro řešení potíží.
1. Zaměnujte disk operačního systému za ovlivněný virtuální modul.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Podrobné kroky pro virtuální počítače s nasazením Správce prostředků

> [!NOTE]
> Tyto kroky se nevztahují na řadiče domény systému Windows. Funguje pouze na samostatném serveru nebo serveru, který je členem domény.

Vždy se pokuste obnovit heslo pomocí [portálu Azure nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před vyzkoušení následujících kroků. Ujistěte se, že máte zálohu virtuálního počítače, než začnete.

1. Pořiďte snímek pro disk operačního systému postiženého virtuálního počítače, vytvořte disk ze snímku a pak připojte disk k virtuálnímu virtuálnímu počítače pro řešení potíží. Další informace najdete [v tématu Poradce při potížích s virtuálním počítačem s Windows připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí portálu Azure](troubleshoot-recovery-disks-portal-windows.md).
2. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy.
3. Vytvořit `gpt.ini` `\Windows\System32\GroupPolicy` na disku zdrojového virtuálního počítači (pokud gpt.ini existuje, přejmenujte na gpt.ini.bak):
   
   > [!WARNING]
   > Ujistěte se, že neomylem nevytvoříte následující soubory v C:\Windows, jednotce operačního systému pro řešení potíží s virtuálním počítačům. Vytvořte následující soubory na jednotce operačního systému pro zdrojový virtuální počítač, který je připojený jako datový disk.
   
   * Do vytvořeného souboru `gpt.ini` přidejte následující řádky:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Vytvořit soubor gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Vytvořit `scripts.ini` `\Windows\System32\GroupPolicy\Machines\Scripts\`v . Zkontrolujte, zda jsou zobrazeny skryté složky. V případě potřeby `Machine` `Scripts` vytvořte složky nebo.
   
   * Přidejte následující `scripts.ini` řádky souboru, který jste vytvořili:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Vytvoření souboru scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. `FixAzureVM.cmd` Vytvořte `\Windows\System32` se s následujícím `<username>` obsahem, který nahradí a `<newpassword>` s vlastními hodnotami:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Vytvořit fixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Při definování nového hesla musíte splňovat nakonfigurované požadavky na složitost hesla pro váš virtuální počítač.

6. Na webu Azure Portal odpojte disk od virtuálního počítače pro řešení potíží.

7. [Změňte disk operačního systému pro ohrožený virtuální modul](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Po spuštění nového virtuálního počítače se připojte k virtuálnímu počítači `FixAzureVM.cmd` pomocí vzdálené plochy s novým heslem, které jste zadali ve skriptu.

9. Odeberete ze vzdálené relace do nového virtuálního počítače následující soubory, abyste vyčistili prostředí:
    
    * Z %windir%\System32
      * odebrání souboru FixAzureVM.cmd
    * Z %windir%\System32\GroupPolicy\Machine\Scripts
      * odebrání souboru scripts.ini
    * Z %windir%\System32\GroupPolicy
      * odstranit gpt.ini (pokud gpt.ini existoval dříve, a přejmenoval i na gpt.ini.bak, přejmenujte soubor .bak zpět na gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Podrobné kroky pro klasický virtuální účet

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Tyto kroky se nevztahují na řadiče domény systému Windows. Funguje pouze na samostatném serveru nebo serveru, který je členem domény.

Vždy se pokuste obnovit heslo pomocí [portálu Azure nebo Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) před vyzkoušení následujících kroků. Ujistěte se, že máte zálohu virtuálního počítače, než začnete. 

1. Odstraňte ovlivněný virtuální počítač na webu Azure Portal. Odstranění virtuálního počítače pouze odstraní metadata, odkaz na virtuální počítač v rámci Azure. Virtuální disky se ponechají při odstranění virtuálního počítače:
   
   * Vyberte virtuální počítač na webu Azure portal a klikněte na *Odstranit*:
     
     ![Odstranění existujícího virtuálního montova.](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Připojte zdrojový disk operačního systému virtuálního počítače k virtuálnímu počítače pro řešení potíží. Řešení potíží s virtuálním počítačem musí být ve stejné oblasti jako `West US`zdrojový disk s os v os virtuálního počítače (například):
   
   1. Vyberte virtuální počítač pro řešení potíží na webu Azure Portal. Klikněte na *Disky* | *Připojit existující*:
     
      ![Připojení existujícího disku](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Vyberte *Soubor Virtuálního pevného disku* a pak vyberte účet úložiště, který obsahuje zdrojový virtuální počítač:
     
      ![Výběr účtu úložiště](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Zaškrtněte políčko *Zobrazit klasické účty úložiště*a vyberte zdrojový kontejner. Zdrojový kontejner je obvykle *vhds*:
     
      ![Výběr kontejneru úložiště](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Výběr kontejneru úložiště](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Vyberte operační systém vhd připojit. Chcete-li proces dokončit, klepněte na *tlačítko Vybrat:*
     
      ![Vybrat zdrojový virtuální disk](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Klepnutím na tlačítko Ok připojte disk.

      ![Připojení existujícího disku](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy a ujistěte se, že je viditelný zdrojový disk s os v os virtuálního počítače:

   1. Vyberte virtuální počítač pro řešení potíží na webu Azure Portal a klikněte na *Připojit*.

   2. Otevřete soubor RDP, který se stáhne. Zadejte uživatelské jméno a heslo virtuálního počítače pro řešení potíží.

   3. V Průzkumníkovi souborů vyhledejte datový disk, který jste připojili. Pokud je virtuální pevný disk zdrojového virtuálního počítače jediným datovým diskem připojeným k virtuálnímu počítače pro řešení potíží, měl by to být jednotka F:
     
      ![Zobrazit připojený datový disk](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Vytvořit `gpt.ini` `\Windows\System32\GroupPolicy` na zdrojové montovně `gpt.ini` virtuálního počítači `gpt.ini.bak`(pokud existuje, přejmenujte na ):
   
   > [!WARNING]
   > Ujistěte se, že jste omylem `C:\Windows`nevytvořili následující soubory v jednotce operačního systému pro řešení potíží s virtuálním montovnam. Vytvořte následující soubory na jednotce operačního systému pro zdrojový virtuální počítač, který je připojený jako datový disk.
   
   * Do vytvořeného souboru `gpt.ini` přidejte následující řádky:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Vytvořit soubor gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Vytvořit `scripts.ini` `\Windows\System32\GroupPolicy\Machines\Scripts\`v . Zkontrolujte, zda jsou zobrazeny skryté složky. V případě potřeby `Machine` `Scripts` vytvořte složky nebo.
   
   * Přidejte následující `scripts.ini` řádky souboru, který jste vytvořili:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Vytvoření souboru scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `FixAzureVM.cmd` Vytvořte `\Windows\System32` se s následujícím `<username>` obsahem, který nahradí a `<newpassword>` s vlastními hodnotami:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Vytvořit fixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Při definování nového hesla musíte splňovat nakonfigurované požadavky na složitost hesla pro váš virtuální počítač.

7. Na webu Azure Portal odpojte disk od virtuálního počítače pro řešení potíží:
   
   1. Vyberte virtuální počítač pro řešení potíží na webu Azure Portal a klikněte na *Disky*.
   
   2. Vyberte datový disk připojený v kroku 2, klepněte na **odpojit**a potom klepněte na tlačítko **OK**.

     ![Odpojit disk](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Odpojit disk](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Vytvořte virtuální virtuální počítače ze zdrojového disku s os virtuálním počítačem:
   
     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Dokončení prostředí vytváření virtuálních strojů

1. Po spuštění nového virtuálního počítače se připojte k virtuálnímu počítači `FixAzureVM.cmd` pomocí vzdálené plochy s novým heslem, které jste zadali ve skriptu.

2. Odeberete ze vzdálené relace do nového virtuálního počítače následující soubory, abyste vyčistili prostředí:
    
    * Z`%windir%\System32`
      * Odebrat`FixAzureVM.cmd`
    * Z`%windir%\System32\GroupPolicy\Machine\Scripts`
      * Odebrat`scripts.ini`
    * Z`%windir%\System32\GroupPolicy`
      * `gpt.ini` odebrat `gpt.ini` (pokud existovaldříve a přejmenovat `gpt.ini.bak`jej na `.bak` , `gpt.ini`přejmenujte soubor zpět na )

## <a name="next-steps"></a>Další kroky
Pokud se stále nemůžete připojit pomocí vzdálené plochy, přečtěte si [příručku pro řešení potíží s protokolem RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Podrobný průvodce odstraňováním potíží s rdp](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se zabývá metodami řešení potíží spíše než konkrétními kroky. Můžete také [otevřít žádost o podporu Azure](https://azure.microsoft.com/support/options/) pro praktickou pomoc.
