---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – nejčastější dotazy | Dokumentace Microsoftu'
description: Odpovědi na nejčastější dotazy o Azure Active Directory bezproblémové jednotné přihlašování.
services: active-directory
keywords: Co je Azure AD Connect, instalace Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8e4cc67af4276bc244d402258a90dfec01d61add
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919015"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory bezproblémové jednotné přihlašování: Nejčastější dotazy

V tomto článku budeme řešit nejčastější dotazy o Azure Active Directory bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování). Kontrolovat zpět pro nový obsah.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>S jaké metody přihlašování fungují bezproblémového jednotného přihlašování?

Bezproblémové jednotné přihlašování je možné kombinovat s buď [synchronizaci hodnot Hash hesel](active-directory-aadconnectsync-implement-password-hash-synchronization.md) nebo [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md) metod přihlašování. Tuto funkci však nelze použít s Active Directory Federation Services (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Bezproblémové jednotné přihlašování je bezplatná funkce?

Bezproblémové jednotné přihlašování je funkci a není nutné žádné placené edice Azure AD, aby ho použít.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Je k dispozici v bezproblémového jednotného přihlašování [cloudu Microsoft Azure Germany](http://www.microsoft.de/cloud-deutschland) a [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Ne. Bezproblémové jednotné přihlašování je k dispozici pouze v celosvětové instance služby Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Jaké aplikace využít `domain_hint` nebo `login_hint` parametr funkce bezproblémového jednotného přihlašování?

Tady je – úplný seznam aplikací, které tyto parametry odeslat do služby Azure AD a proto poskytuje uživatelům bezobslužné možnosti přihlašování pomocí bezproblémového jednotného přihlašování (tj, není nutné pro uživatele k zadání jejich uživatelských jmen):

| Název aplikace | Adresa URL aplikace, který se má použít |
| -- | -- |
| Přístupový panel | myapps.microsoft.com/contoso.com |
| Aplikace Outlook na webu | outlook.office365.com/contoso.com |

Kromě toho, uživatelé získají bezobslužné možnosti přihlašování, když aplikace pošle žádostí o přihlášení k Azure AD bezproblémovým koncové body – to znamená https://login.microsoftonline.com/contoso.com/<..> nebo https://login.microsoftonline.com/<tenant_ID>/<..> – místo pro běžné koncového bodu Azure AD – to znamená, https://login.microsoftonline.com/common/<...>. Tady je – úplný seznam aplikací, které tyto druhy žádostí o přihlášení.

| Název aplikace | Adresa URL aplikace, který se má použít |
| -- | -- |
| SharePoint Online | contoso.sharepoint.com |
| Azure Portal | portal.azure.com/contoso.com |

Ve výše uvedeným tabulkám nahraďte název vaší domény, abyste se dostali adresy URL správné aplikace pro vašeho tenanta "contoso.com".

Pokud chcete dalších aplikací se naše tiché prostředí pro přihlašování, dejte nám vědět v části zpětnou vazbu.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Bezproblémové jednotné přihlašování podporuje `Alternate ID` jako uživatelské jméno, namísto `userPrincipalName`?

Ano. Bezproblémové jednotné přihlašování podporuje `Alternate ID` jako uživatelské jméno při konfiguraci ve službě Azure AD Connect, jak je znázorněno [tady](active-directory-aadconnect-get-started-custom.md). Ne všechny aplikace Office 365 podporují `Alternate ID`. Najdete v dokumentaci pro konkrétní aplikaci pro prohlášení o odborné pomoci.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Jaký je rozdíl mezi jednotné přihlašování poskytuje [Azure AD Join](../active-directory-azureadjoin-overview.md) a bezproblémového jednotného přihlašování?

[Připojení k Azure AD](../active-directory-azureadjoin-overview.md) poskytuje jednotné přihlašování pro uživatele, pokud jejich zařízení jsou zaregistrované s Azure AD. Tato zařízení nemusí nutně být připojené k doméně. Poskytuje jednotné přihlašování pomocí *primární obnovovací tokeny* nebo *PRTs*a ne Kerberos. Činnost koncového uživatele je optimální na zařízeních s Windows 10. Jednotné přihlašování se automaticky stane v prohlížeči Microsoft Edge. Funguje i v Chrome se použití rozšíření prohlížeče.

Ve svém tenantovi můžete použít připojení ke službě Azure AD a bezproblémového jednotného přihlašování. Tyto dvě funkce vzájemně doplňují. Pokud jsou obě funkce zapnutá, pak jednotné přihlašování z Azure AD Join má přednost před bezproblémového jednotného přihlašování.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Chci se zaregistrovat zařízení s Windows 10 v Azure AD, bez použití služby AD FS. Můžu použít bezproblémového jednotného přihlašování místo?

Ano, tento scénář vyžaduje verzi 2.1 nebo novější [klientských připojení k pracovní ploše](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Jak je možné vrátit přes dešifrovací klíč protokolu Kerberos `AZUREADSSOACC` účet počítače?

Je důležité si často nespotřebujete dešifrovací klíč protokolu Kerberos `AZUREADSSOACC` účet počítače, (, který představuje Azure AD) vytvořené v místní doménové struktuře Active Directory.

>[!IMPORTANT]
>Důrazně doporučujeme, aby ho znovu vygenerovat dešifrovací klíče Kerberos nejméně každých 30 dnů.

Proveďte tyto kroky na místním serveru, kde je spuštěn nástroj Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Načíst seznam doménových struktur AD, ve kterém bylo povoleno bezproblémového jednotného přihlašování

1. Nejprve stáhnout a nainstalovat [Microsoft Online Services přihlášení pomocníka](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Pak si stáhnout a nainstalovat [64bitová verze modulu Azure Active Directory pro prostředí Windows PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).
3. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Naimportujte modul Powershellu bezproblémové jednotné přihlašování pomocí tohoto příkazu: `Import-Module .\AzureADSSO.psd1`.
5. Spusťte PowerShell jako správce. V prostředí PowerShell, zavolejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by vám měl dát automaticky otevíraného okna zadejte přihlašovací údaje globálního správce vašeho tenanta.
6. Volání `Get-AzureADSSOStatus`. Tento příkaz poskytuje seznam doménových struktur AD (pohled na seznamu "Domény") na které tato funkce povolená.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Krok 2. Aktualizovat dešifrovací klíč protokolu Kerberos v každé doménové struktuře AD, který ho se ho nastavit na

1. Volání `$creds = Get-Credential`. Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro určené doménové struktuře AD.
2. Volání `Update-AzureADSSOForest -OnPremCredentials $creds`. Tento příkaz aktualizuje dešifrovací klíč protokolu Kerberos pro `AZUREADSSOACC` účet počítače v této konkrétní doménové struktuře AD a aktualizuje ve službě Azure AD.
3. Zopakujte předchozí kroky pro každou doménovou strukturu AD, který jste nastavili tuto funkci na.

>[!IMPORTANT]
>Ujistěte se, které jste _není_ spustit `Update-AzureADSSOForest` příkazu více než jednou. V opačném případě funkci přestane fungovat až do doby vaši uživatelé lístky protokolu Kerberos, jehož platnost nevyprší a se službou Active Directory vaše místní ho znova vydat.

## <a name="how-can-i-disable-seamless-sso"></a>Jak lze zakázat bezproblémového jednotného přihlašování?

Bezproblémové jednotné přihlašování je možné zakázat pomocí služby Azure AD Connect.

Spusťte Azure AD Connect, zvolte "Změna uživatele přihlášení stránka" a klikněte na "Next". Potom zrušte zaškrtnutí možnosti "Povolit jednotné přihlašování". Pokračujte v průvodci. Po dokončení průvodce je bezproblémové jednotné přihlašování zakázáno ve svém tenantovi.

Nicméně zobrazí se zpráva na obrazovce, který čte následujícím způsobem:

"Jednotného přihlašování je teď zakázané, ale existují další kroky za účelem kompletního vyčištění. Další informace"

K dokončení procesu, postupujte takto ruční na místním serveru, kde je spuštěn nástroj Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Načíst seznam doménových struktur AD, ve kterém bylo povoleno bezproblémového jednotného přihlašování

1. Nejprve stáhnout a nainstalovat [Microsoft Online Services přihlášení pomocníka](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Pak si stáhnout a nainstalovat [64bitová verze modulu Azure Active Directory pro prostředí Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Naimportujte modul Powershellu bezproblémové jednotné přihlašování pomocí tohoto příkazu: `Import-Module .\AzureADSSO.psd1`.
5. Spusťte PowerShell jako správce. V prostředí PowerShell, zavolejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by vám měl dát automaticky otevíraného okna zadejte přihlašovací údaje globálního správce vašeho tenanta.
6. Volání `Get-AzureADSSOStatus`. Tento příkaz poskytuje seznam doménových struktur AD (pohled na seznamu "Domény") na které tato funkce povolená.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Krok 2. Odstraňte ručně `AZUREADSSOACCT` účet počítače v každé doménové struktuře AD, které vidíte uvedený.

## <a name="next-steps"></a>Další postup

- [**Rychlý Start** ](active-directory-aadconnect-sso-quick-start.md) – rychle zprovoznit a systémem Azure bezproblémového jednotného přihlašování AD.
- [**Podrobné technické informace** ](active-directory-aadconnect-sso-how-it-works.md) -pochopit, jak tato funkce funguje.
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-sso.md) – zjistěte, jak vyřešit běžné problémy s funkcí.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – k podání žádostí o nové funkce.
