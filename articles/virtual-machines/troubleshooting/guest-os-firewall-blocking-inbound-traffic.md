---
title: Brána firewall hostovaného operačního systému Azure blokuje příchozí provoz | Dokumenty společnosti Microsoft
description: ''
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
ms.openlocfilehash: 0cbd1a24f5c460e248d55777735da6809befba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72028791"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Brána firewall hostovaného operačního systému Azure v centru Počítače blokuje příchozí provoz

Tento článek popisuje, jak opravit problém portálu vzdálené plochy (RDP), ke kterému dochází, pokud brána firewall hostovaného operačního systému blokuje příchozí provoz.

## <a name="symptoms"></a>Příznaky

Připojení RDP nelze použít k připojení k virtuálnímu počítači Azure (VM). Z boot diagnostiky -> Screenshot, ukazuje, že operační systém je plně načten na úvodní obrazovce (Ctrl + Alt + Del).

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčina 1

Pravidlo RDP není nastaveno tak, aby umožňovalo provoz RDP.

### <a name="cause-2"></a>Příčina 2

Profily brány firewall hosta systému jsou nastaveny tak, aby blokovaly všechna příchozí připojení, včetně provozu RDP.

![Nastavení brány firewall](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek systémového disku postiženého virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li tento problém vyřešit, použijte jednu z metod v [části Jak pomocí vzdálených nástrojů řešit problémy s virtuálním počítačem Azure](remote-tools-troubleshoot-azure-vm-issues.md) vzdáleně se připojit k virtuálnímu počítači a pak upravit pravidla brány firewall hostovaného operačního systému tak, aby **povolovala** provoz RDP.

### <a name="online-troubleshooting"></a>Řešení potíží online

Připojte se k [konzoli Serial Console a otevřete instanci prostředí PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Pokud konzola Sériové konzole není na virtuálním počítači povolená, přejděte na "[Oprava virtuálního počítače offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Zmírnění 1

1.  Pokud je agent Azure nainstalovaný a pracuje správně na virtuálním počítači, můžete použít možnost "Resetovat konfiguraci jenom" v části **Podpora + řešení potíží** > **Resetovat heslo** v nabídce virtuálního počítače.

2.  Spuštění této možnosti obnovení provádí následující akce:

    *   Povolí součást RDP, pokud je zakázána.

    *   Povolí všechny profily brány firewall systému Windows.

    *   Zkontrolujte, zda je v bráně Windows Firewall zapnuto pravidlo RDP.

    *   Pokud předchozí kroky nefungují, ručně resetujte pravidlo brány firewall. Chcete-li to provést, dotaz na všechna pravidla, která obsahují název "Vzdálená plocha" spuštěním následujícího příkazu:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Pokud byl port RDP nastaven na jiný port než 3389, je třeba najít vlastní pravidlo, které mohlo být vytvořeno a nastaveno na tento port. Chcete-li zadat dotaz na všechna příchozí pravidla, která mají vlastní port, spusťte následující příkaz:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Pokud zjistíte, že pravidlo je zakázáno, povolte jej. Chcete-li otevřít celou skupinu, například vestavěnou skupinu Vzdálená plocha, spusťte následující příkaz:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    V opačném případě otevřete konkrétní pravidlo vzdálené plochy (TCP-In), spusťte následující příkaz:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Při řešení potíží můžete profily brány firewall vypnout:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Po dokončení řešení potíží a správném nastavení brány firewall povolte bránu firewall znovu.

    > [!Note]
    > Není nutné restartovat virtuální počítač použít tyto změny.

5.  Pokuste se vytvořit připojení RDP pro přístup k virtuálnímu virtuálnímu serveru.

#### <a name="mitigation-2"></a>Zmírnění 2

1.  Dotaz profily brány firewall k určení, zda je nastavena zásada příchozí brány firewall *blockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Všechny profily](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Následující pokyny platí pro zásady brány firewall v závislosti na tom, jak je nastavena:
    >    * *BlockInbound*: Všechny příchozí přenosy budou blokovány, pokud nemáte platné pravidlo, které tento provoz povolí.
    >    * *BlockInboundAlways*: Všechna pravidla brány firewall budou ignorována a veškerý provoz bude blokován.

2.  Chcete-li nastavit tyto profily tak, aby **byly povoleny** přenosy, upravte akci *DefaultInboundAction.* Provedete to spuštěním následujícího příkazu:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Dotaz profily znovu a ujistěte se, že vaše změna byla provedena úspěšně. Provedete to spuštěním následujícího příkazu:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Není nutné restartovat virtuální počítač použít změny.

4.  Zkuste znovu získat přístup k virtuálnímu počítači prostřednictvím rdp.

### <a name="offline-mitigations"></a>Skutečnosti snižující závažnost rizika v offline

1.  [Připojte systémový disk k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.

3.  Zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému systémovému disku.

#### <a name="mitigation-1"></a>Zmírnění 1

Viz [Jak povolit a zakázat pravidlo brány firewall v hostovaném osu](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Zmírnění 2

1.  [Připojte systémový disk k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.

3.  Po připojení systémového disku k virtuálnímu počítače pro obnovení zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému disku operačního systému.

4.  Otevřete instanci CMD se zvýšenými oprávněními a spusťte následující skript:

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

5.  [Odpojte systémový disk a znovu vytvořte virtuální hod](troubleshoot-recovery-disks-portal-windows.md).

6.  Zkontrolujte, zda byl problém vyřešen.
