---
title: Vývoj modulů pro Azure IoT Edge | Dokumentace Microsoftu
description: Vývoj vlastních modulů pro Azure IoT Edge, který může komunikovat s modul runtime a službu IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 878ff5901df80398afff7f429c41f102da3edba4
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793588"
---
# <a name="develop-your-own-iot-edge-modules"></a>Vyvíjet vlastní moduly IoT Edge

Moduly Azure IoT Edge můžete připojení s ostatními službami Azure a přispívat do kanálu dat větší cloudu. Tento článek popisuje, jak vám umožní vytvářet moduly ke komunikaci se modul runtime IoT Edge a IoT Hub a proto rest cloudu Azure. 

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

### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud
Umožňuje komplexní zpracování zpráv typu zařízení cloud, Centrum IoT Edge poskytuje deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub. Deklarativní směrování povoluje modulů k zachycení a zpracování zpráv odesílaných ostatní moduly a jejich šíření kanálech složité. Další informace najdete v tématu [nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md).

Modul IoT Edge, na rozdíl od normální aplikace zařízení služby IoT Hub, může přijímat zprávy typu zařízení cloud, které se připojit přes proxy server pomocí její místní centra IoT Edge k jejich zpracování.

Centrum IoT Edge šíří zprávy, které mají modul podle deklarativní směrování podle [manifest nasazení](module-composition.md). Při vývoji modul IoT Edge, mohou přijímat tyto zprávy nastavením obslužné rutiny zpráv.

Zjednodušení vytváření tras, IoT Edge přidá pojem modul *vstupní* a *výstup* koncových bodů. Modul může přijímat všechny zprávy typu zařízení cloud směrovat bez zadání jakékoli vstup a odesílat zprávy typu zařízení cloud bez zadání žádný výstup. Pomocí explicitní vstupy a výstupy, ale zjednodušuje pravidla směrování pochopit. 

Zprávy typu zařízení cloud zpracovat Centrum Edge se nakonec razítkem s následujícími vlastnostmi systému:

| Vlastnost | Popis |
| -------- | ----------- |
| $connectionDeviceId | ID zařízení klienta, který zprávu odeslal |
| $connectionModuleId | ID modulu modulu, který zprávu odeslal |
| $inputName | Vstup, který se tato zpráva zobrazila. Může být prázdný. |
| $outputName | Výstup používá k odeslání zprávy. Může být prázdný. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Připojení k centru IoT Edge z modulu
Připojení k místní Centrum IoT Edge z modulu zahrnuje dva kroky: 
1. Vytvoření ModuleClient instance ve vaší aplikaci.
2. Zajistěte, aby že vaše aplikace přijímá certifikát předložený Centrum IoT Edge na tomto zařízení.

Vytvoření instance ModuleClient připojit modul IoT Edge hub spuštěného v příslušném zařízení, podobně jako instance DeviceClient jak připojit zařízení IoT ke službě IoT Hub. Další informace o třídě ModuleClient a jeho metody komunikace najdete v referenci rozhraní API pro váš preferovaný jazyk sady SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C a Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable), nebo [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>Další postup

Poté, co při vývoji modulu, zjistěte, jak [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).

