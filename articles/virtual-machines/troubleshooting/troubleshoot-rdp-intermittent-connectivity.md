---
title: Vzdálená plocha se na virtuálním počítači Azure často odpojí. | Microsoft Docs
description: Naučte se řešit časté problémy odpojení vzdálené plochy na virtuálním počítači Azure.
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
ms.openlocfilehash: 60be7c234a0166331c35eb6528eae11bfbbf518f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074317"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Vzdálená plocha se často odpojuje od virtuálního počítače Azure

Tento článek vysvětluje, jak řešit často nepřipojená připojení k virtuálnímu počítači Azure pomocí protokol RDP (Remote Desktop Protocol) RDP).


## <a name="symptom"></a>Příznak

Během relací se můžete setkat s přerušovanými problémy s připojením RDP. K virtuálnímu počítači se můžete nejdřív připojit, ale připojení se pak uvolní.

## <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud je naslouchací proces RDP nesprávně nakonfigurovaný. K tomuto problému obvykle dochází na virtuálním počítači, který používá vlastní image.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte si [snímek disku s operačním systémem](../windows/snapshot-copy-managed-disk.md) OVLIVNĚNÉHO virtuálního počítače jako záložního. 

Pokud chcete tento problém vyřešit, použijte řízení sériového portu nebo [opravte virtuální počítač offline](#repair-the-vm-offline) připojením disku operačního systému virtuálního počítače k virtuálnímu počítači pro obnovení.

### <a name="serial-control"></a>Řízení sériového portu

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md). Pak spusťte následující příkazy pro resetování konfigurací RDP. Pokud není na vašem VIRTUÁLNÍm počítači povolená konzola sériového prostředí, pokračujte na další krok.
2. Snižte úroveň zabezpečení RDP na hodnotu 0. V tomto nastavení komunikace mezi serverem a klientem používá nativní šifrování RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

3. Snižte úroveň šifrování na minimální nastavení, aby bylo možné připojit starší klienty RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

4. Nastavením protokolu RDP načtěte konfiguraci uživatele klientského počítače.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

5. Povolte ovládací prvek RDP Keep-Alive:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

6. Nastavení ovládacího prvku pro obnovení protokolu RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

7. Nastavte řízení času relace protokolu RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

8. Nastavte řízení času odpojení protokolu RDP: 

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f` `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

9. Nastavte řízení času připojení RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

10. Nastavte řízení času nečinnosti relace protokolu RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f`

11. Nastavte ovládací prvek omezit maximální počet souběžných připojení:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f`

12. Restartujte virtuální počítač a zkuste se k němu připojit pomocí protokolu RDP.

### <a name="repair-the-vm-offline"></a>Oprava virtuálního počítače v režimu offline

1. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md).
2. Po připojení disku s operačním systémem k virtuálnímu počítači pro obnovení se ujistěte, že je disk označený jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému disku s operačním systémem.
3. Na disku s operačním systémem, který jste připojili, přejděte do složky **\Windows\System32\Config** . Zkopírujte všechny soubory v této složce jako zálohu pro případ, že je vyžadováno vrácení zpět.
4. Spusťte Editor registru (regedit.exe).
5. Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **File**  >  **podregistr Loading**File:
6. Přejděte do složky **\windows\system32\config\SYSTEM** na disku s operačním systémem, který jste připojili. Jako název podregistru zadejte **BROKENSYSTEM**. Nový podregistr registru se zobrazí pod klíčem **HKEY_LOCAL_MACHINE** . Pak načtěte podregistr software **\windows\system32\config\SOFTWARE** pod klíčem **HKEY_LOCAL_MACHINE** . Jako název softwaru pro podregistr zadejte **BROKENSOFTWARE**. 
7. Otevřete okno příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**) a ve zbývajících krocích spusťte příkazy pro resetování konfigurací RDP. 
8. Snižte úroveň zabezpečení RDP na 0, aby komunikace mezi serverem a klientem používala nativní šifrování RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

9. Snižte úroveň šifrování na minimální nastavení, aby bylo možné připojit starší klienty RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

10. Nastavením protokolu RDP načtěte konfiguraci uživatele klientského počítače.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

11. Povolte ovládací prvek RDP Keep-Alive:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

12. Nastavení ovládacího prvku pro obnovení protokolu RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

13. Nastavte řízení času relace protokolu RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

14. Nastavte řízení času odpojení protokolu RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

15. Nastavte řízení času připojení RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

16. Nastavte řízení času nečinnosti relace protokolu RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

17. Nastavte ovládací prvek omezit maximální počet souběžných připojení:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

18. Restartujte virtuální počítač a zkuste se k němu připojit pomocí protokolu RDP.

## <a name="need-help"></a>Potřebujete pomoc? 
Obraťte se na podporu. Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
