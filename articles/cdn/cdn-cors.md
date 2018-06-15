---
title: Pomocí Azure CDN CORS | Microsoft Docs
description: Další informace o použití Azure Content Delivery Network (CDN) k s sdílení prostředků různých původů (CORS).
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
ms.openlocfilehash: f9429e88525e27c0b6bad29d1927d53d05dfbcc8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765360"
---
# <a name="using-azure-cdn-with-cors"></a>Azure CDN pomocí CORS
## <a name="what-is-cors"></a>Co je CORS?
CORS (mezi sdílení prostředků původu) je funkce protokolu HTTP, která umožňuje webové aplikace spuštěna v rámci jednoho domény přístup k prostředkům v jiné doméně. Chcete-li omezit možnost útoky skriptování mezi weby, všechny moderní prohlížeče implementovat omezení zabezpečení, označuje jako [stejného původu zásad](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Zabrání se tak na webové stránce z volání rozhraní API v jiné doméně.  CORS poskytuje zabezpečení způsob, jak povolit jeden počátek (doménu původu) k volání rozhraní API v jiný počátek.

## <a name="how-it-works"></a>Jak to funguje
Existují dva typy požadavků CORS *jednoduchých požadavků* a *komplexní požadavky.*

### <a name="for-simple-requests"></a>Pro jednoduché požadavky:

1. Prohlížeč odešle požadavek CORS s další **původu** hlavičku požadavku HTTP. Hodnotu této hlavičky je původ, který obsluhuje nadřazená stránka, která je definována jako kombinace *protokol,* *domény,* a *portu.*  Pokud na stránce z https://www.contoso.com pokusy o přístup k datům uživatele v fabrikam.com počátek, následující hlavičku požadavku, které mají být odeslány na fabrikam.com:

   `Origin: https://www.contoso.com`

2. Server odpoví některé z následujících:

   * **Access-Control-Allow-Origin** hlavičky v odpovědi označující původ lokality, která je povolena. Příklad:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kód chyby HTTP například 403, není-li server žádost cross-origin po zkontrolování hlavičku zdroje

   * **Access-Control-Allow-Origin** záhlaví se zástupnými znaky, které umožňuje všechny původy:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Pro komplexní požadavky:

Žádost o komplexní je požadavek CORS, kterých se v prohlížeči vyžaduje k odeslání *předběžný požadavek* (tedy předběžné kontroly) před odesláním aktuální požadavek CORS. Předběžný požadavek požádá oprávnění k serveru, pokud žádost původní CORS můžete pokračovat a je `OPTIONS` požadavek na stejnou adresu URL.

> [!TIP]
> Další informace o CORS toky a běžné nástrahy, podívejte se [Průvodce CORS pro rozhraní REST API](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Zástupný znak nebo jeden počátek scénáře
CORS v Azure CDN budou automaticky fungovat s žádná další konfigurace při **Access-Control-Allow-Origin** záhlaví je nastaven na zástupný znak (*) nebo jeden počátek.  CDN se první odpověď do mezipaměti a následné žádosti bude používat stejné záhlaví.

Pokud již byly provedeny požadavky CDN před CORS se nastavuje na do zdrojového umístění, budete muset vymazat obsah na koncový bod obsah znovu načíst obsah s **Access-Control-Allow-Origin** záhlaví.

## <a name="multiple-origin-scenarios"></a>Více scénářů počátek
Pokud je potřeba povolit konkrétní seznam původů smí pro CORS, získat věcí trochu složitější. Tento problém nastane, když CDN ukládá do mezipaměti **Access-Control-Allow-Origin** záhlaví pro první zdroj CORS.  Pokud jiný zdroj CORS provede následného požadavku, bude sloužit CDN uložená v mezipaměti **Access-Control-Allow-Origin** hlavičky, která nebude odpovídat.  Napravíte to tím několika způsoby.

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium od Verizonu
Nejlepší způsob, jak povolit, je použití **Azure CDN Premium od společnosti Verizon**, který zpřístupňuje některé rozšířené funkce. 

Budete muset [vytvořit pravidlo](cdn-rules-engine.md) zkontrolujte **původu** hlavičky v požadavku.  Pokud je platný původu, pravidla nastaví **Access-Control-Allow-Origin** hlavička s počátek zadaný v požadavku.  Pokud počátek zadané v **původu** hlavičky není povolena, by měl vynechejte pravidla **Access-Control-Allow-Origin** hlavičky, která způsobí, že prohlížeč tak, aby zamítal žádosti. 

Existují dva způsoby, jak to provést pomocí stroj pravidel. V obou případech **Access-Control-Allow-Origin** hlavička ze souboru zdrojový server je ignorován a od CDN pravidla modul provádí kompletní správu povolené zdroje CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jeden regulární výraz s všechny původy platný
V tomto případě vytvoříte regulární výraz, který obsahuje všechny zdroje, které chcete povolit: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN Premium od společnosti Verizon** používá [Perl kompatibilní regulární výrazy](http://pcre.org/) jako jeho modul pro regulární výrazy.  Můžete použít nástroje, jako je [regulární výrazy 101](https://regex101.com/) ověření regulárního výrazu.  Všimněte si, že znak "/" je platná v regulárních výrazech a nemusí být uvozené však uvozovací znaky tento znak se považuje za osvědčený postup a očekává se některé validátory regulární výraz.
> 
> 

Pokud odpovídá regulárnímu výrazu, dojde k nahrazení pravidla **Access-Control-Allow-Origin** záhlaví (pokud existuje) z tohoto počátku s původ, který požadavek odeslal.  Můžete také přidat další hlavičky CORS, jako například **přístup – ovládací prvek-Allow-Methods**.

![Příklad pravidla s regulárním výrazem](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Žádost o pravidlo záhlaví pro každý počátek.
Místo regulární výrazy, můžete místo toho vytvořit samostatné pravidlo pro každý původ chcete povolit používání **zástupné hlavičky požadavku** [vyhovují podmínce](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Stejně jako v případě metody regulární výraz modul pravidel samostatně nastaví hlavičky CORS. 

![Příklad pravidla bez regulární výraz](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> V příkladu výše, použít zástupný znak * informuje stroj pravidel tak, aby odpovídaly HTTP a HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Azure CDN standard profily
U profilů Azure CDN standard (**Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Akamai**, a **Azure CDN Standard od společnosti Verizon**), pouze mechanismus Povolit pro více zdroje bez použití původu zástupných znaků se má používat [řetězce dotazu do mezipaměti](cdn-query-string.md). Povolit řetězec dotazu nastavení pro koncový bod CDN a potom pomocí řetězce dotazu jedinečné požadavky z každé povolené domény. Tak bude mít za následek CDN ukládání do mezipaměti samostatný objekt pro každý jedinečný dotazu řetězec. Tento postup však není ideální, protože výsledkem bude více kopií stejného souboru do mezipaměti na CDN.  

