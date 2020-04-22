---
title: Povolení ověřování služby Active Directory přes SMB pro soubory Azure
description: Zjistěte, jak povolit ověřování na základě identity přes SMB pro sdílené složky Azure prostřednictvím služby Active Directory. Virtuální počítače windows (VM) spojené s doménou pak můžou přistupovat ke sdíleným spodám azure pomocí přihlašovacích údajů služby AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: 44debc299054568769bfbe6cfc089cc528594274
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677078"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Povolení místního ověřování služby Active Directory Domain Services přes SMB pro sdílené složky Azure

[Soubory Azure](storage-files-introduction.md) podporují ověřování na základě identity přes blok zpráv serveru (SMB) prostřednictvím dvou typů doménových služeb: Azure Active Directory Domain Services (Azure AD DS) a místní služby Active Directory Domain Services (AD DS) (preview). Tento článek se zaměřuje na nově zavedenou (předběžnou) podporu využití služby Active Directory Domain Service pro ověřování sdílených složek Azure. Pokud máte zájem o povolení ověřování Azure AD DS (GA) pro sdílené složky Azure, podívejte se na [náš článek na toto téma](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Sdílené složky Azure podporují jenom ověřování proti jedné službě domény, buď službě Azure Active Directory Domain Service (Azure AD DS) nebo místní službě Active Directory Domain Services (AD DS). 
>
> Identity služby AD DS používané pro ověřování sdílení souborů Azure musí být synchronizovány do Azure AD. Synchronizace hodnot hash hesel je volitelná. 
> 
> Místní ověřování služby AD DS nepodporuje ověřování proti účtům počítačů vytvořeným ve službě AD DS. 
> 
> Místní ověřování služby AD DS lze podporovat pouze v jedné doménové struktuře služby AD, kde je účet úložiště registrován. Ke sdíleným souborům Azure můžete přistupovat jenom s přihlašovacími údaji služby AD DS z jedné doménové struktury ve výchozím nastavení. Pokud potřebujete získat přístup ke sdílené složce Azure z jiné doménové struktury, ujistěte se, že máte nakonfigurovaný správný vztah důvěryhodnosti doménové struktury, najdete podrobnosti v [tématu Nejčastější](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) dotazy.  
> 
> Ověřování služby AD DS pro přístup SMB a trvalosti přístupu a cl je podporováno pro sdílené složky Azure spravované službou Azure File Sync.
>
> Soubory Azure podporují ověřování protokolem Kerberos pomocí služby AD s šifrováním RC4-HMAC. Šifrování Protokolu AES Kerberos ještě není podporováno.

Když povolíte sdílené složky služby AD DS pro Azure prostřednictvím protokolu SMB, vaše počítače spojené se službou AD DS můžou připojit sdílené složky Azure pomocí vašich stávajících přihlašovacích údajů služby AD. Tuto funkci lze povolit s prostředím služby AD DS hostovaným v počítačích on-prem nebo hostovaným v Azure.

Identity používané pro přístup ke sdíleným složkám Azure musí být synchronizovány do Azure AD k vynucení oprávnění souboru na úrovni sdílení prostřednictvím modelu [řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/overview.md) [Seznamy DACL ve stylu systému Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) na souborech nebo adresářích přenesených z existujících souborových serverů budou zachovány a vynuceny. Tato funkce nabízí bezproblémovou integraci s podnikovým prostředím AD DS. Při nahrazení souborových serverů na předplacených souborech sdílenými složkami Azure mohou stávající uživatelé přistupovat ke sdíleným spodám souborů Azure ze svých aktuálních klientů pomocí jednotného přihlašování bez jakékoli změny přihlašovacích údajů, které se používají.  

> [!NOTE]
> Abychom vám pomohli nastavit ověřování Azure Files AD pro některé běžné případy použití, publikovali jsme [dvě videa](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) s podrobnými pokyny:
> - Nahrazení místních souborových serverů soubory Azure (včetně nastavení na privátní propojení pro soubory a ověřování služby AD)
> - Použití souborů Azure jako kontejneru profilu pro Virtuální plochu Windows (včetně nastavení ověřování a služby AD a konfigurace FsLogix)

## <a name="prerequisites"></a>Požadavky 

Než povolíte ověřování ve službě AD DS pro sdílené složky Azure, ujistěte se, že jste splnili následující požadavky: 

- Vyberte nebo vytvořte prostředí služby AD DS a [synchronizujte ho se službou Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Tuto funkci můžete povolit v novém nebo existujícím místním prostředí ad ds. Identity používané pro přístup musí být synchronizovány do Služby Azure AD. Klient Azure AD a sdílené složky, ke které přistupujete, musí být přidruženy ke stejnému předplatnému. 

    Chcete-li nastavit prostředí domény služby AD, přečtěte si [informace o přehledu služby Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Pokud jste nesynchronizovali ad do azure ad, postupujte podle pokynů v [Azure AD Connect a Azure AD Connect plán instalace stavu](../../active-directory/hybrid/how-to-connect-install-roadmap.md) pro konfiguraci a nastavení Azure AD Connect. 

- Připojení k místní počítač nebo virtuální počítač Azure k místnímu službu AD DS. 

    Chcete-li získat přístup ke sdílené složce pomocí přihlašovacích údajů služby AD z počítače nebo virtuálního počítače, musí být vaše zařízení připojeno k doméně služby AD DS. Informace o připojení k doméně naleznete v části [Připojení počítače k doméně](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Vyberte nebo vytvořte účet úložiště Azure v [podporované oblasti](#regional-availability). 

    Ujistěte se, že účet úložiště obsahující sdílené složky není ještě nakonfigurovaný pro ověřování Azure AD DS. Pokud azure files Azure AD DS ověřování je povolená na účtu úložiště, je třeba zakázat před změnou používat místní Služby AD DS. To znamená, že existující aklů nakonfigurovaných v prostředí Azure AD DS bude nutné překonfigurovat pro správné vynucení oprávnění.
    
    Informace o vytváření nové sdílené složky najdete [v tématu Vytvoření sdílené složky v Azure Files](storage-how-to-create-file-share.md).
    
    Pro optimální výkon doporučujeme nasadit účet úložiště ve stejné oblasti jako virtuální hod, ze kterého plánujete přístup ke sdílené položce. 

- Ověřte připojení připojení připojením sdílených složek Azure pomocí klíče účtu úložiště. 

    Chcete-li ověřit, zda je zařízení a sdílená složka správně nakonfigurováno, zkuste sdílenou složku nastavovat pomocí klíče účtu úložiště. Další informace najdete [v tématu Použití sdílené složky Azure s Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regionální dostupnost

Azure Files authentication with AD DS (preview) je dostupné ve [všech veřejných oblastech a oblastech Azure Gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="workflow-overview"></a>Přehled pracovního postupu

Než povolíte ověřování služby AD DS přes SMB pro sdílené složky Azure, doporučujeme přečíst a dokončit část [požadavků.](#prerequisites) Požadavky ověřují, že vaše prostředí Služby AD, Azure AD a Azure Storage jsou správně nakonfigurované. 

Pokud plánujete povolit všechny konfigurace sítě ve sdílené složce, doporučujeme nejprve vyhodnotit [pozornost sítě](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) a před povolením ověřování služby AD DS nejprve dokončit související konfiguraci.

Dále postupujte podle následujících kroků a nastavte soubory Azure pro ověřování ve službě AD: 

1. Povolte ověřování Azure Files AD DS na vašem účtu úložiště. 

2. Přiřaďte přístupová oprávnění pro sdílenou složku identitě Azure AD (uživateli, skupině nebo instančnímu objektu), která je synchronizovaná s cílovou identitou služby AD. 

3. Konfigurace seznamů ACL přes SMB pro adresáře a soubory. 
 
4. Připojení sdílené složky Azure do virtuálního počítače připojovaného k vašemu systému AD DS. 

5. Aktualizujte heslo identity účtu úložiště ve službě AD DS.

Následující diagram znázorňuje pracovní postup od konce pro povolení ověřování Azure AD přes SMB pro sdílené složky Azure. 

![Diagram pracovního postupu ad souborů](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Ověřování služby AD DS přes sdílené složky SMB pro sdílené složky Azure je podporováno jenom na počítačích nebo virtuálních počítačích spuštěných ve verzích operačního systému novějších než Windows 7 nebo Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. Povolení ověřování služby AD pro váš účet 

Chcete-li povolit ověřování služby AD DS přes SMB pro sdílené složky Azure, musíte nejprve zaregistrovat účet úložiště ve službě AD DS a potom nastavit požadované vlastnosti domény v účtu úložiště. Pokud je funkce povolena v účtu úložiště, platí pro všechny nové a existující sdílené složky v účtu. Slouží `join-AzStorageAccountForAuth` k povolení funkce. Podrobný popis pracovního postupu od konce najdete ve skriptu v této části. 

> [!IMPORTANT]
> Rutina `Join-AzStorageAccountForAuth` provede změny prostředí AD. Přečtěte si následující vysvětlení, abyste lépe pochopili, co dělá, abyste zajistili, že máte správná oprávnění ke spuštění příkazu a že použité změny jsou v souladu se zásadami dodržování předpisů a zabezpečení. 

Rutina `Join-AzStorageAccountForAuth` provede ekvivalent připojení domény offline jménem uvedeného účtu úložiště. Skript používá rutinu k vytvoření účtu v doméně služby AD, buď [účtu počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí) nebo [přihlašovacího účtu služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Pokud se rozhodnete provést ručně, měli byste vybrat účet, který nejlépe vyhovuje vašemu prostředí.

Účet služby AD DS vytvořený rutinou představuje účet úložiště v doméně služby AD. Pokud je účet služby AD DS vytvořen v rámci organizační jednotky (OU), která vynucuje vypršení platnosti hesla, je nutné heslo aktualizovat před dosažením maximálního stáří hesla. Pokud se nepodaří aktualizovat heslo účtu, bude mít za následek selhání ověřování při přístupu ke sdíleným položkám souborů Azure. Informace o aktualizaci hesla naleznete v [tématu Aktualizace hesla účtu služby AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Můžete použít následující skript k provedení registrace a povolení funkce nebo alternativně můžete ručně provádět operace, které by skript. Tyto operace jsou popsány v části následující skript. Nemusíte dělat obojí.

### <a name="11-script-prerequisites"></a>1.1 Požadavky skriptu
- [Stažení a rozepnout modul AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Nainstalujte a spusťte modul v zařízení, které je doménou připojenou k místnímu systému AD DS s pověřeními služby AD DS, která mají oprávnění k vytvoření přihlašovacího účtu služby nebo účtu počítače v cílové službě AD.
-  Spusťte skript pomocí místního pověření služby AD DS, které se synchronizuje s vaším Azure AD. Místní pověření služby AD DS musí mít buď vlastníka účtu úložiště, nebo oprávnění role RBAC přispěvatele.
- Ujistěte se, že váš účet úložiště je v [podporované oblasti](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1.2 Doména se připojí k účtu úložiště
Nezapomeňte nahradit zástupné hodnoty vlastními v níže uvedených parametrech, než je provedete v prostředí PowerShell.
> [!IMPORTANT]
> Rutina připojení k doméně vytvoří účet služby AD představující účet úložiště (sdílenou složku) ve službě AD. Můžete se zaregistrovat jako účet počítače nebo přihlašovací účet služby, podrobnosti najdete v [častých dotazech.](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) U účtů počítačů je ve společnosti AD nastavenvýchozí věk vypršení platnosti hesla po 30 dnech. Podobně přihlašovací účet služby může mít výchozí stáří vypršení platnosti hesla nastavené v doméně služby AD nebo organizační jednotce (OU).
> U obou typů účtů důrazně doporučujeme zkontrolovat, jaký je věk vypršení platnosti hesla nakonfigurovaný ve vašem prostředí služby AD, a před dosažením maximálního stáří hesla plánujete [aktualizovat heslo identity účtu úložiště ve službách AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) účtu služby AD. Pokud se nepodaří aktualizovat heslo účtu služby AD, dojde k selhání ověřování při přístupu ke sdíleným položkám azure. Můžete zvážit [vytvoření nové organizační jednotky služby AD (OU) ve službě AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) a odpovídajícím způsobem zakázat zásady vypršení platnosti hesla u [účtů počítačů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) nebo přihlašovacích účtů služeb. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

Následující popis shrnuje všechny `Join-AzStorageAccountForAuth` akce provedené při spuštění rutiny. Pokud nechcete příkaz používat, můžete tyto kroky provést ručně:

> [!NOTE]
> Pokud jste již výše `Join-AzStorageAccountForAuth` uvedený skript úspěšně provedli, přejděte k další části "1.3 Potvrďte, že je funkce povolena". Není nutné provádět níže uvedené operace znovu.

#### <a name="a-checking-environment"></a>a. Kontrola prostředí

Nejprve skript zkontroluje vaše prostředí. Konkrétně zkontroluje, zda je [nainstalováno prostředí Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) a zda je prostředí spouštěno s oprávněními správce. Pak zkontroluje, zda je nainstalován [modul Az.Storage 1.11.1-preview,](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) a nainstaluje jej, pokud tomu tak není. Pokud tyto kontroly projdou, zkontroluje váš ad DS, aby zjistil, zda existuje účet [počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí) nebo [přihlašovací účet služby,](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) který již byl vytvořen s SPN / UPN jako "cifs/ vaše úložiště-účet-name-here.file.core.windows.net". Pokud účet neexistuje, vytvoří jej tak, jak je popsáno v části b níže.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Ruční vytvoření identity představující účet úložiště ve vaší ad

Chcete-li tento účet vytvořit ručně, vytvořte nový klíč `New-AzStorageAccountKey -KeyName kerb1`protokolu Kerberos pro váš účet úložiště pomocí aplikace . Potom použijte tento klíč Kerberos jako heslo pro váš účet. Tento klíč se používá pouze během nastavení a nelze jej použít pro žádné operace ovládacího prvku nebo roviny dat proti účtu úložiště.

Jakmile budete mít tento klíč, vytvořte buď službu nebo účet počítače pod ou. Použijte následující specifikaci: HLAVNÍ název služby: "cifs/your-storage-account-name-here.file.core.windows.net" Heslo: Klíč Kerberos pro váš účet úložiště.

Pokud vaše hlavní cena vynucuje vypršení platnosti hesla, je nutné aktualizovat heslo před maximální stáří hesla, aby se zabránilo selhání ověřování při přístupu ke sdíleným položkám souborů Azure. Podrobnosti najdete [v tématu Aktualizace hesla identity účtu úložiště ve službě AD DS.](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Zachovat SID nově vytvořeného účtu, budete potřebovat pro další krok. Identita, kterou jste vytvořili, které představují účet úložiště není nutné synchronizovat do Služby Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Povolení funkce v účtu úložiště

Skript by pak povolit funkci na vašem účtu úložiště. Chcete-li provést toto nastavení ručně, zadejte některé podrobnosti konfigurace vlastností domény v následujícím příkazu a spusťte je. Sid účtu úložiště požadovaný v následujícím příkazu je SID identity, kterou jste vytvořili ve službě AD DS v [předchozí části](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 Zkontrolujte, zda je tato funkce povolena

Můžete zkontrolovat, zda je tato funkce povolena ve vašem účtu úložiště, můžete použít následující skript:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Nyní jste tuto funkci úspěšně povolili v účtu úložiště. Nyní, když je funkce povolena, je třeba provést další kroky k použití této funkce.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nyní jste úspěšně povolili ověřování služby AD DS přes SMB a přiřadili vlastní roli, která poskytuje přístup ke sdílené složce Azure s identitou služby AD DS. Chcete-li udělit přístup ke sdílené složce dalším uživatelům, postupujte podle pokynů v části [Přiřadit přístupová oprávnění](#2-assign-access-permissions-to-an-identity) k použití identity a [konfigurace oprávnění ntfs přes](#3-configure-ntfs-permissions-over-smb) oddíly SMB.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Aktualizace hesla identity účtu úložiště ve službách AD DS

Pokud jste zaregistrovali identitu/účet služby AD DS představující váš účet úložiště pod ou, která vynucuje dobu vypršení platnosti hesla, musíte heslo otočit před maximálním stářím hesla. Pokud se nepodaří aktualizovat heslo účtu služby AD DS, bude mít za následek selhání ověřování pro přístup ke sdíleným položkám Azure.  

Chcete-li aktivovat střídání hesla, můžete spustit `Update-AzStorageAccountADObjectPassword` příkaz z modulu AzFilesHybrid. Rutina provádí akce podobné střídání klíčů účtu úložiště. Získá druhý klíč Protokolu Kerberos účtu úložiště a použije jej k aktualizaci hesla registrovaného účtu ve službě AD DS. Poté regeneruje cílový klíč Protokolu Kerberos účtu úložiště a aktualizuje heslo registrovaného účtu ve službě AD DS. Tuto rutinu je nutné spustit v místním prostředí spojeného s doménou služby AD DS.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Další kroky

Další informace o souborech Azure a o tom, jak používat službu AD přes SMB, najdete v těchto prostředcích:

- [Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
