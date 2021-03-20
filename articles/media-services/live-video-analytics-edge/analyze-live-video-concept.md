---
title: Analýza živého videa bez nahrávání – Azure
description: Mediální graf se dá použít k extrakci analýz z živého streamu videa, aniž byste ho museli nahrávat na hranici nebo v cloudu. Tento článek popisuje tento koncept.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97398960"
---
# <a name="analyzing-live-video-without-any-recording"></a>Analýza živého videa bez nahrávání

## <a name="suggested-pre-reading"></a>Navrhované před čtením 

* [Koncept Media graphu](media-graph-concept.md)
* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)

## <a name="overview"></a>Přehled  

Pomocí Media graphu můžete analyzovat živé video bez zaznamenávání částí videa do souboru nebo prostředku. Grafy médií zobrazené níže jsou podobné těm, které jsou uvedené v článku týkajícím se [nahrávání videa založeného na událostech](event-based-video-recording-concept.md), ale bez uzlu jímky prostředků nebo uzlu jímky souborů.

### <a name="motion-detection"></a>Detekce pohybu

Graf médií zobrazený níže se skládá ze [zdrojového uzlu RTSP](media-graph-concept.md#rtsp-source) , uzlu [procesoru pro detekci pohybu](media-graph-concept.md#motion-detection-processor) a uzlu [jímky zpráv IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Obrázek JSON topologie grafu takového mediálního grafu najdete [tady](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json). Tento graf vám umožní detekovat pohyb v příchozím datovém proudu živého videa a přenášet události pohybu do jiných aplikací a služeb prostřednictvím uzlu jímky zpráv IoT Hub. Externí aplikace nebo služby můžou aktivovat výstrahu nebo odeslat oznámení vhodným pracovníkům.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Analýza živých videí na základě detekce pohybu":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analýza videa pomocí vlastního modelu Vision 

Graf médií zobrazený níže vám umožňuje analyzovat živý Stream videa pomocí vlastního modelu vize zabaleného v samostatném modulu. Obrázek JSON topologie grafu takového mediálního grafu najdete [tady](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json). Některé [Příklady si můžete prohlédnout v tématu](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) zabalení modelů do IoT Edge modulů, které jsou spouštěny jako odvozená služba.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="Živá analýza videí založená na externím modulu Inferencing":::

V tomto mediálním grafu se vstup videa ze zdroje RTSP pošle na uzel [procesoru rozšíření http](media-graph-concept.md#http-extension-processor) , který odesílá snímky obrázků (ve formátech JPEG, BMP nebo PNG) do externí služby odvození za REST. Výsledky z externí služby odvození jsou načteny uzlem rozšíření HTTP a přeneseny do centra IoT Edge prostřednictvím IoT Hubho uzlu jímky zpráv. Tento typ mediálního grafu se dá použít k sestavení řešení pro celou řadu scénářů, jako je třeba porozumění rozdělení vozidel do řady v rámci průniku, porozumění vzoru přenosů uživatelů v maloobchodním obchodu atd.
>[!TIP]
> Kmitočet snímků můžete spravovat v uzlu procesoru rozšíření HTTP pomocí `samplingOptions` pole před odesláním podřízené položky.

Vylepšení tohoto příkladu je použití procesoru snímače pohybu před uzlem procesoru rozšíření HTTP. Tím se sníží zatížení odvozené služby, protože se používá pouze v případě, že je ve videu aktivita pohybu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="Živá analýza videa na základě pohybu zjištěných snímků prostřednictvím externího modulu Inferencing":::

## <a name="next-steps"></a>Další kroky

[Nepřetržité nahrávání videa](continuous-video-recording-concept.md)
