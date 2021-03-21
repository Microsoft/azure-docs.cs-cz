---
title: Nasazení modelu Vision AI do Azure Percept DK
description: Naučte se nasadit model Vision AI do Azure Percept DK z Azure Percept studia.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035479"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Nasazení modelu Vision AI do Azure Percept DK

Podle tohoto průvodce nasaďte model aplikace Vision AI do Azure Percept DK z Azure Percept studia.

## <a name="prerequisites"></a>Předpoklady

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub

## <a name="model-deployment"></a>Nasazení modelu

1. Zapněte si DevKit.

1. Přejděte do [Azure Percept studia](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Na levé straně stránky přehled klikněte na **zařízení**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Obrazovka s přehledem Azure Percept Studio" lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Vyberte DevKit ze seznamu.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Seznam zařízení Percept.":::

1. Na další stránce klikněte na **nasadit ukázkový model** , pokud chcete nasadit některý z předem vyškolených modelů ukázkových visionů. Pokud byste chtěli nasadit stávající [vlastní řešení pro revize bez kódu](./tutorial-nocode-vision.md), klikněte na **nasadit Custom Vision projekt**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Volby modelu pro nasazení.":::

1. Pokud jste se rozhodli nasadit řešení pro vize bez kódu, vyberte svůj projekt a preferovaný model iterace a klikněte na **nasadit**.

1. Pokud jste se rozhodli nasadit Vzorový model, vyberte model a klikněte na **nasadit do zařízení**.

1. Po úspěšném nasazení modelu se zobrazí stavová zpráva v pravém horním rohu obrazovky. Pokud chcete zobrazit Inferencing modelu v akci, klikněte na odkaz **Zobrazit stream** ve stavové zprávě a podívejte se na video stream RTSP z Vision SOM vašich DevKit.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak zobrazit [telemetrii Azure PERCEPT DK](how-to-view-telemetry.md).