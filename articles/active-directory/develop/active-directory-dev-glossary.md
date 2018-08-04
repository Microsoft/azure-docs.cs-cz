---
title: Glosář pro vývojáře Azure Active Directory | Dokumentace Microsoftu
description: Seznam termínů pro běžně používané funkce a koncepty pro vývojáře Azure Active Directory.
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
ms.reviewer: elisol
ms.openlocfilehash: 95ddfb6f2beb303a6adcfe4f7bc47d022ad862a6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503103"
---
# <a name="azure-active-directory-developer-glossary"></a>Glosář pro vývojáře Azure Active Directory
Tento článek obsahuje definice pro některý ze základní koncepce pro vývojáře Azure Active Directory (AD), což je užitečné při získávání informací o vývoji aplikací pro Azure AD.

## <a name="access-token"></a>přístupový token
Typ [token zabezpečení](#security-token) vydané [autorizační server](#authorization-server)a používá [klientská aplikace](#client-application) za účelem přístupu k [chráněný server prostředků](#resource-server). Obvykle ve formě [JSON Web Token (JWT)][JWT], token, který ztělesňuje uděleného klientovi pomocí pověření [vlastníka prostředku](#resource-owner), pro požadovanou úroveň přístupu. Token, který obsahuje všechny použitelné [deklarace identity](#claim) o subjektu, umožňující klientské aplikaci používat jako formu přihlašovacích údajů při přístupu k danému prostředku. Také to eliminuje potřebu vlastník prostředku k odhalení přihlašovacích údajů klienta.

Přístupové tokeny jsou někdy označovány jako "Uživatel + aplikace" nebo "Jen pro aplikace", v závislosti na reprezentované přihlašovací údaje. Například pokud klientská aplikace bude používat:

* [Udělení autorizace "Autorizační kód"](#authorization-grant), koncový uživatel se ověřuje nejdříve jako vlastníka prostředku, delegování povolení klientům přístup k prostředku. Klient se ověří později při získání přístupového tokenu. Token, který můžete někdy odkazovat přesněji řečeno jako token "Uživatel + aplikace", protože představuje na uživatele, která oprávnění klientská aplikace a aplikace.
* [Udělení autorizace "Přihlašovací údaje pro klienta"](#authorization-grant), klient poskytuje jedinou ověřování fungovat bez ověřování/autorizace vlastníka prostředku, takže token, který může být někdy se nazývá "Jen pro aplikace" token.

Zobrazit [odkaz tokenu Azure AD] [ AAD-Tokens-Claims] další podrobnosti.

## <a name="application-id-client-id"></a>id aplikace (id klienta)
Jedinečný identifikátor služby Azure AD problémů, které registraci aplikace, který identifikuje konkrétní aplikaci a přidružené konfigurace. Toto id aplikace ([id klienta](https://tools.ietf.org/html/rfc6749#page-15)) se používá při ověřování požadavků a je do knihoven ověřování k dispozici v době vývoje. Id aplikace (id klienta) není tajným kódem. 

## <a name="application-manifest"></a>manifest aplikace
Funkce poskytované [webu Azure portal][AZURE-portal], který vytvoří reprezentaci JSON konfigurace identity aplikace, používat jako mechanismus pro aktualizaci přidružené [ Aplikace] [ AAD-Graph-App-Entity] a [ServicePrincipal] [ AAD-Graph-Sp-Entity] entity. Zobrazit [vysvětlení manifestu aplikace Azure Active Directory] [ AAD-App-Manifest] další podrobnosti.

## <a name="application-object"></a>Objekt aplikace
Když jste register nebo aktualizovat aplikace v [webu Azure portal][AZURE-portal], portálu vytvoření/aktualizace objekt aplikace a odpovídající [instanční objekt](#service-principal-object) pro tohoto tenanta. Objekt aplikace *definuje* aplikaci prvku konfigurace identity globálně (ve všech tenantech kde má přístup), poskytuje šablony, ze kterého se odpovídající hlavní objekty služby  *odvozené* používat místně v době běhu (v konkrétního tenanta).

Další informace najdete v tématu [aplikační a instanční objekty][AAD-App-SP-Objects].

## <a name="application-registration"></a>Registrace aplikace
Aby bylo možné povolit aplikace a integrovat delegovat funkce správy identit a přístupu ke službě Azure AD, je nutné jej zaregistrovat s Azure AD [tenanta](#tenant). Při registraci vaší aplikace s Azure AD poskytujete konfigurace identity pro vaši aplikaci, což umožňuje integraci s Azure AD a pomocí funkcí, jako:

* Robustní správu z jednotné přihlašování pomocí Azure AD Identity Management a [OpenID Connect] [ OpenIDConnect] implementace protokolu
* Zprostředkované přístup k [chráněné zdroje](#resource-server) podle [klientské aplikace](#client-application), prostřednictvím služby Azure AD OAuth 2.0 [autorizační server](#authorization-server) implementace
* [Rozhraní pro udělování souhlasu](#consent) pro Správa klientského přístupu k chráněným prostředkům podle autorizace vlastníka prostředku.

Zobrazit [integrace aplikací s Azure Active Directory] [ AAD-Integrating-Apps] další podrobnosti.

## <a name="authentication"></a>Ověřování
Operace náročné stranu pro legitimních přihlašovacích údajů, poskytují základ pro vytvoření objektu zabezpečení pro řízení přístupu a identit. Během [udělení autorizace OAuth2](#authorization-grant) stran ověřování je třeba vyplnění roli buď [vlastníka prostředku](#resource-owner) nebo [klientská aplikace](#client-application), v závislosti na udělení použít.

## <a name="authorization"></a>Autorizace
V rámci udělení oprávnění objektu zabezpečení v ověření zabezpečení něco udělat. Existují dva hlavní případy použití v programovacím modelu služby Azure AD:

* Během [udělení autorizace OAuth2](#authorization-grant) toku: když [vlastníka prostředku](#resource-owner) uděluje oprávnění k [klientská aplikace](#client-application), a umožnil tak klientovi přístup k prostředku prostředky vlastníka.
* Při přístupu k prostředkům klienta: jak je implementované [server prostředků](#resource-server), pomocí [deklarace identity](#claim) hodnoty prezentovat [přístupový token](#access-token) provádět rozhodnutí o řízení přístupu na základě jejich.

## <a name="authorization-code"></a>autorizační kód
Krátkodobý "token" k dispozici na [klientská aplikace](#client-application) podle [koncový bod autorizace](#authorization-endpoint), jako součást toku "autorizační kód", jeden ze čtyř OAuth2 [autorizace uděluje](#authorization-grant). Kód se vrátí do klientské aplikace v reakci na ověřování [vlastníka prostředku](#resource-owner), která vlastník prostředku delegoval autorizaci pro přístup k požadovaným prostředkům. Jako součást toku kódu je později uplatnit [přístupový token](#access-token).

## <a name="authorization-endpoint"></a>koncový bod autorizace
Jeden z koncových bodů implementované [autorizační server](#authorization-server), která se používá k interakci s [vlastníka prostředku](#resource-owner) negace [udělení autorizace](#authorization-grant) během OAuth2 toku udělení autorizace. V závislosti na toku udělení autorizace použít skutečné udělení, zadané se může lišit, včetně [autorizační kód](#authorization-code) nebo [token zabezpečení](#security-token).

V tématu Specifikace OAuth2 [typy udělení autorizace] [ OAuth2-AuthZ-Grant-Types] a [koncový bod autorizace] [ OAuth2-AuthZ-Endpoint] oddíly a [OpenIDConnect specifikace] [ OpenIDConnect-AuthZ-Endpoint] další podrobnosti.

## <a name="authorization-grant"></a>udělení autorizace
Přihlašovací údaje představující [vlastníka prostředku](#resource-owner) [autorizace](#authorization) pro přístup k chráněné prostředky udělit [klientská aplikace](#client-application). Klientská aplikace můžete použít jednu z [čtyři typy definované v rámci rozhraní autorizace OAuth2 udělení] [ OAuth2-AuthZ-Grant-Types] získání udělení, v závislosti na typu nebo požadavky na klienta: "udělení autorizačního kódu", "klienta přihlašovací údaje","implicitní grant"a"udělení přihlašovacího hesla vlastníka prostředku". Přihlašovací údaje, které vrátí klientovi je buď [přístupový token](#access-token), nebo [autorizační kód](#authorization-code) (vyměňují později pro přístupový token), v závislosti na typu udělení autorizace používá.

## <a name="authorization-server"></a>autorizační server
Podle definice [Framework autorizace OAuth2][OAuth2-Role-Def], server pověřená přístup tokeny k [klienta](#client-application) po úspěšném ověření [vlastníka prostředku](#resource-owner) a získání jeho povolení. A [klientská aplikace](#client-application) komunikuje pomocí autorizačního serveru za běhu pomocí jeho [autorizace](#authorization-endpoint) a [token](#token-endpoint) definovanékoncovébody,vsouladuOAuth2[udělení autorizace](#authorization-grant).

V případě integrace s aplikací Azure AD, Azure AD implementuje role serveru autorizace pro aplikace Azure AD a Microsoft service rozhraní API, například [rozhraní Microsoft Graph API][Microsoft-Graph].

## <a name="claim"></a>deklarace identity
A [token zabezpečení](#security-token) obsahuje deklarace identity, které poskytují kontrolní výrazy přibližně jednu entitu (například [klientská aplikace](#client-application) nebo [vlastníka prostředku](#resource-owner)) na jinou entitu (například [server prostředků](#resource-server)). Deklarace identity jsou páry název/hodnota, která propojení faktů o token subjektu (například objekt zabezpečení, která byla ověřena podle [autorizační server](#authorization-server)). K dispozici v daný token deklarace identity jsou závislé na několika proměnnými, typ tokenu, typ přihlašovacích údajů pro ověření, předmět, konfigurace aplikace, atd.

Zobrazit [odkaz tokenu Azure AD] [ AAD-Tokens-Claims] další podrobnosti.

## <a name="client-application"></a>Klientská aplikace
Podle definice [Framework autorizace OAuth2][OAuth2-Role-Def], aplikace, která vytvoří požadavky na chráněné prostředky jménem [vlastníka prostředku](#resource-owner). Termín "client" neznamená jakékoli vlastnosti implementace konkrétní hardware (například, jestli aplikace provádí na serveru, desktopové nebo jiná zařízení). 

Klientská aplikace požaduje [autorizace](#authorization) od vlastníka prostředku se účastnit [udělení autorizace OAuth2](#authorization-grant) tok a získávají přístup k rozhraní API/datům jménem vlastníka prostředku. Rozhraní autorizace OAuth2 [definuje dva typy klientů][OAuth2-Client-Types], "důvěrné informace" a "public", podle klienta schopnost zachovat utajení svých přihlašovacích údajů. Aplikace může implementovat [webový klient (důvěrné)](#web-client) na webovém serveru, která se spouští [nativního klienta (public)](#native-client) nainstalované v zařízení, nebo [na základě uživatelského agenta klienta (public)](#user-agent-based-client)která se spouští v prohlížeči zařízení.

## <a name="consent"></a>Vyjádření souhlasu
Proces [vlastníka prostředku](#resource-owner) udělení autorizace [klientská aplikace](#client-application), přístup k chráněným prostředkům v rámci konkrétní [oprávnění](#permissions), jménem vlastník prostředku. V závislosti na oprávněních požadovaným klientem správce nebo uživatele vyzve k souhlasu, pokud chcete povolit přístup ke svým datům organizace nebo jednotlivce v uvedeném pořadí. Mějte na paměti, v [víceklientské](#multi-tenant-application) scénář, aplikace [instanční objekt služby](#service-principal-object) je také zaznamenána v tenantovi consenting uživatele.

## <a name="id-token"></a>ID token
[OpenID Connect] [ OpenIDConnect-ID-Token] [token zabezpečení](#security-token) poskytované [autorizační server](#authorization-server) [koncový bod autorizace](#authorization-endpoint), který obsahuje [deklarace identity](#claim) vztahující se k ověřování koncového uživatele [vlastníka prostředku](#resource-owner). Stejně jako přístupový token, jsou také reprezentované tokeny typu ID jako digitálně podepsané [JSON Web Token (JWT)][JWT]. Na rozdíl od přístupového tokenu, ale ID token deklarace identity nejsou použity pro účely související s přístup k prostředkům a konkrétně řízení přístupu.

Zobrazit [odkaz tokenu Azure AD] [ AAD-Tokens-Claims] další podrobnosti.

## <a name="multi-tenant-application"></a>aplikace s více tenanty
Třídu aplikace, která umožňuje přihlášení a [souhlas](#consent) uživatelé zřízené v jakékoli službě Azure AD [tenanta](#tenant), včetně tenantů, než kde je klient zaregistrovaný. [Nativní klient systému](#native-client) aplikace jsou s více tenanty ve výchozím nastavení, vzhledem k tomu [webový klient](#web-client) a [webové rozhraní API/prostředků](#resource-server) aplikace se budou moct vybrat mezi jedním nebo více tenanty. Naopak webovou aplikaci registrovány jako jednoho tenanta, by povolit pouze přihlášení z uživatelské účty, zřízené ve stejném tenantovi jako ten, ve kterém aplikace bude zaregistrovaná.

Zobrazit [přihlášení jakéhokoli uživatele Azure AD pomocí vzoru aplikace s více tenanty] [ AAD-Multi-Tenant-Overview] další podrobnosti.

## <a name="native-client"></a>Nativní klient
Typ [klientská aplikace](#client-application) , která je nativně nainstalované v zařízení. Vzhledem k tomu, že veškerý kód provádí na zařízení, bude považován za "public" klienta se ukládání přihlašovacích údajů soukromě/důvěrně jeho lokalizovat. Zobrazit [OAuth2 klienta typů a profily] [ OAuth2-Client-Types] další podrobnosti.

## <a name="permissions"></a>oprávnění
A [klientská aplikace](#client-application) získá přístup k [server prostředků](#resource-server) deklarováním žádosti o oprávnění. K dispozici jsou dva typy:

* "Delegovaná" oprávnění, které určují [obor](#scopes) přistupovat pomocí delegované autorizace od přihlášeného [vlastníka prostředku](#resource-owner), jsou uvedeny na prostředek v době běhu jako ["spojovací bod služby" deklarace identity](#claim) v klientovi sady [přístupový token](#access-token).
* Oprávnění "Aplikace", které určují [na základě rolí](#roles) přistupovat pomocí přihlašovacích údajů/identitu aplikace klienta, se zobrazí a prostředků v době běhu jako [deklarace identity "role"](#claim) v klientovi přístupový token.

Jsou také surface během [souhlas](#consent) procesu, poskytuje správce nebo vlastníka prostředku příležitost k udělit nebo zamítnout klientský přístup k prostředkům v rámci jejich tenanta.

Žádosti o oprávnění jsou nakonfigurované na "Aplikace" / "Nastavení" kartu [webu Azure portal][AZURE-portal], v části "Požadováno oprávnění" tak, že vyberete požadovanou "Delegovaná oprávnění" a "aplikace Oprávnění"(druhá možnost vyžaduje členství v roli Globální správce). Protože [veřejným klientem](#client-application) nejde udržovat zabezpečené přihlašovací údaje, může požadovat pouze delegovaná oprávnění, při [důvěrnému klientovi](#client-application) má možnost požadovat delegovaná a aplikace oprávnění. Klienta [aplikační objekt](#application-object) ukládá deklarované oprávnění v jeho [requiredResourceAccess vlastnost][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>vlastník prostředku
Podle definice [Framework autorizace OAuth2][OAuth2-Role-Def], dokáže udělení přístupu k chráněnému prostředku entity. Pokud vlastník prostředku je osoba, to se označuje jako koncový uživatel. Například, když [klientská aplikace](#client-application) chce, aby se pro přístup k poštovní schránky uživatele prostřednictvím [Microsoft Graph API][Microsoft-Graph], vyžaduje oprávnění od vlastníka prostředku poštovní schránky.

## <a name="resource-server"></a>server prostředků
Podle definice [Framework autorizace OAuth2][OAuth2-Role-Def], server, že hostitelé chráněné zdroje, dokáže přijímat a reagovat na požadavky na chráněné prostředky podle [klienta aplikace](#client-application) , které obsahují [přístupový token](#access-token). Označované také jako chráněný prostředek serveru nebo aplikace prostředků.

Server prostředků zpřístupňuje rozhraní API a vynucuje přístup k jeho chráněným prostředkům prostřednictvím [obory](#scopes) a [role](#roles), pomocí rozhraní autorizace OAuth 2.0. Mezi příklady patří Azure AD Graph API, která poskytuje přístup k datům tenanta Azure AD a rozhraní API Office 365, které poskytují přístup k datům, jako jsou e-mailu a kalendáře. Obě tyto jsou také zpřístupněno [Microsoft Graph API][Microsoft-Graph]. 

Stejně jako klientská aplikace pokládáme stav konfigurace identity aplikace prostředků prostřednictvím [registrace](#application-registration) v tenantovi Azure AD, zadejte do něj aplikaci a instanční objekt. Některá poskytovaný společností Microsoft rozhraní API, jako je například Azure AD Graph API, jste se zaregistrovali před instančních objektů k dispozici ve všech tenantech během zřizování.

## <a name="roles"></a>role
Stejně jako [obory](#scopes), role poskytují způsob, jak [server prostředků](#resource-server) řízení přístupu na chráněné prostředky. Existují dva typy: "user" role implementuje řízení přístupu na základě rolí pro uživatele nebo skupiny, které vyžadují přístup k prostředku, zatímco roli "aplikace" implementuje stejné pro [klientské aplikace](#client-application) , které vyžadují přístup.

Role jsou definované prostředek řetězce (například "výdajů schvalovatel", "Jen pro čtení", "Directory.ReadWrite.All"), kterou spravuje v [webu Azure portal] [ AZURE-portal] prostřednictvím zdroje [aplikace manifest](#application-manifest)a uložených v prostředku [appRoles vlastnost][AAD-Graph-Sp-Entity]. Na webu Azure portal slouží také k přiřazení uživatelů k rolím "user" a nakonfigurovat klienta [oprávnění aplikace](#permissions) pro přístup k roli "aplikace".

Podrobnou diskuzi o aplikační role vystavena službou Azure AD Graph API, najdete v části [obory oprávnění rozhraní API Graphu][AAD-Graph-Perm-Scopes]. Implementace podrobný příklad najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal][AAD-RBAC].

## <a name="scopes"></a>Obory
Stejně jako [role](#roles), obory poskytují způsob, jak [server prostředků](#resource-server) řízení přístupu na chráněné prostředky. Obory se používají k implementaci [obor] [ OAuth2-Access-Token-Scopes] řízení přístupu, pro [klientská aplikace](#client-application) , která se předala Delegovaný přístup k prostředku uděluje její vlastník.

Obory jsou definován prostředek řetězce (například "Mail.Read", "Directory.ReadWrite.All"), spravované v [webu Azure portal] [ AZURE-portal] prostřednictvím zdroje [manifest aplikace](#application-manifest)a uložených v prostředku [oauth2Permissions vlastnost][AAD-Graph-Sp-Entity]. Na webu Azure portal se také používá ke konfiguraci klientské aplikace [delegovaná oprávnění](#permissions) pro přístup k oboru.

Osvědčené postupy zásady vytváření názvů, je použití formátu "resource.operation.constraint". Podrobnou diskuzi o obory, vystavené služby Azure AD Graph API, najdete v části [obory oprávnění rozhraní API Graphu][AAD-Graph-Perm-Scopes]. Pro obory, vystavené služby Office 365, najdete v článku [referenční dokumentace k rozhraní API Office 365 oprávněním][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpečení
Podepsaný dokument obsahující deklarace identity, například tokenu OAuth2 nebo kontrolního výrazu SAML 2.0. Pro OAuth2 [udělení autorizace](#authorization-grant), [přístupový token](#access-token) (OAuth2) a [ID Token](http://openid.net/specs/openid-connect-core-1_0.html#IDToken) jsou typy tokenů zabezpečení, které jsou implementovány jako [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>instanční objekt
Když jste register nebo aktualizovat aplikace v [webu Azure portal][AZURE-portal], portálu vytvoření/aktualizace obě [aplikační objekt](#application-object) a odpovídající instanční objekt pro tohoto tenanta. Objekt aplikace *definuje* konfigurace identity aplikace globálně (ve všech tenantech kde v přidružené aplikaci udělit přístup), a je šablona, ze kterého jeho odpovídající objekt služby objekty jsou *odvozené* používat místně v době běhu (v konkrétního tenanta).

Další informace najdete v tématu [aplikační a instanční objekty][AAD-App-SP-Objects].

## <a name="sign-in"></a>přihlášení
Proces [klientská aplikace](#client-application) zahajuje ověřování koncového uživatele a zaznamenávání týkající se stavu, za účelem získání [token zabezpečení](#security-token) a nastavování jejich oborů relace aplikace do daného stavu. Stav můžete zahrnout součásti, jako jsou informace o uživatelském profilu, a informace odvozené z tokenu deklarací identity.

Funkce přihlášení aplikace se obvykle používá k implementaci jednotného přihlašování (SSO). To může také předcházet párový příkaz "registrace" funkce jako vstupní bod pro koncového uživatele k získání přístupu k aplikaci (při prvním přihlášení). Registrace funkce se používá k shromáždění a uložení další stavu specifické pro uživatele a mohou vyžadovat [souhlasu uživatele](#consent).

## <a name="sign-out"></a>adresa URL
Přidružený proces unauthenticating koncového uživatele, odpojení stavu uživatele [klientská aplikace](#client-application) relace během [přihlášení](#sign-in)

## <a name="tenant"></a>tenant
Instance adresáře služby Azure AD se označuje jako klient služby Azure AD. Poskytuje několik funkcí, včetně:

* Služba registru pro integrované aplikace
* ověřování uživatelských účtů a registrované aplikace
* Koncové body REST, které jsou potřeba pro podporu různých protokolů, jako jsou třeba OAuth2 a SAML, včetně [koncový bod autorizace](#authorization-endpoint), [koncový bod tokenu](#token-endpoint) a "common" koncovým bodem používaným [ aplikace s více tenanty](#multi-tenant-application).

Klienty Azure AD se během registrace, poskytují funkce Správa identit a přístupu pro předplatné vytvořili/spojené s předplatným Azure a Office 365. Správci předplatného Azure můžete také vytvořit další klienty Azure AD prostřednictvím webu Azure portal. Zobrazit [získání tenanta služby Azure Active Directory] [ AAD-How-To-Tenant] podrobnosti o různých způsobech můžete získat přístup do tenanta. Zobrazit [předplatné Azure propojeno se službou Azure Active Directory] [ AAD-How-Subscriptions-Assoc] podrobné informace o vztahu mezi předplatnými a tenanta služby Azure AD.

## <a name="token-endpoint"></a>koncový bod tokenu
Jeden z koncových bodů implementované [autorizační server](#authorization-server) podpoře OAuth2 [udělení autorizace](#authorization-grant). V závislosti na oprávnění, můžete použít k získání [přístupový token](#access-token) (a související "obnovit" token) pro [klienta](#client-application), nebo [ID token](#ID-token) při použití s [OpenID Připojit] [ OpenIDConnect] protokolu.

## <a name="user-agent-based-client"></a>Na základě uživatelského agenta klienta
Typ [klientská aplikace](#client-application) , která stáhne kód z webového serveru a provede v rámci uživatelského agenta (například webový prohlížeč), jako je například jedné stránce aplikace (SPA). Vzhledem k tomu, že veškerý kód provádí na zařízení, bude považován za "public" klienta se ukládání přihlašovacích údajů soukromě/důvěrně jeho lokalizovat. Další informace najdete v tématu [OAuth2 klienta typů a profily][OAuth2-Client-Types].

## <a name="user-principal"></a>hlavní název uživatele
Podobně jako způsob, jakým instanční objekt se používá k reprezentaci instance aplikace, je objekt uživatele instančního objektu jiného typu objektu, zabezpečení, který reprezentuje uživatele. Azure AD Graph [entitu uživatele] [ AAD-Graph-User-Entity] definuje schéma pro objekt uživatele, včetně vlastnosti související s uživateli, jako je jméno a příjmení, hlavní název uživatele, členství v roli adresáře, atd. Tímto způsobem konfigurace identity uživatele pro službu Azure AD vytvořit hlavní název uživatele v době běhu. Hlavní název uživatele se používá k reprezentování ověřeného uživatele pro jednotné přihlašování, záznam [souhlas](#consent) delegování, provedení rozhodnutí o řízení přístupu, atd.

## <a name="web-client"></a>Webový klient
Typ [klientská aplikace](#client-application) , který spouští všechny kódu na webovém serveru a může fungovat jako "důvěrné informace" klienta bezpečně uložením pověření uživatele na serveru. Další informace najdete v tématu [OAuth2 klienta typů a profily][OAuth2-Client-Types].

## <a name="next-steps"></a>Další postup
[Příručce vývojáře v Azure AD] [ AAD-Dev-Guide] je cílovou stránkou pro všechny služby Azure AD související s vývojem témata, včetně přehledu toho [integraci aplikací] [ AAD-How-To-Integrate] a rozumět základům [ověřování Azure AD a scénáře podporované metody ověřování][AAD-Auth-Scenarios]. Můžete také najít ukázky a kurzy v tom, jak rychle vytvořit a spustit na [Githubu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Pomocí následujícího oddílu pro komentáře na svůj názor a pomozte Upřesnit a tvarování tohoto obsahu, včetně žádostí o nových definic nebo aktualizaci existující aplikace!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
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
