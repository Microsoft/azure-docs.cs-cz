---
title: Povolení ověřování služby Active Directory přes SMB pro soubory Azure
description: Zjistěte, jak povolit ověřování na základě identity přes SMB pro sdílené složky Azure prostřednictvím služby Active Directory. Virtuální počítače windows (VM) spojené s doménou pak můžou přistupovat ke sdíleným spodám azure pomocí přihlašovacích údajů služby AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: 0bf8960f1e97de45d5369f69c698311d0b4e3dbb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584511"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Povolení ověřování služby Active Directory přes SMB pro sdílené složky Azure

[Soubory Azure](storage-files-introduction.md) podporují ověřování na základě identity přes blok zpráv serveru (SMB) prostřednictvím dvou typů doménových služeb: Azure Active Directory Domain Services (Azure AD DS) (GA) a Active Directory (AD) (preview). Tento článek se zaměřuje na nově zavedenou (předběžnou) podporu využití služby Active Directory Domain Service pro ověřování sdílených složek Azure. Pokud máte zájem o povolení ověřování Azure AD DS (GA) pro sdílené složky Azure, podívejte se na [náš článek na toto téma](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Sdílené složky Azure podporují jenom ověřování proti jedné službě domény, buď službě Azure Active Directory Domain Service (Azure AD DS) nebo službě Active Directory (AD). 
>
> Identity služby AD používané pro ověřování sdílení souborů Azure se musí synchronizovat do Služby Azure AD. Synchronizace hodnot hash hesel je volitelná. 
> 
> Ověřování služby AD nepodporuje ověřování proti účtům počítačů vytvořeným ve službě AD. 
> 
> Ověřování služby AD lze podporovat pouze v jedné doménové struktuře služby AD, kde je účet úložiště registrován. Ke sdíleným souborům Azure můžete přistupovat jenom s přihlašovacími údaji služby AD z jedné doménové struktury služby AD ve výchozím nastavení. Pokud potřebujete získat přístup ke sdílené složce Azure z jiné doménové struktury, ujistěte se, že máte nakonfigurovaný správný vztah důvěryhodnosti doménové struktury, najdete podrobnosti v [tématu Nejčastější](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) dotazy.  
> 
> Ověřování ve službě AD pro přístup SMB a trvalosti přístupu a cl je podporováno pro sdílené složky Azure spravované službou Azure File Sync.
>
> Soubory Azure podporují ověřování protokolem Kerberos pomocí služby AD s šifrováním RC4-HMAC. Šifrování Protokolu AES Kerberos ještě není podporováno.

Když povolíte sdílené složky Služby AD pro Azure přes SMB, vaše počítače s připojením domény Služby AD můžou připojit sdílené složky Azure pomocí vašich stávajících přihlašovacích údajů služby AD. Tuto funkci lze povolit s prostředím služby AD hostovaným v počítačích on-prem nebo hostovaným v Azure.

Identity služby AD používané pro přístup ke sdíleným složkám Azure musí být synchronizovány do Azure AD, aby bylo možné vynutit oprávnění k souborům na úrovni sdílení prostřednictvím standardního modelu [řízení přístupu (RBAC) založeného na rolích.](../../role-based-access-control/overview.md) [Seznamy DACL ve stylu systému Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) na souborech nebo adresářích přenesených z existujících souborových serverů budou zachovány a vynuceny. Tato funkce nabízí bezproblémovou integraci s podnikovou infrastrukturou domény služby AD. Při nahrazení souborových serverů na předplacených souborech sdílenými složkami Azure mohou stávající uživatelé přistupovat ke sdíleným spodám souborů Azure ze svých aktuálních klientů pomocí jednotného přihlašování bez jakékoli změny přihlašovacích údajů, které se používají.  
 
## <a name="prerequisites"></a>Požadavky 

Než povolíte ověřování služby AD pro sdílené složky Azure, ujistěte se, že jste splnili následující požadavky: 

- Vyberte nebo vytvořte prostředí služby AD a synchronizujte ho s Azure AD. 

    Tuto funkci můžete povolit v novém nebo existujícím prostředí ad. Identity používané pro přístup musí být synchronizovány do Služby Azure AD. Klient Azure AD a sdílené složky, ke které přistupujete, musí být přidruženy ke stejnému předplatnému. 

    Chcete-li nastavit prostředí domény služby AD, přečtěte si [informace o přehledu služby Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Pokud jste nesynchronizovali ad do azure ad, postupujte podle pokynů v [co je hybridní identita s Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) chcete-li určit upřednostňovanou metodu ověřování a možnost nastavení Azure AD Connect. 

- Připojení k místní počítač nebo virtuální počítač Azure do služby AD (označované také jako Služba AD DS). 

    Chcete-li získat přístup ke sdílené složce pomocí přihlašovacích údajů služby AD z počítače nebo virtuálního počítače, musí být vaše zařízení připojeno k ad. Informace o připojení k doméně služby AD naleznete v části [Připojení počítače k doméně](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Vyberte nebo vytvořte účet úložiště Azure v [podporované oblasti](#regional-availability). 

    Ujistěte se, že účet úložiště obsahující sdílené složky není ještě nakonfigurovaný pro ověřování Azure AD DS. Pokud azure files Azure AD DS Authentication je povolená na účtu úložiště, je třeba zakázat před změnou používat službu AD. To znamená, že existující aklů nakonfigurovaných v prostředí Azure AD DS bude nutné překonfigurovat pro správné vynucení oprávnění.
    
    Informace o vytváření nové sdílené složky najdete [v tématu Vytvoření sdílené složky v Azure Files](storage-how-to-create-file-share.md).
    
    Pro optimální výkon doporučujeme nasadit účet úložiště ve stejné oblasti jako virtuální hod, ze kterého plánujete přístup ke sdílené položce. 

- Ověřte připojení připojení připojením sdílených složek Azure pomocí klíče účtu úložiště. 

    Chcete-li ověřit, zda je zařízení a sdílená složka správně nakonfigurováno, zkuste sdílenou složku nastavovat pomocí klíče účtu úložiště. Další informace najdete [v tématu Použití sdílené složky Azure s Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regionální dostupnost

Ověřování Azure Files AD (preview) je dostupné ve [všech oblastech ve veřejném cloudu](https://azure.microsoft.com/global-infrastructure/regions/).

## <a name="workflow-overview"></a>Přehled pracovního postupu

Než povolíte ověřování ve službě AD přes SMB pro sdílené složky Azure, doporučujeme projít [si požadavky](#prerequisites) a ujistěte se, že jste dokončili všechny kroky. Požadavky ověřují, že vaše prostředí Služby AD, Azure AD a Azure Storage jsou správně nakonfigurované. 

Dále udělte přístup k prostředkům Azure Files pomocí přihlašovacích údajů služby AD: 

- Povolte ověřování Azure Files AD na vašem účtu úložiště.  

- Přiřaďte přístupová oprávnění pro sdílenou složku identitě Azure AD (uživateli, skupině nebo instančnímu objektu), která je synchronizovaná s cílovou identitou služby AD. 

- Konfigurace seznamů ACL přes SMB pro adresáře a soubory. 

- Připojení sdílené složky Azure z domény služby AD připojované k virtuálnímu počítači. 

Následující diagram znázorňuje pracovní postup od konce pro povolení ověřování Azure AD přes SMB pro sdílené složky Azure. 

![Diagram pracovního postupu ad souborů](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Ověřování ve službě AD přes sdílené složky Azure pro sdílené složky Azure se podporuje jenom na počítačích nebo virtuálních počítačích spuštěných ve verzích operačního systému novějších než Windows 7 nebo Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Povolení ověřování ve službě AD pro váš účet 

Chcete-li povolit ověřování služby AD přes SMB pro sdílené složky Azure, musíte nejprve zaregistrovat účet úložiště ve službě AD a potom nastavit požadované vlastnosti domény v účtu úložiště. Pokud je funkce povolena v účtu úložiště, platí pro všechny nové a existující sdílené složky v účtu. Slouží `join-AzStorageAccountForAuth` k povolení funkce. Podrobný popis pracovního postupu od konce najdete v následující části. 

> [!IMPORTANT]
> Rutina `Join-AzStorageAccountForAuth` provede změny prostředí AD. Přečtěte si následující vysvětlení, abyste lépe pochopili, co dělá, abyste zajistili, že máte správná oprávnění ke spuštění příkazu a že použité změny jsou v souladu se zásadami dodržování předpisů a zabezpečení. 

Rutina `Join-AzStorageAccountForAuth` provede ekvivalent připojení domény offline jménem uvedeného účtu úložiště. Vytvoří účet ve vaší doméně služby AD, buď [účet počítače,](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) nebo [přihlašovací účet služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Vytvořený účet služby AD představuje účet úložiště v doméně služby AD. Pokud je účet služby AD vytvořen v organizační jednotce služby AD , která vynucuje vypršení platnosti hesla, je nutné heslo aktualizovat před dosažením maximálního stáří hesla. Pokud se nepodaří aktualizovat heslo účtu služby AD, dojde k selhání ověřování při přístupu ke sdíleným položkám azure. Informace o aktualizaci hesla naleznete v [tématu Aktualizace hesla účtu služby AD](#update-ad-account-password).

Můžete použít následující skript k provedení registrace a povolení funkce nebo alternativně můžete ručně provádět operace, které by skript. Tyto operace jsou popsány v části následující skript. Nemusíte dělat obojí.

### <a name="1-check-prerequisites"></a>1. Kontrola předpokladů
- [Stažení a rozepnout modul AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Nainstalujte a spusťte modul v zařízení, které je doménou připojenou ke službě AD s pověřeními služby AD, která mají oprávnění k vytvoření přihlašovacího účtu služby nebo účtu počítače v cílové službě AD.
-  Spusťte skript pomocí pověření služby AD, které se synchronizuje s vaším Azure AD. Pověření služby AD musí mít vlastníka účtu úložiště nebo oprávnění role RBAC přispěvatele.
- Ujistěte se, že váš účet úložiště je v [podporované oblasti](#regional-availability).

### <a name="2-domain-join-your-storage-account"></a>2. Doména připojit k účtu úložiště
Nezapomeňte nahradit zástupné hodnoty vlastními v níže uvedených parametrech, než je provedete v prostředí PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

Následující popis shrnuje všechny `Join-AzStorageAccountForAuth` akce provedené při spuštění rutiny. Pokud nechcete příkaz používat, můžete tyto kroky provést ručně:

> [!NOTE]
> Pokud jste již úspěšně `Join-AzStorageAccountForAuth` provedli výše uvedený skript, přejděte k další části "3. Zkontrolujte, zda je tato funkce povolena". Není nutné provádět níže uvedené operace znovu.

#### <a name="a-checking-environment"></a>a. Kontrola prostředí

Nejprve zkontroluje vaše prostředí. Konkrétně zkontroluje, zda je [nainstalováno prostředí Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) a zda je prostředí spouštěno s oprávněními správce. Pak zkontroluje, zda je nainstalován [modul Az.Storage 1.11.1-preview,](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) a nainstaluje jej, pokud tomu tak není. Pokud tyto kontroly projdou, zkontroluje vaše ad, aby zjistil, zda existuje účet [počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí) nebo [přihlašovací účet služby,](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) který již byl vytvořen s spn / UPN jako "cifs/ vaše úložiště-účet-name-here.file.core.windows.net". Pokud účet neexistuje, vytvoří jej tak, jak je popsáno v části b níže.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Ruční vytvoření identity představující účet úložiště ve vaší ad

Chcete-li tento účet vytvořit ručně, vytvořte nový klíč `New-AzStorageAccountKey -KeyName kerb1`kerberos pro váš účet úložiště pomocí aplikace . Potom použijte tento klíč kerberos jako heslo pro váš účet. Tento klíč se používá pouze během nastavení a nelze jej použít pro žádné operace ovládacího prvku nebo roviny dat proti účtu úložiště.

Jakmile budete mít tento klíč, vytvořte buď službu nebo účet počítače pod ou. Použijte následující specifikaci: HLAVNÍ název služby: "cifs/your-storage-account-name-here.file.core.windows.net" Heslo: Klíč Kerberos pro váš účet úložiště.

Pokud vaše hlavní cena vynucuje vypršení platnosti hesla, je nutné aktualizovat heslo před maximální stáří hesla, aby se zabránilo selhání ověřování při přístupu ke sdíleným položkám souborů Azure. Podrobnosti najdete [v tématu Aktualizace hesla účtu služby AD.](#update-ad-account-password)

Zachovat SID nově vytvořeného účtu, budete potřebovat pro další krok. Identita služby AD, kterou jste právě vytvořili a která představuje účet úložiště, nemusí být synchronizována do služby Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Povolení funkce v účtu úložiště

Skript by pak povolit funkci na vašem účtu úložiště. Chcete-li provést toto nastavení ručně, zadejte některé podrobnosti konfigurace vlastností domény v následujícím příkazu a spusťte je. Sid účtu úložiště požadovaný v následujícím příkazu je SID identity, kterou jste vytvořili ve službě AD (oddíl b výše).

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


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Zkontrolujte, zda je tato funkce povolena

Můžete zkontrolovat, zda je tato funkce povolena ve vašem účtu úložiště, můžete použít následující skript:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Nyní jste tuto funkci úspěšně povolili v účtu úložiště. I když je tato funkce povolena, je stále nutné provést další akce, abyste mohli tuto funkci správně používat.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nyní jste úspěšně povolili ověřování služby AD přes SMB a přiřadili vlastní roli, která poskytuje přístup ke sdílené složce Azure s identitou služby AD. Chcete-li udělit přístup ke sdílené složce dalším uživatelům, postupujte podle pokynů v části [Přiřadit přístupová oprávnění](#assign-access-permissions-to-an-identity) k použití identity a [konfigurace oprávnění ntfs přes](#configure-ntfs-permissions-over-smb) oddíly SMB.

## <a name="update-ad-account-password"></a>Aktualizace hesla účtu služby AD

Pokud jste zaregistrovali identitu a účet služby AD představující váš účet úložiště pod ou, která vynucuje dobu vypršení platnosti hesla, musíte heslo otočit před maximálním stářím hesla. Pokud se nepodaří aktualizovat heslo účtu služby AD, bude mít za následek selhání ověřování pro přístup ke sdíleným položkám Azure.  

Chcete-li aktivovat střídání hesla, můžete spustit `Update-AzStorageAccountADObjectPassword` příkaz z modulu AzFilesHybrid. Rutina provádí akce podobné střídání klíčů účtu úložiště. Získá druhý klíč Protokolu Kerberos účtu úložiště a použije jej k aktualizaci hesla registrovaného účtu ve službách AD. Poté regeneruje cílový klíč Protokolu Kerberos účtu úložiště a aktualizuje heslo registrovaného účtu ve službě AD. Tuto rutinu je nutné spustit v prostředí spojeném s doménou služby AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Další kroky

Další informace o souborech Azure a o tom, jak používat službu AD přes SMB, najdete v těchto prostředcích:

- [Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
