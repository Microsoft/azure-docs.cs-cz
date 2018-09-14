---
title: Nainstalujte agenta virtuálního počítače Azure v režimu offline | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat agenta virtuálního počítače Azure v režimu offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: genli
ms.openlocfilehash: 48f6a650fab9ad11607834f2dfe3de873c519786
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544077"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Nainstalujte agenta virtuálního počítače Azure v režimu offline 

Agent virtuálního počítače Azure (VM Agent) poskytuje užitečných funkcí, jako je například resetování hesla místního správce a skript doručením (push). V tomto článku se dozvíte, jak nainstalovat agenta virtuálního počítače pro offline Windows virtuální počítač (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kdy použít agenta virtuálního počítače v offline režimu

Nainstalujte agenta virtuálního počítače v režimu offline v následujících scénářích:

- Nasazený virtuální počítač Azure nemá nainstalovaného agenta virtuálního počítače nebo agenta, ale nefunguje.
- Zapomněli jste heslo správce pro virtuální počítač nebo virtuální počítač nejde získat přístup.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Jak nainstalovat agenta virtuálního počítače v offline režimu

Následujícím postupem nainstalujte agenta virtuálního počítače v režimu offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1: Připojte disk s operačním systémem virtuálního počítače jako datový disk k jinému virtuálnímu počítači

1.  Odstranění virtuálního počítače. Je nutné vybrat **zachovat disky** možnost odstranění virtuálního počítače.

2.  Připojte disk s operačním systémem jako datový disk k jinému virtuálnímu počítači (označované jako _Poradce při potížích_ virtuálního počítače). Další informace najdete v tématu [připojení datového disku k virtuálnímu počítači s Windows na webu Azure Portal](attach-managed-disk-portal.md).

3.  Připojte se k Poradce při potížích se virtuální počítač. Otevřít **Správa počítače** > **nástroji Správa disků**. Potvrďte, že disk s operačním systémem je online a že písmena jednotek jsou přiřazeny k diskové oddíly.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2: Úprava disk s operačním systémem k instalaci agenta virtuálního počítače Azure

1.  Poradce při potížích se virtuální počítač vytvořte připojení ke vzdálené ploše.

2.  Na disk s operačním systémem, který jste připojili přejděte do složky \windows\system32\config. Zkopírujte všechny soubory v této složce jako zálohu, v případě, že vrácení zpět je povinný.

3.  Spustit **Editor registru** (regedit.exe).

4.  Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **souboru** > **načíst Hive**:

    ![Načíst hive](./media/install-vm-agent-offline/load-hive.png)

5.  Přejděte do složky \windows\system32\config\SYSTEM na disk s operačním systémem, který jste připojili. Název hive zadejte **BROKENSYSTEM**. Nový registr hive se zobrazuje v části **HKEY_LOCAL_MACHINE** klíč.

6.  Přejděte do složky \windows\system32\config\SOFTWARE na disk s operačním systémem, který jste připojili. Název softwaru hive zadejte **BROKENSOFTWARE**.

7. Pokud disk s operačním systémem připojené je nainstalovaný agent virtuálního počítače, proveďte zálohu aktuální konfiguraci. Pokud nemá nainstalovaného agenta virtuálního počítače, přesune k dalšímu kroku.
      
    1. Přejmenujte složku \windowsazure na \windowsazure.old.

    2. Exportujte následujících registrů:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Použijte existující soubory na Poradce při potížích se virtuální počítač jako úložiště pro instalaci agenta virtuálního počítače. Proveďte následující kroky:

    1. Z Poradce při potížích se virtuální počítač exportujte následující podklíče registru formát (.reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

        ![Export podklíče registru](./media/install-vm-agent-offline/backup-reg.png)

    2. Úpravy souborů registru. Do každého souboru, změňte hodnotu položky **systému** k **BROKENSYSTEM** (jak je znázorněno na následujících obrázcích) a soubor uložte. Mějte na paměti **ImagePath** aktuální agenta virtuálního počítače. Budeme muset zkopírovat odpovídající složky na připojeném disku s operačním systémem. 

        ![Změňte hodnoty podklíče registru](./media/install-vm-agent-offline/change-reg.png)

    3. Naimportujte soubory registru do úložiště dvojitým kliknutím každého souboru registru.

    4. Ověřte, zda jsou úspěšně importovány do následující tři podklíče **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Zkopírujte složku instalace aktuální agenta virtuálního počítače na připojeném disku s operačním systémem: 

        1.  Na disk s operačním systémem, který jste připojili vytvořte složku s názvem Windows Azure v kořenové cestě.

        2.  Přejděte na C:\WindowsAzure na Poradce při potížích se virtuální počítač, podívejte se pro všechny složky s názvem C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Zkopírujte složku GuestAgent, který má nejnovější číslo verze C:\WindowsAzure do složky Windows Azure v připojeném disku s operačním systémem. Pokud si nejste jisti, složky, která by se měly zkopírovat, zkopírujte všechny složky GuestAgent. Následující obrázek ukazuje příklad GuestAgent složky, která je zkopírován do připojeného disku s operačním systémem.

             ![Zkopírujte složku GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Vyberte **BROKENSYSTEM**. V nabídce vyberte **souboru** > **Uvolnit podregistr**.

10.  Vyberte **BROKENSOFTWARE**. V nabídce vyberte **souboru** > **Uvolnit podregistr**.

11.  Odpojit disk s operačním systémem a pak znovu vytvořte virtuální počítač s použitím disku s operačním systémem.

12.  Přístup k virtuálnímu počítači. Všimněte si, že je spuštěna RdAgent a se generují protokoly.

Pokud jste vytvořili virtuální počítač s použitím modelu nasazení Resource Manager, jste hotovi.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Použijte vlastnost ProvisionGuestAgent pro klasické virtuální počítače

Pokud jste vytvořili virtuální počítač s využitím klasického modelu, pomocí modulu Azure PowerShell k aktualizaci **ProvisionGuestAgent** vlastnost. Vlastnost informuje Azure, virtuálního počítače je nainstalovaný Agent virtuálního počítače.

Chcete-li nastavit **ProvisionGuestAgent** vlastnost, spusťte následující příkazy v prostředí Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Spusťte `Get-AzureVM` příkazu. Všimněte si, **GuestAgentStatus** vlastnost je nyní naplněn daty:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Další postup

- [Přehled služby Azure agenta virtuálního počítače](../extensions/agent-windows.md)
- [Rozšíření virtuálních počítačů a funkce pro Windows](extensions-features.md)
