---
title: Předem naplnit kontaktní informace pro Samoobslužné resetování hesla – Azure Active Directory
description: Naučte se, jak předem naplnit kontaktní informace uživatelům Azure Active Directory Samoobslužné resetování hesla (SSPR), aby mohli funkci používat bez dokončení procesu registrace.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861149"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Doplňte kontaktní údaje pro ověření uživatele pro Azure Active Directory Samoobslužné resetování hesla (SSPR).

Chcete-li použít Samoobslužné resetování hesla (SSPR) Azure Active Directory (Azure AD), musí být k dispozici kontaktní informace pro ověření uživatele. Některé organizace mají uživatele k registraci ověřovacích dat sami. Jiné organizace upřednostňují synchronizaci z ověřovacích dat, která již existují v Active Directory Domain Services (služba AD DS). Tato synchronizovaná data jsou zpřístupněna pro Azure AD a SSPR bez nutnosti zásahu uživatele. Když uživatelé potřebují změnit nebo resetovat svoje heslo, můžou to udělat, i když předtím zaregistrovali své kontaktní údaje.

Pokud splňujete následující požadavky, můžete předem vyplnit kontaktní údaje pro ověření:

* Data v místním adresáři máte správně naformátovaná.
* Nakonfigurovali jste [Azure AD Connect](../hybrid/how-to-connect-install-express.md) pro vašeho TENANTA Azure AD.

Telefonní čísla musí být ve formátu *+ CountryCode PhoneNumber*, například *+ 1 4251234567*.

> [!NOTE]
> Mezi kódem země a telefonním číslem musí být mezera.
>
> Resetování hesla nepodporuje telefonní linky. I ve formátu *+ 1 4251234567X12345* jsou rozšíření odebrána před tím, než je volání umístěno.

## <a name="fields-populated"></a>Vyplněná pole

Pokud v Azure AD Connect použijete výchozí nastavení, provedou se následující mapování k naplnění kontaktních údajů ověřování pro SSPR:

| Místní služby Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Telefon do kanceláře |
| mobil                       | Mobilní telefon |

Jakmile uživatel ověří své číslo mobilního telefonu, pole *telefon* v části **kontaktní údaje pro ověření** ve službě Azure AD se také vyplní tímto číslem.

## <a name="authentication-contact-info"></a>Kontaktní údaje pro ověření

Na stránce **metody ověřování** pro uživatele Azure AD v Azure Portal může globální správce ručně nastavit kontaktní údaje pro ověření. Můžete si prohlédnout existující metody v části *použitelné metody ověřování* nebo **přidat metody ověřování**, jak je znázorněno na následujícím ukázkovém snímku obrazovky:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Správa metod ověřování z Azure Portal":::

Následující požadavky se vztahují na tyto kontaktní údaje pro ověření:

* Pokud je pole *telefon* vyplněné a v zásadách SSPR je povolený *mobilní telefon* , uživatel uvidí toto číslo na registrační stránce pro resetování hesla a během pracovního postupu pro resetování hesla.
* Pokud je v zásadách SSPR zadáno pole *e-mail* a je povolen *e-mail* , uživatel uvidí tento e-mail na registrační stránce pro resetování hesla a během pracovního postupu pro resetování hesla.

## <a name="security-questions-and-answers"></a>Bezpečnostní otázky a odpovědi

Bezpečnostní otázky a odpovědi jsou bezpečně uložené v tenantovi Azure AD a jsou dostupné jenom uživatelům prostřednictvím [portálu pro registraci SSPR](https://aka.ms/ssprsetup). Správci nemůžou zobrazovat, nastavovat ani upravovat obsah dotazů a odpovědí jiných uživatelů.

## <a name="what-happens-when-a-user-registers"></a>Co se stane, když uživatel zaregistruje

Po registraci uživatele registrační stránka nastaví následující pole:

* **Telefon pro ověření**
* **E-mail pro ověření**
* **Bezpečnostní otázky a odpovědi**

Pokud jste zadali hodnotu pro *mobilní telefon* nebo *alternativní e-mail*, můžou uživatelé tyto hodnoty hned použít k resetování hesel, i když nejsou zaregistrovaní u služby.

Uživatelé tyto hodnoty uvidí i při prvním zápisu a můžou je upravovat, pokud chtějí. Po úspěšné registraci jsou tyto hodnoty trvale uložené v polích *telefon ověřování* a E-mail pro *ověření* v uvedeném pořadí.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Nastavení a čtení ověřovacích dat prostřednictvím PowerShellu

Následující pole lze nastavit pomocí prostředí PowerShell:

* *Alternativní e-mail*
* *Mobilní telefon*
* *Telefon do kanceláře*
    * Dá se nastavit jenom v případě, že neprovádíte synchronizaci s místním adresářem.

> [!IMPORTANT]
> Mezi PowerShellem V1 a PowerShellem v2 je známý nedostatek parity v funkcích příkazu. [Microsoft Graph REST API (beta) pro metody ověřování](/graph/api/resources/authenticationmethods-overview) je aktuální technický fokus pro zajištění moderní interakce.

### <a name="use-powershell-version-1"></a>Použití PowerShellu verze 1

Začněte tím, [že si stáhnete a nainstalujete modul Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). Po instalaci použijte následující postup ke konfiguraci jednotlivých polí.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Nastavení ověřovacích dat s využitím PowerShellu verze 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
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

Začněte tím, [že si stáhnete a nainstalujete modul Azure AD verze 2 PowerShell](/powershell/module/azuread/).

Pokud chcete rychle nainstalovat z nedávných verzí PowerShellu, které podporují `Install-Module` , spusťte následující příkazy. První řádek zkontroluje, zda je modul již nainstalován:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

Po instalaci modulu použijte následující postup ke konfiguraci jednotlivých polí.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Nastavení ověřovacích dat pomocí prostředí PowerShell verze 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
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

Jakmile budou pro uživatele předem vyplněny kontaktní informace pro ověření, proveďte následující kurz, který povolí Samoobslužné resetování hesla:

> [!div class="nextstepaction"]
> [Povolit Samoobslužné resetování hesla služby Azure AD](tutorial-enable-sspr.md)
