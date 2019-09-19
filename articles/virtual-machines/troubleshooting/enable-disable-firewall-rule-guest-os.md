---
title: Povolení nebo zakázání pravidla brány firewall v hostovaném operačním systému na virtuálním počítači Azure | Microsoft Docs
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
ms.openlocfilehash: 782240c51833fc841af9f4260860db4c03897c03
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086448"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Povolení nebo zakázání pravidla brány firewall v hostovaném operačním systému virtuálního počítače Azure

Tento článek poskytuje referenční informace pro řešení potíží s situací, ve které máte podezření, že brána firewall hostovaného operačního systému filtruje částečný provoz na virtuálním počítači (VM). To může být užitečné z následujících důvodů:

*   Pokud byla změna záměrně provedena v bráně firewall, která způsobila selhání připojení RDP, může problém vyřešit pomocí funkce rozšíření vlastních skriptů.

*   Vypnutí všech profilů brány firewall je mnohem foolproof způsob, jak řešit potíže s nastavením pravidla brány firewall specifického pro protokol RDP.

## <a name="solution"></a>Řešení

Způsob konfigurace pravidel brány firewall závisí na úrovni přístupu k požadovanému virtuálnímu počítači. Následující příklady používají pravidla protokolu RDP. Stejné metody ale můžete použít na jakýkoli jiný typ provozu tak, že přejdete na správný klíč registru.

### <a name="online-troubleshooting"></a>Řešení potíží s online 

#### <a name="mitigation-1-custom-script-extension"></a>Zmírnění 1: Rozšíření vlastních skriptů

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

#### <a name="mitigation-2-remote-powershell"></a>Zmírnění 2: Vzdálené prostředí PowerShell

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

#### <a name="mitigation-3-pstools-commands"></a>Zmírnění 3: Příkazy nástroj PsTools

Pokud je virtuální počítač online a dá se k němu přistoupit na jiném virtuálním počítači ve stejné virtuální síti, můžete na základě druhého virtuálního počítače udělat zmírňující rizika.

1.  Na virtuálním počítači pro řešení potíží stáhněte [Nástroj PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

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

#### <a name="mitigation-4-remote-registry"></a>Zmírnění omezení 4: Vzdálený registr

Pokud je virtuální počítač online a lze k němu přistupovat na jiném virtuálním počítači ve stejné virtuální síti, můžete použít [Vzdálený registr](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na jiném virtuálním počítači.

1.  Na virtuálním počítači pro řešení potíží spusťte Editor registru (Regedit. exe) a pak vyberte **soubor** > **připojit síťový registr**.

2.  Otevřete *cílovou větev počítač*\System a zadejte následující hodnoty:

    * Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Pak změňte hodnotu **aktivní = false** na **aktivní = true** v řetězci:

        **v 2.22 | Action = Allow | Aktivní = TRUE | DIR = v | Protokol = 6 | Profil = doména | Profil = privátní | Profile = Public | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Název =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**
    
    * Pravidlo zakážete otevřením následující hodnoty registru:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Pak změňte hodnotu **aktivní = true** na **aktivní = false**:
        
        **v 2.22 | Action = Allow | Aktivní = FALSE | DIR = v | Protokol = 6 | Profil = doména | Profil = privátní | Profile = Public | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Název =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

3.  Aby se změny projevily, restartujte virtuální počítač.

### <a name="offline-troubleshooting"></a>Řešení potíží offline 

Pokud nemůžete získat přístup k virtuálnímu počítači pomocí žádné metody, rozšíření vlastních skriptů se nezdaří a bude nutné pracovat v režimu OFFLINE přímo na systémovém disku.

Než budete postupovat podle těchto kroků, pořiďte snímek systémový disk ovlivněných virtuálních počítačů jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

1.  [Připojení disku systému pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3.  Ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Všimněte si, že písmeno jednotky přiřazené k připojenému systémovému disku.

4.  Než provedete jakékoli změny, vytvořte kopii složky \Windows\System32\Config v případě, že je nutné vrátit zpět změny.

5.  Na virtuálním počítači pro řešení potíží spusťte Editor registru (Regedit. exe).

6.  Zvýrazněte klíč **HKEY_LOCAL_MACHINE** a v nabídce **Vyberte možnost** > **Načíst podregistr** .

    ![Programu](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Vyhledejte a otevřete soubor \windows\system32\config\SYSTEM. 

    > [!Note]
    > Zobrazí se výzva k zadání názvu. Zadejte **BROKENSYSTEM**a pak rozbalte **HKEY_LOCAL_MACHINE**. Zobrazí se teď další klíč s názvem **BROKENSYSTEM**. Pro účely tohoto řešení se tyto podregistry namontují jako **BROKENSYSTEM**.

8.  Ve větvi BROKENSYSTEM proveďte následující změny:

    1.  Ověřte, ze kterého klíče registru **CONTROLSET** se virtuální počítač spouští. V HKLM\BROKENSYSTEM\Select\Current. se zobrazí jeho klíčové číslo.

    2.  Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Pak změňte **hodnotu aktivní = false** na **aktivní = true**.
        
        **v 2.22 | Action = Allow | Aktivní = TRUE | DIR = v | Protokol = 6 | Profil = doména | Profil = privátní | Profile = Public | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Název =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

    3.  Pokud chcete pravidlo zakázat, otevřete následující klíč registru:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Pak změňte hodnotu **aktivní = true** na **aktivní = false**.
        
        **v 2.22 | Action = Allow | Aktivní = FALSE | DIR = v | Protokol = 6 | Profil = doména | Profil = privátní | Profile = Public | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Název =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

9.  Zvýrazněte **BROKENSYSTEM**a pak z nabídky vyberte **soubor** > **Uvolnit podregistr** .

10. [Odpojení disku a znovu vytvořte virtuální počítač](troubleshoot-recovery-disks-portal-windows.md).

11. Zkontrolujte, zda byl problém vyřešen.
