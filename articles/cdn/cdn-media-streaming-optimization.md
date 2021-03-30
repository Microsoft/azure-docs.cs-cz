---
title: Optimalizace streamování médií pomocí Azure CDN
description: Přečtěte si o možnostech pro optimalizaci streamování médií v Azure Content Delivery Network, jako je například částečné sdílení mezipaměti a doba čekání na doplňování mezipaměti.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: c3ab722f182e32cf2f3aca6bb2f3d5a9598264af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192613"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optimalizace streamování médií pomocí Azure CDN 
 
Použití videa s vysokým rozlišením roste na internetu, což vytváří problémy pro efektivní doručování velkých souborů. Zákazníci očekávají hladké přehrávání videa na vyžádání nebo živých videí na různých sítích a klientů po celém světě. Rychlý a efektivní mechanismus doručování pro soubory multimediálního streamování je velmi důležitý, aby se zajistilo hladké a pohodlnější uživatelské prostředí.  

Médium živého streamování je obzvlášť obtížné doručovat v důsledku velkých velikostí a počtu souběžných návštěvníků. Dlouhé zpoždění způsobí, že uživatelé odejdou. Vzhledem k tomu, že živé streamy nelze ukládat do mezipaměti před časem a vysoké latence není pro diváky přijatelné, je nutné včas doručovat fragmenty videa. 

Některé nové výzvy poskytují také vzory žádostí o streamování. Když je pro video na vyžádání vydaný oblíbený živý stream nebo nová řada, může datový proud vyžádat tisíce až milionů prohlížečů. V takovém případě je konsolidace inteligentních požadavků zásadní pro případ, že se prostředky ještě neukládají do mezipaměti, a tím nezahltí servery.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimalizace streamování médií pro Azure CDN od Microsoftu

**Azure CDN Standard z** koncových bodů Microsoftu doručovat prostředky streamování přímo pomocí obecného typu optimalizace webového doručování. 

Optimalizace streamování médií pro **Azure CDN Standard od Microsoftu** je platná pro živé streamování nebo streamování videa na vyžádání, které používá jednotlivé fragmenty médií k doručení. Tento proces se liší od jednoho velkého prostředku přenesených prostřednictvím postupného stahování nebo pomocí požadavků na rozsah bajtů. Informace o tomto stylu doručování médií najdete v tématu [optimalizace stahování velkých souborů pomocí Azure CDN](cdn-large-file-optimization.md).

Typy optimalizace doručování médií na základě médií nebo videa na vyžádání využívají službu Azure Content Delivery Network (CDN) s optimalizací back-endu k rychlejšímu poskytování mediálních prostředků. Používají také konfigurace pro Media assety na základě osvědčených postupů, které se v průběhu času seznámily.

### <a name="partial-cache-sharing"></a>Částečné sdílení mezipaměti
Částečné sdílení mezipaměti umožňuje CDN poskytovat obsah v mezipaměti do nových požadavků. Například pokud první požadavek na CDN má za následek neúspěšný pokus o ukládání do mezipaměti, pošle se požadavek na počátek. I když je tento nekompletní obsah načtený do mezipaměti CDN, můžou začít získat tato data další požadavky na CDN. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimalizace streamování médií pro Azure CDN z Verizon

**Azure CDN Standard z Verizon** a **Azure CDN Premium od** koncových bodů Verizon poskytuje prostředky streamování médií přímo pomocí obecného typu optimalizace webového doručování. Několik funkcí v CDN přímo pomáhá při doručování mediálních prostředků ve výchozím nastavení.

### <a name="partial-cache-sharing"></a>Částečné sdílení mezipaměti

Částečné sdílení mezipaměti umožňuje CDN poskytovat obsah v mezipaměti do nových požadavků. Například pokud první požadavek na CDN má za následek neúspěšný pokus o ukládání do mezipaměti, pošle se požadavek na počátek. I když je tento nekompletní obsah načtený do mezipaměti CDN, můžou začít získat tato data další požadavky na CDN. 

### <a name="cache-fill-wait-time"></a>Doba čekání na doplňování mezipaměti

 Funkce čekání na doplňování mezipaměti vynutí hraniční Server, aby obsahoval všechny následné požadavky na stejný prostředek, dokud hlavičky odpovědí HTTP nepřijde ze zdrojového serveru. Pokud se hlavičky HTTP odpovědi od počátku dorazí do vypršení platnosti časovače, všechny požadavky, které byly pozastaveny, se zpracovávají z rostoucí mezipaměti. V současné době se mezipaměť vyplní daty od počátku. Ve výchozím nastavení je doba čekání na vyplňování mezipaměti nastavená na 3 000 milisekund. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimalizace streamování médií pro Azure CDN z Akamai
 
**Azure CDN Standard z Akamai** nabízí funkci, která efektivně doručuje streamování mediálních prostředků uživatelům po celém světě. Funkce snižuje latenci, protože snižuje zatížení na počátečních serverech. Tato funkce je k dispozici ve standardní cenové úrovni Akamai. 

Optimalizace streamování médií pro **Azure CDN Standard od Akamai** je platná pro živé nebo video streamování na vyžádání, které používá jednotlivé fragmenty médií k doručení. Tento proces se liší od jednoho velkého prostředku přenesených prostřednictvím postupného stahování nebo pomocí požadavků na rozsah bajtů. Informace o tomto stylu doručování médií najdete v tématu [optimalizace velkých souborů](cdn-large-file-optimization.md).

Typy optimalizace doručování médií v rámci programu Media Delivery nebo videa na vyžádání využívají síť CDN s optimalizací back-endu k rychlejšímu poskytování mediálních prostředků. Používají také konfigurace pro Media assety na základě osvědčených postupů, které se v průběhu času seznámily.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurace koncového bodu CDN Akamai k optimalizaci streamování médií
 
Můžete nakonfigurovat koncový bod služby Content Delivery Network (CDN) pro optimalizaci doručování velkých souborů prostřednictvím Azure Portal. K tomu můžete použít také rozhraní REST API nebo libovolné klientské sady SDK. Následující kroky ukazují proces prostřednictvím Azure Portal pro **Azure CDN Standard z profilu Akamai** :

1. Pokud chcete přidat nový koncový bod, na stránce **profilu CDN** Akamai vyberte **koncový bod**.
  
    ![Nový koncový bod](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. V rozevíracím seznamu **optimalizované pro** vyberte **video streamování na vyžádání** pro prostředky videa na vyžádání. Pokud používáte kombinaci živého streamování a streamování videa na vyžádání, vyberte možnost **Obecné streamování médií**.

    ![Vybráno streamování](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Po vytvoření koncového bodu se použije optimalizace pro všechny soubory, které splňují určitá kritéria. Tento proces je popsán v následující části. 

### <a name="caching"></a>Ukládání do mezipaměti

Pokud **Azure CDN Standard z Akamai** zjistí, že Asset je manifest nebo fragment streamování, používá jiné časy vypršení platnosti pro ukládání do mezipaměti z obecného doručování webu. (Podívejte se na úplný seznam v následující tabulce.) Jako vždy jsou dodrženy hlavičky pro řízení mezipaměti nebo vypršení platnosti odeslané počátkem. Pokud se nejedná o mediální prostředek, uloží se do mezipaměti pomocí doby vypršení platnosti pro obecné doručování webu.

Krátký negativní čas ukládání do mezipaměti je vhodný pro snižování zátěže, když mnoho uživatelů vyžádá fragment, který ještě neexistuje. Příkladem je živý datový proud, ve kterém nejsou pakety k dispozici od počátku druhého. Delší interval mezipaměti také pomáhá přesměrovat požadavky od počátku, protože obsah videa se obvykle nemění.

| Ukládání do mezipaměti  | Obecné doručování webu | Obecné streamování médií | Streamování médií videa na vyžádání  
|--- | --- | --- | ---
| Ukládání do mezipaměti: kladné <br> HTTP 200, 203, 300, <br> 301, 302 a 410 | 7 dní |365 dní | 365 dní   
| Ukládání do mezipaměti: záporné <br> HTTP 204, 305, 404, <br> a 405 | Žádné | 1 sekunda | 1 sekunda
 
### <a name="deal-with-origin-failure"></a>Zabývat se selháním původu  

Obecné doručování médií a doručování médií na vyžádání má také časový limit původu a opakovaný protokol opakování na základě osvědčených postupů typických vzorů požadavků. Například vzhledem k tomu, že je běžné doručování médií k dislive a k doručování médií na vyžádání, používá kratší časový limit připojení kvůli časově náročnému streamování živého streamování.

Když se připojení vyprší, služba CDN se pokusy opakuje několikrát, než pošle klientovi chybu "504-Gateway timeout". 

Když soubor odpovídá typu souboru a seznamu stavů velikostí, CDN používá chování pro streamování médií. V opačném případě používá obecné doručování webu.
   
### <a name="conditions-for-media-streaming-optimization"></a>Podmínky Optimalizace streamování médií 

Následující tabulka uvádí sadu kritérií, která se mají splnit pro optimalizaci streamování médií: 
 
Podporované typy streamování | Přípony souborů  
--- | ---  
Apple HLS | M3U8, M3U, m3ub, Key, TS, AAC
Adobe HDS | f4m, f4x, drmmeta, Bootstrap, f4f,<br>Seg-Frag struktura adresy URL <br> (odpovídajícího regulárního výrazu: ^ (/. *) SEQ (\d +)-Frag (\d +)
PŘERUŠENÍ | MPD, pomlčka, DivX, ISMV, M4S, M4V, MP4, MP4V, <br> sidx, webm, mp4a, m4a, isma
Hladké streamování | /manifest/, /QualityLevels/Fragments/
  
 
