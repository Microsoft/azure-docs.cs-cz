---
title: Referenční informace o Azure Active Directorych nejnovějších změn | Microsoft Docs
description: Přečtěte si o změnách provedených v protokolech Azure AD, které by mohly mít vliv na vaši aplikaci.
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
ms.date: 07/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747b279b38ec3b73f19194825195f1b3450d4514
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834851"
---
# <a name="whats-new-for-authentication"></a>Co je nového pro ověřování? 

>Získejte oznámení o aktualizacích této stránky. Stačí přidat [tuto adresu URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) ke ČTEČCE kanálů RSS.

Systém ověřování mění a přidává funkce průběžně pro zlepšení dodržování standardů zabezpečení a standardů. V tomto článku najdete informace o následujících podrobnostech, abyste měli přehled o nejnovějším vývoji.

- Nejnovější funkce
- Známé problémy
- Změny protokolu
- Zastaralé funkce

> [!TIP] 
> Tato stránka se pravidelně aktualizuje, takže je často navštěvovaná. Pokud není uvedeno jinak, jsou tyto změny zavedeny pouze pro nově registrované aplikace.  

## <a name="upcoming-changes"></a>Nadcházející změny

2019. srpna: Vyhodnotit sémantiku POST podle pravidel pro analýzu adresy URL – při duplicitních parametrech dojde k chybě, uvozovky napříč parametry se už nebudou ignorovat a [BOM](https://www.w3.org/International/questions/qa-byte-order-mark) se ignoruje.

## <a name="july-2019"></a>Červenec 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Tokeny jenom pro aplikace pro jednotlivé klienty jsou vydané jenom v případě, že klientská aplikace existuje v tenantovi prostředků.

**Datum platnosti**: 26. července 2019

**Ovlivněné koncové body**: [V 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) i [v 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Ovlivněný protokol**: [Přihlašovací údaje klienta (tokeny jenom pro aplikace)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Změna zabezpečení 26a v reálném čase, která mění způsob, jakým jsou vydávány tokeny jenom pro aplikace (prostřednictvím udělení přihlašovacích údajů klienta). Dříve byly aplikacím dovoleno získat tokeny pro volání jakékoli jiné aplikace bez ohledu na přítomnost v tenantovi nebo rolích, které jsou pro tuto aplikaci přijaté.  Toto chování bylo aktualizováno, aby pro prostředky (někdy nazývané webová rozhraní API) bylo nastaveno na jeden tenant (výchozí), klientská aplikace musí existovat v tenantovi prostředku.  Počítejte s tím, že stávající souhlas mezi klientem a rozhraním API ještě není nutný a aplikace by měly i nadále provádět vlastní kontroly autorizace, aby se `roles` zajistilo, že je přítomná deklarace identity a že obsahuje očekávanou hodnotu pro rozhraní API.

Chybová zpráva pro tento scénář nyní uvádí: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Pokud chcete tento problém vyřešit, použijte prostředí pro vyjádření souhlasu správce k vytvoření instančního objektu klientské aplikace ve vašem tenantovi nebo ho vytvořte ručně.  Tento požadavek zajistí, že tenant udělil oprávnění aplikace pro provoz v rámci tenanta.  

#### <a name="example-request"></a>Příklad požadavku

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`V tomto příkladu je tenant prostředků (autorita) contoso.com, aplikace prostředků je klientská aplikace pro jednoho tenanta, která `gateway.contoso.com/api` je volána pro tenanta contoso, a klientská aplikace `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`je.  Pokud má klientská aplikace v rámci služby Contoso.com instanční objekt, může tento požadavek pokračovat.  Pokud ale ne, požadavek se nezdaří s chybou uvedenou výše.  

Pokud se ale jednalo o aplikaci pro více tenantů, bude mít žádost i nadále bez ohledu na klientskou aplikaci, která má instanční objekt v rámci služby Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Identifikátory URI pro přesměrování teď můžou obsahovat parametry řetězce dotazu.

**Datum platnosti**: 22. července 2019

**Ovlivněné koncové body**: V 1.0 i v 2.0

**Ovlivněný protokol**: Všechny toky

V per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)můžou aplikace Azure AD nyní registrovat a používat přesměrované identifikátory URI (Reply) s parametry statického https://contoso.com/oauth2?idp=microsoft) dotazu (například pro žádosti OAuth 2,0.  Dynamické identifikátory URI pro přesměrování jsou stále zakázané, protože představují bezpečnostní riziko, a nelze je použít k uchování informací o stavu v rámci požadavku na ověření – pro to `state` použijte parametr.

Parametr statického dotazu podléhá párování řetězců pro identifikátory URI přesměrování, jako je jakákoli jiná část identifikátoru URI přesměrování – Pokud není zaregistrován žádný řetězec, který by odpovídal identifikátoru redirect_uri s dekódováním identifikátoru URI, pak bude požadavek odmítnut.  Pokud se identifikátor URI najde v registraci aplikace, použije se celý řetězec pro přesměrování uživatele, včetně parametru statického dotazu. 

Všimněte si, že v tuto chvíli (konec července 2019) se uživatelské rozhraní pro registraci aplikace v Azure Portal pořád zablokují parametry dotazu.  Manifest aplikace ale můžete upravit ručně a přidat parametry dotazu a otestovat ho v aplikaci.  


## <a name="march-2019"></a>Březen 2019

### <a name="looping-clients-will-be-interrupted"></a>Přeruší se klienti cyklů.

**Datum platnosti**: 25. března 2019

**Ovlivněné koncové body**: V 1.0 i v 2.0

**Ovlivněný protokol**: Všechny toky

V některých případech se může stát, že se klientské aplikace v krátké době vydávají stovky stejné přihlašovací žádosti.  Tyto požadavky můžou nebo nemusí být úspěšné, ale všechny přispívat ke špatnému uživatelskému prostředí a zvýšenému zatížení pro IDP, což zvyšuje latenci pro všechny uživatele a snižuje dostupnost IDP.  Tyto aplikace pracují mimo hranice normálního využití a měly by se aktualizovat tak, aby se chovaly správně.  

Klientům, kteří vydávají duplicitní žádosti víckrát, se pošle `invalid_grant` Chyba:. `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 

Většina klientů nebude muset změnit chování, aby se předešlo této chybě.  Touto chybou ovlivní jenom nesprávně nakonfigurované klienty (bez mezipaměti tokenů nebo těch, které vykazují smyčky výzev).  Klienti jsou sledováni na základě jednotlivých instancí místně (prostřednictvím souboru cookie) na následujících faktorech:

* Nápověda uživatele, pokud existuje

* Požadované obory nebo prostředky

* ID klienta

* Přesměrovat identifikátor URI

* Typ a režim odpovědi

Aplikace, které provádějí více požadavků (15 +) v krátkém časovém intervalu (5 minut), `invalid_grant` zobrazí chybu s vysvětlením, že se jedná o smyčky.  Vyžádané tokeny mají dostatečně dlouhou dobu životnosti (ve výchozím nastavení je to 10 minut minimálně, 60 minut), takže opakované žádosti v tomto časovém období nejsou potřebné.  

Všechny aplikace by se `invalid_grant` měly zpracovat zobrazením interaktivní výzvy místo tichého vyžadování tokenu.  Aby se zabránilo této chybě, klienti by měli zajistit správné ukládání tokenů, které obdrží, do mezipaměti.


## <a name="october-2018"></a>Říjen 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Autorizační kódy se už nedají znovu použít.

**Datum platnosti**: 15. listopadu 2018

**Ovlivněné koncové body**: V 1.0 i v 2.0

**Ovlivněný protokol**: [Tok kódu](v2-oauth2-auth-code-flow.md)

Od 15. listopadu 2018, Azure AD, přestane přijímat předchozích ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Jakákoli nová aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, bude mít invalid_grant chybu.

Další informace o aktualizačních tokenech najdete v tématu [aktualizace přístupových tokenů](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Pokud používáte ADAL nebo MSAL, je to zpracováno knihovnou – nahraďte druhou instanci ' AcquireTokenByAuthorizationCodeAsync ' ' AcquireTokenSilentAsync '. 

## <a name="may-2018"></a>Květen 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Tokeny ID se nedají použít pro OBO tok.

**Datum**: 1. května 2018

**Ovlivněné koncové body**: V 1.0 i v 2.0

**Ovlivněné protokoly**: Implicitní tok toku a [OBO](v1-oauth2-on-behalf-of-flow.md)

Od 1. května 2018 se id_tokens nedá použít jako kontrolní výraz v toku OBO pro nové aplikace. Místo toho by se měly používat přístupové tokeny k zabezpečení rozhraní API, a to i mezi klientem a střední vrstvou stejné aplikace. Aplikace zaregistrované před 1. května 2018 budou fungovat i nadále a můžou být schopné Exchange id_tokens pro přístupový token. Tento model se však nepovažuje za osvědčený postup.

Pokud chcete tuto změnu obejít, můžete postupovat takto:

1. Vytvořte webové rozhraní API pro vaši aplikaci s jedním nebo více obory. Tento explicitní vstupní bod umožní jemnější kontrolu a zabezpečení.
1. V manifestu vaší aplikace v [Azure Portal](https://portal.azure.com) nebo na [portálu pro registraci aplikací](https://apps.dev.microsoft.com)zajistěte, aby aplikace mohla vystavovat přístupové tokeny prostřednictvím implicitního toku. To se řídí `oauth2AllowImplicitFlow` klíčem.
1. Když klientská aplikace požaduje id_token prostřednictvím `response_type=id_token`, vyžádá si také přístupový token (`response_type=token`) pro webové rozhraní API vytvořené výše. Proto při použití koncového bodu `scope` v 2.0 by měl parametr vypadat `api://GUID/SCOPE`podobně jako. U koncového bodu `resource` v 1.0 by parametr měl být identifikátorem URI aplikace webového rozhraní API.
1. Předání tohoto přístupového tokenu do prostřední vrstvy místo id_token.  
