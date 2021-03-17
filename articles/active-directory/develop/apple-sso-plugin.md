---
title: Modul plug-in Microsoft Enterprise SSO pro zařízení Apple
titleSuffix: Microsoft identity platform | Azure
description: Přečtěte si informace o modulu plug-in Azure Active Directory SSO Microsoftu pro zařízení s iOS, iPadOS a macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427092"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO – modul plug-in pro zařízení Apple (Preview)

>[!IMPORTANT]
> Tato funkce [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Modul plug-in Microsoft Enterprise SSO pro zařízení Apple* poskytuje jednotné přihlašování (SSO) pro účty Azure Active Directory (Azure AD) v MacOS, iOS a iPadOS napříč všemi aplikacemi, které podporují funkci [podnikového jednotného přihlašování](https://developer.apple.com/documentation/authenticationservices) od společnosti Apple. To zahrnuje starší aplikace, na kterých může vaše firma záviset, ale ještě nepodporují nejnovější knihovny a protokoly identit. Společnost Microsoft úzce spolupracuje s Applem při vývoji tohoto modulu plug-in, aby zvýšila použitelnost vaší aplikace a současně poskytovala nejlepší ochranu, kterou může společnost Apple a Microsoft poskytnout.

Modul plug-in jednotného přihlašování k podnikové síti je aktuálně dostupný jako integrovaná funkce následujících aplikací:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) – iOS, iPadOS
* Microsoft Intune [portál společnosti](/mem/intune/apps/apps-company-portal-macos) – MacOS

## <a name="features"></a>Funkce

Modul plug-in Microsoft Enterprise SSO pro zařízení Apple nabízí tyto výhody:

- Poskytuje jednotné přihlašování pro účty Azure AD napříč všemi aplikacemi, které podporují funkci podnikového jednoho Sign-On společnosti Apple.
- Dá se povolit v jakémkoli řešení správy mobilních zařízení (MDM).
- Rozšiřuje jednotné přihlašování k aplikacím, které ještě nepoužívají knihovny Microsoft Identity Platform.
- Rozšiřuje jednotné přihlašování k aplikacím, které používají OAuth2, OpenID Connect a SAML.

## <a name="requirements"></a>Požadavky

Chcete-li použít modul plug-in jednotného přihlašování Microsoft Enterprise pro zařízení Apple:

- Zařízení musí **podporovat** a mít aplikaci, která obsahuje modul plug-in Microsoft Enterprise SSO pro **nainstalovaná** zařízení Apple:
  - iOS 13.0 +: [aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 +: [aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 +: [aplikace Portál společnosti Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Zařízení musí být **zaregistrované v MDM** (například pomocí Microsoft Intune).
- Aby bylo možné na zařízení povolit modul plug-in jednotného přihlašování (SSO), musí být konfigurace **na zařízení vložena** . Toto omezení zabezpečení je vyžadováno společností Apple.

### <a name="ios-requirements"></a>požadavky na iOS:
- v zařízení musí být nainstalovaná iOS 13,0 nebo vyšší.
- V zařízení musí být nainstalovaná aplikace Microsoftu, která poskytuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Pro Public Preview jsou tyto aplikace [Microsoft Authenticator aplikaci](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>požadavky na macOS:
- v zařízení musí být nainstalovaný macOS 10,15 nebo vyšší. 
- V zařízení musí být nainstalovaná aplikace Microsoftu, která poskytuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Pro Public Preview tyto aplikace zahrnují [aplikaci Portál společnosti Intune](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Povolení modulu plug-in jednotného přihlašování se správou mobilních zařízení (MDM)

### <a name="microsoft-intune-configuration"></a>Konfigurace Microsoft Intune

Pokud jako službu MDM používáte Microsoft Intune, můžete k povolení modulu plug-in Microsoft Enterprise SSO použít předdefinované nastavení konfiguračního profilu.

Nejdřív nakonfigurujte nastavení [rozšíření aplikace jednotného přihlašování](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) pro konfigurační profil a [přiřaďte ho ke skupině uživatelů nebo zařízení](/mem/intune/configuration/device-profile-assign) (Pokud ještě není přiřazená).

Nastavení profilu, které povoluje modul plug-in jednotného přihlašování, se automaticky aplikuje na zařízení skupiny při příštím ověření zařízení v Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Ruční konfigurace pro jiné služby MDM

Pokud nepoužíváte Microsoft Intune pro správu mobilních zařízení, použijte následující parametry ke konfiguraci modulu plug-in Microsoft Enterprise SSO pro zařízení Apple.

#### <a name="ios-settings"></a>nastavení iOS:

- **ID rozšíření**: `com.microsoft.azureauthenticator.ssoextension`
- **ID týmu**: (Toto pole není pro iOS potřeba)

#### <a name="macos-settings"></a>macOS nastavení:

- **ID rozšíření**: `com.microsoft.CompanyPortalMac.ssoextension`
- **ID týmu**: `UBF8T346G9`

#### <a name="common-settings"></a>Společná nastavení:

- **Typ**: přesměrování
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Další možnosti konfigurace
Další možnosti konfigurace je možné přidat pro rozšiřování funkcí jednotného přihlašování do dalších aplikací.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Povolení jednotného přihlašování pro aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library

Modul plug-in jednotného přihlašování umožňuje libovolné aplikaci zapojit se do jednotného přihlašování i v případě, že nebyla vyvinuta pomocí sady Microsoft SDK, jako je například knihovna Microsoft Authentication Library (MSAL).

Modul plug-in jednotného přihlašování se instaluje automaticky pomocí zařízení, která stáhly Microsoft Authenticator aplikaci v aplikacích pro iOS a iPadOS nebo Portál společnosti Intune v macOS a zaregistrovaly zařízení ve vaší organizaci. Vaše organizace nejspíš používá ověřovací aplikaci ještě dnes pro scénáře, jako je Multi-Factor Authentication, ověřování bez hesla a podmíněný přístup. Dá se zapnout pro vaše aplikace pomocí libovolného poskytovatele MDM, přestože Microsoft usnadňuje konfiguraci v Microsoft Endpoint Manageru Intune. Seznam povolených aplikací slouží ke konfiguraci těchto aplikací pro použití modulu plugin jednotného přihlašování.

>[!IMPORTANT]
> Podporují se jenom aplikace, které používají nativní síťové technologie a webzobrazení společnosti Apple. Pokud aplikace doplní svoji vlastní implementaci síťové vrstvy, modul plug-in Microsoft Enterprise SSO není podporován.  

Pomocí následujících parametrů nakonfigurujte modul plug-in Microsoft Enterprise SSO pro aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library:

Pokud chcete zadat seznam konkrétních aplikací:

- **Klíč**: `AppAllowList`
- **Zadejte**: `String`
- **Hodnota**: seznam identifikátorů aplikačních sad oddělených čárkami, které se můžou zúčastnit jednotného přihlašování
- **Příklad**: `com.contoso.workapp, com.contoso.travelapp`

Nebo pokud chcete zadat seznam předpon:
- **Klíč**: `AppPrefixAllowList`
- **Zadejte**: `String`
- **Hodnota**: čárkami oddělený seznam předpon ID aplikačního kompletu pro aplikace, které můžou být součástí jednotného přihlašování. Všimněte si, že to umožní, aby se všechny aplikace počínaje konkrétní předponou účastnily jednotného přihlašování.
- **Příklad**: `com.contoso., com.fabrikam.`

[Přijaté aplikace](./application-consent-experience.md) , které může správce MDM zapojit do programu jednotného přihlašování, můžou pro koncového uživatele tiše získat token. Proto je důležité přidat pouze důvěryhodné aplikace do seznamu povolených aplikací. 

>[!NOTE]
> Do tohoto seznamu nemusíte přidávat aplikace, které používají MSAL nebo ASWebAuthenticationSession. Tyto aplikace jsou ve výchozím nastavení povolené. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>Jak zjišťovat identifikátory sady prostředků aplikace na zařízeních s iOS

Apple neposkytuje snadný způsob, jak zjistit ID sad z App Storu. Nejjednodušší způsob, jak zjistit ID sad pro aplikace, které chcete použít pro jednotné přihlašování, je požádat dodavatele nebo vývojáře aplikace. Pokud tato možnost není k dispozici, můžete k zjištění ID sady prostředků použít konfiguraci MDM. 

V konfiguraci MDM dočasně Povolte následující příznak:

- **Klíč**: `admin_debug_mode_enabled`
- **Zadejte**: `Integer`
- **Hodnota**: 1 nebo 0

Když se tento příznak přihlašuje k aplikacím iOS na zařízení, které chcete znát s ID sady pro. Pak otevřete Microsoft Authenticator App-> Help-> Odeslat protokoly – > zobrazit protokoly. 

V souboru protokolu vyhledejte následující řádek:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

To by mělo zachytávání všech identifikátorů aplikačních prostředků viditelných pro rozšíření jednotného přihlašování. Pomocí těchto identifikátorů pak můžete nakonfigurovat jednotné přihlašování pro tyto aplikace. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Povolí uživatelům přihlášení z neznámých aplikací a prohlížeče Safari.

Ve výchozím nastavení modul plug-in Microsoft Enterprise SSO pro autorizované aplikace poskytuje jednotné přihlašování (SSO), jenom když se uživatel přihlásí z aplikace, která používá knihovnu Microsoft Identity Platform Library, jako je ADAL nebo MSAL. Modul plug-in Microsoft Enterprise SSO může také získat sdílené přihlašovací údaje, pokud je zavolá jiná aplikace, která při získání nového tokenu používá knihovnu Microsoft Identity Platform Library.

`browser_sso_interaction_enabled`Možnost povolit příznak umožňuje aplikaci, která nepoužívá knihovnu Microsoft Identity Platform Library, k provádění počátečního zavedení a získání sdíleného přihlašovacího údaje. Také umožňuje prohlížeči Safari provádět počáteční zavedení a získat sdílené přihlašovací údaje. Pokud modul plug-in jednotného přihlašování (SSO) Microsoft Enterprise ještě nemá sdílené přihlašovací údaje, pokusí se ho získat pokaždé, když se na adrese URL služby Azure AD v prohlížeči Safari, ASWebAuthenticationSession, SafariViewController nebo jiné povolené nativní aplikaci žádá o přihlášení.  

- **Klíč**: `browser_sso_interaction_enabled`
- **Zadejte**: `Integer`
- **Hodnota**: 1 nebo 0

MacOS toto nastavení vyžaduje, aby bylo možné dosáhnout jednotnějšího prostředí napříč všemi aplikacemi. Pro iOS a iPadOS toto nastavení není nutné, protože většina aplikací používá aplikaci Microsoft Authenticator k přihlášení. Pokud ale máte některé aplikace, které nepoužívají Microsoft Authenticator v iOS nebo iPadOS, tento příznak vylepší prostředí, takže doporučujeme toto nastavení Povolit. Ve výchozím nastavení je zakázaná.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Zakázat dotazování na vícefaktorové ověřování při počátečním zavádění

Ve výchozím nastavení se modul plug-in Microsoft Enterprise SSO – při provádění počátečního spouštění a získávání sdílených přihlašovacích údajů vždy vyzve uživatele k ověřování pomocí vícefaktorového ověřování (MFA), a to i v případě, že není potřeba pro aktuální aplikaci, kterou uživatel spustil. To znamená, že sdílené přihlašovací údaje je možné snadno používat ve všech dalších aplikacích bez výzvy k zadání uživatele v případě, že MFA se později bude vyžadovat. Tím se zkrátí doba, po kterou uživatel musí být vyzván na zařízení a je to obecně dobré rozhodnutí.

Povolení `browser_sso_disable_mfa` se vypne a zobrazí se uživateli výzva jenom v případě, že aplikace nebo prostředek vyžaduje MFA. 

- **Klíč**: `browser_sso_disable_mfa`
- **Zadejte**: `Integer`
- **Hodnota**: 1 nebo 0

Doporučujeme ponechat tento příznak zakázaný, protože zkracuje dobu, po kterou se uživatel musí na zařízení dotazovat. Pokud vaše organizace zřídka používá MFA, možná budete chtít příznak povolit, ale doporučujeme místo toho použít vícefaktorové ověřování. Z tohoto důvodu je ve výchozím nastavení zakázáno.

#### <a name="disable-oauth2-application-prompts"></a>Zakázat výzvy k použití aplikace OAuth2

Modul plug-in Microsoft Enterprise SSO poskytuje jednotné přihlašování pomocí připojení sdílených přihlašovacích údajů k síťovým požadavkům přicházejících z povolených aplikací. Některé aplikace OAuth2 ale můžou nesprávně vymáhat výzvy pro koncové uživatele na vrstvě protokolu. Pokud se to děje, uvidíte, že se sdílené přihlašovací údaje pro tyto aplikace ignorují a uživatel se zobrazí výzva, abyste se přihlásili, i když modul plug-in Microsoft Enterprise SSO funguje pro jiné aplikace.  

Povolením `disable_explicit_app_prompt` příznaku se omezuje schopnost nativních i webových aplikací, aby se vynutila výzva koncovým uživatelům na vrstvu protokolu a vynechá jednotné přihlašování.

- **Klíč**: `disable_explicit_app_prompt`
- **Zadejte**: `Integer`
- **Hodnota**: 1 nebo 0

Doporučujeme povolit tento příznak pro zajištění jednotnějšího prostředí napříč všemi aplikacemi. Ve výchozím nastavení je zakázaná. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Povolení jednotného přihlašování prostřednictvím souborů cookie pro konkrétní aplikaci

S příponou jednotného přihlašování může být nekompatibilní malý počet aplikací. Konkrétně u aplikací, které mají Pokročilá nastavení sítě, může dojít k neočekávaným problémům, pokud jsou povolené pro jednotné přihlašování (např. se může zobrazit chyba, že se síťová žádost stala zrušená nebo přerušená). 

Pokud máte potíže s přihlášením pomocí metody popsané v `Enable SSO for apps that don't use MSAL` části, můžete zkusit pro tyto aplikace alternativní konfiguraci. 

Ke konfiguraci modulu plug-in Microsoft Enterprise SSO pro tyto konkrétní aplikace použijte následující parametry:

- **Klíč**: `AppCookieSSOAllowList`
- **Zadejte**: `String`
- **Hodnota**: čárkami oddělený seznam předpon ID aplikačního kompletu pro aplikace, které můžou být součástí jednotného přihlašování. Všimněte si, že to umožní, aby se všechny aplikace počínaje konkrétní předponou účastnily jednotného přihlašování.
- **Příklad**: `com.contoso.myapp1, com.fabrikam.myapp2`

Všimněte si, že aplikace, které jsou povolené pro jednotné přihlašování pomocí tohoto mechanismu, je potřeba přidat do `AppCookieSSOAllowList` a `AppPrefixAllowList` .

Tuto možnost doporučujeme vyzkoušet jenom u aplikací, u kterých dochází k neočekávaným chybám při přihlašování. 

#### <a name="use-intune-for-simplified-configuration"></a>Použití Intune pro zjednodušenou konfiguraci

Jak je uvedeno dříve, můžete jako službu MDM použít Microsoft Intune k usnadnění konfigurace modulu plug-in Microsoft Enterprise SSO, včetně povolení modulu plug-in a přidání starších aplikací do seznamu povolených aplikací, aby získali jednotné přihlašování. Další informace najdete v dokumentaci ke [konfiguraci Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Použití modulu plug-in jednotného přihlašování ve vaší aplikaci

[Knihovna Microsoft Authentication Library (MSAL) pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a vyšší podporuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Je doporučený způsob, jak přidat podporu pro modul plug-in Microsoft Enterprise SSO a zajišťuje, abyste získali všechny možnosti platformy Microsoft Identity Platform.

Pokud vytváříte aplikaci pro pracovní postupy prvotní, přečtěte si téma [režim sdíleného zařízení pro zařízení s iOS](msal-ios-shared-devices.md) , kde najdete další nastavení této funkce.

## <a name="how-the-sso-plug-in-works"></a>Jak funguje modul plug-in jednotného přihlašování

Modul plug-in Microsoft Enterprise SSO se spoléhá na [platformu Apple Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Zprostředkovatelé identity, kteří docházejí do architektury, můžou zachytit síťový provoz pro své domény a rozšířit nebo změnit způsob zpracování těchto požadavků. Například modul plug-in jednotného přihlašování může zobrazit další uživatelské rozhraní pro bezpečné shromažďování přihlašovacích údajů pro koncové uživatele, vyžadovat MFA nebo tiše poskytnout aplikacím tokeny.

Nativní aplikace můžou také implementovat vlastní operace a komunikovat přímo s modulem plug-in jednotného přihlašování.
V tomto [videu s 2019 WWDC](https://developer.apple.com/videos/play/tech-talks/301/) se můžete dozvědět víc o architektuře jednotného přihlašování od společnosti Apple.

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Aplikace, které používají knihovnu Microsoft Identity Platform Library

[Knihovna Microsoft Authentication Library (MSAL) pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a vyšší podporuje pro nativně pracovní a školní účty modul plug-in Microsoft Enterprise SSO pro zařízení Apple. 

Pokud jste postupovali podle [všech doporučených kroků](./quickstart-v2-ios.md) a použili jste výchozí [formát URI pro přesměrování](./redirect-uris-ios.md), není potřeba žádná zvláštní konfigurace. Při spuštění na zařízení, které má modul plug-in jednotného přihlašování, MSAL ho automaticky vyvolá pro všechny interaktivní a bezobslužné požadavky na tokeny a taky na operace vyčíslení účtů a odebrání účtu. Vzhledem k tomu, že MSAL implementuje nativní protokol plug-in jednotného přihlašování, který spoléhá na vlastní operace, tato instalační program poskytuje koncovému uživateli nejplynulejšího nativního prostředí. 

Pokud není v MDM povolený modul plug-in jednotného přihlašování, ale aplikace Microsoft Authenticator se nachází na zařízení, místo toho se použije aplikace Microsoft Authenticator pro všechny interaktivní žádosti o tokeny. Modul plug-in jednotného přihlašování sdílí jednotné přihlašování k aplikaci Microsoft Authenticator.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library

Aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library, jako je MSAL, můžou stále získat jednotné přihlašování, pokud je Správce přidá do seznamu povolených aplikací explicitně. 

V těchto aplikacích nejsou potřeba žádné změny kódu, pokud jsou splněné následující podmínky:

- Aplikace používá rozhraní Apple pro provádění síťových požadavků (například [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)). 
- Aplikace používá ke komunikaci se službou Azure AD standardní protokoly (například OAuth2, SAML, WS-Federation).
- Aplikace neshromažďuje uživatelské jméno a heslo ve formátu prostého textu v nativním uživatelském rozhraní.

V takovém případě je k dispozici jednotné přihlašování, když aplikace vytvoří síťový požadavek a otevře webový prohlížeč, ve kterém se uživatel přihlásí. Když se uživatel přesměruje na přihlašovací adresu URL služby Azure AD, modul plug-in pro jednotné přihlašování ověří adresu URL a zkontroluje, jestli jsou pro tuto adresu URL k dispozici přihlašovací údaje jednotného přihlašování. Pokud existuje, modul plug-in jednotného přihlašování předá přihlašovací údaje jednotného přihlašování do služby Azure AD, což aplikaci autorizuje k dokončení požadavku sítě bez nutnosti požádat uživatele, aby zadal svoje přihlašovací údaje. Pokud je navíc zařízení ve službě Azure AD známé, modul plug-in jednotného přihlašování také předá certifikát zařízení, aby splnil kontrolu podmíněného přístupu podle zařízení. 

Pro podporu jednotného přihlašování pro aplikace, které nejsou MSAL, modul plug-in jednotného přihlašování implementuje protokol podobný modulu plug-in prohlížeče Windows, který je popsaný v tématu [co je primární obnovovací token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

V porovnání s MSAL aplikacemi Modul plug-in jednotného přihlašování funguje transparentně pro aplikace, které nejsou MSAL, integrací s existujícím přihlašovacím prostředím prohlížeče, které poskytují aplikace. Koncový uživatel uvidí své známé zkušenosti s tím, že v každé aplikaci nebude nutné provádět další přihlášení. Například namísto zobrazení nástroje pro výběr nativního účtu přidává modul plug-in jednotného přihlašování relace jednotného přihlašování k prostředí pro výběr účtu na základě webu. 

## <a name="next-steps"></a>Další kroky

Další informace o režimu sdíleného zařízení v iOS najdete v tématu [režim sdíleného zařízení pro zařízení s iOS](msal-ios-shared-devices.md).
