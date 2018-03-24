---
title: Web správy cloudových identit a mobilní aplikace Azure AD B2C | Microsoft Docs
description: Vývoj aplikací určených uživatelům pomocí Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 6385a4e4b89cf889b71db6e09010dfe3a674336a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C: Soustřeďte se na svoji aplikaci, o registraci a přihlašování se postaráme my

Azure AD B2C je cloudové řešení správy identit pro webové a mobilní aplikace. Jedná se o vysoce dostupnou globální službu, která je škálovatelná pro stovky milionů identit. Řešení Azure AD B2C je postavené na platformě zabezpečené na podnikové úrovni a chrání tak vaše aplikace, firmu i zákazníky.

Při minimální konfiguraci řešení Azure AD B2C umožňuje vaší aplikaci ověřovat tyto typy účtů:

* **Sociální účty** (jako je Facebook, Google, LinkedIn a další)
* **Účty organizací** (využívající protokoly s otevřenými standardy, OpenID Connect nebo SAML)
* **Místní účty** (e-mailová adresa a heslo nebo uživatelské jméno a heslo)

## <a name="get-started"></a>Začínáme

Nejdřív pomocí návodu v tématu [Vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md) získejte vlastního tenanta.

Zvolte svůj scénář vývoje aplikací:

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobilní aplikace a aplikace počítače](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobilní aplikace a aplikace počítače</center> | [Přehled](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web Apps](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [Přehled](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[Jádro ASP.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Jednostránkové aplikace](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Jednostránkové aplikace</center> | [Přehled](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Webová rozhraní API](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Webová rozhraní API</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [Jádro ASP.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [Volání webového rozhraní API .NET](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>Co je nového

Pravidelně kontrolujte tuto stránku, abyste se dovědět o budoucích změnách Azure Active Directory B2C. O všech aktualizacích také tweetujeme na @AzureAD.

* Vedle funkce Předdefinované zásady (všeobecná dostupnost) je teď ve veřejné verzi Preview dostupná funkce [Vlastní zásady](active-directory-b2c-overview-custom.md).  Vlastní zásady jsou pro profesionály pracující identitami, kteří potřebují mít kontrolu nad složením jejich prostředí identit.
* Ve veřejné verzi Preview je teď dostupná funkce [Přístupový token](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview).
* Proběhlo oznámení [všeobecné dostupnosti služby Azure AD B2C pro Evropu](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/).
* Projděte si naši rostoucí knihovnu [ukázek kódů na GitHubu](https://github.com/Azure-Samples?q=b2c)!

## <a name="how-to-articles"></a>Články s návody

Další informace o použití konkrétních funkcí Azure Active Directory B2C:

* Nastavení [Facebooku](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [účtu Microsoft](active-directory-b2c-setup-msa-app.md), účtů [Amazon](active-directory-b2c-setup-amzn-app.md), a [inkedIn](active-directory-b2c-setup-li-app.md) pro použití v aplikacích určených uživatelům.
* [Použití vlastních atributů ke sběru informací o uživatelích](active-directory-b2c-reference-custom-attr.md).
* [Povolení Azure Multi-Factor Authentication v aplikacích určených uživatelům](active-directory-b2c-reference-mfa.md).
* [Nastavení samoobslužného resetování hesla pro uživatele](active-directory-b2c-reference-sspr.md).
* [Přizpůsobení vzhledu a chování registrace a přihlášení a dalších stránek určených uživatelům](active-directory-b2c-reference-ui-customization.md), které obsluhuje Azure Active Directory B2C.
* [Využijte Azure Active Directory Graph API k programovému vytváření, čtení, aktualizaci a mazání uživatelů](active-directory-b2c-devquickstarts-graph-dotnet.md) ve svém klientu Azure Active Directory B2C.

## <a name="next-steps"></a>Další postup

Následující odkazy jsou užitečné při prozkoumávání služby do hloubky:

* Viz [Informace o cenách Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Projděte si naše [ukázky kódu](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) pro Azure Active Directory B2C. 
* Získejte pomoc na Stack Overflow pomocí značky [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Sdělte nám své myšlenky pomocí [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) chceme je slyšet!
* Přečtěte si [Referenci protokolu Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Přečtěte si [Referenci tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Přečtěte si [Nejčastější dotazy k Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Podávejte požadavky na podporu pro Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Získejte bezpečnostní aktualizace našich produktů

Doporučujeme vám získávat oznámení o bezpečnostních incidentech tak, že navštívíte [tuto stránku](https://technet.microsoft.com/security/dd252948) a přihlásíte se k odběru služby Security Advisory Alerts.

