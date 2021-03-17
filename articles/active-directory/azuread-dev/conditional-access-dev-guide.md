---
title: Pokyny pro vývojáře podmíněného přístupu Azure AD
description: Pokyny a scénáře pro vývojáře pro podmíněný přístup Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: b08b07ebcd6e45e95e23dff8686148889e9d4b39
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173915"
---
# <a name="developer-guidance-for-the-azure-active-directory-conditional-access-feature"></a>Pokyny pro vývojáře funkce podmíněného přístupu Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
> Verzi tohoto článku pro Microsoft Identity Platform najdete v tématu [pokyny pro vývojáře pro Azure Active Directory podmíněný přístup](../develop/v2-conditional-access-dev-guide.md).

Funkce podmíněného přístupu v Azure Active Directory (Azure AD) nabízí jeden z několika způsobů, jak můžete použít k zabezpečení aplikace a ochraně služby. Podmíněný přístup umožňuje vývojářům a podnikovým zákazníkům chránit služby mnoha různými způsoby, včetně těchto:

* Ověřování pomocí služby Multi-Factor Authentication
* Povoluje přístup ke konkrétním službám jenom zařízením zaregistrovaným v Intune.
* Omezení umístění uživatelů a rozsahů IP adres

Další informace o úplných funkcích podmíněného přístupu najdete v tématu [co je podmíněný přístup](../conditional-access/overview.md).

Pro vývojáře, kteří sestavují aplikace pro Azure AD, Tento článek ukazuje, jak můžete použít podmíněný přístup, a Vy se dozvíte o dopadu na přístup k prostředkům, u kterých nemáte kontrolu nad tím, že je možné použít zásady podmíněného přístupu. Tento článek také zkoumá důsledky podmíněného přístupu v toku, Web Apps, přístup k Microsoft Graph a volání rozhraní API.

Předpokládá se znalost [jednotlivých a víceklientské](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aplikací a [běžných vzorů ověřování](v1-authentication-scenarios.md) .

## <a name="how-does-conditional-access-impact-an-app"></a>Jak podmíněný přístup ovlivňuje aplikaci?

### <a name="app-types-impacted"></a>Ovlivněné typy aplikací

V nejběžnějších případech podmíněný přístup nemění chování aplikace nebo vyžaduje žádné změny od vývojáře. Pouze v některých případech, pokud aplikace nepřímo nebo tiše žádá o token pro službu, vyžaduje aplikace změny kódu pro zpracování výzev podmíněného přístupu. Může to být jednoduché jako při provádění interaktivního přihlašovacího požadavku.

Konkrétně následující scénáře vyžadují kód pro zpracování výzev podmíněného přístupu:

* Aplikace, které provádějí tok za běhu
* Aplikace, které přistupují k několika službám nebo prostředkům
* Jednostránkové aplikace používající ADAL.js
* Web Apps volání prostředku

Zásady podmíněného přístupu se dají použít pro aplikaci, ale dají se použít i pro webové rozhraní API, ke kterému přistupuje aplikace. Další informace o tom, jak nakonfigurovat zásady podmíněného přístupu, najdete v tématu [běžné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md).

V závislosti na scénáři může podnikový zákazník kdykoli použít a odebrat zásady podmíněného přístupu. Aby vaše aplikace pokračovala v provozu při použití nové zásady, je nutné implementovat zpracování výzvy. Následující příklady ilustrují zpracování výzvy.

### <a name="conditional-access-examples"></a>Příklady podmíněného přístupu

Některé scénáře vyžadují změny kódu pro zpracování podmíněného přístupu, zatímco ostatní fungují tak, jak jsou. Tady je několik scénářů, které pomocí podmíněného přístupu přistupují k Multi-Factor Authentication, který poskytuje určitý přehled o rozdílech.

* Vytváříte aplikaci pro iOS s jedním klientem a použijete zásady podmíněného přístupu. Aplikace se přihlásí uživateli a nepožaduje přístup k rozhraní API. Když se uživatel přihlásí, zásada se automaticky vyvolá a uživatel musí provádět službu Multi-Factor Authentication (MFA). 
* Vytváříte nativní aplikaci, která používá službu střední vrstvy pro přístup k rozhraní API pro příjem dat. Podnikový zákazník na společnosti, který používá tuto aplikaci, používá zásadu pro rozhraní API pro příjem dat. Když se koncový uživatel přihlásí, nativní aplikace požaduje přístup ke střední vrstvě a pošle token. Střední vrstva provádí tok za účelem vyžádání přístupu k rozhraní API pro příjem dat. V tomto okamžiku se do střední vrstvy zobrazí deklarace "výzva". Střední vrstva pošle výzvu zpátky do nativní aplikace, která musí vyhovovat zásadám podmíněného přístupu.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph má při sestavování aplikací v prostředí podmíněného přístupu zvláštní předpoklady. Obecně platí, že mechanismus podmíněného přístupu se chová stejně, ale zásady, které vaši uživatelé uvidí, budou založené na podkladových datech, která vaše aplikace požaduje od grafu. 

Konkrétně všechny obory Microsoft Graph reprezentují určitou datovou sadu, která může individuálně použít zásady. Vzhledem k tomu, že se zásady podmíněného přístupu přiřazují k určitým datovým sadám, Azure AD vyhodnotí zásady podmíněného přístupu na základě dat na grafu, nikoli v grafu samotném.

Například pokud aplikace požaduje následující obory Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Aplikace může očekávat, že jejich uživatelé splní všechny zásady nastavené na rezervace a Exchange. Některé obory mohou být namapovány na více datových sad, pokud udělí přístup. 

### <a name="complying-with-a-conditional-access-policy"></a>Dodržování zásad podmíněného přístupu

U několika různých topologií aplikací se zásada podmíněného přístupu vyhodnocuje při navázání relace. Vzhledem k tomu, že zásada podmíněného přístupu pracuje na členitosti aplikací a služeb, je bod, ve kterém je vyvolána, závislá na scénáři, který se pokoušíte provést.

Když se vaše aplikace pokusí o přístup ke službě se zásadami podmíněného přístupu, může se objevit výzva pro podmíněný přístup. Tato výzva je zakódovaná v `claims` parametru, který se nachází v odpovědi z Azure AD. Tady je příklad tohoto parametru výzvy: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Vývojáři můžou tuto výzvu přijmout a připojit ji k nové žádosti do Azure AD. Po předání tohoto stavu se koncovým uživatelům zobrazí výzva k provedení jakékoli akce, která je nutná pro splnění zásad podmíněného přístupu. V následujících scénářích jsou vysvětleny konkrétní chyby a postup extrakce parametru.

## <a name="scenarios"></a>Scénáře

### <a name="prerequisites"></a>Předpoklady

Podmíněný přístup Azure AD je funkce, která je součástí [Azure AD Premium](../fundamentals/active-directory-whatis.md). Další informace o licenčních požadavcích najdete v [sestavě používání bez licenčního využití](../reports-monitoring/overview-reports.md). Vývojáři se můžou připojit ke [službě Microsoft Developer Network](/), která zahrnuje bezplatné předplatné sady Enterprise mobility, které zahrnuje Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Předpoklady pro konkrétní scénáře

Následující informace platí jenom v těchto scénářích podmíněného přístupu:

* Aplikace, které provádějí tok za běhu
* Aplikace, které přistupují k několika službám nebo prostředkům
* Jednostránkové aplikace používající ADAL.js

V následujících částech jsou pojednávají běžné scénáře, které jsou složitější. Základní provozní princip je vyhodnocen jako zásady podmíněného přístupu v době, kdy je token požadován pro službu s použitou zásadou podmíněného přístupu.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scénář: aplikace, která provádí tok za běhu

V tomto scénáři Vás provedeme v případě, že nativní aplikace volá webovou službu nebo rozhraní API. V takovém případě je tato služba pro volání služby pro příjem dat spouštěna za běhu. V našem případě jsme použili zásady podmíněného přístupu na službu pro příjem dat (webové rozhraní API 2) a místo aplikace typu server/démon používali nativní aplikaci. 

![Aplikace, která provádí diagram toku spouštěný jménem](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Požadavek na počáteční token pro webové rozhraní API 1 nevyzve koncového uživatele k ověřování služby Multi-Factor Authentication jako webové rozhraní API 1. rozhraní API pro příjem dat nemusí vždy narazit. Jakmile se webové rozhraní API 1 pokusí požádat o token v rámci uživatele webového rozhraní API 2, požadavek se nezdařil, protože uživatel se přihlásil pomocí služby Multi-Factor Authentication.

Azure AD vrátí odpověď HTTP s některými zajímavými daty:

> [!NOTE]
> V této instanci se jedná o popis chyby Multi-Factor Authentication, ale existuje celá řada `interaction_required` možných přístupů k podmíněnému přístupu.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Ve webovém rozhraní API 1 se chyba zachytí `error=interaction_required` a pošle se zpět `claims` výzva do desktopové aplikace. V tomto okamžiku aplikace klasické pracovní plochy může vytvořit nové `acquireToken()` volání a připojit `claims` výzvu jako další parametr řetězce dotazu. Tato nová žádost vyžaduje, aby uživatel prováděl vícefaktorové ověřování, a pak tento nový token poslal zpátky do webového rozhraní API 1 a dokončil tok.

Chcete-li tento scénář vyzkoušet, podívejte se na náš [ukázkový kód .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ukazuje, jak předat výzvy deklarací identity zpátky z webového rozhraní API 1 do nativní aplikace a vytvořit novou žádost v klientské aplikaci.

## <a name="scenario-app-accessing-multiple-services"></a>Scénář: aplikace přistupující k více službám

V tomto scénáři Vás provedeme v případě, že webová aplikace přistupuje ke dvěma službám, z nichž jedna má přiřazenou zásadu podmíněného přístupu. V závislosti na vaší logice aplikace může existovat cesta, ve které vaše aplikace nevyžaduje přístup k oběma webovým službám. V tomto scénáři je pořadí, ve kterém si vyžádáte token, hraje důležitou roli v prostředí koncového uživatele.

Předpokládejme, že máme webové služby a a B a webové služby B, které používají zásady podmíněného přístupu. I když počáteční interaktivní žádost o ověření vyžaduje souhlas pro obě služby, zásada podmíněného přístupu se ve všech případech nevyžaduje. Pokud aplikace požádá o token webové služby B, vyvolá se tato zásada a následné požadavky webové služby A se také zdaří takto.

![Aplikace, která přistupuje k vývojovému diagramu více služeb](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Případně, pokud aplikace poprvé požaduje token webové služby A, koncový uživatel nespustí zásadu podmíněného přístupu. Díky tomu může vývojář aplikace řídit činnost koncového uživatele a nenutí, aby se zásady podmíněného přístupu vyvolaly ve všech případech. Velká písmena jsou v případě, že aplikace následně požaduje token pro webovou službu B. V tomto okamžiku musí koncový uživatel dodržovat zásady podmíněného přístupu. Když se aplikace pokusí `acquireToken` , může generovat následující chybu (znázorněné v následujícím diagramu):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplikace přistupující k více službám požadujícím nový token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Pokud aplikace používá knihovnu ADAL, neúspěšné opakované pokus o získání tokenu se vždy provádí interaktivně. Když dojde k tomuto interaktivnímu požadavku, má koncový uživatel možnost dodržovat podmíněný přístup. To platí, pokud se jedná o žádost `AcquireTokenSilentAsync` nebo `PromptBehavior.Never` v takovém případě aplikace potřebuje provést interaktivní požadavek, ```AcquireToken``` aby koncovému uživateli bylo umožněno dodržovat tyto zásady.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scénář: jednostránkové aplikace (SPA) pomocí ADAL.js

V tomto scénáři Vás provedeme v případě, že máme jednostránkovou aplikaci (SPA), která používá ADAL.js k volání webového rozhraní API chráněného podmíněného přístupu. Toto je jednoduchá architektura, ale má několik drobné odlišnosti, které je potřeba vzít v úvahu při vývoji podmíněného přístupu.

V ADAL.js existuje několik funkcí, které získají tokeny: `login()` , `acquireToken(...)` , a `acquireTokenPopup(…)` `acquireTokenRedirect(…)` .

* `login()` Získá token ID prostřednictvím interaktivní žádosti o přihlášení, ale nezíská přístupové tokeny žádné služby (včetně webového rozhraní API pro podmíněný přístup).
* `acquireToken(…)` dá se pak použít k tichému získání přístupového tokenu, což znamená, že v žádném případě nezobrazuje uživatelské rozhraní.
* `acquireTokenPopup(…)` a `acquireTokenRedirect(…)` jsou použity k interaktivnímu vyžádání tokenu pro prostředek, což znamená, že vždy zobrazují uživatelské rozhraní pro přihlašování.

Když aplikace potřebuje přístupový token pro volání webového rozhraní API, pokusí se `acquireToken(…)` . Pokud vypršela platnost relace tokenu nebo je potřeba splnit zásady podmíněného přístupu, funkce *acquireToken* se nezdařila a aplikace používá `acquireTokenPopup()` nebo `acquireTokenRedirect()` .

![Jednostránkové aplikace s využitím flowového diagramu ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Podívejme se na příklad s naším scénářem podmíněného přístupu. Koncový uživatel, který se právě vyložil na webu a nemá relaci. Provádíme `login()` volání, získáme token ID bez služby Multi-Factor Authentication. Pak uživatel narazí na tlačítko, které vyžaduje, aby aplikace vyžadovala data z webového rozhraní API. Aplikace se pokusí provést `acquireToken()` volání, ale neproběhne úspěšně, protože uživatel ještě neprováděl službu Multi-Factor Authentication a musí splňovat zásady podmíněného přístupu.

Azure AD pošle zpět následující odpověď HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Naše aplikace potřebuje zachytit `error=interaction_required` . Aplikace pak může použít buď `acquireTokenPopup()` nebo `acquireTokenRedirect()` u stejného prostředku. Uživatel je nucen provést službu Multi-Factor Authentication. Jakmile uživatel dokončí službu Multi-Factor Authentication, aplikace vydá nový přístupový token pro požadovaný prostředek.

Pokud si chcete vyzkoušet tento scénář, přečtěte si náš [kód pro ukázku kódu v tématu js Spa](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). V tomto příkladu kódu se k předvedení tohoto scénáře používá zásada podmíněného přístupu a webové rozhraní API, které jste zaregistrovali dříve pomocí zabezpečeného hesla. Ukazuje, jak správně zpracovat výzvu deklarací identity a získat přístupový token, který se dá použít pro vaše webové rozhraní API. Alternativně můžete vyrezervovat obecný [Angular.js ukázka kódu](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) pro doprovodné materiály k ÚHLOVé Spa.

## <a name="see-also"></a>Viz také

* Další informace o možnostech najdete [v tématu podmíněný přístup v Azure Active Directory](../conditional-access/overview.md).
* Další ukázky kódu Azure AD najdete v tématu [úložiště GitHub ukázek kódu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Další informace o sadě ADAL SDK a přístup k referenční dokumentaci najdete v tématu [Průvodce knihovnou](active-directory-authentication-libraries.md).
* Další informace o scénářích s více klienty najdete v tématu [Postup přihlášení uživatelů pomocí vzoru s více klienty](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).