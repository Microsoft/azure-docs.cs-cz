---
title: Vzdálená plocha odpojí často ve virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak řešit časté odpojení vzdálené plochy ve virtuálním počítači Azure.
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
ms.openlocfilehash: 7fecf8c5fdafb64f7922054dd2bb9755b0dec031
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881337"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Vzdálená plocha odpojí často ve virtuálním počítači Azure

Tento článek vysvětluje, jak řešit časté odpojení na virtuálním počítači Azure (VM) přes Remote Desktop Protocol RDP).

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manager. Doporučujeme použít tento model pro nových nasazení namísto pomocí modelu nasazení classic.

## <a name="symptom"></a>Příznak

Během vaší relace čelíte přerušované problémy s připojením RDP. Zpočátku se můžete připojit k virtuálnímu počítači, ale sníží připojení.

## <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud je špatně nakonfigurovaný. naslouchací proces RDP. Obvykle k tomuto problému dochází na virtuálním počítači, který používá vlastní image.

## <a name="solution"></a>Řešení

Než začnete provádět tyto kroky [pořízení snímku disku s operačním systémem](../windows/snapshot-copy-managed-disk.md) ovlivněných virtuálních počítačů jako záložní. 

Chcete-li tento problém vyřešit, použití sériového portu ovládacího prvku nebo [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) připojením disku s operačním systémem virtuálního počítače na virtuální počítač pro obnovení.

### <a name="serial-control"></a>Ovládací prvek sériového portu

1. Připojte se k [sériové konzoly a otevřené instance CMD](./serial-console-windows.md). Potom spusťte následující příkazy k resetování konfigurace protokolu RDP. Pokud konzole sériového portu není povolená na virtuálním počítači, přejděte k dalšímu kroku.
2. Nižší zabezpečení vrstvy protokolu RDP na hodnotu 0. Při tomto nastavení komunikace mezi serverem a klientem použít nativní šifrování protokolu RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Snížení úrovně šifrování pro nastavení minimální umožňující připojení starší verze klientů protokolu RDP.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Nastavit připojení RDP k načtení konfigurace uživatele klientského počítače.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Povolte RDP Keep-Alive ovládacího prvku:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Nastavení ovládacího prvku obnovení připojení protokolu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Nastavení ovládacího prvku čas relace protokolu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Nastavte ovládací prvek v době odpojení protokol RDP: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Nastavení ovládacího prvku doba připojení protokolu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Nastavení ovládacího prvku doby nečinnosti relace protokolu RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Nastavení ovládacího prvku "Omezit maximální počet souběžných připojení":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Restartujte virtuální počítač a zkuste to znovu k němu připojit pomocí protokolu RDP.

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu offline

1. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Po disk s operačním systémem je připojen k virtuální počítač pro obnovení, ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.
3. Na disk s operačním systémem, který jste připojili, přejděte **\windows\system32\config** složky. Zkopírujte všechny soubory v této složce jako zálohu, v případě, že vrácení zpět je povinný.
4. Spusťte Editor registru (regedit.exe).
5. Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **souboru** > **načíst Hive**:
6. Přejděte **\windows\system32\config\SYSTEM** složky na disku s operačním systémem, který jste připojili. Název hive zadejte **BROKENSYSTEM**. Nový registr hive se zobrazuje v části **HKEY_LOCAL_MACHINE** klíč. Pak načíst hive softwaru **\windows\system32\config\SOFTWARE** pod **HKEY_LOCAL_MACHINE** klíč. Název softwaru hive zadejte **BROKENSOFTWARE**. 
7. Otevřete okno příkazového řádku se zvýšenými oprávněními (**spustit jako správce**), a spusťte příkazy ve zbývajících krocích resetování konfigurace protokolu RDP. 
8. Dolní vrstva zabezpečení protokolu RDP na hodnotu 0, tak, aby komunikace mezi serverem a klientem používat nativní šifrování protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Snížení úrovně šifrování pro nastavení minimální umožňující připojení starší verze klientů protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Nastavit připojení RDP k načtení konfigurace uživatele klientského počítače.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Povolte RDP Keep-Alive ovládacího prvku:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Nastavení ovládacího prvku obnovení připojení protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Nastavení ovládacího prvku čas relace protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Nastavte ovládací prvek v době odpojení protokol RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Nastavení ovládacího prvku doba připojení protokolu RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Nastavení ovládacího prvku doby nečinnosti relace protokolu RDP:     REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP Tcp" /v "fInheritMaxIdleTime" /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Nastavení ovládacího prvku "Omezit maximální počet souběžných připojení":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Restartujte virtuální počítač a zkuste to znovu k němu připojit pomocí protokolu RDP.

## <a name="need-help"></a>Potřebujete pomoc? 
Kontaktujte podporu. Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.





