---
title: Proč aktualizovat platformu Microsoft Identity Platform (v 2.0) | Azure
description: Zjistěte rozdíly mezi koncovým bodem Microsoft Identity Platform (v 2.0) a koncovým bodem Azure Active Directory (Azure AD) v 1.0 a Naučte se výhodami aktualizace na verze 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.openlocfilehash: ed48625e4654ac6b8bdaeed713b476ab23d29f97
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697978"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Proč se má aktualizovat platforma Microsoft Identity Platform (v 2.0)?

Při vývoji nové aplikace je důležité znát rozdíly mezi koncovými body Microsoft Identity Platform (v 2.0) a Azure Active Directory (v 1.0). Tento článek popisuje hlavní rozdíly mezi koncovými body a některými stávajícími omezeními pro platformu Microsoft Identity Platform.

> [!NOTE]
> Koncový bod platformy Microsoft Identity Platform nepodporuje všechny scénáře a funkce služby Azure AD. Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](#limitations).

## <a name="who-can-sign-in"></a>Kdo se může přihlásit

![Kdo se může přihlásit pomocí koncových bodů verze 1.0 a v 2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* Koncový bod v 1.0 umožňuje přihlásit se k vaší aplikaci (Azure AD) pouze pracovní a školní účet.
* Koncový bod platformy Microsoft Identity umožňuje pracovní a školní účty z Azure AD a osobních účtů Microsoft (MSA), jako je hotmail.com, outlook.com a msn.com, pro přihlášení.
* Oba koncové body také přijímají přihlášení *[uživatelů typu Host](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* v adresáři Azure AD pro aplikace nakonfigurované jako *[jeden tenant](single-and-multi-tenant-apps.md)* nebo pro *víceklientské* aplikace nakonfigurované tak, aby odkazovaly na koncový bod pro konkrétního klienta (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Koncový bod platformy Microsoft Identity umožňuje psát aplikace, které přijímají přihlášení z osobních účtů Microsoft a pracovních a školních účtů. Díky tomu máte možnost napsat si aplikaci kompletně s účtem – nezávislá. Například pokud vaše aplikace volá [Microsoft Graph](https://graph.microsoft.io), budou k dispozici některé další funkce a data pro pracovní účty, jako jsou například weby služby SharePoint nebo data adresáře. Ale u mnoha akcí, jako je [čtení e-mailů uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), může stejný kód přistupovat k e-mailu pro osobní i pracovní a školní účty.

V případě koncového bodu Microsoft Identity Platform můžete k získání přístupu k podnikovému světůu pro spotřebitele, vzdělávání a Enterprise použít Microsoft Authentication Library (MSAL). Koncový bod Azure AD v 1.0 přijímá přihlášení jenom z pracovních a školních účtů.

## <a name="incremental-and-dynamic-consent"></a>Přírůstkové a dynamické vyjádření souhlasu

Aplikace používající koncový bod Azure AD v 1.0 musí předem určovat požadovaná oprávnění OAuth 2,0, například:

![Příklad znázorňující uživatelské rozhraní pro registraci oprávnění](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Oprávnění nastavená přímo na registraci aplikace jsou **statická**. I když statická oprávnění aplikace definovaná v Azure Portal udržují kód Skvělé a jednoduché, prezentuje některé možné problémy vývojářům:

* Aplikace musí požádat o všechna oprávnění, která by někdy potřebovala při prvním přihlášení uživatele. To může vést k dlouhému seznamu oprávnění, které nedoporučuje koncovým uživatelům schvalovat přístup k aplikaci při počátečním přihlášení.

* Aplikace potřebuje znát všechny prostředky, ke kterým by někdy měl přístup před časem. Bylo obtížné vytvořit aplikace, které by mohly získat přístup k libovolnému počtu prostředků.

Pomocí koncového bodu Microsoft Identity Platform můžete ignorovat statická oprávnění definovaná v informacích o registraci aplikace v Azure Portal a místo toho požádat o oprávnění, což znamená, že se požádá o minimální sadu oprávnění předem. a roste více času, protože zákazník používá další funkce aplikace. V takovém případě můžete určit rozsahy, které aplikace potřebuje, a to tak, že zahrnete nové obory v parametru `scope` při vyžádání přístupového tokenu – aniž byste je museli předem definovat v informacích o registraci aplikace. Pokud uživatel ještě nesouhlasí s novými obory přidanými do žádosti, zobrazí se jim výzva k vyjádření souhlasu pouze s novými oprávněními. Další informace najdete v tématu [oprávnění, souhlas a obory](v2-permissions-and-consent.md).

Umožnění aplikace dynamicky požádat o oprávnění prostřednictvím parametru `scope` poskytne vývojářům plnou kontrolu nad uživatelským prostředím. Můžete také předem načítat své vyjádření souhlasu a požádat o všechna oprávnění v jedné počáteční žádosti o autorizaci. Pokud vaše aplikace vyžaduje velký počet oprávnění, můžete tato oprávnění shromáždit od uživatele přírůstkově, protože se snaží v průběhu času používat určité funkce aplikace.

Souhlas správce, který se provádí jménem organizace, pořád vyžaduje statická oprávnění registrovaná pro aplikaci, takže byste měli nastavit tato oprávnění pro aplikace na portálu pro registraci aplikací, pokud potřebujete správce o udělení souhlasu jménem celé organizace. Tím se omezí cykly, které správce organizace potřebuje k nastavení aplikace.

## <a name="scopes-not-resources"></a>Obory, nikoli prostředky

U aplikací využívajících koncový bod verze 1.0 se aplikace může chovat jako **prostředek**nebo příjemce tokenů. Prostředek může definovat počet **oborů** nebo **oAuth2Permissions** , které rozumí, a umožnit tak klientským aplikacím žádat o tokeny z daného prostředku na určitou sadu oborů. Jako příklad prostředku zvažte Microsoft Graph rozhraní API:

* Identifikátor prostředku nebo `AppID URI`: `https://graph.microsoft.com/`
* Rozsahy nebo `oAuth2Permissions`: `Directory.Read`, `Directory.Write`a tak dále.

To platí pro koncový bod Microsoft Identity Platform. Aplikace se může stále chovat jako prostředek, definovat obory a identifikovat pomocí identifikátoru URI. Klientské aplikace si stále můžou vyžádat přístup k těmto oborům. Nicméně způsob, jakým klient požaduje tato oprávnění, se změnil.

V případě koncového bodu v 1.0 může být ve službě Azure AD vyhledána žádost OAuth 2,0 autorizovat jako:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Zde je uveden parametr **prostředku** , který označuje, který prostředek klientská aplikace požaduje autorizaci. Služba Azure AD vypočítala oprávnění požadovaná aplikací na základě statické konfigurace v Azure Portal a odpovídajícím způsobem vydané tokeny.

U aplikací, které používají koncový bod Microsoft Identity Platform, vypadá stejná žádost o ověření OAuth 2,0 jako:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Tady je parametr **Scope (obor** ) indikuje, který prostředek a oprávnění aplikace požaduje autorizaci. Požadovaný prostředek se stále nachází v žádosti – je obsažený v každé z hodnot parametru Scope. Použití parametru scope tímto způsobem umožňuje, aby byl koncový bod platformy Microsoft Identity lépe kompatibilní se specifikací OAuth 2,0 a přesněji zarovnává s běžnými průmyslovými postupy. Umožňuje také aplikacím provádět [přírůstkové souhlasu](#incremental-and-dynamic-consent) – požaduje oprávnění pouze v případě, že je aplikace požaduje, a to na rozdíl od začátku.

## <a name="well-known-scopes"></a>Známé obory

### <a name="offline-access"></a>Offline přístup

Aplikace, které používají koncový bod platformy Microsoft identity, můžou vyžadovat použití nového známého oprávnění pro aplikace – `offline_access` rozsahu. Všechny aplikace budou muset požádat o toto oprávnění, pokud potřebují přístup k prostředkům jménem uživatele za delší dobu, a to i v případě, že uživatel nemusí aplikaci aktivně používat. Obor `offline_access` se uživateli zobrazí v dialogových oknech pro vyjádření souhlasu s **přístupem k datům kdykoli**a uživatel musí souhlasit. Vyžádáním oprávnění `offline_access` umožníte, aby webová aplikace přijímala refresh_tokens OAuth 2,0 z koncového bodu Microsoft Identity Platform. Aktualizační tokeny jsou dlouhodobé a dají se vyměňovat za nové přístupové tokeny OAuth 2,0 pro rozšířená období přístupu.

Pokud vaše aplikace nepožaduje obor `offline_access`, neobdrží aktualizační tokeny. To znamená, že při uplatnění autorizačního kódu v toku autorizačního kódu OAuth 2,0 obdržíte jenom přístupový token z `/token`ho koncového bodu. Přístupový token zůstane po krátkou dobu platný (obvykle jednou za hodinu), ale nakonec vyprší jeho platnost. V tomto okamžiku bude vaše aplikace muset přesměrovat uživatele zpátky na `/authorize` koncový bod a načíst nový autorizační kód. Během tohoto přesměrování může uživatel nebo nemusí později zadat svoje přihlašovací údaje nebo znovu vyjádřit souhlas s oprávněními v závislosti na typu aplikace.

Další informace o OAuth 2,0, `refresh_tokens`a `access_tokens`, najdete v referenčních informacích k [protokolu platformy Microsoft Identity Platform](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil a e-mail

Z historického hlediska OpenID připojení s platformou Microsoft identity na úrovni Basic Connect poskytuje velké množství informací o uživateli ve výsledných *id_token*. Deklarace v id_token můžou zahrnovat jméno uživatele, upřednostňované uživatelské jméno, e-mailovou adresu, ID objektu a další.

Informace, na které `openid` rozsah poskytují přístup k aplikaci, jsou teď omezené. Obor `openid` umožní, aby se aplikace přihlásila uživateli a získala pro uživatele identifikátor specifický pro aplikaci. Pokud chcete získat osobní údaje o uživateli v aplikaci, aplikace musí požádat uživatele o další oprávnění. Dva nové obory, `email` a `profile`, vám umožní požádat o další oprávnění.

* Obor `email` umožňuje vaší aplikaci přístup k primární e-mailové adrese uživatele prostřednictvím deklarace `email` v id_token za předpokladu, že uživatel má adresovatelnou e-mailovou adresu.
* Obor `profile` poskytuje vaší aplikaci přístup ke všem dalším základním informacím o uživateli, jako je například jeho jméno, upřednostňované uživatelské jméno, ID objektu a tak dále, v id_token.

Tyto obory vám umožňují nakódovat aplikaci při minimálním zpřístupnění, abyste se mohli pouze uživatele zeptat na sadu informací, které vaše aplikace potřebuje ke své práci. Další informace o těchto oborech najdete v [referenčních informacích o oboru platformy Microsoft Identity](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Deklarace identity tokenu

U koncového bodu Microsoft Identity Platform se ve výchozím nastavení v tokenech vystaví menší sada deklarací identity, aby byly datové části malé. Pokud máte aplikace a služby, které mají závislost na konkrétní deklaraci v tokenu v 1.0, který už není ve výchozím nastavení součástí tokenu platformy Microsoft identity, zvažte použití volitelné funkce [deklarace](active-directory-optional-claims.md) identity, která tuto deklaraci zahrnuje.

> [!IMPORTANT]
> tokeny v 1.0 a v 2.0 mohou být vydávány koncovými body v 1.0 i v 2.0. id_tokens *vždy* odpovídají koncovému bodu, ze kterého jsou požadovány, a přístupové tokeny *vždy* odpovídají formátu očekávanému webovým rozhraním API, které bude klient volat pomocí tohoto tokenu.  Takže pokud vaše aplikace používá koncový bod v 2.0 k získání tokenu pro volání Microsoft Graph, což očekává v případě přístupových tokenů ve formátu, vaše aplikace dostane token ve formátu v 1.0.  

## <a name="limitations"></a>Omezení

Při používání platformy Microsoft Identity Platform je potřeba mít na paměti několik omezení.

Když vytváříte aplikace, které se integrují s platformou Microsoft identity, musíte se rozhodnout, jestli koncový bod a ověřovací protokoly od Microsoftu splňují vaše požadavky. Koncový bod verze 1.0 a platforma jsou pořád plně podporované a v některých ohledech je více funkcí bohatých než Microsoft Identity Platform. Platforma Microsoft Identity Platform však [přináší významné výhody](azure-ad-endpoint-comparison.md) pro vývojáře.

Tady je zjednodušené doporučení pro vývojáře:

* Pokud chcete nebo potřebujete podporovat osobní účty Microsoft ve vaší aplikaci nebo píšete novou aplikaci, použijte Microsoft Identity Platform. Než to uděláte, ujistěte se, že rozumíte omezením popsaným v tomto článku.
* Pokud migrujete nebo aktualizujete aplikaci, která spoléhá na SAML, nemůžete použít Microsoft Identity Platform. Místo toho si přečtěte téma [Průvodce Azure AD v 1.0](v1-overview.md).

Koncový bod platformy Microsoft identity se bude vyvíjet, aby se vyloučila níže uvedená omezení, takže budete muset použít koncový bod platformy Microsoft identity. Do té doby použijte tento článek k určení, jestli je koncový bod platformy Microsoft identity pro vás nejvhodnější. Tento článek budeme dál aktualizovat, aby odrážel aktuální stav koncového bodu Microsoft Identity Platform. Vraťte se zpátky a znovu vyhodnoťte vaše požadavky na možnosti platformy Microsoft identity.

### <a name="restrictions-on-app-registrations"></a>Omezení registrace aplikací

Pro každou aplikaci, kterou chcete integrovat s koncovým bodem Microsoft Identity Platform, můžete vytvořit registraci aplikace v novém [prostředí **Registrace aplikací** ](https://aka.ms/appregistrations) v Azure Portal. Stávající aplikace účet Microsoft nejsou kompatibilní s portálem, ale všechny aplikace Azure AD jsou bez ohledu na to, kde nebo kdy byly registrovány.

Registrace aplikací, které podporují pracovní a školní účty a osobní účty, mají následující omezení:

* Pro každé ID aplikace jsou povoleny pouze dva tajné kódy aplikace.
* Aplikaci, která není zaregistrovaná v tenantovi, se dá spravovat jenom pomocí účtu, který ho zaregistroval. Nedá se sdílet s ostatními vývojáři. Jedná se o případ většiny aplikací registrovaných pomocí osobního účet Microsoft na portálu pro registraci aplikací. Pokud chcete registraci vaší aplikace sdílet s více vývojáři, zaregistrujte aplikaci v tenantovi pomocí nové části **Registrace aplikací** Azure Portal.
* Je k dispozici několik omezení formátu adresy URL pro přesměrování, která je povolena. Další informace o adrese URL pro přesměrování najdete v další části.

### <a name="restrictions-on-redirect-urls"></a>Omezení adres URL pro přesměrování

Aplikace registrované pro platformu Microsoft identity jsou omezené na omezenou sadu hodnot adresy URL pro přesměrování. Adresa URL pro přesměrování webových aplikací a služeb musí začínat schématem `https`a všechny hodnoty adresy URL pro přesměrování musí sdílet jednu doménu DNS.  Registrační systém porovnává celý název DNS stávající adresy URL pro přesměrování na název DNS adresy URL pro přesměrování, kterou přidáváte. `http://localhost` se také podporuje jako adresa URL pro přesměrování.  

Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:  

* Celý název DNS nové adresy URL pro přesměrování se neshoduje s názvem DNS existující adresy URL pro přesměrování.
* Celý název DNS nové adresy URL pro přesměrování není subdoménou stávající adresy URL pro přesměrování.

#### <a name="example-1"></a>Příklad 1

Pokud má aplikace adresu URL pro přesměrování `https://login.contoso.com`, můžete přidat adresu URL pro přesměrování, kde se název DNS přesně shoduje, jak je znázorněno v následujícím příkladu:

`https://login.contoso.com/new`

Nebo můžete odkazovat na subdoménu DNS login.contoso.com, jak je znázorněno v následujícím příkladu:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Příklad 2

Pokud chcete mít aplikaci, která má `login-east.contoso.com` a `login-west.contoso.com` jako adresy URL pro přesměrování, musíte tyto adresy URL pro přesměrování přidat v tomto pořadí:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Můžete přidat druhé 2, protože se jedná o subdomény první adresy URL pro přesměrování, contoso.com.

Pro konkrétní aplikaci můžete mít jenom 20 adres URL odpovědí – toto omezení platí pro všechny typy aplikací, které registrace podporuje (jednostránkové aplikace (SPA), nativní klient, webovou aplikaci a službu).  

Informace o tom, jak zaregistrovat aplikaci pro použití s platformou Microsoft identity, najdete v tématu [Registrace aplikace s využitím nového prostředí registrace aplikací](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Omezení knihoven a sad SDK

V současné době je podpora knihoven u koncového bodu Microsoft Identity Platform omezená. Pokud chcete použít koncový bod Microsoft Identity Platform v produkční aplikaci, máte tyto možnosti:

* Pokud vytváříte webovou aplikaci, můžete bezpečně použít všeobecně dostupný middleware na straně serveru pro přihlášení a ověření tokenu. Patří sem OWIN OpenID Connect middleware pro ASP.NET a zásuvný modul Node. js Passport. Ukázky kódu, které používají middleware Microsoft, najdete v části [Začínáme s platformou Microsoft Identity](v2-overview.md#getting-started) .
* Pokud vytváříte desktopovou nebo mobilní aplikaci, můžete použít jednu z knihoven Microsoft Authentication Library (MSAL). Tyto knihovny jsou všeobecně dostupné nebo ve verzi Preview podporované v produkčním prostředí, takže je bezpečné je používat v produkčních aplikacích. Další informace o podmínek verze Preview a dostupných knihovnách najdete v referenčních informacích ke [knihovnám ověřování](reference-v2-libraries.md).
* Pro platformy, které nejsou pokryté knihovnami Microsoftu, můžete integrovat s koncovým bodem platformy Microsoft identity, a to přímým odesíláním a přijímáním zpráv protokolu v kódu aplikace. Protokoly OpenID Connect a OAuth [jsou explicitně zdokumentovány](active-directory-v2-protocols.md) , aby vám usnadnily takovou integraci.
* Nakonec můžete pomocí Open Source knihoven OpenID Connect a OAuth integrovat s koncovým bodem Microsoft Identity Platform. Koncový bod platformy Microsoft identity by měl být kompatibilní s mnoha Open-Source knihovnami protokolů beze změn. Dostupnost těchto typů knihoven se liší v závislosti na jazyku a platformě. Weby [OpenID Connect](https://openid.net/connect/) a [OAuth 2,0](https://oauth.net/2/) uchovávají seznam oblíbených implementací. Další informace najdete v tématech [Microsoft Identity Platform a Authentication](reference-v2-libraries.md)Librarys a seznam Open Source klientských knihoven a ukázek, které byly testovány pomocí koncového bodu Microsoft Identity Platform.
* Pro referenci se `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`koncový bod `.well-known` pro běžný koncový bod platformy Microsoft identity. Pokud chcete získat data specifická pro vašeho tenanta, nahraďte `common` vaším ID tenanta.  

### <a name="protocol-changes"></a>Změny protokolu

Koncový bod Microsoft Identity Platform nepodporuje SAML ani WS-Federation; podporuje jenom OpenID Connect a OAuth 2,0.  Významné změny v protokolech OAuth 2,0 z koncového bodu verze 1.0 jsou tyto: 

* Deklarace `email` se vrátí, pokud je nakonfigurovaná volitelná deklarace identity **nebo** v žádosti je zadaný obor = e-mail. 
* Parametr `scope` se teď podporuje místo parametru `resource`.  
* Mnoho odpovědí bylo upraveno, aby byly lépe kompatibilní se specifikací OAuth 2,0, například správné vrácení `expires_in` jako int namísto řetězce.  

Pro lepší pochopení rozsahu funkčnosti protokolu podporovaného koncovým bodem platformy Microsoft identity, přečtěte si [odkaz OpenID Connect and OAuth 2,0 Protocol reference](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Omezení SAML

Pokud jste v aplikacích pro Windows používali Active Directory Authentication Library (ADAL), možná jste využili výhod integrovaného ověřování Windows, které používá udělení kontrolního výrazu SAML (Security Assertion Markup Language). Díky tomuto udělení se uživatelé federovaných tenantů Azure AD můžou bez zadání přihlašovacích údajů bezodkladně ověřit s místní instancí Active Directory. Udělení kontrolního výrazu SAML není podporované na koncovém bodu Microsoft Identity Platform.
