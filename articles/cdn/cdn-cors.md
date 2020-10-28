---
title: Použití Azure CDN s CORS | Microsoft Docs
description: Naučte se používat Azure Content Delivery Network (CDN) pro sdílení prostředků mezi zdroji (CORS).
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778927"
---
# <a name="using-azure-cdn-with-cors"></a>Použití Azure CDN s CORS
## <a name="what-is-cors"></a>Co je CORS?
CORS (sdílení prostředků mezi zdroji) je funkce HTTP, která umožňuje webové aplikaci spuštěné v jedné doméně přistupovat k prostředkům v jiné doméně. Aby se snížila pravděpodobnost útoků prostřednictvím skriptování mezi weby, všechny moderní webové prohlížeče implementují omezení zabezpečení označované jako [zásady stejného původu](https://www.w3.org/Security/wiki/Same_Origin_Policy).  To brání webové stránce v volání rozhraní API v jiné doméně.  CORS poskytuje zabezpečený způsob, jak umožníte jednomu zdroji (zdrojové doméně) volat rozhraní API v jiném zdroji.

## <a name="how-it-works"></a>Jak to funguje
Existují dva typy požadavků CORS, *jednoduchých požadavků* a *složitých požadavků.*

### <a name="for-simple-requests"></a>Pro jednoduché požadavky:

1. Prohlížeč odešle požadavek CORS s další hlavičkou **původní** žádosti protokolu HTTP. Hodnota tohoto záhlaví je zdrojem, který sloužil nadřazenou stránku, která je definována jako kombinace *protokolu,* *domény* a *portu.*  Když se stránka z https \: //www.contoso.com pokusí získat přístup k datům uživatele v Fabrikam.com původu, pošle se následující Hlavička požadavku na Fabrikam.com:

   `Origin: https://www.contoso.com`

2. Server může reagovat s některým z následujících způsobů:

   * Hlavička **Access-Control-Allow-Origin** v odpovědi, která označuje, který zdrojový web je povolen. Příklad:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kód chyby HTTP, například 403, pokud Server nepovoluje požadavek na více zdrojů po kontrole původní hlavičky

   * Hlavička **Access-Control-Allow-Origin** , která má zástupný znak, který umožňuje všechny zdroje:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Pro komplexní požadavky:

Komplexní požadavek je požadavek CORS, ve kterém se před odesláním skutečné žádosti CORS vyžaduje, aby se v prohlížeči poslal *požadavek na kontrolu před výstupem* (tj. Předběžný test). Požadavek na předběžné kontroly požádá o oprávnění serveru, pokud může pokračovat původní žádost CORS, a je `OPTIONS` požadavek na stejnou adresu URL.

> [!TIP]
> Další podrobnosti o tocích CORS a běžných nástrah najdete v [Průvodci CORS pro rozhraní REST API](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scénáře se zástupnými znaky nebo jedním zdrojem
CORS v Azure CDN bude fungovat automaticky bez další konfigurace, pokud je hlavička **Access-Control-Allow-Origin** nastavená na zástupný znak (*) nebo na jeden počátek.  CDN bude ukládat první odpověď do mezipaměti a následné požadavky budou používat stejnou hlavičku.

Pokud se už požadavky na síť CDN nastavily na svém zdroji, budete muset vyprázdnit obsah v obsahu koncového bodu, aby se obsah znovu načítat pomocí hlavičky **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Scénáře s více zdroji
Pokud potřebujete povolit, aby určitý seznam zdrojů byl pro CORS povolený, věci budou mít trochu složitější. K tomuto problému dochází, když CDN ukládá do mezipaměti hlavičku **Access-Control-Allow-Origin** pro první původ CORS.  Když jiný původ CORS vytvoří následnou žádost, bude CDN **používat hlavičku Access-Control-Allow-Origin** v mezipaměti, která se neshoduje.  Existuje několik způsobů, jak to opravit.

### <a name="azure-cdn-standard-profiles"></a>Profily Azure CDN Standard
Na Azure CDN Standard od Microsoftu můžete vytvořit pravidlo v [modulu Standard Rules](cdn-standard-rules-engine-reference.md) pro kontrolu **původní** hlavičky v žádosti. Pokud se jedná o platný zdroj, vaše pravidlo nastaví hlavičku **Access-Control-Allow-Origin** s požadovanou hodnotou. V tomto případě se hlavička **Access-Control-Allow-Origin** ze zdrojového serveru souboru ignoruje a modul pravidel CDN plně spravuje povolené zdroje CORS.

![Příklad pravidel se strojem standardních pravidel](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Do pravidla můžete přidat další akce, které upraví další hlavičky odpovědí, jako je například **Access-Control-Allow-Methods** .
> 

V **Azure CDN Standard od Akamai** je jediným mechanismem, který umožňuje více míst původu bez použití zástupného znaku, použít [ukládání řetězců dotazu do mezipaměti](cdn-query-string.md). Povolte nastavení řetězce dotazu pro koncový bod CDN a pak pro žádosti z každé povolené domény použijte jedinečný řetězec dotazu. Výsledkem je, že CDN ukládá do mezipaměti samostatný objekt pro každý řetězec jedinečného dotazu. Tento přístup není ideální, ale v takovém případě bude výsledkem více kopií stejného souboru v mezipaměti CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium z Verizon
Pomocí modulu pravidel Verizon Premium budete muset [vytvořit pravidlo](./cdn-verizon-premium-rules-engine.md) pro kontrolu **počáteční** hlavičky v žádosti.  Pokud se jedná o platný zdroj, vaše pravidlo nastaví hlavičku **Access-Control-Allow-Origin** s počátkem uvedeným v žádosti.  Pokud se počátek zadaný v záhlaví **původu** nepovoluje, vaše pravidlo by mělo vynechat hlavičku **Access-Control-Allow-Origin** , která způsobí odmítnutí žádosti v prohlížeči. 

Existují dva způsoby, jak to provést s modulem pravidel Premium. V obou případech se hlavička **Access-Control-Allow-Origin** ze zdrojového serveru souboru ignoruje a modul pravidel pro CDN plně spravuje povolené zdroje CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jeden regulární výraz se všemi platnými zdroji
V tomto případě vytvoříte regulární výraz, který bude obsahovat všechny zdroje, které chcete dovolit: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **Azure CDN Premium od Verizon** využívá jako svůj modul regulární [výrazy kompatibilní s Perl](https://pcre.org/) jako jeho modul pro regulární výrazy.  Můžete použít nástroj jako [regulární výrazy 101](https://regex101.com/) k ověření regulárního výrazu.  Všimněte si, že znak "/" je platný v regulárních výrazech a nemusí být uvozen řídicím znakem, ale označení je považováno za osvědčený postup a je očekáváno některými validátory regulárního výrazu.
> 
> 

Pokud regulární výraz odpovídá, vaše pravidlo nahradí hlavičku **Access-Control-Allow-Origin** (pokud existuje) od počátku s počátkem, který odeslal požadavek.  Můžete také přidat další hlavičky CORS, například **Access-Control-Allow-Methods** .

![Příklad pravidel s regulárním výrazem](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Pravidlo pro záhlaví žádosti pro každý počátek.
Místo regulárních výrazů můžete místo toho vytvořit samostatné pravidlo pro každý zdroj, který chcete použít, pomocí [podmínky shody](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)se **zástupnými znaky v hlavičce požadavku** . Stejně jako u metody regulárních výrazů nastavuje modul pravidel pouze hlavičky CORS. 

![Příklad pravidel bez regulárního výrazu](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> V předchozím příkladu použití zástupného znaku * instruuje modul pravidel, aby odpovídal HTTP i HTTPS.
> 
>