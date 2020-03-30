---
title: Proč aktualizovat platformu microsoft identit (v2.0) | Azure
description: Poznejte rozdíly mezi koncovým bodem platformy microsoftových identit (v2.0) a koncovým bodem Azure Active Directory (Azure AD) v1.0 a zjistěte výhody aktualizace na v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 67a54a2cd4fa071fd47bcebb9aa53fd11fefd61e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154912"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Proč aktualizovat na Microsoft Identity Platform (v2.0)?

Při vývoji nové aplikace je důležité znát rozdíly mezi koncovými body platformy microsoftu identit (v2.0) a Azure Active Directory (v1.0). Tento článek popisuje hlavní rozdíly mezi koncovými body a některá existující omezení pro platformu identit Microsoftu.

> [!NOTE]
> Koncový bod platformy identit Microsoftu nepodporuje všechny scénáře a funkce Azure AD. Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Kdo se může přihlásit

![Kdo se může přihlásit pomocí koncových bodů v1.0 a v2.0](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* Koncový bod v1.0 umožňuje pouze pracovní a školní účty pro přihlášení k vaší aplikaci (Azure AD)
* Koncový bod platformy identit Microsoftu umožňuje přihlašovat se pracovními a školními účty z Azure AD a osobních účtů Microsoft (MSA), jako jsou hotmail.com, outlook.com a msn.com.
* Oba koncové body také přijímají přihlášení *[uživatelů typu Host](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* adresáře Azure AD pro aplikace nakonfigurované jako *[jednoklientské](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* `https://login.microsoftonline.com/{TenantId_or_Name}`nebo pro *víceklientské* aplikace nakonfigurované tak, aby ukazovaly na koncový bod specifický pro klienta ( ).

Koncový bod platformy identit Microsoftu umožňuje psát aplikace, které přijímají přihlášení z osobních účtů Microsoft a pracovních a školních účtů. To vám dává možnost napsat aplikaci zcela účet-agnostik. Pokud například vaše aplikace volá [Microsoft Graph](https://graph.microsoft.io), budou pro pracovní účty k dispozici některé další funkce a data, například jejich sharepointové weby nebo data adresáře. Ale pro mnoho akcí, jako je [čtení pošty uživatele](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0), stejný kód může přistupovat k e-mailu pro osobní i pracovní a školní účty.

Pro koncový bod platformy identit microsoftu můžete pomocí knihovny Microsoft Authentication Library (MSAL) získat přístup ke spotřebitelským, vzdělávacím a podnikovým světům. Koncový bod Azure AD v1.0 přijímá přihlášení z pracovních a školních účtů.

## <a name="incremental-and-dynamic-consent"></a>Přírůstkový a dynamický souhlas

Aplikace používající koncový bod Azure AD v1.0 jsou nutné k zadání požadovaných oprávnění OAuth 2.0 předem, například:

![Příklad zobrazení uj.](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

Oprávnění nastavená přímo při registraci aplikace jsou **statická**. Zatímco statická oprávnění aplikace definované na webu Azure Portal udržují kód příjemný a jednoduchý, představuje některé možné problémy pro vývojáře:

* Aplikace musí požádat o všechna oprávnění, která by kdy potřebovala při prvním přihlášení uživatele. To může vést k dlouhému seznamu oprávnění, která odrazují koncové uživatele od schvalování přístupu k aplikaci při počátečním přihlášení.

* Aplikace potřebuje znát všechny prostředky, ke kterým by měl mít přístup předem. Bylo obtížné vytvořit aplikace, které by mohly přistupovat ke libovolnému počtu prostředků.

S koncovým bodem platformy identit Microsoftu můžete ignorovat statická oprávnění definovaná v informacích o registraci aplikace na webu Azure Portal a požádat o oprávnění postupně, což znamená požádat o minimální sadu oprávnění předem a v průběhu času se stále více zvětšuje, protože zákazník používá další funkce aplikace. Chcete-li tak učinit, můžete zadat obory, které vaše aplikace potřebuje `scope` kdykoli zahrnutím nových oborů do parametru při vyžádání přístupového tokenu – bez nutnosti jejich předběžné definice v informacích o registraci aplikace. Pokud uživatel ještě nesouhlasil s novými obory přidanými do žádosti, bude vyzván k souhlasu pouze s novými oprávněními. Další informace naleznete v [tématu oprávnění, souhlas a obory](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Povolení aplikace požadovat oprávnění dynamicky `scope` prostřednictvím parametru dává vývojářům plnou kontrolu nad prostředím vašeho uživatele. Můžete také předčítat prostředí se souhlasem a požádat o všechna oprávnění v jedné počáteční žádosti o autorizaci. Pokud vaše aplikace vyžaduje velký počet oprávnění, můžete tato oprávnění shromažďovat od uživatele postupně, když se v průběhu času snaží používat určité funkce aplikace.

Souhlas správce provedený jménem organizace stále vyžaduje statická oprávnění zaregistrovaná pro aplikaci, takže byste tato oprávnění měli nastavit na portálu pro registraci aplikací, pokud potřebujete, aby správce uděloval souhlas jménem celé organizace. Tím se sníží cykly vyžadované správcem organizace k nastavení aplikace.

## <a name="scopes-not-resources"></a>Obory, nikoli prostředky

U aplikací používajících koncový bod v1.0 se aplikace může chovat jako **prostředek**nebo jako příjemce tokenů. Prostředek můžete definovat počet **oborů** nebo **oAuth2Permissions,** které rozumí, umožňuje klientské aplikace požadovat tokeny z tohoto prostředku pro určitou sadu oborů. Jako příklad prostředku zvažte rozhraní MICROSOFT Graph API:

* Identifikátor prostředku `AppID URI`nebo :`https://graph.microsoft.com/`
* Obory nebo `oAuth2Permissions` `Directory.Read`: `Directory.Write`, , a tak dále.

To platí pro koncový bod platformy identity Microsoftu. Aplikace se stále může chovat jako prostředek, definovat obory a být identifikována identifikátorem URI. Klientské aplikace mohou stále požadovat přístup k těmto oborům. Způsob, jakým klient požaduje tato oprávnění, se však změnil.

Pro koncový bod v1.0 oauth 2.0 požadavek na autorizaci Azure AD může vypadat takto:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Zde parametr **prostředku** indikoval, který prostředek klientská aplikace požaduje autorizaci. Azure AD vypočítal oprávnění vyžadovaná aplikací na základě statické konfigurace na webu Azure Portal a podle toho vydal tokeny.

Pro aplikace, které používají koncový bod platformy identit y Microsoft, vypadá stejný požadavek autorizovat OAuth 2.0 takto:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Zde parametr **oboru** označuje, který prostředek a oprávnění aplikace požaduje autorizaci. Požadovaný prostředek je stále přítomen v požadavku - je zahrnuta v každé z hodnot parametru oboru. Použití parametru oboru tímto způsobem umožňuje koncový bod platformy identity společnosti Microsoft být více kompatibilní se specifikací OAuth 2.0 a lépe zarovná s běžnými oborovými postupy. Umožňuje také aplikacím provést [přírůstkový souhlas](#incremental-and-dynamic-consent) – pouze s žádostí o oprávnění, pokud je aplikace vyžaduje, na rozdíl od předem.

## <a name="well-known-scopes"></a>Známé obory

### <a name="offline-access"></a>Offline přístup

Aplikace používající koncový bod platformy identit microsoftu mohou vyžadovat použití nového známého oprávnění pro aplikace – `offline_access` obor. Všechny aplikace budou muset požádat o toto oprávnění, pokud potřebují přístup k prostředkům jménem uživatele po delší dobu, i když uživatel nemusí aplikaci aktivně používat. Obor `offline_access` se uživateli zobrazí v dialogových oknech souhlasu jako **Přístup k vašim datům kdykoli**, s čímž musí uživatel souhlasit. Vyžádání `offline_access` oprávnění umožní vaší webové aplikaci přijímat oAuth 2.0 refresh_tokens z koncového bodu platformy identit microsoftu. Obnovovací tokeny jsou dlouhodobé a lze je vyměnit za nové přístupové tokeny OAuth 2.0 pro delší období přístupu.

Pokud vaše aplikace nepožaduje `offline_access` obor, neobdrží obnovovací tokeny. To znamená, že když uplatníte autorizační kód v toku autorizačního kódu OAuth 2.0, obdržíte zpět pouze přístupový token z koncového `/token` bodu. Tento přístupový token zůstává platný po krátkou dobu (obvykle jednu hodinu), ale nakonec vyprší. V tomto okamžiku aplikace bude muset přesměrovat uživatele zpět `/authorize` do koncového bodu, aby načetla nový autorizační kód. Během tohoto přesměrování může nebo nemusí uživatel znovu zadávat své přihlašovací údaje nebo znovu souhlasit s oprávněními v závislosti na typu aplikace.

Další informace o oauth 2.0 , `refresh_tokens`a `access_tokens`, podívejte se na odkaz na [platformu identity společnosti Microsoft](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, profil a e-mail

Historicky nejzákladnější OpenID Connect sign-in tok s platformou identit Microsoft ubytováni mnoho informací o uživateli ve výsledné *id_token*. Deklarace identity v id_token mohou obsahovat jméno uživatele, upřednostňované uživatelské jméno, e-mailovou adresu, ID objektu a další.

Informace, ke `openid` kterým obor poskytuje přístup k aplikaci, jsou teď omezené. Obor `openid` umožní pouze aplikaci přihlásit uživatele a získat identifikátor specifický pro aplikaci pro uživatele. Pokud chcete získat osobní údaje o uživateli ve vaší aplikaci, vaše aplikace musí požádat o další oprávnění od uživatele. Dva nové `email` obory `profile`a , vám umožní požádat o další oprávnění.

* Obor `email` umožňuje aplikaci přístup k primární e-mailovou adresu uživatele prostřednictvím `email` deklarace v id_token, za předpokladu, že uživatel má adresovatelnou e-mailovou adresu.
* Obor `profile` poskytuje aplikaci přístup ke všem dalším základním informacím o uživateli, jako je jeho jméno, upřednostňované uživatelské jméno, ID objektu a tak dále, v id_token.

Tyto obory umožňují kódovat aplikaci způsobem s minimálním zveřejněním, takže můžete uživatele požádat pouze o sadu informací, které vaše aplikace potřebuje ke své práci. Další informace o těchto oborech naleznete [v odkazu na obor platformy identit společnosti Microsoft](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-claims"></a>Deklarace tokenů

Koncový bod platformy identity Microsoft vydává menší sadu deklarací ve svých tokenech ve výchozím nastavení zachovat datové části malé. Pokud máte aplikace a služby, které jsou závislé na konkrétní deklaraci v tokenu v1.0, který již není k dispozici ve výchozím nastavení v tokenu platformy identit microsoftu, zvažte použití volitelné funkce [deklarací identity](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) k zahrnutí této deklarace.

> [!IMPORTANT]
> tokeny v1.0 a v2.0 mohou být vydány koncovými body v1.0 a v2.0! id_tokens *vždy* odpovídat koncovému bodu, od kterého jsou požadovány, a přístupové tokeny *vždy* odpovídají formátu očekávanému webovým rozhraním API, které bude klient volat pomocí tohoto tokenu.  Takže pokud vaše aplikace používá koncový bod v2.0 získat token pro volání Microsoft Graph, který očekává v1.0 formát přístupové tokeny, vaše aplikace obdrží token ve formátu v1.0.  

## <a name="limitations"></a>Omezení

Existuje několik omezení, které je třeba znát při používání platformy identit microsoftu.

Při vytváření aplikací, které se integrují s platformou identit microsoftu, se musíte rozhodnout, zda koncový bod platformy identit microsoftu a ověřovací protokoly vyhovují vašim potřebám. Koncový bod a platforma v1.0 je stále plně podporována a v některých ohledech je více bohatá na funkce než platforma identit y Microsoft. Platforma identit Microsoftu však [zavádí významné výhody](azure-ad-endpoint-comparison.md) pro vývojáře.

Zde je zjednodušené doporučení pro vývojáře nyní:

* Pokud chcete nebo potřebujete podporovat osobní účty Microsoft ve vaší aplikaci nebo píšete novou aplikaci, použijte platformu identit Microsoftu. Ale dříve, než tak učiníte, ujistěte se, že rozumíte omezení matná v tomto článku.
* Pokud migrujete nebo aktualizujete aplikaci, která je založena na SAML, nemůžete používat platformu identit Microsoftu. Místo toho naleznete [v průvodci Azure AD v1.0](v1-overview.md).

Koncový bod platformy identit microsoftu se bude vyvíjet tak, aby eliminoval zde uvedená omezení, takže budete potřebovat pouze koncový bod platformy identit y Microsoft. Do té doby pomocí tohoto článku k určení, zda koncový bod platformy identit y Microsoft je pro vás to pravé. Budeme pokračovat v aktualizaci tohoto článku tak, aby odrážel aktuální stav koncového bodu platformy identity Microsoftu. Zkontrolujte, zda znovu přehodnotit své požadavky na platformu identit Microsoft u možností.

### <a name="restrictions-on-app-registrations"></a>Omezení registrace aplikací

Pro každou aplikaci, kterou chcete integrovat s koncovým bodem platformy identit Microsoftu, můžete vytvořit registraci aplikace v novém prostředí [ **registrace aplikací** ](https://aka.ms/appregistrations) na webu Azure Portal. Stávající aplikace účtu Microsoft nejsou kompatibilní s portálem, ale všechny aplikace Azure AD jsou, bez ohledu na to, kde a kdy byly zaregistrovány.

Registrace aplikací, které podporují pracovní a školní účty a osobní účty, mají následující upozornění:

* Na ID aplikace jsou povoleny pouze dva tajné klíče aplikace.
* Aplikaci, která nebyla zaregistrována v tenantovi, může spravovat pouze účet, který ji zaregistroval. Nelze jej sdílet s jinými vývojáři. To je případ většiny aplikací, které byly zaregistrovány pomocí osobního účtu Microsoft na portálu pro registraci aplikací. Pokud chcete sdílet registraci aplikace s více vývojáři, zaregistrujte aplikaci v tenantovi pomocí nové části **registrace aplikací** na webu Azure Portal.
* Existuje několik omezení ve formátu adresy URL přesměrování, která je povolena. Další informace o adrese URL přesměrování naleznete v další části.

### <a name="restrictions-on-redirect-urls"></a>Omezení adres URL přesměrování

Aplikace registrované pro platformu identit Microsoftu jsou omezeny na omezenou sadu hodnot adres URL přesměrování. Adresa URL přesměrování webových aplikací a služeb `https`musí začínat schématem a všechny hodnoty adresy URL přesměrování musí sdílet jednu doménu DNS.  Registrační systém porovná celý název DNS existující adresy URL přesměrování s názvem DNS přidávané adresy URL přesměrování. `http://localhost`je také podporována jako adresa URL přesměrování.  

Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:  

* Celý název DNS nové adresy URL přesměrování neodpovídá názvu DNS existující adresy URL přesměrování.
* Celý název DNS nové adresy URL přesměrování není subdoménou existující adresy URL přesměrování.

#### <a name="example-1"></a>Příklad 1

Pokud má aplikace adresu URL `https://login.contoso.com`přesměrování aplikace , můžete přidat adresu URL přesměrování, kde se název DNS přesně shoduje, jak je znázorněno v následujícím příkladu:

`https://login.contoso.com/new`

Nebo můžete odkazovat na subdoménu DNS login.contoso.com, jak je znázorněno v následujícím příkladu:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Příklad 2

Pokud chcete mít aplikaci, `login-east.contoso.com` `login-west.contoso.com` která má a jako přesměrování adresy URL, musíte přidat tyto adresy URL přesměrování v následujícím pořadí:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Můžete přidat poslední dvě, protože se contoso.com jsou subdoménami první adresy URL přesměrování.

Pro konkrétní aplikaci můžete mít pouze 20 adres URL odpovědí – toto omezení platí pro všechny typy aplikací, které registrace podporuje (jednostránková aplikace (SPA), nativní klient, webová aplikace a služba).  

Informace o tom, jak zaregistrovat aplikaci pro použití s platformou microsoftu pro identity, najdete v [tématu Registrace aplikace pomocí nového prostředí pro registrace aplikací](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="restrictions-on-libraries-and-sdks"></a>Omezení knihoven a sad SDK

V současné době je omezená podpora knihovny pro koncový bod platformy identit microsoftu. Pokud chcete použít koncový bod platformy identit microsoftu v produkční aplikaci, máte tyto možnosti:

* Pokud vytváříte webovou aplikaci, můžete bezpečně použít obecně dostupný middleware na straně serveru k přihlášení a ověření tokenu. Patří mezi ně middleware OWIN OpenID Connect pro ASP.NET a modul plug-in Node.js Passport. Ukázky kódu, které používají middleware microsoftu, najdete v části [Začínáme na platformě identit microsoftu.](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started)
* Pokud vytváříte desktopovou nebo mobilní aplikaci, můžete použít některou z knihoven ověřování Microsoft (MSAL). Tyto knihovny jsou obecně dostupné nebo v produkčníverzi Preview, takže je bezpečné je používat v produkčních aplikacích. Další informace o podmínkách náhledu a dostupných knihovnách naleznete v [knihovnách ověřování](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Pro platformy, které nejsou zahrnuty v knihovnách Microsoft, můžete integrovat s koncovým bodem platformy identit Microsoft přímým odesíláním a přijímáním zpráv protokolu v kódu aplikace. Protokoly OpenID Connect a OAuth [jsou explicitně zdokumentovány,](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aby vám pomohly s takovou integrací.
* Nakonec můžete použít open-source OpenID Connect a OAuth knihovny pro integraci s koncovým bodem platformy identit microsoftu. Koncový bod platformy identit microsoftu by měl být kompatibilní s mnoha knihovnami protokolů s otevřeným zdrojovým kódem beze změn. Dostupnost těchto druhů knihoven se liší podle jazyka a platformy. Webové stránky [OpenID Connect](https://openid.net/connect/) a [OAuth 2.0](https://oauth.net/2/) udržují seznam oblíbených implementací. Další informace naleznete [v tématu Microsoft identity platformy a ověřování knihovny](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)a seznam knihoven s otevřeným zdrojovým kódem klienta a ukázky, které byly testovány s koncovým bodem platformy identit microsoftu.
* Pro referenci `.well-known` je koncový bod pro platformu `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`identit microsoftu common endpoint . Nahraďte `common` id svého klienta, abyste získali data specifická pro vašeho tenanta.  

### <a name="protocol-changes"></a>Změny protokolu

Koncový bod platformy identit y Microsoft nepodporuje SAML nebo WS-Federation; podporuje pouze OpenID Connect a OAuth 2.0.  Významné změny protokolů OAuth 2.0 z koncového bodu v1.0 jsou: 

* Deklarace je vrácena, `email` pokud je nakonfigurována volitelná deklarace **nebo** byl v požadavku zadán e-mail scope=email. 
* Parametr `scope` je nyní podporován místo `resource` parametru.  
* Mnoho odpovědí byly upraveny tak, aby byly více kompatibilní se specifikací OAuth `expires_in` 2.0, například správně vrací jako int namísto řetězce.  

Chcete-li lépe porozumět rozsahu funkcí protokolu podporovaných v koncovém bodě platformy identit y společnosti Microsoft, přečtěte si [informace o odkazech na protokol OpenID Connect a OAuth 2.0](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="saml-restrictions"></a>Omezení SAML

Pokud jste v aplikacích systému Windows použili knihovnu ADAL (Active Directory Authentication Library), je možné, že jste využili výhod integrovaného ověřování systému Windows, které používá udělení kontrolního výrazu jazyka rozpoznávání skontrolním výrazem (Security Assertion Markup Language). S tímto grantem mohou uživatelé federovaných klientů Azure AD bezobslužně ověřit pomocí své místní instance služby Active Directory bez zadání přihlašovacích údajů. Udělení kontrolního výrazu SAML není podporováno v koncovém bodě platformy identity Microsoftu.
