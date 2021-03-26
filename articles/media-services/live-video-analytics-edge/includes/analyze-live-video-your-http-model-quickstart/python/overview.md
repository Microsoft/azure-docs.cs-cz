---
ms.openlocfilehash: 8124f31213de1bbb794968c616e6efb940ac36b2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582358"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Tok signálů":::

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server protokolu RTSP (Real-Time streaming Protocol). [Zdrojový uzel RTSP](../../../media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru rozšíření http](../../../media-graph-concept.md#http-extension-processor) . 

Uzel rozšíření HTTP hraje roli proxy serveru. Vypíše sadu příchozích snímků videa pomocí `samplingOptions` pole a také převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes REST do jiného modulu Edge, který spouští model AI za koncovým bodem HTTP. V tomto příkladu je tento modul Edge sestaven pomocí modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který dokáže detekovat mnoho typů objektů. Uzel procesoru rozšíření HTTP shromáždí výsledky detekce a publikuje události do uzlu [IoT Hub jímka](../../../media-graph-concept.md#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.