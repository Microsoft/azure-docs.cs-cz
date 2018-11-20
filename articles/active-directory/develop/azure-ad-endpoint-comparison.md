---
title: Porovnání koncového bodu Azure AD v2.0 s koncovým bodem v1.0 | Dokumentace Microsoftu
description: Znát rozdíly mezi koncového bodu Azure AD v2.0 a koncový bod verze 1.0
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2018
ms.author: celested
ms.reviewer: hirsin, andret, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.openlocfilehash: 3e9765bf2c6b746b892f7fbc97ea3124f80d772e
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976006"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Porovnání koncového bodu Azure AD v2.0 s koncovým bodem v1.0

Při vývoji nových aplikací, je důležité znát rozdíly mezi koncovými body pro v1.0 a v2.0 Azure Active Directory (Azure AD). Tento článek popisuje hlavní rozdíly mezi koncové body a některé stávající omezení pro koncový bod verze 2.0.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny funkce a scénáře služby Azure AD. Pokud chcete zjistit, pokud je vhodné použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](#limitations).

## <a name="who-can-sign-in"></a>Kdo se může přihlásit

![Kdo se můžete přihlásit pomocí koncové body v1.0 a v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* Koncový bod v1.0 umožňuje jenom pracovní a školní účty pro přihlášení k aplikaci (Azure AD)
* Koncový bod verze 2.0 umožňuje pracovní a školní účty z Azure AD a osobní účty Microsoft (MSA), jako je například hotmail.com, outlook.com a msn.com, pro přihlášení.
* Koncové body v1.0 a v2.0 také přijímat přihlášení z *[uživatele typu Host](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* adresáře Azure AD pro aplikace, konfigurované jako *[jednoho tenanta](single-and-multi-tenant-apps.md)* nebo *víceklientské* aplikace, konfigurované tak, aby odkazoval na koncový bod specifickým pro tenanta (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Koncový bod verze 2.0 umožňuje psát aplikace, které přijímají přihlášení z osobních účtů Microsoft a pracovním a školním účtům. To vám umožňuje napsat aplikace kompletně účet bez ohledu na. Například, pokud vaše aplikace volá [Microsoft Graphu](https://graph.microsoft.io), některé další funkce a data bude možné pracovní účty, jako je například jejich Sharepointových webech nebo data adresáře. Ale pro mnoho akcí jako například [čtení pošty uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), stejný kód může přistupovat k e-mailu pro osobní a pracovní a školní účty.

Pro koncový bod verze 2.0 můžete použít Microsoft Authentication Library (MSAL) k získání přístupu pro příjemce, vzdělávací sféře a světů enterprise. Koncový bod Azure AD v1.0 přijímá přihlášení z pracovním a školním účtům.

## <a name="incremental-and-dynamic-consent"></a>Přírůstkové a dynamické souhlas

Aplikace s využitím koncového bodu Azure AD verze 1.0 je potřeba zadat jejich požadovaná oprávnění OAuth 2.0 v předstihu, třeba:

![Oprávnění registrace uživatelského rozhraní](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Jsou oprávnění nastavená pro registraci aplikace přímo **statické**. Zatímco statické oprávnění aplikace, které jsou definované na portálu Azure portal byl kód zachován, nice a jednoduché, představuje některé informace o možných problémech pro vývojáře:

* Aplikace musí požádat o všechna oprávnění, která byste zas někdy potřebovali po uživatel poprvé přihlásil. To může vést k dlouhý seznam oprávnění, která zabráníte koncovým uživatelům ve schvalování přístupu aplikace na počáteční přihlášení vyplňujícím.

* Aplikace je potřeba vědět, všechny prostředky, ke kterým by nikdy přístup předem. Bylo to složité vytvářet aplikace, které by přístup k libovolného počtu prostředků.

S koncovým bodem v2.0 můžete ignorovat statické oprávnění definované na informace o registraci aplikace v Azure portal a žádost o oprávnění postupně místo, což znamená, že s žádostí o úplné minimální sadu oprávnění předem a účtují více v čase jako zákazník používá další funkce. Uděláte to tak, můžete určit obory, které vaše aplikace potřebuje kdykoli včetně nových oborů v `scope` parametr při žádání o přístupový token – aniž byste museli předem definovat je v informace o registraci aplikace. Pokud uživatel ještě nevyjádřil souhlas nové obory přidáno k žádosti, zobrazí se výzva k vyjádření souhlasu jenom pro nová oprávnění. Další informace najdete v tématu [oprávnění, vyjádření souhlasu a obory](v2-permissions-and-consent.md).

Umožňuje dynamicky prostřednictvím aplikace a požádat o oprávnění `scope` parametr poskytuje vývojářům plnou kontrolu nad vaše uživatelské prostředí. Můžete také přední zatížení vašeho svolení prostředí a požádat o všechna oprávnění v jedné žádosti počáteční autorizace. Pokud vaše aplikace vyžaduje velké množství oprávnění, můžete získat těmito oprávněními, od uživatele postupně se pokusí o použití určitých funkcí aplikace v čase.

Souhlas správce, provádí jménem organizace stále vyžaduje statické oprávnění zaregistrovaný pro aplikace, takže pokud potřebujete správce udělit souhlas jménem celé organizace, byste měli nastavit tato oprávnění pro aplikace v portálu pro registraci aplikace. To snižuje cykly, vyžaduje správce organizace k nastavení aplikace.

## <a name="scopes-not-resources"></a>Obory, ne prostředky

Pro aplikace s využitím koncového bodu v1.0, aplikace se může chovat jako **prostředků**, nebo příjemce tokenů. Prostředek můžete definovat celou řadou **obory** nebo **oAuth2Permissions** , které rozumí, umožňuje tak klientským aplikacím požádat o tokeny od tohoto prostředku pro určitou sadu oborů. Vezměte v úvahu Azure AD Graph API s ukázkovým prostředku:

* Identifikátor prostředku nebo `AppID URI`: `https://graph.windows.net/`
* Obory, nebo `oAuth2Permissions`: `Directory.Read`, `Directory.Write`, a tak dále.

Totéž platí pro koncový bod verze 2.0. Aplikace se může i nadále chovat jako prostředek, definice oborů a identifikovaného identifikátorem URI. Klientské aplikace můžou stále žádat o přístup k tyto obory. Však byla změněna tak, že klient požádá o oprávnění. 

Pro koncový bod verze 1.0, OAuth 2.0 autorizace může mít vypadal žádost do služby Azure AD:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Tady **prostředků** parametr uvedené který prostředek se klientská aplikace požaduje autorizaci. Azure AD vypočítat oprávnění požadovaná aplikací na základě statické konfigurace na portálu Azure a vystavené tokeny odpovídajícím způsobem. 

Pro aplikace pomocí koncového bodu v2.0 stejné OAuth 2.0 autorizaci žádost vypadá jako:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Tady **oboru** parametr určuje, které prostředků a oprávnění aplikace požaduje autorizaci. Požadovaný prostředek se stále nachází v žádosti – je zahrnuta v každé hodnoty parametru oboru. Pomocí parametru oboru tímto způsobem umožňuje koncovým bodem v2.0 aby víc odpovídala specifikaci OAuth 2.0 a lépe v souladu s běžné postupy. Také umožňuje aplikacím provádět [přírůstkové souhlasu](#incremental-and-dynamic-consent) – pouze pro požadování oprávnění, když aplikace vyžaduje nikoli ještě před zahájením.

## <a name="well-known-scopes"></a>Známé obory

### <a name="offline-access"></a>Přístup v režimu offline

Aplikace s využitím koncového bodu v2.0 může vyžadovat použití nové dobře známé oprávnění pro aplikace – `offline_access` oboru. Všechny aplikace bude muset požádat o toto oprávnění, pokud potřebují přístup k prostředkům jménem uživatele po dobu delší dobu, i když uživatel není aktivně aplikaci používat. `offline_access` Obor bude zobrazovat uživatelům v dialogových oknech souhlasu jako **časově neomezený přístup k datům**, které uživatel musí vyjádřit souhlas s. Požaduje `offline_access` oprávnění vám umožní vaši aplikaci pro příjem z koncového bodu v2.0 refresh_tokens OAuth 2.0. Aktualizovat tokeny jsou dlouhodobé a může probíhat po delší dobu přístupu pro nové přístupových tokenů OAuth 2.0.

Pokud vaše aplikace nebude požadavku `offline_access` oboru, nezíská obnovovací tokeny. To znamená, že když uplatníte autorizační kód v tok autorizačního kódu OAuth 2.0, pouze dostanete zpět přístupového tokenu z `/token` koncového bodu. Který přístup k tokenu zůstává platná pro určitou krátkou dobu (obvykle jedna hodina), ale nakonec vyprší. V této bodu v čase, vaše aplikace bude muset přesměruje uživatele zpět `/authorize` koncový bod k načtení nové autorizační kód. Během toto přesměrování uživatel může nebo nemusí být nutné znovu zadat své přihlašovací údaje nebo reconsent oprávnění, v závislosti na typu aplikace.

Další informace o OAuth 2.0, `refresh_tokens`, a `access_tokens`, podívejte se [referenci na protokol v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil a e-mailu

V minulosti by nejzákladnější tok OpenID Connect přihlašování s Azure AD poskytují velké množství informací o uživateli ve výsledné *id_token*. Deklarace identity v tokentu id_token můžou obsahovat uživatele název upřednostňované uživatelské jméno, e-mailovou adresu, ID objektu a další.

Informace, které `openid` oboru nabízí vaše aplikace je přístup k s omezeným přístupem. `openid` Oboru pouze vám umožní vaši aplikaci pro uživatele a získejte identifikátor specifický pro aplikace pro uživatele. Pokud chcete zjistit osobní údaje o uživateli ve vaší aplikaci, vaše aplikace potřebuje požádat o další oprávnění od uživatele. Dva nové obory `email` a `profile`, vám umožní získat další oprávnění.

* `email` Oboru umožňuje vaší aplikaci přístup ke primární e-mailovou adresu uživatele prostřednictvím `email` deklarací identity v požadavku id_token, za předpokladu, že uživatel nemá adresovatelný e-mailovou adresu. 
* `profile` Oboru umožňuje vaší aplikaci přístup do všech ostatních základní informace o uživateli, jako jsou jména upřednostňované uživatelské jméno, ID objektu, a tak dále v požadavku id_token.

Tyto obory umožňují kódu vaší aplikace v podobě minimální zpřístupnění tak požádáte uživatele, můžete pouze pro konkrétní sadu informací, které vaše aplikace potřebuje pro výkon své práce. Další informace o těchto oborů, naleznete v tématu [odkaz oboru v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Token deklarací identity

Koncový bod v2.0 vydá menší sadu deklarací identity v jeho tokeny ve výchozím nastavení Udržovat malé datové části. Pokud máte aplikace a služby, které jsou závislé na konkrétní deklarace identity v tokenu v1.0, která je již k dispozici ve výchozím nastavení v tokenu v2.0, zvažte použití [nepovinných deklarací identity](active-directory-optional-claims.md) funkce zahrnout tuto žádost.

## <a name="limitations"></a>Omezení

Existuje několik omezení je potřeba vědět při použití verze 2.0.

Při sestavování aplikací, které se integrují s platformou identity Microsoft, musíte se rozhodnout, jestli protokoly pro ověřování a koncový bod v2.0 vašim potřebám. Koncový bod verze 1.0 a platforma jsou stále plně podporovány a v některých ohledech se další bohaté funkce než verze 2.0. Nicméně v2.0 [představuje významné výhody](azure-ad-endpoint-comparison.md) pro vývojáře.

Tady je zjednodušené doporučení pro vývojáře v daném okamžiku:

* Pokud chcete nebo potřebujete podporu ve vaší aplikaci pro osobní účty Microsoft nebo psaní nové aplikace, použijte verze 2.0. Ale předtím, než provedete, ujistěte se, že se že můžete seznámit s omezeními popisovaných v tomto článku.
* Pokud se migrace nebo aktualizujete aplikaci, která se spoléhá na SAML, nemůžete použít verze 2.0. Místo toho odkazovat [v1.0 průvodce](v1-overview.md).

Chcete-li odstranit omezení zde uvedeny, tak, aby vždy jen musíte použít koncový bod verze 2.0 bude vyvíjet koncový bod verze 2.0. Do té doby použijte tento článek k určení, zda je koncový bod verze 2.0 pro vás nejvhodnější. Budeme aktualizovat tak, aby odrážela aktuální stav koncového bodu v2.0 tohoto článku. Vraťte se zpět na opětovné vyhodnocení požadavků pro funkce verze 2.0.

### <a name="restrictions-on-app-registrations"></a>Omezení registrace aplikací

Pro každou aplikaci, kterou chcete integrovat s koncovým bodem v2.0, můžete vytvořit registrace aplikace v [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Alternativně můžete zaregistrovat aplikaci s použitím [ **registrace aplikací (Preview)** prostředí](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) na webu Azure Portal. Existující aplikace účtu Microsoft nejsou kompatibilní s portálem ve verzi preview, ale jsou všechny aplikace AAD, bez ohledu na to, kde a kdy byly registrovány. 

Registrace aplikací, které podporují pracovní a školní účty a osobní účty mají následující upozornění:

* Jsou povolené jenom dva tajné kódy aplikace za ID aplikace.
* Registrace aplikace s registrovaných uživatelů pomocí osobního účtu Microsoft v portálu pro registraci aplikace můžete zobrazit a spravovat jenom jeden vývojářský účet. Není možné sdílet mezi více vývojářů. Pokud chcete sdílet vaše registrace aplikace s více vývojářů, můžete vytvořit aplikaci pomocí části registrace aplikací (Preview) na webu Azure portal.
* Existuje několik omezení se týkají formátu adresy URL pro přesměrování, který je povolen. Další informace o adresu URL pro přesměrování najdete v další části.

### <a name="restrictions-on-redirect-urls"></a>Omezení pro přesměrování adresy URL

Aplikace, které jsou registrovány pro verze 2.0 jsou omezeny na omezená sada hodnot adresy URL přesměrování. Přesměrování URL pro webové aplikace a služby musí začínat schématem `https`, a všechny hodnoty adresy URL pro přesměrování musejí sdílet jednu doménu DNS.  Registrační systém porovnává celý název DNS stávající adresy URL pro přesměrování na název DNS adresy URL pro přesměrování, který přidáváte. `http://localhost` je také podporováno jako adresu URL přesměrování.  

Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:  

* Celý název DNS nové adresy URL pro přesměrování neodpovídá názvu DNS stávající adresy URL přesměrování.
* Celý název DNS nové adresy URL pro přesměrování není subdoménou stávající adresy URL přesměrování.

#### <a name="example-1"></a>Příklad 1

Pokud má adresu URL přesměrování `https://login.contoso.com`, můžete přidat adresu URL přesměrování, kde název DNS přesně shoduje, jak je znázorněno v následujícím příkladu:

`https://login.contoso.com/new`

Nebo můžete odkazujete na subdoménu DNS Login.contoso.com, jak je znázorněno v následujícím příkladu:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Příklad 2

Pokud chcete mít aplikaci, která má `login-east.contoso.com` a `login-west.contoso.com` jako adresy URL přesměrování, je nutné přidat tyto adresy URL přesměrování v následujícím pořadí:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Druhé dvě můžete přidat, protože jsou subdoménami první adresy URL pro přesměrování, contoso.com. Toto omezení se odeberou v nadcházející verzi.

Všimněte si také, může mít pouze 20 adresy URL odpovědi pro konkrétní aplikaci – tento limit vztahuje na všechny typy aplikací, podporuje registraci (jednostránková aplikace, nativní klient systému, webové aplikace a služby).  

Zjistěte, jak zaregistrovat aplikaci pro použití s v2.0, najdete v těchto rychlých startů:

* [Registrace aplikace pomocí portálu pro registraci aplikace](quickstart-v2-register-an-app.md)
* [Registrace aplikace pomocí App prostředí registrace (Preview)](quickstart-register-app.md)

### <a name="restrictions-on-libraries-and-sdks"></a>Omezení týkající se knihoven a sad SDK

Podpora knihovny pro koncový bod verze 2.0 je aktuálně omezená. Pokud chcete použít koncový bod v2.0 v produkční aplikace, máte tyto možnosti:

* Pokud vytváříte webovou aplikaci, bezpečně můžete middleware obecně dostupné na straně serveru k provedení ověřování přihlášení a token. Patří mezi ně middleware OWIN OpenID Connect pro ASP.NET a Node.js Passport modulu plug-in. Ukázky kódu, které používají Microsoft middleware, najdete v článku [v2.0 Začínáme](v2-overview.md#getting-started) oddílu.
* Pokud vytváříte desktopové nebo mobilní aplikace, můžete použít jednu z verze preview knihovny Microsoft Authentication (MSAL). Tyto knihovny jsou ve verzi preview s podporou produkčního prostředí, takže je bezpečný pro použití v aplikacích v produkčním prostředí. Další informace o podmínkách verze preview a dostupné knihovny v [referenční dokumentace knihoven ověřování](reference-v2-libraries.md).
* Pro platformy není pokrytá knihoven Microsoftu můžete integrovat s koncovým bodem v2.0 pomocí přímo, odesílání a přijímání zprávy protokolu v kódu aplikace. Protokoly OpenID Connect a OAuth 2.0 [výslovně popsanými](active-directory-v2-protocols.md) můžete provádět tato integrační.
* Nakonec můžete použít open source knihovny OpenID Connect a OAuth pro integraci s koncovým bodem v2.0. Koncový bod v2.0 by měl být kompatibilní s mnoha protokol open source knihoven beze změn. Dostupnost tyto druhy knihoven se liší podle jazyka a libovolné platformy. [OpenID Connect](http://openid.net/connect/) a [OAuth 2.0](http://oauth.net/2/) websites udržovat seznam oblíbených implementace. Další informace najdete v tématu [knihovny Azure Active Directory v2.0 a ověřování](reference-v2-libraries.md)a seznam open source klientské knihovny a ukázky, které byly testovány s koncovým bodem v2.0.
* Pro srovnání `.well-known` je koncový bod pro společný koncový bod v2.0 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Nahraďte `common` za ID vašeho tenanta se získat konkrétní data do svého tenanta.  

### <a name="protocol-changes"></a>Protokol změn

Koncový bod v2.0 nepodporuje SAML nebo WS-Federation; podporuje pouze OpenID Connect a OAuth 2.0.  Významné změny protokoly OAuth 2.0 z koncového bodu v1.0 jsou: 

* `email` Deklarací identity je vrácena, pokud je nakonfigurovaná volitelnou deklaraci **nebo** oboru = e-mailu byla zadaná v žádosti. 
* `scope` Parametr se teď podporuje místo `resource` parametru.  
* Mnoho odpovědi byly upraveny tak, aby byly víc odpovídala specifikaci OAuth 2.0, například správně vrací `expires_in` jako celé číslo namísto řetězce.  

Chcete-li lépe pochopit tak rozsah protokolu funkcí podporovaných koncový bod verze 2.0, přečtěte si téma [referenční informace o protokolu OpenID Connect a OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Omezení SAML

Pokud jste použili Active Directory Authentication Library (ADAL) v aplikacích Windows, může být trvaly výhod integrovaného ověřování Windows, který používá kontrolní výraz udělení zabezpečení kontrolního výrazu SAML (Markup Language). S tomuto grantu federovaných uživatelů Azure AD můžete bezobslužně tenanti ověřují pomocí jejich místní instancí Active Directory bez zadávání přihlašovacích údajů. Udělení kontrolního výrazu SAML se nepodporuje na koncový bod verze 2.0.
