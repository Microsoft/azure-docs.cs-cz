---
title: Povolení ověřování služby Active Directory přes protokol SMB pro soubory Azure
description: Naučte se, jak povolit ověřování na základě identity přes SMB pro sdílené složky Azure prostřednictvím služby Active Directory. Virtuální počítače s Windows připojené k doméně potom můžou přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů služby AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: rogarana
ms.openlocfilehash: d9d2e06cc3beae8a7bb8ea1b4eee15fb1641ddd4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255220"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Povolení ověřování služby Active Directory přes protokol SMB pro sdílené složky Azure

Služba [soubory Azure](storage-files-introduction.md) podporuje ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím dvou typů doménových služeb: Azure Active Directory Domain Services (Azure služba AD DS) (GA) a Active Directory (AD) (ve verzi Preview). Tento článek se zaměřuje na nově zavedenou podporu (ve verzi Preview) využití služby Doména služby Active Directory pro ověřování do sdílených složek Azure. Pokud vás zajímá povolení ověřování Azure služba AD DS (GA) pro sdílené složky Azure, přečtěte si [náš článek na předmětu](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Sdílené složky Azure podporují pouze ověřování v rámci jedné doménové služby, buď Azure Active Directory Domain Service (Azure služba AD DS), nebo Active Directory (AD). 
>
> Identity služby AD používané pro ověřování sdílené složky Azure se musí synchronizovat do Azure AD. Synchronizace hodnot hash hesel je volitelná. 
> 
> Ověřování AD nepodporuje ověřování u účtů počítačů vytvořených ve službě AD. 
> 
> Ověřování AD se dá podporovat jenom pro jednu doménovou strukturu služby Active Directory, do které je účet úložiště zaregistrovaný. Ve výchozím nastavení můžete přistupovat ke sdíleným složkám Azure pomocí přihlašovacích údajů služby AD z jedné doménové struktury služby AD. Pokud potřebujete mít přístup ke sdílené složce Azure z jiné doménové struktury, ujistěte se, že máte nakonfigurovanou správnou důvěryhodnost doménové struktury. Podrobnosti najdete v [části Nejčastější dotazy](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) .  
> 
> Ověřování AD pro přístup SMB a trvalost seznamů řízení přístupu (ACL) se podporuje pro sdílené složky Azure spravované pomocí Azure File Sync.
>
> Soubory Azure podporují ověřování pomocí protokolu Kerberos se službou AD s šifrováním RC4-HMAC. Šifrování AES Kerberos ještě není podporované.

Když povolíte službu AD pro sdílené složky Azure přes protokol SMB, počítače připojené k doméně AD můžou připojit sdílené složky Azure pomocí stávajících přihlašovacích údajů služby AD. Tato funkce se dá povolit u prostředí služby AD hostovaného buď v premch počítačích, nebo hostovaných v Azure.

Identity služby AD používané pro přístup ke sdíleným složkám Azure musí být synchronizované do Azure AD, aby bylo možné vyhodnotit oprávnění k souborům na úrovni sdílené složky prostřednictvím standardního modelu [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md) . Pro soubory nebo adresáře přenesené z existujících souborových serverů se zachovají a vynutily [volitelné seznamy stylů Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) . Tato funkce nabízí bezproblémovou integraci s infrastrukturou domény podnikové služby AD. Při nahrazení Prem souborové servery pomocí sdílených složek Azure mají stávající uživatelé přístup ke sdíleným složkám Azure ze svých současných klientů s jednotným přihlašováním bez jakýchkoli změn v přihlašovacích údajích, které se používají.  
 
## <a name="prerequisites"></a>Požadavky 

Než povolíte ověřování AD pro sdílené složky Azure, ujistěte se, že jste dokončili následující požadavky: 

- Vyberte nebo vytvořte prostředí služby AD a synchronizujte ho s Azure AD. 

    Tuto funkci můžete povolit v novém nebo existujícím prostředí služby Active Directory. Identity používané pro přístup se musí synchronizovat do Azure AD. Tenant služby Azure AD a sdílená složka, ke kterým přistupujete, musí být přidruženy ke stejnému předplatnému. 

    Pokud chcete nastavit prostředí domény služby Active Directory, přečtěte si [téma přehled Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Pokud jste službu AD nesynchronizoval do služby Azure AD, postupujte podle pokynů v části [co je hybridní identita s Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) , abyste zjistili upřednostňovanou metodu ověřování a Azure AD Connect možnosti instalace. 

- Připojte se k místnímu počítači nebo VIRTUÁLNÍmu počítači Azure ke službě AD (označované také jako služba AD DS). 

    Pokud chcete ke sdílené složce přistupovat pomocí přihlašovacích údajů služby AD z počítače nebo virtuálního počítače, musí být zařízení připojené k doméně AD. Informace o tom, jak připojit k doméně službě AD, najdete v tématu [připojení počítače k doméně](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- V [podporované oblasti](#regional-availability)vyberte nebo vytvořte účet úložiště Azure. 

    Ujistěte se, že účet úložiště obsahující vaše sdílené složky ještě není nakonfigurovaný pro ověřování Azure služba AD DS. Pokud je v účtu úložiště povolené Azure Files Azure služba AD DS Authentication, musí být před změnou na používání služby AD zakázané. To znamená, že existující seznamy ACL, které jsou nakonfigurované v prostředí Azure služba AD DS, bude nutné překonfigurovat pro správné vynucení oprávnění.
    
    Informace o vytvoření nové sdílené složky najdete v tématu [Vytvoření sdílené složky ve službě soubory Azure](storage-how-to-create-file-share.md).
    
    Pro zajištění optimálního výkonu doporučujeme nasadit účet úložiště ve stejné oblasti jako virtuální počítač, ze kterého plánujete přístup ke sdílené složce. 

- Ověřte připojení pomocí klíče účtu úložiště připojením sdílených složek Azure. 

    Pokud chcete ověřit, že vaše zařízení a sdílená složka jsou správně nakonfigurované, zkuste připojit sdílenou složku pomocí klíče účtu úložiště. Další informace najdete v tématu [použití sdílené složky Azure v systému Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regionální dostupnost

Ověřování Azure Files AD (Preview) je dostupné ve [většině veřejných oblastí](https://azure.microsoft.com/global-infrastructure/regions/).

Ověřování služby Azure soubory AD není k dispozici v nástroji:
- Západní USA
- Západní USA 2
- Východní USA
- Východní USA 2
- Západní Evropa
- Severní Evropa

## <a name="workflow-overview"></a>Přehled pracovního postupu

Než povolíte ověřování AD pomocí protokolu SMB pro sdílené složky Azure, doporučujeme vám projít si [požadavky](#prerequisites) a ujistit se, že jste dokončili všechny kroky. Požadavky ověří, že jsou správně nakonfigurovaná vaše prostředí AD, Azure AD a Azure Storage. 

Potom udělte přístup k prostředkům Azure Files pomocí přihlašovacích údajů služby AD: 

- Povolte na svém účtu úložiště ověřování AD služby Azure Files.  

- Přiřazení oprávnění k přístupu ke sdílené složce identitě služby Azure AD (uživatel, skupina nebo instanční objekt), která je synchronizovaná s cílovou identitou AD. 

- Nakonfigurujte seznamy ACL přes protokol SMB pro adresáře a soubory. 

- Připojte sdílenou složku Azure z virtuálního počítače připojeného k doméně AD. 

Následující diagram znázorňuje kompletní pracovní postup pro povolení ověřování Azure AD přes protokol SMB pro sdílené složky Azure. 

![Diagram souborů pracovního postupu služby AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Ověřování AD přes SMB pro sdílené složky Azure se podporuje jenom v počítačích nebo virtuálních počítačích, které používají novější verze operačních systémů než Windows 7 nebo Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Povolení ověřování AD pro svůj účet 

Pokud chcete povolit ověřování AD pomocí protokolu SMB pro sdílené složky Azure, musíte nejdřív zaregistrovat účet úložiště ve službě AD a potom nastavit požadované vlastnosti domény v účtu úložiště. Když je tato funkce povolená v účtu úložiště, platí pro všechny nové a existující sdílené složky v účtu. Funkci povolíte pomocí `join-AzStorageAccountForAuth`. Podrobný popis kompletního a koncového pracovního postupu najdete v níže uvedené části. 

> [!IMPORTANT]
> Rutina `join-AzStorageAccountForAuth` provede změny v prostředí služby Active Directory. Přečtěte si následující vysvětlení, abyste lépe pochopili, co dělá, abyste měli jistotu, že máte správná oprávnění ke spuštění příkazu a že provedené změny odpovídají zásadám dodržování předpisů a zabezpečení. 

Rutina `join-AzStorageAccountForAuth` provede ekvivalent offline připojení k doméně jménem uvedeného účtu úložiště. Vytvoří účet ve vaší doméně služby Active Directory, buď [účet počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) , nebo [účet přihlášení služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Vytvořený účet AD představuje účet úložiště v doméně služby AD. Pokud se účet služby AD vytvoří v rámci organizační jednotky (OU) služby Active Directory, která vynutila vypršení platnosti hesla, musíte aktualizovat heslo před maximálním stářím hesla. Neúspěšné aktualizace hesla účtu služby AD způsobí selhání ověřování při přístupu ke sdíleným složkám Azure. Informace o tom, jak aktualizovat heslo, najdete v tématu [aktualizace hesla k účtu AD](#update-ad-account-password).

K provedení registrace a povolení funkce můžete použít následující skript, případně můžete ručně provést operace, které by měl skript. Tyto operace jsou popsány v části následující po tomto skriptu. Nemusíte dělat obojí.

### <a name="1-check-prerequisites"></a>1. Ověřte předpoklady.
- [Stažení a extrahování modulu AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Nainstalujte a spusťte modul v zařízení, které je připojené k doméně služby AD s přihlašovacími údaji služby AD, které mají oprávnění vytvořit účet pro přihlášení ke službě nebo účet počítače v cílové službě AD.
-  Spusťte skript s použitím přihlašovacích údajů služby Active Directory, které jsou synchronizované s vaší službou Azure AD. Přihlašovací údaje služby AD musí mít oprávnění role správce účtu úložiště nebo role RBAC pro přispěvatele.
- Ujistěte se, že je váš účet úložiště v [podporované oblasti](#regional-availability).

### <a name="2-execute-ad-enablement-script"></a>2. Spusťte skript povolení služby AD.
Nezapomeňte nahradit hodnoty zástupných symbolů vlastními v parametrech níže, než je spustíte v PowerShellu.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU (for example: "OU=ComputersOU,DC=prod,DC=corp,DC=contoso,DC=com")
#You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationalUnitName "<ou-name-here>"
```

Následující popis shrnuje všechny akce prováděné při spuštění rutiny `join-AzStorageAccountForAuth`. Tyto kroky můžete provést ručně, pokud raději nechcete použít příkaz:

> [!NOTE]
> Pokud jste už úspěšně provedli skript JOIN-AzStorageAccountForAuth, přečtěte si další část "3". Potvrďte, že je funkce povolená. Níže uvedené operace není nutné provádět znovu.

#### <a name="a-checking-environment"></a>a. Kontroluje se prostředí.

Nejprve zkontroluje vaše prostředí. Konkrétně kontroluje, zda je [Služba Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) nainstalována a zda je prostředí spuštěno s oprávněními správce. Pak zkontroluje, jestli je nainstalovaný [modul AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , a pokud není, nainstaluje ho. Pokud Kontrola proběhne úspěšně, zkontrolujte ve službě AD, zda existuje buď [účet počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí) nebo [účet přihlášení služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , který již byl vytvořen pomocí hlavního názvu služby (SPN/UPN), jako "CIFS/Your-Storage-Account-Name-zde. File. Core. Windows. NET". Pokud účet neexistuje, vytvoří se, jak je popsáno v části b níže.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Ruční vytvoření identity představující účet úložiště ve službě AD

Pokud chcete tento účet vytvořit ručně, vytvořte nový klíč Kerberos pro svůj účet úložiště pomocí `New-AzStorageAccountKey -KeyName kerb1`. Pak použijte tento klíč Kerberos jako heslo pro váš účet. Tento klíč se používá jenom během nastavení a nedá se použít pro žádné operace ovládacího prvku nebo datové roviny na účtu úložiště.

Jakmile budete mít tento klíč, vytvořte v rámci své organizační jednotky účet služby nebo počítače. Použijte následující specifikaci: název SPN: "CIFS/Your-Storage-Account-Name-sem. File. Core. Windows. NET" Password: klíč Kerberos pro váš účet úložiště.

Pokud vaše organizační jednotka vynutila vypršení platnosti hesla, musíte aktualizovat heslo před maximálním stářím hesla, aby nedocházelo k chybám ověřování při přístupu ke sdíleným složkám Azure. Podrobnosti najdete v tématu [aktualizace hesla k účtu AD](#update-ad-account-password) .

Ponechte si identifikátor SID nově vytvořeného účtu, budete ho potřebovat pro další krok. Identita AD, kterou jste právě vytvořili, která představuje účet úložiště, nemusí být synchronizovaná se službou Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Povolení funkce v účtu úložiště

Skript pak tuto funkci povolí v účtu úložiště. Chcete-li provést tuto instalaci ručně, zadejte v následujícím příkazu některé podrobnosti konfigurace pro vlastnosti domény a potom spusťte příkaz. Identifikátor SID účtu úložiště vyžadovaný v následujícím příkazu je identifikátor SID identity, kterou jste vytvořili ve službě AD (oddíl b výše).

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServicesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Zkontrolujte, jestli je funkce povolená.

Můžete zkontrolovat, jestli je funkce ve vašem účtu úložiště povolená, můžete použít následující skript:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Teď jste úspěšně povolili funkci v účtu úložiště. I když je tato funkce povolená, pořád potřebujete provést další akce, aby bylo možné tuto funkci používat správně.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nyní jste úspěšně povolili ověřování AD přes protokol SMB a přiřadili jste vlastní roli, která poskytuje přístup ke sdílené složce Azure s identitou AD. Pokud chcete ostatním uživatelům udělit přístup ke sdílené složce, postupujte podle pokynů v tématu [Přiřazení přístupových oprávnění](#assign-access-permissions-to-an-identity) k používání identity a [Konfigurace oprávnění NTFS v](#configure-ntfs-permissions-over-smb) oddílech SMB.

## <a name="update-ad-account-password"></a>Aktualizovat heslo účtu AD

Pokud jste zaregistrovali identitu nebo účet AD představující váš účet úložiště v rámci organizační jednotky, která vynutila dobu vypršení platnosti hesla, musíte heslo otočit před maximálním stářím hesla. Neúspěšná aktualizace hesla účtu služby Active Directory způsobí selhání ověřování pro přístup ke sdíleným složkám Azure.  

Pokud chcete aktivovat otočení hesla, můžete spustit příkaz `Update-AzStorageAccountADObjectPassword` z modulu AzFilesHybrid. Rutina provede akce podobné rotaci klíče účtu úložiště. Získá druhý klíč protokolu Kerberos účtu úložiště a použije ho k aktualizaci hesla registrovaného účtu ve službě AD. Pak znovu vygeneruje cílový klíč Kerberos účtu úložiště a aktualizuje heslo registrovaného účtu ve službě AD. Tuto rutinu musíte spustit v prostředí připojeném k doméně AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Další kroky

Další informace o službě soubory Azure a o tom, jak používat službu AD prostřednictvím protokolu SMB, najdete v těchto zdrojích:

- [Přehled podpory ověřování na základě identity souborů Azure pro přístup přes protokol SMB](storage-files-active-directory-overview.md)
- [Nejčastější dotazy](storage-files-faq.md)
