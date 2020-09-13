---
title: Záznam videa založeného na událostech – Azure
description: Nahrávání videa založeného na událostech (EVR) se týká procesu nahrávání videa aktivovaného událostí. Příčinou této události může být zpracování samotného hlasového signálu (například detekce při pohybu), nebo může být z nezávislého zdroje (například otevření dveří).  V tomto článku jsou popsány některé případy použití týkající se nahrávání videa založeného na událostech.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: f3efd2b9be41928ab4721d6db4aa84c0f1f57e2f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568478"
---
# <a name="event-based-video-recording"></a>Nahrávání videa na základě událostí  
 
## <a name="suggested-pre-reading"></a>Navrhované před čtením  

* [Nepřetržité nahrávání videa](continuous-video-recording-concept.md)
* [Přehrávání zaznamenaného obsahu](video-playback-concept.md)
* [Koncept Media graphu](media-graph-concept.md)

## <a name="overview"></a>Přehled 

Nahrávání videa založeného na událostech (EVR) se týká procesu nahrávání videa aktivovaného událostí. Důvodem může být to, že v důsledku zpracování samotného hlasového signálu (například detekce při pohybu) nebo z nezávislého zdroje (například otevření dveří) by se mohlo jednat o konkrétní událost. 

Video můžete nahrávat (aktivované událostmi) z kamery CCTV do assetu Media Services pomocí mediálního grafu, který se skládá ze zdrojového uzlu [RTSP](media-graph-concept.md#rtsp-source) , uzlu [jímky prostředků](media-graph-concept.md#asset-sink) a dalších uzlů, jak je uvedeno v níže uvedených případech použití. Uzel [jímka assetu](media-graph-concept.md#asset-sink) můžete nakonfigurovat tak, aby vygeneroval nové prostředky při každém výskytu události, takže video, které odpovídá každé události, bude ve vlastním prostředku. Můžete také použít jeden Asset pro uložení videa pro všechny události. 

Jako alternativu k uzlu jímka assetu můžete použít uzel [jímky souborů](media-graph-concept.md#file-sink) k zachycení krátkých fragmentů videa (v souvislosti s událostí důležité) do zadaného umístění v místním systému souborů na hraničním zařízení. 

V tomto článku jsou popsány některé případy použití týkající se nahrávání videa založeného na událostech.

### <a name="video-recording-based-on-motion-detection"></a>Záznam videa na základě detekce pohybu  

V tomto případě použití můžete nahrávat video klipy pouze v případě, že ve videu byl zjištěn pohyb a při vygenerování videoklipu se zobrazí výstraha. To může být relevantní v případě komerčních scénářů zabezpečení, které se týkají ochrany kritické infrastruktury. Generováním výstrah a nahráváním videa v případě zjištění neočekávaného pohybu můžete zlepšit efektivitu lidského operátora, protože akce je nutná pouze při vygenerování výstrahy.

Následující diagram znázorňuje grafické znázornění mediálního grafu, který tento případ použití řeší. Obrázek JSON topologie grafu takového mediálního grafu najdete [tady](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Záznam videa na základě detekce pohybu":::

V diagramu zdrojový uzel RTSP zachycuje kanál živého videa z kamery a doručí ho do uzlu [procesoru pro detekci pohybu](media-graph-concept.md#motion-detection-processor) . V případě detekce pohybu ve živém videu uzel procesoru pro detekci pohybu vygeneruje událost, která přejde do uzlu [procesoru brány signálu](media-graph-concept.md#signal-gate-processor) , a také do uzlu jímka IoT Hub zpráv. Druhý uzel odesílá události do centra IoT Edge, ze kterého je lze směrovat do jiných cílů a aktivovat tak výstrahy. 

Událost z uzlu snímače pohybu aktivuje uzel procesoru brány signálu a umožní kanálu živého videa ze zdrojového uzlu RTSP do uzlu jímka assetu. V případě neexistence následných událostí detekce pohybu se tato brána po nakonfigurované době ukončí. Určuje dobu trvání zaznamenaného videa.

### <a name="video-recording-based-on-events-from-other-sources"></a>Záznam videa na základě událostí z jiných zdrojů  

V tomto případě použití lze signály z jiného snímače IoT použít k aktivaci záznamu videa. Následující diagram znázorňuje grafické znázornění mediálního grafu, který tento případ použití řeší. Obrázek JSON topologie grafu takového mediálního grafu najdete [tady](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Záznam videa na základě událostí z jiných zdrojů":::

V diagramu externí senzor odesílá události do centra IoT Edge. Události se pak přesměrují do uzlu procesoru brány signálu přes uzel [zdroje IoT Hub zprávy](media-graph-concept.md#iot-hub-message-source) . Chování uzlu procesoru brány signálu je stejné jako u předchozího případu použití – otevře a zachová živý kanál videa ze zdrojového uzlu RTSP do uzlu jímky souborů (nebo uzlu jímky assetu), když ho spustí externí událost. 

Použijete-li uzel jímky souborů, bude video zaznamenáno do místního systému souborů na hraničním zařízení. V opačném případě, pokud použijete uzel jímky assetu, video se zaznamená do assetu.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Záznam videa na základě externího modulu Inferencing 

V tomto případě použití můžete nahrávat videoklipy na základě signálu z externího systému logiky. Příkladem takového případu použití může být záznam videoklipu jenom v případě, že se v informačním kanálu videa v provozu na dálnici zjistí nákladní vůz. Následující diagram znázorňuje grafické znázornění mediálního grafu, který tento případ použití řeší. Obrázek JSON topologie grafu takového mediálního grafu najdete [tady](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Záznam videa na základě externího modulu Inferencing":::

V diagramu zdrojový uzel RTSP zachycuje kanál živého videa z kamery a doručí ho do dvou větví: jeden má uzel [procesoru brány signálu](media-graph-concept.md#signal-gate-processor) a druhý používá uzel [rozšíření http](media-graph-concept.md) k posílání dat do externího modulu Logic. Uzel rozšíření HTTP umožňuje, aby mediální graf odesílal snímky obrázků (ve formátech JPEG, BMP nebo PNG) do externí služby odvození přes REST. Tato cesta signálu obvykle podporuje pouze nízké míry snímků (<5fps). Pomocí uzlu [procesor filtru frekvence snímků](media-graph-concept.md#frame-rate-filter-processor) můžete snížit kmitočet snímků videa v uzlu rozšíření http.

Výsledky z externí služby odvození jsou načteny uzlem rozšíření HTTP a přeneseny do centra IoT Edge prostřednictvím IoT Hubho uzlu jímky zpráv, kde mohou být dále zpracovávány externím modulem logiky. Pokud je služba odvození schopna rozpoznat vozidla, například modul Logic by mohl vyhledat konkrétní vozidlo, jako je například sběrnice nebo nákladní automobil. Když modul logiky detekuje objekt zájmu, může aktivovat uzel procesoru brány signálu odesláním události prostřednictvím centra IoT Edge do uzlu zdroje IoT Hub zprávy v grafu. Výstup z brány signálu je zobrazený jako uzel jímky souborů nebo uzel jímky assetu. V bývalém případě se video zaznamená do místního systému souborů na hraničním zařízení. V druhém případě se video zaznamená do assetu.

Vylepšením tohoto příkladu je použití procesoru snímače pohybu před uzlem procesoru filtru kmitočtu snímků. Tím se sníží zatížení odvozené služby, například nighttime, pokud se na dálnici neexistují žádná vozidla. 

## <a name="next-steps"></a>Další kroky

[Kurz: nahrávání videa založeného na událostech](event-based-video-recording-tutorial.md)
