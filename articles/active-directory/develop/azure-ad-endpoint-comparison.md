---
title: Co je nového ve koncového bodu Azure AD v2.0? | Dokumenty Microsoft
description: Porovnávání mezi původní služby Azure AD a koncových bodů verze 2.0.
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
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: elisol, jmprieur, hirsin
ms.custom: aaddev
ms.openlocfilehash: 0e344f6e9dfee3793320dc9cb79e3231c2eeda87
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581265"
---
# <a name="whats-different-about-the-v20-endpoint"></a>Čím se liší koncovým bodem v2.0?

Pokud jste se seznámili se službou Azure Active Directory (Azure AD) nebo mají integrované aplikace s Azure AD v minulosti, existují určité rozdíly v koncovým bodem v2.0, nemusí očekávat. Tento článek zdůrazňuje rozdíly za pochopení.

> [!NOTE]
> Ne všechny funkce a scénáře služby Azure AD podporuje koncový bod verze 2.0. Pokud chcete zjistit, pokud je vhodné použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Účty Microsoft a účty Azure AD

Koncový bod verze 2.0 umožňuje vývojářům umožňuje psát aplikace, které přijímají přihlášení z účtů Accounts Microsoftu a Azure AD, pomocí koncového bodu jeden ověřování. To vám umožňuje napsat aplikace kompletně účet bez ohledu na, což znamená, že aplikace může být ignorant typu účtu, který se přihlásí uživatel. Může být vaše aplikace vědět, typ účtu se používá v konkrétní relace, ale není nutné.

Například pokud vaše aplikace volá [Microsoft Graphu](https://graph.microsoft.io), některé další funkce a data budou k dispozici firemním uživatelům, jako je například jejich Sharepointových webech nebo data adresáře. Ale pro mnoho akcí jako například [čtení pošty uživatele](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), může být napsán kód, stejně pro účty Microsoft Accounts a Azure AD. 

Integrace aplikace s Accounts Microsoft a účty Azure AD je nyní jeden jednoduchý proces. Jediné sady koncových bodů, jediná knihovna a registrace aplikace s jedním můžete použít k získání přístupu k světů příjemce i enterprise. Další informace o koncový bod verze 2.0, podívejte se na [přehledu](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Nový portál pro registraci aplikace

Postup pro registraci aplikace, která funguje s koncovým bodem v2.0, musíte použít portál pro registraci aplikace Microsoftu: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Toto je na portálu, kde můžete získat ID aplikace, upravte přihlašovací stránku vaší aplikace a další. Všechno, co potřebujete pro přístup k portálu je účet Microsoft s využitím - osobní a pracovní/školní účet.

## <a name="one-app-id-for-all-platforms"></a>ID aplikací pro všechny platformy

Pokud jste používali Azure AD, zaregistrovaný pravděpodobně několik různých aplikací pro jeden projekt. Například pokud jste vytvořili na webu a aplikace pro iOS, museli jste samostatně, je zaregistrovat pomocí dvou různých ID aplikace. Portál pro registraci aplikace Azure AD se uživatelé museli provést toto rozlišení během registrace:

![Původní registrace aplikace uživatelského rozhraní](./media/azure-ad-endpoint-comparison/old_app_registration.PNG)

Podobně pokud máte web a webové rozhraní api back-endu, možná jste se zaregistrovali každý jako samostatné aplikace ve službě Azure AD. Nebo pokud máte aplikaci pro iOS a aplikace pro Android, také možná jste se zaregistrovali dva různé aplikace. Registrace jednotlivých komponent aplikace vedla k nějaké neočekávané chování pro vývojáře a jejich zákazníky:

* Jednotlivé komponenty se zobrazily jako samostatné aplikace v tenantovi Azure AD z každého zákazníka.
* Při pokusu o použití zásad ke správě přístupu k nebo odstranění aplikace Správce tenanta, bylo by nutné udělat pro každou komponentu aplikace.
* Pokud zákazníci schválená k aplikaci, by jednotlivé komponenty zobrazí v obrazovkami pro vyjádření souhlasu jako různé aplikace.

S koncovým bodem v2.0 můžete teď registraci všech součástí projektu jako registrace aplikace s jedním a používat jednotné Id aplikace pro celý projekt. Můžete přidat několik "platformy" pro každý projekt a zadejte příslušná data pro každou platformu, kterou přidáte. Samozřejmě můžete vytvořit libovolný počet aplikací, jako v závislosti na požadavcích, ale pro většinu případů by měly být nutné pouze jedno Id aplikace.

Naše cílem je, že to vést k více zjednodušená správa aplikací a vývojové prostředí a vytvořit více konsolidované zobrazení jednoho projektu, který může být pracujete.

## <a name="scopes-not-resources"></a>Obory, ne prostředky

Ve službě Azure AD, aplikace se může chovat jako **prostředků**, nebo příjemce tokenů. Prostředek můžete definovat celou řadou **obory** nebo **oAuth2Permissions** , které rozumí, umožňuje tak klientským aplikacím požádat o tokeny do tohoto prostředku pro určitou sadu oborů. Vezměte v úvahu Azure AD Graph API s ukázkovým prostředku:

* Identifikátor prostředku nebo `AppID URI`: `https://graph.windows.net/`
* Obory, nebo `OAuth2Permissions`: `Directory.Read`, `Directory.Write`atd. 

To vše platí pro koncový bod verze 2.0. Aplikace se může i nadále chovat jako prostředek, definice oborů a identifikovaného identifikátorem URI. Klientské aplikace můžou stále žádat o přístup k tyto obory. Však byla změněna tak, že klient požádá o oprávnění. V minulosti autorizaci OAuth 2.0 můžou mít vypadal žádost do služby Azure AD:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

kde **prostředků** parametr uvedené, který prostředek se klientská aplikace požaduje autorizaci. Azure AD vypočítat oprávnění požadovaná aplikací na základě statické konfigurace na portálu Azure a vystavené tokeny odpovídajícím způsobem. Nyní stejné OAuth 2.0 autorizaci žádost vypadá jako:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

kde **oboru** parametr označuje, který prostředek a oprávnění aplikace požaduje autorizaci. Požadovaný prostředek se stále nachází v požadavku – jednoduše je zahrnuta v každé hodnoty parametru oboru. Pomocí parametru oboru tímto způsobem umožňuje koncovým bodem v2.0 aby víc odpovídala specifikaci OAuth 2.0 a lépe v souladu s běžné postupy. Také umožňuje aplikacím provádět [přírůstkové souhlasu](#incremental-and-dynamic-consent), které je popsáno v další části.

## <a name="incremental-and-dynamic-consent"></a>Přírůstkové a dynamické souhlas

Aplikace registrovaná v Azure AD dřív potřebná pro určení jejich požadovaná oprávnění OAuth 2.0 na webu Azure Portal, v okamžiku vytvoření aplikace:

![Oprávnění registrace uživatelského rozhraní](./media/azure-ad-endpoint-comparison/app_reg_permissions.PNG)

Oprávnění aplikaci jako povinnou byly nakonfigurovány **staticky**. I když to povolená konfigurace aplikace existovat na portálu Azure portal a udržovat kód nice a jednoduché, představuje několik problémů pro vývojáře:

* Aplikace se museli znát všechna oprávnění, které někdy potřebovat při vytváření aplikace. Přidání oprávnění v čase bylo obtížné procesu.
* Aplikace musela všechny prostředky, ke kterým by nikdy přístup předem vědět. Bylo to složité vytvářet aplikace, které by přístup k libovolného počtu prostředků.
* Aplikace musí požádat o všechna oprávnění, která byste zas někdy potřebovali po uživatel poprvé přihlásil. V některých případech to vedlo k dlouhý seznam oprávnění, která nedoporučuje koncovým uživatelům schválení aplikace přístup na počáteční přihlášení.

S koncovým bodem v2.0, můžete určit oprávnění požadavkům vašich aplikací **dynamicky**, za běhu a během pravidelné používání vaší aplikace. Uděláte to tak, můžete určit obory, které vaše aplikace potřebuje v libovolném časovém okamžiku v čase uvedete v `scope` parametr žádost o autorizaci:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Výše uvedené požadavky na oprávnění pro aplikaci číst data adresáře uživatele služby Azure AD, stejně jako zápis dat do jejich adresáře. Pokud uživatel vyjádřil souhlas se tato oprávnění v minulosti pro dané aplikace, bude zadat své přihlašovací údaje a přihlášení k aplikaci. Pokud uživatele. nevyjádřil souhlas některý z těchto oprávnění, koncový bod verze 2.0 požádá uživatele o souhlas s těmito oprávněními. Další informace si můžete přečíst [oprávnění, vyjádření souhlasu a obory](v2-permissions-and-consent.md).

Umožňuje dynamicky prostřednictvím aplikace a požádat o oprávnění `scope` parametr dává vám plnou kontrolu nad komfortem při uživatele. Pokud chcete, můžete nastavit frontload vašeho svolení prostředí a požádat o všechna oprávnění v jedné žádosti počáteční autorizace. Nebo pokud vaše aplikace vyžaduje velké množství oprávnění, můžete shromažďovat tato oprávnění od uživatele postupně, protože pokus o použití určitých funkcí vaší aplikace v čase.

## <a name="well-known-scopes"></a>Známé obory

### <a name="offline-access"></a>Přístup v režimu offline

Aplikace s využitím koncového bodu v2.0 může vyžadovat použití nové dobře známé oprávnění pro aplikace – `offline_access` oboru. Všechny aplikace bude muset požádat o toto oprávnění, pokud potřebují přístup k prostředkům jménem uživatele po dobu delší dobu, i když uživatel není aktivně aplikaci používat. `offline_access` Obor bude zobrazovat uživatelům v dialogových oknech souhlasu jako "Přístup k datům v režimu offline", které uživatel musí vyjádřit souhlas s. Požaduje `offline_access` oprávnění vám umožní vaši aplikaci pro příjem z koncového bodu v2.0 refresh_tokens OAuth 2.0. Refresh_tokens jsou dlouhodobé a může probíhat po delší dobu přístupu pro novou access_tokens OAuth 2.0. 

Pokud vaše aplikace nebude vyžadovat `offline_access` oboru, nezíská refresh_tokens. To znamená, že když uplatníte authorization_code v tok autorizačního kódu OAuth 2.0, obdržíte jen zpět access_token z `/token` koncového bodu. Tento access_token zůstanou platné krátké doby (obvykle jedna hodina), ale nakonec vyprší. V této bodu v čase, vaše aplikace bude muset přesměruje uživatele zpět `/authorize` koncový bod k načtení nové authorization_code. Během toto přesměrování uživatel může nebo nemusí být nutné znovu zadat své přihlašovací údaje nebo znovu souhlas s oprávnění v závislosti na typu aplikace.

Další informace o OAuth 2.0, refresh_tokens a access_tokens, podívejte se [referenci na protokol v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil a e-mailu

V minulosti by nejzákladnější tok OpenID Connect přihlašování s Azure AD poskytují velké množství informací o uživateli ve výsledné id_token. Deklarace identity v tokentu id_token můžou obsahovat uživatele název upřednostňované uživatelské jméno, e-mailovou adresu, ID objektu a další.

Informace, které `openid` oboru nabízí vaše aplikace je přístup k s omezeným přístupem. `openid` Oboru pouze vám umožní vaši aplikaci pro uživatele a získejte identifikátor specifický pro aplikace pro uživatele. Pokud chcete získat osobních údajů o uživateli ve vaší aplikaci, vaše aplikace bude nutné požádat o další oprávnění od uživatele. Dva nové obory – `email` a `profile` obory – vám umožní získat další oprávnění.

`email` Oboru umožňuje vaší aplikaci přístup ke primární e-mailovou adresu uživatele prostřednictvím `email` deklarací identity v požadavku id_token. 

`profile` Oboru zajišťuje přístup k vaší aplikaci na jiné základní informace o uživateli – jejich název, upřednostňované uživatelské jméno, ID objektu a tak dále.

Díky tomu můžete kód vaší aplikace v podobě minimální zpřístupnění – požádáte uživatele, můžete pouze pro konkrétní sadu informací, že vaše aplikace vyžaduje, aby fungovala správně. Další informace o těchto oborů, naleznete v tématu [odkaz oboru v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Deklarace identity tokenu

Deklarace identity v tokeny vystavené službou koncovým bodem v2.0 nesmí být shodný s tokeny vystavené službou obecně k dispozici koncové body služby Azure AD. Migrace na novou službu aplikace by neměl Předpokládejme, že konkrétní deklarace identity budou existovat v id_tokens nebo access_tokens. Další informace o konkrétní deklarace identity v tokenech v2.0, protože ho, najdete v článku [odkaz tokenu v2.0](v2-id-and-access-tokens.md).

## <a name="limitations"></a>Omezení

Existuje několik omezení, které mají být vědět, když používáte bod verze 2.0. Další informace, pokud platí kterákoli z těchto omezení pro váš konkrétní scénář, najdete v článku [v2.0 omezení doc](active-directory-v2-limitations.md).
