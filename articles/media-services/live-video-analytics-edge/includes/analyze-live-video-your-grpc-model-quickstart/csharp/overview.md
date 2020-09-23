---
ms.openlocfilehash: fa0c2f5bb00122b40fb4f4ea06b7cf55c0248904
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025039"
---

![Přehled](../../../media/quickstarts/gRPC-extension.svg)

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server RTSP (Real-time streaming Protocol). [Zdrojový uzel RTSP](../../../media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru detekce pohybu](../../../media-graph-concept.md#motion-detection-processor) . Tento procesor rozpozná pohyb a při detekci pozastaví snímky videa do uzlu [procesoru rozšíření gRPC](../../../media-graph-concept.md#grpc-extension-processor) .

Uzel rozšíření gRPC hraje roli proxy serveru. Převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes gRPC do jiného modulu Edge, který spouští model AI za koncovým bodem gRPC přes [sdílenou paměť](https://en.wikipedia.org/wiki/Shared_memory). V tomto příkladu je tento modul Edge sestaven pomocí modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který dokáže detekovat mnoho typů objektů. Uzel procesoru rozšíření gRPC shromažďuje výsledky detekce a publikuje události do uzlu [IoT Hub jímka](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.
