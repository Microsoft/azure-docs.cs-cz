---
title: 'Azure Active Directory Domain Services: Průvodce odstraňováním potíží | Microsoft Docs'
description: Průvodce řešením potíží pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: maheshu
ms.openlocfilehash: b15f339889aa2a4602295e662877e82a1d3a6b46
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219434"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – Průvodce odstraňováním potíží s
Tento článek obsahuje pokyny k odstranění potíží pro problémy, se kterými se můžete setkat při nastavení nebo jejich správě Azure Active Directory (AD) Domain Services.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nejde povolit Azure AD Domain Services pro svůj adresář Azure AD
Tato část vám pomůže vyřešit chyby při pokusu o povolení služby Azure AD Domain Services pro svůj adresář.

Vyberte kroky řešení potíží, které odpovídají v chybové zprávě, na které narazíte.

| **Chybová zpráva** | **Řešení** |
| --- |:--- |
| *Název contoso100.com se už v síti používá. Zadejte název, který se nepoužívá.* |[Konflikt názvů domény ve virtuální síti](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Služba nemá dostatečná oprávnění pro aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikace s názvem Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Domain Services nemá dostatečná oprávnění k aplikaci synchronizace Azure AD Domain Services](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Domain Services není správně nakonfigurována ve vašem klientovi](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD zakázaná. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-c000-000000000000 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Microsoft Graph je zakázána v klientovi služby Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt názvů domény
**Chybová zpráva:**

*Název contoso100.com se už v síti používá. Zadejte název, který se nepoužívá.*

**Náprava:**

Ujistěte se, že nemáte existující doménu se stejným názvem domény k dispozici ve virtuální síti. Například předpokládejme, že již máte doménu „contoso.com“ k dispozici na vybrané virtuální síti. Později Zkuste povolit spravované doméně služby Azure AD Domain Services se stejným názvem domény (tedy "contoso.com") ve virtuální síti. Dojde k chybě při pokusu o povolení služby Azure AD Domain Services.

Toto selhání je z důvodu konfliktu názvů pro název domény ve virtuální síti. V této situaci musíte použít jiný název pro nastavení domény spravované službou Azure AD Domain Services. Případně můžete zrušit existující doménu a poté pokračovat v povolení služby Azure AD Domain Services.

### <a name="inadequate-permissions"></a>Nedostatečná oprávnění
**Chybová zpráva:**

*Domain Services nelze povolit v tomto tenantovi Azure AD. Služba nemá dostatečná oprávnění pro aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikace s názvem Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Náprava:**

Zkontrolujte, zda je aplikace s názvem "Azure AD Domain Services synchronizace, v adresáři služby Azure AD. Pokud tuto aplikaci existuje, odstraňte ji a pak opětovného povolení služby Azure AD Domain Services.

Proveďte následující kroky k kontrolovat přítomnost aplikace a k odstranění, pokud existuje aplikace:

1. Přejděte na **aplikace** oddílu adresáře Azure AD [portál Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Vyberte **všechny aplikace** v **zobrazit** rozevíracího seznamu. Vyberte **žádné** v **stav aplikace** rozevíracího seznamu. Vyberte **žádné** v **aplikace viditelnost** rozevíracího seznamu.
3. Typ **synchronizace Azure AD Domain Services** do vyhledávacího pole. Pokud aplikace existuje, klikněte na něm a klikněte na **odstranit** tlačítka na panelu nástrojů ho odstranit.
4. Jakmile jste odstranili aplikace, zkuste znovu povolit Azure AD Domain Services.

### <a name="invalid-configuration"></a>Neplatná konfigurace
**Chybová zpráva:**

*Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Náprava:**

Zkontrolujte, zda jsou aplikace s názvem 'AzureActiveDirectoryDomainControllerServices' (s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64) v adresáři služby Azure AD. Pokud tuto aplikaci existuje, musíte ji odstranit a pak opětovného povolení služby Azure AD Domain Services.

Pomocí následujícího skriptu prostředí PowerShell můžete najít aplikaci a odstranit.

> [!NOTE]
> Tento skript používá **Azure AD PowerShell verze 2** rutiny. Úplný seznam všech dostupných rutin a stáhnout modul, přečtěte si [AzureAD PowerShell referenční dokumentaci k nástroji](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph zakázáno
**Chybová zpráva:**

Domain Services nelze povolit v klientovi Azure AD. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD zakázaná. Povolit aplikaci s 00000002-0000-0000-c000-000000000000 identifikátor aplikace a zkuste povolit pro vašeho tenanta Azure AD Domain Services.

**Náprava:**

Zkontrolujte, zda jste zakázali aplikaci s identifikátorem 00000002-0000-0000-c000-000000000000. Tato aplikace je aplikace Microsoft Azure AD a poskytuje rozhraní Graph API přístup ke klientovi Azure AD. Služba Azure AD Domain Services musí být povolena synchronizace klientovi Azure AD k vaší spravované domény v této aplikaci.

Tuto chybu vyřešit, povolte tuto aplikaci a zkuste povolit pro vašeho tenanta Azure AD Domain Services.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Uživatelé se nemůžou přihlásit ke spravované doméně Azure AD Domain Services
Pokud jeden nebo více uživatelů v klientovi služby Azure AD nejde se přihlásit k nově vytvořený spravované doméně, proveďte následující kroky řešení potíží:

* **Přihlášení pomocí formátu UPN:** zkuste se přihlásit pomocí formátu UPN (například "joeuser@contoso.com') namísto SAMAccountName formátu (CONTOSO\joeuser). SAMAccountName mohou být automaticky generovány pro uživatele, jehož předpona názvu UPN je příliš dlouhý, nebo je stejný jako jiný uživatel na spravované doméně. Formát UPN je musí být jedinečný v rámci klienta Azure AD.

> [!NOTE]
> Doporučujeme použít formát UPN pro přihlášení k spravované doméně služby Azure AD Domain Services.
>
>

* Zkontrolujte, že jste [povolili synchronizaci hesel](active-directory-ds-getting-started-password-sync.md) podle kroků uvedených v příručce Začínáme.
* **Externí účty:** Ujistěte se, že ovlivněných uživatelský účet není externího účtu v klientovi Azure AD. Příklady externí účty jsou účty Microsoft (například "joe@live.com') nebo uživatelské účty z externího adresář Azure AD. Vzhledem k tomu, že Azure AD Domain Services nemá oprávnění pro tyto uživatelské účty, tito uživatelé nemůže přihlásit k spravované doméně.
* **Synchronizovat účty:** Pokud účty ovlivněného uživatele jsou synchronizované z místního adresáře, ověřte, že:

  * Nasazení nebo aktualizovat, aby [doporučujeme nejnovější verzi služby Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
  * Nakonfigurování Azure AD Connect na [provést úplnou synchronizaci](active-directory-ds-getting-started-password-sync.md).
  * V závislosti na velikosti adresáře může chvíli trvat, než pro uživatelské účty a přihlašovací údaje, hodnoty hash být k dispozici v Azure AD Domain Services. Zajistěte, aby že čekání dost dlouho, než se budete pokoušet ověřování.
  * Pokud potíže potrvají po ověření předchozích kroků, zkuste restartovat službu Microsoft Azure AD Sync. Z počítače synchronizace spustí příkazový řádek a spusťte následující příkazy:

    1. net stop "Microsoft Azure AD Sync.
    2. net start "Microsoft Azure AD Sync.
* **Jen cloudové účty**: Pokud ovlivněných uživatelský účet účet uživatele jenom pro cloud, zajistěte, že uživatel došlo ke změně hesla po povolení služby Azure AD Domain Services. Tento krok způsobí, že se vygenerují hodnoty hash přihlašovacích údajů potřebné pro Azure AD Domain Services.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Na vaší spravované domény nejsou jedné nebo několika výstrah

Zjistit, jak vyřešit výstrahy na vaší spravované domény tak, že navštívíte [řešení výstrahy](active-directory-ds-troubleshoot-alerts.md) článku.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Uživatelé odebrána z vašeho klienta Azure AD nejsou odebrány z vaší spravované domény
Azure AD vás chrání před náhodným odstraněním objektů uživatelů. Když odstraníte uživatelský účet z vašeho tenanta Azure AD, odpovídající objekt uživatele se přesune do složky Koš. Pokud tuto operace odstranění se synchronizuje s vaší spravované domény, budou odpovídající uživatelskému účtu označeno jako zakázané. Tato funkce vám pomůže obnovit nebo zrušení odstranění uživatelský účet později.

Uživatelský účet zůstane v zakázaném stavu ve vaší spravované domény, i když je znovu vytvořit uživatelský účet s stejný hlavní název uživatele v adresáři služby Azure AD. Chcete-li odebrat uživatelský účet z vaší spravované domény, nuceně odstranit z vašeho klienta Azure AD.

Chcete-li odebrat uživatelský účet plně z vaší spravované domény, trvale odstraňte uživatele z vašeho klienta Azure AD. Použití `Remove-MsolUser` rutiny prostředí PowerShell s `-RemoveFromRecycleBin` možnost, jak je popsáno v tomto [článku na webu MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na produktový tým Azure Active Directory Domain Services na [sdílet zpětnou vazbu nebo pro podporu](active-directory-ds-contact-us.md).
