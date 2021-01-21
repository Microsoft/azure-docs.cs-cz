---
title: Navigace a historie verze IoT Edge – Azure IoT Edge
description: Zjistěte, co je nového v IoT Edge s informacemi o nových funkcích a možnostech v nejnovějších verzích.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e7b85f3e01a1c9a9c354599cfed8359b4a56d549
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631450"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Verze Azure IoT Edge a poznámky k verzi

Azure IoT Edge je produkt sestavený z Open Source IoT Edge projektu hostovaného na GitHubu. Všechny nové verze jsou zpřístupněny v [Azure IoT Edge projektu](https://github.com/Azure/azure-iotedge). Příspěvky a zprávy o chybách lze vytvořit v [projektu Open source IoT Edge](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Dokumentované verze

Dokumentace IoT Edge v této lokalitě je dostupná pro dvě různé verze produktu, takže můžete vybrat obsah, který se vztahuje na vaše IoT Edge prostředí. V současné době jsou k dispozici tyto dvě podporované verze:

* **IoT Edge 1.0.10** zahrnuje všechny funkce a možnosti prostřednictvím nejnovější všeobecně dostupné verze: [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10).
* **IoT Edge 1,2 (Preview)** obsahuje další obsah pro funkce a funkce, které jsou v nejnovější verzi preview: [1,2-RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * I když je IoT Edge 1,2 ve verzi Preview, je nutné nainstalovat Release Candidate verze. Další informace najdete v tématu [instalace offline nebo specifické verze](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation-optional).

## <a name="version-history"></a>Historie verzí

Tato tabulka obsahuje historii nedávné verze IoT Edge verzí balíčků a zvýrazňuje aktualizace dokumentace k jednotlivým verzím.

| Poznámky k verzi a materiály | Typ | Datum | Nejzajímavější body |
| ------------------------ | ---- | ---- | ---------- |
| [1,2 – RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Preview | Listopad 2020 | [IoT Edge zařízení za brány](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge zprostředkovatel MQTT](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Říjen 2020 | [UploadSupportBundle Direct – metoda](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Nahrát běhové metriky](how-to-access-built-in-metrics.md)<br>[Priorita trasy a doba do provozu](module-composition.md#priority-and-time-to-live)<br>[Pořadí spouštění modulu](module-composition.md#configure-modules)<br>[Ruční zřizování X. 509](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Březen 2020 | [Automatické zřizování X. 509 s DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule Direct – metoda](how-to-edgeagent-direct-method.md#restart-module)<br>[Podpora – příkaz sady](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Další kroky

* [Zobrazit všechny verze Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)
* [Vytváření nebo kontrola žádostí o funkce ve fóru pro zpětnou vazbu](https://feedback.azure.com/forums/907045-azure-iot-edge)