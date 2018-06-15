---
title: Glosář vývojáře Azure Active Directory | Microsoft Docs
description: Seznam podmínek pro běžně používané funkce a koncepty pro vývojáře Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 12c1a4b2b1f3e433721b9c8a335c6b55de746643
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158145"
---
# <a name="azure-active-directory-developer-glossary"></a>Glosář vývojáře Azure Active Directory
Tento článek obsahuje definice pro některé základní koncepty vývojáře Azure Active Directory (AD), což je užitečné při získávání informací o vývoj aplikací pro Azure AD.

## <a name="access-token"></a>přístupový token
Typ [token zabezpečení](#security-token) vydaný [serveru ověřování](#authorization-server)a používá [klientská aplikace](#client-application) pro přístup [chráněný server prostředků](#resource-server). Obvykle ve formě [JSON Web Token (JWT)][JWT], token ztělesňuje autorizace udělit klienta pomocí [vlastník prostředku](#resource-owner), pro požadovanou úroveň přístupu. Token obsahuje všechny použitelné [deklarace identity](#claim) o subjektu, povolení klientská aplikace můžete použít jako formu přihlašovacích údajů při přístupu k danému prostředku. Také to eliminuje potřebu vlastníka prostředku vystavit přihlašovací údaje do klienta.

Přístupové tokeny jsou někdy označovány jako "Uživatele + aplikace" nebo "Aplikaci jen", v závislosti na reprezentované přihlašovací údaje. Například, když používá klientská aplikace:

* [Udělení autorizace "Autorizační kód"](#authorization-grant), koncový uživatel se ověřuje nejprve jako vlastníka prostředku, delegování autorizace klientovi přístup k prostředku. Klient se ověří následně při získání tokenu přístupu. Token může někdy se označuje konkrétně jako token "Uživatele + aplikace" jako reprezentuje oba uživatele, který oprávnění klientská aplikace a aplikace.
* [Udělení autorizace "Pověření klienta"](#authorization-grant), klient poskytuje jedinou ověřování, funguje bez vlastníka prostředku ověřování nebo autorizaci, takže token může být někdy označuje jako tokenu "Jen aplikace".

V tématu [odkaz tokenu Azure AD] [ AAD-Tokens-Claims] další podrobnosti.

## <a name="application-id-client-id"></a>id aplikace (id klienta)
Jedinečný identifikátor Azure AD problémy k registraci aplikace, který identifikuje konkrétní aplikace a související konfigurace. Toto id aplikace ([id klienta](https://tools.ietf.org/html/rfc6749#page-15)) se používá při provádění ověřování požadavků a je uvedené do knihoven ověřování v okamžiku vývoje. Id aplikace (id klienta) není tajný klíč. 

## <a name="application-manifest"></a>Manifest aplikace
Funkce poskytované [portál Azure][AZURE-portal], který vytvoří reprezentaci JSON konfigurace identity aplikace, používá jako mechanismus pro aktualizaci přidružené [ Aplikace] [ AAD-Graph-App-Entity] a [ServicePrincipal] [ AAD-Graph-Sp-Entity] entity. V tématu [pochopení manifest aplikace Azure Active Directory] [ AAD-App-Manifest] další podrobnosti.

## <a name="application-object"></a>objekt aplikace
Pokud jste registrace nebo aktualizovat aplikaci v [portál Azure][AZURE-portal], portálu pro vytvoření nebo aktualizace objekt aplikace a odpovídající [objekt zabezpečení služby](#service-principal-object) pro tohoto klienta. Objekt aplikace *definuje* aplikace je konfigurace identity globálně (v rámci všech klientů kde má přístup), poskytuje šablony, ze kterého se odpovídající hlavní objekty služby  *odvozené* pro použití místně za běhu (v konkrétní klienta).

V tématu [aplikací a hlavní objekty služeb] [ AAD-App-SP-Objects] Další informace.

## <a name="application-registration"></a>registrace aplikace
Aby bylo možné aplikaci do integrovat a delegovat mu funkce identita a správa přístupu ke službě Azure AD, je nutné jej zaregistrovat s Azure AD [klienta](#tenant). Při registraci vaší aplikace s Azure AD, tím konfigurace aplikace identity pro vaši aplikaci, díky kterému jej integraci se službou Azure AD a pomocí funkce, jako například:

* Robustní správu z jednotného přihlašování pomocí Azure AD Identity Management a [OpenID Connect] [ OpenIDConnect] implementace protokolu
* Zprostředkované přístup k [chráněné zdroje](#resource-server) podle [klientské aplikace](#client-application), přes Azure AD OAuth 2.0 [serveru ověřování](#authorization-server) implementace
* [Souhlas framework](#consent) pro Správa klientského přístupu k chráněným prostředkům, podle autorizace vlastníka prostředku.

V tématu [integrace aplikací s Azure Active Directory] [ AAD-Integrating-Apps] další podrobnosti.

## <a name="authentication"></a>Ověřování
Operace náročné strany na oprávněné pověření, poskytuje základ pro vytvoření objektu zabezpečení, který se má použít pro řízení přístupu a identit. Během [udělení autorizace OAuth2](#authorization-grant) například strany ověřování probíhá naplňování roli buď [vlastník prostředku](#resource-owner) nebo [klientská aplikace](#client-application), v závislosti na udělení použít.

## <a name="authorization"></a>Autorizace
V rámci udělení hlavní oprávnění ověření zabezpečení něco udělat. V tomto programovacím modelu služby Azure AD jsou dva případy použití primární:

* Během [udělení autorizace OAuth2](#authorization-grant) toku: při [vlastník prostředku](#resource-owner) uděluje oprávnění pro [klientská aplikace](#client-application), a umožnil tak klientovi přístup k prostředku prostředky vlastníka.
* Během přístupu k prostředkům klienta: jak je implementované [server prostředků](#resource-server)pomocí [deklarace identity](#claim) hodnoty k dispozici v [přístupový token](#access-token) aby rozhodnutí o řízení přístupu podle nich.

## <a name="authorization-code"></a>Autorizační kód
Zkrátí "token" poskytované [klientská aplikace](#client-application) pomocí [koncový bod autorizace](#authorization-endpoint), v rámci toku "autorizační kód", jeden z čtyři OAuth2 [autorizací udělí](#authorization-grant). Kód se vrátí do klientské aplikace v reakci na ověřování [vlastník prostředku](#resource-owner), označující vlastníka prostředku má delegovaná oprávnění pro přístup k požadované prostředky. V rámci toku kód později uplatněný pro [přístupový token](#access-token).

## <a name="authorization-endpoint"></a>Koncový bod autorizace
Jeden z koncových bodů implementované [serveru ověřování](#authorization-server)používané pro interakci s [vlastník prostředku](#resource-owner) Chcete-li poskytovat [udělení autorizace](#authorization-grant) během OAuth2 tok poskytování autorizačních. V závislosti na toku udělení autorizace používá, skutečný grant zadat se může lišit, včetně [autorizační kód](#authorization-code) nebo [token zabezpečení](#security-token).

Najdete v OAuth2 specifikaci [typy udělení autorizace] [ OAuth2-AuthZ-Grant-Types] a [koncový bod autorizace] [ OAuth2-AuthZ-Endpoint] části a [OpenIDConnect specifikace] [ OpenIDConnect-AuthZ-Endpoint] další podrobnosti.

## <a name="authorization-grant"></a>udělení autorizace
Přihlašovací údaje představující [vlastník prostředku](#resource-owner) [autorizace](#authorization) přístup k jeho chráněným prostředkům udělit [klientská aplikace](#client-application). Klientská aplikace můžete použít jednu z [čtyři typy definované rozhraní autorizace OAuth2 udělení] [ OAuth2-AuthZ-Grant-Types] získat udělení, v závislosti na typu nebo požadavky na klienta: "udělení autorizačního kódu", "klienta přihlašovací údaje umožní","implicitní grant"a"udělit oprávnění hesla vlastníka prostředku". Přihlašovací údaje vrácen do klienta je buď [přístupový token](#access-token), nebo [autorizační kód](#authorization-code) (vyměňují později pro přístupový token), v závislosti na typu udělení autorizace používá.

## <a name="authorization-server"></a>Autorizace serveru
Podle definice [OAuth2 autorizace Framework][OAuth2-Role-Def], příslušné k udělování přístupu serveru tokenů se má [klienta](#client-application) po úspěšném ověření [vlastník prostředku](#resource-owner) a získání jeho povolení. A [klientská aplikace](#client-application) komunikuje se serverem autorizace za běhu prostřednictvím jeho [autorizace](#authorization-endpoint) a [tokenu](#token-endpoint) koncových bodů, v souladu s OAuth2 definované [autorizace uděluje](#authorization-grant).

V případě integrace aplikací Azure AD, Azure AD implementuje roli serveru autorizace pro aplikace Azure AD a služby Microsoft rozhraní API, například [Microsoft Graph API][Microsoft-Graph].

## <a name="claim"></a>deklarace identity
A [token zabezpečení](#security-token) obsahuje deklarace identity, které poskytují kontrolní výrazy o jedné entity (například [klientská aplikace](#client-application) nebo [vlastník prostředku](#resource-owner)) k jiné entitě (například [server prostředků](#resource-server)). Deklarace identity jsou páry název/hodnota, které předávání faktů o token subjektu (například objekt zabezpečení, která byla ověřena pomocí [serveru ověřování](#authorization-server)). Daný token obsahuje deklarace identity jsou závislé na několika proměnných, včetně typ tokenu, typ pověření pro ověření předmět konfigurace aplikace, atd.

V tématu [odkaz tokenu Azure AD] [ AAD-Tokens-Claims] další podrobnosti.

## <a name="client-application"></a>klientské aplikace
Podle definice [Framework ověřování OAuth2][OAuth2-Role-Def], aplikace, která umožňuje chráněných prostředků požadavky jménem [vlastník prostředku](#resource-owner). Termín "client" není určeno žádné vlastností implementace konkrétní hardwaru (například, jestli aplikace spustí na serveru, ploše nebo jiné zařízení). 

Klientská aplikace požaduje [autorizace](#authorization) od vlastníka prostředku se účastnit [udělení autorizace OAuth2](#authorization-grant) toku a může získat přístup k rozhraní API nebo data jménem vlastníka prostředku. Rozhraní Framework ověřování OAuth2 [definuje dva typy klientů][OAuth2-Client-Types], "důvěrné informace" a "veřejná", podle schopnost klienta zachovávat mlčenlivost o svoje přihlašovací údaje. Aplikace můžete implementovat [webového klienta (důvěrné)](#web-client) na webovém serveru, která se spouští [nativního klienta (veřejných)](#native-client) instalovat na zařízení, nebo [klienta na základě uživatelského agenta (veřejných)](#user-agent-based-client)která se spouští v prohlížeči zařízení.

## <a name="consent"></a>Vyjádřit souhlas.
Proces [vlastník prostředku](#resource-owner) registraci [klientská aplikace](#client-application), přístup k chráněným prostředkům v rámci konkrétní [oprávnění](#permissions), jménem vlastník prostředku. Záviset na oprávněních, kterou klient požaduje správce nebo uživatel se vyzve k vyjádření souhlasu pro povolení přístupu k datům jejich organizace nebo jednotlivce v uvedeném pořadí. Poznámka: v [víceklientské](#multi-tenant-application) scénář, aplikace [instanční objekt](#service-principal-object) je také zaznamenávají v klientovi consenting uživatele.

## <a name="id-token"></a>ID tokenu
[OpenID Connect] [ OpenIDConnect-ID-Token] [token zabezpečení](#security-token) poskytované [serveru ověřování](#authorization-server) [koncový bod autorizace](#authorization-endpoint), který obsahuje [deklarace identity](#claim) vztahující se k ověření koncového uživatele [vlastník prostředku](#resource-owner). Jako přístupový token, jsou také reprezentované tokeny typu ID jako digitálně podepsaných [JSON Web Token (JWT)][JWT]. Na rozdíl od přístupový token, ale ID token deklarace identity nepoužívají se pro účely související s přístupem k prostředkům a konkrétně řízení přístupu.

V tématu [odkaz tokenu Azure AD] [ AAD-Tokens-Claims] další podrobnosti.

## <a name="multi-tenant-application"></a>víceklientské aplikace
Třída aplikace, která umožňuje přihlášení a [souhlas](#consent) uživatelé zřízené v žádné službě Azure AD [klienta](#tenant), včetně klientů, než kde je klient zaregistrovaný. [Nativní klient](#native-client) aplikace jsou víceklientské ve výchozím nastavení, zatímco [webového klienta](#web-client) a [webové prostředků nebo rozhraní API](#resource-server) aplikací mít možnost vybrat mezi jedním nebo více klientů. Naopak webové aplikace registrovaný jako jednoho klienta, by pouze povolit přihlášení z uživatelské účty, které jsou zřízené ve stejném klientovi jako ten, kde je registrovaná aplikace.

V tématu [jak se přihlásit žádné uživatele Azure AD pomocí vzoru víceklientské aplikace] [ AAD-Multi-Tenant-Overview] další podrobnosti.

## <a name="native-client"></a>Nativní klient systému
Typ [klientská aplikace](#client-application) je nativní aplikace na zařízení. Vzhledem k tomu, že veškerý kód se spustí na zařízení, bude považován za "veřejná" klienta z důvodu jeho nemohou k uložení pověření soukromě nebo jako s důvěrnými. V tématu [OAuth2 klienta typy a profily] [ OAuth2-Client-Types] další podrobnosti.

## <a name="permissions"></a>oprávnění
A [klientská aplikace](#client-application) získá přístup k [server prostředků](#resource-server) deklarováním žádosti o oprávnění. K dispozici jsou dva typy:

* "Delegovaný" oprávnění, které určují [obor](#scopes) přistupovat pomocí delegovaného autorizace z přihlášeného [vlastník prostředku](#resource-owner), jsou uvedené k prostředku v době spuštění jako ["spojovací bod služby" deklarace identity](#claim) v klienta [přístupový token](#access-token).
* Oprávnění "Aplikace", které určují [na základě rolí](#roles) přistupovat pomocí přihlašovacích údajů nebo identitu aplikace klienta, jsou uvedené k prostředku v době spuštění jako [deklarace identity "role"](#claim) v klienta přístupový token.

Budou také surface během [souhlas](#consent) procesu, udělíte správce nebo vlastníka prostředku možnost udělit nebo odepřít klientský přístup k prostředkům v jejich klienta.

Žádosti o oprávnění jsou nakonfigurované na "Aplikace" / "Nastavení" kartě [portál Azure][AZURE-portal], v části "Požadovaných oprávnění", výběrem požadované "Delegovaná oprávnění" a "aplikace Oprávnění"(k tomu vyžaduje členství v roli globálního správce). Protože [veřejné klienta](#client-application) nelze bezpečně udržovat přihlašovací údaje, může požadovat pouze delegovaných oprávnění, při [důvěrné klienta](#client-application) má umožňuje žádat o delegovanou a aplikace oprávnění. Klienta [objektu application](#application-object) ukládá deklarované oprávnění v jeho [requiredResourceAccess vlastnost][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>vlastník prostředku
Podle definice [OAuth2 autorizace Framework][OAuth2-Role-Def], entitu schopná udělení přístupu k chráněnému prostředku. Pokud je vlastník prostředku osoby, je jako koncový uživatel uvedené. Například když [klientská aplikace](#client-application) požaduje přístup k poštovní schránky uživatele prostřednictvím [Microsoft Graph API][Microsoft-Graph], vyžaduje oprávnění od vlastníka prostředku poštovní schránky.

## <a name="resource-server"></a>server prostředků
Podle definice [OAuth2 autorizace Framework][OAuth2-Role-Def], že hostitelé chráněné zdroje, schopný přijímat a reagovat na serveru chráněný prostředek požadavků podle [klienta aplikace](#client-application) této přítomen [přístupový token](#access-token). Také označované jako chráněného prostředku serveru nebo prostředků aplikace.

Server prostředků zpřístupňuje rozhraní API a vynucuje přístup k jeho chráněným prostředkům prostřednictvím [obory](#scopes) a [role](#roles), pomocí rozhraní autorizace OAuth 2.0. Mezi příklady patří Azure AD Graph API, která poskytuje přístup k datům klienta Azure AD a rozhraní API Office 365, které poskytují přístup k datům, jako je například pošty a kalendáři. Obě tyto jsou také přístupné prostřednictvím [Microsoft Graph API][Microsoft-Graph]. 

Stejně jako klientskou aplikaci, je stanoven konfigurace identity prostředků aplikace pomocí [registrace](#application-registration) v klienta Azure AD, poskytuje aplikace a služby objekt zabezpečení. Některé poskytovaný společností Microsoft rozhraní API, například Azure AD Graph API předem zaregistrovali objekty služby, které jsou k dispozici ve všech klientů při zřizování.

## <a name="roles"></a>role
Jako [obory](#scopes), role poskytují způsob, jak [server prostředků](#resource-server) které řídí přístup k jeho chráněným prostředkům. Existují dva typy: roli "user" implementuje řízení přístupu na základě rolí pro uživatele nebo skupiny, které vyžadují přístup k prostředku, zatímco o roli "aplikace" implementuje pro stejný [klientské aplikace](#client-application) , které vyžadují přístup.

Role jsou definované prostředků řetězců (například "výdajů schvalovatel", "Jen pro čtení", "Directory.ReadWrite.All"), kterou spravuje v [portál Azure] [ AZURE-portal] prostřednictvím prostředku [aplikace manifest](#application-manifest)a uloženy v prostředku [appRoles vlastnost][AAD-Graph-Sp-Entity]. Portál Azure slouží také k přiřazení uživatele k rolím "user" a proveďte konfiguraci klienta [oprávnění aplikací](#permissions) pro přístup roli "aplikace".

Podrobnou diskuzi o aplikační role vystavené Azure AD Graph API, najdete v části [obory oprávnění rozhraní API grafu][AAD-Graph-Perm-Scopes]. Implementace podrobný příklad najdete v tématu [řízení přístupu v cloudových aplikacích pomocí služby Azure AD na základě Role][Duyshant-Role-Blog].

## <a name="scopes"></a>Obory
Jako [role](#roles), obory poskytují způsob, jak [server prostředků](#resource-server) které řídí přístup k jeho chráněným prostředkům. Obory slouží k implementaci [obor] [ OAuth2-Access-Token-Scopes] pro přístup k řízení, [klientská aplikace](#client-application) , nebyla zadána Delegovaný přístup k prostředku jeho vlastníka.

Obory jsou definované prostředků řetězců (například "Mail.Read", "Directory.ReadWrite.All"), spravovat [portál Azure] [ AZURE-portal] prostřednictvím prostředku [manifest aplikace](#application-manifest)a uloženy v prostředku [oauth2Permissions vlastnost][AAD-Graph-Sp-Entity]. Portál Azure také slouží ke konfiguraci klienta aplikace [delegovaná oprávnění](#permissions) pro přístup k oboru.

Nejlepší postup zásady vytváření názvů, je použití formátu "resource.operation.constraint". Podrobnou diskuzi o obory vystavené Azure AD Graph API, najdete v části [obory oprávnění rozhraní API grafu][AAD-Graph-Perm-Scopes]. Obory vystavené službám Office 365, najdete v části [referenční dokumentace rozhraní API Office 365 oprávnění][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpečení
Podepsaný dokument obsahující deklarace identity, například tokenu OAuth2 nebo kontrolního výrazu SAML 2.0. Pro OAuth2 [udělení autorizace](#authorization-grant), [přístupový token](#access-token) (OAuth2) a [ID Token](http://openid.net/specs/openid-connect-core-1_0.html#IDToken) jsou typy tokenů zabezpečení, které jsou implementované jako [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>objekt zabezpečení služby
Když jste registrace nebo aktualizovat aplikaci v [portál Azure][AZURE-portal], portálu pro vytvoření nebo aktualizace i [objektu application](#application-object) a odpovídající objekt zabezpečení služby pro tohoto klienta. Objekt aplikace *definuje* konfigurace identity aplikace globálně (v rámci všech klientů kde v přidružené aplikaci udělit přístup), a je šablona, ze kterého jeho odpovídající instančního objektu objekty jsou *odvozené* pro použití místně za běhu (v konkrétní klienta).

V tématu [aplikací a hlavní objekty služeb] [ AAD-App-SP-Objects] Další informace.

## <a name="sign-in"></a>přihlášení
Proces [klientská aplikace](#client-application) inicializaci ověřování koncového uživatele a zaznamenávání související stav, za účelem získání [token zabezpečení](#security-token) a obor relace aplikace na tento stav. Stavu může obsahovat artefaktů, jako jsou informace o profilu uživatele a informace odvozené z tokenu deklarací identity.

Funkce přihlášení aplikace se obvykle používá k implementaci jednotného přihlašování (SSO). Ho může také předcházet "registrace" funkce, jako vstupní bod pro koncové uživatele k získání přístupu k aplikaci (při prvním přihlášení). Registrace funkce slouží ke shromažďování a zachovat další stav specifická pro uživatele a může vyžadovat [souhlas uživatele](#consent).

## <a name="sign-out"></a>adresa URL
Proces zrušení ověřovací přidružené koncového uživatele, odpojení stavu uživatele [klientská aplikace](#client-application) během relaci [přihlášení](#sign-in)

## <a name="tenant"></a>tenant
Instance adresáře služby Azure AD se označuje jako klient služby Azure AD. Poskytuje několik funkcí, včetně:

* Služba registru pro integrované aplikace
* ověřování uživatelských účtů a registrované aplikace
* Koncové body REST, které jsou potřeba pro podporu různých protokolů, včetně OAuth2 a SAML, včetně [koncový bod autorizace](#authorization-endpoint), [koncový bod tokenu](#token-endpoint) a "běžné" koncovým bodem používaným [ víceklientským aplikacím](#multi-tenant-application).

Během registrace, poskytnete Správa identit a přístupu funkce pro předplatné jsou klienty Azure AD vytvořené nebo přidružit k předplatnému Azure a Office 365. Správci předplatného Azure můžete vytvořit také další služby Azure AD tenantů prostřednictvím portálu Azure. V tématu [postup získání klienta Azure Active Directory] [ AAD-How-To-Tenant] podrobnosti o různých způsobech získáte přístup do klienta. V tématu [asociování předplatných Azure se službou Azure Active Directory] [ AAD-How-Subscriptions-Assoc] podrobnosti o vztahu mezi předplatnými a klient služby Azure AD.

## <a name="token-endpoint"></a>Koncový bod tokenu
Jeden z koncových bodů implementované [serveru ověřování](#authorization-server) podpory OAuth2 [autorizace uděluje](#authorization-grant). V závislosti na udělení, může být použitý k získání [přístupový token](#access-token) (a související token "aktualizovat") na [klienta](#client-application), nebo [ID token](#ID-token) při použití s [OpenID Připojit] [ OpenIDConnect] protokolu.

## <a name="user-agent-based-client"></a>Na základě uživatelského agenta klienta
Typ [klientská aplikace](#client-application) zda stáhne kód z webového serveru a spustí v rámci uživatelského agenta (například webový prohlížeč), jako je například jedné stránce aplikace (SPA). Vzhledem k tomu, že veškerý kód se spustí na zařízení, bude považován za "veřejná" klienta z důvodu jeho nemohou k uložení pověření soukromě nebo jako s důvěrnými. V tématu [OAuth2 klienta typy a profily] [ OAuth2-Client-Types] další podrobnosti.

## <a name="user-principal"></a>hlavní název uživatele
Podobně jako objekt zabezpečení služby se používá k reprezentaci instanci aplikace, je objekt zabezpečení uživatele jiný typ objektu, zabezpečení, který reprezentuje uživatele. Azure AD Graph [entitu uživatele] [ AAD-Graph-User-Entity] definuje schéma pro objekt uživatele, včetně vlastnosti související s uživatelem, například křestní jméno a příjmení, hlavní název uživatele, členství v rolích directory atd. To poskytuje konfigurace identity uživatele pro Azure AD k navázání hlavní název uživatele při spuštění. Hlavní název uživatele se používá k reprezentování ověřeného uživatele pro jednotné přihlašování, zaznamenávání [souhlas](#consent) delegování, provedení rozhodnutí o řízení přístupu, atd.

## <a name="web-client"></a>Webový klient
Typ [klientská aplikace](#client-application) , která se spouští všechny kódu na webovém serveru a může fungovat jako "důvěrné informace" klienta bezpečně uloží pověření uživatele na serveru. V tématu [OAuth2 klienta typy a profily] [ OAuth2-Client-Types] další podrobnosti.

## <a name="next-steps"></a>Další postup
[Příručka vývojáře pro Azure AD] [ AAD-Dev-Guide] je cílová stránka pro použití při vývoji všechny Azure AD související témata, včetně přehledu [integraci aplikací] [ AAD-How-To-Integrate] a základní informace o [ověřování Azure AD a scénáře podporované ověřování][AAD-Auth-Scenarios]. Taky můžete najít ukázky kódu & kurzy o tom, jak získat rychle vytvořit a spustit na [Githubu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah, včetně žádostí pro nové definice nebo aktualizuje existující!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ../active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
