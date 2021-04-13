---
title: Navigace a historie verze IoT Edge – Azure IoT Edge
description: Zjistěte, co je nového v IoT Edge s informacemi o nových funkcích a možnostech v nejnovějších verzích.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d3473e975e7c69a83c359b040a2de0defaac69b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310461"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Verze Azure IoT Edge a poznámky k verzi

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge je produkt sestavený z Open Source IoT Edge projektu hostovaného na GitHubu. Všechny nové verze jsou zpřístupněny v [Azure IoT Edge projektu](https://github.com/Azure/azure-iotedge). Příspěvky a zprávy o chybách lze vytvořit v [projektu Open source IoT Edge](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Dokumentované verze

Dokumentace IoT Edge v této lokalitě je dostupná pro dvě různé verze produktu, takže můžete vybrat obsah, který se vztahuje na vaše IoT Edge prostředí. V současné době jsou k dispozici tyto dvě podporované verze:

* **IoT Edge 1,2** obsahuje další obsah pro nové funkce a funkce, které jsou v nejnovější stabilní verzi.
* **IoT Edge 1,1 (LTS)** je první Dlouhodobá podpora IoT Edge (LTS). Dokumentace k této verzi se zabývá všemi funkcemi a možnostmi ze všech předchozích verzí až 1,1. Tato verze dokumentace bude stabilní až do podporované životnosti verze 1,1 a neprojeví se nové funkce vydané v novějších verzích.

Další informace o IoT Edge verzích najdete v části [Azure IoT Edge podporované systémy](support.md).

## <a name="version-history"></a>Historie verzí

Tato tabulka obsahuje historii nedávné verze IoT Edge verzí balíčků a zvýrazňuje aktualizace dokumentace k jednotlivým verzím.

| Poznámky k verzi a materiály | Typ | Date (Datum) | Nejzajímavější body |
| ------------------------ | ---- | ---- | ---------- |
| [1.2](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0) | Stable | Duben 2021 | [IoT Edge zařízení za brány](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge zprostředkovatel MQTT (Preview)](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true)<br>Nové balíčky IoT Edge představeny s novými kroky instalace a konfigurace. Další informace najdete v tématu [aktualizace z 1,0 nebo 1,1 na 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12) .
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Dlouhodobá podpora (LTS) | Únor 2021 | [Plán dlouhodobé podpory a podporované aktualizace systémů](support.md) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Říjen 2020 | [UploadSupportBundle Direct – metoda](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Nahrát běhové metriky](how-to-access-built-in-metrics.md)<br>[Priorita trasy a doba do provozu](module-composition.md#priority-and-time-to-live)<br>[Pořadí spouštění modulu](module-composition.md#configure-modules)<br>[Ruční zřizování X. 509](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Březen 2020 | [Automatické zřizování X. 509 s DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule Direct – metoda](how-to-edgeagent-direct-method.md#restart-module)<br>[Podpora – příkaz sady](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Další kroky

* [Zobrazit všechny verze Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)
* [Vytváření nebo kontrola žádostí o funkce ve fóru pro zpětnou vazbu](https://feedback.azure.com/forums/907045-azure-iot-edge)