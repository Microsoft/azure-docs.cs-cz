---
title: Kurz – základní služba Active Directory v místním prostředí a prostředí Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356a05d4d92f17ceb66ff0208153ec3eac736757
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793895"
---
# <a name="tutorial-basic-active-directory-environment"></a>Kurz: Základní prostředí služby Active Directory

Tento kurz vás provede vytvořením základního prostředí služby Active Directory. 

![Vytvořit](media/tutorial-single-forest/diagram1.png)

Prostředí, které vytvoříte v kurzu, můžete použít k testování různých aspektů scénářů hybridní identity a bude předpokladem pro některé kurzy.  Pokud již máte existující prostředí služby Active Directory, můžete jej použít jako náhradu.  Tyto informace jsou poskytovány pro jednotlivce, kteří mi začínají od ničeho.

Tento výukový program se skládá
## <a name="prerequisites"></a>Požadavky
Níže jsou uvedeny požadavky potřebné pro dokončení tohoto kurzu
- Počítač s nainstalovaným [hyper-v.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview)  Doporučuje se to provést v počítači [se systémem Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) nebo [Windows Server 2016.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)
- [Externí síťový adaptér,](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) který umožňuje virtuálnímu počítači komunikovat s Internetem.
- [Předplatné Azure](https://azure.microsoft.com/free)
- Kopie Systému Windows Server 2016
- [Rozhraní Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Tento kurz používá skripty prostředí PowerShell, takže můžete vytvořit výukové prostředí v nejrychlejším čase.  Každý skript používá proměnné, které jsou deklarovány na začátku skriptů.  Můžete a měli byste změnit proměnné tak, aby odrážely vaše prostředí.
>
>Použité skripty vytvořit obecné prostředí služby Active Directory před instalací agenta azure ad connect cloud zřizování.  Jsou relevantní pro všechny výukové programy.
>
> Kopie skriptů Prostředí PowerShell, které se používají v tomto kurzu, jsou k dispozici na [GitHubu zde](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
První věc, kterou musíte udělat, abyste zprovoznili naše prostředí hybridní identity, je vytvoření virtuálního počítače, který bude použit jako náš místní server služby Active Directory.  Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>Dokončení nasazení operačního systému
Chcete-li dokončit vytváření virtuálního počítače, je třeba dokončit instalaci operačního systému.

1. Správce Hyper-V, poklepejte na virtuální stroj
2. Klikněte na tlačítko Start.
3. Budete vyzváni k 'Stisknutím libovolné klávesy pro spuštění z CD nebo DVD'. Jen do toho a udělejte to.
4. Na obrazovce Spuštění systému Windows Server vyberte jazyk a klepněte na tlačítko **Další**.
5. Klepněte na tlačítko **Nainstalovat .**
6. Zadejte licenční klíč a klepněte na tlačítko **Další**.
7. Zaškrtněte políčko **Souhlasím s licenčními podmínkami a klepněte na tlačítko **Další**.
8. Vybrat **vlastní: Instalace systému Windows pouze (upřesnit)**
9. Klikněte na **Další.**
10. Po dokončení instalace restartujte virtuální počítač, přihlaste se a spusťte aktualizace systému Windows, abyste zajistili, že virtuální počítač bude nejaktuálnější.  Nainstalujte nejnovější aktualizace.

## <a name="install-active-directory-prerequisites"></a>Instalace požadavků služby Active Directory
Nyní, když máte virtuální počítač nahoru, musíte udělat pár věcí před instalací služby Active Directory.  To znamená, že je třeba přejmenovat virtuální počítač, nastavit statickou IP adresu a informace DNS a nainstalovat nástroje pro vzdálenou správu serveru.   Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Vytvoření prostředí služby Windows Server AD
Teď, když jste vytvořili virtuální ho svirtuální ms a byl přejmenován a má statickou IP adresu, můžete pokračovat a nainstalovat a nakonfigurovat službu Active Directory Domain Services.  Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Vytvoření uživatele služby AD systému Windows Server
Nyní, když máte naše prostředí služby Active Directory, musíte provést testovací účet.  Tento účet se vytvoří v našem místním prostředí služby AD a pak se synchronizuje s Azure AD.  Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Vytvoření tenanta Azure AD
Teď je potřeba vytvořit klienta Azure AD, abyste mohli synchronizovat naše uživatele do cloudu.  Při vytvoření nového tenanta Azure AD postupujte podle následujících pokynů.

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Vyberte **ikonu plus (+)** a vyhledejte **Azure Active Directory**.
3. Vyberte ve výsledcích hledání **Azure Active Directory**.
4. Vyberte **Vytvořit**.</br>
![Vytvoření](media/tutorial-single-forest/create1.png)</br>
5. Zadejte **název organizace** společně s **počátečním názvem domény**. Pak vyberte **Vytvořit**. Vytvoří se váš adresář.
6. Po dokončení klikněte na odkaz **zde** a spravujte adresář.

## <a name="create-a-global-administrator-in-azure-ad"></a>Vytvoření globálního správce ve službě Azure AD
Teď, když máte klienta Azure AD, vytvoříte účet globálního správce.  Chcete-li vytvořit účet globálního správce, postupujte takto.

1.  V části **Spravovat** vyberte **Uživatele**.</br>
![Vytvoření](media/tutorial-single-forest/administrator1.png)</br>
2.  Vyberte **Všichni uživatelé** a pak vyberte **+ Nový uživatel**.
3.  Zadejte jméno a uživatelské jméno uživatele. To bude globální správce pro tenanta. Budete také chtít změnit **roli adresáře** na **globálního správce.** A můžete i zobrazit dočasné heslo. Po dokončení vyberte **Vytvořit**.</br>
![Vytvoření](media/tutorial-single-forest/administrator2.png)</br>
4. Po dokončení otevřete nový webový prohlížeč a přihlaste se k myapps.microsoft.com pomocí nového účtu globálního správce a dočasného hesla.
5. Změňte heslo globálního správce na něco, co si zapamatujete.

## <a name="optional--additional-server-and-forest"></a>Volitelné: Další server a doménová struktura
Následuje volitelná část, která poskytuje kroky k vytvoření dalšího serveru nebo doménové struktury.  To lze použít v některých pokročilejších kurzech, jako je [pilot pro Azure AD Connect ke zřizování cloudu](tutorial-pilot-aadc-aadccp.md).

Pokud potřebujete pouze další server, můžete zastavit po - Vytvořit krok **virtuálního počítače** a připojit server k existující doméně, která byla vytvořena výše.  

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>Dokončení nasazení operačního systému
Chcete-li dokončit vytváření virtuálního počítače, je třeba dokončit instalaci operačního systému.

1. Správce Hyper-V, poklepejte na virtuální stroj
2. Klikněte na tlačítko Start.
3. Budete vyzváni k 'Stisknutím libovolné klávesy pro spuštění z CD nebo DVD'. Jen do toho a udělejte to.
4. Na obrazovce Spuštění systému Windows Server vyberte jazyk a klepněte na tlačítko **Další**.
5. Klepněte na tlačítko **Nainstalovat .**
6. Zadejte licenční klíč a klepněte na tlačítko **Další**.
7. Zaškrtněte políčko **Souhlasím s licenčními podmínkami a klepněte na tlačítko **Další**.
8. Vybrat **vlastní: Instalace systému Windows pouze (upřesnit)**
9. Klikněte na **Další.**
10. Po dokončení instalace restartujte virtuální počítač, přihlaste se a spusťte aktualizace systému Windows, abyste zajistili, že virtuální počítač bude nejaktuálnější.  Nainstalujte nejnovější aktualizace.

### <a name="install-active-directory-prerequisites"></a>Instalace požadavků služby Active Directory
Nyní, když máte virtuální počítač nahoru, musíte udělat pár věcí před instalací služby Active Directory.  To znamená, že je třeba přejmenovat virtuální počítač, nastavit statickou IP adresu a informace DNS a nainstalovat nástroje pro vzdálenou správu serveru.   Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Vytvoření prostředí služby Windows Server AD
Teď, když jste vytvořili virtuální ho svirtuální ms a byl přejmenován a má statickou IP adresu, můžete pokračovat a nainstalovat a nakonfigurovat službu Active Directory Domain Services.  Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Vytvoření uživatele služby AD systému Windows Server
Nyní, když máte naše prostředí služby Active Directory, musíte provést testovací účet.  Tento účet se vytvoří v našem místním prostředí služby AD a pak se synchronizuje s Azure AD.  Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
2. Spusťte následující skript.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Závěr
Nyní máte prostředí, které lze použít pro existující kurzy a testování dalších funkcí cloud zřizování poskytuje.

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
