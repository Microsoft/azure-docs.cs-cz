---
title: Vzdálená plocha odpojí často ve virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak vyřešit problém, ve kterém vzdálené plochy odpojí často ve virtuálním počítači Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: b65cd4d7e6f68f9444ca0264892bcca31cfbe674
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142358"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Vzdálená plocha odpojí často ve virtuálním počítači Azure

Tento článek ukazuje, jak vyřešit problém, ve kterém vzdálené plochy odpojí často ve virtuálním počítači Azure.

> [!NOTE] 
> Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje pomocí modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic.

## <a name="symptom"></a>Příznak 

Během vaší relace čelíte přerušované připojení RDP. Můžete se připojit k virtuálnímu počítači, ale sníží připojení.

## <a name="cause"></a>Příčina

Tento problém může být způsobeno naslouchací proces RDP je špatně nakonfigurovaný. Tento problém nastává obvykle na virtuálním počítači vlastní.

## <a name="solution"></a>Řešení  

Než začnete provádět kroky, [pořízení snímku disku s operačním systémem](../windows/snapshot-copy-managed-disk.md) ovlivněných virtuálních počítačů jako záložní. 

Chcete-li tento problém vyřešit, použití sériového portu ovládacího prvku nebo [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) připojením disku s operačním systémem virtuálního počítače na virtuální počítač pro obnovení.

### <a name="reset-rdp-configurations"></a>Resetování konfigurace protokolu RDP

1. Připojte se k [sériové konzoly a otevřené instance CMD](./serial-console-windows.md), spusťte následující příkazy v části resetování konfigurace protokolu RDP. Pokud na vašem virtuálním počítači není povolené konzole sériového portu, přesune k dalšímu kroku. 
2. Vrstva zabezpečení protokolu RDP na hodnotu 0, snížit tak komunikaci mezi serverem a klientem budou používat nativní šifrování protokolu RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Nižší úroveň šifrování na minimum umožňující připojení starší verze klientů protokolu RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Nastavte připojení RDP k načtení konfigurace uživatele klientského počítače.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Povolení protokolu RDP Keep-Alive řízení:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Nastavení ovládacího prvku, opětovné připojení RDP

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Nastavení ovládacího prvku, doba trvání relace protokolu RDP 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Nastavení ovládacího prvku, doba odpojení protokolu RDP 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Nastavení ovládacího prvku, doba připojení protokolu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Nastavení řízení čas nečinnosti relace protokolu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
11. Sada omezení maximální počet souběžných připojení:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

12. Restartujte virtuální počítač a pokud jste ji připojit pomocí protokolu RDP.

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu offline

1. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Jakmile disk s operačním systémem je připojen k obnovení virtuálního počítače, ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.
3. Přejděte **\windows\system32\config** složky. Zkopírujte všechny soubory v této složce jako zálohu, v případě, že vrácení zpět je povinný.
4. Spusťte Editor registru (regedit.exe).
5. Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **souboru** > **načíst Hive**:
6. Přejděte **\windows\system32\config\SYSTEM** složky na disku s operačním systémem, který jste připojili. Název hive zadejte **BROKENSYSTEM**. Nový registr hive se zobrazuje v části **HKEY_LOCAL_MACHINE** klíč.
7. Přejděte **\windows\system32\config\SOFTWARE** složky na disku s operačním systémem, který jste připojili. Název softwaru hive zadejte **BROKENSOFTWARE**.
8. Otevřete okno Příkazový řádek se zvýšenými oprávněními příkaz (Spustit jako správce), spusťte příkazy v zbývající kroky pro resetování konfigurace protokolu RDP. 
9. Snížit zabezpečení vrstvy protokolu RDP na hodnotu 0, pokud komunikace mezi serverem a klientem použije nativní šifrování protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
10. Snížení úrovně šifrování pro minimální umožňující připojení starší verze klientů protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
11. Nastavte připojení RDP k načtení konfigurace uživatele klientského počítače.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
12. Povolení protokolu RDP Keep-Alive řízení:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
13. Nastavení ovládacího prvku, opětovné připojení RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

14. Nastavení ovládacího prvku, doba trvání relace protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
15. Nastavení ovládacího prvku, doba odpojení protokol RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
16. Nastavení ovládacího prvku, doba připojení protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
17. Nastavení řízení čas nečinnosti relace protokolu RDP: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP Tcp" /v "fInheritMaxIdleTime" /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
18. Sada omezení maximální počet souběžných připojení:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
19. Restartujte virtuální počítač a pokud jste ji připojit pomocí protokolu RDP.

## <a name="need-help"></a>Potřebujete pomoct? 
Obraťte se na podporu. Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.





