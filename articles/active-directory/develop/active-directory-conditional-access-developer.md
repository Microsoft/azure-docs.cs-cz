---
title: Průvodce pro vývojáře pro Azure Active Directory podmíněného přístupu
description: Scénáře pro podmíněný přístup k Azure AD a Průvodce pro vývojáře
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
editor: PatAltimore
ms.author: celested
ms.reviewer: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 922af0d860528902bc1eb36d72476852c67fcadf
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157435"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Průvodce pro vývojáře pro Azure Active Directory podmíněného přístupu

Azure nabízí několik způsobů, jak zabezpečit vaši aplikaci a chránit služby Active Directory (Azure AD). Jedním z těchto jedinečné funkce je podmíněného přístupu. Podmíněný přístup umožňuje vývojářům a podnikoví zákazníci ochraně služeb v mnoho způsobů, včetně:

* Ověřování pomocí služby Multi-Factor Authentication
* Povolení jenom Intune zaregistrovaná zařízení pro přístup k určitým službám
* Omezení umístění uživatele a IP rozsahy

Další informace o všech možnostech podmíněného přístupu najdete v tématu [podmíněného přístupu v Azure Active Directory](../active-directory-conditional-access-azure-portal.md). 

V tomto článku se zaměříme na jaké podmíněného přístupu znamená vývojářům vytvářet aplikace pro Azure AD. Předpokládá znalost [jeden](active-directory-integrating-applications.md) a [víceklientské](active-directory-devhowto-multi-tenant-overview.md) aplikace a [běžných vzorů ověřování](active-directory-authentication-scenarios.md).

Jsme vám podrobně dopad přístup k prostředkům, které máte řízení přes, které mohou mít zásady podmíněného přístupu. Kromě toho jsme dopad podmíněného přístupu v tok on-behalf-of, webové aplikace, prozkoumejte přístup k Microsoft Graph a volání rozhraní API.

## <a name="how-does-conditional-access-impact-an-app"></a>Jak podmíněný přístup ovlivňuje aplikace?

### <a name="app-types-impacted"></a>Typy aplikací dopad

Podmíněný přístup v nejběžnější případy nezmění chování aplikace nebo vyžaduje změny od vývojáře. Pouze v určitých případech při aplikaci nepřímo nebo tiché požadavky token pro služby, aplikace vyžaduje změny kódu pro zpracování podmíněného přístupu "výzvy". To může být stejně jednoduché jako provádění požadavek interaktivní přihlašování. 

Následující scénáře konkrétně vyžadovat kód pro zpracování podmíněného přístupu "výzvy": 

* Aplikace přístup k Microsoft Graph
* Provádění tok on-behalf-of aplikace
* Přístup k více služby nebo prostředky aplikace
* Jednostránkové aplikace pomocí ADAL.js
* Webové aplikace volání prostředku

Podmíněný přístup zásady můžete použít k aplikaci, ale můžete také použít k webovému rozhraní API přistupuje k aplikaci. Další informace o tom, jak nakonfigurovat zásady podmíněného přístupu, najdete v tématu [Začínáme s Azure Active Directory podmíněného přístupu](../active-directory-conditional-access-azure-portal-get-started.md).

V závislosti na scénáři můžete použít podnikových zákazníků a kdykoli odebrat zásady podmíněného přístupu. Pořadí pro vaši aplikaci, aby fungovaly i při použití nové zásady je nutné implementovat zpracování "výzvy". Následující příklady znázorňují výzvy zpracování. 

### <a name="conditional-access-examples"></a>Příklady podmíněného přístupu

Některé scénáře vyžadují změny kódu pro zpracování podmíněného přístupu, zatímco ostatní pracovat, jako je. Tady je několik scénářů, pomocí podmíněného přístupu udělat vícefaktorového ověřování, která poskytuje určitý pohled na rozdíl.

* Vytváříte jednoho klienta iOS aplikace a aplikovat zásady podmíněného přístupu. Aplikace přihlásí uživatel a nemá požádat o přístup k rozhraní API. Když se uživatel přihlásí, zásady se automaticky vyvolá, a uživatelé musí provést službu Multi-Factor authentication (MFA). 
* Vytváříte víceklientské webovou aplikaci, která používá Microsoft Graph pro přístup k systému Exchange, mezi další služby. Enterprise zákazníkovi, který přijme tuto aplikaci nastaví zásady na serveru Exchange. Pokud webová aplikace požádá o token pro MS Graph, aplikace nebude postiženy k zajištění souladu se zásadami. Koncový uživatel přihlášený pomocí platné tokeny. Když se aplikace pokusí použít tento token proti Microsoft Graph pro přístup k dat systému Exchange, deklarace identity "výzvu" je vrácen do webové aplikace pomocí ```WWW-Authenticate``` záhlaví. Aplikace pak může použít ```claims``` v nové žádosti o, a koncový uživatel se vyzve k dosažení souladu s podmínkami. 
* Vytváříte nativní aplikaci, která používá pro přístup k podřízené API střední vrstvy služby. Enterprise zákazníka ve firmě používat tuto aplikaci platí zásady pro podřízené rozhraní API. Když koncový uživatel přihlásí, nativní aplikaci požaduje přístup k střední vrstvy a odešle token. Střední vrstva provede tok on-behalf-of požádáte o přístup k rozhraní API pro příjem dat. V tomto okamžiku je předkládán deklarace identity "výzvu" střední vrstvy. Střední vrstva odešle výzvy zpět do nativní aplikace, který se musí v souladu se zásadami podmíněného přístupu.

### <a name="complying-with-a-conditional-access-policy"></a>V souladu se zásadami podmíněného přístupu

Pro několik topologií jiné aplikace. zásady podmíněného přístupu vyhodnocení při vytvoření relace. Jako zásady podmíněného přístupu na členitost aplikace a služby, bod, ve kterém je volána výraznou závisí na scénáři, který se snažíte provést.

Když se aplikace pokusí o přístup ke službě se zásadami podmíněného přístupu, může dojít k výzvy podmíněného přístupu. Tento problém je zakódován do `claims` parametr, který se dodává v odpovědi z Azure AD nebo Microsoft Graph. Tady je příklad tohoto parametru výzvy: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Vývojáři můžou trvat tento problém a přidá je do nový požadavek do služby Azure AD. Tento stav předávání vyzve k zadání koncového uživatele k provedení všech akcí, které jsou nezbytné k zajištění souladu se zásadami podmíněného přístupu. V následujících scénářích jsou vysvětleny podrobností chyby a extrahování parametr. 

## <a name="scenarios"></a>Scénáře

### <a name="prerequisites"></a>Požadavky

Podmíněný přístup pro Azure AD je součástí funkce [Azure AD Premium](../active-directory-whatis.md#choose-an-edition). Další informace o licencování požadavky [zprávu nelicencovaná využití](../active-directory-conditional-access-unlicensed-usage-report.md). Vývojáři se můžete zapojit [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), což zahrnuje bezplatné předplatné pro Enterprise Mobility Suite, které zahrnuje Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Důležité informace týkající se konkrétních scénářů

Následující informace platí pouze v těchto scénářích podmíněného přístupu:

* Aplikace přístup k Microsoft Graph
* Provádění tok on-behalf-of aplikace
* Přístup k více služby nebo prostředky aplikace
* Jednostránkové aplikace pomocí ADAL.js

V následujících částech budete získáme do běžné scénáře, ve kterých jsou složitější. Je základní princip funkce podmíněného přístupu, které se vyhodnocují zásady v době, kdy se požaduje token pro službu, která se má použít, pokud je přistupuje prostřednictvím Microsoft Graph zásady podmíněného přístupu.

## <a name="scenario-app-accessing-microsoft-graph"></a>Scénář: Aplikace přístup k Microsoft Graph

V tomto scénáři jsme provede případu při žádosti o přístup k webové aplikace do aplikace Microsoft Graph. Zásady podmíněného přístupu v takovém případě může přiřadit služby SharePoint, Exchange nebo jiné služby, které je přístupné jako zatížení prostřednictvím Microsoft Graph. V tomto příkladu předpokládejme, že je zásada podmíněného přístupu na Sharepoint Online.

![Aplikace přístup k Microsoft Graph vývojový diagram](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

Aplikaci poprvé požádá ověřování do aplikace Microsoft Graph, který vyžaduje přístup k podřízené úlohy bez podmíněného přístupu. Neproběhne bez vyvolání všechny zásady a aplikace obdrží tokeny pro Microsoft Graph. V tomto okamžiku může aplikace používat přístupový token v požadavku nosiče pro požadovaný koncový bod. Nyní aplikace potřebuje přístup k Sharepointu Online koncového bodu Microsoft Graph, například: `https://graph.microsoft.com/v1.0/me/mySite`

Aplikace již má platný token pro Microsoft Graph, aby mohl vykonávat novou žádost o bez vystavení nový token. Tento požadavek selže a je výzva deklarace identity vystavené Microsoft Graph ve formě protokolu HTTP 403 Zakázáno s ```WWW-Authenticate``` výzvu.

Tady je příklad odpovědi: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

Výzvy deklarací identity je uvnitř ```WWW-Authenticate``` záhlaví, který lze analyzovat k extrakci deklarací parametrů pro další požadavek. Jakmile připojí se k nový požadavek, Azure AD zná k vyhodnocení zásad podmíněného přístupu při přihlášení uživatele a aplikace je teď souladu se zásadami podmíněného přístupu. Opakující se požadavek na koncový bod služby Sharepoint Online úspěšné.

```WWW-Authenticate``` Záhlaví jedinečný struktura a není triviální analyzovat k extrahování hodnot. Zde je krátký metoda pomohou.

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
```

Ukázky kódu, které ukazují, jak zpracovat výzvy deklarace identity, najdete v části [ukázka kódu On-behalf-of](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) pro ADAL .NET.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scénář: Aplikace provádění tok on-behalf-of

V tomto scénáři jsme provede tento případ, ve kterém nativní aplikaci volání webové služby nebo API. Pak se tato služba nemá [toku "on-behalf-of"](active-directory-authentication-scenarios.md#application-types-and-scenarios) volat podřízené služby. V našem případě jsme provedli naše zásady podmíněného přístupu ke službě podřízené (webovém rozhraní API 2) a používají nativní aplikaci spíše než server/démon aplikace. 

![Vývojový diagram on-behalf-of provádění aplikací](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

Počáteční žádosti o token pro webové rozhraní API 1 nezobrazí výzvu koncového uživatele pro službu Multi-Factor authentication jako Web API 1 nemusí vždy dosáhl podřízené rozhraní API. Po 1 webové rozhraní API se pokusí požádat o token on-behalf-of uživatele pro webové rozhraní API 2, žádost skončí s chybou vzhledem k tomu, že uživatel není přihlášený pomocí služby Multi-Factor authentication.

Azure AD vrátí odpověď HTTP s některé zajímavé údaje: 

> [!NOTE]
> V této instanci je popis chyby služby Multi-Factor authentication, ale je širokou škálu `interaction_required` možné týkající se podmíněného přístupu. 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

V našem webové rozhraní API 1, jsme catch chyba `error=interaction_required`a odeslat zpět `claims` výzvu k desktopová aplikace. Desktopová aplikace v tomto okamžiku můžete provést novou `acquireToken()` zavolat a připojit `claims`problém jako parametr řetězce dotazu navíc. Tento nový požadavek vyžaduje, aby uživatel provést službu Multi-Factor authentication a pak tento nový token poslat zpět na Web API 1 a dokončete tok on-behalf-of.

Můžete vyzkoušet na tento scénář, najdete v našich [ukázka kódu .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ukazuje, jak předat výzvy deklarace identity zpět z webové rozhraní API 1 nativní aplikaci a vytvořit novou žádost o uvnitř klientské aplikace. 

## <a name="scenario-app-accessing-multiple-services"></a>Scénář: Aplikace přístup k několika služeb

V tomto scénáři jsme provede případ, ve kterém přistupuje k webové aplikaci dvě služby, z nichž jeden je přiřazen zásady podmíněného přístupu. V závislosti na logice aplikace můžou existovat cesta, ve kterém aplikace nevyžaduje přístup na oba webových služeb. V tomto scénáři pořadí, ve kterém můžete požádat o token hraje důležitou roli v činnost koncového uživatele.

Předpokládejme máme webové služby A a B a webová služba B má naše zásady podmíněného přístupu použije. Při požadavku počáteční interaktivní ověřování vyžaduje souhlas pro obě služby, zásady podmíněného přístupu není potřeba ve všech případech. Pokud aplikace požaduje token pro webovou službu B, je vyvolána zásady a následné žádosti pro webovou službu A také úspěšné následujícím způsobem.

![Aplikace přístup k více služeb vývojový diagram](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

Případně pokud se aplikace původně žádost token pro webové služby A, koncový uživatel nevyvolá zásady podmíněného přístupu. To umožňuje vývojáři aplikace k řízení činnost koncového uživatele a bez vynucení zásad podmíněného přístupu, který má být vyvolána ve všech případech. Složité případem je, pokud aplikace následně požadavky token pro webové služby serveru B. V tomto okamžiku koncový uživatel musí v souladu se zásadami podmíněného přístupu. Když se aplikace pokusí o `acquireToken`, to může generovat následující chybu (znázorněné v následujícím diagramu): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Přístup k více služeb, které žádají o nový token aplikace](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Pokud aplikace používá knihovnu ADAL, selhání získat token je vždy opakovat interaktivně. Když dojde k této žádosti interaktivní, koncový uživatel má možnost v souladu s podmíněným přístupem. Toto je hodnota true, pokud je požadavek `AcquireTokenSilentAsync` nebo `PromptBehavior.Never` v takovém případě aplikace potřebuje k provedení interaktivní ```AcquireToken``` požadavku poskytnout koncového užití příležitost k zajištění souladu se zásadami. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scénář: Jednu stránku aplikace (SPA) pomocí ADAL.js

V tomto scénáři jsme provede tak když máme jednostránkové aplikace (SPA), pomocí ADAL.js volání podmíněného přístupu k chráněným webového rozhraní API. To je jednoduché architekturou, ale má některé drobné odlišnosti, které je potřeba vzít v úvahu při vývoji kolem podmíněného přístupu.

V ADAL.js, existuje několik funkcí, které získat tokeny: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, a `acquireTokenRedirect(…)`. 

* `login()` Získá token ID prostřednictvím požadavek interaktivní přihlašování, ale není získali přístupové tokeny pro všechny služby (včetně podmíněného přístupu k chráněným webového rozhraní API). 
* `acquireToken(…)` pak lze bezobslužně získat token přístupu, což znamená, že se za žádných okolností nezobrazuje uživatelského rozhraní. 
* `acquireTokenPopup(…)` a `acquireTokenRedirect(…)` jsou obě slouží k vyžádání interaktivně token pro prostředek znamená budou vždy zobrazovat přihlašovací rozhraní.

Pokud aplikace potřebuje přístupového tokenu k volání webového rozhraní API, všechny pokusy `acquireToken(…)`. Pokud je platnost tokenu relace nebo potřebujeme, abyste dosáhli souladu se zásadami podmíněného přístupu, pak se *acquireToken* funkce selže a používá aplikace `acquireTokenPopup()` nebo `acquireTokenRedirect()`.

![Jednostránkové aplikace pomocí ADAL vývojový diagram](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Projděme příklad s náš scénář podmíněného přístupu. Koncový uživatel právě dostali v lokalitě a nemá relaci. Můžeme provádět `login()` volání, získání ID tokenu bez ověřování Multi-Factor authentication. Uživatel pak dotkne tlačítka, který aplikace vyžaduje, aby data žádosti z webového rozhraní API. Aplikace se pokouší provést `acquireToken()` volání ale selže, protože uživatel neprovedl ještě Multi-Factor authentication a musí být v souladu se zásadami podmíněného přístupu.

Azure AD zašle zpět odpověď HTTP, která následující: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Naše aplikace potřebuje k zachycení `error=interaction_required`. Aplikace pak může použít buď `acquireTokenPopup()` nebo `acquireTokenRedirect()` se stejným prostředkem. Uživatel bude muset provést službu Multi-Factor authentication. Po dokončení ověřování Multi-Factor authentication uživateli aplikace je vydán nový přístupový token pro požadovaný prostředek.

Můžete vyzkoušet na tento scénář, najdete v našich [ukázka kódu On-behalf-of JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Tento příklad používá zásady podmíněného přístupu a webového rozhraní API, které jste dříve zaregistrovali s JS SPA k předvedení tento scénář. Ukazuje, jak správně zpracovat výzvy deklarace identity a získat přístupový token, který lze použít pro webové rozhraní API. Případně, najdete v článku věnovaném Obecné [ukázka kódu Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) pokyny k úhlová SPA


## <a name="see-also"></a>Další informace najdete v tématech

* Další informace o možnostech najdete v tématu [podmíněného přístupu v Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Ukázky kódu více Azure AD, najdete v části [úložiště Github ukázek kódu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Další informace o ADAL SDK společnosti a přístup v referenční dokumentaci, najdete v části [příručku knihovny](active-directory-authentication-libraries.md).
* Další informace o scénářích více klientů naleznete v tématu [jak se přihlásit uživatele pomocí vzoru víceklientské](active-directory-devhowto-multi-tenant-overview.md).
