---
title: Požadavky na data samoosla správce Azure AD – Azure Active Directory
description: Požadavky na data pro samoobslužné resetování hesla Azure AD a jak je uspokojit
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652362"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Nasazení obnovení hesla bez nutnosti registrace koncových uživatelů

Chcete-li nasadit samoobslužné resetování hesla služby Azure Active Directory (Azure AD), musí být k dispozici ověřovací data. Některé organizace mají své uživatele zadat jejich ověřovací data sami. Jiné organizace dávají přednost synchronizaci s daty, která již ve službě Active Directory existují. Tato synchronizovaná data jsou k dispozici pro Azure AD a SSPR bez nutnosti interakce s uživatelem, pokud splňujete následující požadavky:

* Správně naformátujte data v místním adresáři.
* Konfigurace [služby Azure AD Connect pomocí expresního nastavení](../hybrid/how-to-connect-install-express.md).

Aby telefonní čísla fungovala správně, musí být ve formátu *+CountryCode PhoneNumber*, například +1 4255551234.

> [!NOTE]
> Mezi kódem země a telefonním číslem musí být mezera.
>
> Resetování hesla nepodporuje telefonní rozšíření. I ve formátu +1 4255551234X12345 jsou rozšíření před voláním odebrána.

## <a name="fields-populated"></a>Vyplněná pole

Pokud ve službě Azure AD Connect použijete výchozí nastavení, budou provedena následující mapování:

| Místní služby Active Directory | Azure AD |
| --- | --- |
| phoneČíslo | Telefon do kanceláře |
| mobil | Mobilní telefon |

Poté, co uživatel ověří své číslo mobilního telefonu, pole *Telefon* v části **Ověřování kontaktní ch údajů** ve službě Azure AD je také naplněna tímto číslem.

## <a name="authentication-contact-info"></a>Ověřovací kontaktní údaje

Na stránce **Metody ověřování** pro uživatele Azure AD na webu Azure Portal může globální správce ručně nastavit kontaktní informace pro ověřování, jak je znázorněno na následujícím příkladu snímku obrazovky:

![Ověřovací kontaktní údaje uživatele ve službě Azure AD][Contact]

* Pokud je pole **Telefon** vyplněno a **mobilní telefon** je povolen v zásadách sspr, uživatel uvidí toto číslo na registrační stránce pro obnovení hesla a během pracovního postupu pro obnovení hesla.
* Pole **Alternativní telefon** se nepoužívá pro resetování hesla.
* Pokud je pole **E-mail** vyplněno a **e-mail** je povolen v zásadách sspr, uživatel uvidí tento e-mail na registrační stránce pro obnovení hesla a během pracovního postupu pro resetování hesla.
* Pokud je pole **Alternativní e-mail** naplněna a **e-mail** je povolen v zásadách sspr, uživatel **neuvidí,** že e-mail na stránce registrace reset hesla, ale vidí to během postupu resetování hesla.

## <a name="security-questions-and-answers"></a>Bezpečnostní otázky a odpovědi

Bezpečnostní otázky a odpovědi jsou bezpečně uloženy ve vašem tenantovi Azure AD a jsou přístupné jenom uživatelům prostřednictvím [registračního portálu spr.](https://aka.ms/ssprsetup) Správci nemohou zobrazit, nastavit ani upravit obsah otázek a odpovědí jiného uživatele.

## <a name="what-happens-when-a-user-registers"></a>Co se stane, když se uživatel zaregistruje

Když se uživatel zaregistruje, registrační stránka nastaví následující pole:

* **Ověřovací telefon**
* **Ověřovací e-mail**
* **Bezpečnostní otázky a odpovědi**

Pokud jste zadali hodnotu pro **mobilní telefon** nebo **alternativní e-mail**, mohou uživatelé tyto hodnoty okamžitě použít k resetování hesel, a to i v případě, že se do služby nezaregistrovali. Kromě toho uživatelé zobrazit tyto hodnoty při první registraci a mohou je upravit, pokud chtějí. Po úspěšné registraci jsou tyto hodnoty trvalé v polích **Ověřovací telefon** a **Ověřovací e-mail.**

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Nastavení a čtení ověřovacích dat pomocí PowerShellu

Pomocí prostředí PowerShell lze nastavit následující pole:

* **Alternativní e-mail**
* **Mobilní telefon**
* **Office phone**: Lze nastavit pouze v případě, že nesynchronizujete s místním adresářem

### <a name="use-powershell-version-1"></a>Použití PowerShellu verze 1

Chcete-li začít, je třeba [stáhnout a nainstalovat modul Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Po instalaci můžete pomocí následujících kroků nakonfigurovat každé pole.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Nastavení ověřovacích dat pomocí PowerShellu verze 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Čtení ověřovacích dat pomocí PowerShellu verze 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Přečtěte si možnosti Ověřovací telefon a Ověřovací e-mail

Chcete-li při použití powershellu verze 1 číst **ověřovací telefon** a **ověřovací e-mail,** použijte následující příkazy:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Použití PowerShellu verze 2

Chcete-li začít, je třeba [stáhnout a nainstalovat modul Azure AD verze 2 PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Po instalaci můžete pomocí následujících kroků nakonfigurovat každé pole.

Chcete-li rychle nainstalovat z posledních verzí prostředí PowerShell, které podporují instalační modul, spusťte následující příkazy. (První řádek zkontroluje, zda je modul již nainstalován.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Nastavení ověřovacích dat pomocí PowerShellu verze 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Čtení ověřovacích dat pomocí PowerShellu verze 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Další kroky

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco rozbité. Jak lze vyřešit probléms sspr?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globální správci mohou upravit kontaktní údaje uživatele na ověřování."
