---
title: Ověřování a autorizace
description: Přečtěte si o integrované podpoře ověřování a autorizaci v Azure App Service a o tom, jak vám může pomoci zabezpečit vaši aplikaci před neoprávněným přístupem.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: ff0eb102d37f285279c041ff91b7a89e157259eb
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672246"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service

> [!NOTE]
> V tuto chvíli se pro Azure App Services a Azure Functions nepodporuje AAD v2 (včetně MSAL). Vraťte se prosím na aktualizace.
>

Azure App Service poskytuje integrovanou podporu ověřování a autorizace, takže se můžete přihlašovat uživatelům a přistupovat k datům tak, že ve své webové aplikaci, rozhraní RESTful API a mobilním back-endu napíšete minimální nebo žádný kód a také [Azure Functions](../azure-functions/functions-overview.md). Tento článek popisuje, jak App Service pomáhá zjednodušit ověřování a autorizaci pro vaši aplikaci.

Zabezpečené ověřování a autorizace vyžadují důkladné porozumění zabezpečení, včetně federace, šifrování, správy [webových tokenů JSON (Jwt)](https://wikipedia.org/wiki/JSON_Web_Token) , [typů udělení](https://oauth.net/2/grant-types/)a tak dále. App Service poskytuje tyto nástroje, díky kterým můžete věnovat více času a energii na poskytování obchodních hodnot vašemu zákazníkovi.

> [!IMPORTANT]
> Nemusíte používat App Service pro AuthN/AuthO. Mnohé webové architektury jsou součástí sady funkcí zabezpečení a můžete je použít, pokud chcete. Pokud potřebujete větší flexibilitu, než App Service poskytuje, můžete také napsat vlastní nástroje.  
>
> Pokud však přecházíte na některou z možností bez App Service pro vzdálené ověřování, pamatujte, že [Chrome 80 provádí zásadní změny v implementaci SameSite for soubory cookie](https://www.chromestatus.com/feature/5088147346030592) (Datum vydání v březnu 2020) a ověřovací mechanismus vaší aplikace může při aktualizaci prohlížečů klienta poškodit. Dokumentace k ASP.NET Core obsahuje informace o tom, jak tento postup vyřešit v aplikaci, v [http: Browser SameSite změny dopadu ověřování](/dotnet/core/compatibility/3.0-3.1#http-browser-samesite-changes-impact-authentication). Obsahuje užitečné pokyny k tomu, jak otestovat tuto zásadní změnu v hlavních prohlížečích bez ohledu na to, jestli používáte ASP.NET Core nebo ne.
>

Informace specifické pro nativní mobilní aplikace najdete v tématech [ověřování a autorizace uživatelů pro mobilní aplikace s Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to funguje

Modul ověřování a autorizace se spouští ve stejném izolovaném prostoru jako váš kód aplikace. Pokud je povolena, každý příchozí požadavek HTTP projde před tím, než bude zpracován kódem vaší aplikace.

![](media/app-service-authentication-overview/architecture.png)

Tento modul zpracovává několik věcí pro vaši aplikaci:

- Ověřuje uživatele pomocí zadaného zprostředkovatele.
- Ověří, ukládá a aktualizuje tokeny.
- Spravuje ověřenou relaci.
- Vloží informace o identitě do hlaviček požadavků.

Modul se spouští odděleně od kódu aplikace a je nakonfigurovaný pomocí nastavení aplikace. Nevyžadují se žádné sady SDK, konkrétní jazyky ani změny kódu vaší aplikace. 

### <a name="user-claims"></a>Deklarace identity uživatele

Pro všechny jazykové architektury App Service zpřístupňuje deklarace identity uživatele vložením do hlaviček požadavku. Pro App Service aplikace ASP.NET 4,6 naplní [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) deklarací identity ověřeného uživatele, takže můžete postupovat podle standardního vzoru kódu .NET, včetně atributu `[Authorize]`. Podobně pro aplikace PHP App Service naplní `_SERVER['REMOTE_USER']` proměnnou. Pro aplikace Java jsou deklarace identity [dostupné z servlet Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Pro [Azure Functions](../azure-functions/functions-overview.md)`ClaimsPrincipal.Current` není v kódu .NET vysušen, ale v hlavičkách požadavků můžete stále najít deklarace identity uživatelů.

Další informace najdete v tématu [přístup k deklaracím uživatelů](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Úložiště tokenů

App Service poskytuje integrované úložiště tokenů, což je úložiště tokenů, které jsou přidružené k uživatelům webových aplikací, rozhraní API nebo nativních mobilních aplikací. Pokud povolíte ověřování u libovolného poskytovatele, je úložiště tokenů hned dostupné pro vaši aplikaci. Pokud kód aplikace potřebuje přístup k datům z těchto poskytovatelů jménem uživatele, například: 

- odeslání příspěvku na časovou osu Facebooku ověřeného uživatele
- Přečtěte si podniková data uživatele z Azure Active Directory Graph API nebo dokonce i Microsoft Graph

Obvykle je nutné napsat kód pro shromažďování, ukládání a aktualizaci těchto tokenů ve vaší aplikaci. V úložišti tokenů stačí [načíst tokeny](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) , když je potřebujete, a [sdělit App Service, že je budou aktualizovat](app-service-authentication-how-to.md#refresh-identity-provider-tokens) , jakmile se stanou neplatnými. 

Tokeny ID, přístupové tokeny a obnovovací tokeny uložené v mezipaměti pro ověřenou relaci a jsou přístupné pouze přidruženému uživateli.  

Pokud nepotřebujete v aplikaci pracovat s tokeny, můžete úložiště tokenů zakázat.

### <a name="logging-and-tracing"></a>Protokolování a trasování

Pokud [povolíte protokolování aplikací](troubleshoot-diagnostic-logs.md), zobrazí se přímo v souborech protokolu trasování pro ověřování a autorizaci. Pokud se zobrazí chyba ověřování, kterou jste neočekávali, můžete pohodlně vyhledat všechny podrobnosti, a to tak, že budete hledat v existujících protokolech aplikací. Pokud povolíte [trasování chybných požadavků](troubleshoot-diagnostic-logs.md), vidíte přesně to, jakou roli se modul ověřování a autorizace mohl přehrát v neúspěšném požadavku. V protokolech trasování vyhledejte odkazy na modul s názvem `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Zprostředkovatelé identit

App Service používá [federované identity](https://en.wikipedia.org/wiki/Federated_identity), ve kterém poskytovatel identity od jiného výrobce spravuje identity uživatelů a tok ověřování za vás. Ve výchozím nastavení je dostupných pět zprostředkovatelů identity: 

| Poskytovatel | Koncový bod přihlášení |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Účet Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Pokud povolíte ověřování a autorizaci jedním z těchto poskytovatelů, je k dispozici koncový bod přihlášení pro ověřování uživatelů a ověření tokenů ověřování od poskytovatele. Uživatelům můžete snadno poskytnout libovolný počet možností přihlašování. Můžete také integrovat jiného poskytovatele identity nebo vlastní [řešení identity][custom-auth].

## <a name="authentication-flow"></a>Tok ověřování

Tok ověřování je stejný pro všechny poskytovatele, ale liší se v závislosti na tom, jestli se chcete přihlásit pomocí sady SDK pro poskytovatele:

- Bez sady Provider SDK: aplikace deleguje federované přihlašování k App Service. Většinou se jedná o prohlížeč pro aplikace v prohlížeči, který může uživateli prezentovat přihlašovací stránku poskytovatele. Serverový kód spravuje proces přihlášení, takže se také nazývá _tok směrovaného serveru_ nebo _tok serveru_. Tento případ se vztahuje na aplikace prohlížeče. Vztahuje se také na nativní aplikace, které uživatele podepisují pomocí Mobile Apps klientské sady SDK, protože sada SDK otevírá webové zobrazení k podepisování uživatelů pomocí ověřování App Service. 
- Se sadou SDK pro poskytovatele: aplikace podepisuje uživatele do poskytovatele ručně a pak odešle ověřovací token, aby App Service k ověření. Většinou se jedná o aplikace bez prohlížeče, které nemůžou uživateli prezentovat přihlašovací stránku poskytovatele. Kód aplikace spravuje proces přihlášení, takže se také nazývá _tok směrovaného klientem_ nebo _tok klienta_. Tento případ se vztahuje na klienty rozhraní REST API, [Azure Functions](../azure-functions/functions-overview.md)a JavaScript, jakož i aplikace v prohlížeči, které v procesu přihlašování vyžadují větší flexibilitu. Vztahuje se také na nativní mobilní aplikace, které uživatele podepisují pomocí sady SDK poskytovatele.

> [!NOTE]
> Volání z důvěryhodné aplikace prohlížeče v App Service zavolají další REST API v App Service nebo [Azure Functions](../azure-functions/functions-overview.md) lze ověřit pomocí toku směrovaného na server. Další informace najdete v tématu [Přizpůsobení ověřování a autorizace v App Service](app-service-authentication-how-to.md).
>

V následující tabulce jsou uvedené kroky toku ověřování.

| Krok | Bez sady Provider SDK | Se sadou SDK pro poskytovatele |
| - | - | - |
| 1. podepsat uživatele | Přesměruje klienta na `/.auth/login/<provider>`. | Kód klienta podepisuje uživatele přímo se sadou SDK pro poskytovatele a přijímá ověřovací token. Informace najdete v dokumentaci k poskytovateli. |
| 2. po ověření | Zprostředkovatel přesměrovává klienta na `/.auth/login/<provider>/callback`. | [Token příspěvků klientských kódů od poskytovatele po](app-service-authentication-how-to.md#validate-tokens-from-providers) `/.auth/login/<provider>` k ověření. |
| 3. vytvoření ověřené relace | App Service přidá ověřený soubor cookie pro odpověď. | App Service vrátí do klientského kódu svůj vlastní ověřovací token. |
| 4. zajišťovat ověřený obsah | Klient zahrnuje ověřovací soubor cookie v následných požadavcích (automaticky zpracovávaných prohlížečem). | Klientský kód prezentuje ověřovací token v hlavičce `X-ZUMO-AUTH` (automaticky zpracovávaný Mobile Apps klientské sady SDK). |

Pro klientské prohlížeče App Service může automaticky směrovat všechny neověřené uživatele na `/.auth/login/<provider>`. Můžete taky prezentovat uživatele s jedním nebo více `/.auth/login/<provider>` odkazy pro přihlášení k aplikaci pomocí poskytovatele podle vlastního výběru.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Chování autorizace

V [Azure Portal](https://portal.azure.com)můžete nakonfigurovat App Service autorizaci s řadou chování, když příchozí žádost není ověřena.

![](media/app-service-authentication-overview/authorization-flow.png)

Tyto možnosti jsou popsány v následujících nadpisech.

### <a name="allow-anonymous-requests-no-action"></a>Povolení anonymních požadavků (žádná akce)

Tato možnost odloží autorizaci neověřeného provozu do kódu aplikace. U ověřených požadavků App Service také společně s ověřovacími informacemi v hlavičkách protokolu HTTP. 

Tato možnost nabízí větší flexibilitu při zpracování anonymních požadavků. Například umožňuje uživatelům [prezentovat více poskytovatelů přihlášení](app-service-authentication-how-to.md#use-multiple-sign-in-providers) . Je však nutné napsat kód. 

### <a name="allow-only-authenticated-requests"></a>Povolení pouze ověřených požadavků

Tato možnost se **přihlásí pomocí > poskytovatele \<** . App Service přesměruje všechny anonymní požadavky na `/.auth/login/<provider>` pro poskytovatele, kterého si zvolíte. Pokud anonymní požadavek pochází z nativní mobilní aplikace, vrácená odpověď je `HTTP 401 Unauthorized`.

Pomocí této možnosti nemusíte v aplikaci psát žádný ověřovací kód. Přesnější autorizaci, například autorizaci specifickou pro role, je možné zpracovat kontrolou deklarací identity uživatele (viz [přístup k deklaracím uživatelů](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou.

## <a name="more-resources"></a>Další zdroje

[Kurz: ověřování a autorizace uživatelů na konci Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Kurz: ověřování a autorizace uživatelů na konci Azure App Service pro Linux](containers/tutorial-auth-aad.md)  
[Přizpůsobení ověřování a autorizace v App Service](app-service-authentication-how-to.md)

Návody pro konkrétního poskytovatele:

* [Konfigurace aplikace pro použití přihlášení Azure Active Directory][AAD]
* [Konfigurace aplikace pro použití přihlášení k Facebooku][Facebook]
* [Konfigurace aplikace pro použití přihlášení ke Googlu][Google]
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft][MSA]
* [Konfigurace aplikace pro použití přihlášení k Twitteru][Twitter]
* [Postupy: použití vlastního ověřování pro vaši aplikaci][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
