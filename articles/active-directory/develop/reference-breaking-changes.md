---
title: Odkaz na změny služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Další informace o změnách provedených v protokolech Azure AD, které mohou mít vliv na vaši aplikaci.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1248063c1b4c1b1e124ff671797450dd5c1b8727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050174"
---
# <a name="whats-new-for-authentication"></a>Co je nového pro ověřování? 

>Získejte upozornění na aktualizace této stránky. Stačí přidat [tuto adresu URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) do čtečky kanálů RSS.

Systém ověřování průběžně mění a přidává funkce, aby se zlepšilo zabezpečení a dodržování standardů. Chcete-li mít aktuální informace o nejnovějším vývoji, tento článek obsahuje informace o následujících podrobnostech:

- Nejnovější funkce
- Známé problémy
- Změny protokolu
- Zastaralé funkce

> [!TIP] 
> Tato stránka je pravidelně aktualizována, takže často navštěvujte. Není-li uvedeno jinak, jsou tyto změny zavedeny pouze pro nově registrované aplikace.  

## <a name="upcoming-changes"></a>Nadcházející změny

V tuto chvíli není žádná naplánována.  Níže naleznete změny, které jsou nebo přicházejí do výroby. 

## <a name="march-2020"></a>Březen 2020 

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Uživatelská hesla budou omezena na 256 znaků.

**Datum účinnosti**: 13.

**Ovlivněné koncové body**: V1.0 a v2.0

**Ovlivněný protokol**: Všechny toky uživatelů. 

Uživatelé s hesly delšími než 256 znaků, kteří se přihlašují přímo do služby Azure AD (na rozdíl od federovaného protokolu IDP, jako je ADFS), se nebudou moci přihlásit od 13.  Správci mohou přijímat žádosti o pomoc při resetování hesla uživatelů. 

Chyba v protokolech přihlášení bude AADSTS 50052: InvalidPasswordExceedsMaxLength

Zprávu:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Nápravných:

Uživatel se nemůže přihlásit, protože jeho heslo překračuje povolenou maximální délku. Měli by kontaktovat svého správce, aby resetoval heslo. Pokud je pro jejich tenanta povoleno snop, mohou obnovit své heslo podle odkazu "Zapomněli jste heslo".



## <a name="february-2020"></a>Únor 2020 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Prázdné fragmenty budou připojeny ke každému přesměrování HTTP z koncového bodu přihlášení. 

**Datum účinnosti**: 8. února 2020

**Ovlivněné koncové body**: V1.0 a v2.0

**Ovlivněný protokol**: Toky OAuth a OIDC, které používají response_type=query - v některých případech se tok autorizačního kódu vztahuje na [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) a [implicitní tok](v2-oauth2-implicit-grant-flow.md). 

Pokud je odpověď na ověření odeslána z login.microsoftonline.com do aplikace prostřednictvím přesměrování HTTP, služba připojí prázdný fragment k adrese URL odpovědi.  Tím se zabrání třídy přesměrování útoků tím, že zajistí, že prohlížeč vymaže všechny existující fragment v žádosti o ověření.  Žádné aplikace by měly být závislé na tomto chování. 


## <a name="august-2019"></a>Srpen 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Sémantiku formuláře POST budou vynucovány přísněji - mezery a uvozovky budou ignorovány

**Datum účinnosti**: Září 2, 2019

**Ovlivněné koncové body**: V1.0 a v2.0

**Ovlivněný protokol**: Kdekoli se používá POST[(přihlašovací údaje klienta,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) [uplatnění autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)a [uplatnění tokenu aktualizace)](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)

Počínaje týdnem 9/2 budou požadavky na ověření, které používají metodu POST, ověřeny pomocí přísnějších standardů HTTP.  Konkrétně mezery a dvojité uvozovky (") již nebudou odebrány z hodnot formuláře požadavku. Neočekává se, že tyto změny přeruší všechny existující klienty a zajistí, že požadavky odeslané do služby Azure AD jsou spolehlivě zpracovány pokaždé. V budoucnu (viz výše) plánujeme dodatečně odmítnout duplicitní parametry a ignorovat kusovník v rámci požadavků. 

Příklad:

Dnes `?e=    "f"&g=h` je analyzován `?e=f&g=h` atožně `e`  ==  `f`jako - tak .  S touto změnou by nyní byla `e`  ==  `    "f"` analyzována tak, že - to je nepravděpodobné, že by byl platný argument, a požadavek by nyní selhal. 


## <a name="july-2019"></a>Červenec 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokeny pouze pro aplikace pouze pro aplikace s jedním tenantem jsou vydávány pouze v případě, že klientská aplikace existuje v tenantovi prostředků.

**Datum účinnosti**: Červenec 26, 2019

**Ovlivněné koncové body**: [V1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) a [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Ovlivněný protokol**: [Pověření klienta (tokeny pouze pro aplikace)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Změna zabezpečení přešla do provozu 26. Dříve aplikace bylo povoleno získat tokeny volat jiné aplikace, bez ohledu na přítomnost v tenantovi nebo role, které souhlasily pro tuto aplikaci.  Toto chování bylo aktualizováno tak, aby pro prostředky (někdy nazývané webová api) nastavena na jednoklient (výchozí), klientská aplikace musí existovat v rámci klienta.  Všimněte si, že existující souhlas mezi klientem a rozhraní API stále není vyžadováno `roles` a aplikace by měly stále dělat vlastní kontroly autorizace, aby bylo zajištěno, že deklarace je k dispozici a obsahuje očekávanou hodnotu rozhraní API.

Chybová zpráva pro tento scénář aktuálně uvádí: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Chcete-li tento problém odstranit, použijte prostředí souhlasu správce k vytvoření instančního objektu klientské aplikace ve vašem tenantovi nebo k jeho vytvoření ručně.  Tento požadavek zajišťuje, že klient udělil aplikaci oprávnění k provozu v rámci klienta.  

#### <a name="example-request"></a>Příklad požadavku

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`V tomto příkladu je klient prostředků (autorita) contoso.com, aplikace prostředků `gateway.contoso.com/api` je aplikace s jedním klientem `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`volaná pro klienta Contoso a klientská aplikace je .  Pokud klientská aplikace má instanční objekt v rámci Contoso.com, tento požadavek může pokračovat.  Pokud tomu tak však není, požadavek se nezdaří s výše uvedenou chybou.  

Pokud však aplikace brány Contoso byla víceklientskou aplikací, požadavek by pokračoval bez ohledu na to, zda klientská aplikace má v rámci Contoso.com instanční objekt.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Přesměrování identifikátorů URI nyní může obsahovat parametry řetězce dotazu

**Datum účinnosti**: Červenec 22, 2019

**Ovlivněné koncové body**: V1.0 a v2.0

**Protokol ovlivněný**: Všechny toky

Podle [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2), Aplikace Azure AD nyní můžete zaregistrovat a používat přesměrování `https://contoso.com/oauth2?idp=microsoft`(odpověď) URI se statickými parametry dotazu (například) pro oAuth 2.0 požadavky.  Dynamické přesměrování identifikátorů URI jsou stále zakázány, protože představují bezpečnostní riziko, a to nelze použít `state` k uchování informací o stavu v rámci požadavku na ověření – pro tento parametr použijte parametr.

Parametr statického dotazu podléhá porovnávání řetězců pro přesměrování identifikátorů URI, stejně jako jakákoli jiná část identifikátoru URI přesměrování - pokud není registrován žádný řetězec, který by odpovídal redirect_uri dekódovaného identifikátoru URI, bude požadavek odmítnut.  Pokud je identifikátor URI nalezen v registraci aplikace, bude celý řetězec použit k přesměrování uživatele, včetně parametru statického dotazu. 

Všimněte si, že v tuto chvíli (konec července 2019) uživatelské ho uživatelského rozhraní registrace aplikace na webu Azure Portal stále blokují parametry dotazu.  Manifest aplikace však můžete upravit ručně, abyste přidali parametry dotazu a otestovali to ve vaší aplikaci.  


## <a name="march-2019"></a>Březen 2019

### <a name="looping-clients-will-be-interrupted"></a>Opakování klientů bude přerušeno

**Datum účinnosti**: Březen 25, 2019

**Ovlivněné koncové body**: V1.0 a v2.0

**Protokol ovlivněný**: Všechny toky

Klientské aplikace mohou někdy nechová, vydávání stovky stejné žádosti o přihlášení v krátkém časovém období.  Tyto požadavky mohou nebo nemusí být úspěšné, ale všechny přispívají ke špatné uživatelské zkušenosti a zvýšené úlohy pro IDP, zvýšení latence pro všechny uživatele a snížení dostupnosti IDP.  Tyto aplikace pracují mimo hranice běžného použití a měly by být aktualizovány tak, aby se chovaly správně.  

Klientům, kteří vydají duplicitní `invalid_grant` požadavky `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`vícekrát, bude odeslána chyba: . 

Většina klientů nebude muset měnit chování, aby se zabránilo této chybě.  Pouze nesprávně nakonfigurované klienty (ty bez ukládání tokenů do mezipaměti nebo ty, které již vykazují rychlé smyčky) budou ovlivněny touto chybou.  Klienti jsou sledováni na základě instance místně (prostřednictvím souboru cookie) na následující faktory:

* Nápověda uživatele, pokud existuje

* Obory nebo požadovaný zdroj

* ID klienta

* Identifikátor URI pro přesměrování

* Typ a režim odezvy

Aplikace, které v krátkém časovém období (5 minut) podávají více `invalid_grant` požadavků (15+), obdrží chybu vysvětlující, že se smyčkuují.  Požadované tokeny mají dostatečně dlouhou životnost (minimálně 10 minut, ve výchozím nastavení 60 minut), takže opakované požadavky v tomto časovém období nejsou nutné.  

Všechny aplikace `invalid_grant` by měly být zvládány zobrazením interaktivní výzvy, nikoli tichým vyžádáním tokenu.  Aby se předešlo této chybě, klienti by měli zajistit, že jsou správně ukládání tokenů, které obdrží.


## <a name="october-2018"></a>Říjen 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Autorizační kódy již nelze znovu použít.

**Datum účinnosti**: 15.

**Ovlivněné koncové body**: V1.0 a v2.0

**Ovlivněný protokol**: [Tok kódu](v2-oauth2-auth-code-flow.md)

listopadu 2018 Azure AD přestane přijímat dříve používané ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá, aby Azure AD v souladu se specifikací OAuth a bude vynuceno na v1 a v2 koncové body.

Pokud vaše aplikace opakovaně používá autorizační kódy pro získání tokenů pro více prostředků, doporučujeme použít kód k získání aktualizačního tokenu a pak použít tento obnovovací token k získání dalších tokenů pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale tokeny aktualizace lze použít vícekrát napříč více prostředky. Každá nová aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, získá chybu invalid_grant.

Další informace o tokenech aktualizace naleznete [v tématu Aktualizace přístupových tokenů](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Pokud používáte ADAL nebo MSAL, je to zpracováno knihovnou - nahraďte druhou instanci 'AcquireTokenByAuthorizationCodeAsync' 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Květen 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID tokeny nelze použít pro tok OBO

**Datum**: 1. května 2018

**Ovlivněné koncové body**: V1.0 a v2.0

**Ovlivněné protokoly**: Implicitní tok a [tok jménem](v2-oauth2-on-behalf-of-flow.md)

května 2018 nelze použít id_tokens jako kontrolní výraz v toku Objektu pro nové aplikace. Přístupové tokeny by měly být použity místo k zabezpečení api, i mezi klientskou a střední vrstvou stejné aplikace. Aplikace registrované před id_tokens 1. tento vzor však není považován za osvědčený postup.

Chcete-li tuto změnu obejít, postupujte takto:

1. Vytvořte webové rozhraní API pro vaši aplikaci s jedním nebo více obory. Tento explicitní vstupní bod umožní jemnější odstupňovanou kontrolu a zabezpečení.
1. V manifestu vaší aplikace na [portálu Azure](https://portal.azure.com) nebo [na portálu pro registraci aplikací](https://apps.dev.microsoft.com)se ujistěte, že aplikace může vydávat přístupové tokeny prostřednictvím implicitního toku. Tohle je ovládáno `oauth2AllowImplicitFlow` klíčem.
1. Když vaše klientská aplikace `response_type=id_token`požaduje id_token prostřednictvím`response_type=token`, také požádat o přístupový token ( ) pro webové rozhraní API vytvořené výše. Proto při použití koncového bodu v2.0 `scope` by `api://GUID/SCOPE`měl parametr vypadat podobně jako . V koncovém bodě v1.0 by `resource` měl být parametr em URI aplikace webového rozhraní API.
1. Předajte tento přístupový token střední vrstvě místo id_token.  
