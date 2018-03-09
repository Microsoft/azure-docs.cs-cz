---
title: "Azure AD Connect: Bezproblémové jednotné přihlašování – nejčastější dotazy | Microsoft Docs"
description: "Odpovědi na časté otázky o Azure Active Directory bezproblémové jednotné přihlašování."
services: active-directory
keywords: "Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: 15155ecaf17ae309a218bb1f51a4757e5338f64c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory bezproblémové jednotné přihlašování: Nejčastější dotazy

V tomto článku jsme adres často kladené otázky o Azure Active Directory bezproblémové jednotné přihlašování (SSO bezproblémové). Kontrolovat zpět pro nový obsah.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>S jaké metody přihlašování fungují bezproblémové jednotné přihlašování?

Bezproblémové jednotného přihlašování je možné kombinovat s buď [synchronizaci hodnoty Hash hesla](active-directory-aadconnectsync-implement-password-synchronization.md) nebo [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md) metody přihlašování. Tato funkce ale nelze použít s Active Directory Federation Services (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Je bezproblémové jednotného přihlašování k bezplatné funkce?

Bezproblémové jednotného přihlašování je bezplatné funkce a nepotřebujete žádné placené edice Azure AD pro použití.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Je k dispozici v bezproblémové jednotného přihlašování [cloudu Microsoft Azure v Německu](http://www.microsoft.de/cloud-deutschland) a [cloudu Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Ne. Bezproblémové jednotného přihlašování je k dispozici pouze v celosvětové instance služby Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Jaké aplikace využívat `domain_hint` nebo `login_hint` parametr funkce bezproblémové přihlašování?

Níže uvedené je doplňovat seznam aplikací, které tyto parametry odeslat do služby Azure AD a proto poskytuje uživatelům tichou prostředí přihlašování pomocí jednotného přihlašování bezproblémové:

| Název aplikace | Adresa URL aplikace má být použit |
| -- | -- |
| Přístupový panel | myapps.microsoft.com/contoso.com |
| Outlook na webu | outlook.office365.com/contoso.com |

V tabulce nahraďte název domény pro zajištění adresy URL správné aplikace pro vašeho klienta "contoso.com".

Pokud máte jiné aplikace, které vás zajímají, dejte nám vědět v sekci komentáře.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Podporuje bezproblémové SSO `Alternate ID` jako uživatelské jméno, místo `userPrincipalName`?

Ano. Bezproblémové jednotného přihlašování podporuje `Alternate ID` jako uživatelské jméno při konfiguraci ve službě Azure AD Connect, jak je znázorněno [zde](active-directory-aadconnect-get-started-custom.md). Ne všechny aplikace Office 365 podporují `Alternate ID`. Vyhledejte konkrétní aplikaci dokumentaci pro příkaz podpory.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Co je rozdíl mezi jeden přihlašování poskytovaný [Azure AD Join](../active-directory-azureadjoin-overview.md) a bezproblémové jednotné přihlašování?

[Azure AD Join](../active-directory-azureadjoin-overview.md) poskytuje jednotné přihlašování pro uživatele, pokud jejich zařízení jsou zaregistrovaná službou Azure AD. Tato zařízení nemusí nutně být připojený k doméně. Jednotné přihlašování je prováděno pomocí *primární obnovovacích tokenů* nebo *PRTs*a ne protokolu Kerberos. Činnost koncového uživatele je optimální na zařízení s Windows 10. Jednotné přihlašování se automaticky stane na prohlížeč Microsoft Edge. Taky funguje na Chrome s použitím rozšíření prohlížeče.

Můžete použít Azure AD Join a bezproblémové jednotného přihlašování na vašeho klienta. Tyto dvě funkce jsou vzájemně doplňují. Pokud jsou obě funkce zapnutá, pak jednotného přihlašování z Azure AD Join má přednost před bezproblémové jednotné přihlašování.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Chcete zaregistrovat zařízení s Windows 10 s Azure AD, bez použití služby AD FS. Můžete použít bezproblémové SSO místo?

Ano, tento scénář vyžaduje verze 2.1 nebo novější z [připojení k pracovišti klienta](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Jak lze zahrnout přes dešifrovací klíč protokolu Kerberos `AZUREADSSOACC` účet počítače?

Je důležité často mění dešifrovací klíč protokolu Kerberos `AZUREADSSOACC` účet počítače, (což představuje Azure AD) vytvořené v místní doménové struktuře Active Directory.

>[!IMPORTANT]
>Důrazně doporučujeme, bude možné zahrnout prostřednictvím protokolu Kerberos dešifrovací klíč minimálně každých 30 dnů.

Proveďte tyto kroky na místní server, kde je spuštěn nástroj Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Získání seznamu doménových struktur služby AD, kde bylo povoleno bezproblémové jednotného přihlašování

1. Nejprve stáhnout a nainstalovat [Microsoft Online Services Sign-In Assistant](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Potom stáhněte a nainstalujte [64-bit modulu Azure Active Directory pro prostředí Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importujte modul PowerShell bezproblémové jednotného přihlašování k použití tohoto příkazu: `Import-Module .\AzureADSSO.psd1`.
5. Spusťte prostředí PowerShell jako správce. V prostředí PowerShell, zavolejte `New-AzureADSSOAuthenticationContext`. Tento příkaz musí poskytnout místní okno k zadání přihlašovacích údajů globálního správce vašeho klienta.
6. Volání `Get-AzureADSSOStatus`. Tento příkaz poskytuje seznam doménových struktur AD (podívejte se na seznamu "Domény") na které tato funkce povolená.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Krok 2. Dešifrovací klíč protokolu Kerberos v každé doménové struktuře AD, který ho se ho nastavit na aktualizace

1. Volání `$creds = Get-Credential`. Po zobrazení výzvy zadejte přihlašovací údaje správce domény určený doménové struktuře AD.
2. Volání `Update-AzureADSSOForest -OnPremCredentials $creds`. Tento příkaz aktualizuje dešifrovací klíč protokolu Kerberos pro `AZUREADSSOACC` účet počítače v této konkrétní doménové struktuře AD a aktualizace ve službě Azure AD.
3. Opakujte předchozí kroky pro každou doménovou strukturu AD, který jste nastavili funkci na.

>[!IMPORTANT]
>Ujistěte se, kterou jste _není_ spustit `Update-AzureADSSOForest` příkaz více než jednou. Funkci, jinak hodnota přestane fungovat až do doby, lístky protokolu Kerberos vaši uživatelé vyprší a opakováno pomocí služby Active Directory v místě.

## <a name="how-can-i-disable-seamless-sso"></a>Jak můžete zakázat bezproblémové jednotné přihlašování?

Je možné zakázat bezproblémové jednotného přihlašování pomocí služby Azure AD Connect.

Spusťte Azure AD Connect, zvolte "Změna uživatele přihlašovací stránka" a klikněte na tlačítko "Další". Poté zrušte zaškrtnutí políčka "Povolit jednotné přihlašování na". Postupujte podle pokynů průvodce. Po dokončení průvodce je bezproblémové jednotného přihlašování na váš klient zakázána.

Ale zobrazí se zpráva na obrazovce, který čte následujícím způsobem:

"Jednotné přihlašování je nyní zakázán, ale existují ručně provést další kroky k dokončení vyčištění. Další informace"

Dokončete proces, postupujte takto ruční na místním serveru, kde je spuštěn nástroj Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Získání seznamu doménových struktur služby AD, kde bylo povoleno bezproblémové jednotného přihlašování

1. Nejprve stáhnout a nainstalovat [Microsoft Online Services Sign-In Assistant](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Potom stáhněte a nainstalujte [64-bit modulu Azure Active Directory pro prostředí Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Přejděte do složky `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importujte modul PowerShell bezproblémové jednotného přihlašování k použití tohoto příkazu: `Import-Module .\AzureADSSO.psd1`.
5. Spusťte prostředí PowerShell jako správce. V prostředí PowerShell, zavolejte `New-AzureADSSOAuthenticationContext`. Tento příkaz musí poskytnout místní okno k zadání přihlašovacích údajů globálního správce vašeho klienta.
6. Volání `Get-AzureADSSOStatus`. Tento příkaz poskytuje seznam doménových struktur AD (podívejte se na seznamu "Domény") na které tato funkce povolená.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Krok 2. Ručně odstraňte `AZUREADSSOACCT` účet počítače z každé doménové struktuře AD, které vidíte uvedené.

## <a name="next-steps"></a>Další postup

- [**Rychlý Start** ](active-directory-aadconnect-sso-quick-start.md) – zprovoznění a systémem Azure bezproblémové jednotného přihlašování k AD.
- [**Podrobné technické informace** ](active-directory-aadconnect-sso-how-it-works.md) -pochopit, jak tato funkce funguje.
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-sso.md) – zjistěte, jak řešit obvyklé problémy s funkcí.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro vyplnění žádosti o nové funkce.
