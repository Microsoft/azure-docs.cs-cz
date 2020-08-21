---
ms.openlocfilehash: 97c21ca300ee070b2cebaa01a585c1618899b1eb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691755"
---

![Přehled](../../../media/quickstarts/overview-grpc.png)

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server RTSP (Real-time streaming Protocol). [Zdrojový uzel RTSP](../../../media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru detekce pohybu](../../../media-graph-concept.md#motion-detection-processor) . Tento procesor rozpozná pohyb a při detekci pozastaví snímky videa do uzlu [procesoru rozšíření gRPC](../../../media-graph-concept.md#grpc-extension-processor) .

Uzel rozšíření gRPC hraje roli proxy serveru. Převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes gRPC do jiného modulu Edge, který spouští model AI za koncovým bodem gRPC přes [sdílenou paměť](https://en.wikipedia.org/wiki/Shared_memory). V tomto příkladu je tento modul Edge sestaven pomocí modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který dokáže detekovat mnoho typů objektů. Uzel procesoru rozšíření gRPC shromažďuje výsledky detekce a publikuje události do uzlu [IoT Hub jímka](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.
