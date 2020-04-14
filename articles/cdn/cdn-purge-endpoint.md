---
title: Vyčištění koncového bodu Azure CDN | Dokumenty společnosti Microsoft
description: Zjistěte, jak vymazat veškerý obsah uložený v mezipaměti z koncového bodu Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: ebbb0dd059ce2bcf4a3bc260ed6d426d5be09dfe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260254"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Vymazání koncového bodu Azure CDN
## <a name="overview"></a>Přehled
Azure CDN hraniční uzly bude mezipaměti prostředky, dokud vyprší platnost datového zdroje (TTL) .  Po vypršení platnosti ttl prostředku, když klient požaduje prostředek z hraničního uzlu, hraniční uzel načte novou aktualizovanou kopii datového zdroje, aby sloužil požadavku klienta a ukládal aktualizaci mezipaměti.

Osvědčeným postupem, jak zajistit, aby uživatelé vždy získali nejnovější kopii vašich datových zdrojů, je verze vašich datových zdrojů pro každou aktualizaci a jejich publikování jako nových adres URL.  CDN okamžitě načte nové prostředky pro další požadavky klienta.  Někdy můžete chtít vyčistit obsah uložený v mezipaměti ze všech hraničních uzlů a vynutit je, aby načítaly nové aktualizované datové zdroje.  To může být způsobeno aktualizacemi webové aplikace nebo rychlou aktualizací prostředků obsahujících nesprávné informace.

> [!TIP]
> Všimněte si, že vymazání pouze vymaže obsah uložený v mezipaměti na okrajových serverech CDN.  Všechny následné mezipaměti, například proxy servery a místní mezipaměti mezipaměti, mohou stále obsahovat kopii souboru uloženou v mezipaměti.  Je důležité si to pamatovat při nastavení doby přenosu souboru.  Navazující klient můžete vynutit vyžádání nejnovější verze souboru tak, že mu při každé aktualizaci přiloží jedinečný název nebo využijete [ukládání řetězce dotazu do mezipaměti](cdn-query-string.md).  
> 
> 

Tento kurz vás provede vymazáním prostředků ze všech hraničních uzlů koncového bodu.

## <a name="walkthrough"></a>Názorný postup
1. Na [webu Azure Portal](https://portal.azure.com)přejděte na profil CDN obsahující koncový bod, který chcete vyčistit.
2. V okně profilu CDN klepněte na tlačítko pročistit.
   
    ![CdN profilový nůž](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Otevře se proplachovací čepel.
   
    ![Proplachovací nůž CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. V okně Vyčistit vyberte adresu služby, kterou chcete vymazat z rozevíracího seznamu adres URL.
   
    ![Formulář Pročistit](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > K noži pročistit se můžete také dostat klepnutím na tlačítko **Vyčistit** na okně koncového bodu CDN.  V takovém případě bude pole **adresy URL** předem vyplněno adresou služby tohoto konkrétního koncového bodu.
   > 
   > 
4. Vyberte, jaké prostředky chcete vymazat z hraničních uzlů.  Chcete-li vymazat všechny datové zdroje, zaškrtněte políčko **Vymazat všechny.**  V opačném případě zadejte cestu ke každému datovému zdroji, který chcete vymazat, do textového pole **Cesta.** Níže uvedené formáty jsou podporovány v cestě.
    1. **Vymazání jedné adresy URL**: Vymazání jednotlivých datových zdrojů zadáním úplné`/pictures/strasbourg.png`adresy URL s příponou souboru nebo bez ní, např.`/pictures/strasbourg`
    2. **Čistka se zástupnými symboly**: Hvězdička (\*) může být použita jako zástupný znak. Vymažte všechny složky, podsložky a soubory pod koncovým bodem v `/*` cestě nebo vyčistěte `/*`všechny podsložky`/pictures/*`a soubory pod určitou složkou zadáním složky následované například .  Všimněte si, že vyprázdnění zástupných symbolů není podporováno Azure CDN z Akamai aktuálně. 
    3. **Vyčištění kořenové domény**: Vyčistěte kořenový adresář koncového bodu s "/" v cestě.
   
   > [!TIP]
   > Cesty musí být určeny pro vymazání a musí být relativní adresou URL, která odpovídá následujícímu [regulárnímu výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Vyčistit všechny** a **vyprázdnění zástupných symbolů** není podporováno **Azure CDN z Akamai** v současné době.
   > > Vymazání jedné adresy URL`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Řetězec dotazu`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Očištění `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`se zástupnými symboly . 
   > 
   > Po zadání textu se zobrazí textová pole **Další cesta,** která vám umožní sestavit seznam více datových zdrojů.  Datové zdroje ze seznamu můžete odstranit kliknutím na tlačítko tři tečky (...).
   > 
5. Klepněte na tlačítko **Vyčistit.**
   
    ![Tlačítko Vyčistit](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Čištění požadavků trvat přibližně 10 minut ke zpracování s **Azure CDN od Microsoftu**, přibližně 2 minuty s **Azure CDN od Verizon** (standardní a premium) a přibližně 10 sekund s Azure **CDN z Akamai**.  Azure CDN má limit 50 souběžných žádostí o vymazání v daném okamžiku na úrovni profilu. 
> 
> 

## <a name="see-also"></a>Viz také
* [Předběžné načtení prostředků v koncovém bodu Azure CDN](cdn-preload-endpoint.md)
* [Odkaz na rozhraní API Azure CDN REST – vyprázdnění nebo předběžné načtení koncového bodu](/rest/api/cdn/endpoints)

