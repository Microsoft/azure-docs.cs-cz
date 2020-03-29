---
title: Vzdálená plocha se často odpojuje ve virtuálním počítači Azure| Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit časté odpojení vzdálené plochy ve virtuálním počítači Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: c22a401a6b25f7bb2c27a10e52214fa42ac6089b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918219"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Vzdálená plocha se často odpojuje od virtuálního počítače Azure

Tento článek vysvětluje, jak řešit časté odpojení k virtuálnímu počítači (VM) Azure prostřednictvím rdp protokolu rdp vzdálené plochy).


## <a name="symptom"></a>Příznak

Během relací čelíte občasným problémům s připojením protokolu RDP. Zpočátku se můžete připojit k virtuálnímu virtuálnímu zařízení, ale pak připojení klesne.

## <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je chybně nakonfigurován naslouchací proces PROTOKOLU RDP. Obvykle k tomuto problému dochází na virtuálním počítači, který používá vlastní image.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, [pořiďte snímek disku operačního systému](../windows/snapshot-copy-managed-disk.md) ovlivněného virtuálního počítače jako zálohu. 

K řešení tohoto problému použijte sériový ovládací prvek nebo [opravte virtuální ho v provozu offline](#repair-the-vm-offline) připojením disku ooperačnímu systému virtuálního počítače k virtuálnímu virtuálnímu počítače pro obnovení.

### <a name="serial-control"></a>Sériové řízení

1. Připojte se k [konzoli Serial Console a otevřete instanci CMD](./serial-console-windows.md). Potom spusťte následující příkazy k obnovení konfigurace protokolu RDP. Pokud konzola Serial Console není na vašem virtuálním počítači povolená, přejděte k dalšímu kroku.
2. Snižte vrstvu zabezpečení PROTOKOLU RDP na 0. Při tomto nastavení používá komunikace mezi serverem a klientem nativní šifrování RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Snižte úroveň šifrování na minimální nastavení, aby se starší klienti RDP mohli připojit.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Nastavte program RDP tak, aby načítat uživatelskou konfiguraci klientského počítače.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Povolte ovládací prvek UDRŽOVÁNÍ V PP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Nastavte ovládací prvek OPĚTOVNÉ připojení k programu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Nastavte ovládací prvek čas relace RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Nastavte řízení doby odpojení RDP: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Nastavte řízení doby připojení rdp:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Nastavte ovládací prvek Doba nečinnosti relace RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Nastavte ovládací prvek "Omezit maximální počet souběžných připojení":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Restartujte virtuální počítač a zkuste se k němu znovu připojit pomocí programu RDP.

### <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montovadova offline

1. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Po připojení disku operačního systému k virtuálnímu počítače pro obnovení zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému disku operačního systému.
3. Na připojeném disku operačního systému přejděte do složky **\windows\system32\config.** Zkopírujte všechny soubory v této složce jako zálohu pro případ, že je vyžadováno vrácení zpět.
4. Spusťte Editor registru (regedit.exe).
5. Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **File** > **Load Hive**:
6. Přejděte do složky **\windows\system32\config\SYSTEM** na připojeném disku operačního systému. Pro název podregistru zadejte **BROKENSYSTEM**. Pod klíčem **HKEY_LOCAL_MACHINE** se zobrazí nový podregistr registru. Potom načtěte pod **HKEY_LOCAL_MACHINE** klíč pod softwarovým podregistrem **\windows\system32\config\SOFTWARE.** Pro název softwaru podregistru zadejte **BROKENSOFTWARE**. 
7. Otevřete okno příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**) a spusťte příkazy ve zbývajících krocích pro obnovení konfigurací protokolu RDP. 
8. Snižte vrstvu zabezpečení PROTOKOLU RDP na 0, aby komunikace mezi serverem a klientem používala nativní šifrování PROTOKOLU RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Snižte úroveň šifrování na minimální nastavení, aby se starší klienti RDP mohli připojit:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Nastavte rdp načíst uživatelskou konfiguraci klientského počítače.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Povolte ovládací prvek UDRŽOVÁNÍ V PP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Nastavte ovládací prvek OPĚTOVNÉ připojení k programu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Nastavte ovládací prvek čas relace RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Nastavte řízení doby odpojení RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Nastavte řízení doby připojení rdp:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Nastavení ovládacího prvku Doba nečinnosti relace protokolu RDP: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Nastavte ovládací prvek "Omezit maximální počet souběžných připojení":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Restartujte virtuální počítač a zkuste se k němu znovu připojit pomocí programu RDP.

## <a name="need-help"></a>Potřebujete pomoc? 
Obraťte se na podporu. Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.





