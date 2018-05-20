---
title: Co je nového v koncového bodu v2.0 Azure AD? | Dokumenty Microsoft
description: Porovnání mezi původní Azure AD v2.0 koncové body a.
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
ms.reviewer: celested
ms.custom: aaddev
ms.openlocfilehash: c2af2e9f7ab87edcb9ac2b074514c36ac8fec2b6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="whats-different-about-the-v20-endpoint"></a>Co se liší o koncový bod v2.0?
Pokud jste se seznámili s Azure Active Directory nebo aplikací mít integrované s Azure AD v minulosti, může být určité rozdíly v koncový bod v2.0, které by uživatel očekával. Tento dokument se volá na těchto rozdílů za pochopení.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0. Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Účty Microsoft a účty Azure AD
Koncový bod v2.0 umožňují vývojářům zápisu aplikace, které přijímají přihlášení z účtů Microsoft Accounts i Azure AD, pomocí koncový bod jedné ověřování. To vám dává možnost zapisovat aplikaci zcela účet bez ohledu na; může být typu účtu, který uživatel přihlásí, které ignorují. Samozřejmě můžete *můžete* zvýšit vaši aplikaci vědět, typ účtu se používá v konkrétní relace, ale není nutné.

Například pokud aplikace zavolá [Microsoft Graph](https://graph.microsoft.io), některé další funkce a dat budou k dispozici uživatelům enterprise, jako jsou jejich weby SharePoint nebo dat adresáře. Ale pro mnoho akce jako například [čtení e-mailu uživatele](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), kód může být napsán stejně pro účty Microsoft Accounts a Azure AD. 

Integrace aplikace s Accounts Microsoft a účty Azure AD je nyní jeden jednoduchý proces. K získání přístupu k příjemce i enterprise světů můžete použít jednu sadu koncové body, jeden knihovny a registrace jediné aplikaci. Další informace o koncový bod v2.0, podívejte se na [přehledu](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Nový portál pro registraci aplikace
Chcete-li zaregistrovat aplikaci, která funguje s koncovým bodem v2.0, musíte použít nové portálu pro registraci aplikace: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Toto je portál, kde lze získat ID aplikace přizpůsobení vzhledu přihlašovací stránku vaší aplikace a další. Všechny potřebné pro přístup k portálu je účet používá technologii Microsoft - osobní nebo pracovní nebo školní účet.

## <a name="one-app-id-for-all-platforms"></a>ID jednu aplikaci pro všechny platformy
Pokud jste použili Azure Active Directory, pravděpodobně jste registrováni několik různých aplikací pro jeden projekt. Například pokud jste vytvořili web a aplikaci pro iOS, museli jste samostatně, zaregistrujte je použití dvou různých ID aplikace. Portálu pro registraci aplikace Azure AD vynutit vám umožní provádět na těchto rozdílech během registrace:

![Původní registrace aplikace uživatelského rozhraní](../../media/active-directory-v2-flows/old_app_registration.PNG)

Podobně pokud jste měli web a back-endové webové rozhraní api, může jste si zaregistrovali každý jako samostatnou aplikaci ve službě Azure AD. Nebo pokud jste měli k aplikaci pro iOS a aplikace pro Android, můžete také může mít registrovaný dvě různé aplikace. Registrace jednotlivých součástí aplikace vedla k některé neočekávané chování pro vývojáře a zákazníkům:

* Jednotlivé komponenty zobrazovaly jako samostatnou aplikaci v klientovi služby Azure Active Directory z každého zákazníka.
* Při pokusu o použití zásad ke správě přístupu k nebo odstranit aplikaci správce klienta, mají by k tomu pro jednotlivé součásti aplikace.
* Když zákazníci dá souhlas k aplikaci, jednotlivé komponenty se zobrazí na obrazovce souhlasu jako různých aplikací.

S koncovým bodem v2.0 teď můžete zaregistrovat všechny součásti projektu jako registrace jedna aplikace a použít jeden Id aplikace pro celý projekt. Můžete přidat několik "platforem" pro každý projekt a zadejte příslušná data pro každou platformu, kterou přidáte. Samozřejmě můžete vytvořit libovolný počet aplikací, jako třeba v závislosti na požadavcích, ale pro většině případů musí být nutné jenom jeden Id aplikace.

Naše cílem je, že to vést k více zjednodušené správy aplikací a vývojové prostředí a vytvořit více konsolidované zobrazení jeden projekt, který je možné, že pracujete na.

## <a name="scopes-not-resources"></a>Obory, ne prostředky
V Azure Active Directory, můžete aplikaci chovat jako **prostředků**, nebo příjemce tokenů. Prostředek můžete definovat několika **obory** nebo **oAuth2Permissions** , že rozumí, umožňuje tak klientským aplikace k žádosti o tokeny pro tento prostředek pro určitou oborů. Vezměte v úvahu Azure AD Graph API jako příklad prostředku:

* Identifikátor prostředku nebo `AppID URI`: `https://graph.windows.net/`
* Obory, nebo `OAuth2Permissions`: `Directory.Read`, `Directory.Write`atd. 

Všechny tyto platí pro koncový bod v2.0. Aplikace může stále chovat jako prostředek, definovat obory a identifikovat podle identifikátoru URI. Klientské aplikace může stále požádat o přístup k tyto obory. Ale způsob, ve kterém klient požaduje tato oprávnění se změnila. V minulosti autorizace OAuth 2.0 požadavek do služby Azure AD může mít hledá jako:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

kde **prostředků** parametr uvedené, který prostředek se klientská aplikace požaduje autorizaci pro. Azure AD vypočtená oprávnění požadovaná aplikace na základě statické konfigurace na portálu Azure a vystavené tokeny odpovídajícím způsobem. Nyní stejné OAuth 2.0 autorizace požadavku vypadá jako:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

kde **oboru** parametr určuje, který prostředek a oprávnění aplikace požaduje autorizaci pro. Požadovaný prostředek je stále velmi přítomné v žádosti – jednoduše je zahrnut v jednotlivých hodnot parametru oboru. Pomocí parametru oboru tímto způsobem umožňuje koncového bodu v2.0 být více kompatibilní se specifikací OAuth 2.0 a přesněji zarovnaná s obvyklé postupy odvětví. Umožňuje také aplikace k provedení [přírůstkové souhlasu](#incremental-and-dynamic-consent), který je popsaný v následující části.

## <a name="incremental-and-dynamic-consent"></a>Přírůstkové a dynamické souhlasu
Aplikace zaregistrované ve službě Azure AD dříve potřeby k zadání jejich požadovaná oprávnění OAuth 2.0 na portálu Azure při vytváření aplikace:

![Registrace oprávnění uživatelského rozhraní](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

Oprávnění požadované aplikace byla nakonfigurována **staticky**. Když to povoleno konfigurace aplikace existovat v portálu Azure a uchovávat kód dobrý a jednoduché, uvede několik problémů pro vývojáře:

* Aplikace se museli vědět všechna oprávnění, která se někdy potřebovat při vytváření aplikace. Přidání oprávnění v čase bylo obtížné procesu.
* Aplikace musela všechny prostředky, ke kterým by někdy přístup předem znát. Bylo obtížné vytvářet aplikace, které mají přístup libovolný počet prostředků.
* Aplikace musí požádat o všechna oprávnění, která by někdy potřebovat při uživatele první přihlášení. V některých případech to vedlo k velmi dlouhý seznam oprávnění, která se nedoporučuje. koncoví uživatelé z schválení aplikace přístup na počáteční přihlášení.

S koncovým bodem v2.0, můžete zadat oprávnění vašim potřebám aplikace **dynamicky**, v době běhu během regulární využití vaší aplikace. Uděláte to tak, můžete zadat obory, vaše aplikace, musí v libovolném bodě v čase zahrnutím je do `scope` parametr požadavku ověřování:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Výše uvedené požadavky oprávnění pro aplikaci pro čtení dat adresáře uživatele Azure AD, stejně jako zapsat data do svého adresáře. Pokud uživatel souhlasí s tato oprávnění v minulosti pro tuto konkrétní aplikaci, bude jednoduše zadat své přihlašovací údaje a být přihlášeni k aplikaci. Pokud uživatel nebyla přijata některé z těchto oprávnění, koncový bod v2.0 požádá uživatele o souhlas těchto oprávnění. Další informace si můžete přečíst na [oprávnění, souhlasu a obory](active-directory-v2-scopes.md).

Povolení aplikace a požádat o oprávnění dynamicky prostřednictvím `scope` parametr získáte plnou kontrolu nad možnosti pro uživatele. Pokud chcete, můžete frontload vašeho souhlasu prostředí a požádat o všechna oprávnění v jedné žádosti počáteční autorizace. Nebo pokud vaše aplikace vyžaduje velký počet oprávnění, můžete získat tato oprávnění od uživatele postupně, jak se pokus o použití některých funkcí aplikace v čase.

## <a name="well-known-scopes"></a>Známé oborů
#### <a name="offline-access"></a>Přístup v režimu offline
Aplikace používající koncového bodu v2.0 může vyžadovat použití nové dobře známé oprávnění pro aplikace - `offline_access` oboru. Všechny aplikace bude muset požádat o toto oprávnění, pokud je nutné pro přístup k prostředkům jménem uživatele delší dobu dobu, i když uživatel nemusí být používá aktivně aplikace. `offline_access` Obor se zobrazí uživateli v dialogových oknech souhlasu jako "Přístup k datům v režimu offline", který uživatel musí vyjádřit souhlas s. Požaduje `offline_access` oprávnění umožní vaší webové aplikace na příjem OAuth 2.0 refresh_tokens z koncového bodu v2.0. Refresh_tokens je dlouhodobé a může být vyměňují pro nové access_tokens OAuth 2.0 po delší dobu přístupu. 

Pokud vaše aplikace neuvede v požadavku `offline_access` oboru, neobdrží refresh_tokens. To znamená, že po uplatníte authorization_code v toku kódu autorizace OAuth 2.0, se pouze zobrazí zpět access_token z `/token` koncový bod. Tento access_token zůstane platná, a to krátkou dobu (obvykle jedna hodina), ale nakonec vyprší. AT, který bod v čase, vaše aplikace bude potřeba přesměruje uživatele zpět na `/authorize` koncový bod k načtení nové authorization_code. Během této přesměrování, uživatel může nebo nemusí být nutné znovu zadat své přihlašovací údaje nebo znovu souhlas oprávnění, v závislosti na typu aplikace.

Další informace o protokolu OAuth 2.0, refresh_tokens a access_tokens, podívejte se [referenci na protokol v2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile-and-email"></a>OpenID, profil a e-mailu
Nejzákladnější tok OpenID Connect přihlášení s Azure Active Directory v minulosti by poskytují množství informací o uživateli ve výsledné požadavku id_token. Deklarací identity ve požadavku id_token může obsahovat uživatele název, upřednostňované uživatelské jméno, e-mailovou adresu, ID objektu a další.

Nemůžeme se teď omezení informace, `openid` oboru dává vám přístup aplikace k. Obor, openid, bude pouze povolit přihlášení uživatele ve vaší aplikaci a přijímat identifikátor specifický pro aplikace pro uživatele. Pokud chcete získat identifikovatelné osobní údaje (PII) o uživateli ve vaší aplikaci, vaše aplikace bude muset požádat o další oprávnění od uživatele. Představujeme dva nové obory – `email` a `profile` obory – které umožňují učinit.

`email` Obor je velmi jednoduchá – umožňuje vám přístup aplikace k primární e-mailovou adresu uživatele prostřednictvím `email` deklarací identity v požadavku id_token. `profile` Oboru poskytuje přístup k vaší aplikaci pro všechny ostatní základní informace o uživateli – jejich název, upřednostňované uživatelské jméno, ID objektu a tak dále.

To umožňuje kód aplikace způsobem minimální zpřístupnění – můžete požádat uživatele jenom pro sadu informací, že vaše aplikace vyžaduje pro své práci. Další informace o těchto oborů, najdete v části [odkaz oboru v2.0](active-directory-v2-scopes.md).

## <a name="token-claims"></a>Token deklarací identity
Deklarace identity v tokenech vystavený koncového bodu v2.0 nebudou shodovat s tokeny vydané obecně dostupná koncové body Azure AD – aplikace migraci na nové služby by neměl předpokládá konkrétní deklaraci identity bude existovat v id_tokens nebo access_tokens. Další informace o konkrétní vygenerované v v2.0 tokeny deklarací identity najdete v tématu [odkaz tokenu v2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Omezení
Existuje několik omezení, která má být vědět, když používáte bod v2.0. Naleznete [v2.0 omezení doc](active-directory-v2-limitations.md) chcete zobrazit, pokud žádné z těchto omezení platí pro konkrétní scénář.
