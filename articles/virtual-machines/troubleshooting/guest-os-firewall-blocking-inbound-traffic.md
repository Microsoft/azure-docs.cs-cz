---
title: Brána firewall operačního systému hosta virtuálního počítače Azure blokuje příchozí provoz | Microsoft Docs
description: Zjistěte, jak opravit problém s připojením k portálu vzdálené plochy (RDP), který brána firewall hostovaného operačního systému blokuje příchozí provoz.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 1b80fc997a4b3d2b472717b1ec2f379a4e958d8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422558"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Brána firewall operačního systému hosta virtuálního počítače Azure blokuje příchozí provoz

Tento článek popisuje, jak opravit problém s portálem vzdálené plochy (RDP), ke kterému dochází, když brána firewall hostovaného operačního systému blokuje příchozí provoz.

## <a name="symptoms"></a>Příznaky

Připojení RDP nelze použít pro připojení k virtuálnímu počítači Azure (VM). Z okna Diagnostika spouštění – > snímku obrazovky se zobrazí, že je operační systém plně načtený na úvodní obrazovce (Ctrl + Alt + Del).

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčina 1

Pravidlo protokolu RDP není nastavené tak, aby umožňovalo provoz protokolu RDP.

### <a name="cause-2"></a>Příčina 2

Profily brány firewall systému hosta jsou nastavené tak, aby blokovaly všechna příchozí připojení včetně provozu protokolu RDP.

![Nastavení brány firewall](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek systémového disku ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, použijte jednu z metod, [jak pomocí vzdálených nástrojů řešit problémy](remote-tools-troubleshoot-azure-vm-issues.md) s virtuálním počítačem Azure pro vzdálené připojení k virtuálnímu počítači, a pak upravte pravidla brány firewall hostovaného operačního systému tak, aby **umožňovala** provoz protokolu RDP.

### <a name="online-troubleshooting"></a>Online řešení potíží

Připojte se ke [konzole sériového portu a pak otevřete instanci prostředí PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Pokud není na virtuálním počítači povolená konzola sériového prostředí, použijte možnost[opravit virtuální počítač offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Zmírnění 1

1.  Pokud je agent Azure nainstalovaný a na virtuálním počítači funguje správně, můžete použít možnost resetovat jenom konfiguraci v nabídce **Podpora a Poradce při potížích s**  >  **resetování hesla** v nabídce VM.

2.  Spuštění této možnosti obnovení provede následující akce:

    *   Povolí komponentu RDP, pokud je zakázaná.

    *   Povolí všechny profily brány Windows Firewall.

    *   Ujistěte se, že je pravidlo protokolu RDP zapnuté v bráně Windows Firewall.

    *   Pokud předchozí kroky nefungují, ručně resetujte pravidlo brány firewall. Provedete to tak, že spustíte následující příkaz a vydáte dotaz na všechna pravidla, která obsahují název Vzdálená plocha:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Pokud byl port RDP nastaven na jiný port než 3389, je nutné najít jakékoli vlastní pravidlo, které bylo pravděpodobně vytvořeno a nastaveno na tento port. Pokud chcete zadat dotaz na všechna příchozí pravidla, která mají vlastní port, spusťte následující příkaz:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Pokud vidíte, že je pravidlo zakázané, povolte ho. Chcete-li otevřít celou skupinu, například integrovanou skupinu vzdálené plochy, spusťte následující příkaz:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Jinak otevřete konkrétní pravidlo vzdálené plochy (TCP-in) a spusťte následující příkaz:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Pro řešení potíží můžete vypnout profily brány firewall na OFF:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Po dokončení odstraňování potíží a správné nastavení brány firewall znovu povolte bránu firewall.

    > [!Note]
    > Nemusíte restartovat virtuální počítač, aby se tyto změny projevily.

5.  Pokuste se vytvořit připojení RDP pro přístup k virtuálnímu počítači.

#### <a name="mitigation-2"></a>Zmírnění 2

1.  Dotaz na profily brány firewall, abyste zjistili, jestli jsou příchozí zásady brány firewall nastavené na *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Následující pokyny se vztahují na zásady brány firewall v závislosti na tom, jak se nastavuje:
    >    * *BlockInbound*: veškerý příchozí provoz bude zablokován, pokud nemáte pravidlo pro povolení provozu.
    >    * *BlockInboundAlways*: všechna pravidla brány firewall budou ignorována a veškerý provoz bude zablokován.

2.  Upravte *DefaultInboundAction* a nastavte tyto profily tak, aby **umožňovaly** provoz. Provedete to spuštěním následujícího příkazu:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Vytvořte dotaz na profily znovu a ujistěte se, že se vaše změna úspěšně provedla. Provedete to spuštěním následujícího příkazu:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Změny se projeví až po restartování virtuálního počítače.

4.  Zkuste to znovu s přístupem k VIRTUÁLNÍmu počítači přes RDP.

### <a name="offline-mitigations"></a>Zmírnění offline

1.  [Připojte systémový disk k virtuálnímu počítači pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše virtuálního počítače pro obnovení.

3.  Ujistěte se, že je disk označen jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému systémovému disku.

#### <a name="mitigation-1"></a>Zmírnění 1

Přečtěte si, [jak Enable-Disable pravidlo brány firewall v hostovaném operačním systému](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Zmírnění 2

1.  [Připojte systémový disk k virtuálnímu počítači pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše virtuálního počítače pro obnovení.

3.  Po připojení systémového disku k virtuálnímu počítači pro obnovení se ujistěte, že je disk označen jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému disku s operačním systémem.

4.  Otevřete instanci příkazového řádku se zvýšenými oprávněními a spusťte následující skript:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Odpojte systémový disk a vytvořte virtuální počítač znovu](troubleshoot-recovery-disks-portal-windows.md).

6.  Ověřte, zda je problém vyřešen.
