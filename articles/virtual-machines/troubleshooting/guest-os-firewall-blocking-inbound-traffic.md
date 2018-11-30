---
title: Blokuje příchozí provoz Azure brána firewall hostovaného operačního systému virtuálního počítače | Dokumentace Microsoftu
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 3ddd2f122de832654be295c5978a88bec702558c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319432"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Blokuje příchozí provoz Azure brána firewall hostovaného operačního systému virtuálního počítače

Tento článek popisuje, jak portál RDP (Remote Desktop) tento problém vyřešit, který nastane, pokud brána firewall blokuje operačního systému hosta příchozí provoz.

## <a name="symptoms"></a>Příznaky

Připojení ke vzdálené ploše nelze použít pro připojení k Azure virtuální počítač (VM). Pomocí spuštění ze diagnostické nástroje -> – snímek obrazovky, ukazuje, že je operační systém plně načtený zobrazená úvodní obrazovka (Ctrl + Alt + Del).

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčiny 1 

Chcete-li povolit provoz protokolu RDP není nastavené pravidlo protokolu RDP.

### <a name="cause-2"></a>Příčiny 2 

Profily brány firewall systému hosta jsou nastavení blokovat všechna příchozí připojení včetně provozu protokolu RDP.

![Nastavení brány firewall](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Řešení 

Než budete postupovat podle těchto kroků, pořiďte snímek systémový disk ovlivněných virtuálních počítačů jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete problém vyřešit, použijte jednu z metod v [použití nástrojů remote tools pro řešení potíží s virtuálním počítači Azure](remote-tools-troubleshoot-azure-vm-issues.md) vzdálené připojení k virtuálnímu počítači a potom upravit pravidla brány firewall hostovaného operačního systému pro **povolit** provoz protokolu RDP .

### <a name="online-troubleshooting"></a>Řešení potíží s online

Připojte se k [konzoly sériového portu a pak otevřete PowerShell instance](serial-console-windows.md#open-cmd-or-powershell-in-serial-console). Pokud konzole sériového portu není povolená na virtuálním počítači, přejděte na "[opravit virtuální počítač Offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Zmírnění dopadů 1

1.  Pokud je nainstalovaný Agent služby Azure a správně funguje na virtuálním počítači, můžete použít možnost "Jenom resetování konfigurace" v části **podpora a řešení potíží** > **resetovat heslo** v nabídce virtuálního počítače.

2.  Tato možnost obnovení provede následující akce:

    *   Umožňuje jako součást protokolu RDP, jestli není zakázaný.

    *   Povolí všechny profily firewallu Windows.

    *   Ujistěte se, že v bráně Windows Firewall je zapnutá pravidlo protokolu RDP.

    *   Pokud předchozí postup nefunguje, ručně obnovit pravidlo brány firewall. Chcete-li to provést, dotaz všechna pravidla, které obsahují název "Vzdálená plocha" spuštěním následujícího příkazu:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        RDP port byl nastavený na jakýkoli jiný port než 3389, budete muset najít všechny vlastní pravidlo, které může být vytvořená a nastavte na tento port. Se dotázat na všechny příchozí pravidla, která mají port. Tento vlastní port, spusťte následující příkaz:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Pokud vidíte, že je zakázané pravidlo, povolte ho. Pokud chcete otevřít celou skupinu, jako je například předdefinované skupiny Vzdálená plocha, spusťte následující příkaz:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```
    
    V opačném případě otevřete příslušné vzdálené plochy (TCP-In) pravidlo, spusťte následující příkaz:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  S řešením problémů, můžete ji profilů brány firewall na hodnotu OFF:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Po dokončení odstraňování potíží a nastavení brány firewall správně, povolte bránu firewall.
    
    > [!Note]
    > Není nutné restartovat počítač změny se projeví.

5.  Pokuste se vytvořit připojení RDP k virtuálnímu počítači.

#### <a name="mitigation-2"></a>Zmírnění dopadů 2

1.  Dotazování profilů brány firewall k určení, zda je nastaven zásady brány firewall pro příchozí *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofile](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Následující pokyny se vztahují zásady brány firewall, v závislosti na tom, jak je nastavit:
    >    * *BlockInbound*: veškerý příchozí provoz se zablokuje, pokud nemáte v platnosti pravidla povolení provozu.
    >    * *BlockInboundAlways*: ignorují se všechna pravidla brány firewall a veškerý provoz se zablokuje.

2.  Upravit *DefaultInboundAction* nastavit tyto profily **povolit** provoz. Chcete-li to provést, spusťte následující příkaz:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Dotazovat profily znovu a ujistit se, že změny proběhla úspěšně. Chcete-li to provést, spusťte následující příkaz:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Není nutné restartovat počítač změny se projeví. 

4.  Přístup k vašemu virtuálnímu počítači přes SSH, zkuste to znovu.

### <a name="offline-mitigations"></a>Offline migrace 

1.  [Připojení disku systému pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3.  Ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojený systémový disk.

#### <a name="mitigation-1"></a>Zmírnění dopadů 1

Zobrazit [jak do brány Firewall povolte nebo zakažte pravidlo v dokumentu operačního systému hosta]().

#### <a name="mitigation-2"></a>Zmírnění dopadů 2

1.  [Připojení disku systému pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3.  Po systémový disk připojen k virtuální počítač pro obnovení, ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.

4.  Spusťte instanci příkazového řádku se zvýšenými oprávněními a spusťte následující skript:

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

5.  [Odpojení disku a znovu vytvořte virtuální počítač](troubleshoot-recovery-disks-portal-windows.md).

6.  Zkontrolujte, zda byl problém vyřešen.
