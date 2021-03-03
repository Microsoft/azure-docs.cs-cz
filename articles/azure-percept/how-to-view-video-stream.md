---
title: Podívejte se na video stream RTSP ve službě Azure Percept DK
description: Přečtěte si, jak zobrazit datový proud RTSP z Vision SoM pro Azure Percept DK.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 20fb8495e17d4294351a50c3bc97436de9f03450
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662363"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Podívejte se na video stream RTSP ve službě Azure Percept DK

Podle tohoto průvodce si můžete zobrazit datový proud videa RTSP z Vision SoM pro Azure Percept DK v rámci Azure Percept studia. Inferencing z visionových modelů AI nasazených do vašeho zařízení se budou zobrazovat ve webovém streamu.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub

## <a name="view-the-rtsp-video-stream"></a>Zobrazit datový proud videa RTSP

1. Zapněte si DevKit.

1. Přejděte do [Azure Percept studia](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Na levé straně stránky přehled klikněte na **zařízení**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Obrazovka s přehledem Azure Percept Studio" lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Vyberte DevKit ze seznamu.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Obrazovka s přehledem Azure Percept Studio":::

1. Klikněte na **Zobrazit datový proud zařízení**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Obrazovka s přehledem Azure Percept Studio":::

    Otevře se samostatná karta s živým webstreamem ze sady Vision SoM vaší služby Azure Percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Obrazovka s přehledem Azure Percept Studio":::

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak zobrazit [telemetrii Azure PERCEPT DK](./how-to-view-telemetry.md).