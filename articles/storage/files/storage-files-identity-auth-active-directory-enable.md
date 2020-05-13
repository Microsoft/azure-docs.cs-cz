---
title: Povolení ověřování služby Active Directory přes protokol SMB pro soubory Azure
description: Naučte se, jak povolit ověřování na základě identity přes SMB pro sdílené složky Azure prostřednictvím služby Active Directory. Virtuální počítače s Windows připojené k doméně potom můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů služby AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: febb796a47b9f5e78906d513c115b62b35c7c7d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196499"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Povolit místní Active Directory Domain Services ověřování pomocí protokolu SMB pro sdílené složky Azure

[Soubory Azure](storage-files-introduction.md)   podporuje ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím dvou typů doménových služeb: Azure Active Directory Domain Services (Azure služba AD DS) a místní Active Directory Domain Services (služba AD DS) (verze Preview). Tento článek se zaměřuje na nově zavedenou podporu (ve verzi Preview) využití služby Doména služby Active Directory pro ověřování do sdílených složek Azure. Pokud vás zajímá povolení ověřování Azure služba AD DS (GA) pro sdílené složky Azure, přečtěte si [náš článek na předmětu](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Sdílené složky Azure podporují pouze ověřování v rámci jedné doménové služby, buď Azure Active Directory Domain Service (Azure služba AD DS), nebo místní Active Directory Domain Services (služba AD DS). 
>
> Služba AD DS identit, které se používají pro ověřování sdílené složky Azure, musí být synchronizované do Azure AD. Synchronizace hodnot hash hesel je volitelná. 
> 
> Ověřování pomocí místních služba AD DS nepodporuje ověřování u účtů počítačů vytvořených v služba AD DS. 
> 
> Ověřování pomocí místních služba AD DS se dá podporovat jenom pro jednu doménovou strukturu služby Active Directory, ve které je účet úložiště zaregistrovaný. Ve výchozím nastavení můžete přistupovat ke sdíleným složkám Azure jenom s přihlašovacími údaji služba AD DS z jedné doménové struktury. Pokud potřebujete mít přístup ke sdílené složce Azure z jiné doménové struktury, ujistěte se, že máte nakonfigurovanou správnou důvěryhodnost doménové struktury. Podrobnosti najdete v [části Nejčastější dotazy](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) .  
> 
> Pro sdílené složky Azure spravované pomocí Azure File Sync je podporovaná služba AD DS ověřování pro přístup SMB a trvalost seznamů ACL.
>
> Soubory Azure podporují ověřování pomocí protokolu Kerberos se službou AD s šifrováním RC4-HMAC. Šifrování AES Kerberos ještě není podporované.

Když povolíte služba AD DS pro sdílené složky Azure přes protokol SMB, počítače připojené k služba AD DS můžou připojit sdílené složky Azure pomocí stávajících přihlašovacích údajů služby AD. Tato funkce se dá povolit u prostředí služba AD DS hostovaného buď v premch počítačích, nebo hostovaných v Azure.

Identity používané pro přístup ke sdíleným složkám Azure se musí synchronizovat s Azure AD, aby se vynutila oprávnění k souborům na úrovni sdílené složky prostřednictvím modelu [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md) . Pro soubory nebo adresáře přenesené z existujících souborových serverů se zachovají a vynutily [volitelné seznamy stylů Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) . Tato funkce nabízí bezproblémovou integraci s podnikovým služba AD DSm prostředím. Při nahrazení Prem souborové servery pomocí sdílených složek Azure mají stávající uživatelé přístup ke sdíleným složkám Azure ze svých současných klientů s jednotným přihlašováním bez jakýchkoli změn v přihlašovacích údajích, které se používají.  

> [!NOTE]
> Abychom vám pomohli nastavit ověřování Azure Files AD pro některé běžné případy použití, publikovali jsme [dvě videa](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) s podrobnými pokyny:
> - Výměna místních souborových serverů se soubory Azure (včetně nastavení na privátním odkazu pro soubory a ověřování AD)
> - Používání služby soubory Azure jako kontejneru profilů pro virtuální počítače s Windows (včetně nastavení ověřování AD a konfigurace FsLogix)

## <a name="prerequisites"></a>Požadavky 

Než povolíte služba AD DS ověřování sdílených složek Azure, ujistěte se, že jste dokončili následující požadavky: 

- Vyberte nebo vytvořte prostředí služba AD DS a [synchronizujte ho s Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Tuto funkci můžete povolit na novém nebo existujícím místním služba AD DS prostředí. Identity používané pro přístup se musí synchronizovat do Azure AD. Tenant služby Azure AD a sdílená složka, ke kterým přistupujete, musí být přidruženy ke stejnému předplatnému. 

    Pokud chcete nastavit prostředí domény služby Active Directory, přečtěte si [téma přehled Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Pokud jste službu AD nesynchronizoval do služby Azure AD, postupujte podle pokynů v části [Azure AD Connect a Azure AD Connect Health Průvodce instalací](../../active-directory/hybrid/how-to-connect-install-roadmap.md) a nakonfigurujte a nastavte Azure AD Connect. 

- Připojte se k místnímu počítači nebo VIRTUÁLNÍmu počítači Azure k místnímu služba AD DS. 

    Pro přístup ke sdílené složce pomocí přihlašovacích údajů služby AD z počítače nebo virtuálního počítače musí být zařízení připojené k doméně služba AD DS. Informace o tom, jak připojit k doméně, najdete v tématu [připojení počítače k doméně](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Vyberte nebo vytvořte účet úložiště Azure.  Pro zajištění optimálního výkonu doporučujeme nasadit účet úložiště ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup ke sdílené složce.

    Ujistěte se, že účet úložiště obsahující vaše sdílené složky ještě není nakonfigurovaný pro ověřování Azure služba AD DS. Pokud je v účtu úložiště povolené Azure Files Azure služba AD DS Authentication, musí být před změnou na použití v místním služba AD DS zakázané. To znamená, že existující seznamy ACL, které jsou nakonfigurované v prostředí Azure služba AD DS, bude nutné překonfigurovat pro správné vynucení oprávnění.
    
    Informace o vytvoření nové sdílené složky najdete v tématu [Vytvoření sdílené složky ve službě soubory Azure](storage-how-to-create-file-share.md).

- Ověřte připojení pomocí klíče účtu úložiště připojením sdílených složek Azure. 

    Pokud chcete ověřit, že vaše zařízení a sdílená složka jsou správně nakonfigurované, zkuste připojit [sdílenou složku](storage-how-to-use-files-windows.md) pomocí klíče účtu úložiště. Pokud dochází k problémům s připojením k souborům Azure, přečtěte si prosím [Nástroj pro řešení potíží, který jsme publikovali pro chyby připojení k souborům Azure ve Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Poskytujeme také [pokyny](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) pro řešení scénářů při zablokování portu 445. 

## <a name="regional-availability"></a>Regionální dostupnost

Ověřování pomocí souborů Azure s služba AD DS (Preview) je dostupné ve [všech veřejných oblastech a oblastech Azure gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Přehled

Pokud máte v plánu povolit pro sdílenou složku všechny síťové konfigurace, doporučujeme, abyste před povolením služba AD DS ověřování vyhodnotili [posouzení sítě](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) a nejprve dokončíte související konfiguraci.

Povolením ověřování služba AD DS pro sdílené složky Azure vám umožníte ověřit sdílené složky Azure pomocí vašich přihlašovacích údajů služba AD DS on-Prem. Dále vám umožní lépe spravovat vaše oprávnění, aby bylo možné podrobnější řízení přístupu. To vyžaduje synchronizaci identit z Prem služba AD DS do služby Azure AD pomocí služby AD Connect. Řízení přístupu na úrovni sdílené složky s identitami, které se synchronizují ve službě Azure AD, vám umožní spravovat přístup k souborům a sdíleným složkám pomocí Prem služba AD DS přihlašovacích údajů.

Potom postupujte podle následujících kroků a nastavte soubory Azure pro ověřování služba AD DS: 

1. [Povolit Azure Files služba AD DS ověřování v účtu úložiště](#1-enable-ad-ds-authentication-for-your-account)

1. [Přiřazení oprávnění k přístupu ke sdílené složce identitě služby Azure AD (uživatel, skupina nebo instanční objekt), který je synchronizovaný s cílovou identitou AD](#2-assign-access-permissions-to-an-identity)

1. [Konfigurace seznamů ACL přes protokol SMB pro adresáře a soubory](#3-configure-ntfs-permissions-over-smb)
 
1. [Připojení sdílené složky Azure k VIRTUÁLNÍmu počítači připojenému k vašemu služba AD DS](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [Aktualizujte heslo identity účtu úložiště v služba AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Následující diagram znázorňuje kompletní pracovní postup pro povolení ověřování Azure AD přes protokol SMB pro sdílené složky Azure. 

![Diagram souborů pracovního postupu služby AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Služba AD DS ověřování pomocí protokolu SMB pro sdílené složky Azure je podporované jenom na počítačích nebo virtuálních počítačích, na kterých běží novější verze operačních systémů než Windows 7 nebo Windows Server 2008 R2. 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1 povolení služba AD DS ověřování pro váš účet 

Pokud chcete povolit služba AD DS ověřování pomocí protokolu SMB pro sdílené složky Azure, musíte nejdřív zaregistrovat účet úložiště pomocí služba AD DS a pak nastavit požadované vlastnosti domény v účtu úložiště. Když je tato funkce povolená v účtu úložiště, platí pro všechny nové a existující sdílené složky v účtu. Stáhněte modul AzFilesHybrid PowerShell a použijte `join-AzStorageAccountForAuth` ho k povolení této funkce. Podrobný popis kompletního a koncového pracovního postupu najdete ve skriptu uvnitř této části. 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth`Rutina provede změny v prostředí služby Active Directory. Přečtěte si následující vysvětlení, abyste lépe pochopili, co dělá, abyste měli jistotu, že máte správná oprávnění ke spuštění příkazu a že provedené změny odpovídají zásadám dodržování předpisů a zabezpečení. 

`Join-AzStorageAccountForAuth`Rutina provede ekvivalent offline připojení k doméně jménem uvedeného účtu úložiště. Skript pomocí rutiny vytvoří účet ve vaší doméně služby Active Directory, buď [účet počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí), nebo [účet přihlášení služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Pokud se rozhodnete tuto možnost provést ručně, měli byste vybrat účet, který nejlépe vyhovuje vašemu prostředí.

Účet služba AD DS vytvořený rutinou představuje účet úložiště v doméně služby AD. Pokud je účet služba AD DS vytvořen v rámci organizační jednotky (OU), která vynutila vypršení platnosti hesla, je nutné aktualizovat heslo před maximálním stářím hesla. Při neúspěšném pokusu o aktualizaci hesla účtu dojde k selhání ověřování při přístupu ke sdíleným složkám Azure. Informace o tom, jak aktualizovat heslo, najdete v tématu [aktualizace hesla účtu služba AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

K provedení registrace a povolení funkce můžete použít následující skript, případně můžete ručně provést operace, které by měl skript. Tyto operace jsou popsány v části následující po tomto skriptu. Nemusíte dělat obojí.

### <a name="11-script-prerequisites"></a>požadavky na skripty 1,1
- [Stažení a extrahování modulu AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Nainstalujte a spusťte modul v zařízení, které je připojené k místnímu počítači, služba AD DS služba AD DS přihlašovací údaje, které mají oprávnění k vytvoření přihlašovacího účtu služby nebo účtu počítače v cílové službě AD.
-  Spusťte skript pomocí místního služba AD DS přihlašovacích údajů, které se synchronizují s vaší službou Azure AD. Přihlašovací údaje pro místní služba AD DS musí mít oprávnění role správce účtu úložiště nebo role RBAC pro přispěvatele.
- Ujistěte se, že je váš účet úložiště v [podporované oblasti](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1,2 doména připojení k vašemu účtu úložiště
Nezapomeňte nahradit hodnoty zástupných symbolů vlastními v parametrech níže, než je spustíte v PowerShellu.
> [!IMPORTANT]
> Rutina připojení k doméně vytvoří účet služby AD, který bude představovat účet úložiště (sdílená složka) ve službě AD. Můžete se rozhodnout, že se zaregistrujete jako účet počítače nebo přihlašovací účet služby. Podrobnosti najdete v [části Nejčastější dotazy](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) . V případě účtů počítačů je ve službě AD po dobu 30 dnů nastavena výchozí doba platnosti hesla. Podobně platí, že přihlašovací účet služby může mít nastavené stáří vypršení platnosti hesla na doméně služby AD nebo organizační jednotce (OU).
> U obou typů účtů důrazně doporučujeme, abyste zkontrolovali, jaký je stáří vypršení platnosti hesla nakonfigurované ve vašem prostředí služby AD, a plánujete [aktualizovat heslo své identity účtu úložiště ve službě AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) účtu služby AD níže před maximálním stářím hesla. Můžete zvážit [Vytvoření nové organizační jednotky AD (OU) ve službě AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) a zakázat zásady vypršení platnosti hesla na účtech [počítačů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) nebo účtů přihlášení služby. 

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
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

Následující popis shrnuje všechny akce prováděné při `Join-AzStorageAccountForAuth` spuštění rutiny. Tyto kroky můžete provést ručně, pokud raději nechcete použít příkaz:

> [!NOTE]
> Pokud jste už dříve úspěšně spustili `Join-AzStorageAccountForAuth` skript, přečtěte si další část "1,3" potvrďte, že je funkce povolená. Níže uvedené operace není nutné provádět znovu.

#### <a name="a-checking-environment"></a>a. Kontroluje se prostředí.

Nejprve skript zkontroluje vaše prostředí. Konkrétně kontroluje, zda je [Služba Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) nainstalována a zda je prostředí spuštěno s oprávněními správce. Pak zkontroluje, jestli je nainstalovaný [modul AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , a pokud není, nainstaluje ho. Pokud Kontrola proběhne úspěšně, zkontrolujte služba AD DS a ověřte, zda se jedná o [účet počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí) nebo [účet přihlášení služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , který již byl vytvořen pomocí hlavního názvu služby (SPN) nebo hlavního názvu uživatele (UPN), a to jako "CIFS/Your-Storage-Account-Name-tady. File. Core. Windows. NET". Pokud účet neexistuje, vytvoří se, jak je popsáno v části b níže.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Ruční vytvoření identity představující účet úložiště ve službě AD

Pokud chcete tento účet vytvořit ručně, vytvořte nový klíč Kerberos pro svůj účet úložiště pomocí `New-AzStorageAccountKey -KeyName kerb1` . Pak použijte tento klíč Kerberos jako heslo pro váš účet. Tento klíč se používá jenom během nastavení a nedá se použít pro žádné operace ovládacího prvku nebo datové roviny na účtu úložiště.

Jakmile budete mít tento klíč, vytvořte v rámci své organizační jednotky účet služby nebo počítače. Použijte následující specifikaci: název SPN: "CIFS/Your-Storage-Account-Name-sem. File. Core. Windows. NET" Password: klíč Kerberos pro váš účet úložiště.

Pokud vaše organizační jednotka vynutila vypršení platnosti hesla, musíte aktualizovat heslo před maximálním stářím hesla, aby nedocházelo k chybám ověřování při přístupu ke sdíleným složkám Azure. Podrobnosti najdete [v tématu aktualizace hesla identity účtu úložiště v služba AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) .

Ponechte si identifikátor SID nově vytvořeného účtu, budete ho potřebovat pro další krok. Identita, kterou jste vytvořili, která představuje účet úložiště, nemusí být synchronizovaná s Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Povolení funkce v účtu úložiště

Skript pak tuto funkci povolí v účtu úložiště. Chcete-li provést tuto instalaci ručně, zadejte v následujícím příkazu některé podrobnosti konfigurace pro vlastnosti domény a potom spusťte příkaz. Identifikátor SID účtu úložiště vyžadovaný v následujícím příkazu je identifikátor SID identity, kterou jste vytvořili v služba AD DS v [předchozí části](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3 Potvrďte, že je funkce povolená.

Můžete zkontrolovat, jestli je funkce ve vašem účtu úložiště povolená, můžete použít následující skript:

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

Teď jste úspěšně povolili funkci v účtu úložiště. Teď, když je funkce povolená, je potřeba provést další kroky, abyste mohli tuto funkci používat.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nyní jste úspěšně povolili služba AD DS ověřování přes protokol SMB a přiřadili jste vlastní roli, která poskytuje přístup ke sdílené složce Azure s identitou služba AD DS. Pokud chcete ostatním uživatelům udělit přístup ke sdílené složce, postupujte podle pokynů v tématu [Přiřazení přístupových oprávnění](#2-assign-access-permissions-to-an-identity) k používání identity a [Konfigurace oprávnění NTFS v](#3-configure-ntfs-permissions-over-smb) oddílech SMB.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5 aktualizujte heslo identity účtu úložiště v služba AD DS

Pokud jste zaregistrovali služba AD DS identitu nebo účet představující účet úložiště v rámci organizační jednotky, která vynutila dobu vypršení platnosti hesla, musíte heslo před maximálním stářím hesla otočit. Neúspěšná aktualizace hesla služba AD DS účtu způsobí selhání ověřování pro přístup ke sdíleným složkám Azure.  

Pokud chcete aktivovat otočení hesla, můžete spustit `Update-AzStorageAccountADObjectPassword` příkaz z modulu AzFilesHybrid. Tato rutina provede podobné akce jako při obměně klíčů účtu úložiště. Získá druhý klíč protokolu Kerberos účtu úložiště a použije ho k aktualizaci hesla registrovaného účtu v služba AD DS. Pak znovu vygeneruje cílový klíč Kerberos účtu úložiště a aktualizuje heslo zaregistrovaného účtu v služba AD DS. Tuto rutinu musíte spustit v prostředí připojeném k místní služba AD DS doméně.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Další kroky

Další informace o službě soubory Azure a o tom, jak používat službu AD prostřednictvím protokolu SMB, najdete v těchto zdrojích:

- [Přehled podpory ověřování na základě identity souborů Azure pro přístup přes protokol SMB](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
