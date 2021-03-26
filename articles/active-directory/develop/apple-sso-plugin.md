---
title: Modul plug-in Microsoft Enterprise SSO pro zařízení Apple
titleSuffix: Microsoft identity platform | Azure
description: Seznamte se s modulem plug-in Azure Active Directory SSO pro zařízení s iOS, iPadOS a macOS.
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
ms.openlocfilehash: 05bfcc86c72d9eb393da919035ce198948b943f2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559124"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO – modul plug-in pro zařízení Apple (Preview)

>[!IMPORTANT]
> Tato funkce [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Modul plug-in Microsoft Enterprise SSO pro zařízení Apple* poskytuje jednotné přihlašování (SSO) pro účty Azure Active Directory (Azure AD) v MacOS, iOS a iPadOS napříč všemi aplikacemi, které podporují funkci [podnikového jednotného přihlašování](https://developer.apple.com/documentation/authenticationservices) od společnosti Apple. Modul plug-in poskytuje jednotné přihlašování pro sudé staré aplikace, na kterých může být vaše podnikání závislé, ale ještě nepodporují nejnovější knihovny a protokoly identit. Společnost Microsoft úzce spolupracuje s Applem při vývoji tohoto modulu plug-in, aby zvýšila použitelnost vaší aplikace a současně poskytovala nejlepší dostupnou ochranu.

Modul plug-in jednotného přihlašování (SSO) Enterprise je v současnosti integrovaná funkce následujících aplikací:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, iPadOS
* Microsoft Intune [portál společnosti](/mem/intune/apps/apps-company-portal-macos): MacOS

## <a name="features"></a>Funkce

Modul plug-in Microsoft Enterprise SSO pro zařízení Apple nabízí tyto výhody:

- Poskytuje jednotné přihlašování pro účty Azure AD ve všech aplikacích, které podporují funkci Apple Enterprise SSO.
- Dá se povolit pro jakékoli řešení správy mobilních zařízení (MDM).
- Rozšiřuje jednotné přihlašování do aplikací, které ještě nepoužívají knihovny Microsoft Identity Platform Library.
- Rozšiřuje jednotné přihlašování k aplikacím, které používají OAuth 2, OpenID Connect a SAML.

## <a name="requirements"></a>Požadavky

Chcete-li použít modul plug-in Microsoft Enterprise SSO pro zařízení Apple:

- Zařízení musí *podporovat* a mít nainstalovanou aplikaci, která má modul plug-in Microsoft Enterprise SSO pro zařízení Apple:
  - iOS 13,0 a novější: [Microsoft Authenticator aplikace](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13,0 a novější: [aplikace Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 a novější: [aplikace Portál společnosti Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Zařízení musí být *zaregistrované v MDM*, například prostřednictvím Microsoft Intune.
- Aby bylo možné povolit modul plug-in jednotného přihlašování (SSO), musí být *do zařízení vložena* konfigurace. Apple vyžaduje toto omezení zabezpečení.

### <a name="ios-requirements"></a>požadavky na iOS:
- v zařízení musí být nainstalovaná iOS 13,0 nebo vyšší.
- V zařízení musí být nainstalovaná aplikace Microsoftu, která poskytuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Pro Public Preview jsou tyto aplikace [Microsoft Authenticator aplikaci](/azure/active-directory/user-help/user-help-auth-app-overview).


### <a name="macos-requirements"></a>požadavky na macOS:
- v zařízení musí být nainstalovaný macOS 10,15 nebo vyšší. 
- V zařízení musí být nainstalovaná aplikace Microsoftu, která poskytuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Pro Public Preview tyto aplikace zahrnují [aplikaci Portál společnosti Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).

## <a name="enable-the-sso-plug-in"></a>Povolení modulu plug-in jednotného přihlašování

Použijte následující informace, chcete-li povolit modul plug-in jednotného přihlašování pomocí MDM.
### <a name="microsoft-intune-configuration"></a>Konfigurace Microsoft Intune

Pokud jako službu MDM používáte Microsoft Intune, můžete k povolení modulu plug-in Microsoft Enterprise SSO použít předdefinované nastavení konfiguračního profilu:

1. Nakonfigurujte nastavení [rozšíření aplikace jednotného přihlašování](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) pro konfigurační profil. 
1. Pokud profil ještě není přiřazený, [přiřaďte ho ke skupině uživatelů nebo zařízení](/mem/intune/configuration/device-profile-assign).

Nastavení profilu, které povoluje modul plug-in jednotného přihlašování, se automaticky aplikuje na zařízení skupiny při příštím ověření zařízení v Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Ruční konfigurace pro jiné služby MDM

Pokud nepoužíváte Intune pro MDM, nakonfigurujte pomocí následujících parametrů modul plug-in Microsoft Enterprise SSO pro zařízení Apple.

nastavení iOS:

- **ID rozšíření**: `com.microsoft.azureauthenticator.ssoextension`
- **ID týmu**: Toto pole není pro iOS nutné.

macOS nastavení:

- **ID rozšíření**: `com.microsoft.CompanyPortalMac.ssoextension`
- **ID týmu**: `UBF8T346G9`

Společná nastavení:

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
  
### <a name="more-configuration-options"></a>Další možnosti konfigurace
Můžete přidat další možnosti konfigurace pro rozšiřování funkcí jednotného přihlašování na jiné aplikace.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Povolení jednotného přihlašování pro aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library

Modul plug-in jednotného přihlašování umožňuje všem aplikacím přispívat do jednotného přihlašování i v případě, že nebyla vyvinutá pomocí sady Microsoft SDK, jako je Microsoft Authentication Library (MSAL).

Modul plug-in jednotného přihlašování se instaluje automaticky podle zařízení, která mají:
* Stáhli jste aplikaci ověřovatele v systému iOS nebo iPadOS nebo jste stáhli aplikaci Portál společnosti Intune v macOS.
* Zaregistrovali jste svoje zařízení ve vaší organizaci. 

Vaše organizace nejspíš používá ověřovací aplikaci pro scénáře, jako je vícefaktorové ověřování (MFA), ověřování bez hesla a podmíněný přístup. Pomocí poskytovatele MDM můžete zapnout modul plug-in jednotného přihlašování pro vaše aplikace. Microsoft usnadňuje konfiguraci modulu plug-in v rámci Microsoft Endpoint Manageru v Intune. Povolených se používá ke konfiguraci těchto aplikací pro použití modulu plugin jednotného přihlašování.

>[!IMPORTANT]
> Modul plug-in jednotného přihlašování Microsoft Enterprise podporuje jenom aplikace, které používají nativní síťové technologie a webzobrazení společnosti Apple. Nepodporuje aplikace, které dodávají vlastní implementaci síťové vrstvy.  

Pomocí následujících parametrů nakonfigurujte modul plug-in Microsoft Enterprise SSO pro aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library.

Chcete-li poskytnout seznam konkrétních aplikací, použijte tyto parametry:

- **Klíč**: `AppAllowList`
- **Zadejte**: `String`
- **Hodnota**: seznam identifikátorů aplikačních sad oddělených čárkami, které se můžou účastnit jednotného přihlašování (SSO).
- **Příklad**: `com.contoso.workapp, com.contoso.travelapp`

Chcete-li zadat seznam předpon, použijte tyto parametry:
- **Klíč**: `AppPrefixAllowList`
- **Zadejte**: `String`
- **Hodnota**: čárkami oddělený seznam předpon ID aplikačního kompletu pro aplikace, které můžou být součástí jednotného přihlašování. Tento parametr umožňuje, aby se všechny aplikace, které začínají určitou předponou, účastnily jednotného přihlašování.
- **Příklad**: `com.contoso., com.fabrikam.`

[Přijaté aplikace](./application-consent-experience.md) , které může správce MDM zúčastnit v rámci jednotného přihlašování, můžou tiše získat token pro koncového uživatele. Přidejte pouze důvěryhodné aplikace do povolených. 

>[!NOTE]
> Nemusíte přidávat aplikace, které používají MSAL nebo ASWebAuthenticationSession, do seznamu aplikací, které se můžou zúčastnit jednotného přihlašování. Tyto aplikace jsou ve výchozím nastavení povolené. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Vyhledání identifikátorů sady prostředků aplikace na zařízeních s iOS

Apple neposkytuje žádný snadný způsob, jak získat ID sad z App Storu. Nejjednodušší způsob, jak získat ID sady pro aplikace, které chcete použít pro jednotné přihlašování, je požádat dodavatele nebo vývojáře aplikace. Pokud tato možnost není k dispozici, můžete k vyhledání ID sady prostředků použít konfiguraci MDM: 

1. V konfiguraci MDM dočasně Povolte následující příznak:

    - **Klíč**: `admin_debug_mode_enabled`
    - **Zadejte**: `Integer`
    - **Hodnota**: 1 nebo 0
1. Pokud je tento příznak zapnutý, přihlaste se k aplikacím iOS na zařízení, pro které chcete znát ID sady. 
1. V ověřovací aplikaci vyberte **help**  >  **Odeslat protokoly**  >  **Zobrazit protokoly**. 
1. V souboru protokolu vyhledejte následující řádek: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Tento řádek by měl zachytit všechna ID aplikačních prostředků, která jsou viditelná pro rozšíření jednotného přihlašování. 

K nakonfigurování jednotného přihlašování pro aplikace použijte ID sady prostředků. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Umožněte uživatelům, aby se přihlásili z neznámých aplikací a prohlížeče Safari.

Ve výchozím nastavení poskytuje modul plug-in Microsoft Enterprise SSO jednotné přihlašování pro autorizované aplikace jenom v případě, že se uživatel přihlásil z aplikace, která používá knihovnu Microsoft Identity Platform Library, jako je MSAL nebo knihovna ADAL (Azure Active Directory Authentication Library). Modul plug-in Microsoft Enterprise SSO může také získat sdílené přihlašovací údaje, pokud je zavolá jiná aplikace, která při získání nového tokenu používá knihovnu Microsoft Identity Platform Library.

Když příznak povolíte `browser_sso_interaction_enabled` , aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library, můžou provádět počáteční zavedení a získat sdílené přihlašovací údaje. V prohlížeči Safari můžete také provádět počáteční zavádění a získat sdílené přihlašovací údaje. 

Pokud modul plug-in jednotného přihlašování (SSO) Microsoft Enterprise ještě nemá sdílené přihlašovací údaje, pokusí se ho získat pokaždé, když se na adrese URL Azure AD v prohlížeči Safari, ASWebAuthenticationSession, SafariViewController nebo jiné povolené nativní aplikaci přihlásí žádost o přihlášení. 

Pomocí těchto parametrů povolte příznak: 

- **Klíč**: `browser_sso_interaction_enabled`
- **Zadejte**: `Integer`
- **Hodnota**: 1 nebo 0

macOS toto nastavení vyžaduje, aby bylo možné zajistit konzistentní prostředí napříč všemi aplikacemi. iOS a iPadOS toto nastavení nevyžadují, protože většina aplikací používá ověřovací aplikaci pro přihlášení. Doporučujeme ale toto nastavení povolit, protože pokud některé aplikace nepoužívají ověřovací aplikaci v iOS nebo iPadOS, tento příznak vylepší prostředí. Toto nastavení je ve výchozím nastavení zakázáno.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Zakázat dotazování vícefaktorového ověřování při počátečním spouštění

Ve výchozím nastavení se při počátečním spouštění a při získávání přihlašovacích údajů vždycky uživateli zobrazí výzva k ověření MFA v Microsoft Enterprise SSO. Uživateli se zobrazí výzva k ověřování MFA, i když to není vyžadováno pro aplikaci, kterou uživatel otevřel. Toto chování umožňuje snadné použití sdíleného pověření ve všech ostatních aplikacích bez nutnosti zobrazit uživatele v případě, že je později vyžadováno MFA. Vzhledem k tomu, že uživatel získá méně výzev, je toto nastavení obecně dobrým rozhodnutím.

Povolení `browser_sso_disable_mfa` vypne MFA během počátečního spouštění a při získávání sdíleného přihlašovacího údaje. V takovém případě se uživateli zobrazí výzva jenom v případě, že aplikace nebo prostředek vyžaduje MFA. 

Chcete-li povolit příznak, použijte tyto parametry:

- **Klíč**: `browser_sso_disable_mfa`
- **Zadejte**: `Integer`
- **Hodnota**: 1 nebo 0

Doporučujeme ponechat tento příznak zakázaný, protože snižuje počet pokusů, kolikrát se uživatel vyzve k přihlášení. Pokud vaše organizace zřídka používá MFA, možná budete chtít příznak povolit. Doporučujeme místo toho použít vícefaktorové ověřování (MFA). Z tohoto důvodu je příznak ve výchozím nastavení zakázán.

#### <a name="disable-oauth-2-application-prompts"></a>Zakázat výzvy k použití aplikace OAuth 2

Modul plug-in Microsoft Enterprise SSO poskytuje jednotné přihlašování připojením sdílených přihlašovacích údajů k požadavkům sítě, které pocházejí z povolených aplikací. Některé aplikace OAuth 2 ale můžou nesprávně vymáhat výzvy pro koncové uživatele na vrstvě protokolu. Pokud se tento problém zobrazí, uvidíte také, že se sdílené přihlašovací údaje pro tyto aplikace ignorují. Uživatel je vyzván, aby se přihlásil, i když modul plug-in Microsoft Enterprise SSO funguje pro ostatní aplikace.  

Povolením `disable_explicit_app_prompt` příznaku se omezí schopnost jak nativních aplikací, tak i webových aplikací, aby se zobrazila výzva koncovým uživatelům na vrstvu protokolu a vynechá jednotné přihlašování. Chcete-li povolit příznak, použijte tyto parametry:

- **Klíč**: `disable_explicit_app_prompt`
- **Zadejte**: `Integer`
- **Hodnota**: 1 nebo 0

Doporučujeme povolit tento příznak pro zajištění konzistentního prostředí napříč všemi aplikacemi. Ve výchozím nastavení je zakázaný. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Povolení jednotného přihlašování prostřednictvím souborů cookie pro konkrétní aplikaci

Několik aplikací může být nekompatibilní s příponou jednotného přihlašování. Konkrétně u aplikací, které mají Pokročilá nastavení sítě, může dojít k neočekávaným problémům, pokud jsou povolené pro jednotné přihlašování. Může se například zobrazit chyba oznamující, že se síťová žádost zrušila nebo přerušila. 

Pokud máte potíže s přihlášením pomocí metody popsané v části [aplikace, které nepoužívají MSAL](#applications-that-dont-use-msal) , zkuste alternativní konfiguraci. Pomocí těchto parametrů můžete nakonfigurovat modul plug-in:

- **Klíč**: `AppCookieSSOAllowList`
- **Zadejte**: `String`
- **Hodnota**: čárkami oddělený seznam předpon ID aplikačního kompletu pro aplikace, které můžou být součástí jednotného přihlašování. Všechny aplikace, které začínají uvedenými předponami, se budou moci zúčastnit jednotného přihlašování.
- **Příklad**: `com.contoso.myapp1, com.fabrikam.myapp2`

Aplikace, které jsou povolené pro jednotné přihlašování pomocí této instalace, se musí přidat do `AppCookieSSOAllowList` a `AppPrefixAllowList` .

Vyzkoušejte tuto konfiguraci pouze pro aplikace, které mají neočekávané chyby přihlášení. 

#### <a name="use-intune-for-simplified-configuration"></a>Použití Intune pro zjednodušenou konfiguraci

Intune můžete použít jako službu MDM k usnadnění konfigurace modulu plug-in Microsoft Enterprise SSO. Pomocí Intune můžete například povolit modul plug-in a přidat staré aplikace do povolených, aby získaly jednotné přihlašování. 

Další informace najdete v dokumentaci ke [konfiguraci Intune](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Použití modulu plug-in jednotného přihlašování v aplikaci

[MSAL pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a novější podporují modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Je doporučený způsob, jak přidat podporu pro modul plug-in Microsoft Enterprise SSO. Zajišťuje plné možnosti platformy Microsoft identity.

Pokud vytváříte aplikaci pro prvotní scénáře, informace o instalaci najdete v tématu [režim sdíleného zařízení pro zařízení s iOS](msal-ios-shared-devices.md) .

## <a name="understand-how-the-sso-plug-in-works"></a>Vysvětlení způsobu, jakým modul plug-in jednotného přihlašování funguje

Modul plug-in Microsoft Enterprise SSO se spoléhá na rozhraní [Apple Enterprise SSO Framework](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Zprostředkovatelé identity, kteří se připojují k rozhraní, mohou zachytit síťový provoz pro své domény a rozšířit nebo změnit způsob zpracování těchto požadavků. Například modul plug-in jednotného přihlašování může zobrazit více uživatelská rozhraní pro bezpečné shromažďování přihlašovacích údajů pro koncové uživatele, vyžadovat MFA nebo tiše poskytnout aplikacím tokeny.

Nativní aplikace můžou také implementovat vlastní operace a komunikovat přímo s modulem plug-in jednotného přihlašování. Další informace najdete v tomto [2019 celosvětovém videokonference pro vývojáře od společnosti Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Aplikace, které používají MSAL

[MSAL pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a novější podporuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple pro nativně pro pracovní a školní účty. 

Pokud jste postupovali podle [všech doporučených kroků](./quickstart-v2-ios.md) a použili jste výchozí [Formát identifikátoru URI pro přesměrování](./redirect-uris-ios.md), nepotřebujete žádnou speciální konfiguraci. V zařízeních, která mají modul plug-in jednotného přihlašování, ji MSAL automaticky vyvolá pro všechny interaktivní a tiché požadavky na tokeny. Vyvolá ho také pro operace vyčíslení účtu a odebrání účtu. Vzhledem k tomu, že MSAL implementuje nativní protokol plug-in jednotného přihlašování, který spoléhá na vlastní operace, tato instalační program poskytuje koncovému uživateli nejplynulejšího nativního prostředí. 

Pokud není modul plug-in jednotného přihlašování povolený pro MDM, ale Microsoft Authenticator aplikace se nachází na zařízení, MSAL místo toho používá ověřovací aplikaci pro všechny interaktivní žádosti o tokeny. Modul plug-in jednotného přihlašování sdílí jednotné přihlašování k aplikaci ověřovatele.

### <a name="applications-that-dont-use-msal"></a>Aplikace, které nepoužívají MSAL

Aplikace, které nepoužívají knihovnu Microsoft Identity Platform Library, jako je MSAL, mohou i nadále získat jednotné přihlašování, pokud správce přidá tyto aplikace do nástroje povolených. 

V těchto aplikacích nemusíte měnit kód, pokud jsou splněné následující podmínky:

- Aplikace používá rozhraní Apple pro spouštění síťových požadavků. Mezi tyto architektury patří například [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) a [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession). 
- Aplikace používá ke komunikaci se službou Azure AD standardní protokoly. Mezi tyto protokoly patří například OAuth 2, SAML a WS-Federation.
- Aplikace neshromažďuje uživatelská jména a hesla ve formátu prostého textu v nativním uživatelském rozhraní.

V takovém případě je k dispozici jednotné přihlašování, když aplikace vytvoří síťový požadavek a otevře webový prohlížeč, ve kterém se uživatel přihlásí. Když se uživatel přesměruje na přihlašovací adresu URL služby Azure AD, modul plug-in pro jednotné přihlašování ověří adresu URL a zkontroluje přihlašovací údaje jednotného přihlašování pro tuto adresu URL. Pokud se přihlašovací údaje najde, modul plug-in jednotného přihlašování ho předá službě Azure AD, což aplikaci autorizuje k dokončení požadavku sítě bez nutnosti požádat uživatele o zadání přihlašovacích údajů. Kromě toho, pokud je zařízení známo pro Azure AD, modul plug-in jednotného přihlašování předá certifikát zařízení, aby splnil kontrolu podmíněného přístupu podle zařízení. 

Pro podporu jednotného přihlašování pro aplikace, které nejsou MSAL, modul plug-in jednotného přihlašování implementuje protokol podobný modulu plug-in prohlížeče Windows, který je popsaný v tématu [co je primární obnovovací token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

V porovnání s MSAL aplikacemi Modul plug-in jednotného přihlašování funguje transparentně pro aplikace, které nejsou MSAL. Integruje se se stávajícím přihlašovacím prostředím prohlížeče, které poskytují aplikace. 

Koncový uživatel uvidí známé prostředí a v každé aplikaci se nemusí znovu přihlašovat. Například namísto zobrazení nástroje pro výběr nativního účtu přidává modul plug-in jednotného přihlašování relace jednotného přihlašování k prostředí pro výběr účtu na základě webu. 

## <a name="next-steps"></a>Další kroky

Přečtěte si o [režimu sdíleného zařízení pro zařízení s iOS](msal-ios-shared-devices.md).
