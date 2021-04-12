---
title: Řešení potíží s modulem Azure Percept Vision a Vision
description: Získejte tipy pro řešení problémů s některými častými problémy, které najdete v prostředích pro vytváření prototypů AI.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 78de5ef0ef77a181d4a2da91e4b468db1b47f208
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074688"
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

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="IoT Edge stránka pro vybrané zařízení se zobrazeným obsahem karty moduly" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Odstranit zařízení

1. Přejděte na [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Klikněte na ikonu **centra IoT** .

1. Vyberte IoT Hub, ke kterému je vaše cílové zařízení připojené.

1. Vyberte **IoT Edge** a zaškrtněte políčko vedle ID cílového zařízení. Kliknutím na ikonu koše můžete zařízení odstranit.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Ikona odstranění zvýrazněná na domovské stránce IoT Edge":::

## <a name="eye-module-troubleshooting-tips"></a>Tipy pro řešení potíží s modulem pro oči

### <a name="check-the-runtime-status-of-azureeyemodule"></a>Zkontroluje stav modulu runtime pro azureeyemodule.

Pokud dojde k potížím s **WebStreamModule**, ujistěte se, že je spuštěný **azureeyemodule**, který zpracovává model Vision Inferencing. Chcete-li zjistit stav modulu runtime, přejděte na [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) a přejděte do části **všechny prostředky**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kliknutím na kartu **moduly** zobrazíte stav modulu runtime všech instalovaných modulů.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Obrazovka stavu modulu runtime modulu zařízení." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Pokud běhový stav **azureeyemodule** není uveden jako **spuštěný**, klikněte na **nastavit moduly**  ->  **azureeyemodule**. Na stránce **nastavení modulu** nastavte **požadovaný stav** na **spuštěno** a klikněte na **aktualizovat**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Obrazovka konfigurace nastavení modulu":::

### <a name="update-telemetryintervalneuralnetworkms"></a>Aktualizovat TelemetryIntervalNeuralNetworkMs

Pokud narazíte na následující chybu omezení počtu, bude nutné aktualizovat hodnotu TelemetryIntervalNeuralNetworkMs v modulu azureeyemodule s dvojitým nastavením.

|Chybová zpráva|
|------|
|Celkový počet zpráv v IotHub ' XXXXXXXXX ' překročil přidělenou kvótu. Maximální povolený počet zpráv: ' 8000 ', aktuální počet zpráv: ' xxxx '. Operace odeslání a přijetí jsou pro toto centrum blokované až do dalšího dne UTC. Pokud chcete kvótu zvýšit, zvažte zvýšení počtu jednotek tohoto centra.|

TelemetryIntervalNeuralNetworkMs určuje, jak často se mají posílat zprávy (v milisekundách) ze sítě neuronové. Předplatné Azure mají v závislosti na vaší úrovni předplatného omezený počet zpráv za den. Pokud zjistíte, že jste byli uzamčeni z důvodu odeslání příliš velkého počtu zpráv, zvyšte hodnotu na vyšší číslo. 12000 (což znamená každých 12 sekund) vám poskytne skvělé kulaté zprávy 7200 za den, což je limit zpráv 8000 pro bezplatné předplatné.

K aktualizaci hodnoty TelemetryIntervalNeuralNetworkMs použijte následující postup:

1. Přihlaste se k [Azure Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) a otevřete **všechny prostředky**.

1. Na stránce **všechny prostředky** klikněte na název IoT Hub, který jste zřídili pro vaši DevKit během prostředí nastavení.

1. Na levé straně stránky IoT Hub klikněte na **IoT Edge** v části **Automatická správa zařízení**. Na stránce IoT Edge zařízení vyhledejte ID zařízení DevKit. Kliknutím na ID zařízení DevKit otevřete jeho stránku IoT Edge zařízení.

1. Na kartě **moduly** vyberte **azureeyemodule** .

1. Na stránce azureeyemodule otevřete **Nevlákennou identitu modulu**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Snímek stránky modulu." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Přejděte dolů na **vlastnosti**. Všimněte si, že vlastnosti running a Logging nejsou v tuto chvíli aktivní.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Snímek obrazovky modulu s dvojitými vlastnostmi" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Aktualizujte hodnotu **TelemetryIntervalNeuralNetworkMs** podle potřeby a klikněte na ikonu **Uložit** .

## <a name="view-device-rtsp-video-stream"></a>Zobrazit datový proud videa RTSP zařízení

Podívejte se na video stream RTSP vašeho zařízení v [Azure Percept studiu](./how-to-view-video-stream.md) nebo v [přehrávači médií VLC](https://www.videolan.org/vlc/index.html).

Pokud chcete otevřít datový proud RTSP v VLC Media Playeru, klikněte na **multimédia**  ->  **otevřít síťový Stream**  ->  **RTSP://[IP adresa zařízení]/Result**.

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s Azure Percept DK najdete v tématu [Obecná příručka pro odstraňování potíží](./troubleshoot-dev-kit.md) .