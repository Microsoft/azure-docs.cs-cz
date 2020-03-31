---
title: Optimalizace streamování médií pomocí Azure CDN
description: Optimalizace datových proudů mediálních souborů pro hladké doručení
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: c6ed546735058e330368151adb0df7323f943050
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593656"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optimalizace streamování médií pomocí Azure CDN 
 
Používání videa s vysokým rozlišením se zvyšuje na internetu, což vytváří potíže pro efektivní doručování velkých souborů. Zákazníci očekávají plynulé přehrávání videa na vyžádání nebo živé video datové zdroje v různých sítích a klientech po celém světě. Rychlý a efektivní mechanismus doručování souborů datových proudů médií je zásadní pro zajištění plynulého a příjemného zážitku pro spotřebitele.  

Živé vysílání médií je obzvláště obtížné dodat z důvodu velkých velikostí a počtu souběžných prohlížečů. Dlouhá zpoždění způsobují, že uživatelé odejdou. Vzhledem k tomu, že živé přenosy nelze předem uložit do mezipaměti a velké latence nejsou pro diváky přijatelné, fragmenty videa musí být doručeny včas. 

Vzorce požadavků streamování také poskytují některé nové výzvy. Když je populární živý přenos nebo nová série vydána pro video na vyžádání, tisíce až miliony diváků mohou požádat o datový proud současně. V tomto případě je inteligentní konsolidace požadavků nezbytná pro to, aby nedošlo k zahlcení původních serverů, když datové zdroje ještě nejsou uloženy do mezipaměti.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimalizace streamování médií pro Azure CDN od Microsoftu

**Azure CDN Standard od koncových** bodů Microsoftu doručují datové prostředky datových proudů médií přímo pomocí obecného typu optimalizace webového doručování. 

Optimalizace streamování médií pro **Azure CDN Standard od Microsoftu** je efektivní pro živé nebo video na vyžádání streamovaná média, která používají jednotlivé fragmenty médií pro doručování. Tento proces se liší od jednoho velkého majetku převedeného prostřednictvím postupného stahování nebo pomocí požadavků na rozsah bajtů. Informace o tomto stylu doručování médií najdete v [tématu optimalizace stahování velkých souborů pomocí Azure CDN](cdn-large-file-optimization.md).

Obecné doručování médií nebo typy optimalizace doručování médií na vyžádání používají Azure Content Delivery Network (CDN) s back-endovými optimalizacemi pro rychlejší doručování mediálních prostředků. Používají také konfigurace pro mediální prostředky na základě osvědčených postupů získaných v průběhu času.

### <a name="partial-cache-sharing"></a>Částečné sdílení mezipaměti
Částečné sdílení mezipaměti umožňuje síti CDN poskytovat částečně uložený obsah částečně uložený v mezipaměti novým požadavkům. Například pokud první požadavek na CDN výsledky v mezipaměti chybět, požadavek je odeslán do původu. Přestože je tento neúplný obsah načten do mezipaměti CDN, ostatní požadavky na CDN mohou začít získat tato data. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimalizace streamování médií pro Azure CDN od verizonu

**Azure CDN Standard od Verizonu** a **Azure CDN Premium od koncových** bodů Verizonu poskytují datové zdroje datových proudů datových proudů prostředků přímo pomocí obecného typu optimalizace webového doručování. Několik funkcí na CDN přímo pomoci při poskytování mediálních prostředků ve výchozím nastavení.

### <a name="partial-cache-sharing"></a>Částečné sdílení mezipaměti

Částečné sdílení mezipaměti umožňuje síti CDN poskytovat částečně uložený obsah částečně uložený v mezipaměti novým požadavkům. Například pokud první požadavek na CDN výsledky v mezipaměti chybět, požadavek je odeslán do původu. Přestože je tento neúplný obsah načten do mezipaměti CDN, ostatní požadavky na CDN mohou začít získat tato data. 

### <a name="cache-fill-wait-time"></a>Doba čekání na plnění mezipaměti

 Funkce čekací doby vyplňování mezipaměti nutí hraniční server podržet všechny následné požadavky na stejný prostředek, dokud nebudou z původního serveru doručeny hlavičky odpovědí HTTP. Pokud hlavičky odpovědi HTTP z počátku dorazí před vypršením časovače, všechny požadavky, které byly pozastaveny jsou doručeny mimo rostoucí mezipaměti. Současně je mezipaměť vyplněna daty z počátku. Ve výchozím nastavení je doba čekání na plnění mezipaměti nastavena na 3 000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimalizace streamování médií pro Azure CDN z Akamai
 
**Azure CDN Standard od Akamai** nabízí funkci, která efektivně poskytuje datové zdroje datových proudů datových proudů uživatelům po celém světě ve velkém měřítku. Tato funkce snižuje latence, protože snižuje zatížení na původní servery. Tato funkce je k dispozici se standardní cenovou úrovní Akamai. 

Optimalizace streamování médií pro **Azure CDN Standard od Akamai** je efektivní pro živé nebo video-on-demand streamování médií, která používá jednotlivé fragmenty médií pro doručení. Tento proces se liší od jednoho velkého majetku převedeného prostřednictvím postupného stahování nebo pomocí požadavků na rozsah bajtů. Informace o tomto stylu doručování médií naleznete v tématu [Optimalizace velkých souborů](cdn-large-file-optimization.md).

Obecné typy pro doručování médií nebo optimalizace doručování videa na vyžádání používají cdn s back-endovými optimalizacemi k rychlejšímu doručování mediálních prostředků. Používají také konfigurace pro mediální prostředky na základě osvědčených postupů získaných v průběhu času.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurace koncového bodu Akamai CDN pro optimalizaci streamování médií
 
Můžete nakonfigurovat koncový bod sítě pro doručování obsahu (CDN) pro optimalizaci doručování pro velké soubory prostřednictvím portálu Azure. Můžete také použít rest API nebo některý z klientských sad SDK k tomu. Následující kroky ukazují proces prostřednictvím portálu Azure pro Azure CDN Standard z profilu **Akamai:**

1. Pokud chcete přidat nový koncový bod, vyberte na stránce profilu Akamai **CDN** možnost **Koncový bod**.
  
    ![Nový koncový bod](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. V rozevíracím seznamu **Optimalizace pro** vyberte **streamování médií Videa na vyžádání** pro datové zdroje videa na vyžádání. Pokud děláte kombinaci živého vysílání a streamování videa na vyžádání, vyberte **možnost Obecné streamování médií**.

    ![Vybrané streamování](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po vytvoření koncového bodu použije optimalizaci pro všechny soubory, které odpovídají určitým kritériím. Následující část popisuje tento proces. 

### <a name="caching"></a>Ukládání do mezipaměti

Pokud **Azure CDN Standard od Akamai** zjistí, že datový zdroj je manifest streamování nebo fragment, používá různé doby vypršení platnosti ukládání do mezipaměti z obecného poskytování webu. (Viz úplný seznam v následující tabulce.) Jako vždy jsou dodrženy hlavičky cache-control nebo Expires odeslané z počátku. Pokud datový zdroj není datový mprostředek, ukládá do mezipaměti pomocí doby vypršení platnosti pro obecné doručování webu.

Krátká doba negativního ukládání do mezipaměti je užitečná pro počáteční zátěž, když mnoho uživatelů požaduje fragment, který ještě neexistuje. Příkladem je živý datový proud, kde pakety nejsou k dispozici od počátku, že druhý. Delší interval ukládání do mezipaměti také pomáhá snížit zatížení požadavků z počátku, protože video obsah není obvykle změněn.
 

|   | Obecné doručování webových stránek | Obecné vysílání datových proudů médií | Streamování médií videa na vyžádání  
--- | --- | --- | ---
Ukládání do mezipaměti: Pozitivní <br> HTTP 200, 203, 300, <br> 301, 302 a 410 | 7 dní |365 dní | 365 dní   
Ukládání do mezipaměti: Negativní <br> HTTP 204, 305, 404, <br> a 405 | Žádný | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Řešení selhání původu  

Obecné doručování médií a doručování médií na vyžádání mají také časové osátosti původu a protokol opakování na základě osvědčených postupů pro typické vzory požadavků. Například protože obecné doručování médií je pro živé a video-on-demand doručování médií, používá kratší časový limit připojení vzhledem k časově citlivé povaze živého vysílání.

Při výpadku časového limitu připojení CDN opakuje několikrát před odesláním chyby "504 - Gateway Timeout" klientovi. 

Pokud soubor odpovídá seznamu podmínek typu souboru a velikosti, cdn používá chování pro vysílání datových proudů médií. V opačném případě používá obecné doručování webových stránek.
   
### <a name="conditions-for-media-streaming-optimization"></a>Podmínky pro optimalizaci streamování médií 

V následující tabulce je uvedena sada kritérií, která mají být splněna pro optimalizaci streamování médií: 
 
Podporované typy datových proudů | Přípony souborů  
--- | ---  
Jablko HLS | m3u8, m3u, m3ub, klíč, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Seg-Frag STRUKTURA URL <br> (odpovídající regulární výraz: ^(/.*)Seq(\d+)-Frag(\d+)
Pomlčka | mpd, pomlčka, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Plynulé streamování | /manifest/, /QualityLevels/Fragments/
  
 
