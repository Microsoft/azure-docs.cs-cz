---
title: Povolení nebo zakázání pravidla brány firewall u hostovaného operačního systému ve virtuálním počítači Azure | Dokumenty společnosti Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71086448"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Povolení nebo zakázání pravidla brány firewall na hostovaném operačním systému virtuálního počítače Azure

Tento článek obsahuje odkaz na řešení potíží se situací, ve které máte podezření, že brána firewall hostovaného operačního systému filtruje částečný provoz na virtuálním počítači (VM). To může být užitečné z následujících důvodů:

*   Pokud byla provedena změna brány firewall, která způsobila selhání připojení RDP, může problém vyřešit pomocí funkce Rozšíření vlastního skriptu.

*   Zakázání všech profilů brány firewall je spolehlivější způsob řešení potíží než nastavení pravidla brány firewall specifického pro zásady obnovení služby RDP.

## <a name="solution"></a>Řešení

Způsob konfigurace pravidel brány firewall závisí na úrovni přístupu k virtuálnímu počítači, který je povinný. Následující příklady používají pravidla RDP. Stejné metody však lze použít pro jakýkoli jiný druh provozu odkazem na správný klíč registru.

### <a name="online-troubleshooting"></a>Řešení potíží online 

#### <a name="mitigation-1-custom-script-extension"></a>Zmírnění 1: Vlastní rozšíření skriptu

1.  Vytvořte skript pomocí následující šablony.

    *   Povolení pravidla:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Zakázání pravidla:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Nahrajte tento skript na portál Azure pomocí funkce [Rozšíření vlastního skriptu.](../extensions/custom-script-windows.md) 

#### <a name="mitigation-2-remote-powershell"></a>Zmírnění skutečnosti 2: Vzdálené prostředí PowerShell

Pokud je virtuální počítač online a je přístupný na jiném virtuálním počítači ve stejné virtuální síti, můžete provést následující skutečnosti snižující závažnost rizika pomocí jiného virtuálního počítače.

1.  Na řešení potíží s virtuálním počítačem otevřete okno konzoly PowerShellu.

2.  Podle potřeby spusťte následující příkazy.

    *   Povolení pravidla:
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

#### <a name="mitigation-3-pstools-commands"></a>Zmírnění 3: PSTools příkazy

Pokud je virtuální počítač online a je přístupný na jiném virtuálním počítači ve stejné virtuální síti, můžete provést následující skutečnosti snižující závažnost rizika pomocí jiného virtuálního počítače.

1.  Na řešení potíží s virtuálním počítačem stáhněte [pstools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Otevřete instanci CMD a získejte přístup k virtuálnímu virtuálnímu ms prostřednictvím jeho interní IP adresy (DIP). 

    * Povolení pravidla:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Zakázání pravidla:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Zmírnění 4: Vzdálený registr

Pokud je virtuální počítač online a je přístupný na jiném virtuálním počítači ve stejné virtuální síti, můžete použít [vzdálený registr](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na druhém virtuálním počítači.

1.  Na řešení potíží s virtuálním počítačem spusťte Editor registru (regedit.exe) a vyberte**Registr sítě připojení** **souborů** > .

2.  Otevřete větev *TARGET MACHINE*\SYSTEM a zadejte následující hodnoty:

    * Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        *CÍLOVÝ POČÍTAČ*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Potom změňte **Active=FALSE** na **Active=TRUE** v řetězci:

        **v2.22| Akce=Povolit| Aktivní=PRAVDA| Dir=In| Protokol=6| Profil=Doména| Profil=Soukromé| Profil=Veřejné| LPort=3389| App=%Kořen_systému%\system32\svchost.exe| Svc=termservice| Název\@= FirewallAPI.dll,-28775| Desc\@= FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**
    
    * Chcete-li zakázat pravidlo, otevřete následující hodnotu registru:
    
        *CÍLOVÝ POČÍTAČ*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Potom změňte **hodnotu Aktivní =TRUE** na **Active=FALSE**:
        
        **v2.22| Akce=Povolit| Active=NEPRAVDA| Dir=In| Protokol=6| Profil=Doména| Profil=Soukromé| Profil=Veřejné| LPort=3389| App=%Kořen_systému%\system32\svchost.exe| Svc=termservice| Název\@= FirewallAPI.dll,-28775| Desc\@= FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

3.  Restartujte virtuální počítač, chcete-li použít změny.

### <a name="offline-troubleshooting"></a>Řešení potíží offline 

Pokud nemáte přístup k virtuálnímu počítače žádnou metodou, pomocí rozšíření vlastního skriptu se nezdaří a budete muset pracovat v režimu OFFLINE tím, že pracuje přímo prostřednictvím systémového disku.

Než budete postupovat podle těchto kroků, pořiďte snímek systémového disku postiženého virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

1.  [Připojte systémový disk k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.

3.  Zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Všimněte si, že písmeno jednotky, které je přiřazeno k připojenému systémovému disku.

4.  Před prováděním jakýchkoli změn vytvořte kopii složky \windows\system32\config v případě, že je nutné změny vrátit zpět.

5.  Na řešení potíží s virtuálním počítačem spusťte Editor registru (regedit.exe).

6.  Zvýrazněte **HKEY_LOCAL_MACHINE** klíč a pak z nabídky vyberte**Podregistr načtení** **souboru.** > 

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Vyhledejte a otevřete soubor \windows\system32\config\SYSTEM. 

    > [!Note]
    > Budete vyzváni k zadání jména. Zadejte **příkaz BROKENSYSTEM**a rozbalte **HKEY_LOCAL_MACHINE**. Nyní se zobrazí další klíč s názvem **BROKENSYSTEM**. Pro toto řešení potíží jsme montáž těchto problémových úly jako **BROKENSYSTEM**.

8.  Ve větvi BROKENSYSTEM proveďte následující změny:

    1.  Zkontrolujte, který klíč registru **ControlSet** virtuální ho spouštěný. Číslo klíče uvidíte v hklm\BROKENSYSTEM\Select\Current.

    2.  Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Potom změňte **Active=FALSE** na **Active=True**.
        
        **v2.22| Akce=Povolit| Aktivní=PRAVDA| Dir=In| Protokol=6| Profil=Doména| Profil=Soukromé| Profil=Veřejné| LPort=3389| App=%Kořen_systému%\system32\svchost.exe| Svc=termservice| Název\@= FirewallAPI.dll,-28775| Desc\@= FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

    3.  Chcete-li zakázat pravidlo, otevřete následující klíč registru:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Potom změňte **Active=True** to **Active=FALSE**.
        
        **v2.22| Akce=Povolit| Active=NEPRAVDA| Dir=In| Protokol=6| Profil=Doména| Profil=Soukromé| Profil=Veřejné| LPort=3389| App=%Kořen_systému%\system32\svchost.exe| Svc=termservice| Název\@= FirewallAPI.dll,-28775| Desc\@= FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

9.  Zvýrazněte **funkce BROKENSYSTEM**a v nabídce vyberte**Příkaz Uvolněte** **File** > podregistr soubor.

10. [Odpojte systémový disk a znovu vytvořte virtuální hod](troubleshoot-recovery-disks-portal-windows.md).

11. Zkontrolujte, zda byl problém vyřešen.
