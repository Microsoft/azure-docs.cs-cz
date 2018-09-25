---
title: Porovnání koncového bodu Azure AD v2.0 s koncovým bodem v1.0 | Dokumentace Microsoftu
description: Znát rozdíly mezi koncového bodu Azure AD v2.0 a koncový bod verze 1.0
services: active-directory
documentationcenter: ''
author: andretms
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: andret
ms.reviewer: hirsin, celested
ms.custom: aaddev
ms.openlocfilehash: 02c7edc84d2ac3a91c33d8f266d022db5cd5cb40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948949"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-v10-endpoint"></a>Porovnání koncového bodu Azure AD v2.0 s koncovým bodem v1.0

Při vývoji nových aplikací, je důležité znát rozdíly mezi koncové body v1.0 a v2.0. V následující tabulce jsou hlavní rozdíly, jakož i některé stávající omezení pro koncový bod verze 2.0.

> [!NOTE]
> Ne všechny funkce a scénáře služby Azure AD podporuje koncový bod verze 2.0. Pokud chcete zjistit, pokud je vhodné použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](#limitations).

## <a name="who-can-sign-in"></a>Kdo se může přihlásit

![Kdo se můžete přihlásit pomocí koncové body v1.0 a v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* Koncový bod v1.0 umožňuje jenom pracovní a školní účty pro přihlášení k aplikaci (Azure AD)

* Koncový bod verze 2.0 umožňuje pracovní a školní účty z Azure Active Directory a osobní účty (MSA) (hotmail.com, outlook.com, msn.com) pro přihlášení.

* Koncové body v1.0 a v2.0 také přijímat přihlášení z *[uživatele typu Host](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* adresáře Azure AD pro aplikace, konfigurované jako *[jednoho tenanta](single-and-multi-tenant-apps.md)* nebo *víceklientské* aplikace, konfigurované tak, aby odkazoval na koncový bod specifickým pro tenanta (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Koncový bod verze 2.0 umožňuje psát aplikace, které přijímají přihlášení z osobní a pracovní a školní účty, získáte schopnost psát aplikace kompletně účet bez ohledu na. Například pokud vaše aplikace volá [Microsoft Graphu](https://graph.microsoft.io), některé další funkce a data bude možné pracovní účty, jako je například jejich Sharepointových webech nebo data adresáře. Ale pro mnoho akcí jako například [čtení pošty uživatele](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), stejný kód může přistupovat k e-mailu pro osobní a pracovní a školní účty.

Pro koncový bod verze 2.0 můžete použít jeden library (MSAL) k získání přístupu k spotřebitele, vzdělávací i světů enterprise.

 Koncový bod Azure AD v1.0 přijímá přihlášení z pracovním a školním účtům.

## <a name="incremental-and-dynamic-consent"></a>Přírůstkové a dynamické souhlas

Aplikace s využitím koncového bodu Azure AD verze 1.0 je potřeba zadat jejich požadovaná oprávnění OAuth 2.0 v předstihu, třeba:

![Oprávnění registrace uživatelského rozhraní](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Jsou oprávnění nastavená pro registraci aplikace přímo **statické**. Zatímco statické oprávnění aplikace definované na portálu Azure portal a udržovat kód nice a jednoduché, ji mohou představovat několik problémů pro vývojáře:

* Aplikace potřebujete znát všechna oprávnění, které někdy potřebovat při vytváření aplikace. Přidání oprávnění v čase bylo obtížné procesu.

* Aplikace potřeba znát všechny prostředky, ke kterým by nikdy přístup předem. Bylo to složité vytvářet aplikace, které by přístup k libovolného počtu prostředků.

* Aplikace si muset vyžádat všechna oprávnění, která byste zas někdy potřebovali po uživatel poprvé přihlásil. V některých případech to vedlo k dlouhý seznam oprávnění, která nedoporučuje koncovým uživatelům schválení aplikace přístup na počáteční přihlášení.

S koncovým bodem v2.0, můžete přeskočit na staticky definovaných oprávnění, které jsou definované na informace o registraci aplikace na webu Azure Portal a zadejte oprávnění požadavkům vašich aplikací **dynamicky** za běhu a během pravidelné používání vašeho aplikace bez ohledu na staticky definovaných oprávnění v informace o registraci aplikace.

Uděláte to tak, můžete určit obory, které vaše aplikace potřebuje v libovolném časovém okamžiku v čase vaší aplikace včetně nových oborů v `scope` parametr při žádání o přístupový token – aniž byste museli předem definovat je v informace o registraci aplikace .

Pokud uživatel ještě nevyjádřil souhlas nové obory přidáno k žádosti, jsou vyzváni k vyjádření souhlasu jenom pro nová oprávnění. Další informace si můžete přečíst [oprávnění, vyjádření souhlasu a obory](v2-permissions-and-consent.md).

Umožňuje dynamicky prostřednictvím aplikace a požádat o oprávnění `scope` parametr poskytuje vývojářům plnou kontrolu nad vaše uživatelské prostředí. Pokud chcete, můžete nastavit také zatížení vašeho svolení prostředí a požádat o všechna oprávnění v žádosti o jednu počáteční autorizace. Nebo pokud vaše aplikace vyžaduje velké množství oprávnění, můžete shromažďovat tato oprávnění od uživatele postupně, protože pokus o použití určitých funkcí vaší aplikace v čase.

Všimněte si, že souhlas správce, provádí jménem organizace stále používá statickou oprávnění zaregistrovaný pro aplikaci, proto se doporučuje nastavit tato oprávnění pro aplikace s využitím koncového bodu v2.0, pokud potřebujete správce udělit souhlas jménem celé organizace. Tím se snižuje cykly, vyžaduje správce organizace k nastavení aplikace

## <a name="scopes-not-resources"></a>Obory, ne prostředky

Pro aplikace s využitím koncového bodu v1.0, aplikace se může chovat jako **prostředků**, nebo příjemce tokenů. Prostředek můžete definovat celou řadou **obory** nebo **oAuth2Permissions** , které rozumí, umožňuje tak klientským aplikacím požádat o tokeny do tohoto prostředku pro určitou sadu oborů. Vezměte v úvahu Azure AD Graph API s ukázkovým prostředku:

* Identifikátor prostředku nebo `AppID URI`: `https://graph.windows.net/`

* Obory, nebo `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, a tak dále.

To vše platí pro koncový bod verze 2.0. Aplikace se může i nadále chovat jako prostředek, definice oborů a identifikovaného identifikátorem URI. Klientské aplikace můžou stále žádat o přístup k tyto obory. Však byla změněna tak, že klient požádá o oprávnění. Pro koncový bod verze 1.0, OAuth 2.0 autorizace může mít vypadal žádost do služby Azure AD:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

kde **prostředků** parametr uvedené, který prostředek se klientská aplikace požaduje autorizaci. Azure AD vypočítat oprávnění požadovaná aplikací na základě statické konfigurace na portálu Azure a vystavené tokeny odpovídajícím způsobem. Pro aplikace pomocí koncového bodu v2.0 stejné OAuth 2.0 autorizaci žádost vypadá jako:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

kde **oboru** parametr označuje, který prostředek a oprávnění aplikace požaduje autorizaci. Požadovaný prostředek se stále nachází v požadavku – jednoduše je zahrnuta v každé hodnoty parametru oboru. Pomocí parametru oboru tímto způsobem umožňuje koncovým bodem v2.0 aby víc odpovídala specifikaci OAuth 2.0 a lépe v souladu s běžné postupy. Také umožňuje aplikacím provádět [přírůstkové souhlasu](#incremental-and-dynamic-consent), který je popsán výše.

## <a name="well-known-scopes"></a>Známé obory

### <a name="offline-access"></a>Přístup v režimu offline

Aplikace s využitím koncového bodu v2.0 může vyžadovat použití nové dobře známé oprávnění pro aplikace – `offline_access` oboru. Všechny aplikace bude muset požádat o toto oprávnění, pokud potřebují přístup k prostředkům jménem uživatele po dobu delší dobu, i když uživatel není aktivně aplikaci používat. `offline_access` Obor bude zobrazovat uživatelům v dialogových oknech souhlasu jako **časově neomezený přístup k datům**, které uživatel musí vyjádřit souhlas s. Požaduje `offline_access` oprávnění vám umožní vaši aplikaci pro příjem z koncového bodu v2.0 refresh_tokens OAuth 2.0. Aktualizovat tokeny jsou dlouhodobé a může probíhat po delší dobu přístupu pro nové přístupových tokenů OAuth 2.0.

Pokud vaše aplikace nebude vyžadovat `offline_access` oboru, nezíská obnovovací tokeny. To znamená, že když uplatníte autorizační kód v tok autorizačního kódu OAuth 2.0, obdržíte jen zpět přístupového tokenu z `/token` koncového bodu. Tento přístupový token se jsou dál platné pro určitou krátkou dobu (obvykle jedna hodina), ale nakonec vyprší. V této bodu v čase, vaše aplikace bude muset přesměruje uživatele zpět `/authorize` koncový bod k načtení nové autorizační kód. Během toto přesměrování uživatel může nebo nemusí být nutné znovu zadat své přihlašovací údaje nebo znovu souhlas s oprávnění v závislosti na typu aplikace.

Další informace o OAuth 2.0, `refresh_tokens`, a `access_tokens`, podívejte se [referenci na protokol v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil a e-mailu

V minulosti by nejzákladnější tok OpenID Connect přihlašování s Azure AD poskytují velké množství informací o uživateli ve výsledné *id_token*. Deklarace identity v *id_token* může obsahovat uživatele název, upřednostňované uživatelské jméno, e-mailovou adresu, ID objektu a další.

Informace, které `openid` oboru nabízí vaše aplikace je přístup k s omezeným přístupem. `openid` Oboru pouze vám umožní vaši aplikaci pro uživatele a získejte identifikátor specifický pro aplikace pro uživatele. Pokud chcete získat osobních údajů o uživateli ve vaší aplikaci, vaše aplikace bude nutné požádat o další oprávnění od uživatele. Dva nové obory – `email` a `profile` obory – vám umožní získat další oprávnění.

`email` Oboru umožňuje vaší aplikaci přístup ke primární e-mailovou adresu uživatele prostřednictvím `email` deklarací identity v požadavku id_token. `profile` Oboru zajišťuje přístup k vaší aplikaci na jiné základní informace o uživateli – jejich název, upřednostňované uživatelské jméno, ID objektu a tak dále.

Díky tomu můžete kód vaší aplikace v podobě minimální zpřístupnění – požádáte uživatele, můžete pouze pro konkrétní sadu informací, že vaše aplikace vyžaduje, aby fungovala správně. Další informace o těchto oborů, naleznete v tématu [odkaz oboru v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Deklarace identity tokenu

Deklarace identity v tokeny vystavené službou koncovým bodem v2.0 nesmí být shodný s tokeny vystavené službou obecně k dispozici koncové body služby Azure AD. Migrace na novou službu aplikace by neměl Předpokládejme, že konkrétní deklarace identity budou existovat v id_tokens nebo access_tokens. Další podrobnosti o různých typů tokeny použité v koncovým bodem v2.0 jsou k dispozici v [přístupový token](access-tokens.md) odkaz a [ `id_token` odkaz](id-tokens.md)

## <a name="limitations"></a>Omezení

Existuje několik omezení je potřeba vědět při použití verze 2.0.

Při sestavování aplikací, které se integrují s platformou identity Microsoft, musíte se rozhodnout, jestli protokoly pro ověřování a koncový bod v2.0 vašim potřebám. Koncový bod verze 1.0 a platforma jsou stále plně podporovány a v některých ohledech se další bohaté funkce než verze 2.0. Nicméně v2.0 [představuje významné výhody](azure-ad-endpoint-comparison.md) pro vývojáře.

Tady je zjednodušené doporučení pro vývojáře v daném okamžiku:

* Pokud vaše aplikace musí podporovat osobní účty Microsoft, použití verze 2.0. Ale předtím, než provedete, ujistěte se, že rozumíte omezení popsané v tomto článku.

* Pokud vaše aplikace potřebuje pouze pro podporu pracovní a školní účty, nepoužívejte verze 2.0. Místo toho odkazovat [v1.0 průvodce](azure-ad-developers-guide.md).

Chcete-li odstranit omezení zde uvedeny, tak, aby vždy jen musíte použít koncový bod verze 2.0 bude vyvíjet koncový bod verze 2.0. Do té doby použijte tento článek k určení, zda je koncový bod verze 2.0 pro vás nejvhodnější. Budeme nadále aktualizovat tak, aby odrážela aktuální stav koncového bodu v2.0 tohoto článku. Vraťte se zpět na opětovné vyhodnocení požadavků pro funkce verze 2.0.

### <a name="restrictions-on-app-types"></a>Omezení typů aplikací

V současné době nepodporuje následující typy aplikací koncovým bodem v2.0. Popis typů podporovaných aplikací najdete v tématu [typy aplikací v v2.0](v2-app-types.md).

#### <a name="standalone-web-apis"></a>Samostatné webové rozhraní API

Můžete použít k koncovým bodem v2.0 [sestavení webového rozhraní API, která je zabezpečena pomocí OAuth 2.0](v2-app-types.md#web-apis). Ale tohoto webového rozhraní API může přijímat tokeny pouze z aplikace, která má stejné ID aplikace. Nelze přistupovat k webové rozhraní API z klienta, který má jiné ID aplikace. Klient nebude moci vyžádat si či získat oprávnění pro vaše webové rozhraní API.

Informace o tom sestavení webového rozhraní API, které přijímá tokeny z klienta, který má stejné ID aplikace, najdete v ukázkách webového rozhraní API koncový bod v2.0 v [v2.0 Začínáme](v2-overview.md#getting-started) části.

### <a name="restrictions-on-app-registrations"></a>Omezení registrace aplikací

V současné době pro každou aplikaci, kterou chcete integrovat s koncovým bodem v2.0, je nutné vytvořit registrace aplikace v novém [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Existující služby Azure AD, nebo nejsou kompatibilní s koncovým bodem v2.0 aplikace účtu Microsoft. Aplikace, které jsou registrovány žádné portálu než portál pro registraci aplikací nejsou kompatibilní s koncovým bodem v2.0.

Kromě toho registrace aplikací, které vytvoříte v [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) mají následující upozornění:

* Jsou povolené jenom dva tajné kódy aplikace za ID aplikace.

* Registrace aplikace s registrovaných uživatelů pomocí osobního účtu Microsoft můžete zobrazit a spravovat jenom jeden vývojářský účet. Není možné sdílet mezi více vývojářů. Pokud chcete sdílet vaše registrace aplikace mezi více vývojářů, můžete vytvořit aplikaci po přihlášení k portálu pro registraci pomocí účtu služby Azure AD.

* Existuje několik omezení se týkají formátu adresy URL pro přesměrování, který je povolen. Další informace o adresu URL pro přesměrování najdete v další části.

### <a name="restrictions-on-redirect-urls"></a>Omezení pro přesměrování adresy URL

Aplikace, které jsou registrovány v portálu pro registraci aplikace jsou omezeny na omezená sada hodnot adresy URL přesměrování. Přesměrování URL pro webové aplikace a služby musí začínat schématem `https`, a všechny hodnoty adresy URL pro přesměrování musejí sdílet jednu doménu DNS. Například nemůžete zaregistrovat webovou aplikaci, která má jednu z těchto adres URL pro přesměrování:

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

Registrační systém porovnává celý název DNS stávající adresy URL pro přesměrování na název DNS adresy URL pro přesměrování, který chcete přidat. Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:  

* Celý název DNS nové adresy URL pro přesměrování neodpovídá názvu DNS stávající adresy URL přesměrování.

* Celý název DNS nové adresy URL pro přesměrování není subdoménou stávající adresy URL přesměrování.

Například, pokud má tato adresa URL pro přesměrování aplikace:

`https://login.contoso.com`

Můžete ji přidat tímto způsobem:

`https://login.contoso.com/new`

V tomto případě se název DNS přesně shoduje. Nebo můžete provést toto:

`https://new.login.contoso.com`

V tomto případě odkazujete na subdoménu DNS login.contoso.com. Pokud chcete mít aplikaci, která má `login-east.contoso.com` a `login-west.contoso.com` jako adresy URL přesměrování, je nutné přidat tyto adresy URL přesměrování v tomto pořadí:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Druhé dvě můžete přidat, protože jsou subdoménami první adresy URL pro přesměrování, contoso.com. Toto omezení se odeberou v nadcházející verzi.

Všimněte si také, může mít pouze 20 adresy URL odpovědí pro určitou aplikaci.

Zjistěte, jak zaregistrovat aplikaci v portálu pro registraci aplikací, najdete v článku [postup registrace aplikace s koncovým bodem v2.0](quickstart-v2-register-an-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Omezení týkající se knihoven a sad SDK

Podpora knihovny pro koncový bod verze 2.0 je aktuálně omezená. Pokud chcete použít koncový bod v2.0 v produkční aplikace, máte tyto možnosti:

* Pokud vytváříte webovou aplikaci, můžete bez obav použít middlewaru Microsoft obecně dostupné na straně serveru k provedení ověřování přihlášení a token. Patří mezi ně middleware OWIN Open ID Connect pro ASP.NET a Node.js Passport modulu plug-in. Ukázky kódu, které používají Microsoft middleware, najdete v článku [v2.0 Začínáme](v2-overview.md#getting-started) oddílu.

* Pokud vytváříte desktopové nebo mobilní aplikaci, můžete použít jednu z verze preview knihovny Microsoft Authentication (MSAL). Tyto knihovny jsou ve verzi preview s podporou produkčního prostředí, takže je bezpečný pro použití v aplikacích v produkčním prostředí. Další informace o podmínkách verze preview a dostupné knihovny v [referenční dokumentace knihoven ověřování](reference-v2-libraries.md).

* Pro platformy není pokrytá knihoven Microsoftu můžete integrovat s koncovým bodem v2.0 pomocí přímo, odesílání a přijímání zprávy protokolu v kódu aplikace. Protokoly OpenID Connect a OAuth 2.0 [výslovně popsanými](active-directory-v2-protocols.md) můžete provádět tato integrační.

* Nakonec můžete použít open source knihovny otevřete ID Connect a OAuth pro integraci s koncovým bodem v2.0. Verze 2.0 protokol by měl být kompatibilní s mnoha protokol open source knihovny bez důležité změny. Dostupnost tyto druhy knihoven se liší podle jazyka a libovolné platformy. [Open ID Connect](http://openid.net/connect/) a [OAuth 2.0](http://oauth.net/2/) websites udržovat seznam oblíbených implementace. Další informace najdete v tématu [knihovny Azure Active Directory v2.0 a ověřování](reference-v2-libraries.md)a seznam open source klientské knihovny a ukázky, které byly testovány s koncovým bodem v2.0.

* Pro srovnání `.well-known` je koncový bod pro společný koncový bod v2.0 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` .  Nahraďte `common` za ID vašeho tenanta se získat konkrétní data do svého tenanta.  

### <a name="restrictions-on-protocols"></a>Omezení pro protokoly

Koncový bod v2.0 nepodporuje SAML nebo WS-Federation; podporuje pouze Open ID Connect a OAuth 2.0. Ne všechny funkce a možnosti protokoly OAuth byly zahrnuty do koncového bodu v2.0.

Následující protokol funkce a možnosti v současné době jsou *není k dispozici* koncový bod verze 2.0:

* V současné době `email` deklarace identity se vrátí pouze pokud je nakonfigurovaná volitelnou deklaraci a obor je rozsah = e-mailu byla zadaná v žádosti. Toto chování však změní jako koncový bod v2.0 se aktualizuje a další v souladu se standardy Open ID Connect a OAuth 2.0.

* Koncový bod verze 2.0 nepodporuje vydávající roli nebo skupinu deklarací identity v tokenech ID.

* [Udělení přihlašovacích údajů heslo vlastníka prostředku OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3) není podporované koncovým bodem v2.0.

Koncový bod v2.0 nepodporuje žádnou formu protokoly SAML nebo WS-Federation.

Chcete-li lépe pochopit tak rozsah protokolu funkcí podporovaných koncový bod verze 2.0, přečtěte si naše [referenční informace o protokolu OpenID Connect a OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Omezení SAML

Pokud jste použili Active Directory Authentication Library (ADAL) v aplikacích Windows, může být trvaly výhod integrovaného ověřování Windows, který používá kontrolní výraz udělení zabezpečení kontrolního výrazu SAML (Markup Language). S tomuto grantu federovaných uživatelů Azure AD můžete bezobslužně tenanti ověřují pomocí jejich místní instancí Active Directory bez zadávání přihlašovacích údajů. Udělení kontrolního výrazu SAML se v současné době nepodporuje na koncový bod verze 2.0.
