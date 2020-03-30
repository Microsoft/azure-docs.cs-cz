---
title: Používání Azure CDN s CORS | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat Síť pro doručování obsahu Azure (CDN) pomocí sdílení prostředků mezi zdroji (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 169de21b6dbdafaaeff64e315daa104f3b6faadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278101"
---
# <a name="using-azure-cdn-with-cors"></a>Používání Azure CDN s CORS
## <a name="what-is-cors"></a>Co je CORS?
CORS (Cross Origin Resource Sharing) je funkce HTTP, která umožňuje webové aplikaci spuštěné pod jednou doménou přístup k prostředkům v jiné doméně. Aby se snížila možnost útoků skriptování mezi weby, všechny moderní webové prohlížeče implementují bezpečnostní omezení známé jako [zásady stejného původu](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Tím zabráníte webové stránce v volání api v jiné doméně.  CORS poskytuje bezpečný způsob, jak povolit jeden původ (původní doména) volat api v jiném původu.

## <a name="how-it-works"></a>Jak to funguje
Existují dva typy požadavků CORS, *jednoduché požadavky* a *složité požadavky.*

### <a name="for-simple-requests"></a>Pro jednoduché požadavky:

1. Prohlížeč odešle požadavek CORS s další hlavičkou požadavku **HTTP originu.** Hodnota tohoto záhlaví je původ, který sloužil nadřazené stránce, která je definována jako kombinace *protokolu,* *domény* a *portu.*  Když se stránka\:z https //www.contoso.com pokusí o přístup k datům uživatele v fabrikam.com původu, bude fabrikam.com odeslána následující hlavička požadavku:

   `Origin: https://www.contoso.com`

2. Server může odpovědět některou z následujících možností:

   * Hlavička **Access-Control-Allow-Origin** v odpovědi označující, která původní lokalita je povolena. Například:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kód chyby HTTP, například 403, pokud server nepovoluje požadavek na příčný původ po kontrole hlavičky Originu

   * Hlavička **Access-Control-Allow-Origin** se zástupným znakem, který umožňuje všechny počátky:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Pro komplexní požadavky:

Komplexní požadavek je požadavek CORS, kde je prohlížeč povinen odeslat *požadavek před výstupem* (tj. předběžnou sondu) před odesláním skutečného požadavku CORS. Požadavek kontroly před výstupem požádá server o oprávnění, `OPTIONS` pokud původní požadavek CORS může pokračovat a je požadavkem na stejnou adresu URL.

> [!TIP]
> Další podrobnosti o tocích CORS a běžných úskalích naleznete v [průvodci cors pro rest API](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scénáře zástupných symbolů nebo jednoho původu
CORS v Azure CDN bude fungovat automaticky bez další konfigurace, když je **hlavička Access-Control-Allow-Origin** nastavena na zástupný znak (*) nebo jeden původ.  CDN uloží první odpověď do mezipaměti a následné požadavky budou používat stejnou hlavičku.

Pokud již byly na CDN podány požadavky před nastavením CORS v původu, budete muset vyčistit obsah v obsahu koncového bodu, abyste znovu načetli obsah pomocí záhlaví **Access-Control-Allow-Origin.**

## <a name="multiple-origin-scenarios"></a>Více scénářů původu
Pokud potřebujete povolit, aby byl pro CORS povolen konkrétní seznam původů, věci se trochu zkomplikují. K problému dochází, když CDN ukládá do mezipaměti **hlavičku Access-Control-Allow-Origin** pro první původ CORS.  Když jiný původ CORS provede další požadavek, CDN bude sloužit v mezipaměti **Access-Control-Allow-Origin** záhlaví, které se neshodují.  Existuje několik způsobů, jak to napravit.

### <a name="azure-cdn-standard-profiles"></a>Standardní profily Azure CDN
Na Azure CDN Standard od Microsoftu můžete vytvořit pravidlo v [modulu standardnípravidla](cdn-standard-rules-engine-reference.md) pro kontrolu záhlaví **Origin** u požadavku. Pokud se jedná o platný původ, pravidlo nastaví hlavičku **Access-Control-Allow-Origin** s požadovanou hodnotou. V tomto případě je **hlavička Access-Control-Allow-Origin** ze zdrojového serveru souboru ignorována a modul pravidel CDN zcela spravuje povolené počátky CORS.

![Příklad pravidel s motorem standardních pravidel](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Do pravidla můžete přidat další akce a upravit další hlavičky odpovědí, například **Metody přístupu a řízení- povolení**.
> 

V **Azure CDN Standard od Akamai**, jediný mechanismus, který umožňuje více původů bez použití původu zástupných symbolů je použití [mezipaměti řetězce dotazu](cdn-query-string.md). Povolte nastavení řetězce dotazu pro koncový bod CDN a potom použijte jedinečný řetězec dotazu pro požadavky z každé povolené domény. To bude mít za následek cdn ukládání do mezipaměti samostatný objekt pro každý jedinečný řetězec dotazu. Tento přístup však není ideální, protože bude mít za následek více kopií stejného souboru uloženého v mezipaměti na CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium od společnosti Verizon
Pomocí modulu pravidel Verizon Premium budete muset [vytvořit pravidlo pro](cdn-rules-engine.md) kontrolu záhlaví **Originu** na žádosti.  Pokud se jedná o platný původ, vaše pravidlo nastaví hlavičku **Access-Control-Allow-Origin** s původem uvedeným v žádosti.  Pokud není povolen původ zadaný v hlavičce **Originu,** pravidlo by mělo vynechat hlavičku **Access-Control-Allow-Origin,** což způsobí, že prohlížeč požadavek odmítne. 

S motorem pravidel Premium to lze provést dvěma způsoby. V obou případech je **hlavička Access-Control-Allow-Origin** ze zdrojového serveru souboru ignorována a modul pravidel CDN zcela spravuje povolené počátky CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jeden regulární výraz se všemi platnými počátky
V takovém případě vytvoříte regulární výraz, který obsahuje všechny počátky, které chcete povolit: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN Premium od společnosti Verizon** používá [regulární výrazy kompatibilní s perlem](https://pcre.org/) jako modul pro regulární výrazy.  K ověření regulárního výrazu můžete použít nástroj, jako jsou [regulární výrazy 101.](https://regex101.com/)  Všimněte si, že znak "/" je platný v regulárních výrazech a nemusí být uvozen, ale únik tohoto znaku je považován za osvědčený postup a je očekáván některými validátory regulárních výrazů.
> 
> 

Pokud se regulární výraz shoduje, vaše pravidlo nahradí hlavičku **Access-Control-Allow-Origin** (pokud existuje) z počátku s původem, který odeslal žádost.  Můžete také přidat další hlavičky CORS, například **metody access-control-allow-methods**.

![Příklad pravidel s regulárním výrazem](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Požadavek záhlaví pravidlo pro každý původ.
Místo regulárních výrazů můžete místo toho vytvořit samostatné pravidlo pro každý původ, který chcete povolit, pomocí [podmínky shody](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) **zástupných symbolů záhlaví požadavku** . Stejně jako u metody regulárního výrazu samotný modul pravidel nastaví záhlaví CORS. 

![Příklad pravidel bez regulárního výrazu](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> Ve výše uvedeném příkladu použití zástupného znaku * říká modulu pravidel tak, aby odpovídal protokolu HTTP i PROTOKOLU HTTPS.
> 
> 



