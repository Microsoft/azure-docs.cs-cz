---
title: Ověřování a autorizace
description: Přečtěte si o integrované podpoře ověřování a autorizace ve službě Azure App Service a o tom, jak může pomoci zabezpečit vaši aplikaci před neoprávněným přístupem.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 825d113bbe081ba6fb85da19ff6449824db92d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475387"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service

> [!NOTE]
> V současné době AAD V2 (včetně MSAL) není podporována pro Služby Azure App Services a Azure functions. Zkontrolujte, zda neobsahuje aktualizace.
>

Služba Azure App Service poskytuje integrovanou podporu ověřování a autorizace, takže můžete přihlašovat uživatele a přistupovat k datům zápisem minimálního nebo žádného kódu ve webové aplikaci, rozhraní RESTful API a mobilní back-endu a také [funkce Azure](../azure-functions/functions-overview.md). Tento článek popisuje, jak služba App Service pomáhá zjednodušit ověřování a autorizaci pro vaši aplikaci.

Zabezpečené ověřování a autorizace vyžadují hluboké pochopení zabezpečení, včetně federace, šifrování, správy [webových tokenů JSON (JWT),](https://wikipedia.org/wiki/JSON_Web_Token) [typů grantů](https://oauth.net/2/grant-types/)a tak dále. Služba App Service poskytuje tyto nástroje, takže můžete strávit více času a energie na poskytování obchodní hodnoty pro svého zákazníka.

> [!IMPORTANT]
> Není nutné používat službu App Service pro AuthN/AuthO. Můžete použít přibalené funkce zabezpečení ve webovém rámci volby, nebo si můžete napsat vlastní nástroje. Mějte však na paměti, že [Chrome 80 provádí zásadní změny v implementaci samesite pro soubory cookie](https://www.chromestatus.com/feature/5088147346030592) (datum vydání kolem března 2020) a vlastní vzdálené ověřování nebo jiné scénáře, které se spoléhají na odesílání souborů cookie mezi stránkami, se mohou po aktualizaci prohlížečů Chrome v klientském prohlížeči Chrome zlomit. Řešení je složité, protože potřebuje podporovat různé chování Stejného webu pro různé prohlížeče. 
>
> Verze ASP.NET Core 2.1 a vyšší hostované službou App Service jsou již opraveny pro tuto narušující změnu a vhodně zpracovávají prohlížeče Chrome 80 a starší prohlížeče. Kromě toho se stejná oprava pro ASP.NET Framework 4.7.2 nasadí na instance služby App Service v průběhu ledna 2020. Další informace, včetně toho, jak zjistit, jestli vaše aplikace obdržela opravu, najdete v [tématu Aktualizace souborů cookie služby Azure App Service SameSite](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

Informace specifické pro nativní mobilní aplikace najdete [v tématu Ověřování uživatelů a autorizace pro mobilní aplikace pomocí služby Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to funguje

Modul ověřování a autorizace běží ve stejné izolovaném prostoru jako kód aplikace. Pokud je povolena, každý příchozí požadavek HTTP prochází před zpracováním kódu aplikace.

![](media/app-service-authentication-overview/architecture.png)

Tento modul zpracovává několik věcí pro vaši aplikaci:

- Ověřuje uživatele pomocí zadaného zprostředkovatele.
- Ověřuje, ukládá a aktualizuje tokeny.
- Spravuje ověřenou relaci.
- Vloží informace o identitě do hlavičky požadavku.

Modul běží odděleně od kódu aplikace a je nakonfigurován pomocí nastavení aplikace. Nejsou vyžadovány žádné sady SDK, konkrétní jazyky nebo změny kódu aplikace. 

### <a name="user-claims"></a>Deklarace identity uživatelů

Pro všechny jazykové architektury App Service zpřístupňuje deklarace identity uživatele pro váš kód jejich vložením do hlavičky požadavku. Pro ASP.NET aplikací 4.6 služba App Service naplní [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) s deklaracemi ověřeného uživatele, takže `[Authorize]` můžete postupovat podle standardního vzoru kódu .NET, včetně atributu. Podobně pro aplikace PHP App Service `_SERVER['REMOTE_USER']` naplní proměnnou. U aplikací java jsou nároky [přístupné z servletu Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Pro [funkce](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` Azure , není hydratované pro kód .NET, ale stále můžete najít deklarace identity uživatele v hlavičkách požadavku.

Další informace naleznete v [tématu Deklarace identity uživatelů aplikace Access](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Úložiště tokenů

Služba App Service poskytuje integrované úložiště tokenů, což je úložiště tokenů, které jsou přidruženy k uživatelům vašich webových aplikací, api nebo nativních mobilních aplikací. Když povolíte ověřování s libovolným poskytovatelem, toto úložiště tokenů je okamžitě k dispozici pro vaši aplikaci. Pokud kód aplikace potřebuje přístup k datům od těchto poskytovatelů jménem uživatele, například: 

- příspěvek na facebookovou časovou osu ověřeného uživatele
- čtení podnikových dat uživatele pomocí rozhraní Microsoft Graph API

Obvykle je nutné napsat kód pro shromažďování, ukládání a aktualizaci těchto tokenů ve vaší aplikaci. S úložištěm tokenů stačí [načíst tokeny,](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) když je potřebujete, a [sdělit službě App Service, aby je aktualizovala,](app-service-authentication-how-to.md#refresh-identity-provider-tokens) když se stanou neplatnými. 

Id tokeny, přístupové tokeny a obnovovací tokeny uložené v mezipaměti pro ověřenou relaci a jsou přístupné pouze přidruženému uživateli.  

Pokud ve své aplikaci nepotřebujete pracovat s tokeny, můžete úložiště tokenů zakázat.

### <a name="logging-and-tracing"></a>Protokolování a trasování

Pokud [povolíte protokolování aplikací](troubleshoot-diagnostic-logs.md), zobrazí se trasování ověřování a autorizace přímo v souborech protokolu. Pokud se zobrazí chyba ověřování, kterou jste nečekali, můžete pohodlně najít všechny podrobnosti při pohledu do existujících protokolů aplikací. Pokud povolíte [trasování neúspěšných požadavků](troubleshoot-diagnostic-logs.md), můžete přesně zjistit, jakou roli mohl modul ověřování a autorizace přehrát v neúspěšném požadavku. V protokolech trasování vyhledejte odkazy na `EasyAuthModule_32/64`modul s názvem . 

## <a name="identity-providers"></a>Zprostředkovatelé identit

Služba App Service používá [federovní identitu](https://en.wikipedia.org/wiki/Federated_identity), ve které poskytovatel identity třetí strany spravuje identity uživatelů a tok ověřování za vás. Ve výchozím nastavení je k dispozici pět zprostředkovatelů identit: 

| Poskytovatel | Koncový bod přihlášení |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Účet Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Pokud povolíte ověřování a autorizaci s jedním z těchto zprostředkovatelů, jeho přihlašovací koncový bod je k dispozici pro ověřování uživatelů a pro ověření ověřovacích tokenů od zprostředkovatele. Uživatelům můžete snadno poskytnout libovolný počet těchto možností přihlášení. Můžete také integrovat jiného zprostředkovatele identity nebo [vlastní řešení identity][custom-auth].

## <a name="authentication-flow"></a>Tok ověřování

Tok ověřování je stejný pro všechny zprostředkovatele, ale liší se v závislosti na tom, zda se chcete přihlásit pomocí sady SDK zprostředkovatele:

- Bez zprostředkovatele SDK: Aplikace deleguje federované přihlášení ke službě App Service. To je obvykle případ aplikací prohlížeče, které mohou uživateli prezentovat přihlašovací stránku poskytovatele. Serverový kód spravuje proces přihlášení, takže se také nazývá _tok směrovaný serverem_ nebo _tok serveru_. Tento případ se vztahuje na aplikace prohlížeče. Platí také pro nativní aplikace, které přihlašují uživatele pomocí sady SDK klienta mobilních aplikací, protože sada SDK otevře webové zobrazení pro přihlášení uživatelů pomocí ověřování služby App Service. 
- S poskytovatelem SDK: Aplikace přihlásí uživatele k poskytovateli ručně a potom odešle ověřovací token do služby App Service pro ověření. To je obvykle případ aplikací bez prohlížeče, které nemohou uživateli prezentovat přihlašovací stránku poskytovatele. Kód aplikace spravuje proces přihlášení, takže se také nazývá _tok řízený klientem_ nebo _tok klienta_. Tento případ se vztahuje na REST API, [Azure Functions](../azure-functions/functions-overview.md)a JavaScript prohlížeče klientů, stejně jako aplikace prohlížeče, které vyžadují větší flexibilitu v procesu přihlášení. Platí také pro nativní mobilní aplikace, které přihlašují uživatele pomocí sady SDK poskytovatele.

> [!NOTE]
> Volání z důvěryhodné aplikace prohlížeče ve službě App Service volá jiné rozhraní REST API ve službě App Service nebo [Azure Functions](../azure-functions/functions-overview.md) lze ověřit pomocí toku řízeného serverem. Další informace naleznete [v tématu Přizpůsobení ověřování a autorizace ve službě App Service](app-service-authentication-how-to.md).
>

V následující tabulce jsou uvedeny kroky toku ověřování.

| Krok | Bez poskytovatele SDK | S poskytovatelem SDK |
| - | - | - |
| 1. Přihlásit uživatele | Přesměruje klienta na . `/.auth/login/<provider>` | Klientský kód přihlásí uživatele přímo pomocí sady SDK zprostředkovatele a obdrží ověřovací token. Další informace naleznete v dokumentaci poskytovatele. |
| 2. Post-autentizace | Zprostředkovatel přesměruje `/.auth/login/<provider>/callback`klienta na . | Klientský kód [publikuje token od poskytovatele](app-service-authentication-how-to.md#validate-tokens-from-providers) k `/.auth/login/<provider>` ověření. |
| 3. Vytvoření ověřené relace | Služba App Service přidá ověřený soubor cookie k odpovědi. | Služba App Service vrátí vlastní ověřovací token do kódu klienta. |
| 4. Poskytování ověřeného obsahu | Klient zahrnuje ověřovací soubor cookie v následných požadavcích (automaticky zpracovávaných prohlížečem). | Klientský kód představuje `X-ZUMO-AUTH` ověřovací token v hlavičce (automaticky zpracovány sady SDK klienta mobilních aplikací). |

V klientských prohlížečích může služba App Service `/.auth/login/<provider>`automaticky nasměrovat všechny neověřené uživatele na aplikaci . Můžete také uživatelům představit jeden `/.auth/login/<provider>` nebo více odkazů pro přihlášení k aplikaci pomocí svého zvoleného poskytovatele.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Chování autorizace

Na [webu Azure Portal](https://portal.azure.com)můžete nakonfigurovat autorizaci služby App Service s řadou chování, když není ověřen příchozí požadavek.

![](media/app-service-authentication-overview/authorization-flow.png)

Následující nadpisy popisují možnosti.

### <a name="allow-anonymous-requests-no-action"></a>Povolit anonymní požadavky (žádná akce)

Tato možnost odloží autorizaci neověřeného provozu na kód aplikace. U ověřených požadavků služba App Service také předává informace o ověřování v hlavičkách HTTP. 

Tato možnost poskytuje větší flexibilitu při zpracování anonymních požadavků. Umožňuje například uživatelům [prezentovat více poskytovatelů přihlášení.](app-service-authentication-how-to.md#use-multiple-sign-in-providers) Je však nutné napsat kód. 

### <a name="allow-only-authenticated-requests"></a>Povolit pouze ověřené požadavky

Možnost **Přihlásit se \<pomocí zprostředkovatele>**. Služba App Service přesměruje `/.auth/login/<provider>` všechny anonymní požadavky na poskytovatele, který zvolíte. Pokud anonymní požadavek pochází z nativní mobilní `HTTP 401 Unauthorized`aplikace, vrácená odpověď je .

S touto možností nemusíte do aplikace psát žádný ověřovací kód. Jemnější autorizace, jako je například autorizace specifická pro roli, může být zpracována kontrolou deklarací identity uživatele (viz [Deklarace uživatelů aplikace Access](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Omezení přístupu tímto způsobem platí pro všechna volání do vaší aplikace, což nemusí být žádoucí pro aplikace, které chtějí veřejně dostupnou domovskou stránku, jako v mnoha jednostránkových aplikacích.

## <a name="more-resources"></a>Další zdroje informací

[Kurz: Ověření a autorizace uživatelů od konce ve službě Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Kurz: Ověření a autorizace uživatelů od konce ve službě Azure App Service pro Linux](containers/tutorial-auth-aad.md)  
[Přizpůsobení ověřování a autorizace ve službě App Service](app-service-authentication-how-to.md)

Návody pro konkrétního poskytovatele:

* [Konfigurace aplikace pro použití přihlášení Azure Active Directory][AAD]
* [Konfigurace aplikace pro použití přihlášení k Facebooku][Facebook]
* [Konfigurace aplikace pro použití přihlášení ke Googlu][Google]
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft][MSA]
* [Konfigurace aplikace pro použití přihlášení k Twitteru][Twitter]
* [Postup: Použití vlastního ověřování pro vaši aplikaci][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
