---
title: 'Kurz: integrace jedné doménové struktury AD do Azure pomocí KOSMETICE'
description: Ukazuje, jak nastavit hybridní prostředí identity pomocí synchronizace hodnot hash hesel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82dcc633f0f2bb13cb84f80873117f9eb94a24f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96176116"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Kurz: integrace jedné doménové struktury služby AD pomocí synchronizace hodnot hash hesel (KOSMETICE)

![Vytvořit](media/tutorial-password-hash-sync/diagram.png)

Následující kurz vás provede vytvořením hybridního prostředí identity pomocí synchronizace hodnot hash hesel.  Toto prostředí se pak dá použít k testování nebo pro získání více znalostí, jak hybridní identita funguje.

## <a name="prerequisites"></a>Předpoklady
Níže jsou uvedené předpoklady nezbytné pro dokončení tohoto kurzu.
- Počítač s nainstalovanou [technologií Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) .  Tento postup je navržený na počítači s [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) nebo [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) .
- [Externí síťový adaptér](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) , který umožňuje, aby virtuální počítač komunikoval s internetem.
- [Předplatné Azure](https://azure.microsoft.com/free)
- Kopie systému Windows Server 2016

> [!NOTE]
> V tomto kurzu se používají skripty prostředí PowerShell, díky kterým můžete v nejrychlejší době vytvořit prostředí kurzu.  Každý ze skriptů používá proměnné, které jsou deklarovány na začátku skriptu.  Proměnné můžete změnit tak, aby odrážely vaše prostředí.
>
>Pomocí skriptů se před instalací Azure AD Connect vytvořit obecné prostředí Active Directory.  Jsou relevantní pro všechny kurzy.
>
> Kopie skriptů PowerShellu, které se používají v tomto kurzu, jsou k dispozici [na GitHubu](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
První věc, kterou musíme udělat, je vytvoření virtuálního počítače, který se bude používat jako náš místní server služby Active Directory, za účelem zprovoznění našeho prostředí hybridních identit.  Postupujte následovně:

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
Teď, když máme virtuální počítač, musíme před instalací služby Active Directory udělat několik věcí.  To znamená, že musíme virtuální počítač přejmenovat, nastavit statickou IP adresu a informace DNS a nainstalovat nástroje pro vzdálenou správu serveru.   Postupujte následovně:

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
Teď, když jsme vytvořili virtuální počítač a přejmenovali a má statickou IP adresu, můžeme pokračovat a nainstalovat a nakonfigurovat Active Directory Domain Services.  Postupujte následovně:

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
Teď, když máme prostředí Active Directory, musíme otestovat účet.  Tento účet se vytvoří v místním prostředí AD a pak se synchronizuje do Azure AD.  Postupujte následovně:

1. Otevřete PowerShellový ISE jako správce.
2. Spusťte následující skript.

```powershell 
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
Nyní musíme vytvořit tenanta Azure AD, abychom mohli synchronizovat naše uživatele s cloudem.  Při vytvoření nového tenanta Azure AD postupujte podle následujících pokynů.

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Vyberte **ikonu plus (+)** a vyhledejte **Azure Active Directory**.
3. Vyberte ve výsledcích hledání **Azure Active Directory**.
4. Vyberte **Vytvořit**.</br>
![Snímek obrazovky, který ukazuje, jak vytvořit tenanta Azure AD.](media/tutorial-password-hash-sync/create1.png)</br>
5. Zadejte **název organizace** společně s **počátečním názvem domény**. Potom vyberte **Vytvořit**. Vytvoří se váš adresář.
6. Po dokončení této možnosti klikněte na odkaz **tady** a adresář spravujte.

## <a name="create-a-global-administrator-in-azure-ad"></a>Vytvoření globálního správce v Azure AD
Teď, když máme tenanta Azure AD, vytvoříme účet globálního správce.  Tento účet se používá k vytvoření účtu konektoru Azure AD během Azure AD Connect instalace.  Účet konektoru Azure AD se používá k zápisu informací do služby Azure AD.   Účet globálního správce vytvoříte takto.

1.  V části **Spravovat** vyberte **Uživatelé**.</br>
![Snímek obrazovky, který zobrazuje možnost uživatele vybranou v části Správa, kde vytvoříte globálního správce v Azure AD.](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Vyberte **Všichni uživatelé** a pak vyberte **+ Nový uživatel**.
3.  Zadejte jméno a uživatelské jméno uživatele. To bude globální správce pro tenanta. Také budete chtít změnit **roli adresáře** na **globální správce.** A můžete i zobrazit dočasné heslo. Po dokončení vyberte **Vytvořit**.</br>
![Snímek obrazovky, který zobrazuje tlačítko pro vytvoření, když vytváříte globálního správce v Azure AD.](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Až to dokončíte, otevřete nový webový prohlížeč a přihlaste se k myapps.microsoft.com pomocí nového účtu globálního správce a dočasného hesla.
5. Změňte heslo pro globálního správce na něco, co si pamatujete.

## <a name="download-and-install-azure-ad-connect"></a>Stažení a instalace Azure AD Connect
Teď je čas stáhnout a nainstalovat Azure AD Connect.  Po instalaci se spustí prostřednictvím Expresní instalace.  Postupujte následovně:

1. Stáhnout [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Přejděte k souboru **AzureADConnect.msi** a dvakrát na něj klikněte.
3. Na uvítací obrazovce zaškrtněte políčko, kterým odsouhlasíte licenční podmínky, a klikněte na **Pokračovat**.  
4. Na obrazovce expresního nastavení klikněte na **Použít expresní nastavení**.</br>  
![Snímek obrazovky, který zobrazuje obrazovku expresních nastavení a tlačítko použít expresní nastavení.](media/tutorial-password-hash-sync/express1.png)</br>
5. Na obrazovce připojit k Azure AD zadejte uživatelské jméno a heslo pro globálního správce služby Azure AD. Klikněte na **Next** (Další).  
6. Na obrazovce Připojení ke službě AD DS zadejte uživatelské jméno a heslo pro účet správce podnikové sítě. Klikněte na **Next** (Další).  
7. Na obrazovce Připraveno ke konfiguraci klikněte na **Nainstalovat**.
8. Až instalace skončí, klikněte na **Konec**.
9. Po dokončení instalace se odhlaste a znovu se přihlaste, než použijete Synchronization Service Manager nebo editor pravidel synchronizace.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Ověřte, že se vytvářejí uživatelé a dochází k synchronizaci.
Nyní bude ověřeno, že uživatelé, kteří byli v místním adresáři, byli synchronizováni a nyní existují v tenantovi Azure AD.  Mějte na paměti, že dokončení tohoto může trvat několik hodin.  Chcete-li ověřit, zda jsou uživatelé synchronizováni, postupujte následovně.


1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Na levé straně vyberte **Azure Active Directory**
3. V části **Spravovat** vyberte **Uživatelé**.
4. Ověřte, že se v našem tenantovi zobrazují noví uživatelé.</br>
![Synchronizace](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Vyzkoušejte si přihlašování jedním z našich uživatelů

1. Přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Přihlaste se pomocí uživatelského účtu vytvořeného v našem novém tenantovi.  Budete se muset přihlásit pomocí následujícího formátu: ( user@domain.onmicrosoft.com ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.</br>
   ![Ověření](media/tutorial-password-hash-sync/verify1.png)</br>

Nyní jste úspěšně nastavili hybridní prostředí identity, které můžete použít k otestování a seznámení s tím, co Azure nabízí.

## <a name="next-steps"></a>Další kroky


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)|