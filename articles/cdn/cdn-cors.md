---
title: Používání Azure CDN s CORS | Dokumentace Microsoftu
description: Další informace o použití Azure Content Delivery Network (CDN) k s sdílení prostředků mezi zdroji (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 337f2a31d60d54b47c692b06b5d63c28c0964061
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876223"
---
# <a name="using-azure-cdn-with-cors"></a>Používání Azure CDN s CORS
## <a name="what-is-cors"></a>Co je CORS?
CORS (Cross původu sdílení prostředků) je funkce protokolu HTTP, která umožňuje webové aplikaci spuštěné v rámci jedné domény pro přístup k prostředkům v jiné doméně. Aby bylo možné snížení rizika vzniku útoky skriptování napříč weby, všechny moderní webové prohlížeče implementují bezpečnostní omezení, označované jako [zásada stejného zdroje](https://www.w3.org/Security/wiki/Same_Origin_Policy).  To zabraňuje webové stránky z volání rozhraní API v jiné doméně.  CORS nabízí zabezpečený způsob, jak povolit jeden počátek (zdrojová doména) pro volání rozhraní API v jiné zdroje.

## <a name="how-it-works"></a>Jak to funguje
Existují dva typy požadavků CORS *jednoduché požadavky* a *složité požadavky.*

### <a name="for-simple-requests"></a>Pro jednoduché požadavky:

1. V prohlížeči odesílá požadavek CORS ještě **původu** hlavičku požadavku HTTP. Hodnotu této hlavičky origin, která obsluhuje nadřazenou stránku, která je definována jako kombinace *protokolu* *domény,* a *portu.*  Když stránku z https\:/ / www.contoso.com pokusí o přístup k datům uživatele v původu fabrikam.com, následující hlavičky žádosti by se odeslaly na fabrikam.com:

   `Origin: https://www.contoso.com`

2. Server může odpovídat s žádným z následujících akcí:

   * **Access-Control-Allow-Origin** hlaviček v odpovědi určující, které zdrojový web je povolen. Příklad:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kód chyby protokolu HTTP jako je například 403, pokud server nepovoluje po kontrole hlavička počátečního žádosti nepůvodního zdroje

   * **Access-Control-Allow-Origin** záhlaví se zástupným znakem, který umožňuje všechny původy:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Pro komplexní požadavky:

Komplexní požadavek není požadavek CORS, kde prohlížeče je vyžadován pro odeslání *předběžný požadavek* (to znamená, předběžný test) před odesláním aktuální požadavek CORS. Předběžný požadavek žádá oprávnění serveru, pokud žádost o původní CORS můžete pokračovat a je `OPTIONS` požadavek na stejnou adresu URL.

> [!TIP]
> Podrobné informace o tocích CORS a běžné nástrahy zobrazení [Průvodce CORS pro rozhraní REST API](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Zástupný znak nebo jeden počátek scénáře
CORS v Azure CDN bude fungovat automaticky bez dodatečné konfigurace při **Access-Control-Allow-Origin** záhlaví je nastaven na zástupný znak (*) nebo jeden počátek.  CDN se první odpověď do mezipaměti a následné žádosti budou používat stejné záhlaví.

Pokud již byly provedeny požadavky do sítě CDN před CORS nastavena na váš původní název, budete muset Vyprázdnit obsah na vašem obsahu koncový bod znovu načte obsah s **Access-Control-Allow-Origin** záhlaví.

## <a name="multiple-origin-scenarios"></a>Scénářích s více zdroji
Pokud je potřeba povolit konkrétní seznam zdrojů povolit CORS, získejte věcí o něco složitější. Tento problém nastane, pokud síť CDN ukládá do mezipaměti **Access-Control-Allow-Origin** záhlaví pro první zdroj CORS.  Jiný zdroj CORS provádí další požadavek, bude použit v mezipaměti CDN **Access-Control-Allow-Origin** záhlaví, které se nebudou shodovat.  Chcete-li to několika způsoby.

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium od Verizonu
Nejlepší způsob, jak tuto možnost povolte, je použití **Azure CDN Premium od Verizonu**, která zveřejní některé rozšířené funkce. 

Budete muset [vytvořit pravidlo](cdn-rules-engine.md) ke kontrole **původu** hlavičce požadavku.  Pokud jde o platný původu, nastavíte pravidlo **Access-Control-Allow-Origin** záhlaví s původem zadaný v požadavku.  Pokud zadaný původ v **původu** záhlaví není povolený, by měly vynechat pravidla **Access-Control-Allow-Origin** hlavičky, která způsobí, že prohlížeč, aby zamítnutí žádosti. 

Existují dva způsoby, jak to udělat pomocí stroj pravidel. V obou případech platí **Access-Control-Allow-Origin** záhlaví ze zdrojového serveru souboru je ignorován a provádí kompletní správu povolené zdroje CORS CDN stroj pravidel.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jedna regulárního výrazu s všechny platné zdroje
V tomto případě vytvoříte regulární výraz, který obsahuje všechny zdroje, které chcete povolit: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN Premium od Verizonu** používá [kompatibilní regulární výrazy jazyka Perl](https://pcre.org/) jako jeho modul regulárních výrazů.  Můžete použít nástroje, jako je [regulární výrazy 101](https://regex101.com/) ověřit regulární výraz.  Mějte na paměti, že je platný v regulárních výrazech znak "/" a nemusí být uvozeny řídicími znaky, ale uvozovací znak je považován za osvědčený postup a datacommand validátory některé regulární výraz.
> 
> 

Pokud se shoduje s regulárním výrazem, dojde k nahrazení pravidlo **Access-Control-Allow-Origin** záhlaví (pokud existuje) ze zdroje s původu, který požadavek odeslal.  Můžete také přidat další hlavičky CORS, jako například **přístup – ovládací prvek-Allow-Methods**.

![Příklad pravidla s regulárním výrazem](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Žádost o pravidlo záhlaví pro každý původu.
Místo regulárních výrazů, můžete místo toho vytvořit samostatné pravidlo pro každý původ chcete povolit používání **zástupné záhlaví požadavku** [odpovídají podmínce](/previous-versions/azure/mt757336(v=azure.100)#Anchor_1). Stejně jako u metody regulárních výrazů stroj pravidel samostatně nastaví hlavičky CORS. 

![Příklad pravidla bez regulárního výrazu](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> V příkladu výše, použijte zástupný znak * říká stroj pravidel tak, aby odpovídaly HTTP i HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Azure CDN standard profily
Azure CDN standard profilů (**Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai**, a **Azure CDN Standard od Verizonu**), pouze mechanismus pro Povolit pro více míst původu bez použití původu zástupných znaků se má používat [řetězce dotazu do mezipaměti](cdn-query-string.md). Povolit řetězec dotazu nastavení pro koncový bod CDN a potom použijte řetězec dotazu jedinečný pro žádosti od každé povolené domény. To způsobí ukládání do mezipaměti samostatný objekt pro každý řetězec dotazu jedinečný CDN. Tento přístup však není ideální, jinak dojde ve více kopií stejné souboru do mezipaměti v CDN.  

