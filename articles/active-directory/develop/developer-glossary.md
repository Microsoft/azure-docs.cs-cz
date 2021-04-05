---
title: Microsoft Identity Platform Developer Glosář | Azure
description: Seznam podmínek pro běžně používané koncepce a funkce Microsoft Identity Platform Developer
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: 77a4e7389952b81df13dae929dc1aec664fcc0b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755654"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft Identity Platform Developer Glosář

Tento článek obsahuje definice některých základních konceptů a terminologie pro vývojáře, které jsou užitečné při učení o vývoji aplikací s využitím platformy Microsoft Identity Platform.

## <a name="access-token"></a>přístupový token

Typ [tokenu zabezpečení](#security-token) vydaného [autorizačním serverem](#authorization-server), který používá [klientská aplikace](#client-application) pro přístup k [chráněnému serveru prostředků](#resource-server). Ve formě [JSON web token (Jwt)][JWT]se obvykle token podílí na autorizaci udělené klientovi [vlastníkem prostředku](#resource-owner), a to pro požadovanou úroveň přístupu. Token obsahuje všechny použitelné [deklarace identity](#claim) týkající se předmětu, což umožňuje klientské aplikaci, aby ji používala jako formu přihlašovacích údajů při přístupu k danému prostředku. Zároveň se tím eliminuje nutnost, aby vlastník prostředku vystavoval přihlašovací údaje pro klienta.

Přístupové tokeny jsou platné pouze po krátkou dobu a nelze je odvolat. Autorizační Server může při vydání přístupového tokenu vydat taky [obnovovací token](#refresh-token) . Aktualizační tokeny jsou obvykle poskytovány pouze důvěrným klientským aplikacím.

Přístupové tokeny se někdy označují jako "uživatel + aplikace" nebo "jenom aplikace", v závislosti na tom, jaké přihlašovací údaje jsou reprezentované. Například když klientská aplikace používá:

* ["Autorizační kód" udělení autorizace](#authorization-grant), koncový uživatel je nejprve ověřuje jako vlastník prostředku a při přístupu k prostředku delegování autorizace klientovi. Po získání přístupového tokenu se klient ověřuje později. Token se někdy může označovat přesněji jako token "uživatel + aplikace", protože představuje uživatele, který schválil klientskou aplikaci, a aplikaci.
* [Přihlašovací údaje klienta udělují autorizaci](#authorization-grant), klient poskytuje jediné ověřování a funguje bez ověřování/autorizace vlastníka prostředků, takže se token může někdy označovat jako token jenom pro aplikace.

Další podrobnosti najdete v referenčních informacích k [tokenům platformy Microsoft Identity Platform][AAD-Tokens-Claims] .

## <a name="application-id-client-id"></a>ID aplikace (ID klienta)

Jedinečný identifikátor, který Azure AD vystavuje při registraci aplikace, která identifikuje konkrétní aplikaci a související konfigurace. Toto ID aplikace ([ID klienta](https://tools.ietf.org/html/rfc6749#page-15)) se používá při provádění požadavků na ověření a je součástí knihoven ověřování v době vývoje. ID aplikace (ID klienta) není tajný klíč.

## <a name="application-manifest"></a>manifest aplikace

Funkce poskytovaná [Azure Portal][AZURE-portal], která vytvoří reprezentaci JSON konfigurace identity aplikace, která se používá jako mechanismus aktualizace přidružené [aplikace][Graph-App-Resource] a [ServicePrincipal][Graph-Sp-Resource] entit. Další podrobnosti najdete v tématu věnovaném [porozumění manifestu aplikace Azure Active Directory][AAD-App-Manifest] .

## <a name="application-object"></a>objekt aplikace

Když zaregistrujete nebo aktualizujete aplikaci v [Azure Portal][AZURE-portal], portál vytvoří nebo aktualizuje aplikační objekt a odpovídající [instanční objekt služby](#service-principal-object) pro daného tenanta. Objekt aplikace *definuje* konfiguraci identit aplikace globálně (ve všech klientech, kde má přístup), a poskytuje šablonu, ze které jsou příslušné objekty instančních objektů *odvozeny* pro místní použití v době běhu (v konkrétním tenantovi).

Další informace naleznete v tématu [Application and Service instanční objekty][AAD-App-SP-Objects].

## <a name="application-registration"></a>registrace aplikací

Aby mohla aplikace integrovat s funkcemi pro správu identit a přístupu do služby Azure AD, musí být zaregistrované v [Tenantovi](#tenant)Azure AD. Při registraci aplikace ve službě Azure AD poskytujete konfiguraci identity pro vaši aplikaci, která umožňuje integraci s Azure AD a používání funkcí, jako například:

* Robustní Správa jednoho Sign-On pomocí implementace protokolu Azure AD Identity Management a [OpenID Connect][OpenIDConnect]
* Zprostředkovaný přístup k [chráněným prostředkům](#resource-server) pomocí [klientských aplikací](#client-application)prostřednictvím [autorizačního serveru](#authorization-server) OAuth 2,0
* [Rozhraní pro vyjádření souhlasu](#consent) pro správu přístupu klientů k chráněným prostředkům na základě autorizace vlastníka prostředků.

Další podrobnosti najdete v tématu [Integrace aplikací s Azure Active Directory][AAD-Integrating-Apps] .

## <a name="authentication"></a>ověřování

Nenáročný večírek na legitimní přihlašovací údaje, který poskytuje základ pro vytvoření objektu zabezpečení, který se má použít k řízení identit a přístupu. V rámci [udělení autorizace OAuth2](#authorization-grant) je třeba, aby ověřování strany naplnilo roli [vlastníka prostředku](#resource-owner) nebo [klientské aplikace](#client-application)v závislosti na používaném udělení.

## <a name="authorization"></a>autorizace

Způsob udělení oprávnění ověřeného objektu zabezpečení k tomu, aby něco fungovalo. V modelu programování Azure AD existují dva hlavní případy použití:

* Během procesu [udělení autorizace OAuth2](#authorization-grant) : [vlastník prostředku](#resource-owner) udělí autorizaci [klientské aplikaci](#client-application)a umožňuje klientovi přístup k prostředkům vlastníka prostředku.
* Během přístupu k prostředkům klienta: jak je implementováno [serverem prostředků](#resource-server), pomocí hodnot [deklarací identity](#claim) přítomných v [přístupovém tokenu](#access-token) k rozhodování o řízení přístupu na základě nich.

## <a name="authorization-code"></a>autorizační kód

Pro [klientskou aplikaci](#client-application) poskytnutý [koncovým bodem autorizace](#authorization-endpoint)má krátkodobý "token", který je součástí toku "autorizační kód", jedním ze čtyř OAuth2 [autorizací](#authorization-grant). Kód se vrátí do klientské aplikace v reakci na ověření [vlastníka prostředku](#resource-owner), což značí, že vlastník prostředku má delegovanou autorizaci pro přístup k požadovaným prostředkům. V rámci toku je kód později uplatněn pro [přístupový token](#access-token).

## <a name="authorization-endpoint"></a>koncový bod autorizace

Jeden z koncových bodů implementovaných [autorizačním serverem](#authorization-server), který slouží k interakci s [vlastníkem prostředku](#resource-owner) za účelem poskytnutí [udělení autorizace](#authorization-grant) během toku udělení autorizace OAuth2. V závislosti na použitém toku autorizačního udělení se může skutečný poskytnutý grant lišit, včetně [autorizačního kódu](#authorization-code) nebo [tokenu zabezpečení](#security-token).

Další podrobnosti najdete v částech [typy udělení autorizace][OAuth2-AuthZ-Grant-Types] a [koncových bodů autorizace][OAuth2-AuthZ-Endpoint] specifikace OAuth2 a [specifikace OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] .

## <a name="authorization-grant"></a>udělení autorizace

Přihlašovací údaje představující [autorizaci](#authorization) [vlastníka prostředků](#resource-owner) pro přístup k chráněným prostředkům uděleným [klientské aplikaci](#client-application). Klientská aplikace může použít jeden ze [čtyř typů grantů definovaných pomocí autorizačního rozhraní OAuth2][OAuth2-AuthZ-Grant-Types] k získání grantu v závislosti na typu nebo požadavcích klienta: "udělení autorizačního kódu", "udělení pověření klienta", "implicitní udělení", "udělení přihlašovacích údajů pro heslo vlastníka prostředku". Přihlašovací údaje vracené klientovi jsou buď [přístupový token](#access-token), nebo [autorizační kód](#authorization-code) (vyměňovaný později pro přístupový token) v závislosti na typu použitého autorizačního oprávnění.

## <a name="authorization-server"></a>autorizační Server

Jak je definováno [autorizačním rozhraním OAuth2][OAuth2-Role-Def], server zodpovědný za vydávání přístupových tokenů [klientovi](#client-application) po úspěšném ověření [vlastníka prostředku](#resource-owner) a získání jeho autorizace. [Klientská aplikace](#client-application) komunikuje s autorizačním serverem za běhu [prostřednictvím svých](#authorization-endpoint) koncových bodů [tokenu](#token-endpoint) , a to v souladu s OAuth2 definovanými [autorizačními uděleními](#authorization-grant).

V případě integrace aplikací Microsoft Identity Platform implementuje Microsoft Identity Platform role autorizačního serveru pro aplikace Azure AD a rozhraní API služeb Microsoftu, například [Microsoft Graph rozhraní API][Microsoft-Graph].

## <a name="claim"></a>tvrdit

[Token zabezpečení](#security-token) obsahuje deklarace identity, které poskytují kontrolní výrazy týkající se jedné entity (například [klientské aplikace](#client-application) nebo [vlastníka prostředku](#resource-owner)) k jiné entitě (například k [serveru prostředků](#resource-server)). Deklarace identity jsou páry název-hodnota, které přenášejí fakta týkající se předmětu tokenu (například objekt zabezpečení, který byl ověřen [autorizačním serverem](#authorization-server)). Deklarace identity přítomné v daném tokenu jsou závislé na několika proměnných, včetně typu tokenu, typu přihlašovacích údajů použitých k ověření předmětu, konfigurace aplikace atd.

Další podrobnosti najdete v referenčních informacích k [tokenům platformy Microsoft Identity Platform][AAD-Tokens-Claims] .

## <a name="client-application"></a>klientská aplikace

Jak je definováno v [rámci autorizačního rozhraní OAuth2][OAuth2-Role-Def], aplikace, která vytváří chráněné požadavky na prostředky jménem [vlastníka prostředku](#resource-owner). Pojem "klient" nezahrnuje žádné konkrétní charakteristiky implementace hardwaru (například bez ohledu na to, jestli se aplikace spouští na serveru, počítači nebo jiném zařízení).

Klientská aplikace požaduje [autorizaci](#authorization) od vlastníka prostředku, aby se účastnila toku [udělení autorizace OAuth2](#authorization-grant) a mohli přistupovat k rozhraním API/data jménem vlastníka prostředku. Autorizační rozhraní OAuth2 [definuje dva typy klientů][OAuth2-Client-Types]"důvěrné" a "veřejné" na základě schopnosti klienta zachovat důvěrnost svých přihlašovacích údajů. Aplikace můžou implementovat [webového klienta (důvěrné)](#web-client) , který běží na webovém serveru, [nativním klientovi (veřejném)](#native-client) nainstalovaném na zařízení nebo klientovi s [uživatelským agentem (veřejný)](#user-agent-based-client) , který běží v prohlížeči zařízení.

## <a name="consent"></a>souhlas

Proces [vlastníka prostředku](#resource-owner) , který uděluje autorizaci [klientské aplikaci](#client-application), pro přístup k chráněným prostředkům v rámci konkrétních [oprávnění](#permissions)jménem vlastníka prostředku. V závislosti na oprávněních, která klient požaduje, bude správce nebo uživatel požádán o souhlas s povolením přístupu ke svým organizacím/jednotlivým datům. Všimněte si, že ve scénáři s [více klienty](#multi-tenant-application) je [instanční objekt](#service-principal-object) aplikace také zaznamenán v tenantovi odesílajícího uživatele.

Další informace najdete v tématu o [souhlasu](consent-framework.md) .

## <a name="id-token"></a>Token ID

[Token zabezpečení](#security-token) [OpenID Connect][OpenIDConnect-ID-Token] poskytnutý [koncovým bodem autorizace](#authorization-endpoint) [autorizačního serveru](#authorization-server) , který obsahuje [deklarace identity](#claim) související s ověřováním [vlastníka prostředku](#resource-owner)koncového uživatele. Podobně jako přístupový token se tokeny ID také reprezentují jako digitálně podepsaný [JSON web token (Jwt)][JWT]. Na rozdíl od přístupového tokenu se ale deklarace tokenu ID nepoužívají pro účely související s přístupem k prostředkům a specifickou kontrolou přístupu.

Další podrobnosti najdete v referenčních informacích k [tokenům platformy Microsoft Identity Platform][AAD-Tokens-Claims] .

## <a name="microsoft-identity-platform"></a>Microsoft Identity Platform

Platforma Microsoft identity je vývojem služby identity a platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání Microsoft Graphu, dalších rozhraní API od Microsoftu nebo rozhraní API, která vytvořili vývojáři. Je to plně vybavená platforma, která se skládá ze služby ověřování, knihoven, registrace a konfigurace aplikací, úplné dokumentace pro vývojáře, ukázek kódu a dalšího obsahu pro vývojáře. Microsoft Identity Platform podporuje standardní oborové protokoly, jako jsou OAuth 2.0 a OpenID Connect.

## <a name="multi-tenant-application"></a>víceklientské aplikace

Třída aplikace, která umožňuje přihlašovat a [odsouhlasit](#consent) uživatele zřízené v jakémkoli [tenantovi](#tenant)Azure AD, včetně jiných klientů, než je ten, kde je klient zaregistrován. [Nativní klientské](#native-client) aplikace jsou ve výchozím nastavení víceklientské, zatímco [webové klientské](#web-client) a [webové prostředky nebo aplikace API](#resource-server) umožňují výběr mezi jedním nebo více klienty. Naproti tomu webová aplikace zaregistrovaná jako jeden tenant povolí jenom přihlášení z uživatelských účtů zřízených ve stejném tenantovi jako ten, ve kterém je aplikace zaregistrovaná.

Další podrobnosti najdete v tématu [jak se přihlásit k libovolnému uživateli Azure AD pomocí vzoru aplikace pro více tenantů][AAD-Multi-Tenant-Overview] .

## <a name="native-client"></a>Nativní klient

Typ [klientské aplikace](#client-application) , která je nainstalovaná nativně na zařízení. Vzhledem k tomu, že je veškerý kód spuštěný v zařízení, považuje se za "veřejný" klient, protože jeho neschopnost ukládat přihlašovací údaje soukromě/důvěrné. Další podrobnosti najdete v tématu [typy a profily klienta OAuth2][OAuth2-Client-Types] .

## <a name="permissions"></a>oprávnění

[Klientská aplikace](#client-application) získá přístup k [serveru prostředků](#resource-server) tím, že deklaruje žádosti o oprávnění. K dispozici jsou dva typy:

* Delegovaná oprávnění, která určují přístup [založený na rozsahu](#scopes) pomocí delegované autorizace z přihlášeného [vlastníka prostředku](#resource-owner), se předloží prostředku za běhu jako [deklarace spojovacího bodu](#claim) služby v [přístupovém tokenu](#access-token)klienta.
* Oprávnění "aplikace", která určují přístup [založený na rolích](#roles) pomocí pověření nebo identity klientské aplikace, jsou prezentována prostředku za běhu jako [deklarace role](#claim) v přístupovém tokenu klienta.

Také se během procesu [souhlasu](#consent) nastavily správci nebo prostředku, aby měli možnost udělit nebo odepřít klientský přístup k prostředkům ve svém tenantovi.

Žádosti o oprávnění jsou nakonfigurovány na stránce **oprávnění rozhraní API** pro aplikaci v [Azure Portal][AZURE-portal]tak, že vyberete požadovaná "delegovaná oprávnění" a "oprávnění aplikace" (ta druhá vyžaduje členství v roli globálního správce). Vzhledem k tomu, že [veřejný klient](#client-application) nemůže bezpečně udržovat přihlašovací údaje, může požadovat jenom delegovaná oprávnění, zatímco [důvěrného klienta](#client-application) má možnost požadovat oprávnění delegovaná i aplikace. [Objekt aplikace](#application-object) klienta ukládá deklarovaná oprávnění do své [vlastnosti requiredResourceAccess][Graph-App-Resource].

## <a name="refresh-token"></a>aktualizovat token

Typ [tokenu zabezpečení](#security-token) vydaného [autorizačním serverem](#authorization-server), který používá [klientská aplikace](#client-application) pro vyžádání nového [přístupového tokenu](#access-token) před vypršením platnosti přístupového tokenu. Obvykle ve formě [JSON web token (Jwt)][JWT].

Na rozdíl od přístupových tokenů se dají aktualizace tokenů odvolat. Pokud se klientská aplikace pokusí požádat o nový přístupový token pomocí obnovovacího tokenu, který byl odvolán, autorizační server žádost zamítne a klientská aplikace již nebude mít oprávnění pro přístup k [serveru prostředků](#resource-server) jménem [vlastníka prostředku](#resource-owner).

## <a name="resource-owner"></a>vlastník prostředku

Jak je definováno v [rámci autorizačního rozhraní OAuth2][OAuth2-Role-Def], entita, která umožňuje udělit přístup k chráněnému prostředku. Když je vlastníkem prostředku osoba, označuje se jako koncový uživatel. Například když chce [klientská aplikace](#client-application) získat přístup k poštovní schránce uživatele prostřednictvím [rozhraní Microsoft Graph API][Microsoft-Graph], vyžaduje oprávnění od vlastníka prostředku poštovní schránky.

## <a name="resource-server"></a>Server prostředků

Jak je definováno v [rámci autorizačního rozhraní OAuth2][OAuth2-Role-Def], server, který je hostitelem chráněných prostředků, umožňuje přijímat a reagovat na požadavky na chráněné prostředky [klientskými aplikacemi](#client-application) , které prezentují [přístupový token](#access-token). Označuje se také jako chráněný server prostředků nebo aplikace prostředků.

Server prostředků zveřejňuje rozhraní API a vynutil přístup k chráněným prostředkům prostřednictvím [oborů](#scopes) a [rolí](#roles)pomocí autorizačního rozhraní OAuth 2,0. Mezi příklady patří [rozhraní Microsoft Graph API][Microsoft-Graph] , které poskytuje přístup k datům TENANTA Azure AD a rozhraní api pro Microsoft 365, která poskytují přístup k datům, jako je například pošta a kalendář.

Stejně jako klientská aplikace, konfigurace identity aplikace prostředků se vytváří prostřednictvím [registrace](#application-registration) v TENANTOVI Azure AD, která poskytuje aplikační i instanční objekt. Některá rozhraní API poskytnutá Microsoftem, jako je Microsoft Graph API, mají předem registrované instanční objekty, které jsou k dispozici ve všech klientech během zřizování.

## <a name="roles"></a>Role

Podobně jako [obory](#scopes)role poskytují [serveru prostředků](#resource-server) způsob, jak řídit přístup k chráněným prostředkům. Existují dva typy: role "uživatel" implementuje řízení přístupu na základě rolí pro uživatele nebo skupiny, které vyžadují přístup k prostředku, zatímco role "aplikace" implementuje stejné pro [klientské aplikace](#client-application) , které vyžadují přístup.

Role jsou řetězce definované prostředky (například schvalovatel výdajů, "jen pro čtení", "adresář. četl. vše") spravované v [Azure Portal][AZURE-portal] prostřednictvím [manifestu aplikace](#application-manifest)prostředku a uložené ve [vlastnosti appRoles][Graph-Sp-Resource]prostředku. Azure Portal se také používá k přiřazení uživatelů k rolím "uživatel" a ke konfiguraci [oprávnění klientských aplikací](#permissions) pro přístup k roli "aplikace".

Podrobné informace o aplikačních rolích, které jsou vystavené rozhraním API Microsoft Graph, najdete v tématu [Graph API obory oprávnění][Graph-Perm-Scopes]. Podrobný příklad implementace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal][AAD-RBAC].

## <a name="scopes"></a>obory

Podobně jako [role](#roles)poskytují obory pro [server prostředků](#resource-server) způsob, jak řídit přístup k chráněným prostředkům. Obory se používají k implementaci řízení přístupu [na základě oboru][OAuth2-Access-Token-Scopes] pro [klientskou aplikaci](#client-application) , která má delegovaný přístup k prostředku podle jeho majitele.

Obory jsou řetězce definované prostředky (například mail. Read "," Directory. četl. All ") spravované v [Azure Portal][AZURE-portal] prostřednictvím [manifestu aplikace](#application-manifest)prostředku a uložené ve [vlastnosti oauth2Permissions][Graph-Sp-Resource]prostředku. Azure Portal se také používá ke konfiguraci [delegovaných oprávnění](#permissions) klientské aplikace pro přístup k oboru.

Osvědčenými postupy pro vytváření názvů je použití formátu "prostředek. Operation. Constraint". Podrobnou diskuzi o oborech vystavených rozhraním Microsoft Graph API najdete v tématu [Graph API obory oprávnění][Graph-Perm-Scopes]. Obory vystavené službou Microsoft 365 Services najdete v tématu [Přehled oprávnění rozhraní API pro Microsoft 365][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpečení

Podepsaný dokument obsahující deklarace identity, jako je například token OAuth2 nebo kontrolní výraz SAML 2,0. Pro [udělení autorizace](#authorization-grant)OAuth2 jsou tokeny [přístupu](#access-token) (OAuth2), [aktualizační token](#refresh-token)a [token ID](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) typy tokenů zabezpečení, které jsou implementované jako [JSON web token (Jwt)][JWT].

## <a name="service-principal-object"></a>instanční objekt služby

Když zaregistrujete nebo aktualizujete aplikaci v [Azure Portal][AZURE-portal], portál vytvoří nebo aktualizuje aplikační [objekt](#application-object) a odpovídající instanční objekt služby pro daného tenanta. Objekt aplikace *definuje* konfiguraci identit aplikace globálně (ve všech klientech, kde má přidružená aplikace udělen přístup) a je šablona, ze které jsou příslušné objekty instančních objektů *odvozeny* pro místní použití v době běhu (v konkrétním tenantovi).

Další informace naleznete v tématu [Application and Service instanční objekty][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Proces [klientské aplikace](#client-application) , která zahajuje ověřování koncového uživatele a zachytí související stav, pro účely získání [tokenu zabezpečení](#security-token) a určení rozsahu relace aplikace do daného stavu. Stav může zahrnovat artefakty, jako jsou informace o profilu uživatele, a informace odvozené z deklarací identity tokenu.

Funkce přihlašování aplikace se obvykle používá k implementaci jednotného přihlašování (SSO). Je také možné, že před tím, jako vstupní bod pro koncového uživatele, získá přístup k aplikaci (při prvním přihlášení). Funkce registrace se používá ke shromáždění a uchování dalších stavů specifických pro uživatele a může vyžadovat [souhlas s uživatelem](#consent).

## <a name="sign-out"></a>adresa URL

Proces ověřování koncového uživatele odpojením stavu uživatele přidruženého k relaci [klientské aplikace](#client-application) během [přihlašování](#sign-in)

## <a name="tenant"></a>tenant

Instance adresáře služby Azure AD je označována jako tenant služby Azure AD. Poskytuje několik funkcí, včetně:

* Služba registru pro integrované aplikace
* ověřování uživatelských účtů a registrovaných aplikací
* Koncové body REST vyžadované pro podporu různých protokolů, včetně OAuth2 a SAML, včetně [koncového bodu autorizace](#authorization-endpoint), [koncového bodu tokenu](#token-endpoint) a "společného" koncového bodu používaného [více aplikacemi tenantů](#multi-tenant-application).

Tenant Azure AD se vytváří/přidružit k Azure a předplatným Microsoft 365 během registrace a poskytují funkce pro správu přístupu identit & pro předplatné. Správci předplatného Azure můžou také vytvářet další klienty Azure AD prostřednictvím Azure Portal. Podrobnosti o různých způsobech přístupu ke klientovi získáte v tématu [Jak získat klienta Azure Active Directory][AAD-How-To-Tenant] . Podrobnosti o vztahu mezi předplatnými a klientem služby Azure AD najdete v tématu [přidružení nebo přidání předplatného Azure do vašeho tenanta Azure Active Directory][AAD-How-Subscriptions-Assoc] . pokyny k přidružení nebo přidání předplatného k TENANTOVI Azure AD.

## <a name="token-endpoint"></a>koncový bod tokenu

Jeden z koncových bodů implementovaných [autorizačním serverem](#authorization-server) pro podporu [autorizací OAuth2 autorizace](#authorization-grant). V závislosti na udělení se dá [klientovi](#client-application)použít k získání [přístupového tokenu](#access-token) (a souvisejícího "aktualizačního) tokenu klienta nebo [tokenu ID](#id-token) při použití s protokolem [OpenID Connect][OpenIDConnect] .

## <a name="user-agent-based-client"></a>Uživatel – klient založený na agentovi

Typ [klientské aplikace](#client-application) , který stáhne kód z webového serveru a provede ho v rámci uživatelského agenta (například webového prohlížeče), jako je například jednostránkové aplikace (Spa). Vzhledem k tomu, že je veškerý kód spuštěný v zařízení, považuje se za "veřejný" klient, protože jeho neschopnost ukládat přihlašovací údaje soukromě/důvěrné. Další informace najdete v tématu [typy a profily klienta OAuth2][OAuth2-Client-Types].

## <a name="user-principal"></a>objekt zabezpečení uživatele

Podobně jako instanční objekt se používá k reprezentaci instance aplikace, objekt zabezpečení uživatele je jiný typ objektu zabezpečení, který představuje uživatele. [Typ prostředku][Graph-User-Resource] Microsoft Graph definuje schéma pro objekt uživatele, včetně uživatelských vlastností, jako je například jméno a příjmení, hlavní název uživatele, členství v adresáři role atd. To poskytuje konfiguraci identity uživatele pro Azure AD k navázání hlavního uživatele v době běhu. Objekt zabezpečení uživatele slouží k reprezentaci ověřeného uživatele pro jednotné přihlašování, zaznamenávání [souhlasu](#consent) delegace, rozhodování o řízení přístupu atd.

## <a name="web-client"></a>Webový klient

Typ [klientské aplikace](#client-application) , který spouští veškerý kód na webovém serveru a který je schopný fungovat jako "důvěrný" klient bezpečně uložený přihlašovací údaje na serveru. Další informace najdete v tématu [typy a profily klienta OAuth2][OAuth2-Client-Types].

## <a name="next-steps"></a>Další kroky

[Příručka pro vývojáře Microsoft Identity Platform Developer][AAD-Dev-Guide] je cílová stránka, která se používá pro všechna témata týkající se vývoje platformy Microsoft Identity Platform, včetně přehledu [Integrace aplikací][AAD-How-To-Integrate] a základních [scénářů ověřování platformy Microsoft Identity Platform a podporovaných scénářů ověřování][AAD-Auth-Scenarios]. Můžete také najít ukázky kódu & kurzy, jak rychle začít pracovat na [GitHubu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Následující komentáře vám poskytnou zpětnou vazbu a nápovědu k vylepšení a obnově obsahu obsahu, včetně požadavků na nové definice nebo aktualizace stávajících.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta&preserve-view=true
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken