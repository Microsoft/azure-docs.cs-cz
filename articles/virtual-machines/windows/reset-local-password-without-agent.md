---
title: Resetovat heslo místního systému Windows bez agenta Azure | Microsoft Docs
description: Obnovení hesla místního uživatelského účtu systému Windows, když Azure hostovaného agenta, není nainstalován nebo je funkční na virtuálním počítači
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: iainfou
ms.openlocfilehash: ad892aee646b1a5f8c96d5bdeca24b7a0d88f38e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Obnovit heslo místního systému Windows pro virtuální počítač Azure v režimu offline
Můžete resetovat hesla místního systému Windows virtuálního počítače v Azure pomocí [portál Azure nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zadaný je nainstalován agent hosta Azure. Tato metoda je primární způsob, jak resetovat heslo pro virtuální počítač Azure. Pokud narazíte na potíže s agentem Azure hosta neodpovídá nebo je možné nainstalovat, nahráním vlastní image, můžete ručně obnovit heslo systému Windows. Tento článek popisuje, jak resetovat heslo místního účtu pomocí zdrojový OS virtuální disk se připojuje k jiným virtuálním Počítačem. Podle pokynů popsaných v tomto článku se nevztahují na řadiče domény systému Windows. 

> [!WARNING]
> Tento proces lze používejte pouze jako poslední možnost. Vždy se pokusí resetovat heslo pomocí [portál Azure nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) první.
> 
> 

## <a name="overview-of-the-process"></a>Přehled procesu
Základní kroky pro místní heslo resetovat pro virtuální počítač s Windows v Azure, když není k dispozici přístup k Azure hostovaného agenta je následující:

* Odstraňte zdrojový virtuální počítač. Virtuální disky se zachovají.
* Disk s operačním systémem zdrojového Virtuálního počítače připojte k jiným virtuálním Počítačem na stejné umístění v rámci vašeho předplatného Azure. Tento virtuální počítač se označuje jako řešení potíží virtuální počítač.
* Pomocí řešení potíží virtuální počítač, vytvořte některé konfigurační soubory na disku pro operační systém zdrojového Virtuálního počítače.
* Odpojte disk s operačním systémem Virtuálního počítače z virtuálního počítače, řešení potíží.
* Pomocí šablony Resource Manageru k vytvoření virtuálního počítače, pomocí původní virtuální disk.
* Když se nový virtuální počítač spustí, konfigurační soubory, které vytvoříte aktualizovat heslo požadovaného uživatele.

## <a name="detailed-steps"></a>Podrobné kroky

> [!NOTE]
> Kroky se nevztahují na řadiče domény systému Windows. Funguje pouze na samostatném serveru nebo server, který je členem domény.
> 
> 

Vždy se pokusí resetovat heslo pomocí [portál Azure nebo Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) než to zkusíte následující kroky. Ujistěte se, že máte zálohu virtuálního počítače, před zahájením. 

1. Odstraňte ovlivněné virtuální počítač na portálu Azure. Odstraňuje se virtuální počítač odstraní pouze metadata, odkaz na virtuální počítač v rámci Azure. Virtuální disky jsou uchovány při odstranění virtuálního počítače:
   
   * Vyberte virtuální počítač na portálu Azure, klikněte na *odstranit*:
     
     ![Odstraňte existující virtuální počítač](./media/reset-local-password-without-agent/delete_vm.png)
2. Disk s operačním systémem zdrojového Virtuálního počítače připojte k řešení potíží virtuální počítač. Řešení potíží virtuální počítač musí být ve stejné oblasti jako disk s operačním systémem zdrojového Virtuálního počítače (například `West US`):
   
   * Vyberte řešení potíží virtuální počítač na portálu Azure. Klikněte na tlačítko *disky* | *připojit existující*:
     
     ![Připojit stávající disk](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Vyberte *souboru virtuálního pevného disku* a potom vyberte účet úložiště, který obsahuje vaše zdrojového virtuálního počítače:
     
     ![Výběr účtu úložiště](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Vyberte kontejner zdroje. Kontejner zdroj je obvykle *virtuální pevné disky*:
     
     ![Vyberte kontejner úložiště](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Vyberte virtuální pevný disk operačního systému připojit. Klikněte na tlačítko *vyberte* dokončete proces:
     
     ![Vybrat zdroj virtuálního disku](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Připojení k řešení potíží virtuálního počítače pomocí vzdálené plochy a ujistěte se, že disk s operačním systémem zdrojového Virtuálního počítače je viditelná:
   
   * Vyberte řešení potíží virtuální počítač na portálu Azure a klikněte na tlačítko *Connect*.
   * Otevřete soubor RDP, který stahuje. Zadejte uživatelské jméno a heslo řešení potíží virtuálního počítače.
   * V Průzkumníku souborů vyhledejte datový disk, který jste připojili. Když zdroj virtuálního pevného disku Virtuálního počítače je pouze datový disk připojen k řešení potíží virtuální počítač, měla by být F: jednotky:
     
     ![Zobrazit připojené datový disk](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Vytvoření `gpt.ini` v `\Windows\System32\GroupPolicy` na jednotce zdrojového Virtuálního počítače (pokud existuje gpt.ini, přejmenujte gpt.ini.bak):
   
   > [!WARNING]
   > Ujistěte se, že nevytvoříte omylem následující soubory v C:\Windows, disk operačního systému pro řešení potíží virtuální počítač. Vytvořte tyto soubory na jednotce operačního systému pro vaše zdrojového virtuálního počítače, který je připojen jako datový disk.
   > 
   > 
   
   * Přidejte následující řádky do `gpt.ini` souborů, které jste vytvořili:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Vytvoření gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Vytvoření `scripts.ini` v `\Windows\System32\GroupPolicy\Machine\Scripts`. Ujistěte se, že se zobrazují skrytých složek. V případě potřeby vytvořte `Machine` nebo `Scripts` složek.
   
   * Přidejte následující řádky `scripts.ini` souborů, které jste vytvořili:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Vytvoření scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Vytvoření `FixAzureVM.cmd` v `\Windows\System32` s následující obsah, nahraďte `<username>` a `<newpassword>` vlastními hodnotami:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Při definování nové heslo, musí splňovat požadavky na složitost nakonfigurovaným heslem pro váš virtuální počítač.
7. Na portálu Azure odpojte disk z virtuálního počítače, řešení potíží:
   
   * Vyberte řešení potíží virtuální počítač na portálu Azure, klikněte na *disky*.
   * Klikněte na tlačítko připojit datový disk v kroku 2, vyberte *odpojení*:
     
     ![Odpojte disk](./media/reset-local-password-without-agent/detach_disk.png)
8. Než vytvoříte virtuální počítač, získejte identifikátor URI pro váš zdrojový disk operačního systému:
   
   * Vyberte účet úložiště na portálu Azure, klikněte na tlačítko *objekty BLOB*.
   * Vyberte kontejner. Kontejner zdroj je obvykle *virtuální pevné disky*:
     
     ![Vyberte účet úložiště objektů blob](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Vyberte zdroj virtuální pevný disk operačního systému virtuálního počítače a klikněte na tlačítko *kopie* vedle položky *URL* název:
     
     ![Zkopírujte disk identifikátor URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Vytvoření virtuálního počítače z disku operačního systému zdrojového Virtuálního počítače:
   
   * Použití [této šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) vytvoření virtuálního počítače z specializované virtuálního pevného disku. Klikněte `Deploy to Azure` tlačítko otevřete portál Azure s použitím šablon podrobnosti vyplní za vás.
   * Pokud chcete zachovat předchozí nastavení pro virtuální počítač, vyberte *úpravy šablony* zajistit existující virtuální síť, podsíť, síťový adaptér nebo veřejné IP adresy.
   * V `OSDISKVHDURI` parametr textového pole, vložte identifikátor URI virtuálního pevného disku zdrojového získat v předchozím kroku:
     
     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Po spuštění nového virtuálního počítače připojit k virtuálnímu počítači pomocí vzdálené plochy s nové heslo, které jste zadali v `FixAzureVM.cmd` skriptu.
11. Ze vzdálené relace do nového virtuálního počítače odeberte tyto soubory do vyčištění prostředí:
    
    * Z %windir%\System32
      * remove FixAzureVM.cmd
    * From %windir%\System32\GroupPolicy\Machine\
      * odebrat scripts.ini
    * From %windir%\System32\GroupPolicy
      * Odeberte gpt.ini (pokud existoval gpt.ini a přejmenoval jej na gpt.ini.bak, přejmenujte soubor .bak zpět do gpt.ini)

## <a name="next-steps"></a>Další postup
Pokud se pořád nemůžete připojit pomocí vzdálené plochy, přečtěte si téma [Průvodci odstraňováním potíží RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Podrobný Průvodce odstraňováním potíží s RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vypadá na řešení potíží s metody, nikoli konkrétní kroky. Můžete také [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/options/) praktických pomoc.

