---
title: Povolit nebo zakázat pravidlo brány firewall na hostovaný operační systém na virtuálním počítači Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: cb2c548a94a91fe9126f684e382e9626adb93dd6
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "52319460"
---
# <a name="enable-or-disable-a-firewall-rule-on-a-azure-vm-guest-os"></a>Povolit nebo zakázat pravidlo brány firewall na hostovaný operační systém virtuálního počítače Azure

Tento článek poskytuje odkaz pro řešení potíží s situace, ve kterém máte podezření, že brána firewall operačního systému hosta je filtrování částečné přenosy na virtuální počítač (VM). To může být užitečné z následujících důvodů:

*   Pokud úmyslně provedené změny do brány firewall, která způsobila selhání připojení RDP, pomocí funkce rozšíření vlastních skriptů můžete problém vyřešit.

*   Zakázání všech profilů brány firewall je více spolehlivá způsob řešení potíží s než nastavení pravidla brány firewall specifické pro protokol RDP.

## <a name="solution"></a>Řešení

Jak nakonfigurovat pravidla brány firewall, závisí na úroveň přístupu k virtuálnímu počítači, který je potřeba. Následující příklady používají pravidla protokolu RDP. Však stejně jako lze použít na jakýkoli jiný druh provoz najetím myší na správný klíč registru.

### <a name="online-troubleshooting"></a>Řešení potíží s online 

#### <a name="mitigation-1-custom-script-extension"></a>Zmírnění dopadů 1: Rozšíření vlastních skriptů

1.  Vytvořte váš skript pomocí následující šablony.

    *   Pokud chcete povolit pravidlo:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Postup při zakázání pravidla:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Uložení v Azure portal pomocí tohoto skriptu [rozšíření vlastních skriptů](../extensions/custom-script-windows.md) funkce. 

#### <a name="mitigation-2-remote-powershell"></a>Zmírnění dopadů 2: Vzdáleného prostředí PowerShell

Pokud virtuální počítač je v režimu online a je přístupný jinému virtuálnímu počítači ve stejné virtuální síti, můžete provést podle zmírnění pomocí druhým virtuálním Počítačem.

1.  Řešení potíží virtuální počítač otevřete okno konzole Powershellu.

2.  Spusťte následující příkazy, podle potřeby.

    *   Pokud chcete povolit pravidlo:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Postup při zakázání pravidla:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Zmírnění dopadů 3: Příkazy PSTools

Pokud virtuální počítač je v režimu online a je přístupný jinému virtuálnímu počítači ve stejné virtuální síti, můžete provést podle zmírnění pomocí druhým virtuálním Počítačem.

1.  Řešení potíží virtuálního počítače, stáhněte si [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Spusťte instanci CMD a přístup k virtuálnímu počítači prostřednictvím jeho vnitřní IP (DIP). 

    * Pokud chcete povolit pravidlo:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Postup při zakázání pravidla:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Zmírnění dopadů 4: Vzdálený registr

Pokud virtuální počítač je v režimu online a je přístupný jinému virtuálnímu počítači ve stejné virtuální síti, můžete použít [Remote Registry](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na jiný virtuální počítač.

1.  Na virtuálním počítači řešení potíží spusťte Editor registru (regedit.exe) a pak vyberte **souboru** > **připojit síťový registr**.

2.  Otevřít *cílový počítač*\SYSTEM větev a potom zadejte následující hodnoty:

    * Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        *CÍLOVÝ počítač*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Poté změňte **aktivní = FALSE** k **aktivní = TRUE** v řetězci:

        **v2.22 | Akce = Povolit | Aktivní = TRUE | Příkaz dir = In | Protokol = 6 | Profil domény = | Profil = Private | Profil = Public | LPort = 3389 | App=%SystemRoot%\System32\svchost.exe| SVC = inicializace | Název =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**
    
    * Chcete-li zakázat pravidlo, otevřete následující hodnotu registru:
    
        *CÍLOVÝ počítač*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Poté změňte **aktivní = TRUE** k **aktivní = FALSE**:
        
        **v2.22 | Akce = Povolit | Aktivní = FALSE | Příkaz dir = In | Protokol = 6 | Profil domény = | Profil = Private | Profil = Public | LPort = 3389 | App=%SystemRoot%\System32\svchost.exe| SVC = inicializace | Název =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**

3.  Restartujte virtuální počítač, aby se změny projevily.

### <a name="offline-troubleshooting"></a>Řešení potíží s offline 

Pokud virtuální počítač nemá přístup k jakýmkoli způsobem, pomocí rozšíření vlastních skriptů se nezdaří a budete muset pracovat v OFFLINE režimu při práci přímo prostřednictvím systémový disk.

Než budete postupovat podle těchto kroků, pořiďte snímek systémový disk ovlivněných virtuálních počítačů jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

1.  [Připojení disku systému pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3.  Ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Všimněte si, že písmeno jednotky, který je přiřazen k připojený systémový disk.

4.  Než provedete jakékoli úpravy, vytvořte kopii složky \windows\system32\config v případě, že je nutné vrátit zpět změny.

5.  Na virtuálním počítači řešení potíží spusťte Editor registru (regedit.exe).

6.  Zvýrazněte **HKEY_LOCAL_MACHINE** klíče a pak vyberte **souboru** > **načíst Hive** z nabídky.

    ![Příkaz regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Vyhledejte a pak otevřete soubor \windows\system32\config\SYSTEM. 

    > [!Note]
    > Zobrazí se výzva k zadání jména. Zadejte **BROKENSYSTEM**a potom rozbalte **HKEY_LOCAL_MACHINE**. Nyní uvidíte Další klíč, který je pojmenován **BROKENSYSTEM**. Pro toto řešení potíží, jsme se připojení tyto podregistrů problému jako **BROKENSYSTEM**.

8.  Proveďte následující změny ve větvi BROKENSYSTEM:

    1.  Zkontrolujte, které **ControlSet** virtuální počítač se spouští z klíče registru. Zobrazí se jeho číslo klíče v HKLM\BROKENSYSTEM\Select\Current.

    2.  Chcete-li povolit pravidlo, otevřete následující hodnotu registru:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop přesměrovač v TCP
        
        Poté změňte **aktivní = FALSE** k **aktivní = True**.
        
        **v2.22 | Akce = Povolit | Aktivní = TRUE | Příkaz dir = In | Protokol = 6 | Profil domény = | Profil = Private | Profil = Public | LPort = 3389 | App=%SystemRoot%\System32\svchost.exe| SVC = inicializace | Název =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**

    3.  Pokud chcete zakázat pravidlo, otevřete následující klíč registru:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop přesměrovač v TCP

        Poté změňte **aktivní = True** k **aktivní = FALSE**.
        
        **v2.22 | Akce = Povolit | Aktivní = FALSE | Příkaz dir = In | Protokol = 6 | Profil domény = | Profil = Private | Profil = Public | LPort = 3389 | App=%SystemRoot%\System32\svchost.exe| SVC = inicializace | Název =@FirewallAPI.dll,-28775 | Desc =@FirewallAPI.dll,-28756 | EmbedCtxt =@FirewallAPI.dll,-28752 |**

9.  Zvýrazněte **BROKENSYSTEM**a pak vyberte **souboru** > **Uvolnit podregistr** z nabídky.

10. [Odpojení disku a znovu vytvořte virtuální počítač](troubleshoot-recovery-disks-portal-windows.md).

11. Zkontrolujte, zda byl problém vyřešen.