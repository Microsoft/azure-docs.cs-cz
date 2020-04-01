---
title: 'Kurz: Integrace jedné doménové struktury služby AD do Azure pomocí phs'
description: Ukazuje, jak nastavit prostředí hybridní identity pomocí synchronizace hash hesla.
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
ms.openlocfilehash: b17300fa69b61c7713c860e2a35e63fcb6584bc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "66474017"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Kurz: Integrace jedné doménové struktury služby AD pomocí synchronizace hash hesel (PHS)

![Vytvořit](media/tutorial-password-hash-sync/diagram.png)

Následující kurz vás provede vytvořením prostředí hybridní identity pomocí synchronizace hash hesel.  Toto prostředí pak lze použít pro testování nebo pro seznámení s tím, jak funguje hybridní identita.

## <a name="prerequisites"></a>Požadavky
Níže jsou uvedeny požadavky potřebné pro dokončení tohoto kurzu
- Počítač s nainstalovaným [hyper-v.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview)  Doporučuje se to provést v počítači [se systémem Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) nebo [Windows Server 2016.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)
- [Externí síťový adaptér,](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) který umožňuje virtuálnímu počítači komunikovat s Internetem.
- [Předplatné Azure](https://azure.microsoft.com/free)
- Kopie Systému Windows Server 2016

> [!NOTE]
> Tento kurz používá skripty prostředí PowerShell, takže můžete vytvořit výukové prostředí v nejrychlejším čase.  Každý skript používá proměnné, které jsou deklarovány na začátku skriptů.  Můžete a měli byste změnit proměnné tak, aby odrážely vaše prostředí.
>
>Použité skripty vytvořit obecné prostředí služby Active Directory před instalací Služby Azure AD Connect.  Jsou relevantní pro všechny výukové programy.
>
> Kopie skriptů Prostředí PowerShell, které se používají v tomto kurzu, jsou k dispozici na [GitHubu zde](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
První věc, kterou musíme udělat, abychom zprovoznili naše prostředí hybridní identity, je vytvoření virtuálního počítače, který bude použit jako náš místní server služby Active Directory.  Udělejte toto:

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
Nyní, když máme virtuální počítač nahoru, musíme udělat pár věcí před instalací služby Active Directory.  To znamená, že musíme přejmenovat virtuální počítač, nastavit statickou IP adresu a informace DNS a nainstalovat nástroje pro vzdálenou správu serveru.   Udělejte toto:

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
Teď, když jsme vytvořili virtuální hospodařící a byl přejmenován a má statickou IP adresu, můžeme pokračovat a nainstalovat a nakonfigurovat službu Active Directory Domain Services.  Udělejte toto:

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
Nyní, když máme prostředí služby Active Directory, potřebujeme testovací účet.  Tento účet se vytvoří v našem místním prostředí služby AD a pak se synchronizuje s Azure AD.  Udělejte toto:

1. Otevřete powershellovou ISE jako správce.
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
Teď musíme vytvořit klienta Azure AD, abychom mohli synchronizovat naše uživatele do cloudu.  Při vytvoření nového tenanta Azure AD postupujte podle následujících pokynů.

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Vyberte **ikonu plus (+)** a vyhledejte **Azure Active Directory**.
3. Vyberte ve výsledcích hledání **Azure Active Directory**.
4. Vyberte **Vytvořit**.</br>
![Vytvoření](media/tutorial-password-hash-sync/create1.png)</br>
5. Zadejte **název organizace** společně s **počátečním názvem domény**. Pak vyberte **Vytvořit**. Vytvoří se váš adresář.
6. Po dokončení klikněte na odkaz **zde** a spravujte adresář.

## <a name="create-a-global-administrator-in-azure-ad"></a>Vytvoření globálního správce ve službě Azure AD
Teď, když máme klienta Azure AD, vytvoříme účet globálního správce.  Tento účet se používá k vytvoření účtu Konektor Azure AD během instalace Azure AD Connect.  Účet Konektor Azure AD se používá k zápisu informací do Azure AD.   Chcete-li vytvořit účet globálního správce, postupujte takto.

1.  V části **Spravovat** vyberte **Uživatele**.</br>
![Vytvoření](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Vyberte **Všichni uživatelé** a pak vyberte **+ Nový uživatel**.
3.  Zadejte jméno a uživatelské jméno uživatele. To bude globální správce pro tenanta. Budete také chtít změnit **roli adresáře** na **globálního správce.** A můžete i zobrazit dočasné heslo. Po dokončení vyberte **Vytvořit**.</br>
![Vytvoření](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Po dokončení otevřete nový webový prohlížeč a přihlaste se k myapps.microsoft.com pomocí nového účtu globálního správce a dočasného hesla.
5. Změňte heslo globálního správce na něco, co si zapamatujete.

## <a name="download-and-install-azure-ad-connect"></a>Stažení a instalace služby Azure AD Connect
Teď je čas stáhnout a nainstalovat Azure AD Connect.  Po instalaci budeme probíhat přes expresní instalaci.  Udělejte toto:

1. Stažení [služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Přejděte k souboru **AzureADConnect.msi** a dvakrát na něj klikněte.
3. Na uvítací obrazovce zaškrtněte políčko, kterým odsouhlasíte licenční podmínky, a klikněte na **Pokračovat**.  
4. Na obrazovce expresního nastavení klikněte na **Použít expresní nastavení**.</br>  
![Vytvoření](media/tutorial-password-hash-sync/express1.png)</br>
5. Na obrazovce Připojit k Azure AD zadejte uživatelské jméno a heslo globálního správce pro Azure AD. Klikněte na **Další**.  
6. Na obrazovce Připojení ke službě AD DS zadejte uživatelské jméno a heslo pro účet správce podnikové sítě. Klikněte na **Další**.  
7. Na obrazovce Připraveno ke konfiguraci klikněte na **Nainstalovat**.
8. Až instalace skončí, klikněte na **Konec**.
9. Po dokončení instalace se před použitím Správce synchronizačních služeb nebo Editoru pravidel synchronizace odhlaste a znovu se přihlaste.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Ověřte, zda jsou uživatelé vytvořeni a dochází k synchronizaci.
Teď ověříme, že uživatelé, které jsme měli v našem místním adresáři, byli synchronizováni a teď existují v tenantovi Azure AD.  Uvědomte si, že to může trvat několik hodin.  Chcete-li ověřit, zda jsou uživatelé synchronizováni, postupujte takto.


1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Na levé straně vyberte **Azure Active Directory.**
3. V části **Spravovat** vyberte **Uživatele**.
4. Ověřte, zda se v našem tenantovi zobrazují noví uživatelé.</br>
![Synchronizace](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Otestujte přihlášení s jedním z našich uživatelů

1. Přejít na[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našem novém tenantovi.  Budete se muset přihlásit v následujícímuser@domain.onmicrosoft.comformátu: ( ). Použijte stejné heslo, které uživatel používá k místnímu přihlášení.</br>
   ![Ověřit](media/tutorial-password-hash-sync/verify1.png)</br>

Nyní jste úspěšně nastavili prostředí hybridní identity, které můžete použít k testování a seznámení s tím, co Azure nabízí.

## <a name="next-steps"></a>Další kroky


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)|
