---
title: Instalace agenta virtuálního počítače Azure v offline režimu | Microsoft Docs
description: Informace o instalaci agenta virtuálního počítače Azure v režimu offline.
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
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 8b9d1ea3e4b5bf959484a737453d7f6f1f63d1e6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalace agenta virtuálního počítače Azure v offline režimu 

Agent virtuálního počítače Azure (VM Agent) poskytuje užitečných funkcí, jako je například resetování hesla místního správce a skript vkládání. Tento článek ukazuje, jak nainstalovat agenta virtuálního počítače pro se offline Windows virtuální počítač (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kdy použít agenta virtuálního počítače v režimu offline

Nainstalujte agenta virtuálního počítače v režimu offline v následujících scénářích:

- Nasazený virtuální počítač Azure nemá nainstalovaného agenta virtuálního počítače nebo agenta není funkční.
- Jste zapomněli heslo správce pro virtuální počítač nebo virtuální počítač nelze získat přístup.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Postup instalace agenta virtuálního počítače v režimu offline

Použijte následující postup k instalaci agenta virtuálního počítače v režimu offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1: Připojte disk operačního systému virtuálního počítače na jiný virtuální počítač jako datový disk

1.  Odstraňte virtuální počítač. Je nutné vybrat **zachovat disky** možnost při odstranění virtuálního počítače.

2.  Připojit disk operačního systému jako datový disk k jiným virtuálním Počítačem (označované jako _Poradce při potížích s_ virtuálních počítačů). Další informace najdete v tématu [připojit datový disk k virtuálnímu počítači Windows na portálu Azure](attach-managed-disk-portal.md).

3.  Připojte k Poradce při potížích s virtuálních počítačů. Otevřete **Správa počítače** > **disku správu**. Zkontrolujte, zda je disk operačního systému online a že písmena jednotek jsou přiřazeny k diskové oddíly.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2: Upravte disk operačního systému pro instalaci agenta virtuálního počítače Azure

1.  Zkontrolujte připojení ke vzdálené ploše Poradce při potížích s virtuálních počítačů.

2.  Na disku operačního systému, který jste připojili přejděte do složky \windows\system32\config. Zkopírujte všechny soubory v této složce zálohování, v případě, že je vyžadován vrácení zpět.

3.  Spuštění **Editor registru** (regedit.exe).

4.  Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **soubor** > **načíst podregistr**:

    ![Načíst podregistr](./media/install-vm-agent-offline/load-hive.png)

5.  Přejděte do složky \windows\system32\config\SYSTEM na disk operačního systému, který jste připojili. Název podregistr, zadejte **BROKENSYSTEM**. Nové podregistru se zobrazí v části **HKEY_LOCAL_MACHINE** klíč.

6.  Přejděte do složky \windows\system32\config\SOFTWARE na disk operačního systému, který jste připojili. Název softwaru hive, zadejte **BROKENSOFTWARE**.

7.  Pokud agenta virtuálního počítače není funkční, zálohujte aktuální konfiguraci.

    >[!NOTE]
    >Pokud virtuální počítač nemá nainstalovaného agenta, pokračujte ke kroku 8. 
      
    1. Přejmenujte složku \windowsazure \windowsazure.old.

    2. Exportujte následující registrech:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Použijte existující soubory na Poradce při potížích se virtuální počítač jako úložiště pro instalaci agenta virtuálního počítače. Proveďte následující kroky:

    1. Z Poradce při potížích virtuálních počítačů exportujte následující podklíče registru formát (REG): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Export podklíčů registru](./media/install-vm-agent-offline/backup-reg.png)

    2. Upravte soubory registru. Do každého souboru, změňte hodnotu položky **systému** k **BROKENSYSTEM** (jak je znázorněno v následujícím bitovým kopiím) a soubor uložte.

        ![Změna hodnoty podklíčů registru](./media/install-vm-agent-offline/change-reg.png)

    3. Importujte souborů registru do úložiště dvojitým kliknutím na každého souboru registru.

    4. Potvrďte, že jsou úspěšně importovány do následující tři podklíče **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Zkopírujte složku agenta virtuálního počítače z C:\windowsazure\packages k &lt;disk operačního systému, který jste připojili&gt;: \windowsazure\packages.

    ![Zkopírujte soubory agenta virtuálního počítače na disk operačního systému](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Nemáte zkopírovat **protokoly** složky. Po spuštění služby jsou generovány nové protokoly.

10.  Vyberte **BROKENSYSTEM**. V nabídce vyberte **soubor** > **Uvolnit podregistr**.

11.  Vyberte **BROKENSOFTWARE**. V nabídce vyberte **soubor** > **Uvolnit podregistr**.

12.  Odpojte disk operačního systému a pak znovu vytvořte virtuální počítač pomocí disku operačního systému.

13.  Přístup k virtuálnímu počítači. Všimněte si, že je spuštěna RdAgent a generovány protokoly.

Pokud jste vytvořili virtuální počítač pomocí modelu nasazení classic, jste hotovi.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Použijte parametr ProvisionGuestAgent vlastnost pro virtuální počítače vytvořené pomocí Azure Resource Manageru

Pokud vytvoříte virtuální počítač pomocí modelu nasazení Resource Manager, použijte modul Azure PowerShell k aktualizaci **parametr ProvisionGuestAgent** vlastnost. Vlastnost informuje Azure, že virtuální počítač má nainstalovaný Agent virtuálního počítače.

Chcete-li nastavit **parametr ProvisionGuestAgent** vlastnost, spusťte následující příkazy v prostředí Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Spusťte `Get-AzureVM` příkaz. Všimněte si, že **GuestAgentStatus** vlastnost je nyní naplněný daty:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Další postup

- [Přehled služby Azure agenta virtuálního počítače](agent-user-guide.md)
- [Rozšíření virtuálního počítače a funkce pro Windows](extensions-features.md)
