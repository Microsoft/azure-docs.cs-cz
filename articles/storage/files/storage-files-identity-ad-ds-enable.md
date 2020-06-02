---
title: Povolení služba AD DS ověřování pro sdílené složky Azure
description: Naučte se, jak povolit Active Directory Domain Services ověřování pomocí protokolu SMB pro sdílené složky Azure. Virtuální počítače s Windows připojenými k doméně pak můžou přistupovat ke sdíleným složkám Azure pomocí služba AD DS přihlašovacích údajů.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 5592a3c53a57e9cd96468bfca187e02faef28b05
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268503"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Část 1: povolení ověřování služba AD DS pro sdílené složky Azure 

Než povolíte ověřování Active Directory Domain Services (služba AD DS), ujistěte se, že jste si přečetli [článek s přehledem](storage-files-identity-auth-active-directory-enable.md) , abyste porozuměli podporovaným scénářům a požadavkům.

Tento článek popisuje proces potřebný k povolení ověřování Active Directory Domain Services (služba AD DS) ve vašem účtu úložiště. Po povolení této funkce je nutné nakonfigurovat svůj účet úložiště a služba AD DS, aby se k ověřování sdílené složky Azure používaly přihlašovací údaje služba AD DS. Pokud chcete povolit služba AD DS ověřování pomocí protokolu SMB pro sdílené složky Azure, musíte svůj účet úložiště zaregistrovat pomocí služba AD DS a pak nastavit požadované vlastnosti domény v účtu úložiště. Když je tato funkce povolená v účtu úložiště, platí pro všechny nové a existující sdílené složky v účtu.

## <a name="option-one-recommended-use-the-script"></a>Možnost 1 (doporučeno): použít skript

Skript v tomto článku provede nezbytné změny a umožní vám to funkce. Vzhledem k tomu, že některé části skriptu budou spolupracovat s místními služba AD DS, Vysvětleme, co skript dělá, abyste mohli určit, jestli se změny zarovnají se zásadami dodržování předpisů a zabezpečení, a máte jistotu, že máte správná oprávnění ke spuštění skriptu. I když ho nemůžete použít, doporučujeme vám postup, abyste ho mohli ručně provést.

### <a name="script-prerequisites"></a>Předpoklady pro skripty

- [Stažení a extrahování modulu AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Nainstalujte a spusťte modul v zařízení, které je připojené k místnímu počítači, služba AD DS služba AD DS přihlašovací údaje, které mají oprávnění k vytvoření přihlašovacího účtu služby nebo účtu počítače v cílové službě AD.
-  Spusťte skript pomocí místního služba AD DS přihlašovacích údajů, které se synchronizují s vaší službou Azure AD. Přihlašovací údaje pro místní služba AD DS musí mít oprávnění role správce účtu úložiště nebo role RBAC pro přispěvatele.

### <a name="offline-domain-join"></a>Offline připojení k doméně

`Join-AzStorageAccountForAuth`Rutina provádí ekvivalent offline připojení k doméně jménem zadaného účtu úložiště. Skript pomocí rutiny vytvoří účet ve vaší doméně služby Active Directory, buď [účet počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí), nebo [účet přihlášení služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Pokud se rozhodnete spustit příkaz ručně, měli byste vybrat účet, který nejlépe vyhovuje vašemu prostředí.

Účet služba AD DS vytvořený rutinou představuje účet úložiště. Pokud je účet služba AD DS vytvořen v rámci organizační jednotky (OU), která vynutila vypršení platnosti hesla, je nutné aktualizovat heslo před maximálním stářím hesla. Nepodaří se aktualizovat heslo účtu dřív, než tato brána způsobí selhání ověřování při přístupu ke sdíleným složkám Azure. Informace o tom, jak aktualizovat heslo, najdete v tématu [aktualizace hesla účtu služba AD DS](storage-files-identity-ad-ds-update-password.md).

### <a name="use-the-script-to-enable-ad-ds-authentication"></a>Povolení služba AD DS ověřování pomocí skriptu

Nezapomeňte nahradit hodnoty zástupných symbolů vlastními v parametrech níže, než je spustíte v PowerShellu.
> [!IMPORTANT]
> Rutina připojení k doméně vytvoří účet služby AD, který bude představovat účet úložiště (sdílená složka) ve službě AD. Můžete se rozhodnout, že se zaregistrujete jako účet počítače nebo přihlašovací účet služby. Podrobnosti najdete v [části Nejčastější dotazy](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) . V případě účtů počítačů je ve službě AD po dobu 30 dnů nastavena výchozí doba platnosti hesla. Podobně platí, že přihlašovací účet služby může mít nastavené stáří vypršení platnosti hesla na doméně služby AD nebo organizační jednotce (OU).
> U obou typů účtů doporučujeme, abyste zkontrolovali stáří vypršení platnosti hesla nakonfigurované v prostředí služby AD a naplánovali byste si [aktualizovat heslo účtu úložiště](storage-files-identity-ad-ds-update-password.md) účtu služby AD před maximálním stářím hesla. Můžete zvážit [Vytvoření nové organizační jednotky AD (OU) ve službě AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) a zakázat zásady pro vypršení platnosti hesla na účtech [počítačů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) nebo účtů přihlášení služby odpovídajícím způsobem. 

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
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount" if parameter is omitted
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-script-actions"></a>Možnost 2: ruční provedení akcí skriptu

Pokud jste už dříve úspěšně spustili `Join-AzStorageAccountForAuth` skript, přečtěte si část [potvrzení, že je funkce povolená](#confirm-the-feature-is-enabled) . Nemusíte provádět následující ruční kroky.

### <a name="checking-environment"></a>Kontroluje se prostředí.

Nejdřív je potřeba, abyste zkontrolovali stav svého prostředí. Konkrétně je nutné ověřit, zda je [Služba Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) nainstalována a zda je prostředí spuštěno s oprávněními správce. Potom zkontrolujte, zda je nainstalován [modul AZ. Storage 2,0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) a nainstalujte jej, pokud není. Po dokončení těchto kontrol zkontrolujte služba AD DS a podívejte se, jestli je k dispozici buď [účet počítače](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (výchozí), nebo [účet přihlášení služby](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , který už je vytvořený pomocí SPN nebo UPN, jako "CIFS/Your-Storage-Account-Name-tady. File. Core. Windows. NET". Pokud účet neexistuje, vytvořte ho tak, jak je popsáno v následující části.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Ruční vytvoření identity představující účet úložiště ve službě AD

Pokud chcete tento účet vytvořit ručně, vytvořte nový klíč Kerberos pro svůj účet úložiště pomocí `New-AzStorageAccountKey -KeyName kerb1` . Pak použijte tento klíč Kerberos jako heslo pro váš účet. Tento klíč se používá jenom během instalace a nedá se použít pro žádné operace ovládacího prvku nebo roviny dat na účtu úložiště. Jakmile budete mít tento klíč, vytvořte v rámci své organizační jednotky účet služby nebo počítače. Použijte následující specifikaci (Nezapomeňte nahradit vzorový text názvem svého účtu úložiště):

SPN: "CIFS/Your-Storage-Account-Name-. soubor. Core. Windows. NET" heslo: klíč protokolu Kerberos pro váš účet úložiště.

Pokud vaše organizační jednotka vynutila vypršení platnosti hesla, musíte aktualizovat heslo před maximálním stářím hesla, aby nedocházelo k chybám ověřování při přístupu ke sdíleným složkám Azure. Podrobnosti najdete v tématu [aktualizace hesla identity účtu úložiště ve službě AD](storage-files-identity-ad-ds-update-password.md) .

Ponechte si identifikátor SID nově vytvořené identity, budete ho potřebovat pro další krok. Identita, kterou jste vytvořili, která představuje účet úložiště, nemusí být synchronizovaná s Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Povolení funkce v účtu úložiště

Tuto funkci teď můžete povolit na svém účtu úložiště. V následujícím příkazu zadejte podrobnosti konfigurace pro vlastnosti domény a pak je spusťte. Identifikátor SID účtu úložiště vyžadovaný v následujícím příkazu je identifikátor SID identity, kterou jste vytvořili v služba AD DS v [předchozí části](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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

### <a name="debugging"></a>Ladění

K provedení sady základních kontrol konfigurace služby AD pomocí přihlášeného uživatele služby AD můžete spustit rutinu Debug-AzStorageAccountAuth. Tuto rutinu podporuje AzFilesHybrid verze 0.1.2 nebo novější. Další informace o kontrolách provedených v této rutině najdete v tématu [nelze připojit soubory Azure s přihlašovacími údaji služby AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) v Průvodci odstraňováním potíží pro Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Potvrďte, že je funkce povolená.

Pomocí následujícího skriptu můžete ověřit, jestli je funkce ve vašem účtu úložiště povolená:

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

## <a name="next-steps"></a>Další kroky

V tomto okamžiku jste úspěšně povolili funkci v účtu úložiště. Chcete-li použít funkci, je nutné nakonfigurovat a provést změny. Přejděte k další části.

[Druhá část: přiřazení oprávnění na úrovni sdílení k identitě](storage-files-identity-ad-ds-assign-permissions.md)