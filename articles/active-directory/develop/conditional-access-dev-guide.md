---
title: Informace pro vývojáře pro podmíněný přístup Azure Active Directory
description: Informace pro vývojáře a scénáře pro podmíněný přístup Azure AD
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
ms.openlocfilehash: ab6936d62aac5502d70239bacfbfd15bd6b793ab
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057519"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Informace pro vývojáře pro podmíněný přístup Azure Active Directory

Funkce podmíněného přístupu v Azure Active Directory (Azure AD) nabízí několika způsoby, které můžete použít k zabezpečení aplikace a chránit služby. Podmíněný přístup umožňuje vývojářům a podnikovými zákazníky chrání služby ve velké množství způsobů, včetně:

* Ověřování pomocí služby Multi-Factor Authentication
* Povolení Intune jenom registrovaná zařízení pro přístup k určité služby
* Rozsahy omezení umístění uživatele a IP adres

Další informace o všech možnostech podmíněného přístupu najdete v tématu [podmíněného přístupu v Azure Active Directory](../active-directory-conditional-access-azure-portal.md). 

Vývojářům vyvíjejícím aplikace pro službu Azure AD Tento článek ukazuje, jak můžete pomocí podmíněného přístupu a dozvíte se víc i o důsledcích přístup k prostředkům, které nemáte kontrolu nad, který může mít zásady podmíněného přístupu. Tento článek také zkoumá dopad podmíněného přístupu v tok on-behalf-of, webových aplikací, přístupu k Microsoft Graphu a volání rozhraní API.

Znalost [jeden](quickstart-v1-integrate-apps-with-azure-ad.md) a [víceklientské](howto-convert-app-to-be-multi-tenant.md) aplikace a [běžné vzory ověřování](authentication-scenarios.md) se předpokládá, že.

## <a name="how-does-conditional-access-impact-an-app"></a>Jak podmíněný přístup ovlivňuje aplikace?

### <a name="app-types-impacted"></a>Ovlivněné typy aplikací

Podmíněný přístup v nejběžnějších případech nezmění chování vaší aplikace nebo vyžaduje změny od vývojáře. Pouze v určitých případech při aplikaci nepřímo nebo v tichém režimu žádá token pro služby, aplikace vyžaduje změny kódu pro zpracování podmíněného přístupu "výzvy". Může být stejně jednoduché jako požadavek interaktivní přihlášení. 

Konkrétně následující scénáře vyžadují kód pro zpracování podmíněného přístupu "výzvy": 

* Aplikace přístup k Microsoft Graphu
* Aplikace provádí tok on-behalf-of
* Aplikace přístup k více službám nebo prostředkům
* Jednostránkové aplikace s využitím ADAL.js
* Funkce Web Apps volání prostředku

Podmíněný přístup pro aplikaci můžete použít zásady, ale můžete také použít k webovému rozhraní API přistupuje k aplikaci. Další informace o tom, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [rychlý start: vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](../conditional-access/app-based-mfa.md).

V závislosti na scénáři můžete použít podnikový zákazník a kdykoli odebrat zásady podmíněného přístupu. Aby vaše aplikace bude moct pracovat při použije nová zásada budete muset implementovat zpracování "problém". Následující příklady znázorňují zpracování challenge. 

### <a name="conditional-access-examples"></a>Příklady podmíněného přístupu

Některé scénáře vyžadují změny kódu pro zpracování podmíněného přístupu, zatímco ostatní funguje stejným způsobem. Tady je několik scénářů používání podmíněného přístupu provést ověřování službou Multi-Factor Authentication, která poskytuje určitý pohled na rozdíl.

* Zodpovídají za tvorbu aplikace s jedním tenantem iOS a aplikovat zásady podmíněného přístupu. Přihlásí uživatele a aplikace nebude žádat o přístup k rozhraní API. Když se uživatel přihlásí, je automaticky vyvolána zásady a uživatel nemusí provádět ověřování službou Multi-Factor Authentication (MFA). 
* Vytváření víceklientských webové aplikace, která používá pro přístup k Exchangi, mimo jiné služby Microsoft Graph. Podnikový zákazník, který přijme tuto aplikaci nastaví zásadu pro Exchange. Když webové aplikace žádá token pro MS Graphu, aplikace nebude výzvou k zajištění souladu se zásadami. Koncový uživatel přihlášený pomocí platné tokeny. Když se aplikace pokusí používat tento token proti Microsoft Graph pro přístup k datům systému Exchange, deklarace identity "problém" se vrátí do webové aplikace prostřednictvím ```WWW-Authenticate``` záhlaví. Aplikace pak může použít ```claims``` v nové žádosti a koncový uživatel se vyzve k dosažení souladu s podmínkami. 
* Vytváříte nativní aplikace, která používá pro přístup k podřízené API střední vrstvy služby. Podnikový zákazník ve společnosti tuto aplikaci používat nastavení uplatní zásady na příjem dat rozhraní API. Když koncový uživatel přihlásí, nativní aplikace požaduje přístup do střední vrstvy a odešle token. Střední vrstvy provádí tok on-behalf-of požádáte o přístup k rozhraní API pro příjem. V tomto okamžiku deklarace identity "problém" se zobrazí střední vrstvy. Střední vrstva před obrovskou výzvou – odešle zpět do nativní aplikace, které musí dodržovat zásady podmíněného přístupu.

### <a name="complying-with-a-conditional-access-policy"></a>V souladu se zásadami podmíněného přístupu

Pro několik topologií různé aplikace se vyhodnotí zásady podmíněného přístupu při vytvoření relace. Jak zásady podmíněného přístupu pracuje členitost aplikacemi a službami, závisí do značné míry bodu, kdy je vyvolána na scénář, který se snažíte dosáhnout.

Když se aplikace pokusí o přístup ke službě pomocí zásad podmíněného přístupu, setkat challenge podmíněného přístupu. Tento problém je zakódován do `claims` parametr, který je k dispozici ve odpověď ze služby Azure AD a Microsoft Graph. Tady je příklad tohoto parametru výzvy: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Vývojáři mohli využít tuto výzvu a připojte ho do nové žádosti do služby Azure AD. Předejte tento stav se zobrazí výzva end podnikat žádné kroky nezbytné k zajištění souladu se zásadami podmíněného přístupu. V těchto scénářích jsou vysvětleny specifika chyby a tom, jak extrahovat parametr. 

## <a name="scenarios"></a>Scénáře

### <a name="prerequisites"></a>Požadavky

Podmíněný přístup Azure AD je součástí funkce [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis#choose-an-edition). Další informace o požadavcích na licencování [sestava nelicencovaného využití](../active-directory-conditional-access-unlicensed-usage-report.md). Vývojáři se můžete zapojit [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), což zahrnuje bezplatné předplatné pro Enterprise Mobility Suite, který obsahuje Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Důležité informace týkající se konkrétních scénářů

Následující informace platí jenom v těchto scénářích podmíněného přístupu:

* Aplikace přístup k Microsoft Graphu
* Aplikace provádí tok on-behalf-of
* Aplikace přístup k více službám nebo prostředkům
* Jednostránkové aplikace s využitím ADAL.js

Následující části popisují běžných scénářů, které jsou složitější. Základní princip je podmíněný přístup, které zásady jsou vyhodnocovány v okamžiku vyžádání tokenu pro službu, která se má použít, pokud je přistupováno prostřednictvím Microsoft Graphu zásady podmíněného přístupu.

## <a name="scenario-app-accessing-microsoft-graph"></a>Scénář: Aplikace přístup k Microsoft Graphu

V tomto scénáři dozvíte, jak webová aplikace žádá o přístup k Microsoft Graphu. Zásady podmíněného přístupu v tomto případě může přiřadit ke službě SharePoint, Exchange nebo některé jiné službě, která je přístupná jako úlohy prostřednictvím Microsoft Graphu. V tomto příkladu předpokládejme, že je zásady podmíněného přístupu na Sharepoint Online.

![Aplikace přístup k Microsoft Graphu vývojový diagram](./media/conditional-access-dev-guide/app-accessing-microsoft-graph-scenario.png)

Aplikace poprvé požádá autorizace Microsoft graphu, která vyžaduje přístup k podřízené úlohy bez podmíněného přístupu. Požadavek bude úspěšné bez vyvolání všechny zásady a aplikace obdrží tokeny pro Microsoft Graph. V tomto okamžiku může aplikace použít přístupový token v požadavku nosiče pro požadovaný koncový bod. Nyní aplikace potřebuje přístup k Sharepointu Online koncového bodu Microsoft Graphu, například: `https://graph.microsoft.com/v1.0/me/mySite`

Aplikace už má platný token pro Microsoft Graph, tak nový požadavek lze provádět bez vystavení nový token. Tento požadavek selže a deklarace identity výzvu k vyvolání z Microsoft Graph ve formě HTTP 403 Zakázáno s ```WWW-Authenticate``` challenge.

Tady je příklad odpovědi: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

Před obrovskou výzvou – deklarace je uvnitř ```WWW-Authenticate``` hlavičky, která může být analyzován extrahovat parametr deklarací identity pro další požadavek. Jakmile se připojí k novou žádost, mohl vyhodnocení zásad podmíněného přístupu při přihlášení uživatele Azure AD a aplikace je nyní souladu se zásadami podmíněného přístupu. Opakující se požadavek na Sharepointu Online koncový bod bude úspěšná.

```WWW-Authenticate``` Záhlaví nemá strukturu jedinečný a není zrovna snadné analyzovat, aby bylo možné extrahovat hodnoty. Tady je krátké způsob, jak pomoct.

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

Ukázky kódu, které ukazují, jak zpracovat před obrovskou výzvou – deklarace identity, najdete [vzorový kód On-behalf-of](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) pro ADAL .NET.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scénář: Aplikace provádí tok on-behalf-of

V tomto scénáři provedeme tento případ, ve kterém nativní aplikace volá webové služby nebo rozhraní API. Pak tato služba nemá [toku "on-behalf-of"](authentication-scenarios.md#application-types-and-scenarios) volat podřízené služby. V našem případě jsme použili naše zásady podmíněného přístupu u podřízené služby (webové rozhraní API 2) a používají nativní aplikace namísto aplikace typu server/démon. 

![Aplikace provádí vývojový diagram on-behalf-of](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Počáteční žádosti o token pro webové rozhraní API 1 nevyzve koncového uživatele pro ověřování službou Multi-Factor Authentication jako webové rozhraní API 1 nemusí vždy volání rozhraní API pro příjem. Jakmile webové rozhraní API 1 pokusí k vyžádání tokenu on-behalf-of uživatele pro webové rozhraní API 2, požadavek selže, protože uživatel není přihlášený pomocí služby Multi-Factor authentication.

Azure AD vrátí odpověď HTTP s některé zajímavé údaje: 

> [!NOTE]
> V tomto případě je popis chyby ověřování službou Multi-Factor Authentication, ale neexistuje širokou škálu `interaction_required` možné týkající se podmíněného přístupu. 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Webové rozhraní API 1, Chyba zachycení `error=interaction_required`a jejich odeslání zpět `claims` výzvu pro desktopové aplikace. V tomto okamžiku může být desktopovou aplikaci novou `acquireToken()` zavolat a připojit `claims`challenge jako parametr řetězce dotazu navíc. Tato nová žádost o vyžaduje, aby uživatel provést ověřování službou Multi-Factor Authentication a pak tento nový token poslat zpět do webového rozhraní API 1 a dokončete tok on-behalf-of.

Vyzkoušejte si v tomto scénáři, najdete v tématu naše [ukázku kódu .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ukazuje, jak předat deklarace identity před obrovskou výzvou – zpět z webového rozhraní API 1 do nativní aplikace a vytvořit novou žádost o uvnitř klientské aplikace. 

## <a name="scenario-app-accessing-multiple-services"></a>Scénář: Aplikace přístup k více službám

V tomto scénáři provedeme případ, ve kterém webová aplikace přistupuje k dvě služby jeden z nich má přiřazené zásady podmíněného přístupu. V závislosti na vaší aplikace logiky může existovat cesta, ve kterém vaše aplikace nevyžaduje přístup na oba webové služby. V tomto scénáři pořadí, ve kterém můžete požádat o token hraje důležitou roli v prostředí koncového uživatele.

Předpokládejme, máme webové služby A a B a webová služba B má naše zásady podmíněného přístupu, které jsou použity. Během požadavku počáteční interaktivní ověřování vyžaduje souhlas pro obě služby, zásady podmíněného přístupu není potřeba ve všech případech. Pokud aplikace požaduje tokenu pro webovou službu B, je vyvolána zásady a následné žádosti pro webovou službu A také úspěšné následujícím způsobem.

![Aplikace přístup k více službám vývojový diagram](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Případně pokud aplikace pro webové služby A zpočátku žádá token, koncový uživatel se nevyvolá zásady podmíněného přístupu. To umožňuje vývojáři aplikace ovládací prvek prostředí koncového uživatele a vynutit zásady podmíněného přístupu, který má být volána ve všech případech. Složité případem je, pokud aplikace požaduje následně tokenu pro webovou službu B. V tomto okamžiku koncový uživatel musí dodržovat zásady podmíněného přístupu. Když se aplikace pokusí `acquireToken`, se můžou generovat následující chybu (znázorněný v následujícím diagramu): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Aplikace přístup k více službám požaduje nový token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Pokud aplikace používá knihovnu ADAL, nepovedlo se získat token je vždy opakovat interaktivně. Pokud dojde k této interaktivní žádosti, koncový uživatel má příležitost k zajištění souladu s podmíněným přístupem. To platí, pokud je žádost `AcquireTokenSilentAsync` nebo `PromptBehavior.Never` v takovém případě musí aplikace provádět interaktivní ```AcquireToken``` požadavku poskytnout příležitosti k zajištění souladu se zásadami koncového užívání. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scénář: Jednu stránku aplikace (SPA) pomocí ADAL.js

V tomto scénáři provedeme tak když máme jednostránkové aplikace (SPA) pomocí ADAL.js podmíněného přístupu, které jsou chráněné webové rozhraní API volat. Toto je jednoduchá architektura, ale má nějaké drobné rozdíly, které je potřeba vzít v úvahu při vývoji týkající se podmíněného přístupu.

V ADAL.js, existuje několik funkcí, které získat tokeny: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, a `acquireTokenRedirect(…)`. 

* `login()` Získá token ID prostřednictvím požadavek interaktivní přihlášení, ale ne získat přístupové tokeny pro libovolnou službu (včetně podmíněného přístupu, které jsou chráněné webové rozhraní API). 
* `acquireToken(…)` pak umožňuje bezobslužné získání přístupového tokenu, což znamená, že jej za žádných okolností nezobrazuje uživatelského rozhraní. 
* `acquireTokenPopup(…)` a `acquireTokenRedirect(…)` jsou obě používá interaktivně požádat o token prostředku znamená vždy zobrazí přihlašovacího uživatelského rozhraní.

Když aplikace potřebuje přístupového tokenu pro volání webového rozhraní API, se pokusí `acquireToken(…)`. Pokud je platnost tokenu relace nebo musíme souladu se zásadami podmíněného přístupu, pak bude *acquireToken* funkce selže a tato aplikace používá `acquireTokenPopup()` nebo `acquireTokenRedirect()`.

![Jednostránkové aplikace pomocí knihovny ADAL vývojový diagram](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Projděme si příklad se scénáři podmíněného přístupu. Koncový uživatel právě dostal se na webu a nemá relaci. Provádíme `login()` volání, získejte ID tokenu bez ověřování Multi-Factor Authentication. Poté uživatel stiskne tlačítko, které vyžaduje, aby aplikace na žádost o data z webového rozhraní API. Aplikace se pokouší provést `acquireToken()` volání ale selže, protože uživatel neprovedl ještě ověřování službou Multi-Factor Authentication a musí v souladu se zásadami podmíněného přístupu.

Azure AD odešle zpět odpověď HTTP, která následující: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Naše aplikace je potřeba zachytit `error=interaction_required`. Aplikace můžete použít, pak buď `acquireTokenPopup()` nebo `acquireTokenRedirect()` se stejným prostředkem. Uživatel musí provést ověření službou Multi-Factor Authentication. Poté, co uživatel dokončil vícefaktorové ověřování, objeví se v aplikaci nový přístupový token pro požadovaný prostředek.

Vyzkoušejte si v tomto scénáři, najdete v tématu naše [vzorový kód On-behalf-of SPA v JavaScriptu](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Tento vzorový kód používá zásady podmíněného přístupu a webového rozhraní API, které jste se zaregistrovali dříve SPA v JavaScriptu k předvedení tohoto scénáře. Ukazuje, jak správně zpracovat před obrovskou výzvou – deklarace identity a získání přístupového tokenu, který lze použít pro vaše webové rozhraní API. Alternativně checkout Obecné [vzorový kód Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) pro doprovodné materiály k Angular SPA


## <a name="see-also"></a>Další informace najdete v tématech

* Další informace o možnostech najdete v tématu [podmíněného přístupu v Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Ukázky kódu Azure AD najdete v tématu [úložiště Github se vzorovými vzorové kódy](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Další informace o ADAL sady SDK pro a přístup v referenční dokumentaci najdete v tématu [příručku knihovny](active-directory-authentication-libraries.md).
* Další informace o scénářích s více tenanty najdete v tématu [jak přihlásit uživatele pomocí vzoru více tenantů](howto-convert-app-to-be-multi-tenant.md).
