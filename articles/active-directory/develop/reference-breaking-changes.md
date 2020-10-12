---
title: Referenční informace pro Azure Active Directory přerušující změny
description: Přečtěte si o změnách provedených v protokolech Azure AD, které by mohly mít vliv na vaši aplikaci.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 5/4/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 0c5abf345fda9db4cc5123360245e42ea0ef40e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115029"
---
# <a name="whats-new-for-authentication"></a>Co je nového pro ověřování?

> Oznámení o aktualizacích této stránky získáte vložením této adresy URL do čtečky kanálů RSS:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

Systém ověřování mění a přidává funkce průběžně pro zlepšení dodržování standardů zabezpečení a standardů. V tomto článku najdete informace o následujících podrobnostech, abyste měli přehled o nejnovějším vývoji.

- Nejnovější funkce
- Známé problémy
- Změny protokolu
- Zastaralé funkce

> [!TIP]
> Tato stránka se pravidelně aktualizuje, takže je často navštěvovaná. Pokud není uvedeno jinak, jsou tyto změny zavedeny pouze pro nově registrované aplikace.

## <a name="upcoming-changes"></a>Nadcházející změny

V tuto chvíli není naplánováno žádné.  Níže najdete informace o změnách, které se nacházejí v produktu nebo přicházejí do produkčního prostředí.

## <a name="may-2020"></a>Květen 2020

### <a name="azure-government-endpoints-are-changing"></a>Mění se Azure Government koncové body.

**Datum začátku platnosti**: 5. června 2020 (dokončuje se) 

**Ovlivněné koncové body**: vše

**Ovlivněný protokol**: všechny toky

Od 1. června 2018 se oficiální autorita Azure Active Directory (AAD) pro Azure Government změnila z `https://login-us.microsoftonline.com` na `https://login.microsoftonline.us` . Tato změna se taky aplikuje na Microsoft 365e vysoké a DoD, které Azure Government AAD i služby. Pokud vlastníte aplikaci v rámci tenanta státní správy USA, je nutné aplikaci aktualizovat, aby se uživatelé v `.us` koncovém bodě mohli podepisovat.  

Od 5. května Azure AD zahájí vynucování změny koncového bodu a zablokuje uživatelům státní správy, aby se přihlásili k aplikacím hostovaným v klientech státní správy USA pomocí veřejného koncového bodu ( `microsoftonline.com` ).  Ovlivněné aplikace začnou vidět chybu `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` . Tato chyba označuje, že se aplikace pokouší přihlásit se k veřejnému koncovému bodu veřejného cloudu na uživatele státní správy USA. Pokud je vaše aplikace ve veřejném cloudu a je určená pro podporu pro státní správu USA, budete muset [aplikaci aktualizovat, aby se podporovala explicitně](./authentication-national-cloud.md). To může vyžadovat vytvoření nové registrace aplikace v cloudu pro státní správu USA. 

Vynucování této změny se provádí postupným zavedením na základě toho, jak často se uživatelé z cloudu pro státní správu USA přihlásí k aplikaci – aplikace, které se přihlašují uživatelům z oblasti státní správy USA zřídka, uvidí vynucování jako první a aplikace, které často používají uživatelé státní správy USA, budou platit jako poslední. Očekáváme, že se vynucení dokončí napříč všemi aplikacemi v červnu 2020. 

Další podrobnosti najdete v [příspěvku na blogu Azure Government na této migraci](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/). 

## <a name="march-2020"></a>Březen 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Hesla uživatelů budou omezena na 256 znaků.

**Datum účinnosti**: 13. března 2020

**Ovlivněné koncové body**: vše

**Ovlivněný protokol**: všechny toky uživatelů.

Uživatelé s heslem delšími než 256 znaků, které se přihlásí přímo do služby Azure AD (na rozdíl od federovaného IDP jako ADFS), se nebudou moct přihlásit od 13. března 2020 a budou vyzváni k resetování hesla.  Správci mohou obdržet požadavky, které vám pomohou resetovat heslo uživatele.

Chyba v protokolech přihlášení bude AADSTS 50052: InvalidPasswordExceedsMaxLength

Zpráva `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Nápravy

Uživatel se nemůže přihlásit, protože jeho heslo překračuje povolenou maximální délku. Měli by kontaktovat správce, aby heslo resetoval. Pokud je pro svého tenanta povolený SSPR, můžou resetovat svoje heslo pomocí odkazu Zapomenuté heslo.



## <a name="february-2020"></a>Únor 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Prázdné fragmenty budou připojeny ke každému přesměrování HTTP z koncového bodu přihlášení.

**Datum účinnosti**: 8. února 2020

**Ovlivněné koncové body**: v 1.0 i v 2.0

**Ovlivněný protokol**: toky OAuth a OIDC, které používají response_type = Query – zahrnuje [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) v některých případech a [implicitní tok](v2-oauth2-implicit-grant-flow.md).

Když se pošle odpověď na ověření z login.microsoftonline.com do aplikace přes přesměrování protokolu HTTP, služba připojí k adrese URL s odpovědí prázdný fragment.  Tím se zabrání třída útoků přesměrování tím, že zajistí, že prohlížeč vymaže všechny existující fragmenty v žádosti o ověření.  Žádné aplikace by neměly mít závislost na tomto chování.


## <a name="august-2019"></a>Srpen 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Sémantika POST Form bude vyvykonána více striktně a uvozovky budou ignorovány.

**Datum účinnosti**: 2. září 2019

**Ovlivněné koncové body**: v 1.0 i v 2.0

**Ovlivněný protokol**: použití příspěvku odkudkoli ([přihlašovací údaje klienta](./v2-oauth2-client-creds-grant-flow.md), [uplatnění autorizačního kódu](./v2-oauth2-auth-code-flow.md), [ROPC](./v2-oauth-ropc.md), [OBO](./v2-oauth2-on-behalf-of-flow.md)a [uplatnění aktualizačního tokenu](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

Od týdne 9/2 budou žádosti o ověření, které používají metodu POST, ověřeny pomocí přísnějších standardů protokolu HTTP.  Konkrétně mezery a dvojité uvozovky (") již nebudou odebrány z hodnot formuláře žádosti. Tyto změny se neočekávají pro přerušení stávajících klientů a zajistí, že požadavky odeslané do služby Azure AD budou spolehlivě zpracovávány pokaždé. V budoucnu (viz výše) plánujeme také odmítat duplicitní parametry a ignorovat v rámci požadavků.

Příklad:

Dnes `?e=    "f"&g=h` se analyzuje stejně jako `?e=f&g=h` `e`  ==  `f` .  Tato změna by teď mohla být analyzována tak, aby byla `e`  ==  `    "f"` nepravděpodobná platná argument a požadavek by nyní byl neúspěšný.


## <a name="july-2019"></a>Červenec 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokeny jenom pro aplikace pro jednotlivé klienty jsou vydané jenom v případě, že klientská aplikace existuje v tenantovi prostředků.

**Datum účinnosti**: 26. července 2019

**Ovlivněné koncové body**: [v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) i [v 2.0](./v2-oauth2-client-creds-grant-flow.md)

**Ovlivněný protokol**: [přihlašovací údaje klienta (tokeny jenom pro aplikace)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

Změna zabezpečení 26a v reálném čase, která mění způsob, jakým jsou vydávány tokeny jenom pro aplikace (prostřednictvím udělení přihlašovacích údajů klienta). Dříve byly aplikacím dovoleno získat tokeny pro volání jakékoli jiné aplikace bez ohledu na přítomnost v tenantovi nebo rolích, které jsou pro tuto aplikaci přijaté.  Toto chování bylo aktualizováno, aby pro prostředky (někdy nazývané webová rozhraní API) bylo nastaveno na jeden tenant (výchozí), klientská aplikace musí existovat v tenantovi prostředku.  Počítejte s tím, že stávající souhlas mezi klientem a rozhraním API ještě není nutný a aplikace by měly i nadále provádět vlastní kontroly autorizace, aby se zajistilo, že `roles` je přítomná deklarace identity a že obsahuje očekávanou hodnotu pro rozhraní API.

Chybová zpráva pro tento scénář nyní uvádí:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Pokud chcete tento problém vyřešit, použijte prostředí pro vyjádření souhlasu správce k vytvoření instančního objektu klientské aplikace ve vašem tenantovi nebo ho vytvořte ručně.  Tento požadavek zajistí, že tenant udělil oprávnění aplikace pro provoz v rámci tenanta.

#### <a name="example-request"></a>Příklad požadavku

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` V tomto příkladu je tenant prostředků (autorita) contoso.com, aplikace prostředků je klientská aplikace pro jednoho tenanta, která je volána `gateway.contoso.com/api` pro tenanta contoso, a klientská aplikace je `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  Pokud má klientská aplikace v rámci služby Contoso.com instanční objekt, může tento požadavek pokračovat.  Pokud ale ne, požadavek se nezdaří s chybou uvedenou výše.

Pokud se ale jednalo o aplikaci pro více tenantů, bude mít žádost i nadále bez ohledu na klientskou aplikaci, která má instanční objekt v rámci služby Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Identifikátory URI pro přesměrování teď můžou obsahovat parametry řetězce dotazu.

**Datum účinnosti**: 22. července 2019

**Ovlivněné koncové body**: v 1.0 i v 2.0

**Ovlivněný protokol**: všechny toky

V rámci [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)můžou aplikace Azure AD nyní registrovat a používat přesměrované identifikátory URI (Reply) s parametry statického dotazu (například `https://contoso.com/oauth2?idp=microsoft` ) pro žádosti OAuth 2,0.  Dynamické identifikátory URI pro přesměrování jsou stále zakázané, protože představují bezpečnostní riziko, a nelze je použít k uchování informací o stavu v rámci požadavku na ověření – pro to použijte `state` parametr.

Parametr statického dotazu podléhá párování řetězců pro identifikátory URI přesměrování, jako jakákoli jiná část identifikátoru URI přesměrování – Pokud není zaregistrován žádný řetězec, který by odpovídal identifikátoru URI-Dekódovatelné redirect_uri, požadavek se odmítne.  Pokud se identifikátor URI najde v registraci aplikace, použije se celý řetězec pro přesměrování uživatele, včetně parametru statického dotazu.

Všimněte si, že v tuto chvíli (konec července 2019) se uživatelské rozhraní pro registraci aplikace v Azure Portal pořád zablokují parametry dotazu.  Manifest aplikace ale můžete upravit ručně a přidat parametry dotazu a otestovat ho v aplikaci.


## <a name="march-2019"></a>Březen 2019

### <a name="looping-clients-will-be-interrupted"></a>Přeruší se klienti cyklů.

**Datum účinnosti**: 25. března 2019

**Ovlivněné koncové body**: v 1.0 i v 2.0

**Ovlivněný protokol**: všechny toky

V některých případech se může stát, že se klientské aplikace v krátké době vydávají stovky stejné přihlašovací žádosti.  Tyto požadavky můžou nebo nemusí být úspěšné, ale všechny přispívat ke špatnému uživatelskému prostředí a zvýšenému zatížení pro IDP, což zvyšuje latenci pro všechny uživatele a snižuje dostupnost IDP.  Tyto aplikace pracují mimo hranice normálního využití a měly by se aktualizovat tak, aby se chovaly správně.

Klientům, kteří vydávají duplicitní žádosti víckrát, se pošle `invalid_grant` Chyba: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

Většina klientů nebude muset změnit chování, aby se předešlo této chybě.  Touto chybou ovlivní jenom nesprávně nakonfigurované klienty (bez mezipaměti tokenů nebo těch, které vykazují smyčky výzev).  Klienti jsou sledováni na základě jednotlivých instancí místně (prostřednictvím souboru cookie) na následujících faktorech:

* Nápověda uživatele, pokud existuje

* Požadované obory nebo prostředky

* ID klienta

* Identifikátor URI pro přesměrování

* Typ a režim odpovědi

Aplikace, které provádějí více požadavků (15 +) v krátkém časovém intervalu (5 minut) `invalid_grant` , zobrazí chybu s vysvětlením, že se jedná o smyčky.  Vyžádané tokeny mají dostatečně dlouhou dobu životnosti (ve výchozím nastavení je to 10 minut minimálně, 60 minut), takže opakované žádosti v tomto časovém období nejsou potřebné.

Všechny aplikace by se měly zpracovat `invalid_grant` zobrazením interaktivní výzvy místo tichého vyžadování tokenu.  Aby se zabránilo této chybě, klienti by měli zajistit správné ukládání tokenů, které obdrží, do mezipaměti.


## <a name="october-2018"></a>Říjen 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Autorizační kódy se už nedají znovu použít.

**Datum účinnosti**: 15. listopadu 2018

**Ovlivněné koncové body**: v 1.0 i v 2.0

**Ovlivněný protokol**: [tok kódu](v2-oauth2-auth-code-flow.md)

Od 15. listopadu 2018 přestane Azure AD přijímat dříve použité ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá přenést službu Azure AD v souladu se specifikací OAuth a vynucuje se v koncových bodech V1 a v2.

Pokud vaše aplikace znovu používá autorizační kódy k získání tokenů pro více prostředků, doporučujeme použít kód k získání obnovovacího tokenu a pak pomocí tohoto aktualizačního tokenu získat další tokeny pro jiné prostředky. Autorizační kódy se dají použít jenom jednou, ale aktualizace tokenů se dají použít víckrát napříč několika prostředky. Jakákoli nová aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, obdrží chybu invalid_grant.

Další informace o aktualizačních tokenech najdete v tématu [aktualizace přístupových tokenů](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Pokud používáte ADAL nebo MSAL, je to zpracováno knihovnou – nahraďte druhou instanci ' AcquireTokenByAuthorizationCodeAsync ' ' AcquireTokenSilentAsync '.

## <a name="may-2018"></a>Květen 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Tokeny ID se nedají použít pro OBO tok.

**Datum**: Květen 1, 2018

**Ovlivněné koncové body**: v 1.0 i v 2.0

**Ovlivněné protokoly**: implicitní tok a [tok](v2-oauth2-on-behalf-of-flow.md) za běhu

Od 1. května 2018 se id_tokens nedá použít jako kontrolní výraz v OBO toku pro nové aplikace. Místo toho by se měly používat přístupové tokeny k zabezpečení rozhraní API, a to i mezi klientem a střední vrstvou stejné aplikace. Aplikace zaregistrované před 1. května 2018 budou fungovat i nadále a budou moci Exchange id_tokens pro přístupový token. Tento model se však nepovažuje za osvědčený postup.

Pokud chcete tuto změnu obejít, můžete postupovat takto:

1. Vytvořte webové rozhraní API pro vaši aplikaci s jedním nebo více obory. Tento explicitní vstupní bod umožní jemnější kontrolu a zabezpečení.
1. V manifestu vaší aplikace v [Azure Portal](https://portal.azure.com) nebo na [portálu pro registraci aplikací](https://apps.dev.microsoft.com)zajistěte, aby aplikace mohla vystavovat přístupové tokeny prostřednictvím implicitního toku. To se řídí `oauth2AllowImplicitFlow` klíčem.
1. Když klientská aplikace požaduje id_token přes `response_type=id_token` , vyžádá si také přístupový token ( `response_type=token` ) pro webové rozhraní API vytvořené výše. Proto při použití koncového bodu v 2.0 `scope` by měl parametr vypadat podobně jako `api://GUID/SCOPE` . U koncového bodu v 1.0 `resource` by parametr měl být identifikátorem URI aplikace webového rozhraní API.
1. Předejte tento přístupový token do prostřední vrstvy místo id_token.