---
title: Podpora jednotného přihlašování a zásad ochrany aplikací v mobilních aplikacích, které vyvíjíte | Azure
titleSuffix: Microsoft identity platform
description: Vysvětlení a Přehled vytváření mobilních aplikací, které podporují jednotné přihlašování a zásady ochrany aplikací pomocí platformy Microsoft identity a integrace s Azure Active Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165174"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Podpora jednotného přihlašování a zásad ochrany aplikací v mobilních aplikacích, které vyvíjíte

Jednotné přihlašování (SSO) je klíčovou nabídkou platformy Microsoft identity a Azure Active Directory, která poskytuje snadné a zabezpečené přihlášení uživatelů vaší aplikace. Zásady ochrany aplikací (aplikace) navíc umožňují podporu klíčových zásad zabezpečení, které udržují data uživatelů v bezpečí. Tyto funkce společně umožňují zabezpečená přihlášení uživatelů a správu dat vaší aplikace.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

Tento článek vysvětluje, proč jednotné přihlašování a aplikace jsou důležité, a poskytuje pokyny pro vysokou úroveň vytváření mobilních aplikací, které tyto funkce podporují. To platí pro aplikace pro telefon i pro tablety. Pokud jste správce IT, který chce nasadit jednotné přihlašování v rámci tenanta Azure Active Directory vaší organizace, přečtěte si naše [pokyny k plánování nasazení jednotného přihlašování](../manage-apps/plan-sso-deployment.md) .

## <a name="about-single-sign-on-and-app-protection-policies"></a>Informace o jednotném přihlašování a zásadách ochrany aplikací

[Jednotné přihlašování (SSO)](../manage-apps/plan-sso-deployment.md) umožňuje uživateli přihlásit se jednou a získat přístup k ostatním aplikacím bez nutnosti opětovného zadání přihlašovacích údajů. To usnadňuje přístup k aplikacím a eliminuje nutnost zapamatovat si uživatele dlouhý seznam uživatelských jmen a hesel. Implementace ve vaší aplikaci usnadňuje přístup k vaší aplikaci a její používání.

Kromě toho povolení jednotného přihlašování ve vaší aplikaci odemkne nové mechanismy ověřování, které se dodávají s moderním ověřováním, jako třeba [přihlášení bez hesla](../authentication/concept-authentication-passwordless.md). Uživatelská jména a hesla představují jeden z nejoblíbenějších vektorů útoku na aplikace a povolení jednotného přihlašování umožňuje zmírnit toto riziko vynucením podmíněného přístupu nebo přihlášení bez hesla, která přidávají další zabezpečení nebo spoléhají na bezpečnější mechanismy ověřování. Nakonec povolení jednotného přihlašování umožňuje také [jednotné přihlašování](v2-protocols-oidc.md#single-sign-out). To je užitečné v situacích, jako jsou pracovní aplikace, které se budou používat na sdílených zařízeních.

[Zásady ochrany aplikací (aplikace)](/mem/intune/apps/app-protection-policy) zajišťují, aby data organizace zůstala bezpečná a obsažená. Umožňují společnostem spravovat a chránit svá data v rámci aplikace a umožňují kontrolu nad tím, kdo má přístup k aplikaci a jejím datům. Implementace zásad ochrany aplikací umožňuje aplikaci připojit uživatele k prostředkům chráněným zásadami podmíněného přístupu a bezpečně přenášet data do a z jiných chráněných aplikací. Mezi scénáře odemknuté zásadami ochrany aplikací patří vyžadování PIN kódu pro otevření aplikace, řízení sdílení dat mezi aplikacemi a zabránění ukládání dat firemních aplikací do osobních umístění úložiště.

## <a name="implementing-single-sign-on"></a>Implementace jednotného přihlašování

K tomu, aby vaše aplikace mohla využívat jednotné přihlašování, doporučujeme použít následující.

### <a name="use-the-microsoft-authentication-library-msal"></a>Použití knihovny Microsoft Authentication Library (MSAL)

Nejlepší volbou pro implementaci jednotného přihlašování v aplikaci je použití [knihovny Microsoft Authentication Library (MSAL)](msal-overview.md). Pomocí MSAL můžete do své aplikace přidat ověřování s minimálními voláními kódu a rozhraní API, získat kompletní funkce [platformy Microsoft Identity Platform](./index.yml)a nechat Microsoft zvládnout údržbu řešení zabezpečeného ověřování. Ve výchozím nastavení MSAL přidává podporu jednotného přihlašování pro vaši aplikaci. Použití MSAL je navíc potřeba v případě, že plánujete implementovat taky zásady ochrany aplikací.

> [!NOTE]
> MSAL je možné nakonfigurovat tak, aby používala vložené webové zobrazení. Tím zabráníte jednotnému přihlašování. Pro zajištění, že jednotné přihlašování bude fungovat, použijte výchozí chování (to znamená systémový webový prohlížeč).

Pokud v aplikaci aktuálně používáte [knihovnu ADAL](../azuread-dev/active-directory-authentication-libraries.md) , důrazně doporučujeme, abyste [ji migrovali na MSAL](msal-migration.md), protože [ADAL je zastaralá](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

V případě aplikací pro iOS máme [rychlý Start](quickstart-v2-ios.md) , který vám ukáže, jak nastavit přihlášení pomocí MSAL a také [pokyny ke konfiguraci MSAL pro různé scénáře jednotného přihlašování](single-sign-on-macos-ios.md).

V případě aplikací pro Android máme [rychlý Start](quickstart-v2-android.md) , který vám ukáže, jak nastavit přihlášení pomocí MSAL a návod, [Jak povolit jednotné přihlašování pro různé aplikace v Androidu pomocí MSAL](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>Použití systémového webového prohlížeče

Pro interaktivní ověřování je vyžadován webový prohlížeč. Pro mobilní aplikace, které používají moderní knihovny pro ověřování než MSAL (tj. jiné knihovny OpenID Connect nebo SAML), nebo Pokud implementujete vlastní ověřovací kód, měli byste k povolení jednotného přihlašování použít prohlížeč systému jako plochu ověřování.

Google má pokyny k tomu, jak to udělat v aplikacích pro Android: [vlastní karty Chrome – Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Apple obsahuje pokyny k tomu, jak to provést v aplikacích pro iOS: [ověřování uživatele prostřednictvím webové služby | Dokumentaci pro vývojáře Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> [Modul plug-in jednotného přihlašování pro zařízení Apple](apple-sso-plugin.md) umožňuje jednotné přihlašování pro aplikace iOS, které používají vložená Webová zobrazení na spravovaných zařízeních pomocí Intune. Jako nejlepší možnost pro vývoj aplikací, které umožňují jednotné přihlašování pro všechny uživatele, doporučujeme MSAL a systémový prohlížeč, ale v některých případech to umožní jednotné přihlašování (SSO), kde jinak není možné.

## <a name="enable-app-protection-policies"></a>Povolit zásady ochrany aplikací

Pokud chcete povolit zásady ochrany aplikací, použijte [Microsoft Authentication Library (MSAL)](msal-overview.md). MSAL je knihovna pro ověřování a autorizaci platformy Microsoft a sada Intune SDK je vyvíjená tak, aby fungovala společně s ní.

Kromě toho musíte pro ověřování použít aplikaci zprostředkovatele. Zprostředkovatel vyžaduje, aby aplikace poskytovala informace o aplikaci a zařízení, aby zajistila dodržování předpisů pro aplikace. uživatelé iOS použijí [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md) a uživatelé systému Android použijí pro zprostředkované [ověřování](./msal-android-single-sign-on.md)buď aplikaci Microsoft Authenticator, nebo [aplikaci Portál společnosti](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) . Ve výchozím nastavení MSAL používá zprostředkovatele jako první volbu pro splnění žádosti o ověření, takže použití zprostředkovatele k ověřování bude automaticky zapnuto pro vaši aplikaci při použití MSAL.

Nakonec přidejte do své aplikace [Intune SDK](/mem/intune/developer/app-sdk-get-started) , abyste mohli povolit zásady ochrany aplikací. Sada SDK pro většinu částí sleduje model zachycení a automaticky použije zásady ochrany aplikací k určení, jestli jsou povolené akce, které aplikace přijímá. K dispozici jsou také rozhraní API, která můžete volat ručně a sdělit aplikaci, pokud existují určitá omezení pro určité akce.

## <a name="additional-resources"></a>Další zdroje informací

- [Plánování nasazení s jednotným přihlašováním Azure Active Directory](../manage-apps/plan-sso-deployment.md)
- [Postupy: Konfigurace jednotného přihlašování v macOS a iOS](single-sign-on-macos-ios.md)
- [Microsoft Enterprise SSO – modul plug-in pro zařízení Apple (Preview)](apple-sso-plugin.md)
- [Zprostředkované ověřování v Androidu](./msal-android-single-sign-on.md)
- [Autorizační agenti a jejich povolení](./msal-android-single-sign-on.md)
- [Začínáme se sadou Microsoft Intune App SDK](/mem/intune/developer/app-sdk-get-started)
- [Konfigurace nastavení pro sadu Intune App SDK](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Microsoft Intune chráněné aplikace](/mem/intune/apps/apps-supported-intune-apps)
