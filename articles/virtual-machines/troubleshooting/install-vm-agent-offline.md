---
title: Instalace agenta virtuálního počítače Azure do offline režimu | Microsoft Docs
description: Přečtěte si, jak nainstalovat agenta virtuálního počítače Azure do offline režimu.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002608"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalace agenta virtuálního počítače Azure v offline režimu 

Agent virtuálního počítače Azure (agent virtuálního počítače) poskytuje užitečné funkce, jako je například resetování hesla místního správce a vkládání skriptu. V tomto článku se dozvíte, jak nainstalovat agenta virtuálního počítače pro offline virtuální počítač s Windows (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kdy použít agenta virtuálního počítače v offline režimu

V následujících scénářích nainstalujte agenta virtuálního počítače do offline režimu:

- Nasazený virtuální počítač Azure nemá nainstalovaného agenta virtuálního počítače nebo agent nefunguje.
- Zapomněli jste heslo správce pro virtuální počítač nebo nemůžete získat přístup k virtuálnímu počítači.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Postup instalace agenta virtuálního počítače v offline režimu

Pomocí následujících kroků nainstalujete agenta virtuálního počítače do offline režimu.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1: Připojte disk s operačním systémem virtuálního počítače k jinému virtuálnímu počítači jako datový disk.

1. Položte si snímek disku s operačním systémem ovlivněného virtuálního počítače, vytvořte disk ze snímku a pak ho připojte k virtuálnímu počítači pro odstraňování potíží. Další informace najdete v tématu [řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal](troubleshoot-recovery-disks-portal-windows.md). Pro klasický virtuální počítač odstraňte virtuální počítač a ponechejte disk s operačním systémem a připojte disk s operačním systémem k virtuálnímu počítači pro odstraňování potíží.

2.  Připojte se k virtuálnímu počítači poradce při potížích. Spusťte správu disků v **nástroji Správa počítače**  >  **Disk management**. Potvrďte, že je disk s operačním systémem online a že se k diskovým oddílům přiřazují písmena jednotek.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2: Úprava disku s operačním systémem pro instalaci agenta virtuálního počítače Azure

1.  Vytvořte připojení ke vzdálené ploše k virtuálnímu počítači poradce při potížích.

2.  Na virtuálním počítači poradce při potížích přejděte na disk s operačním systémem, který jste připojili, a otevřete složku \Windows\System32\Config. Zkopírujte všechny soubory v této složce jako zálohu pro případ, že je vyžadováno vrácení zpět.

3.  Spusťte **Editor registru** (regedit.exe).

4.  Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **File**  >  **podregistr Loading** File:

    ![Načtení podregistru](./media/install-vm-agent-offline/load-hive.png)

5.  Přejděte do složky \windows\system32\config\SYSTEM na disku s operačním systémem, který jste připojili. Jako název podregistru zadejte **BROKENSYSTEM**. Nový podregistr registru se zobrazí pod klíčem **HKEY_LOCAL_MACHINE** .

6.  Přejděte do složky \windows\system32\config\SOFTWARE na disku s operačním systémem, který jste připojili. Jako název softwaru pro podregistr zadejte **BROKENSOFTWARE**.

7. Pokud je na připojeném disku s operačním systémem nainstalovaný agent virtuálního počítače, proveďte zálohu aktuální konfigurace. Pokud není nainstalovaný agent virtuálního počítače, přejděte k dalšímu kroku.
      
    1. Přejmenování složky \windowsazure na \windowsazure.old.

    2. Exportujte následující Registry:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Použijte existující soubory na virtuálním počítači poradce při potížích jako úložiště pro instalaci agenta virtuálního počítače. Dokončete následující kroky:

    1. Z virtuálního počítače Poradce při potížích exportujte následující podklíče ve formátu registru (. reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportujte podklíče registru.](./media/install-vm-agent-offline/backup-reg.png)

    2. Upravte soubory registru. V každém souboru změňte **systém** hodnoty položky na **BROKENSYSTEM** (jak je znázorněno na následujících obrázcích) a uložte soubor. Zapamatujte si **ImagePath** aktuálního agenta virtuálního počítače. Bude nutné zkopírovat odpovídající složku na připojený disk s operačním systémem. 

        ![Změna hodnot podklíčů registru](./media/install-vm-agent-offline/change-reg.png)

    3. Naimportujte soubory registru do úložiště dvojitým kliknutím na každý soubor registru.

    4. Potvrďte, že následující dva podklíče byly úspěšně importovány do podregistru **BROKENSYSTEM** :
        - WindowsAzureGuestAgent
        - RdAgent

    5. Zkopírujte instalační složku aktuálního agenta virtuálního počítače do připojeného disku s operačním systémem: 

        1.  Na disku s operačním systémem, který jste připojili, vytvořte v kořenové cestě složku s názvem WindowsAzure.

        2.  Na virtuálním počítači poradce při potížích C:\WindowsAzure vyhledejte všechny složky s názvem C:\WindowsAzure\ GuestAgent_X. X. XXXX. XXX. Zkopírujte složku GuestAgent, která má nejnovější číslo verze z C:\WindowsAzure do složky WindowsAzure na připojeném disku s operačním systémem. Pokud si nejste jistí, která složka se má zkopírovat, zkopírujte všechny složky GuestAgent. Následující obrázek ukazuje příklad složky GuestAgent, která se kopíruje na připojený disk s operačním systémem.

             ![Kopírovat složku GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Vyberte **BROKENSYSTEM**. V nabídce vyberte **soubor**  >  **Uvolnit podregistr**.

10.  Vyberte **BROKENSOFTWARE**. V nabídce vyberte **soubor**  >  **Uvolnit podregistr**.

11.  Odpojte disk s operačním systémem a pak [změňte disk s operačním systémem ovlivněného virtuálního počítače](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Pro klasický virtuální počítač vytvořte nový virtuální počítač pomocí opraveného disku s operačním systémem.

12.  Přístup k virtuálnímu počítači. Všimněte si, že RdAgent je spuštěný a že se generují protokoly.

Pokud jste virtuální počítač vytvořili pomocí modelu nasazení Správce prostředků, jste hotovi.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Pro klasické virtuální počítače použijte vlastnost ProvisionGuestAgent.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Pokud jste virtuální počítač vytvořili pomocí klasického modelu, aktualizujte vlastnost **ProvisionGuestAgent** pomocí modulu Azure PowerShell. Vlastnost informuje Azure o tom, že je na virtuálním počítači nainstalovaný agent virtuálního počítače.

Chcete-li nastavit vlastnost **ProvisionGuestAgent** , spusťte v Azure PowerShell následující příkazy:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Pak spusťte `Get-AzureVM` příkaz. Všimněte si, že vlastnost **GuestAgentStatus** je nyní naplněna daty:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Další kroky

- [Přehled agenta virtuálního počítače Azure](../extensions/agent-windows.md)
- [Rozšíření a funkce virtuálních počítačů pro Windows](../extensions/features-windows.md)
