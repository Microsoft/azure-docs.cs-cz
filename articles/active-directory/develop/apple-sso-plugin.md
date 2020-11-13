---
title: Modul plug-in Microsoft Enterprise SSO pro zařízení Apple
titleSuffix: Microsoft identity platform | Azure
description: Přečtěte si informace o modulu plug-in Azure Active Directory SSO Microsoftu pro zařízení s iOS a macOS.
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
ms.openlocfilehash: b7ec6ab8b52d9d43d898f481a2f36310e5c0897d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561076"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO – modul plug-in pro zařízení Apple (Preview)

>[!IMPORTANT]
> Tato funkce [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Modul plug-in Microsoft Enterprise SSO pro zařízení Apple* poskytuje jednotné přihlašování (SSO) pro účty Azure Active Directory (Azure AD) ve všech aplikacích, které podporují funkci [podnikového jednotného přihlašování](https://developer.apple.com/documentation/authenticationservices) od společnosti Apple. Společnost Microsoft úzce spolupracuje s Applem při vývoji tohoto modulu plug-in, aby zvýšila použitelnost vaší aplikace a současně poskytovala nejlepší ochranu, kterou může společnost Apple a Microsoft poskytnout.

V této Public Preview vydané verzi je modul plug-in jednotného přihlašování (SSO) Enterprise dostupný jenom pro zařízení s iOS a distribuuje se do určitých aplikací Microsoftu.

## <a name="features"></a>Funkce

Modul plug-in Microsoft Enterprise SSO pro zařízení Apple nabízí tyto výhody:

- Poskytuje jednotné přihlašování pro účty Azure AD napříč všemi aplikacemi, které podporují funkci podnikového jednoho Sign-On společnosti Apple.
- Služba se automaticky doručí do Microsoft Authenticator a může ji povolit jakékoli řešení pro správu mobilních zařízení (MDM).

## <a name="requirements"></a>Požadavky

Chcete-li použít modul plug-in jednotného přihlašování Microsoft Enterprise pro zařízení Apple:

- v zařízení musí být nainstalovaná iOS 13,0 nebo vyšší.
- V zařízení musí být nainstalovaná aplikace Microsoftu, která poskytuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple. Pro Public Preview tyto aplikace zahrnují [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Zařízení musí být zaregistrované v MDM (například pomocí Microsoft Intune).
- Aby bylo možné povolit modul plug-in Microsoft Enterprise SSO pro zařízení Apple na zařízení, musí být do zařízení vložena konfigurace. Toto omezení zabezpečení je vyžadováno společností Apple.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Povolení modulu plug-in jednotného přihlašování se správou mobilních zařízení (MDM)

Aby bylo možné povolit modul plug-in Microsoft Enterprise SSO pro zařízení Apple, musí být vaše zařízení odesíláno signálem přes službu MDM. Vzhledem k tomu, že společnost Microsoft obsahuje v [aplikaci Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md)modul plug-in jednotného přihlašování (SSO), pomocí své správy mobilních zařízení nakonfigurujte aplikaci tak, aby povolovala modul plug-in Microsoft Enterprise SSO.

Ke konfiguraci modulu plug-in Microsoft Enterprise SSO pro zařízení Apple použijte následující parametry:

- **Typ** : přesměrování
- **ID rozšíření** : `com.microsoft.azureauthenticator.ssoextension`
- **ID týmu** : (Toto pole není pro iOS potřeba)
- **Adresy URL** :
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

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Povolení jednotného přihlašování pro aplikace, které nepoužívají MSAL

Modul plug-in jednotného přihlašování umožňuje libovolné aplikaci zapojit se do jednotného přihlašování i v případě, že nebyla vyvinuta pomocí sady Microsoft SDK, jako je například knihovna Microsoft Authentication Library (MSAL).

Modul plug-in jednotného přihlašování se instaluje automaticky pomocí zařízení, která stáhla aplikaci Microsoft Authenticator a zaregistrovala svoje zařízení ve vaší organizaci. Vaše organizace nejspíš používá ověřovací aplikaci ještě dnes pro scénáře, jako je Multi-Factor Authentication, ověřování bez hesla a podmíněný přístup. Dá se zapnout pro vaše aplikace pomocí libovolného poskytovatele MDM, přestože Microsoft usnadňuje konfiguraci v Microsoft Endpoint Manageru Intune. Pomocí seznamu povolených aplikací se tyto aplikace nakonfigurují tak, aby používaly modul plug-in jednotného přihlašování, který je nainstalovaný ověřovací aplikací.

Podporují se jenom aplikace, které používají nativní síťové technologie a webzobrazení společnosti Apple. Pokud aplikace doplní svoji vlastní implementaci síťové vrstvy, modul plug-in Microsoft Enterprise SSO není podporován.  

Pomocí následujících parametrů nakonfigurujte modul plug-in Microsoft Enterprise SSO pro aplikace, které nepoužívají MSAL:

- **Klíč** : `AppAllowList`
- **Zadejte** : `String`
- **Hodnota** : seznam identifikátorů aplikačních sad oddělených čárkami, které se můžou zúčastnit jednotného přihlašování
- **Příklad** : `com.contoso.workapp, com.contoso.travelapp`

[Přijaté aplikace](./application-consent-experience.md) , které může správce MDM zapojit do programu jednotného přihlašování, můžou pro koncového uživatele tiše získat token. Proto je důležité přidat pouze důvěryhodné aplikace do seznamu povolených aplikací. 

Do tohoto seznamu nemusíte přidávat aplikace, které používají MSAL nebo ASWebAuthenticationSession. Tyto aplikace jsou ve výchozím nastavení povolené. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Povolení vytvoření relace jednotného přihlašování z libovolné aplikace

Ve výchozím nastavení poskytuje modul plug-in Microsoft Enterprise SSO jednotné přihlašování (SSO) pro autorizované aplikace jenom v případě, že modul plug-in jednotného přihlašování už má sdílené přihlašovací údaje. Modul plug-in Microsoft Enterprise SSO může získat sdílené přihlašovací údaje, pokud je zavolá jiná aplikace založené na knihovně ADAL nebo MSAL během získávání tokenu. Většina aplikací Microsoftu používá Microsoft Authenticator nebo modul plug-in jednotného přihlašování. To znamená, že ve výchozím nastavení je nejvhodnější přihlašování mimo nativní toky aplikací.  

`browser_sso_interaction_enabled`Při povolení příznaku se neMSAL aplikace a prohlížeč Safari provede počáteční zavedení a získá sdílené přihlašovací údaje. Pokud modul plug-in jednotného přihlašování (SSO) Microsoft Enterprise ještě nemá sdílené přihlašovací údaje, pokusí se ho získat pokaždé, když se na adrese URL služby Azure AD v prohlížeči Safari, ASWebAuthenticationSession, SafariViewController nebo jiné povolené nativní aplikaci žádá o přihlášení.  

- **Klíč** : `browser_sso_interaction_enabled`
- **Zadejte** : `Integer`
- **Hodnota** : 1 nebo 0

Doporučujeme povolit tento příznak pro zajištění jednotnějšího prostředí napříč všemi aplikacemi. Ve výchozím nastavení je zakázaná. 

#### <a name="disable-oauth2-application-prompts"></a>Zakázat výzvy k použití aplikace OAuth2

Modul plug-in Microsoft Enterprise SSO poskytuje jednotné přihlašování pomocí připojení sdílených přihlašovacích údajů k síťovým požadavkům přicházejících z povolených aplikací. Některé aplikace OAuth2 můžou vynutit zobrazení výzvy koncového uživatele na vrstvě protokolu. Sdílené přihlašovací údaje by se pro tyto aplikace ignorovaly.  

Povolením `disable_explicit_app_prompt` příznaku se omezuje schopnost nativních i webových aplikací, aby se vynutila výzva koncovým uživatelům na vrstvu protokolu a vynechá jednotné přihlašování.

- **Klíč** : `disable_explicit_app_prompt`
- **Zadejte** : `Integer`
- **Hodnota** : 1 nebo 0

Doporučujeme povolit tento příznak pro zajištění jednotnějšího prostředí napříč všemi aplikacemi. Ve výchozím nastavení je zakázaná. 

#### <a name="use-intune-for-simplified-configuration"></a>Použití Intune pro zjednodušenou konfiguraci

Jako službu MDM můžete použít Microsoft Intune k usnadnění konfigurace modulu plug-in Microsoft Enterprise SSO. Další informace najdete v dokumentaci ke [konfiguraci Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Použití modulu plug-in jednotného přihlašování ve vaší aplikaci

[Knihovna Microsoft Authentication Library (MSAL) pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a vyšší podporuje modul plug-in Microsoft Enterprise SSO pro zařízení Apple.

Pokud vytváříte aplikaci pro pracovní postupy prvotní, přečtěte si téma [režim sdíleného zařízení pro zařízení s iOS](msal-ios-shared-devices.md) , kde najdete další nastavení této funkce.

## <a name="how-the-sso-plug-in-works"></a>Jak funguje modul plug-in jednotného přihlašování

Modul plug-in Microsoft Enterprise SSO se spoléhá na [platformu Apple Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Zprostředkovatelé identity, kteří docházejí do architektury, můžou zachytit síťový provoz pro své domény a rozšířit nebo změnit způsob zpracování těchto požadavků. Například modul plug-in jednotného přihlašování může zobrazit další uživatelské rozhraní pro bezpečné shromažďování přihlašovacích údajů pro koncové uživatele, vyžadovat MFA nebo tiše poskytnout aplikacím tokeny.

Nativní aplikace můžou také implementovat vlastní operace a komunikovat přímo s modulem plug-in jednotného přihlašování.
V tomto [videu s 2019 WWDC](https://developer.apple.com/videos/play/tech-talks/301/) se můžete dozvědět víc o architektuře jednotného přihlašování od společnosti Apple.

### <a name="applications-that-use-msal"></a>Aplikace, které používají MSAL

[Knihovna Microsoft Authentication Library (MSAL) pro zařízení Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verze 1.1.0 a vyšší podporuje pro nativně pracovní a školní účty modul plug-in Microsoft Enterprise SSO pro zařízení Apple. 

Pokud jste postupovali podle [všech doporučených kroků](./quickstart-v2-ios.md) a použili jste výchozí [formát URI pro přesměrování](./redirect-uris-ios.md), není potřeba žádná zvláštní konfigurace. Při spuštění na zařízení, které má modul plug-in jednotného přihlašování, MSAL ho automaticky vyvolá pro všechny interaktivní a bezobslužné požadavky na tokeny a taky na operace vyčíslení účtů a odebrání účtu. Vzhledem k tomu, že MSAL implementuje nativní protokol plug-in jednotného přihlašování, který spoléhá na vlastní operace, tato instalační program poskytuje koncovému uživateli nejplynulejšího nativního prostředí. 

Pokud není v MDM povolený modul plug-in jednotného přihlašování, ale aplikace Microsoft Authenticator se nachází na zařízení, místo toho se použije aplikace Microsoft Authenticator pro všechny interaktivní žádosti o tokeny. Modul plug-in jednotného přihlašování sdílí jednotné přihlašování k aplikaci Microsoft Authenticator.

### <a name="applications-that-dont-use-msal"></a>Aplikace, které nepoužívají MSAL

Aplikace, které nepoužívají MSAL, můžou stále získat jednotné přihlašování, pokud je Správce přidá do seznamu povolených aplikací explicitně. 

V těchto aplikacích nejsou potřeba žádné změny kódu, pokud jsou splněné následující podmínky:

- Aplikace používá rozhraní Apple pro provádění síťových požadavků (například [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)). 
- Aplikace používá ke komunikaci se službou Azure AD standardní protokoly (například OAuth2, SAML, WS-Federation).
- Aplikace neshromažďuje uživatelské jméno a heslo ve formátu prostého textu v nativním uživatelském rozhraní.

V takovém případě je k dispozici jednotné přihlašování, když aplikace vytvoří síťový požadavek a otevře webový prohlížeč, ve kterém se uživatel přihlásí. Když se uživatel přesměruje na přihlašovací adresu URL služby Azure AD, modul plug-in pro jednotné přihlašování ověří adresu URL a zkontroluje, jestli jsou pro tuto adresu URL k dispozici přihlašovací údaje jednotného přihlašování. Pokud existuje, modul plug-in jednotného přihlašování předá přihlašovací údaje jednotného přihlašování do služby Azure AD, což aplikaci autorizuje k dokončení požadavku sítě bez nutnosti požádat uživatele, aby zadal svoje přihlašovací údaje. Pokud je navíc zařízení ve službě Azure AD známé, modul plug-in jednotného přihlašování také předá certifikát zařízení, aby splnil kontrolu podmíněného přístupu podle zařízení. 

Pro podporu jednotného přihlašování pro aplikace, které nejsou MSAL, modul plug-in jednotného přihlašování implementuje protokol podobný modulu plug-in prohlížeče Windows, který je popsaný v tématu [co je primární obnovovací token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

V porovnání s MSAL aplikacemi Modul plug-in jednotného přihlašování funguje transparentně pro aplikace, které nejsou MSAL, integrací s existujícím přihlašovacím prostředím prohlížeče, které poskytují aplikace. Koncový uživatel uvidí své známé zkušenosti s tím, že v každé aplikaci nebude nutné provádět další přihlášení. Například namísto zobrazení nástroje pro výběr nativního účtu přidává modul plug-in jednotného přihlašování relace jednotného přihlašování k prostředí pro výběr účtu na základě webu. 

## <a name="next-steps"></a>Další kroky

Další informace o režimu sdíleného zařízení v iOS najdete v tématu [režim sdíleného zařízení pro zařízení s iOS](msal-ios-shared-devices.md).
