---
title: Zakázání brány firewall hostovaného operačního systému ve virtuálním počítači Azure | Dokumenty společnosti Microsoft
description: Naučte se metodu řešení pro řešení situací, kdy brána firewall hostovaného operačního systému filtruje částečný nebo úplný provoz na virtuální ms.
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
ms.openlocfilehash: e4cd1595d963330bd5decb366310bf5e97f59bc8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422367"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Zakázání brány firewall hostovaného operačního systému na virtuálním počítači Azure

Tento článek obsahuje odkaz na situace, ve kterých máte podezření, že brána firewall hostovaného operačního systému filtruje částečný nebo úplný provoz na virtuální počítač (VM). Tato situace může nastat, pokud byly provedeny změny brány firewall, které způsobily selhání připojení RDP.

## <a name="solution"></a>Řešení

Proces popsaný v tomto článku je určen k použití jako řešení, takže se můžete zaměřit na opravu skutečného problému, což je, jak správně nastavit pravidla brány firewall. Je osvědčeným postupem společnosti Microsoft mít povolenou součást brány Windows Firewall. Způsob konfigurace pravidel brány firewall závisí na úrovni přístupu k virtuálnímu počítači, který je požadován.

### <a name="online-solutions"></a>Online řešení 

Pokud je virtuální počítač online a je přístupný na jiném virtuálním počítači ve stejné virtuální síti, můžete provést tyto skutečnosti snižující závažnost rizika pomocí jiného virtuálního počítače.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Zmírnění rizika 1: Vlastní rozšíření skriptu nebo funkce Spustit příkaz

Pokud máte funkčního agenta Azure, můžete ke vzdálenému spuštění následujících skriptů použít [rozšíření vlastního skriptu](../extensions/custom-script-windows.md) nebo funkci [Spustit příkazy](../windows/run-command.md) (jenom virtuální počítače Správce prostředků).

> [!Note]
> * Pokud je brána firewall nastavena místně, spusťte následující skript:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Pokud je brána firewall nastavena pomocí zásad služby Active Directory, můžete pro dočasný přístup použít následující skript. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Jakmile je však zásada znovu použita, budete vyloučeni ze vzdálené relace. Trvalou opravou tohoto problému je změna zásady, která je použita v tomto počítači.

#### <a name="mitigation-2-remote-powershell"></a>Zmírnění skutečnosti 2: Vzdálené prostředí PowerShell

1.  Připojte se k virtuálnímu počítači, který je umístěn ve stejné virtuální síti jako virtuální počítač, ke kterému nedosáhnete pomocí připojení RDP.

2.  Otevřete okno konzoly PowerShellu.

3.  Spusťte následující příkazy:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Pokud je brána firewall nastavena prostřednictvím objektu zásad skupiny, tato metoda nemusí fungovat, protože tento příkaz změní pouze položky místního registru. Pokud je zásada na místě, přepíše tuto změnu. 

#### <a name="mitigation-3-pstools-commands"></a>Zmírnění 3: PSTools příkazy

1.  Na řešení potíží s virtuálním počítačem stáhněte [pstools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Otevřete instanci CMD a pak přistupte k virtuálnímu virtuálnímu mněmu prostřednictvím jeho DIP.

3.  Spusťte následující příkazy:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Zmírnění 4: Vzdálený registr 

Chcete-li použít [vzdálený registr,](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)postupujte takto .

1.  Na řešení potíží s virtuálním počítačem spusťte editor registru a přejděte na registr sítě **File** > **Connect .**

2.  Otevřete větev *TARGET MACHINE*\SYSTEM a zadejte následující hodnoty:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Restartujte službu. Vzhledem k tomu, že to nelze provést pomocí vzdáleného registru, je nutné použít službu Remote Service Console.

4.  Otevřete instanci **služby Services.msc**.

5.  Klepněte na **položku Služby (místní).**

6.  Vyberte **Připojit k jinému počítači**.

7.  Zadejte **privátní IP adresu (DIP)** problémového virtuálního soudu.

8.  Restartujte zásady místní brány firewall.

9.  Zkuste se znovu připojit k virtuálnímu počítači prostřednictvím rdp z místního počítače.

### <a name="offline-solutions"></a>Offline řešení 

Pokud máte situaci, ve které nelze dosáhnout virtuálního počítače žádnou metodou, rozšíření vlastního skriptu se nezdaří a budete muset pracovat v režimu OFFLINE tím, že pracuje přímo prostřednictvím systémového disku. Provedete to podle těchto kroků:

1.  [Připojte systémový disk k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

2.  Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.

3.  Zkontrolujte, zda je disk v konzole pro správu disků označen jako online. Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému systémovému disku.

4.  Před prováděním jakýchkoli změn vytvořte kopii složky \windows\system32\config v případě, že je nutné změny vrátit zpět.

5.  Na řešení potíží s virtuálním počítačem spusťte editor registru (regedit.exe). 

6.  Pro tento postup řešení potíží montujeme úly jako BROKENSYSTEM a BROKENSOFTWARE.

7.  Zvýrazněte HKEY_LOCAL_MACHINE klíč a pak z nabídky vyberte Podregistr načíst soubor >.

8.  Vyhledejte soubor \windows\system32\config\SYSTEM na připojeném systémovém disku.

9.  Otevřete instanci Prostředí PowerShell se zvýšenými oprávněními a spusťte následující příkazy:

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
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
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

10. [Odpojte systémový disk a znovu vytvořte virtuální hod](troubleshoot-recovery-disks-portal-windows.md).

11. Zkontrolujte, zda byl problém vyřešen.
