---
title: Řešení potíží netvsc.sys při vzdálené připojení k Windows 10 nebo Windows serveru 2016 virtuálního počítače v Azure | Dokumentace Microsoftu
description: Informace o řešení potíží s RDP se netsvc.sys související vydání, když jste připojení k Windows 10 nebo Windows serveru 2016 virtuálního počítače v Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 777d5cb9449bcf9424e2514b2b8f90a9ca6c479c
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52285447"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nelze vzdáleně připojit k Windows 10 nebo Windows serveru 2016 virtuálního počítače v Azure z důvodu netvsc.sys

Tento článek vysvětluje, jak vyřešit nějaký problém, ve kterém není žádné síťové připojení při připojení k Windows 10 nebo Windows Server 2016 Datacenter virtuálních počítačů (VM) na hostiteli Hyper-V Server 2016.

## <a name="symptoms"></a>Příznaky

Nelze se připojení k Azure Windows 10 nebo virtuální počítač s Windows serverem 2016 pomocí VP Desktop Protocol (RDP). V [Diagnostika spouštění](boot-diagnostics.md), na obrazovce zobrazí červený křížek v průběhu síťovou kartu (NIC). To znamená, že virtuální počítač nemá připojení po plně načtený operační systém.

Obvykle k tomuto problému dochází ve Windows [sestavení 14393](http://support.microsoft.com/help/4093120/) a [sestavení 15063](http://support.microsoft.com/help/4015583/). Pokud verze operačního systému je novější než tato verze, v tomto článku se nevztahují na váš scénář. Pokud chcete zkontrolovat verzi systému, otevřete relaci příkazového řádku v [funkce konzoly sériového portu přístup](serial-console-windows.md)a pak spusťte **Ver**.

## <a name="cause"></a>Příčina

Této situaci může dojít, pokud je verze nainstalovaného netvsc.sys systémový soubor **10.0.14393.594** nebo **10.0.15063.0**. Tyto verze netvsc.sys může systém zabránit interakci s platformou Azure.


## <a name="solution"></a>Řešení

Než začnete provádět tyto kroky [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md) ovlivněných virtuálních počítačů jako záložní. Chcete-li tento problém vyřešit, použijte konzole sériového portu nebo [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) připojením systémový disk virtuálního počítače na virtuální počítač pro obnovení.


### <a name="use-the-serial-console"></a>Použití konzole sériového portu

Připojte se k [konzoly sériového portu, otevřete PowerShell instanci](serial-console-windows.md)a pak postupujte podle těchto kroků.

> [!NOTE]
> Pokud konzole sériového portu není povolená na virtuálním počítači, přejděte [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) části.

1. Spuštěním následujícího příkazu v Powershellu instance se získat verzi souboru (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Stáhněte si příslušnou aktualizaci na nový nebo existující datový disk, který je připojen k funkčním virtuálním počítači ze stejné oblasti:

   - **10.0.14393.594**: [KB4073562](http://support.microsoft.com/help/4073562) nebo novější aktualizace
   - **10.0.15063.0**: [KB4016240](http://support.microsoft.com/help/4016240) nebo novější aktualizace

3. Odpojení disku nástroje funkčním virtuálním počítači a poté jej připojit k virtuálnímu počítači nefunkční.

4. Spusťte následující příkaz k instalaci aktualizace na virtuálním počítači:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Restartujte virtuální počítač.

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu Offline

1. [Připojení disku systému pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).

2. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3. Ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojený systémový disk.

4. Vytvoření kopie **\Windows\System32\config** složku v případě a vrácení zpět při změně je nezbytné.

5. Na stanici virtuálního počítače spusťte Editor registru (regedit.exe).

6. Vyberte **HKEY_LOCAL_MACHINE** klíče a pak vyberte **souboru** > **načíst Hive** z nabídky.

7. Vyhledejte soubor systému v **\Windows\System32\config** složky.

8. Vyberte **otevřít**, typ **BROKENSYSTEM** názvu, rozbalte **HKEY_LOCAL_MACHINE** klíče a vyhledejte další klíč, který je pojmenován **BROKENSYSTEM** .

9. Přejděte do následujícího umístění:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. V každé podklíč (jako je 0000) v souvislosti **DriverDesc** hodnotu, která se zobrazí jako **síťový adaptér Microsoft HYPER-V**.

11. V podklíči, zkontrolujte **DriverVersion** hodnotu, která je verze ovladače síťového adaptéru virtuálního počítače.

12. Stáhněte si příslušnou aktualizaci:

   - **10.0.14393.594**: [KB4073562](http://support.microsoft.com/help/4073562) nebo novější aktualizace
   - **10.0.15063.0**: [KB4016240](http://support.microsoft.com/help/4016240) nebo novější aktualizace

13. Připojte disk systému jako datový disk na virtuálním počítači s zachránit, na kterém si můžete stáhnout aktualizace.

14. Spusťte následující příkaz k instalaci aktualizace na virtuálním počítači:

   ```
   dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
   ```

15. Spusťte následující příkaz pro odpojení podregistry:

   ```
   reg unload HKLM\BROKENSYSTEM
   ```

16. [Odpojení disku a vytvoření virtuálního počítače znovu](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
