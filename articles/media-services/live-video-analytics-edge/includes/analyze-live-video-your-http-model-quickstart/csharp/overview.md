---
ms.openlocfilehash: afe5ec179826b6d8dbef54ef773948bf9cbaea2f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684166"
---

![Přehled](../../../media/quickstarts/overview-qs5.png)

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server RTSP (Real-time streaming Protocol). [Zdrojový uzel RTSP](../../../media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a pošle snímky videa na uzel [procesoru filtru snímkové frekvence](../../../media-graph-concept.md#frame-rate-filter-processor) . Tento procesor omezuje kmitočet snímků streamu videa, který se dorazí na uzel [procesoru rozšíření http](../../../media-graph-concept.md#http-extension-processor) . 

Uzel rozšíření HTTP hraje roli proxy serveru. Převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes REST do jiného modulu Edge, který spouští model AI za koncovým bodem HTTP. V tomto příkladu je tento modul Edge sestaven pomocí modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který dokáže detekovat mnoho typů objektů. Uzel procesoru rozšíření HTTP shromáždí výsledky detekce a publikuje události do uzlu [IoT Hub jímka](../../../media-graph-concept.md#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.
