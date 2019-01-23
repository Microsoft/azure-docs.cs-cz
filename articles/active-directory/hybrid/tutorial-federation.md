---
title: 'Kurz: Vytvoření federace pro jedno prostředí doménové struktury AD do Azure | Dokumentace Microsoftu'
description: Ukazuje, jak nastavit hybridní prostředí identit pomocí federačních.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9e13b6aca7fd6302fc521e549b0f5a587ead9ed5
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472462"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>Kurz: Federace prostředí jedné doménové struktury služby AD do cloudu

![Vytvořit](media/tutorial-federation/diagram.png)

V následujícím kurzu vás provede procesem vytvoření hybridního prostředí identit pomocí federačních.  Toto prostředí je pak možné pro účely testování nebo pro získání více do hloubky s tím, jak funguje hybridní identita.

## <a name="prerequisites"></a>Požadavky
Toto jsou nezbytné součásti potřebné k dokončení tohoto kurzu
- Počítač s [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) nainstalované.  Doporučuje se to udělat buď [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) nebo [Windows serveru 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) počítače.
- [Předplatné Azure](https://azure.microsoft.com/free)
- - [Externí síťový adaptér](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) umožňující virtuálního počítače ke komunikaci s Internetem.
- Kopii Windows serveru 2016
- A [vlastní doménu](../../active-directory/fundamentals/add-custom-domain.md) , který se dá ověřit

> [!NOTE]
> V tomto kurzu používá skripty prostředí PowerShell, aby mohl vytvořit výukový program prostředí v nejrychlejší době.  Každá skriptů používá proměnné, které jsou deklarovány na začátku skripty.  Můžou a měli změnit proměnné, aby se zohlednilo vaše prostředí.
>
>Skripty používané vytvořit obecné prostředí služby Active Directory před instalací Azure AD Connect.  Jsou relevantní pro všechny z kurzů.
>
> Kopie Powershellových skriptů, které se používají v tomto kurzu jsou k dispozici na Githubu [tady](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
První věcí, kterou potřebujeme udělat, aby naše prostředí hybridní identity zprovoznění a spuštění je k vytvoření virtuálního počítače, který se použije jako naše místní server služby Active Directory.  

>[!NOTE]
>Pokud jste nikdy spuštění skriptu v prostředí PowerShell na hostitelském počítači musíte spustit `Set-ExecutionPolicy remotesigned` a Řekněme, že Ano v prostředí PowerShell, před spuštěním skriptů.

Udělejte toto:

1. Otevřete prostředí PowerShell ISE jako správce.
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
Pokud chcete dokončit vytváření virtuálního počítače, je potřeba dokončit instalace operačního systému.

1. Správce technologie Hyper-V, dvakrát klikněte na virtuální počítač
2. Klikněte na tlačítko Start.
3.  Jste vyzváni k "Stisknutím libovolné klávesy pro spuštění z CD nebo DVD". Pokračujte a udělat.
4. V nabídce start systému Windows Server do obrazovky vyberte svůj jazyk a klikněte na tlačítko **Další**.
5. Klikněte na tlačítko **nainstalovat**.
6. Zadejte klíč licence a klikněte na tlačítko **Další**.
7. Zkontrolujte ** Přijímám licenční podmínky a klikněte na tlačítko **Další**.
8. Vyberte **vlastní:  Instalovat pouze Windows (rozšířené)**
9. Klikněte na **Další**
10. Po dokončení instalace restartujte virtuální počítač, přihlášení a spuštění aktualizací Windows k zajištění, že je virtuální počítač nejnovější.  Nainstalujte nejnovější aktualizace.

## <a name="install-active-directory-pre-requisites"></a>Instalace požadovaných součástí služby Active Directory
Když teď máme virtuálního počítače, musíme udělat pár věcí před instalací služby Active Directory.  To znamená musíme přejmenujte virtuální počítač, nastavte statickou IP adresu a informace o DNS a nainstalujte nástroje pro vzdálenou správu serveru.   Udělejte toto:

1. Otevřete prostředí PowerShell ISE jako správce.
2. Spustit `Set-ExecutionPolicy remotesigned` a říkají, Ano všem [A].  Stiskněte klávesu Enter.
3. Spusťte následující skript.

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

## <a name="create-a-windows-server-ad-environment"></a>Vytvoření prostředí s AD na Windows serveru
Teď, když jsme vytvořili virtuální počítač a byla přejmenována a má statickou IP adresu, budeme pokračovat a nainstalujte a nakonfigurujte Active Directory Domain Services.  Udělejte toto:

1. Otevřete prostředí PowerShell ISE jako správce.
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

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Vytvořte uživatele systému Windows Server AD
Teď, když jsme naše prostředí služby Active Directory, musíme zkušební účet.  Tento účet se vytvoří v našem místním prostředí služby AD a pak synchronizovány do Azure AD.  Udělejte toto:

1. Otevřete prostředí PowerShell ISE jako správce.
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

## <a name="create-a-certificate-for-ad-fs"></a>Vytvořit certifikát pro službu AD FS
Teď vytvoříme certifikát SSL, který se použije služba AD FS.  To bude možné certifikát podepsaný svým držitelem a je určena pouze pro účely testování.  Microsoft nedoporučuje používat certifikát podepsaný svým držitelem v produkčním prostředí. Udělejte toto:

1. Otevřete prostředí PowerShell ISE jako správce.
2. Spusťte následující skript.

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
```

## <a name="create-an-azure-ad-tenant"></a>Vytvoření tenanta Azure AD
Nyní potřebujeme vytvořit tenanta Azure AD, takže jsme se mohou synchronizovat naši uživatelé do cloudu.  K vytvoření nové služby Azure AD tenanta, postupujte takto.

1. Přejděte [webu Azure portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Vyberte **ikonu plus (+)** a vyhledejte **Azure Active Directory**.
3. Vyberte **Azure Active Directory** ve výsledcích hledání.
4. Vyberte **Vytvořit**.</br>
![Vytvoření](media/tutorial-password-hash-sync/create1.png)</br>
5. Zadejte **název organizace** spolu s **počáteční název domény**. Potom vyberte **Vytvořit**. Vytvoří se váš adresář.
6. Jakmile to dokončí, klikněte na tlačítko **tady** odkaz, ke správě adresáře.

## <a name="create-a-global-administrator-in-azure-ad"></a>Vytvoření globálního správce ve službě Azure AD
Když teď máme tenanta služby Azure AD, vytvoříme účet globálního správce.  Tento účet slouží k vytvoření účtu Azure AD Connector během instalace služby Azure AD Connect.  Účet Azure AD Connector se používá při zápisu informací do služby Azure AD.   Chcete-li vytvořit globální správce účtu takto:

1.  V části **Spravovat** vyberte **Uživatele**.</br>
![Vytvoření](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Vyberte **všichni uživatelé** a pak vyberte **+ nový uživatel**.
3.  Zadejte název a uživatelské jméno pro tohoto uživatele. To bude globální správce pro tenanta. Budete také chtít změnit **role adresáře** k **globálního správce.** Můžete také zobrazit dočasné heslo. Až to budete mít, vyberte **Vytvořit**.</br>
![Vytvoření](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Po dokončení, otevřete nový webový prohlížeč a přihlaste se do myapps.microsoft.com pomocí nového účtu globálního správce a dočasné heslo.
5. Změňte heslo pro globálního správce a něco, co si budou pamatovat.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Přidání vlastního názvu domény do adresáře
Když teď máme tenanta a globální správce, potřebujeme přidat naše vlastní doménu, můžete tuto Azure ověří, že.  Udělejte toto:

1. Zpátky [webu Azure portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ukončete **všichni uživatelé** okno.
2. Nalevo vyberte **Názvy vlastních domén**.
3. Vyberte **Přidat vlastní doménu**.</br>
![Federace](media/tutorial-federation/custom1.png)</br>
4. Na **vlastní názvy domén**, zadejte do pole název vaší vlastní domény a klikněte na tlačítko **přidat doménu**.
5. Na obrazovce název vlastní domény se @username TXT nebo MX informacemi.  Tyto informace musí být přidaný do informace DNS doménového registrátora v rámci vaší domény.  Proto budete muset přejít do vašeho registrátora domény, zadejte buď TXT nebo MX informace v nastavení DNS pro vaši doménu.  To vám umožní Azure, abychom ověřili vaši doménu.  To může trvat až 24 hodin pro Azure a ověřte ji.  Další informace najdete v tématu [přidat vlastní doménu](../../active-directory/fundamentals/add-custom-domain.md) dokumentaci.</br>
![Federace](media/tutorial-federation/custom2.png)</br>
6. Aby bylo zajištěno, že je ověřený, klikněte na tlačítko ověřit.</br>
![Federace](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Stáhněte a nainstalujte Azure AD Connect
Nyní je čas ke stažení a instalaci Azure AD Connect.  Po jeho instalaci provedeme prostřednictvím Expresní instalace.  Udělejte toto:

1. Stáhněte si [služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Přejděte k souboru **AzureADConnect.msi** a dvakrát na něj klikněte.
3. Na uvítací obrazovce zaškrtněte políčko, kterým odsouhlasíte licenční podmínky, a klikněte na **Pokračovat**.  
4. Na obrazovce expresního nastavení klikněte na tlačítko **vlastní**.  
5. Na obrazovce instalace požadované součásti. Klikněte na **Nainstalovat**.  
6. Na obrazovce přihlášení uživatele, vyberte **federace se službou AD FS** a klikněte na tlačítko **Další**.
![Federace](media/tutorial-federation/fed1.png)

1. Na obrazovce připojení ke službě Azure AD, zadejte uživatelské jméno a heslo globálního správce, který jsme vytvořili výše a klikněte na tlačítko **Další**.
2. Na obrazovce adresáře připojení, klikněte na tlačítko **přidat adresář**.  Potom vyberte **vytvořit nový účet AD** zadejte contoso\Administrator uživatelské jméno a heslo a klikněte na tlačítko **OK**.
3. Klikněte na **Další**.
4. Na obrazovce konfigurace přihlášení Azure AD, vyberte **pokračovat bez odpovídajícího všechny přípony UPN k ověřených domén** a klikněte na tlačítko **Další.**
5. Na doménu a organizační jednotku filtrování obrazovka, klikněte na tlačítko **Další**.
6. Na Jednoznačná identifikace uživatelů obrazovky, klikněte na tlačítko **Další**.
7. Filtr uživatelů a obrazovky zařízení, klikněte na tlačítko **Další**.
8. Na obrazovce volitelných funkcí, klikněte na tlačítko **Další**.
9. Na stránce přihlašovací údaje správce domény, zadejte contoso\Administrator uživatelské jméno a heslo a klikněte na tlačítko **Další.**
10. Na obrazovce pro farmu služby AD FS, ujistěte se, že **konfigurovat novou farmu služby AD FS** zaškrtnuto.
11. Vyberte **použít certifikát nainstalovaný na federačních serverech** a klikněte na tlačítko **Procházet**.
12. Do vyhledávacího pole zadejte řadič domény DC1 a vyberte ji, když je nalezena.  Klikněte na tlačítko **OK**.
13. Z **soubor certifikátu** rozevíracího seznamu, vyberte **adfs.contoso.com** certifikát, který jsme vytvořili výše.  Klikněte na **Další**.
![Federace](media/tutorial-federation/fed2.png)

1. Na obrazovce server služby AD FS, klikněte na tlačítko **Procházet** a do vyhledávacího pole zadejte řadič domény DC1 a vyberte ji, když je nalezena.  Klikněte na tlačítko **OK**.  Klikněte na **Další**.
![Federace](media/tutorial-federation/fed3.png)

1. V okně webové aplikace Proxy servery klikněte na tlačítko **Další**.
2. Na obrazovce účet služby AD FS, zadejte contoso\Administrator uživatelské jméno a heslo a klikněte na tlačítko **Další.**
3. Na obrazovce Azure AD Domain ověřené vlastní doméně vyberte z rozevíracího seznamu a klikněte na tlačítko **Další**.
4. Na obrazovce Připraveno ke konfiguraci klikněte na **Instalovat**.
5. Až instalace skončí, klikněte na **Konec**.
6. Po dokončení instalace se odhlaste a znovu se přihlaste teprve pak použijte Synchronization Service Manager nebo Synchronization Rule Editor.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Ověření uživatele se vytvářejí a dochází k synchronizaci
Nyní jsme ověří, že uživatelé, které jsme měli v našem místním adresářem byly synchronizované a nyní existují v si tenanta Azure AD.  Mějte na paměti, že to může trvat několik hodin.  Ověření uživatelé používají synchronizaci postupujte takto.


1. Přejděte [webu Azure portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Na levé straně vyberte **Azure Active Directory**
3. V části **Spravovat** vyberte **Uživatele**.
4. Ověřte, jestli se nové uživatele v naší tenanta ![synchronizace](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Test přihlášení pomocí jednoho z našich uživatelů

1.  Přejděte na [https://myapps.microsoft.com](httpss://myapps.microsoft.com)
2. Přihlaste se pomocí uživatelského účtu, který byl vytvořen v našich nového tenanta.  Budete muset přihlásit pomocí následujícího formátu: (user@domain.onmicrosoft.com). Použijte stejné heslo, které uživatel použije k přihlášení místní.
![Ověření](media/tutorial-password-hash-sync/verify1.png)

Máte teď úspěšně nastavení hybridního prostředí identit, můžete použít k testování a seznamte se s co Azure může nabídnout.

## <a name="next-steps"></a>Další kroky

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)

