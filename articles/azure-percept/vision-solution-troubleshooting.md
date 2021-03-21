---
title: Řešení potíží s modulem Azure Percept Vision a Vision
description: Získejte tipy pro řešení problémů s některými častými problémy, které najdete v prostředích pro vytváření prototypů AI.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662602"
---
# <a name="vision-solution-troubleshooting"></a>Řešení potíží s řešením Vision

V následujících pokynech najdete informace o řešení potíží s řešeními pro vize bez kódu v Azure Percept studiu.

## <a name="delete-a-vision-project"></a>Odstranit projekt sady Vision

1. Přejděte na https://www.customvision.ai/projects.

1. Najeďte myší na projekt, který chcete odstranit, a kliknutím na ikonu koše můžete projekt odstranit.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Stránka projekty v Custom Vision se zvýrazněnou ikonou odstranit":::

## <a name="check-which-modules-are-on-a-device"></a>Zjistit, které moduly jsou na zařízení

1. Přejděte na [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klikněte na ikonu **centra IoT** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Azure Portal domovskou stránku se zvýrazněnou ikonou centra IoT." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Vyberte IoT Hub, ke kterému je vaše cílové zařízení připojené.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Seznam Center IoT":::

1. Vyberte **IoT Edge** a na kartě **ID zařízení** klikněte na své zařízení.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge domovskou stránku.":::

1. Moduly zařízení budou uvedené na kartě **moduly** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Stránka IoT Edge pro vybrané zařízení se zobrazeným obsahem karty moduly" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Odstranit zařízení

1. Přejděte na [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klikněte na ikonu **centra IoT** .

1. Vyberte IoT Hub, ke kterému je vaše cílové zařízení připojené.

1. Vyberte **IoT Edge** a zaškrtněte políčko vedle ID cílového zařízení. Kliknutím na ikonu koše můžete zařízení odstranit.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Ikona Odstranit zvýrazněná na domovské stránce IoT Edge":::

## <a name="eye-module-troubleshooting-tips"></a>Tipy pro řešení potíží s modulem pro oči

Pokud dojde k potížím s **WebStreamModule**, ujistěte se, že je spuštěný **azureeyemodule** model Vision Inferencing. Chcete-li zjistit stav modulu runtime, přejděte na [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) a přejděte do části **všechny prostředky**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kliknutím na kartu **moduly** zobrazíte stav modulu runtime všech instalovaných modulů.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Obrazovka stavu modulu runtime modulu zařízení." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Pokud běhový stav **azureeyemodule** není uveden jako **spuštěný**, klikněte na **nastavit moduly**  ->  **azureeyemodule**. Na stránce **nastavení modulu** nastavte **požadovaný stav** na **spuštěno** a klikněte na **aktualizovat**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Obrazovka konfigurace nastavení modulu":::

## <a name="view-device-rtsp-video-stream"></a>Zobrazit datový proud videa RTSP zařízení

Podívejte se na video stream RTSP vašeho zařízení v [Azure Percept studiu](./how-to-view-video-stream.md) nebo v [přehrávači médií VLC](https://www.videolan.org/vlc/index.html).

Pokud chcete otevřít datový proud RTSP v VLC Media Playeru, klikněte na **multimédia**  ->  **otevřít síťový Stream**  ->  **RTSP://[IP adresa zařízení]/Result**.

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s Azure Percept DK najdete v tématu [Obecná příručka pro odstraňování potíží](./troubleshoot-dev-kit.md) .