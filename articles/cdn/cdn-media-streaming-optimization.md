---
title: Optimalizace se Azure CDN streamování médií
description: Optimalizovat datové proudy mediálních souborů pro smooth doručení
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: v-deasim
ms.openlocfilehash: 8a2b69aaa601e1d00152f57841a4d67f98680181
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766168"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optimalizace se Azure CDN streamování médií 
 
Použití vysokým rozlišením video roste na Internetu, které vytvoří problémy pro efektivní doručování velkých souborů. Zákazníci očekávat plynulé přehrávání videa na vyžádání nebo live video prostředků v různých sítí a klienti po celém světě. Doručení rychlé a efektivní mechanismus pro streamování soubory médií je velmi důležité, aby bylo prostředí hladký a zábavná příjemce.  

Živé streamování médií je obzvláště složité doručit z důvodu velkého velikosti a počtu souběžných prohlížeče. Velká zpoždění, že uživatelům ponechat. Protože živé datové proudy nelze uložit do mezipaměti dopředu a velké latence nejsou přijatelné pro prohlížeče, videa fragmenty musí doručit včas. 

Vzory požadavku streamování také poskytují další výzvy. Po vydání oblíbených živý datový proud nebo nové řady pro video na vyžádání, tisíc na miliony prohlížeče si mohou vyžádat datového proudu ve stejnou dobu. V takovém případě je důležité při prostředky neukládají do mezipaměti ještě není zahlcovat servery počátek konsolidace inteligentní žádostí.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimalizace pro Azure CDN společnosti Microsoft streamování médií

**Azure CDN Standard od společnosti Microsoft** koncové body doručování streamování médií prostředky přímo pomocí typu obecné webové doručení optimalizace. 

Optimalizace pro streamování médií **Azure CDN Standard od společnosti Microsoft** je platná pro živé streamování na vyžádání video-on-demand média, které používá fragmenty jednotlivých média pro doručení nebo. Tento proces se liší od jednoho velkého asset přenést přes progresivní stahování nebo pomocí žádosti o rozsah bajtů. Informace v tomto styl doručování médií najdete v tématu [optimalizace stahování velkých souborů pomocí Azure CDN](cdn-large-file-optimization.md).

Obecné doručení nebo na vyžádání video-on-demand média doručení optimalizace typy médií rychlejší dostat média prostředků pomocí Azure Content Delivery Network (CDN) s back-end optimalizace. Také používají konfigurace pro média prostředky na základě osvědčených postupů se naučili v čase.

### <a name="partial-cache-sharing"></a>Sdílení částečné mezipaměti
Sdílení částečné mezipaměti umožňuje CDN k obsluze obsahu částečně v mezipaměti na nové požadavky. Například pokud první požadavek na CDN výsledkem k neúspěšnému přístupu do mezipaměti, je žádost odeslána k počátku. I když tento obsah neúplné je načten do mezipaměti CDN, můžete začít ostatních požadavků na CDN získávání tato data. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimalizace pro Azure CDN společnosti Verizon streamování médií

**Azure CDN Standard od společnosti Verizon** a **Azure CDN Premium od společnosti Verizon** koncové body doručování streamování médií prostředky přímo pomocí typu obecné webové doručení optimalizace. Několik funkcí v CDN přímo pomáhají doručovat média prostředky ve výchozím nastavení.

### <a name="partial-cache-sharing"></a>Sdílení částečné mezipaměti

Sdílení částečné mezipaměti umožňuje CDN k obsluze obsahu částečně v mezipaměti na nové požadavky. Například pokud první požadavek na CDN výsledkem k neúspěšnému přístupu do mezipaměti, je žádost odeslána k počátku. I když tento obsah neúplné je načten do mezipaměti CDN, můžete začít ostatních požadavků na CDN získávání tato data. 

### <a name="cache-fill-wait-time"></a>Doba čekání výplně mezipaměti

 Funkce čas čekání výplně mezipaměti vynutí hraničního serveru k uchování všechny následné žádosti pro stejný prostředek, dokud hlavičky HTTP odpovědi přicházejí ze zdrojového serveru. Pokud hlavičky HTTP odpovědi z tohoto počátku přicházejí před vypršením platnosti časovač, jsou z mezipaměti rostoucí zpracovat všechny požadavky, které byly blokovat. Ve stejnou dobu mezipaměti vyplní dat z tohoto počátku. Ve výchozím nastavení Doba čekání výplně mezipaměti na 3 000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimalizace pro Azure CDN společnosti Akamai streamování médií
 
**Azure CDN Standard od Akamai** nabízí funkce, která poskytuje prostředky ke streamování médií efektivně uživatelům po celém světě ve velkém měřítku. Funkci snižuje latenci, protože snižuje zatížení serverů původu. Tato funkce je k dispozici s Akamai standard cenová úroveň. 

Optimalizace pro streamování médií **Azure CDN Standard od společnosti Akamai** je platná pro živé streamování na vyžádání video-on-demand média, které používá fragmenty jednotlivých média pro doručení nebo. Tento proces se liší od jednoho velkého asset přenést přes progresivní stahování nebo pomocí žádosti o rozsah bajtů. Informace v tomto styl doručování médií najdete v tématu [velkých souborů optimalizace](cdn-large-file-optimization.md).

Obecné doručení nebo na vyžádání video-on-demand média doručení optimalizace typy médií používají název CDN s back-end optimalizace pro doručování média prostředky rychlejší. Také používají konfigurace pro média prostředky na základě osvědčených postupů se naučili v čase.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurace koncového bodu Akamai CDN za účelem optimalizace streamování médií
 
Můžete nakonfigurovat váš koncový bod sítě (CDN) doručování obsahu za účelem optimalizace doručování velkých souborů prostřednictvím portálu Azure. K tomu můžete také použít rozhraní REST API nebo některou z klienta sady SDK. Následující kroky ukazují proces prostřednictvím portálu Azure **Azure CDN Standard od společnosti Akamai** profil:

1. Chcete-li přidat nový koncový bod na Akamai **profil CDN** vyberte **koncový bod**.
  
    ![Nový koncový bod](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. V **optimalizované pro** rozevíracího seznamu vyberte **videa na vyžádání streamování médií** pro vyžádání video-on-demand prostředky. Pokud tak učiníte, kombinace za provozu a na vyžádání video-on-demand streamování, vyberte **datové proudy obecné media**.

    ![Streamování vybrané](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po vytvoření koncového bodu, bude se vztahovat optimalizace pro všechny soubory, které splňují určitá kritéria. Následující část popisuje tento proces. 

### <a name="caching"></a>Ukládání do mezipaměti

Pokud **Azure CDN Standard od společnosti Akamai** zjistí, že je asset streamování manifest nebo fragment, používá ukládání do mezipaměti vypršení platnosti odděleně od obecné webové doručení. (Viz úplný seznam v následující tabulce.) Jako vždy jsou dodržení cache-control nebo Expires hlavičky odeslaných z tohoto počátku. Pokud prostředku není asset média, se ukládá do mezipaměti pomocí dobu vypršení platnosti pro obecné webové přenosy.

Krátký záporný čas ukládání do mezipaměti je užitečné pro přesměrování zpracování počátku, když mnoho uživatelů požaduje fragment, která ještě neexistuje. Příkladem je živý datový proud, kde pakety nejsou k dispozici z tohoto počátku této sekundu. Již ukládání do mezipaměti interval také pomáhá přesměrovat zpracování požadavky z tohoto počátku, protože obsahu videa se obvykle nemění.
 

|   | Obecné doručování webu | Streamování obecných médií | Streamování videa na přání médií  
--- | --- | --- | ---
Ukládání do mezipaměti: kladné <br> HTTP 200, 203, 300, <br> 301, 302 a 410 | 7 dní |365 dnů | 365 dnů   
Ukládání do mezipaměti: záporná <br> HTTP 204, 305, 404, <br> a 405 | Žádný | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Řešení s chybou počátek  

Obecné média doručení a doručování média na vyžádání video-on-demand také mají původ vypršení časových limitů a protokolu opakování podle osvědčené postupy pro vzory typické požadavku. Protože je doručení obecné média pro za provozu a doručování média na vyžádání video-on-demand, používá kratší časový limit připojení vzhledem k povaze dobou živé například streamování.

Pokud vyprší časový limit připojení, CDN opakuje několikrát, než odešle "504 – časový limit brány" Chyba do klienta. 

Když soubor odpovídá seznamu podmínky typ a velikost souborů, používá CDN chování pro streamování médií. Jinak použije obecné webové doručení.
   
### <a name="conditions-for-media-streaming-optimization"></a>Podmínky pro optimalizaci streamování médií 

Následující tabulka uvádí sadu kritérií, které je třeba splnit pro streamování optimalizace médií: 
 
Podporované typy datových proudů | Přípony souborů  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, klíč, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Adresa URL KŘEH seg – struktura <br> (odpovídající regulární výraz: ^(/.*)Seq(\d+)-Frag(\d+)
POMLČKA | MPD, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, WBEM, mp4a, m4a, isma
Technologie Smooth streaming | / manifest /, /QualityLevels/fragmenty /
  
 
