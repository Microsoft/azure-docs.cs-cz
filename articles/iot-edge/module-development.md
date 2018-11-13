---
title: Vývoj modulů pro Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet vlastní moduly pro Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2513624aecff652e8a952b3255faf2ab9366f21a
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288692"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Pochopení požadavků a nástroje pro vývoj modulů IoT Edge

Tento článek vysvětluje, jaké funkce jsou k dispozici při psaní aplikace, které běží jako modul IoT Edge a tom, jak využít.

## <a name="iot-edge-runtime-environment"></a>Prostředí modulu runtime IoT Edge
Modul runtime IoT Edge poskytuje infrastrukturu integrovat funkce více modulů IoT Edge a k jejich nasazení do zařízení IoT Edge. Na vysoké úrovni se dá zabalit jako modul IoT Edge žádný program. Však pokud chcete využívat všech výhod IoT Edge komunikační a funkce správy, můžete program spuštěný v modulu připojit k místní centra IoT Edge, integrované v modulu runtime IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Pomocí centra IoT Edge
Centrum IoT Edge poskytuje dva hlavní funkce: proxy serveru do služby IoT Hub a místní komunikace.

### <a name="iot-hub-primitives"></a>IoT Hub primitiv
IoT Hub vidí instancí modulu analogicky k zařízení, v tom smyslu, že:

* má dvojčete modulu, který je samostatný a izolované od [dvojče zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a další modul dvojčata zařízení;
* můžete odeslat [zpráv typu zařízení cloud](../iot-hub/iot-hub-devguide-messaging.md);
* může přijímat [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) zaměřené na svoji identitu.

V současné době modulu nemůže přijímat zprávy typu cloud zařízení ani pomocí funkce nahrání souboru.

Při psaní modulu, můžete použít [sady SDK Azure IoT Device](../iot-hub/iot-hub-devguide-sdks.md) pro připojení k centru IoT Edge a použití výše uvedené funkce stejně jako při používání služby IoT Hub s aplikací zařízení, jediným rozdílem je, že z vaší aplikace back-end, je nutné odkazovat na modul identity místo na identitě zařízení.

V tématu [vývoj a nasazení modul IoT Edge na simulovaném zařízení](tutorial-csharp-module.md) příklad modul aplikace, která odesílá zprávy typu zařízení cloud a používá dvojčete modulu.

### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud
Chcete-li povolit komplexní zpracování zpráv typu zařízení cloud, Centrum IoT Edge poskytuje deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub. Deklarativní směrování povoluje modulů k zachycení a zpracování zpráv odesílaných ostatní moduly a jejich šíření kanálech složité. Tento článek [složení modulu](module-composition.md) vysvětluje, jak sestavit modulů do složitých kanálů pomocí tras.

Modul IoT Edge, na rozdíl od normální aplikace zařízení služby IoT Hub, může přijímat zprávy typu zařízení cloud, které se připojit přes proxy server pomocí její místní centra IoT Edge k jejich zpracování.

Centrum IoT Edge šíří zprávy, které mají modul podle deklarativní směrování podle [složení modulu](module-composition.md) článku. Při vývoji modul IoT Edge, mohou přijímat tyto zprávy nastavením obslužné rutiny zpráv.

Aby bylo možné zjednodušit proces vytváření tras, IoT Edge přidá pojem modul *vstupní* a *výstup* koncových bodů. Modul může přijímat všechny zprávy typu zařízení cloud směrovat bez zadání jakékoli vstup a odesílat zprávy typu zařízení cloud bez zadání žádný výstup.
Pomocí explicitní vstupy a výstupy, ale zjednodušuje pravidla směrování pochopit. Další informace o pravidla směrování a vstupní a výstupní koncové body pro moduly, naleznete v tématu [složení modulu](module-composition.md).

Zprávy typu zařízení cloud zpracovat Centrum Edge se nakonec razítkem s následujícími vlastnostmi systému:

| Vlastnost | Popis |
| -------- | ----------- |
| $connectionDeviceId | ID zařízení klienta, který zprávu odeslal |
| $connectionModuleId | ID modulu modulu, který zprávu odeslal |
| $inputName | Vstup, který se tato zpráva zobrazila. Může být prázdný. |
| $outputName | Výstup používá k odeslání zprávy. Může být prázdný. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Připojení k centru IoT Edge z modulu
Připojení k místní Centrum IoT Edge z modulu zahrnuje dva kroky: 
1. Použijte připojovací řetězec poskytuje modul runtime IoT Edge při spuštění modulu.
2. Zajistěte, aby že vaše aplikace přijímá certifikát předložený Centrum IoT Edge na tomto zařízení.

Modul runtime IoT Edge v proměnné prostředí se vloží řetězec připojení používaný `EdgeHubConnectionString`. Díky tomu dostupné všem programům, které chce, aby se jeho použití.

Analogicky, se vloží certifikát, který chcete použít k ověření připojení k centru IoT Edge modulem runtime IoT Edge v souboru, jehož cesta je k dispozici v proměnné prostředí `EdgeModuleCACertificateFile`.

## <a name="next-steps"></a>Další postup

Poté, co při vývoji modulu, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).

