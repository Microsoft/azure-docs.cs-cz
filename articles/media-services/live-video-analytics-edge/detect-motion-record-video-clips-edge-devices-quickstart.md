---
title: Detekce videa o pohybových &ch na hraničních zařízeních – Azure
description: V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého informačního kanálu z kamery (simulované) IP adresy, zjistit, jestli je k dispozici nějaký pohyb, a pokud ano, zaznamenejte videoklip MP4 do místního systému souborů na hraničním zařízení.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e456bb5b8d6d6658158af0c58f05ab38fe4693b8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682176"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Rychlý Start: detekce a nahrávání videa na hraničních zařízeních
 
V tomto rychlém startu se dozvíte, jak používat Live video Analytics na IoT Edge k analýze živého kanálu videa z (simulované) kamery IP. Ukazuje, jak zjistit, jestli je k dispozici nějaký pohyb, a pokud ano, zaznamenejte si videoklip ve formátu MP4 do místního systému souborů na hraničním zařízení. Rychlý Start používá virtuální počítač Azure jako zařízení IoT Edge a používá simulovaný živý Stream videa. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Předpoklady

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Kontrola ukázkového videa

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Přehled

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>Kontrola a úprava ukázkových souborů

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Kontrola – zkontrolujte stav modulů.

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Kontrola – Příprava na monitorování událostí

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Spuštění ukázkového programu

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretace výsledků 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>Přehrát klip MP4

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, potom ponechejte prostředky, které jste vytvořili. V opačném případě v Azure Portal do skupin prostředků vyberte skupinu prostředků, ve které jste spustili tento rychlý Start, a pak odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

* Postup pro použití AI na živé video kanály najdete v kurzu [spuštění živého videa s](use-your-model-quickstart.md) využitím vlastního rychlého startu.
* Přečtěte si další výzvy pro pokročilé uživatele:

    * Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera) , který podporuje RTSP. Kamery protokolu IP, které podporují protokol RTSP, najdete na stránce vyhovující [ONVIF produktů](https://www.onvif.org/conformant-products) . Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.
    * Místo použití virtuálního počítače se systémem Linux v Azure použijte zařízení AMD64 nebo x64 Linux. Toto zařízení musí být ve stejné síti jako kamera IP. Postupujte podle pokynů v části [Instalace modulu runtime Azure IoT Edge v systému Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Pak postupujte podle pokynů v části [nasazení prvního IoT Edge modulu do zařízení s Virtual Linux](../../iot-edge/quickstart-linux.md) a zaregistrujte zařízení ve službě Azure IoT Hub.
