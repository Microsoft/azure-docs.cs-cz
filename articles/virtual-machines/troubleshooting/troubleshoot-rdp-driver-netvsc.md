---
title: Řešení potíží s problémem netvsc.sys při vzdáleném připojení k virtuálnímu počítači SAS 10 nebo Windows Serveru 2016 v Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vyřešit problém s RDP souvisejícím s netsvc.sys při připojování k virtuálnímu počítači s Windows 10 nebo Windows Server 2016 v Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057990"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Z důvodu netvsc.sys se nelze vzdáleně připojit k virtuálnímu počítači Windows 10 nebo Windows Server 2016 v Azure

Tento článek vysvětluje, jak vyřešit problém, ve kterém neexistuje žádné síťové připojení při připojení k virtuálnímu počítači (VM) s Windows 10 nebo Windows Server 2016 Datacenter na hostiteli Hyper-V Serveru 2016.

## <a name="symptoms"></a>Příznaky

K virtuálnímu počítači Azure Windows 10 nebo Windows Server 2016 se nemůžete připojit pomocí protokolu RDP (Remote Desktop Protocol). V [diagnostice spouštění](boot-diagnostics.md)se na obrazovce zobrazuje červený kříž přes kartu síťového rozhraní (NIC). To znamená, že virtuální hod nemá žádné připojení po úplném načtení operačního systému.

Obvykle k tomuto problému dochází v systému Windows [sestavení 14393](https://support.microsoft.com/help/4093120/) a [sestavení 15063](https://support.microsoft.com/help/4015583/). Pokud je verze operačního systému novější než tyto verze, tento článek se nevztahuje na váš scénář. Chcete-li zkontrolovat verzi systému, otevřete relaci CMD ve [funkci Konzola sériového přístupu](serial-console-windows.md)a spusťte **aplikaci Ver**.

## <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je verze nainstalovaného systémového souboru netvsc.sys **10.0.14393.594** nebo **10.0.15063.0**. Tyto verze netvsc.sys může zabránit systému v interakci s platformou Azure.


## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, [pořiďte snímek systémového disku](../windows/snapshot-copy-managed-disk.md) postiženého virtuálního počítače jako zálohu. K řešení tohoto problému použijte konzolu Serial Console nebo [opravte virtuální počítače offline](#repair-the-vm-offline) připojením systémového disku virtuálního počítače k virtuálnímu virtuálnímu počítače pro obnovení.


### <a name="use-the-serial-console"></a>Použití sériové konzole

Připojte se k [konzoli Serial Console, otevřete instanci prostředí PowerShell](serial-console-windows.md)a postupujte takto.

> [!NOTE]
> Pokud konzola Serial Console není na vašem virtuálním počítači povolená, přejděte do části [oprava virtuálního počítače offline.](#repair-the-vm-offline)

1. Spuštěním následujícího příkazu v instanci prostředí PowerShell získáte verzi souboru (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Stáhněte příslušnou aktualizaci na nový nebo existující datový disk, který je připojený k fungujícímu virtuálnímu počítače ze stejné oblasti:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) nebo novější aktualizace
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) nebo novější aktualizace

3. Odpojte disk nástroje od pracovního virtuálního počítače a pak jej připojte k poškozenému virtuálnímu počítače.

4. Chcete-li nainstalovat aktualizaci na virtuální počítač, spusťte následující příkaz:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Restartujte virtuální počítač.

### <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montu offline

1. [Připojte systémový disk k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).

2. Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.

3. Zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému systémovému disku.

4. Vytvořte kopii složky **\Windows\System32\config** v případě, že je nutné změny vrátit zpět.

5. Na záchranném virtuálním počítači spusťte Editor registru (regedit.exe).

6. Vyberte **HKEY_LOCAL_MACHINE** klíč a pak v nabídce vyberte**Podregistr načtení** **souboru.** > 

7. Vyhledejte soubor SYSTEM ve složce **\Windows\System32\config.**

8. Vyberte **otevřít**, zadejte příkaz **BROKENSYSTEM,** rozbalte **HKEY_LOCAL_MACHINE** klíč a vyhledejte další klíč s názvem **BROKENSYSTEM**.

9. Přejděte na následující místo:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. V každém podklíči (například 0000) zkontrolujte hodnotu **DriverDesc,** která je zobrazena jako **síťový adaptér Microsoft HYPER-V**.

11. V podklíči zkontrolujte hodnotu **DriverVersion,** která je verzí ovladače síťového adaptéru virtuálního soudu.

12. Stáhněte si příslušnou aktualizaci:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) nebo novější aktualizace
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) nebo novější aktualizace

13. Připojte systémový disk jako datový disk na záchranném virtuálním počítači, na kterém můžete stáhnout aktualizaci.

14. Chcete-li nainstalovat aktualizaci na virtuální počítač, spusťte následující příkaz:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Chcete-li odpojit úly, spusťte následující příkaz:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Odpojte systémový disk a znovu vytvořte virtuální virtuální modul](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu Azure a rychle se obraťte na problém.
