---
title: Detekce pohybu objektů a pohybů ve službě Azure IoT Central video | Microsoft Docs
description: Naučte se sestavovat IoT Central aplikaci pomocí šablony video Analytics – objekt a detekce pohybu v IoT Central. Tato šablona používá Live video Analytics a připojené kamery.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 916d491c45a2979c59580328a721c11bd79d49c0
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372200"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Video Analytics – architektura aplikace pro detekci objektů a pohybů

Šablona aplikace **pro detekci video Analytics – objekt a pohyb** umožňuje sestavovat řešení IoT zahrnující funkce živé analýzy videí.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagram objektu video Analytics a součásti detekce pohybu":::

Mezi klíčové komponenty řešení video Analytics patří:

## <a name="live-video-analytics-lva"></a>Live video Analytics (LVA)

LVA poskytuje platformu pro vytváření inteligentních aplikací pro video, které přesahují hranice a Cloud. Platforma vám umožní vytvářet inteligentní aplikace pro video, které přesahují hranice a Cloud. Platforma nabízí možnost zachytit, nahrávat, analyzovat živé video a publikovat výsledky, což může být video nebo video Analytics, ke službám Azure. Služby Azure by mohly běžet v cloudu nebo na hraničních zařízeních. Platforma se dá použít k vylepšení řešení IoT pomocí video Analytics.

Další informace najdete v tématu [Live video Analytics](https://github.com/Azure/live-video-analytics) na GitHubu.

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge modul brány LVA

Modul IoT Edge LVA Gateway vytváří instance kamer jako nová zařízení a připojuje je přímo k IoT Central pomocí klientské sady SDK pro zařízení IoT.

V této referenční implementaci se zařízení připojují k řešení pomocí symetrických klíčů od okraje. Další informace o připojení zařízení najdete v tématu věnovaném [připojení k Azure IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Graf médií

Media Graph umožňuje definovat, kam se mají média zachytit, jak je zpracovat a kde doručovat výsledky. Media Graph nakonfigurujete tak, že propojíte součásti nebo uzly podle požadovaného způsobu. Další informace najdete v tématu [Media Graph](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) na GitHubu.

## <a name="next-steps"></a>Další kroky

Navržený další krok se naučíte, jak [vytvořit aplikaci video Analytics v Azure IoT Central](tutorial-video-analytics-create-app.md).
