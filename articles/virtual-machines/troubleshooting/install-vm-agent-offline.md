---
title: Instalace agenta virtuálního počítače Azure v režimu offline | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nainstalovat agenta virtuálního počítače Azure v režimu offline.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8ea85b560f35c79b3d5066d794f587345810b5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920854"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalace agenta virtuálních strojů Azure v režimu offline 

Agent virtuálních strojů Azure (VM Agent) poskytuje užitečné funkce, jako je například místní správce resetování hesla a skript ování. Tento článek ukazuje, jak nainstalovat agenta virtuálního počítače pro offline virtuální počítač (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kdy použít agenta virtuálního soudu v režimu offline

Nainstalujte agenta virtuálního soudu do režimu offline v následujících scénářích:

- Nasazený virtuální počítač Azure nemá nainstalovaný agenta virtuálního počítače nebo agent nefunguje.
- Zapomněli jste heslo správce pro virtuální počítače nebo nemáte přístup k virtuálnímu počítače.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Jak nainstalovat agenta virtuálního aplikace v režimu offline

Pomocí následujících kroků nainstalujte agenta virtuálního aplikace v režimu offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1: Připojení disku operačního systému virtuálního počítače k jinému virtuálnímu virtuálnímu počítače jako datovému disku

1. Pořiďte snímek pro disk operačního systému postiženého virtuálního počítače, vytvořte disk ze snímku a pak připojte disk k virtuálnímu virtuálnímu počítače pro řešení potíží. Další informace najdete [v tématu Poradce při potížích s virtuálním počítačem s Windows připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí portálu Azure](troubleshoot-recovery-disks-portal-windows.md). Pro klasický virtuální počítače odstraňte virtuální ho nový virtuální počítače a zachovat disk operačního systému a pak připojte disk operačního systému k řešení potíží s virtuálním počítačem.

2.  Připojte se k virtuálnímu zařízení pro poradce při potížích. Otevřená **správa disku správy** > **počítače**. Zkontrolujte, zda je disk operačního systému online a zda jsou diskovým oddílům přiřazena písmena jednotek.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2: Úprava disku operačního systému pro instalaci agenta virtuálního počítače Azure

1.  Vytvořte připojení ke vzdálené ploše k virtuálnímu počítači poradce při potížích.

2.  V modulu pro řešení potíží s virtuálním počítačem přejděte na připojený disk operačního systému a otevřete složku \windows\system32\config. Zkopírujte všechny soubory v této složce jako zálohu pro případ, že je vyžadováno vrácení zpět.

3.  Spusťte **Editor registru** (regedit.exe).

4.  Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **File** > **Load Hive**:

    ![Načtení úlu](./media/install-vm-agent-offline/load-hive.png)

5.  Přejděte do složky \windows\system32\config\SYSTEM na připojeném disku operačního systému. Pro název podregistru zadejte **BROKENSYSTEM**. Pod klíčem **HKEY_LOCAL_MACHINE** se zobrazí nový podregistr registru.

6.  Přejděte do složky \windows\system32\config\SOFTWARE na připojeném disku operačního systému. Pro název softwaru podregistru zadejte **BROKENSOFTWARE**.

7. Pokud připojený disk operačního systému má nainstalovaného agenta virtuálního počítače, proveďte zálohu aktuální konfigurace. Pokud nemá nainstalovaného agenta virtuálního zařízení, přejděte k dalšímu kroku.
      
    1. Přejmenujte složku \windowsazure na \windowsazure.old.

    2. Exportovat tyto registry:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Použijte existující soubory na virtuálním počítači poradce při potížích jako úložiště pro instalaci agenta virtuálního počítače. Proveďte následující kroky:

    1. Z virtuálního zařízení poradce při potížích exportujte následující podklíče ve formátu registru (.reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![Export podklíčů registru](./media/install-vm-agent-offline/backup-reg.png)

    2. Upravte soubory registru. V každém souboru změňte hodnotu položky **SYSTEM** na **BROKENSYSTEM** (jak je znázorněno na následujících obrázcích) a uložte soubor. Zapamatujte si **ImagePath** aktuálního agenta virtuálního aplikace. Budeme muset zkopírovat odpovídající složku na připojený disk operačního systému. 

        ![Změna hodnot podklíče registru](./media/install-vm-agent-offline/change-reg.png)

    3. Importujte soubory registru do úložiště poklepáním na každý soubor registru.

    4. Zkontrolujte, zda jsou do podregistru **BROKENSYSTEM** úspěšně importovány následující tři podklíče:
        - WindowsAzureGuestAgent
        - Služba WindowsAzureTelemetryService
        - Zprostředkovatel rdagent

    5. Zkopírujte instalační složku aktuálního agenta virtuálního počítače na připojený disk operačního systému: 

        1.  Na připojeném disku operačního systému vytvořte v kořenové cestě složku s názvem WindowsAzure.

        2.  Přejděte na virtuální m> poradce při potížích na c:\WindowsAzure, vyhledejte libovolnou složku s názvem C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Zkopírujte složku GuestAgent s nejnovějším číslem verze z C:\WindowsAzure do složky WindowsAzure na připojeném disku operačního systému. Pokud si nejste jisti, která složka by měla být zkopírována, zkopírujte všechny složky GuestAgent. Následující obrázek ukazuje příklad složky GuestAgent, která je zkopírována na připojený disk operačního systému.

             ![Kopírovat složku GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Vyberte **možnost BROKENSYSTEM**. V nabídce vyberte **File** > **Unload Hive**.

10.  Vyberte **MOŽNOST BROKENSOFTWARE**. V nabídce vyberte **File** > **Unload Hive**.

11.  Odpojte disk operačního systému a [změňte disk operačního systému pro ohrožený virtuální modul](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Pro klasický virtuální počítače vytvořte nový virtuální počítače pomocí opraveného disku operačního systému.

12.  Přístup k virtuálnímu virtuálnímu mněmu. Všimněte si, že RdAgent je spuštěn a protokoly jsou generovány.

Pokud jste vytvořili virtuální hospo- pomocí modelu nasazení Správce prostředků, jste hotovi.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Použití vlastnosti ProvisionGuestAgent pro klasické virtuální hody

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Pokud jste vytvořili virtuální počítač pomocí klasického modelu, použijte modul Azure PowerShell k aktualizaci **provisionGuestAgent** vlastnost. Vlastnost informuje Azure, že virtuální počítač má nainstalovaného agenta virtuálního počítače.

Pokud chcete nastavit vlastnost **ProvisionGuestAgent,** spusťte v Azure PowerShellu následující příkazy:

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
- [Rozšíření a funkce virtuálních strojů pro Windows](../extensions/features-windows.md)
