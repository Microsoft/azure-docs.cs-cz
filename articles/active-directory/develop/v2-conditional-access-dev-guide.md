---
title: Pokyny pro vývojáře podmíněného přístupu azure služby Active Directory
description: Pokyny pro vývojáře a scénáře pro podmíněný přístup Azure AD a platformu identit Microsoftu.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 03/16/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9d82840681450ec855cb35c8700da2a53b9dd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481445"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Pokyny pro vývojáře podmíněného přístupu azure služby Active Directory

Funkce podmíněného přístupu ve službě Azure Active Directory (Azure AD) nabízí jeden z několika způsobů, jak můžete použít k zabezpečení aplikace a ochraně služby. Podmíněný přístup umožňuje vývojářům a podnikovým zákazníkům chránit služby mnoha způsoby, včetně:

* Ověřování pomocí služby Multi-Factor Authentication
* Povolení přístupu ke konkrétním službám pouze zaregistrovaná zařízení Intune
* Omezení umístění uživatelů a rozsahů IP adres

Další informace o úplných možnostech podmíněného přístupu najdete [v tématu Podmíněný přístup ve službě Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Pro vývojáře, kteří vytvářejí aplikace pro Azure AD, tento článek ukazuje, jak můžete použít podmíněný přístup a dozvíte se také o dopadu přístupu k prostředkům, nad kterými nemáte kontrolu nad tím, že mohou mít zásady podmíněného přístupu použity. Článek také zkoumá důsledky podmíněného přístupu v toku jménem, webových aplikací, přístupu k Microsoft Graphu a volání rozhraní API.

Předpokládá se znalost [aplikací s jedním](quickstart-register-app.md) a více [klienty](howto-convert-app-to-be-multi-tenant.md) a [běžné vzory ověřování.](authentication-scenarios.md)

> [!NOTE]
> Použití této funkce vyžaduje licenci Azure AD Premium P1. Pokud chcete najít správnou licenci pro vaše požadavky, přečtěte si [porovnání obecně dostupných funkcí edic Free, Basic a Premium](https://azure.microsoft.com/pricing/details/active-directory/).
> Zákazníci s [licencemi Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mají také přístup k funkcím podmíněného přístupu.

## <a name="how-does-conditional-access-impact-an-app"></a>Jaký vliv má podmíněný přístup na aplikaci?

### <a name="app-types-impacted"></a>Ovlivněné typy aplikací

Ve většině běžných případů podmíněný přístup nezmění chování aplikace nebo vyžaduje žádné změny od vývojáře.Pouze v určitých případech, kdy aplikace nepřímo nebo tiše požaduje token pro službu, aplikace vyžaduje změny kódu pro zpracování podmíněný přístup "výzvy".Může být stejně jednoduché jako provedení interaktivní žádosti o přihlášení.

Konkrétně následující scénáře vyžadují kód pro zpracování podmíněného přístupu "výzvy":

* Aplikace provádějící tok jménem
* Aplikace, které přistupují k více službám/prostředkům
* Jednostránkové aplikace používající msal.js
* Webové aplikace volající do prostředku

Zásady podmíněného přístupu lze použít na aplikaci, ale také na webové rozhraní API, ke kterým vaše aplikace přistupuje. Další informace o konfiguraci zásad podmíněného přístupu najdete v [tématu Úvodní příručka: Vyžadovat vícefaktorové informace pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](../conditional-access/app-based-mfa.md).

V závislosti na scénáři může podnikový zákazník kdykoli použít a odebrat zásady podmíněného přístupu. Aby vaše aplikace mohla fungovat i při použití nové zásady, je třeba implementovat zpracování "výzvy". Následující příklady ilustrují zpracování výzvy.

### <a name="conditional-access-examples"></a>Příklady podmíněného přístupu

Některé scénáře vyžadují změny kódu pro zpracování podmíněného přístupu, zatímco jiné fungují tak, jak jsou. Zde je několik scénářů pomocí podmíněného přístupu k provedení vícefaktorového ověřování, které poskytuje určitý přehled o rozdílu.

* Vytváříte aplikaci pro iOS s jedním tenantem a používáte zásady podmíněného přístupu. Aplikace se přihlásí k uživateli a nepožádá o přístup k rozhraní API. Když se uživatel přihlásí, zásada je automaticky vyvolána a uživatel potřebuje provést vícefaktorové ověřování (MFA). 
* Vytváříte nativní aplikaci, která používá službu střední vrstvy pro přístup k rozhraní API pro příjem dat. Podnikový zákazník ve společnosti používající tuto aplikaci použije zásady pro rozhraní API pro příjem dat. Když se koncový uživatel přihlásí, nativní aplikace požádá o přístup ke střední vrstvě a odešle token. Střední vrstva provádí jménem toku požádat o přístup k rozhraní API pro příjem dat. V tomto okamžiku je "výzva" nároky prezentována střední vrstvě. Střední vrstva odešle výzvu zpět do nativní aplikace, která musí splňovat zásady podmíněného přístupu.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph má zvláštní aspekty při vytváření aplikací v prostředích podmíněného přístupu. Obecně platí, že mechanismy podmíněného přístupu se chovají stejně, ale zásady, které se uživatelům zobrazí, budou založeny na podkladových datech, která vaše aplikace požaduje z grafu. 

Konkrétně všechny obory Microsoft Graph představují některé datové sady, které mohou jednotlivě použít zásady. Vzhledem k tomu, že zásady podmíněného přístupu jsou přiřazeny konkrétní datové sady, Azure AD bude vynucovat zásady podmíněného přístupu na základě dat za Graph – spíše než graph sám.

Pokud například aplikace požaduje následující obory aplikace Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Aplikace může očekávat, že jejich uživatelé splní všechny zásady stanovené na rezervacích a výměně. Některé obory mohou mapovat na více datových sad, pokud uděluje přístup. 

### <a name="complying-with-a-conditional-access-policy"></a>Dodržování zásad podmíněného přístupu

Pro několik různých topologie aplikace zásady podmíněného přístupu se vyhodnotí při vytvoření relace. Jako zásady podmíněného přístupu pracuje na rozlišovací schopnost aplikací a služeb, bod, ve kterém je vyvolána závisí do značné míry na scénáři, který se snažíte dosáhnout.

Když se vaše aplikace pokusí o přístup ke službě pomocí zásad podmíněného přístupu, může se vyskytnat výzva podmíněného přístupu. Tato výzva je zakódována v parametru, `claims` který přichází v odpovědi z Azure AD. Zde je příklad tohoto parametru výzvy: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Vývojáři můžou přijmout tuto výzvu a připojit ji k novému požadavku do Azure AD. Předání tohoto stavu vyzve koncového uživatele k provedení jakékoli akce nezbytné ke splnění zásadpodmíněného přístupu. V následujících scénářích jsou vysvětleny podrobnosti o chybě a jak extrahovat parametr.

## <a name="scenarios"></a>Scénáře

### <a name="prerequisites"></a>Požadavky

Podmíněný přístup Azure AD je funkce zahrnutá ve [službě Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Zákazníci s [licencemi Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mají také přístup k funkcím podmíněného přístupu.

### <a name="considerations-for-specific-scenarios"></a>Důležité informace pro konkrétní scénáře

Následující informace platí pouze v těchto scénářích podmíněného přístupu:

* Aplikace provádějící tok jménem
* Aplikace, které přistupují k více službám/prostředkům
* Jednostránkové aplikace používající msal.js

V následujících částech se popisují běžné scénáře, které jsou složitější. Základní princip fungování je podmíněného přístupu zásady jsou vyhodnocovány v době, kdy je požadován token pro službu, která má zásady podmíněného přístupu použít.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scénář: Aplikace provádějící tok jménem

V tomto scénáři procházíme případ, ve kterém nativní aplikace volá webovou službu nebo rozhraní API. Na druhé straně tato služba provádí tok "jménem" volat navazující služby. V našem případě jsme použili zásady podmíněného přístupu na příjem služby (Web API 2) a používáme nativní aplikaci, nikoli server/daemon aplikace. 

![Aplikace provádějící vývojový diagram](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Počáteční požadavek na token pro webové rozhraní API 1 nevyzve koncového uživatele k vícefaktorovému ověřování, protože webové rozhraní API 1 nemusí vždy sáhnout do rozhraní API pro příjem dat. Jakmile web rozhraní API 1 pokusí požádat o token jménem uživatele pro webové rozhraní API 2, požadavek se nezdaří, protože uživatel nebyl přihlášen s vícefaktorové ověřování.

Azure AD vrátí odpověď HTTP s některými zajímavými daty:

> [!NOTE]
> V tomto případě je to vícefaktorové ověřování popis chyby, `interaction_required` ale existuje široká škála možných týkajících se podmíněného přístupu.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Ve webovém rozhraní API `error=interaction_required`1 zachytíme `claims` chybu a odešleme výzvu zpět do aplikace klasické pracovní plochy. V tomto okamžiku aplikace klasické pracovní `acquireToken()` plochy můžete `claims`provést nové volání a připojit výzvu jako další parametr řetězce dotazu. Tento nový požadavek vyžaduje, aby uživatel provést vícefaktorové ověřování a potom odeslat tento nový token zpět do webového rozhraní API 1 a dokončit tok jménem.

Chcete-li vyzkoušet tento scénář, naleznete v naší [ukázce kódu .NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Ukazuje, jak předat výzvu deklarací identity zpět z webového rozhraní API 1 do nativní aplikace a vytvořit nový požadavek uvnitř klientské aplikace.

## <a name="scenario-app-accessing-multiple-services"></a>Scénář: Přístup k aplikaci k více službám

V tomto scénáři procházíme případ, ve kterém webová aplikace přistupuje ke dvěma službám, z nichž jedna má přiřazenu zásadu podmíněného přístupu. V závislosti na logice aplikace může existovat cesta, ve které vaše aplikace nevyžaduje přístup k oběma webovým službám. V tomto scénáři pořadí, ve kterém požadujete token hraje důležitou roli v prostředí koncového uživatele.

Předpokládejme, že máme webovou službu A a B a webová služba B má naše zásady podmíněného přístupu. Zatímco počáteční požadavek interaktivního authu vyžaduje souhlas pro obě služby, zásady podmíněného přístupu není vyžadováno ve všech případech. Pokud aplikace požaduje token pro webovou službu B, pak je vyvolána zásada a následné požadavky na webovou službu A také úspěšné následujícím způsobem.

![Aplikace přistupující k vývojovému diagramu více služeb](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Případně pokud aplikace zpočátku požaduje token pro webovou službu A, koncový uživatel nevyvolá zásady podmíněného přístupu. To umožňuje vývojáři aplikace řídit prostředí koncového uživatele a ne vynutit zásady podmíněného přístupu, které mají být vyvolány ve všech případech. Složitý případ je, pokud aplikace následně požaduje token pro webovou službu B. V tomto okamžiku musí koncový uživatel dodržovat zásady podmíněného přístupu. Když se aplikace `acquireToken`pokusí , může generovat následující chybu (znázorněno na následujícím diagramu):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplikace přistupující k více službám požadujícím nový token](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Pokud aplikace používá knihovnu MSAL, selhání získání tokenu je vždy opakován interaktivně. Dojde-li k tomuto interaktivnímu požadavku, koncový uživatel má možnost dodržovat podmíněný přístup. To platí, pokud požadavek je `AcquireTokenSilentAsync` nebo `PromptBehavior.Never` v takovém případě ```AcquireToken``` aplikace potřebuje provést interaktivní požadavek, aby koncový uživatel možnost dodržovat zásady.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Scénář: Jednostránková aplikace (SPA) pomocí souboru MSAL.js

V tomto scénáři procházíme případ, když máme jednostránkovou aplikaci (SPA), pomocí MSAL.js volat webové rozhraní API chráněné podmíněný přístup. Jedná se o jednoduchou architekturu, ale má některé nuance, které je třeba vzít v úvahu při vývoji kolem podmíněného přístupu.

V souboru MSAL.js existuje několik funkcí, `loginPopup()` `acquireTokenSilent(...)`které `acquireTokenPopup(…)`získávají `acquireTokenRedirect(…)`tokeny: , , a .

* `loginPopup()`získá token ID prostřednictvím interaktivního požadavku na přihlášení, ale nezíská přístupové tokeny pro žádnou službu (včetně webového rozhraní API chráněného pro podmíněný přístup).
* `acquireTokenSilent(…)`potom lze použít k tiše získat přístupový token, což znamená, že nezobrazuje uI v žádném případě.
* `acquireTokenPopup(…)`a `acquireTokenRedirect(…)` oba se používají k interaktivní žádost o token pro prostředek, což znamená, že vždy zobrazit přihlašovací umělá nastavení.

Když aplikace potřebuje přístupový token pro volání webového `acquireTokenSilent(…)`rozhraní API, pokusí se o . Pokud vypršela platnost relace tokenu nebo potřebujeme dodržovat zásady podmíněného přístupu, `acquireTokenPopup()` pak `acquireTokenRedirect()`funkce *acquireToken* selže a aplikace používá nebo .

![Jednostránková aplikace pomocí vývojového diagramu MSAL](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Pojďme si projít příklad s naším scénářem podmíněného přístupu. Koncový uživatel právě přistál na webu a nemá relaci. Provedeme `loginPopup()` volání, získáme token ID bez vícefaktorového ověřování. Pak uživatel stiskne tlačítko, které vyžaduje, aby aplikace požadovat data z webového rozhraní API. Aplikace se pokusí `acquireTokenSilent()` provést volání, ale selže, protože uživatel ještě neprovedl vícefaktorové ověřování a musí dodržovat zásady podmíněného přístupu.

Azure AD odešle zpět následující odpověď HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Naše aplikace potřebuje `error=interaction_required`zachytit . Aplikace pak můžete `acquireTokenPopup()` použít `acquireTokenRedirect()` buď nebo na stejný prostředek. Uživatel je nucen provést vícefaktorové ověřování. Poté, co uživatel dokončí vícefaktorové ověřování, aplikace je vydán nový přístupový token pro požadovaný prostředek.

Chcete-li vyzkoušet tento scénář, podívejte se na naše [JS SPA In-behalf-of ukázky kódu](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Tato ukázka kódu používá zásady podmíněného přístupu a webové rozhraní API, které jste zaregistrovali dříve u js spa k předvedení tohoto scénáře. Ukazuje, jak správně zpracovat výzvu deklarací identity a získat přístupový token, který lze použít pro webové rozhraní API. Případně můžete provést obecný [vzorek kódu Angular.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) pro navádění na úhlovém spa

## <a name="see-also"></a>Viz také

* Další informace o možnostech najdete v [tématu Podmíněný přístup ve službě Azure Active Directory](/azure/active-directory/conditional-access/overview).
* Další ukázky kódu Azure AD najdete v [tématu ukázky](sample-v2-code.md).
* Další informace o sadě MSAL SDK a přístup k referenční dokumentaci naleznete v [tématu Microsoft Authentication Library overview](msal-overview.md).
* Další informace o scénářích s více tenanty najdete v tématu [Jak přihlašovat uživatele pomocí vzoru více klientů](howto-convert-app-to-be-multi-tenant.md).
* Přečtěte si další informace o [podmíněném přístupu a zabezpečení přístupu k aplikacím IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
