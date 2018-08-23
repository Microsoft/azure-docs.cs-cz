---
title: 'Azure Active Directory Domain Services: Průvodce odstraňováním potíží | Dokumentace Microsoftu'
description: Průvodce odstraňováním potíží pro Azure AD Domain Services
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
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: maheshu
ms.openlocfilehash: fa4d40786fdc61183c119a17b6e36bd853a6fd64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060755"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – Průvodce odstraňováním potíží
Tento článek obsahuje pokyny k odstranění potíží pro problémy, na které můžete narazit při nastavení a Správa služby Azure Active Directory (AD) Domain Services.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nejde povolit Azure AD Domain Services pro svůj adresář Azure AD
Tato část umožňuje řešení chyb při pokusu o povolení služby Azure AD Domain Services pro svůj adresář.

Můžete si vyberte kroků pro řešení problémů, které odpovídají chybová zpráva, na které narazíte.

| **Chybová zpráva** | **Řešení** |
| --- |:--- |
| *Název contoso100.com se už v síti používá. Zadejte název, který se nepoužívá.* |[Konflikt názvu domény ve virtuální síti](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Služba nemá dostatečná oprávnění pro aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikace s názvem Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Domain Services nemá dostatečná oprávnění k aplikaci Azure AD Domain Services Sync](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Domain Services není správně nakonfigurována ve vašem tenantovi](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD zakázaná. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-c000-000000000000 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Microsoft Graphu je zakázaná ve vašem tenantovi Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt názvů domény
**Chybová zpráva:**

*Název contoso100.com se už v síti používá. Zadejte název, který se nepoužívá.*

**Náprava:**

Ujistěte se, že nemáte existující doménu se stejným názvem domény dostupným v dané virtuální síti. Například předpokládejme, že již máte doménu „contoso.com“ k dispozici na vybrané virtuální síti. Později Zkuste povolit spravované domény služby Azure AD Domain Services se stejným názvem domény (tedy "contoso.com") v dané virtuální síti. Dojde k chybě při pokusu o povolení služby Azure AD Domain Services.

To je způsobeno konflikty názvů pro název domény v této virtuální síti. V této situaci musíte použít jiný název pro nastavení domény spravované službou Azure AD Domain Services. Případně můžete zrušit existující doménu a poté pokračovat v povolení služby Azure AD Domain Services.

### <a name="inadequate-permissions"></a>Nedostatečná oprávnění
**Chybová zpráva:**

*Domain Services nelze povolit v tomto tenantovi Azure AD. Služba nemá dostatečná oprávnění pro aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikace s názvem Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Náprava:**

Zkontrolujte, jestli je aplikace s názvem "Azure AD Domain Services Sync' v adresáři služby Azure AD. Pokud tuto aplikaci existuje, odstraňte ho a potom znovu povolit Azure AD Domain Services.

Proveďte následující kroky a kontrolovat přítomnost aplikace. odstraňte ji, pokud existuje aplikace:

1. Přejděte **aplikací** svého adresáře Azure AD v [webu Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Vyberte **všechny aplikace** v **zobrazit** rozevíracího seznamu. Vyberte **jakékoli** v **stav aplikací** rozevíracího seznamu. Vyberte **jakékoli** v **viditelnost aplikace** rozevíracího seznamu.
3. Typ **Azure AD Domain Services Sync** do vyhledávacího pole. Pokud aplikace existuje, klikněte na něj a klikněte na tlačítko **odstranit** tlačítko na panelu nástrojů k jeho odstranění.
4. Po odstranění aplikace se pokusí znovu povolit Azure AD Domain Services.

### <a name="invalid-configuration"></a>Neplatná konfigurace
**Chybová zpráva:**

*Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Náprava:**

Zaškrtněte, pokud chcete zobrazit, pokud máte aplikaci s názvem "AzureActiveDirectoryDomainControllerServices" (s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64) v adresáři služby Azure AD. Pokud tuto aplikaci existuje, musíte odstranit a znovu povolit Azure AD Domain Services.

Pomocí následujícího skriptu prostředí PowerShell aplikaci vyhledat a odstranit ji.

> [!NOTE]
> Tento skript používá **Azure AD PowerShell verze 2** rutiny. Úplný seznam všech dostupných rutin a stáhnout modul, najdete v článku [referenční dokumentace k Azure AD Powershellu](https://msdn.microsoft.com/library/azure/mt757189.aspx).
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

Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD zakázaná. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-c000-000000000000 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.

**Náprava:**

Zkontrolujte, jestli jste zakázali aplikaci s identifikátorem 00000002-0000-0000-c000-000000000000. Tato aplikace je aplikace Microsoft Azure AD a poskytuje přístup k rozhraní Graph API k tenantovi Azure AD. Azure AD Domain Services potřebuje tuto aplikaci, aby mohl synchronizovat vašeho tenanta Azure AD do spravované domény.

Chcete-li vyřešit tuto chybu, povolte tuto aplikaci a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Uživatelé se nemůžou přihlásit ke spravované doméně Azure AD Domain Services
Pokud jeden nebo více uživatelů ve vašem tenantovi Azure AD se nepodařilo přihlásit ke spravované doméně nově vytvořený, proveďte následující kroky:

* **Přihlásit pomocí formátu UPN:** zkuste se přihlásit pomocí formátu UPN (například "joeuser@contoso.com') namísto formátu SAMAccountName (CONTOSO\joeuser). SAMAccountName automaticky vygeneruje se pro uživatele, jehož předpona hlavní název uživatele je příliš dlouhá nebo je stejný jako jiný uživatel ve spravované doméně. Formát UPN je musí být jedinečný v rámci tenanta služby Azure AD.

> [!NOTE]
> Doporučujeme použít formát hlavního názvu uživatele přihlásit ke spravované doméně služby Azure AD Domain Services.
>
>

* Zkontrolujte, že jste [povolili synchronizaci hesel](active-directory-ds-getting-started-password-sync.md) podle kroků uvedených v příručce Začínáme.
* **Externí účty:** Ujistěte se, že příslušný uživatelský účet není externím účtem v tenantovi Azure AD. Příklady externí účty jsou účty Microsoft (například "joe@live.com") nebo uživatelské účty z externí adresář Azure AD. Protože Azure AD Domain Services nemá žádné přihlašovací údaje pro takové uživatelské účty, tito uživatelé přihlásit ke spravované doméně.
* **Synchronizovat účty:** Pokud příslušné uživatelské účty synchronizované z místního adresáře, ověřte, že:

  * Nasazení nebo aktualizovat, aby [nejnovější doporučená verze služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Nakonfigurovali jste Azure AD Connect k [provádět úplnou synchronizaci](active-directory-ds-getting-started-password-sync.md).
  * V závislosti na velikosti vašeho adresáře může trvat nějakou dobu uživatelské účty a hodnoty hash bude k dispozici ve službě Azure AD Domain Services přihlašovacích údajů. Zajistěte, aby že počkejte dostatečně dlouho, než to zkusíte znovu ověřování.
  * Pokud se problém nevyřeší po ověření v předchozích krocích, zkuste restartovat službu Microsoft Azure AD Sync. Z počítače synchronizace spusťte příkazový řádek a spusťte následující příkazy:

    1. net stop "Microsoft Azure AD Sync.
    2. příkaz net start "Microsoft Azure AD Sync.
* **Účty jenom cloudu**: Pokud příslušný uživatelský účet je účet jenom cloudu, ujistěte se, že uživatel změnil heslo po povolení služby Azure AD Domain Services. Tento krok způsobí, že se vygenerují hodnoty hash přihlašovacích údajů potřebné pro Azure AD Domain Services.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Nejsou k dispozici jeden nebo více upozornění ve vaší spravované doméně

Zjistěte, jak vyřešit upozornění ve vaší spravované doméně návštěvou [řešit oznámení](active-directory-ds-troubleshoot-alerts.md) článku.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Uživatelé Odebraní z vašeho tenanta Azure AD se neodeberou z vaší spravované domény
Azure AD vás chrání před náhodným odstraněním objektů uživatelů. Když odstraníte uživatelský účet z vašeho tenanta Azure AD, odpovídající objekt uživatele se přesune do složky Koš. Když se tato operace odstranění se synchronizují do spravované domény, způsobí, že odpovídající uživatelský účet se označí jako zakázaný. Tato funkce vám pomůže obnovit nebo uživatelský účet později obnovit.

Uživatelský účet zůstane v zakázaném stavu ve vaší spravované doméně, i když znovu vytvoříte uživatelský účet se stejným hlavním názvem uživatele v adresáři služby Azure AD. Chcete-li odebrat uživatelský účet z vaší spravované domény, nuceně odstranit z vašeho tenanta Azure AD.

Odebrat uživatelský účet plně z vaší spravované domény, trvale odstraňte uživatele z vašeho tenanta Azure AD. Použití `Remove-MsolUser` rutiny prostředí PowerShell s `-RemoveFromRecycleBin` možnosti, jak je popsáno v tomto [článku na webu MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
