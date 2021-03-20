---
title: Podívejte se na video stream RTSP ve službě Azure Percept DK
description: Naučte se zobrazit datový proud videa RTSP z Azure Percept DK.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095999"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Podívejte se na video stream RTSP ve službě Azure Percept DK

Podle tohoto průvodce si můžete zobrazit datový proud videa RTSP z Azure Percept DK v rámci Azure Percept studia. Inferencing z visionových modelů AI nasazených do vašeho zařízení se budou zobrazovat ve webovém streamu.

## <a name="prerequisites"></a>Předpoklady

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub

## <a name="view-the-rtsp-video-stream"></a>Zobrazit datový proud videa RTSP

1. Zapněte si DevKit.

1. Přejděte do [Azure Percept studia](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Na levé straně stránky přehled klikněte na **zařízení**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Obrazovka s přehledem Azure Percept Studio" lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Vyberte DevKit ze seznamu.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Snímek obrazovky dostupných zařízení v Azure Percept studiu":::

1. Klikněte na **Zobrazit datový proud zařízení**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Snímek obrazovky se stránkou zařízení zobrazující dostupné akce projektu vize":::

    Otevře se samostatná karta s živým webovým datovým proudem z Azure Percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Snímek webového streamu zařízení":::

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak zobrazit [telemetrii Azure PERCEPT DK](./how-to-view-telemetry.md).