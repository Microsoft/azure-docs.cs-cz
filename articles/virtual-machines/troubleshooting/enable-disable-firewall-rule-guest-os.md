---
title: Povolení nebo zakázání pravidla brány firewall v hostovaném operačním systému na virtuálním počítači Azure | Microsoft Docs
description: Naučte se používat online nebo offline nástroje pro vzdálenou práci nebo nastavení registru a povolit nebo zakázat pravidla brány firewall operačního systému hosta na vzdáleném virtuálním počítači Azure.
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
ms.openlocfilehash: 17616a223292ec07186b0a3fba264400423977ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87058764"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Povolení nebo zakázání pravidla brány firewall na hostovaném operačním systému virtuálního počítače Azure

Tento článek poskytuje referenční informace pro řešení potíží s situací, ve které máte podezření, že brána firewall hostovaného operačního systému filtruje částečný provoz na virtuálním počítači (VM). To může být užitečné z následujících důvodů:

*   Pokud byla změna záměrně provedena v bráně firewall, která způsobila selhání připojení RDP, může problém vyřešit pomocí funkce rozšíření vlastních skriptů.

*   Vypnutí všech profilů brány firewall je mnohem foolproof způsob, jak řešit potíže s nastavením pravidla brány firewall specifického pro protokol RDP.

## <a name="solution"></a>Řešení

Způsob konfigurace pravidel brány firewall závisí na úrovni přístupu k požadovanému virtuálnímu počítači. Následující příklady používají pravidla protokolu RDP. Stejné metody ale můžete použít na jakýkoli jiný typ provozu tak, že přejdete na správný klíč registru.

### <a name="online-troubleshooting"></a>Online řešení potíží 

#### <a name="mitigation-1-custom-script-extension"></a>Zmírnění 1: rozšíření vlastních skriptů

1.  Pomocí následující šablony Vytvořte skript.

    *   Postup při povolení pravidla:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Zakázání pravidla:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Nahrajte tento skript do Azure Portal pomocí funkce [rozšíření vlastních skriptů](../extensions/custom-script-windows.md) . 

#### <a name="mitigation-2-remote-powershell"></a>Zmírnění 2: vzdálené prostředí PowerShell

Pokud je virtuální počítač online a dá se k němu přistoupit na jiném virtuálním počítači ve stejné virtuální síti, můžete na základě druhého virtuálního počítače udělat zmírňující rizika.

1.  Na virtuálním počítači pro řešení potíží otevřete okno konzoly PowerShellu.

2.  Podle potřeby spusťte následující příkazy.

    *   Postup při povolení pravidla:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Zakázání pravidla:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Zmírnění 3: nástroj PsTools příkazy

Pokud je virtuální počítač online a dá se k němu přistoupit na jiném virtuálním počítači ve stejné virtuální síti, můžete na základě druhého virtuálního počítače udělat zmírňující rizika.

1.  Na virtuálním počítači pro řešení potíží stáhněte [Nástroj PsTools](/sysinternals/downloads/pstools).

2.  Otevřete instanci CMD a získejte přístup k virtuálnímu počítači přes jeho interní IP adresu (DIP). 

    * Postup při povolení pravidla:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Zakázání pravidla:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Zmírnění 4: vzdálený registr

Pokud je virtuální počítač online a lze k němu přistupovat na jiném virtuálním počítači ve stejné virtuální síti, můžete použít [Vzdálený registr](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na jiném virtuálním počítači.

1.  Na virtuálním počítači pro řešení potíží spusťte Editor registru (regedit.exe) a pak vyberte **soubor**  >  **připojit síťový registr**.

2.  Otevřete *cílovou větev počítač*\System a zadejte následující hodnoty:

    * Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        \SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP *CÍLOVÉHO počítače*
    
        Pak změňte hodnotu **aktivní = false** na **aktivní = true** v řetězci:

        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`
    
    * Pravidlo zakážete otevřením následující hodnoty registru:
    
        \SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP *CÍLOVÉHO počítače*

        Pak změňte hodnotu **aktivní = true** na **aktivní = false**:
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

3.  Aby se změny projevily, restartujte virtuální počítač.

### <a name="offline-troubleshooting"></a>Řešení potíží offline 

Pokud nemůžete získat přístup k virtuálnímu počítači pomocí žádné metody, rozšíření vlastních skriptů se nezdaří a bude nutné pracovat v režimu OFFLINE přímo na systémovém disku.

Než budete postupovat podle těchto kroků, pořiďte snímek systémového disku ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

1.  [Připojte systémový disk k virtuálnímu počítači pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše virtuálního počítače pro obnovení.

3.  Ujistěte se, že je disk označen jako **online** v konzole pro správu disků. Všimněte si, že písmeno jednotky přiřazené k připojenému systémovému disku.

4.  Než provedete jakékoli změny, vytvořte kopii složky \Windows\System32\Config v případě, že je nutné vrátit zpět změny.

5.  Na virtuálním počítači pro řešení potíží spusťte Editor registru (regedit.exe).

6.  Zvýrazněte **HKEY_LOCAL_MACHINE** klíč a potom z nabídky vyberte **File**možnost  >  **načíst** soubor.

    ![Programu](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Vyhledejte a otevřete soubor \windows\system32\config\SYSTEM. 

    > [!Note]
    > Zobrazí se výzva k zadání názvu. Zadejte **BROKENSYSTEM**a potom rozbalte **HKEY_LOCAL_MACHINE**. Zobrazí se teď další klíč s názvem **BROKENSYSTEM**. Pro účely tohoto řešení se tyto podregistry namontují jako **BROKENSYSTEM**.

8.  Ve větvi BROKENSYSTEM proveďte následující změny:

    1.  Ověřte, ze kterého klíče registru **CONTROLSET** se virtuální počítač spouští. V HKLM\BROKENSYSTEM\Select\Current. se zobrazí jeho klíčové číslo.

    2.  Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Pak změňte **hodnotu aktivní = false** na **aktivní = true**.
        
        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

    3.  Pokud chcete pravidlo zakázat, otevřete následující klíč registru:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Pak změňte hodnotu **aktivní = true** na **aktivní = false**.
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

9.  Zvýrazněte **BROKENSYSTEM**a pak z nabídky vyberte **soubor**  >  **Uvolnit podregistr** .

10. [Odpojte systémový disk a vytvořte virtuální počítač znovu](troubleshoot-recovery-disks-portal-windows.md).

11. Ověřte, zda je problém vyřešen.
