---
ms.openlocfilehash: e96975bce5e5d99db4ea78c80c027a95d4b662e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92038456"
---
![Přehled](../../../media/quickstarts/gRPC-extension.svg)

Tento diagram znázorňuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje fotoaparát IP, který hostuje server protokolu RTSP (Real-Time streaming Protocol). [Zdrojový uzel RTSP](../../../media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru detekce pohybu](../../../media-graph-concept.md#motion-detection-processor) . Tento procesor rozpozná pohyb a při detekci pozastaví snímky videa do uzlu [procesoru rozšíření gRPC](../../../media-graph-concept.md#grpc-extension-processor) .

Uzel rozšíření gRPC hraje roli proxy serveru. Převede snímky videa na zadaný typ obrázku. Pak přenáší Image přes gRPC do jiného modulu Edge, který spouští model AI za koncovým bodem gRPC přes [sdílenou paměť](https://en.wikipedia.org/wiki/Shared_memory). V tomto příkladu je tento modul Edge sestaven pomocí modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , který dokáže detekovat mnoho typů objektů. Uzel procesoru rozšíření gRPC shromažďuje výsledky detekce a publikuje události do uzlu [IoT Hub jímka](../../../media-graph-concept.md#iot-hub-message-sink) . Uzel pak tyto události pošle do [centra IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.