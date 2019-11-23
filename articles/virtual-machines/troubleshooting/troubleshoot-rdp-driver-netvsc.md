---
title: Řešení problému s netvsc. sys při vzdáleném připojení k virtuálnímu počítači s Windows 10 nebo Windows serverem 2016 v Azure | Microsoft Docs
description: Naučte se řešit potíže s protokolem RDP související s Netsvc. sys při připojování k virtuálnímu počítači s Windows 10 nebo Windows serverem 2016 v Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057990"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nejde se vzdáleně připojit k virtuálnímu počítači s Windows 10 nebo Windows serverem 2016 v Azure kvůli netvsc. sys.

Tento článek vysvětluje, jak řešit potíže, ke kterým nedochází při připojování k virtuálnímu počítači s Windows 10 nebo Windows serverem 2016 Datacenter na hostiteli Hyper-V na serveru 2016, pokud se nepoužívá žádné síťové připojení.

## <a name="symptoms"></a>Příznaky

Nemůžete se připojit k virtuálnímu počítači s Windows 10 nebo Windows serverem 2016 pomocí protokol RDP (Remote Desktop Protocol) (RDP). V [diagnostice spouštění](boot-diagnostics.md)se na obrazovce zobrazí červená křížová obrazovka přes síťovou kartu (nic). To znamená, že virtuální počítač nemá žádné připojení, když je operační systém plně načtený.

K tomuto problému obvykle dochází v systému Windows [build 14393](https://support.microsoft.com/help/4093120/) a [sestavení 15063](https://support.microsoft.com/help/4015583/). Pokud je verze vašeho operačního systému novější než tyto verze, Tento článek se nevztahuje na váš scénář. Chcete-li zjistit verzi systému, otevřete relaci příkazu CMD v [konzole sériového přístupu](serial-console-windows.md)a potom spusťte možnost **ver**.

## <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je verze nainstalovaného systémového souboru netvsc. sys **10.0.14393.594** nebo **10.0.15063.0**. Tyto verze netvsc. sys můžou zabránit systému v interakci s platformou Azure.


## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte [snímek systémového disku](../windows/snapshot-copy-managed-disk.md) OVLIVNĚNÉHO virtuálního počítače jako zálohy. Chcete-li tento problém vyřešit, použijte konzole sériového portu nebo [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) připojením systémový disk virtuálního počítače na virtuální počítač pro obnovení.


### <a name="use-the-serial-console"></a>Použití konzoly sériového portu

Připojte se ke [konzole sériového portu, otevřete instanci prostředí PowerShell](serial-console-windows.md)a pak postupujte podle těchto kroků.

> [!NOTE]
> Pokud konzole sériového portu není povolená na virtuálním počítači, přejděte [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) části.

1. Spuštěním následujícího příkazu v instanci PowerShellu Získejte verzi souboru (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Stáhněte si příslušnou aktualizaci na nový nebo existující datový disk, který je připojený k pracovnímu virtuálnímu počítači ze stejné oblasti:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) nebo pozdější aktualizace
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) nebo pozdější aktualizace

3. Odpojte disk s nástrojem od pracovního virtuálního počítače a pak ho připojte k poškozenému virtuálnímu počítači.

4. Spusťte následující příkaz pro instalaci aktualizace na virtuální počítač:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Restartujte virtuální počítač.

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu Offline

1. [Připojení disku systému pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).

2. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3. Ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojený systémový disk.

4. Vytvoří kopii složky **\Windows\System32\config** v případě, že je nutné vrátit zpět změny.

5. Na záchranném virtuálním počítači spusťte Editor registru (Regedit. exe).

6. Vyberte **HKEY_LOCAL_MACHINE** klíč a potom z nabídky vyberte **soubor** > **Načíst podregistr** .

7. Vyhledejte systémový soubor ve složce **\Windows\System32\config** .

8. Vyberte **otevřít**, jako název zadejte **BROKENSYSTEM** , rozbalte klíč **HKEY_LOCAL_MACHINE** a pak vyhledejte další klíč s názvem **BROKENSYSTEM**.

9. Přejít do následujícího umístění:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. V každém podklíči (například 0000) Prověřte hodnotu **DriverDesc** , která se zobrazí jako **síťový adaptér Microsoft Hyper-V**.

11. V podklíči vyhledejte hodnotu **DriverVersion** , která je verze ovladače síťového adaptéru virtuálního počítače.

12. Stáhněte si příslušnou aktualizaci:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) nebo pozdější aktualizace
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) nebo pozdější aktualizace

13. Připojte systémový disk jako datový disk na záchranném VIRTUÁLNÍm počítači, na kterém si můžete stáhnout aktualizaci.

14. Spusťte následující příkaz pro instalaci aktualizace na virtuální počítač:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Spuštěním následujícího příkazu odpojte podregistr:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Odpojte systémový disk a znovu vytvořte virtuální počítač](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu

Pokud stále potřebujete pomoc, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , která vám umožní rychle vyřešit potíže.
