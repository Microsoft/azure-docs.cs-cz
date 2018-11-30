---
title: Vypnout hostovaný operační systém brány Firewall na virtuálním počítači Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: b0cd20278287b41dd953c64044b705aa2dba7557
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319404"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Vypnout hostovaný operační systém brány Firewall na virtuálním počítači Azure

Tento článek poskytuje odkaz pro situace, ve kterých je podezření, že brána firewall operačního systému hosta je filtrování částečné nebo úplné provozu do virtuálních počítačů (VM). Tato situace může nastat, pokud úmyslně provedly se změny brány firewall, která způsobila selhání připojení RDP.

## <a name="solution"></a>Řešení

Proces, který je popsaný v tomto článku je určena pro použití tento problém obejdete tak, aby se mohli soustředit na řešení skutečných problém, tedy jak správně nastavit pravidla brány firewall. It\rquote s Microsoft osvědčeným postupem je součást brány Windows Firewall povolena. Jak nakonfigurovat \cf3 pravidla brány firewall, závisí na úroveň přístupu k s that\rquote virtuálního počítače vyžaduje.

### <a name="online-solutions"></a>Online řešení 

Pokud virtuální počítač je v režimu online a je přístupný jinému virtuálnímu počítači ve stejné virtuální síti, můžete vytvořit tyto způsoby zmírnění rizik pomocí druhým virtuálním Počítačem.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Zmírnění 1: Rozšíření vlastních skriptů nebo spustit příkaz funkci

Pokud máte funkční agenta služby Azure, můžete použít [rozšíření vlastních skriptů](../extensions/custom-script-windows.md) nebo [spustit příkazy](../windows/run-command.md) funkci (virtuální počítače Resource Manageru jenom) pro vzdálené spuštění těchto skriptů.

> [!Note]
> * Pokud je brána firewall nastavená místně, spusťte následující skript:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Pokud brána firewall je nastavená prostřednictvím zásady služby Active Directory, můžete spustit následující skript pro dočasný přístup. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Ale co nejdříve zásady se použijí znovu, můžete budete má zahájit mimo vzdálenou relaci. Trvalé řešení tohoto problému je upravit zásady, které jsou použity na tomto počítači.

#### <a name="mitigation-2-remote-powershell"></a>Zmírnění dopadů 2: Vzdálený PowerShell

1.  Připojte se k virtuálnímu počítači, který je umístěný ve stejné virtuální síti jako virtuální počítač, který nemůže získat přístup pomocí připojení RDP.

2.  Otevřete okno konzole Powershellu.

3.  Spusťte následující příkazy:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Pokud je brána firewall nastavená pomocí objektu zásad skupiny, tato metoda nemusí fungovat, protože tento příkaz změní pouze položky místního registru. Pokud je zásada na místě, přepíše tuto změnu. 

#### <a name="mitigation-3-pstools-commands"></a>Zmírnění 3: Příkazy PSTools

1.  Řešení potíží virtuálního počítače, stáhněte si [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Spusťte instanci CMD a přejděte k virtuálnímu počítači prostřednictvím jeho vyhrazené IP adresy.

3.  Spusťte následující příkazy:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Zmírnění 4: Vzdálený registr 

Použijte následující postup použijte [Remote Registry](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Na virtuálním počítači řešení potíží spusťte editor registru a přejděte na **souboru** > **připojit síťový registr**.

2.  Otevřete *cílový počítač*\SYSTEM větve a zadejte následující hodnoty:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Restartujte službu. Protože nemůžete udělat pomocí vzdálený registr, je nutné použít odebrat konzoly služby.

4.  Spusťte instanci **Services.msc**.

5.  Klikněte na tlačítko **služby (místní počítač)**.

6.  Vyberte **připojit k jinému počítači**.

7.  Zadejte **privátní IP adresa (DIP)** problému virtuálního počítače.

8.  Restartujte zásadu místní brány firewall.

9.  Pokuste se znovu připojit k virtuálnímu počítači pomocí protokolu RDP ze svého místního počítače.

### <a name="offline-solutions"></a>Offline řešení 

Pokud máte situace, ve kterém není možné dosáhnout virtuálního počítače pomocí libovolné metody rozšíření vlastních skriptů se nezdaří a budete muset pracovat v OFFLINE režimu při práci přímo prostřednictvím systémový disk. Provedete to podle těchto kroků:

1.  [Připojení disku systému pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

3.  Ujistěte se, že je disk označený jako Online v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojený systémový disk.

4.  Než provedete jakékoli úpravy, vytvořte kopii složky \windows\system32\config v případě, že je nutné vrátit zpět změny.

5.  Na virtuálním počítači řešení potíží spusťte editor registru (regedit.exe). 

6.  Pro tento postup řešení potíží jsme se připojení podregistry jako BROKENSYSTEM a BROKENSOFTWARE.

7.  Zvýrazněte klíči HKEY_LOCAL_MACHINE a potom vyberte soubor > načíst Hive v nabídce.

8.  Vyhledejte soubor \windows\system32\config\SYSTEM na připojený systémový disk.

9.  Spusťte instanci prostředí PowerShell se zvýšenými oprávněními a spusťte následující příkazy:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Odpojení disku a znovu vytvořte virtuální počítač](troubleshoot-recovery-disks-portal-windows.md).

11. Zkontrolujte, zda byl problém vyřešen.
