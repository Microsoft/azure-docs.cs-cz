---
title: S Azure CDN optimalizace streamování médií
description: Optimalizace streamování médií soubory přenášel
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9802296170f07bb8599058e230798f647e900d4d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093693"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>S Azure CDN optimalizace streamování médií 
 
Použití video s vysokým rozlišením roste na Internetu, která vytvoří potíže pro efektivní doručování velkých souborů. Zákazníci očekávají hladké přehrávání videa na vyžádání nebo živé video-prostředků v různých sítích a klienti po celém světě. Rychlé a efektivní doručování mechanismus pro streamování souborů médií je důležité zajistit hladký a dovoluje uživatelské prostředí.  

Živé streamování médií je obzvláště složité je doručit z důvodu velké velikosti a počtu souběžných prohlížeče. Uživatelům ponechte způsobit prodlevy. Protože živé streamy nejde udržovat v mezipaměti předem domluvili a velké latenci nejsou přijatelné pro prohlížeče, musí doručit videa fragmenty včas. 

Vzory požadavku datových proudů také poskytují další výzvy. Po oblíbených živý stream nebo nová řada vydání pro video na vyžádání, může vyžádat tisíců miliónům diváků datového proudu ve stejnou dobu. V takovém případě je důležité při prostředky neukládají do mezipaměti ještě není zahlcovat původních serverů konsolidace inteligentní žádostí.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimalizace pro Azure CDN od společnosti Microsoft streamování médií

**Azure CDN Standard od společnosti Microsoft** koncové body doručovat datové proudy mediálních materiálů přímo pomocí typu optimalizace doručení obecné webu. 

Optimalizace streamování médií **Azure CDN Standard od společnosti Microsoft** platí pro za provozu nebo vod (Video-on-demand) streamování médií, které používá media jednotlivé fragmenty pro doručení. Tento proces se liší od jednoho velkého asset přenést přes progresivní stahování nebo požadavky na zjištění rozsahu bajtů. Informace o stylu doručování médií, naleznete v tématu [optimalizace stahování velkých souborů pomocí Azure CDN](cdn-large-file-optimization.md).

Obecné doručování nebo vod (Video-on-demand) média doručování optimalizace typy médií pomocí Azure Content Delivery Network (CDN) back-end optimalizace zajistit rychlejší dodání mediálních materiálů. Používá taky konfigurace pro mediální aktiva podle doporučené postupy osvojené v čase.

### <a name="partial-cache-sharing"></a>Sdílení částečné mezipaměti
Sdílení částečné mezipaměti umožňuje síť CDN pro obsluhu částečně uložené v mezipaměti obsah pro nové žádosti. Například pokud je první požadavek do CDN výsledkem neúspěšnému přístupu do mezipaměti, je žádost odeslána k původu. I když tato neúplný obsah je načten do mezipaměti CDN, můžete začít získání těchto dat jiné požadavky na síť CDN. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimalizace pro Azure CDN od Verizonu streamování médií

**Azure CDN Standard od Verizonu** a **Azure CDN Premium od Verizonu** koncové body doručovat datové proudy mediálních materiálů přímo pomocí typu optimalizace doručení obecné webu. Několik funkcí ve službě CDN přímo pomáhají doručovat mediálních materiálů ve výchozím nastavení.

### <a name="partial-cache-sharing"></a>Sdílení částečné mezipaměti

Sdílení částečné mezipaměti umožňuje síť CDN pro obsluhu částečně uložené v mezipaměti obsah pro nové žádosti. Například pokud je první požadavek do CDN výsledkem neúspěšnému přístupu do mezipaměti, je žádost odeslána k původu. I když tato neúplný obsah je načten do mezipaměti CDN, můžete začít získání těchto dat jiné požadavky na síť CDN. 

### <a name="cache-fill-wait-time"></a>Doba čekání výplně mezipaměti

 Funkce mezipaměti výplně čekací čas vynutí hraničního serveru pro uchování pozdějšími požadavky pro jeden prostředek, až dorazí hlavičky odpovědí HTTP ze zdrojového serveru. Pokud hlavičky HTTP odpovědi z původního zdroje doručení předtím, než čas vyprší, všechny požadavky, které byly pozastavit se obsluhují z rostoucí mezipaměti. Ve stejnou dobu mezipaměť naplní daty z původního zdroje. Ve výchozím nastavení Doba čekání výplně mezipaměti nastavená na 3 000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimalizace pro Azure CDN od Akamai streamování médií
 
**Azure CDN Standard od Akamai** nabízí funkce, která poskytuje datové proudy mediálních materiálů efektivně uživatelům po celém světě ve velkém měřítku. Funkci snižuje latenci, protože snižuje zatížení původních serverů. Tato funkce je k dispozici s Akamai standard cenovou úroveň. 

Optimalizace streamování médií **Azure CDN Standard od Akamai** platí pro za provozu nebo vod (Video-on-demand) streamování médií, které používá media jednotlivé fragmenty pro doručení. Tento proces se liší od jednoho velkého asset přenést přes progresivní stahování nebo požadavky na zjištění rozsahu bajtů. Informace o stylu doručování médií, naleznete v tématu [optimalizace velkých souborů](cdn-large-file-optimization.md).

Obecné doručování nebo vod (Video-on-demand) média doručování optimalizace typy médií používají název CDN s optimalizací back-end na rychlejší doručování mediálních materiálů. Používá taky konfigurace pro mediální aktiva podle doporučené postupy osvojené v čase.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurace koncového bodu CDN společnosti Akamai optimalizace streamování médií
 
Můžete nakonfigurovat koncový bod content delivery network (CDN) k optimalizaci doručování velkých souborů prostřednictvím portálu Azure portal. Provedete to tak můžete také použít rozhraní REST API nebo některou z klientské sady SDK. Následující kroky ukazují proces prostřednictvím webu Azure portal **Azure CDN Standard od Akamai** profilu:

1. Chcete-li přidat nový koncový bod na Akamai **profil CDN** stránce **koncový bod**.
  
    ![Nový koncový bod](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. V **optimalizovaná pro** rozevíracího seznamu vyberte **Video streamování na vyžádání média** assetů vod (Video-on-demand). Pokud tak učiníte, kombinace za provozu a streamování videa na vyžádání, vyberte **streamování obecných médií**.

    ![Streamování vybrané](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po vytvoření koncového bodu se vztahuje optimalizace pro všechny soubory, které splňují určitá kritéria. Následující část popisuje tento proces. 

### <a name="caching"></a>Ukládání do mezipaměti

Pokud **Azure CDN Standard od Akamai** zjistí, prostředek je streamovací manifest nebo fragment, používá různou dobu vypršení platnosti mezipaměti z obecné doručování webu. (Podívejte se na seznam v následující tabulce.) Protože jsou vždy, neuplatňují cache-control nebo Expires záhlaví odeslanými ze zdroje. Pokud prostředek není asset média, se ukládá do mezipaměti pomocí dobách vypršení platnosti pro obecné doručování webu.

Krátký záporný časový ukládání do mezipaměti je užitečný pro snižování zátěže původu když mnoho uživatelů požadavku fragment, který ještě neexistuje. Příkladem je živý stream, kam se pakety nejsou k dispozici z původního zdroje této druhé. Už ukládání do mezipaměti interval také pomáhá přesměrovat zpracování požadavků z původního zdroje, protože obsah videa se obvykle nezmění.
 

|   | Obecné doručování webu | Streamování obecných médií | Streamování médií vod (Video-on-demand)  
--- | --- | --- | ---
Ukládání do mezipaměti: pozitivní <br> HTTP 200, 203, 300, <br> 301, 302 a 410 | 7 dní |365 dnů | 365 dnů   
Ukládání do mezipaměti: záporná <br> HTTP 204, 305, 404, <br> a 405 | Žádný | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Řešení selhání původu  

Poskytování médií obecné a doručování médií vod (Video-on-demand) mají také původu vypršení časového limitu a opakovat protokolu na základě osvědčených postupů pro vzory typické požadavku. Protože je doručování obecných médií pro za provozu a doručování médií vod (Video-on-demand), použije kratší časový limit připojení vzhledem k povaze časově live například streamování.

Když vyprší časový limit připojení, síť CDN opakování s počtem opakování, než odešle klientovi chybu "504 – časový limit brány". 

Když soubor odpovídá seznamu podmínky typu a velikosti souborů, CDN používá chování pro streamování médií. V opačném případě používá obecné doručování webu.
   
### <a name="conditions-for-media-streaming-optimization"></a>Podmínky pro optimalizaci streamování médií 

V následující tabulce jsou uvedeny sadu kritérií, které je třeba splnit pro optimalizace streamování médií: 
 
Podporované typy datových proudů | Přípony souborů  
--- | ---  
Apple HLS | m3u8 m3u, m3ub, klíč, ts, aac
Adobe HDS | f4m f4x, drmmeta, bootstrap, f4f,<br>Adresa URL KŘEH seg – struktura <br> (odpovídající regulární výraz: ^(/.*)Seq(\d+)-Frag(\d+)
POMLČKA | MPD, pomlčku, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx webm, mp4a, m4a, isma
Technologie Smooth streaming | / manifest /, /QualityLevels/fragmenty /
  
 
