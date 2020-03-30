---
title: Glosář vývojářů platformy Microsoft pro identity | Azure
description: Seznam termínů pro běžně používané koncepty a funkce vývojáře platformy identit Microsoftu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: ce98d2db86c87ac6aa8fa4872bc076714467d32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263046"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Glosář vývojářů platformy Microsoft pro identity

Tento článek obsahuje definice pro některé základní vývojářské koncepty a terminologie, které jsou užitečné při učení o vývoji aplikací pomocí platformy identit microsoftu.

## <a name="access-token"></a>přístupový token

Typ [tokenu zabezpečení](#security-token) vydaný [autorizačním serverem](#authorization-server)a používaný [klientskou aplikací](#client-application) pro přístup k [serveru chráněných prostředků](#resource-server). Obvykle ve formě [webového tokenu JSON (JWT)][JWT]ztělesňuje token autorizaci udělenou klientovi [vlastníkem prostředku](#resource-owner)pro požadovanou úroveň přístupu. Token obsahuje všechny příslušné [deklarace identity](#claim) o předmětu, povolení klientské aplikace použít jako formu pověření při přístupu k danému prostředku. To také eliminuje potřebu vlastníka prostředku vystavit pověření klientovi.

Přístupové tokeny jsou někdy označovány jako "Uživatel + App" nebo "Pouze app", v závislosti na pověření jsou zastoupeny. Například když klientská aplikace používá:

* [Autorizační udělení "Autorizační kód"](#authorization-grant), koncový uživatel se ověří jako první jako vlastník prostředku a deleguje autorizaci klientovi pro přístup k prostředku. Klient se ověří později při získání přístupového tokenu. Token může být někdy označovánkonkrétnějako token "User+App", protože představuje jak uživatele, který autorizoval klientskou aplikaci, tak aplikaci.
* ["Pověření klienta" udělení autorizace](#authorization-grant), klient poskytuje výhradní ověřování, které funguje bez ověřování nebo autorizace vlastníka prostředku, takže token může být někdy označován jako token "Pouze app".

Další podrobnosti najdete v [tématu Odkaz na token platformy identit y Microsoft.][AAD-Tokens-Claims]

## <a name="application-id-client-id"></a>ID aplikace (ID klienta)

Jedinečný identifikátor Azure AD problémy s registrací aplikace, která identifikuje konkrétní aplikace a přidružené konfigurace. Toto ID aplikace[(ID klienta)](https://tools.ietf.org/html/rfc6749#page-15)se používá při provádění požadavků na ověření a je poskytováno knihovnám ověřování v době vývoje. ID aplikace (ID klienta) není tajný klíč.

## <a name="application-manifest"></a>manifest aplikace

Funkce poskytovaná [portálem Azure][AZURE-portal], který vytváří json reprezentaci konfigurace identity aplikace, který se používá jako mechanismus pro aktualizaci přidružených entit [Application][Graph-App-Resource] a [ServicePrincipal.][Graph-Sp-Resource] Další podrobnosti najdete [v tématu Principy manifestu aplikace Azure Active Directory.][AAD-App-Manifest]

## <a name="application-object"></a>aplikační objekt

Když zaregistrujete nebo aktualizujete aplikaci na [portálu Azure][AZURE-portal], portál vytvoří nebo aktualizuje objekt aplikace a odpovídající [objekt instančního objektu](#service-principal-object) pro tohoto klienta. Aplikační objekt *definuje* konfiguraci identity aplikace globálně (napříč všemi klienty, kde má přístup), poskytuje šablonu, ze které jsou *odvozeny* jeho odpovídající objekty instančního objektu pro použití místně za běhu (v konkrétním tenantovi).

Další informace naleznete v [tématu Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="application-registration"></a>registrace aplikací

Aby se aplikace mohla integrovat s funkcemi správy identit a přístupu a delegovat ji do služby Azure AD, musí být registrovaná u [klienta](#tenant)Azure AD . Když zaregistrujete aplikaci ve službě Azure AD, poskytujete konfiguraci identity pro vaši aplikaci, která jí umožňuje integraci s Azure AD a používat funkce, jako jsou:

* Robustní správa jednotného přihlášení pomocí azure ad identity management a implementace protokolu [OpenID Connect][OpenIDConnect]
* Zprostředkovaný přístup k [chráněným prostředkům](#resource-server) [klientskými aplikacemi](#client-application)prostřednictvím [autorizačního serveru](#authorization-server) OAuth 2.0
* [Rozhraní souhlasu](#consent) pro správu přístupu klienta k chráněným prostředkům na základě autorizace vlastníka prostředků.

Další podrobnosti najdete [v tématu Integrace aplikací s Azure Active Directory.][AAD-Integrating-Apps]

## <a name="authentication"></a>ověřování

Akt zpochybnění strany pro legitimní pověření, poskytuje základ pro vytvoření zaregistrovaný objekt zabezpečení, který má být použit pro identitu a řízení přístupu. Například během [udělení autorizace OAuth2](#authorization-grant) obsazuje ověřující strana roli [vlastníka prostředku](#resource-owner) nebo [klientské aplikace](#client-application)v závislosti na použitém grantu.

## <a name="authorization"></a>autorizace

Udělení oprávnění ověřeného objektu zabezpečení k něčemu provést. V modelu programování Azure AD existují dva hlavní případy použití:

* Během toku [udělení autorizace OAuth2:](#authorization-grant) pokud [vlastník prostředku](#resource-owner) udělí autorizaci [klientské aplikaci](#client-application), umožní klientovi přístup k prostředkům vlastníka prostředku.
* Během přístupu k prostředkům klientem: jak je implementováno [serverem prostředků](#resource-server), pomocí hodnot [deklarací,](#claim) které jsou k dispozici v [přístupovém tokenu,](#access-token) můžete na nich provádět rozhodnutí o řízení přístupu.

## <a name="authorization-code"></a>autorizační kód

Krátkodobý "token" poskytnutý [klientské aplikaci](#client-application) [koncovým bodem autorizace](#authorization-endpoint)jako součást toku "autorizačního kódu", jednoho ze čtyř [autorizačních grantů](#authorization-grant)OAuth2 . Kód je vrácen klientské aplikaci v reakci na ověření [vlastníka prostředku](#resource-owner), což znamená, že vlastník prostředku delegoval oprávnění k přístupu k požadovaným prostředkům. Jako součást toku kód je později uplatněna pro [přístupový token](#access-token).

## <a name="authorization-endpoint"></a>koncový bod autorizace

Jeden z koncových bodů implementovaných [autorizačním serverem](#authorization-server), který se používá k interakci s [vlastníkem prostředku](#resource-owner) za účelem poskytnutí [udělení autorizace](#authorization-grant) během toku udělení autorizace OAuth2. V závislosti na použitém toku udělení autorizace se skutečný poskytnutý grant může lišit, včetně [autorizačního kódu](#authorization-code) nebo [tokenu zabezpečení](#security-token).

Další podrobnosti najdete v [části autorizačního programu specifikace][OAuth2-AuthZ-Grant-Types] OAuth2 a v části [koncový bod autorizace][OAuth2-AuthZ-Endpoint] a [v části OpenIDConnect.][OpenIDConnect-AuthZ-Endpoint]

## <a name="authorization-grant"></a>udělení povolení

Pověření představující [oprávnění](#authorization) [vlastníka prostředku](#resource-owner) k přístupu k chráněným prostředkům udělené [klientské aplikaci](#client-application). Klientská aplikace můžete použít jeden ze [čtyř typů udělení definované OAuth2 Autorizační rámec][OAuth2-AuthZ-Grant-Types] získat grant, v závislosti na typu klienta/požadavky: "udělení autorizačního kódu", "udělení pověření klienta", "implicitní udělení" a "vlastník prostředku heslo pověření grant". Pověření vrácená klientovi je buď [přístupový token](#access-token), nebo [autorizační kód](#authorization-code) (později vyměněný za přístupový token) v závislosti na typu použitého autorizačního grantu.

## <a name="authorization-server"></a>autorizační server

Jak je definováno v [autorizační masce OAuth2][OAuth2-Role-Def], server odpovědný za vydávání přístupových tokenů [klientovi](#client-application) po úspěšném ověření [vlastníka prostředku](#resource-owner) a získání jeho autorizace. [Klientská aplikace](#client-application) spolupracuje s autorizačním serverem za běhu prostřednictvím svých koncových bodů [autorizace](#authorization-endpoint) a [tokenu](#token-endpoint) v souladu s [udělenými autorizačními granty definovanými](#authorization-grant)oAuth2 .

V případě integrace aplikací platformy microsoftu platformy identit implementuje platforma identit microsoftu roli autorizačního serveru pro aplikace Azure AD a rozhraní API služby Microsoft, například [rozhraní API Microsoft Graph .][Microsoft-Graph]

## <a name="claim"></a>Tvrdí

[Token zabezpečení](#security-token) obsahuje deklarace identity, které poskytují kontrolní výrazy o jedné entitě (například [o klientské aplikaci](#client-application) nebo [vlastníkovi prostředku](#resource-owner)) jiné entitě (například serveru [prostředků).](#resource-server) Deklarace jsou dvojice název/hodnota, které přenášejí fakta o předmětu tokenu (například objekt zabezpečení, který byl ověřen [autorizačním serverem](#authorization-server)). Deklarace identity v daném tokenu jsou závislé na několika proměnných, včetně typu tokenu, typu pověření použitého k ověření předmětu, konfigurace aplikace atd.

Další podrobnosti najdete [v tématu Odkaz na token platformy identit y Microsoft.][AAD-Tokens-Claims]

## <a name="client-application"></a>klientská aplikace

Jak je definováno v [rámci autorizace OAuth2][OAuth2-Role-Def], aplikace, která provádí požadavky na chráněné prostředky jménem [vlastníka prostředku](#resource-owner). Termín "klient" neznamená žádné konkrétní vlastnosti implementace hardwaru (například zda se aplikace spouští na serveru, na ploše nebo jiných zařízeních).

Klientská aplikace požaduje [autorizaci](#authorization) od vlastníka prostředku k účasti v toku [udělení autorizace OAuth2](#authorization-grant) a může přistupovat k apim/datům jménem vlastníka prostředku. Autorizační rám OAuth2 [definuje dva typy klientů][OAuth2-Client-Types], "důvěrné" a "veřejné", na základě schopnosti klienta zachovat důvěrnost svých přihlašovacích údajů. Aplikace mohou implementovat [webového klienta (důvěrné),](#web-client) který běží na webovém serveru, [nativního klienta (veřejného)](#native-client) nainstalovaného na zařízení nebo [klienta (veřejného) založeného na uživatelském agentovi,](#user-agent-based-client) který běží v prohlížeči zařízení.

## <a name="consent"></a>Souhlasu

Proces [vlastníka prostředku](#resource-owner) udělujícího autorizaci [klientské aplikaci](#client-application), přístupu k chráněným prostředkům pod [určitými oprávněními](#permissions)jménem vlastníka prostředku. V závislosti na oprávněních požadovaných klientem bude správce nebo uživatel požádán o souhlas s povolením přístupu k jejich organizačním/individuálním datům. Všimněte si, že ve scénáři [s více klienty](#multi-tenant-application) je [instanční objekt](#service-principal-object) aplikace také zaznamenán v tenantovi uživatele, který s tím souhlasí.

Další informace naleznete [v rámci souhlasu.](consent-framework.md)

## <a name="id-token"></a>ID token

[Token zabezpečení](#security-token) [OpenID Connect][OpenIDConnect-ID-Token] poskytovaný [koncovým bodem autorizace autorizačního](#authorization-endpoint) [serveru](#authorization-server) , který obsahuje [deklarace](#claim) identity týkající se ověřování [vlastníka prostředku](#resource-owner)koncového uživatele . Podobně jako přístupový token jsou tokeny ID také reprezentovány jako digitálně podepsaný [webový token JSON (JWT).][JWT] Na rozdíl od přístupového tokenu se však deklarace identity tokenu ID nepoužívají pro účely související s přístupem k prostředkům a konkrétně řízením přístupu.

Další podrobnosti najdete [v tématu Odkaz na token platformy identit y Microsoft.][AAD-Tokens-Claims]

## <a name="microsoft-identity-platform"></a>Microsoft identity platform

Platforma Microsoft Identity Platform vznikla z platformy pro vývojáře a službu identit Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání Microsoft Graphu, dalších rozhraní API od Microsoftu nebo rozhraní API, která vytvořili vývojáři. Jedná se o plnohodnotnou platformu, která se skládá z ověřovací služby, knihoven, registrace a konfigurace aplikací, úplné dokumentace pro vývojáře, ukázek kódu a dalšího obsahu pro vývojáře. Microsoft Identity Platform podporuje standardní oborové protokoly, jako jsou OAuth 2.0 a OpenID Connect. Další informace najdete [v tématu O platformě identit Microsoftu.](about-microsoft-identity-platform.md)

## <a name="multi-tenant-application"></a>víceklientská aplikace

Třída aplikace, která umožňuje přihlášení a [souhlas](#consent) uživatelů zřízených v libovolném [tenantovi](#tenant)Azure AD , včetně klientů jiných než ten, kde je klient registrován. [Nativní klientské](#native-client) aplikace jsou ve výchozím nastavení víceklientské, zatímco aplikace [webového klienta](#web-client) a [webového prostředku/rozhraní API](#resource-server) mají možnost vybírat mezi jedním nebo více klienty. Naproti tomu webová aplikace registrovaná jako jednoklientská by umožňovala pouze přihlášení z uživatelských účtů zřízených ve stejném tenantovi jako ten, kde je aplikace registrována.

Další podrobnosti [najdete v tématu Jak se přihlásit k libovolnému uživateli Azure AD pomocí vzoru víceklientské aplikace][AAD-Multi-Tenant-Overview] pro další podrobnosti.

## <a name="native-client"></a>nativní klient

Typ [klientské aplikace,](#client-application) která je nainstalována nativně na zařízení. Vzhledem k tomu, že veškerý kód je spuštěn na zařízení, je považován za "veřejného" klienta z důvodu jeho neschopnosti ukládat přihlašovací údaje soukromě/ důvěrně. Další podrobnosti najdete v [tématu Typy klientů oAuth2 a profily.][OAuth2-Client-Types]

## <a name="permissions"></a>Oprávnění

[Klientská aplikace](#client-application) získá přístup k [serveru prostředků](#resource-server) deklarováním žádostí o oprávnění. K dispozici jsou dva typy:

* "Delegovaná" oprávnění, která určují přístup [založený na oboru](#scopes) pomocí delegované autorizace od vlastníka přiřizovaného [prostředku](#resource-owner), jsou zdroji v době běhu prezentována jako [deklarace identity "scp"](#claim) v [přístupovém tokenu](#access-token)klienta .
* "Aplikace" oprávnění, které určují přístup [založený na rolích](#roles) pomocí pověření klientské aplikace nebo identity, jsou prezentovány prostředku za běhu jako ["role" deklarace](#claim) v přístupovém tokenu klienta.

Také se vynořují během procesu [souhlasu,](#consent) což dává správci nebo vlastníkovi prostředku možnost udělit nebo odepřít klientovi přístup k prostředkům v jejich tenantovi.

Požadavky na oprávnění se nakonfigurují na stránce **oprávnění rozhraní API** pro aplikaci na webu Azure [Portal][AZURE-portal]výběrem požadovaných "Delegovaná oprávnění" a "Oprávnění aplikací" (ta vyžadují členství v roli globálního správce). Vzhledem k tomu, že [veřejný klient](#client-application) nemůže bezpečně udržovat pověření, může požadovat pouze delegovaná oprávnění, zatímco [důvěrný klient](#client-application) má možnost požadovat delegovaná i aplikační oprávnění. [Objekt aplikace](#application-object) klienta ukládá deklarovaná oprávnění ve své [vlastnosti requiredResourceAccess][Graph-App-Resource].

## <a name="resource-owner"></a>vlastník prostředku

Podle definice [autorizačního rámce OAuth2][OAuth2-Role-Def], entita schopná udělit přístup k chráněnému prostředku. Pokud je vlastníkem prostředku osoba, označuje se jako koncový uživatel. Například pokud [klientská aplikace](#client-application) chce získat přístup k poštovní schránce uživatele prostřednictvím [rozhraní Microsoft Graph API][Microsoft-Graph], vyžaduje oprávnění od vlastníka prostředků poštovní schránky.

## <a name="resource-server"></a>server prostředků

Podle definice [autorizační ho serveru OAuth2][OAuth2-Role-Def], serveru, který hostuje chráněné prostředky a je schopen přijímat požadavky na chráněné prostředky a reagovat na ně [klientskými aplikacemi,](#client-application) které představují [přístupový token](#access-token). Označuje se také jako server chráněných prostředků nebo aplikace prostředků.

Server prostředků zveřejňuje rozhraní API a vynucuje přístup k chráněným prostředkům prostřednictvím [oborů](#scopes) a [rolí](#roles)pomocí autorizační ho rozhraní OAuth 2.0. Mezi příklady patří [rozhraní Microsoft Graph API,][Microsoft-Graph] které poskytuje přístup k datům klienta Azure AD, a rozhraní API Office 365, která poskytují přístup k datům, jako je pošta a kalendář. 

Stejně jako klientská aplikace, konfigurace identity aplikace prostředků je vytvořena prostřednictvím [registrace](#application-registration) v tenantovi Azure AD, poskytuje aplikační i instanční objekt. Některá rozhraní API poskytovaná společností Microsoft, například rozhraní Microsoft Graph API, mají předregistrované instanční objekty zpřístupněné ve všech klientech během zřizování.

## <a name="roles"></a>role

Podobně jako [obory](#scopes)poskytují role serveru [prostředků](#resource-server) způsob, jak řídit přístup k chráněným prostředkům. Existují dva typy: role "uživatel" implementuje řízení přístupu na základě rolí pro uživatele nebo skupiny, které vyžadují přístup k prostředku, zatímco role "aplikace" implementuje stejné pro [klientské aplikace,](#client-application) které vyžadují přístup.

Role jsou řetězce definované prostředky (například "Schvalovatel výdajů", "Jen pro čtení", "Directory.ReadWrite.All"), spravované na [portálu Azure][AZURE-portal] prostřednictvím [manifestu aplikace](#application-manifest)prostředku a uložené ve [vlastnosti appRoles][Graph-Sp-Resource]prostředku . Portál Azure se také používá k přiřazení uživatelů k rolím "uživatelů" a k konfiguraci oprávnění klientských [aplikací](#permissions) pro přístup k roli "aplikace".

Podrobnou diskusi o rolích aplikací vystavených rozhraním MICROSOFT Graph API naleznete v tématu [Obory oprávnění rozhraní API grafu][Graph-Perm-Scopes]. Příklad implementace krok za krokem najdete v tématu [Správa přístupu pomocí RBAC a portálu Azure][AAD-RBAC].

## <a name="scopes"></a>obory

Stejně jako [role](#roles)poskytují obory způsob, jak [server prostředků](#resource-server) řídit přístup k jeho chráněné prostředky. Obory se používají k implementaci řízení [přístupu na základě oboru][OAuth2-Access-Token-Scopes] pro [klientskou aplikaci,](#client-application) které byl udělen delegovaný přístup k prostředku jeho vlastníkem.

Obory jsou řetězce definované prostředky (například "Mail.Read", "Directory.ReadWrite.All"), spravované na [portálu Azure][AZURE-portal] prostřednictvím [manifestu aplikace](#application-manifest)prostředku a uložené ve [vlastnosti oauth2Permissions][Graph-Sp-Resource]prostředku . Portál Azure se také používá ke konfiguraci [delegovaných oprávnění](#permissions) klientské aplikace pro přístup k oboru.

Konvence pojmenování osvědčených postupů je použití formátu "resource.operation.constraint". Podrobné informace o oborech vystavených rozhraním MICROSOFT Graph API naleznete v tématu [Obory oprávnění rozhraní API rozhraní Graph .][Graph-Perm-Scopes] Obory vystavené službami Office 365 najdete v [článku Odkaz na oprávnění rozhraní API Office 365][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpečení

Podepsaný dokument obsahující deklarace identity, například token OAuth2 nebo kontrolní výraz SAML 2.0. Pro [udělení autorizace](#authorization-grant)OAuth2 , [přístupový token](#access-token) (OAuth2) a [ID token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) jsou typy tokenů zabezpečení, které jsou implementovány jako [JSON webový token (JWT)][JWT].

## <a name="service-principal-object"></a>objekt instančního objektu

Když zaregistrujete nebo aktualizujete aplikaci na [portálu Azure][AZURE-portal], portál vytvoří nebo aktualizuje [objekt aplikace](#application-object) a odpovídající objekt instančního objektu pro tohoto klienta. Objekt aplikace *definuje* konfiguraci identity aplikace globálně (napříč všemi klienty, kde byl přidruženým aplikacím udělen přístup) a je šablona, ze které jsou *odvozeny* odpovídající objekty instančního objektu pro použití místně za běhu (v konkrétním tenantovi).

Další informace naleznete v [tématu Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Proces [klientské aplikace,](#client-application) která inicializuje ověřování koncových uživatelů a zachytává související stav za účelem získání [tokenu zabezpečení](#security-token) a vymezení relace aplikace do tohoto stavu. Stav může obsahovat artefakty, jako jsou informace o profilu uživatele a informace odvozené z deklarací tokenu.

Funkce přihlášení aplikace se obvykle používá k implementaci jednotného přihlašování (SSO). Může také předcházet funkce "registrace", jako vstupní bod pro koncového uživatele získat přístup k aplikaci (při prvním přihlášení). Funkce registrace se používá ke shromažďování a zachování dalšího stavu specifického pro uživatele a může vyžadovat [souhlas uživatele](#consent).

## <a name="sign-out"></a>adresa URL

Proces neověření koncového uživatele, odpojení stavu uživatele přidruženého k relaci [klientské aplikace](#client-application) během [přihlášení](#sign-in)

## <a name="tenant"></a>Nájemce

Instance adresáře Azure AD se označuje jako klient azure ad. Poskytuje několik funkcí, včetně:

* služba registru pro integrované aplikace
* ověřování uživatelských účtů a registrovaných aplikací
* Koncové body REST potřebné pro podporu různých protokolů včetně OAuth2 a SAML, včetně [koncového bodu autorizace](#authorization-endpoint), [koncového bodu tokenu](#token-endpoint) a "společného" koncového bodu používaného [víceklientské aplikace](#multi-tenant-application).

Tenanti Azure AD se během registrace vytvářejí a přidružují k předplatným Azure a Office 365 a poskytují funkce správy identit y & přístupu pro předplatné. Správci předplatného Azure můžou taky vytvořit další klienty Azure AD prostřednictvím portálu Azure. Podrobnosti o různých způsobech získání přístupu k tenantovi najdete v [tématu Jak získat klienta Služby Azure Active Directory.][AAD-How-To-Tenant] Podrobnosti o vztahu mezi předplatnými a klientem Azure AD najdete v tématu [Jak jsou předplatná Azure přidružená k Azure Active Directory.][AAD-How-Subscriptions-Assoc]

## <a name="token-endpoint"></a>koncový bod tokenu

Jeden z koncových bodů implementovaných [autorizačním serverem](#authorization-server) pro podporu [autorizačních grantů](#authorization-grant)OAuth2 . V závislosti na udělení jej lze použít k získání [přístupového tokenu](#access-token) (a souvisejícího [tokenu](#client-application)aktualizace) klientovi nebo [tokenu ID](#id-token) při použití s protokolem [OpenID Connect.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Klient založený na uživatelském agentovi

Typ [klientské aplikace,](#client-application) která stahuje kód z webového serveru a spouští se v rámci uživatelského agenta (například webového prohlížeče), například jednostránkové aplikace (SPA). Vzhledem k tomu, že veškerý kód je spuštěn na zařízení, je považován za "veřejného" klienta z důvodu jeho neschopnosti ukládat přihlašovací údaje soukromě/ důvěrně. Další informace naleznete [v tématu OAuth2 typy klientů a profily][OAuth2-Client-Types].

## <a name="user-principal"></a>uživatelská objekt

Podobně jako způsob, jakým objekt instančního objektu slouží k reprezentaci instance aplikace, objekt zaregistrovaný objekt uživatele je jiný typ zaregistrovaný objekt zabezpečení, který představuje uživatele. [Typ prostředku uživatele][Graph-User-Resource] aplikace Microsoft Graph definuje schéma objektu uživatele, včetně vlastností souvisejících s uživatelem, jako je jméno a příjmení, hlavní jméno uživatele, členství v rolích adresáře atd. To poskytuje konfiguraci identity uživatele pro Azure AD vytvořit uživatelský objekt v běhu. Uživatelský objekt zabezpečení se používá k reprezentaci ověřeného uživatele pro jednotné přihlašování, nahrávání delegování [souhlasu,](#consent) rozhodování o řízení přístupu atd.

## <a name="web-client"></a>webový klient

Typ [klientské aplikace,](#client-application) která spouští veškerý kód na webovém serveru a může fungovat jako "důvěrný" klient bezpečným uložením pověření na server. Další informace naleznete [v tématu OAuth2 typy klientů a profily][OAuth2-Client-Types].

## <a name="next-steps"></a>Další kroky

[Příručka pro vývojáře platformy microsoft identit][AAD-Dev-Guide] je vstupní stránka, která se používá pro všechna témata související s vývojem platformy microsoftu pro vývoj, včetně přehledu [integrace aplikací][AAD-How-To-Integrate] a základů [ověřování platformy identit microsoftu a podporovaných scénářů ověřování][AAD-Auth-Scenarios]. Ukázky kódu najdete také & návody, jak rychle začít pracovat na [GitHubu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

V následující části s poznámkami můžete poskytnout zpětnou vazbu a pomoci tento obsah upřesnit a utvářet, včetně žádostí o nové definice nebo aktualizaci existujících definic!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
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
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
