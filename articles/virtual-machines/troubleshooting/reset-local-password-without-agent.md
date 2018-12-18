---
title: Resetování místního hesla Windows bez agenta Azure | Dokumentace Microsoftu
description: Jak resetovat heslo místního uživatelského účtu Windows, pokud agent hosta Azure není nainstalovaná nebo funkční na virtuálním počítači
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 31e675b101d903af5dd4a07fee3bc56fbc3353d9
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412784"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetování místního hesla Windows pro virtuální počítač Azure do offline režimu
Můžete resetovat hesla Windows místního virtuálního počítače v Azure s využitím [webu Azure portal nebo Azure Powershellu](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) předpokladu, že je nainstalovaný agent hosta Azure. Tato metoda je primárním způsob, jak resetování hesla pro virtuální počítač Azure. Pokud narazíte na problémy s neodpovídajícím agentem hosta Azure nebo selhání instalace po nahrání vlastní image, můžete ručně resetovat hesla Windows. Tento článek podrobně popisuje, jak resetovat heslo místního účtu připojením zdrojový OS virtuální disk k jinému virtuálnímu počítači. Podle kroků popsaných v tomto článku se nevztahují na řadiče domény Windows. 

> [!WARNING]
> Tento postup lze používejte pouze jako poslední možnost. Vždy se pokusí resetovat heslo pomocí [webu Azure portal nebo Azure Powershellu](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) první.
> 
> 

## <a name="overview-of-the-process"></a>Přehled procesu
Základní kroky pro místní heslo resetoval virtuálního počítače s Windows v Azure, když není k dispozici přístup k agentovi hosta Azure vypadá takto:

* Odstraňte zdrojový virtuální počítač. Virtuální disky zůstanou zachovány.
* Disk s operačním systémem zdrojového Virtuálního počítače připojte k jinému virtuálnímu počítači na stejném umístění v rámci vašeho předplatného Azure. Tento virtuální počítač se označuje jako řešení potíží virtuální počítač.
* Pomocí řešení potíží virtuálního počítače, vytvořte několik konfiguračních souborů na disk s operačním systémem zdrojového Virtuálního počítače.
* Odpojte disk s operačním systémem Virtuálního počítače z virtuálního počítače pro řešení potíží.
* Pomocí šablony Resource Manageru k vytvoření virtuálního počítače, pomocí původního virtuálního disku.
* Při spuštění nového virtuálního počítače, aktualizovat konfigurační soubory, které vytvoříte heslo požadovaného uživatele.

## <a name="detailed-steps"></a>Podrobné kroky

> [!NOTE]
> Kroky se nevztahují na řadiče domény Windows. Funguje pouze na samostatném serveru nebo serveru, který je členem domény.
> 
> 

Vždy se pokusí resetovat heslo pomocí [webu Azure portal nebo Azure Powershellu](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) před pokusem o následující kroky. Ujistěte se, že je potřeba mít zálohu virtuálního počítače, než začnete. 

1. Odstraňte ovlivněných virtuálních počítačů na webu Azure portal. Odstraňuje se virtuální počítač odstraní pouze metadata a odkaz na virtuální počítač v rámci Azure. Virtuální disky se zachovají i po odstranění virtuálního počítače:
   
   * Vyberte virtuální počítač na webu Azure Portal, klikněte na tlačítko *odstranit*:
     
     ![Odstranění existujícího virtuálního počítače](./media/reset-local-password-without-agent/delete_vm.png)
2. Připojte disk s operačním systémem zdrojového Virtuálního počítače k řešení problémů s virtuálnímu počítači. Řešení potíží virtuální počítač musí být ve stejné oblasti jako disk s operačním systémem zdrojového Virtuálního počítače (například `West US`):
   
   * Vyberte řešení potíží virtuální počítač na webu Azure Portal. Klikněte na tlačítko *disky* | *připojit existující*:
     
     ![Připojit stávající disk](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Vyberte *souboru virtuálního pevného disku* a pak vyberte účet úložiště, který obsahuje váš zdrojový virtuální počítač:
     
     ![Výběr účtu úložiště](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Vyberte zdrojového kontejneru. Zdrojového kontejneru je obvykle *virtuální pevné disky*:
     
     ![Vyberte kontejner úložiště](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Vyberte virtuální pevný disk operačního systému připojit. Klikněte na tlačítko *vyberte* dokončete proces:
     
     ![Vybrat zdroj virtuálního disku](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Připojení k řešení problémů s virtuálnímu počítači pomocí vzdálené plochy a ujistěte se, že je viditelná disk s operačním systémem zdrojového Virtuálního počítače:
   
   * Vyberte řešení potíží virtuální počítač na webu Azure Portal a klikněte na tlačítko *připojit*.
   * Otevřete soubor RDP, který stahuje. Zadejte uživatelské jméno a heslo virtuálního počítače pro řešení potíží.
   * V Průzkumníku souborů vyhledejte datový disk, který jste připojili. Pokud se zdrojový virtuální pevný disk pouze datový disk připojený k virtuálnímu počítači pro řešení potíží, je třeba F: jednotky:
     
     ![Zobrazení připojených datových disků](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Vytvoření `gpt.ini` v `\Windows\System32\GroupPolicy` na jednotku zdrojového Virtuálního počítače (pokud existuje gpt.ini, přejmenovat na gpt.ini.bak):
   
   > [!WARNING]
   > Ujistěte se, že nevytvoříte omylem následující soubory v C:\Windows jednotky operačního systému pro řešení potíží virtuální počítač. Vytvořte tyto soubory na jednotce operačního systému pro zdrojový virtuální počítač, který je připojený jako datový disk.
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
5. Vytvoření `scripts.ini` v `\Windows\System32\GroupPolicy\Machine\Scripts\Startup`. Zajistěte, aby skryté složky jsou zobrazeny. V případě potřeby vytvořte `Machine` nebo `Scripts` složky.
   
   * Přidejte následující řádky `scripts.ini` souborů, které jste vytvořili:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Vytvoření scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Vytvoření `FixAzureVM.cmd` v `\Windows\System32` s použitím následujícího obsahu nahrazení `<username>` a `<newpassword>` vlastními hodnotami:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Vytvoření FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Při definování nové heslo, musí splňovat požadavky na složitost hesla nakonfigurované pro váš virtuální počítač.
7. Na webu Azure portal odpojte disk od virtuálního počítače pro řešení potíží:
   
   * Vyberte řešení potíží virtuální počítač na webu Azure Portal, klikněte na tlačítko *disky*.
   * Vyberte datový disk připojený v kroku 2, klikněte na tlačítko *odpojit*:
     
     ![Odpojení disku](./media/reset-local-password-without-agent/detach_disk.png)
8. Před vytvořením virtuálního počítače, získejte identifikátor URI pro zdrojový disk s operačním systémem:
   
   * Vyberte účet úložiště na webu Azure Portal, klikněte na tlačítko *objekty BLOB*.
   * Vyberte kontejner. Zdrojového kontejneru je obvykle *virtuální pevné disky*:
     
     ![Vyberte objekt blob účtu úložiště](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Vybrat zdroj virtuálního pevného disku virtuálního počítače a klikněte na tlačítko *kopírování* vedle *URL* název:
     
     ![Zkopírujte disk identifikátoru URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Vytvoření virtuálního počítače z disku s operačním systémem zdrojového Virtuálního počítače:
   
   Použití [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) vytvoření virtuálního počítače ze specializovaného VHD. Klikněte na tlačítko `Deploy to Azure` tlačítko Otevřít na webu Azure portal s podrobnostmi bez vizuálního vzhledu za vás.
   * Pokud chcete zachovat všechny předchozí nastavení pro virtuální počítač, vyberte *úpravy šablony* poskytovat existující virtuální síť, podsíť, síťový adaptér nebo veřejnou IP adresu.
   * V `OSDISKVHDURI` parametr textového pole, vložit získat identifikátor URI zdroje virtuální pevný disk v předchozím kroku:
     
     ![Vytvoření virtuálního počítače ze šablony](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Po spuštění nového virtuálního počítače připojit k virtuálnímu počítači pomocí vzdálené plochy s novým heslem, které jste zadali v `FixAzureVM.cmd` skriptu.
11. Ze vzdálené relace pro nový virtuální počítač odeberte tyto soubory do vyčistit prostředí:
    
    * Z %windir%\System32
      * odebrat FixAzureVM.cmd
    * Z %windir%\System32\GroupPolicy\Machine\
      * odebrat scripts.ini
    * Z %windir%\System32\GroupPolicy
      * Odeberte gpt.ini (pokud existoval gpt.ini a přejmenoval jej na gpt.ini.bak, přejmenujte soubor .bak zpět do gpt.ini)

## <a name="next-steps"></a>Další postup
Pokud se pořád nemůžete připojit pomocí vzdálené plochy, přečtěte si článek [Průvodce odstraňováním potíží RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Podrobný průvodce řešením potíží s RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vypadá na odstraňování potíží u metody spíše než konkrétní kroky. Můžete také [otevřete požadavek na podporu Azure](https://azure.microsoft.com/support/options/) praktickou pomoc.

