---
title: Analýza živého videa pomocí vlastního modelu gRPC – Azure
description: V tomto rychlém startu použijete počítačovou vizi k analýze živého kanálu videa z (simulované) kamery IP.
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 907d402a4b0ef5d5e437322d02431a0ccd2d8da3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89421513"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model"></a>Rychlý Start: Analýza živého videa pomocí vlastního modelu gRPC

V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého kanálu videa z (simulované) kamery IP. Uvidíte, jak použít model počítačové vize k detekci objektů. Do odvozené služby se pošle podmnožina snímků v živém obrazovém kanálu. Výsledky se odesílají do centra IoT Edge.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Předpoklady

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Přehled

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Vytvoření a nasazení mediálního grafu

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretace výsledků

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet další rychlé starty, ponechte prostředky, které jste vytvořili. V opačném případě přejdete na Azure Portal, přejdete do skupin prostředků, vyberete skupinu prostředků, ve které jste spustili tento rychlý Start, a odstraníte všechny prostředky.

## <a name="next-steps"></a>Další kroky

* Zkuste spustit různé topologie mediálních grafů pomocí protokolu gRPC.
* **Sestavování a spouštění ukázkových rozšíření LVA (Live video Analytics)**
<br/>Vyzkoušejte si naše ukázkové poznámkové bloky Jupyter, které vám umožní vytvářet a spouštět modely YOLO založené na [ONNX](http://onnx.ai/) jako rozšíření Live video Analytics (LVA).
    * [Vzorový model YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/readme.md)
    * [Vzorový model YOLOv4](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov4/yolov4-grpc-icpu-onnx/readme.md)

