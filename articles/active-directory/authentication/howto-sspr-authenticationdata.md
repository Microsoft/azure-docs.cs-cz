---
title: Požadavky služby Azure AD SSPR dat | Dokumentace Microsoftu
description: Požadavky na data pro hesla pomocí samoobslužné služby Azure AD resetování a postupy jejich splnění
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: eb3c1177f86f4c595280521f4dbcbe8081f68895
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296240"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Nasazení resetování hesla bez vyžadování registrace koncového uživatele

Ověřovací data pro nasazení služby Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR), musí být k dispozici. Některé organizace mají své uživatele zadejte ověřovací data sami. Ale mnoho organizací raději synchronizovat s daty, která již existuje ve službě Active Directory. Synchronizace dat je k dispozici pro Azure AD a samoobslužné resetování HESLA bez nutnosti zásahu uživatele, pokud jste:
   * Správně formátování dat v místním adresáři.
   * Konfigurace [Azure AD Connect s použitím expresního nastavení](../hybrid/how-to-connect-install-express.md).

Pro vše správně fungovalo, musí být telefonní čísla ve formátu *+ CountryCode PhoneNumber*, například 4255551234 + 1.

> [!NOTE]
> Musí být mezera mezi směrové číslo země a telefonní číslo.
>
> Resetování hesla nepodporuje telefonní linky. Dokonce i ve formátu 12345 4255551234 + 1 X se odeberou rozšíření před uvedením volání.

## <a name="fields-populated"></a>Naplní pole

Pokud používáte výchozí nastavení ve službě Azure AD Connect, jsou provedeny následující mapování:

| Místní služby Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Telefon do kanceláře |
| Mobilní zařízení | Mobilní telefon |

Když uživatel zadání čísla mobilního telefonu, pole Telefon pod kontaktní údaje pro ověření ve službě Azure AD také naplní se s tímto číslem.

## <a name="authentication-contact-info"></a>Kontaktní údaje pro ověření

Globální správce, můžete ručně nastavit kontaktní údaje pro ověření pro uživatele, jako je zobrazena na následujícím snímku obrazovky.

![Obraťte se na][Contact]

Pokud je vyplněno pole Telefon a v zásadách samoobslužné resetování HESLA je povoleno mobilní telefon, uživateli se zobrazí, že číslo, na stránce registrace pro resetování hesla a během heslo pracovního postupu pro obnovení.

Pole alternativní telefon se nepoužívá pro resetování hesla.

Pokud je zadáno pole e-mailu a e-mailu je v zásadách samoobslužné resetování HESLA povolené, uživateli se zobrazí, že e-mailu na registrační stránce pro resetování hesla a během heslo pracovního postupu pro obnovení.

Pokud je zadáno pole s alternativní e-mailu a e-mailu je v zásadách samoobslužné resetování HESLA povolené, uživatel uvidí **není** naleznete v tématu, že registrační stránku pro resetování e-mailu na heslo, ale zobrazí se mu během heslo pracovního postupu pro obnovení.

## <a name="security-questions-and-answers"></a>Bezpečnostní otázky a odpovědi

Bezpečnostní otázky a odpovědi jsou bezpečně uložené ve vašem tenantovi Azure AD a jsou přístupné prostřednictvím pouze [portál pro registraci SSPR](https://aka.ms/ssprsetup). Správci nelze zobrazit, nastavit nebo změnit obsah otázek a odpovědí jiného uživatele.

## <a name="what-happens-when-a-user-registers"></a>Co se stane, když se uživatel zaregistruje

Když se uživatel zaregistruje, nastaví na registrační stránku následující pole:

* **Telefon pro ověření**
* **E-mail pro ověření**
* **Bezpečnostní otázky a odpovědi**

Pokud zadáte hodnotu pro **mobilního telefonu** nebo **alternativní e-mailu**, uživatelé můžete okamžitě použít tyto hodnoty resetovat svá hesla, i v případě, že ještě nezaregistrovali pro službu. Kromě toho uživatelé uvidí tyto hodnoty při registraci poprvé a mohou je upravit, aby bylo možné. Po úspěšné registraci, budou tyto hodnoty v jako trvalý **telefon pro ověření** a **E-mail pro ověření** pole, v uvedeném pořadí.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Nastavení a čtení ověřovacích dat prostřednictvím prostředí PowerShell

Následující pole lze nastavit pomocí prostředí PowerShell:

* **Alternativní e-mailu**
* **Mobilní telefon**
* **Telefon do kanceláře**: lze nastavit pouze v případě, že nejsou synchronizaci s místním adresářem

### <a name="use-powershell-version-1"></a>Pomocí prostředí PowerShell verze 1

Chcete-li začít, je potřeba [stáhněte a nainstalujte modul Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Po jeho instalaci, můžete použít následující kroky konfigurace jednotlivých polí.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Nastavení ověření dat pomocí prostředí PowerShell verze 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Čtení ověřovacích dat pomocí prostředí PowerShell verze 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Přečtěte si možnosti Telefon pro ověření a E-mail pro ověření

Ke čtení **telefon pro ověření** a **E-mail pro ověření** při použití prostředí PowerShell verze 1, použijte následující příkazy:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Pomocí prostředí PowerShell verze 2

Chcete-li začít, je potřeba [stáhněte a nainstalujte modul prostředí PowerShell verze 2 služby Azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Po jeho instalaci, můžete použít následující kroky konfigurace jednotlivých polí.

Pokud chcete rychle nainstalovat z nejnovější verze prostředí PowerShell, které podporují Install-Module, spusťte následující příkazy. (První řádek zkontroluje, pokud je již nainstalován modul.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Nastavení ověření dat pomocí prostředí PowerShell verze 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Čtení ověřovacích dat pomocí prostředí PowerShell verze 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globální správci můžou upravit kontaktní údaje pro ověření uživatele"
