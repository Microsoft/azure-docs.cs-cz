---
title: Požadavky na data SSPR Azure AD – Azure Active Directory
description: Požadavky na data pro Samoobslužné resetování hesla služby Azure AD a způsob jejich navýšení
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1c00d0f4ba365442762df6e041f02ea0a39f099
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847299"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Nasazení resetování hesla bez nutnosti registrace koncového uživatele

Pokud chcete nasadit Samoobslužné resetování hesla (SSPR) Azure Active Directory (Azure AD), musí být k dispozici data ověřování. Některé organizace mají své uživatele, kteří sami vstupují svá ověřovací data. Ale mnoho organizací upřednostňuje synchronizaci s daty, která už ve službě Active Directory existují. Synchronizovaná data jsou k dispozici pro Azure AD a SSPR bez nutnosti zásahu uživatele, pokud:

* Správně naformátujte data v místním adresáři.
* Nakonfigurujte [Azure AD Connect pomocí expresního nastavení](../hybrid/how-to-connect-install-express.md).

Aby telefonní čísla fungovala správně, musí být ve formátu *+ CountryCode PhoneNumber*, například + 1 4255551234.

> [!NOTE]
> Musí existovat mezera mezi kódem země a telefonním číslem.
>
> Resetování hesla nepodporuje telefonní linky. I ve formátu + 1 4255551234X12345 jsou rozšíření odebrána před tím, než je volání umístěno.

## <a name="fields-populated"></a>Vyplněná pole

Pokud použijete výchozí nastavení v Azure AD Connect, provedou se následující mapování:

| Místní služby Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Telefon do kanceláře |
| Mobilní zařízení | Mobilní telefon |

Jakmile uživatel ověří své číslo mobilního telefonu, pole telefon v části kontaktní údaje pro ověření ve službě Azure AD bude také vyplněno tímto číslem.

## <a name="authentication-contact-info"></a>Kontaktní údaje pro ověření

Globální správce může ručně nastavit kontaktní údaje pro ověření pro uživatele, jak je znázorněno na následujícím snímku obrazovky.

![Kontaktní údaje pro ověření uživatele v Azure AD][Contact]

Pokud je pole telefon vyplněné a v zásadách SSPR je povolený mobilní telefon, uživatel uvidí toto číslo na registrační stránce pro resetování hesla a během pracovního postupu pro resetování hesla.

Pro resetování hesla se nepoužívá pole alternativní telefon.

Pokud je v zásadách SSPR zadáno pole E-mail a je povolen E-mail, uživatel uvidí tento e-mail na registrační stránce pro resetování hesla a během pracovního postupu pro resetování hesla.

Pokud je v zásadách SSPR zadáno alternativní pole e-mailu a v zásadách je povolen E-mail, uživatel tento **e-mail neuvidí na** registrační stránce pro resetování hesla, ale uvidí ho během pracovního postupu pro resetování hesla.

## <a name="security-questions-and-answers"></a>Bezpečnostní otázky a odpovědi

Bezpečnostní otázky a odpovědi jsou bezpečně uložené v tenantovi Azure AD a jsou dostupné jenom uživatelům prostřednictvím [portálu pro registraci SSPR](https://aka.ms/ssprsetup). Správci nemůžou zobrazovat, nastavovat ani upravovat obsah dotazů a odpovědí jiných uživatelů.

## <a name="what-happens-when-a-user-registers"></a>Co se stane, když uživatel zaregistruje

Po registraci uživatele registrační stránka nastaví následující pole:

* **Telefon pro ověření**
* **E-mail pro ověření**
* **Bezpečnostní otázky a odpovědi**

Pokud jste zadali hodnotu pro **mobilní telefon** nebo **alternativní e-mail**, můžou uživatelé tyto hodnoty hned použít k resetování hesel, i když nejsou zaregistrovaní u služby. Uživatelé tyto hodnoty uvidí i při prvním zápisu a můžou je upravovat, pokud chtějí. Po úspěšném zaregistrování budou tyto hodnoty trvale uložené v polích **telefon ověřování** a **e-mail pro ověření** v uvedeném pořadí.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Nastavení a čtení ověřovacích dat prostřednictvím PowerShellu

Následující pole lze nastavit pomocí prostředí PowerShell:

* **Alternativní e-mail**
* **Mobilní telefon**
* **Telefon do kanceláře**: dá se nastavit jenom v případě, že neprovádíte synchronizaci s místním adresářem.

### <a name="use-powershell-version-1"></a>Použití PowerShellu verze 1

Abyste mohli začít, musíte [si stáhnout a nainstalovat modul Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Po instalaci nástroje můžete pomocí následujících kroků nakonfigurovat jednotlivá pole.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Nastavení ověřovacích dat s využitím PowerShellu verze 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Přečtěte si data ověřování pomocí PowerShellu verze 1.

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Přečtěte si možnosti e-mailu pro ověřování a telefon pro ověření.

Pokud chcete číst **telefon pro ověřování** a **ověřovací e-mail** , když používáte prostředí PowerShell verze 1, použijte následující příkazy:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Použití prostředí PowerShell verze 2

Abyste mohli začít, musíte [si stáhnout a nainstalovat modul Azure AD verze 2 PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Po instalaci nástroje můžete pomocí následujících kroků nakonfigurovat jednotlivá pole.

Pokud chcete rychle nainstalovat z nedávných verzí PowerShellu, která podporuje instalační modul, spusťte následující příkazy. (První řádek zkontroluje, jestli je modul už nainstalovaný.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Nastavení ověřovacích dat pomocí prostředí PowerShell verze 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Přečtěte si data ověřování pomocí prostředí PowerShell verze 2.

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
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco přerušeno. Návody řešit potíže s SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globální správci můžou upravovat kontaktní údaje pro ověření uživatele."
