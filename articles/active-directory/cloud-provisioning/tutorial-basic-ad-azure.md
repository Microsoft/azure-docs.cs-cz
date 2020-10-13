---
title: Kurz – základní služba Active Directory místně a prostředí Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceffc928a0cf8313723ff6193d9a6a63f8465f90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266305"
---
# <a name="tutorial-basic-active-directory-environment"></a>Kurz: základní prostředí služby Active Directory

Tento kurz vás provede vytvořením základního prostředí Active Directory. 

![Diagram, který znázorňuje základní prostředí Azure A D.](media/tutorial-single-forest/diagram1.png)

Prostředí, které vytvoříte v tomto kurzu, můžete použít k otestování různých aspektů hybridních scénářů identity a pro některé z těchto kurzů se vyžaduje předpoklad.  Pokud již máte existující prostředí služby Active Directory, můžete ho použít jako náhradu.  Tyto informace jsou k dispozici pro jednotlivce, kteří začínají od Nothing.

Tento kurz se skládá z
## <a name="prerequisites"></a>Požadavky
Níže jsou uvedené předpoklady nezbytné pro dokončení tohoto kurzu.
- Počítač s nainstalovanou [technologií Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) .  Tento postup je navržený na počítači s [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) nebo [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) .
- [Externí síťový adaptér](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) , který umožňuje, aby virtuální počítač komunikoval s internetem.
- [Předplatné Azure](https://azure.microsoft.com/free)
- Kopie systému Windows Server 2016
- [Rozhraní Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> V tomto kurzu se používají skripty prostředí PowerShell, díky kterým můžete v nejrychlejší době vytvořit prostředí kurzu.  Každý ze skriptů používá proměnné, které jsou deklarovány na začátku skriptu.  Proměnné můžete změnit tak, aby odrážely vaše prostředí.
>
>Pomocí skriptů se před instalací agenta zřizování cloudu Azure AD Connect vytvořit obecné prostředí Active Directory.  Jsou relevantní pro všechny kurzy.
>
> Kopie skriptů PowerShellu, které se používají v tomto kurzu, jsou k dispozici [na GitHubu](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
První věc, kterou je třeba udělat, je vytvořit virtuální počítač, který se bude používat jako náš místní server služby Active Directory, aby bylo možné začít používat naše hybridní prostředí identity.  Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
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
Aby bylo možné dokončit vytváření virtuálního počítače, je nutné dokončit instalaci operačního systému.

1. Správce technologie Hyper-V, dvakrát klikněte na virtuální počítač.
2. Klikněte na tlačítko Start.
3. Zobrazí se výzva k zadání klávesy pro spuštění z disku CD nebo DVD. Pokračujte a udělejte to.
4. Na úvodní obrazovce Windows serveru vyberte svůj jazyk a klikněte na **Další**.
5. Klikněte na **instalovat hned**.
6. Zadejte svůj licenční klíč a klikněte na **Další**.
7. Zaškrtněte * * Přijímám licenční podmínky a klikněte na tlačítko **Další**.
8. Vyberte **vlastní: jenom instalovat Windows (rozšířené)**
9. Klikněte na **Další** .
10. Až se instalace dokončí, restartujte virtuální počítač, přihlaste se a spusťte aktualizace Windows, abyste měli jistotu, že je virtuální počítač nejaktuálnější.  Nainstalujte nejnovější aktualizace.

## <a name="install-active-directory-prerequisites"></a>Nainstalovat požadavky služby Active Directory
Teď, když máte virtuální počítač, musíte před instalací služby Active Directory udělat několik věcí.  To znamená, že budete muset virtuální počítač přejmenovat, nastavit statickou IP adresu a informace DNS a nainstalovat nástroje pro vzdálenou správu serveru.   Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
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

## <a name="create-a-windows-server-ad-environment"></a>Vytvoření prostředí Windows Server AD
Teď, když máte vytvořený virtuální počítač, který se přejmenoval a má statickou IP adresu, můžete pokračovat a nainstalovat a nakonfigurovat Active Directory Domain Services.  Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
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

## <a name="create-a-windows-server-ad-user"></a>Vytvoření uživatele Windows Server AD
Teď, když máte prostředí Active Directory, musíte otestovat účet.  Tento účet se vytvoří v místním prostředí AD a pak se synchronizuje do Azure AD.  Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
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
Teď je potřeba vytvořit tenanta Azure AD, abyste mohli synchronizovat naše uživatele s cloudem.  Při vytvoření nového tenanta Azure AD postupujte podle následujících pokynů.

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Vyberte **ikonu plus (+)** a vyhledejte **Azure Active Directory**.
3. Vyberte ve výsledcích hledání **Azure Active Directory**.
4. Vyberte **Vytvořit**.</br>
![Snímek obrazovky zobrazující stránku Azure Active Directory v Azure Portal](media/tutorial-single-forest/create1.png)</br>
5. Zadejte **název organizace** společně s **počátečním názvem domény**. Potom vyberte **Vytvořit**. Vytvoří se váš adresář.
6. Po dokončení této možnosti klikněte na odkaz **tady** a adresář spravujte.

## <a name="create-a-global-administrator-in-azure-ad"></a>Vytvoření globálního správce v Azure AD
Teď, když máte tenanta Azure AD, vytvoříte účet globálního správce.  Účet globálního správce vytvoříte takto.

1.  V části **Spravovat** vyberte **Uživatelé**.</br>
![Snímek obrazovky, který zobrazuje nabídku "Přehled" s vybranými možnostmi uživatelé](media/tutorial-single-forest/administrator1.png)</br>
2.  Vyberte **Všichni uživatelé** a pak vyberte **+ Nový uživatel**.
3.  Zadejte jméno a uživatelské jméno uživatele. To bude globální správce pro tenanta. Také budete chtít změnit **roli adresáře** na **globální správce.** A můžete i zobrazit dočasné heslo. Po dokončení vyberte **Vytvořit**.</br>
![Vytvoření](media/tutorial-single-forest/administrator2.png)</br>
4. Až to dokončíte, otevřete nový webový prohlížeč a přihlaste se k myapps.microsoft.com pomocí nového účtu globálního správce a dočasného hesla.
5. Změňte heslo pro globálního správce na něco, co si pamatujete.

## <a name="optional--additional-server-and-forest"></a>Volitelné: další server a doménová struktura
V následující části najdete volitelný oddíl, který popisuje kroky k vytvoření dalšího serveru a doménové struktury.  Tato možnost se dá použít v některých pokročilejších kurzech, jako je [pilotní nasazení Azure AD Connect ke zřízení cloudu](tutorial-pilot-aadc-aadccp.md).

Pokud potřebujete jenom další server, můžete ho zastavit po kroku – **Vytvoření virtuálního počítače** a připojení serveru k existující doméně, která byla vytvořená výše.  

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Otevřete PowerShellový ISE jako správce.
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
Aby bylo možné dokončit vytváření virtuálního počítače, je nutné dokončit instalaci operačního systému.

1. Správce technologie Hyper-V, dvakrát klikněte na virtuální počítač.
2. Klikněte na tlačítko Start.
3. Zobrazí se výzva k zadání klávesy pro spuštění z disku CD nebo DVD. Pokračujte a udělejte to.
4. Na úvodní obrazovce Windows serveru vyberte svůj jazyk a klikněte na **Další**.
5. Klikněte na **instalovat hned**.
6. Zadejte svůj licenční klíč a klikněte na **Další**.
7. Zaškrtněte * * Přijímám licenční podmínky a klikněte na tlačítko **Další**.
8. Vyberte **vlastní: jenom instalovat Windows (rozšířené)**
9. Klikněte na **Další** .
10. Až se instalace dokončí, restartujte virtuální počítač, přihlaste se a spusťte aktualizace Windows, abyste měli jistotu, že je virtuální počítač nejaktuálnější.  Nainstalujte nejnovější aktualizace.

### <a name="install-active-directory-prerequisites"></a>Nainstalovat požadavky služby Active Directory
Teď, když máte virtuální počítač, musíte před instalací služby Active Directory udělat několik věcí.  To znamená, že budete muset virtuální počítač přejmenovat, nastavit statickou IP adresu a informace DNS a nainstalovat nástroje pro vzdálenou správu serveru.   Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
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
### <a name="create-a-windows-server-ad-environment"></a>Vytvoření prostředí Windows Server AD
Teď, když máte vytvořený virtuální počítač, který se přejmenoval a má statickou IP adresu, můžete pokračovat a nainstalovat a nakonfigurovat Active Directory Domain Services.  Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
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

### <a name="create-a-windows-server-ad-user"></a>Vytvoření uživatele Windows Server AD
Teď, když máte prostředí Active Directory, musíte otestovat účet.  Tento účet se vytvoří v místním prostředí AD a pak se synchronizuje do Azure AD.  Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
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
Nyní máte prostředí, které lze použít pro existující kurzy a testování dalších funkcí, které poskytuje cloudové zřizování.

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)