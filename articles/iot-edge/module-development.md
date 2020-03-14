---
title: Vývoj modulů pro Azure IoT Edge | Dokumentace Microsoftu
description: Vývoj vlastních modulů pro Azure IoT Edge, který může komunikovat s modul runtime a službu IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271379"
---
# <a name="develop-your-own-iot-edge-modules"></a>Vývoj vlastních modulů IoT Edge

Azure IoT Edge moduly se můžou připojit k ostatním službám Azure a přispívat do vašeho většího cloudového datového kanálu. Tento článek popisuje, jak můžete vyvíjet moduly pro komunikaci s IoT Edge modulem runtime a IoT Hub a tedy i zbytek cloudu Azure.

## <a name="iot-edge-runtime-environment"></a>Prostředí modulu runtime IoT Edge

Modul runtime IoT Edge poskytuje infrastrukturu integrovat funkce více modulů IoT Edge a k jejich nasazení do zařízení IoT Edge. Každý program může být zabalen jako modul IoT Edge. Aby bylo možné plně využít výhod IoT Edge komunikace a správy, program spuštěný v modulu může použít sadu SDK pro zařízení Azure IoT k připojení k místnímu centru IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Pomocí centra IoT Edge

Centrum IoT Edge poskytuje dva hlavní funkce: proxy serveru do služby IoT Hub a místní komunikace.

### <a name="iot-hub-primitives"></a>IoT Hub primitiv

IoT Hub vidí instancí modulu analogicky k zařízení, v tom smyslu, že:

* má nevlákenný modul, který je odlišný a izolovaný od [vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a druhý modul je v tomto zařízení vyzdvojený.
* může posílat [zprávy ze zařízení do cloudu](../iot-hub/iot-hub-devguide-messaging.md);
* může přijímat [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) zaměřené na svou identitu.

V současné době moduly nemůžou přijímat zprávy z cloudu na zařízení nebo používat funkci pro nahrání souborů.

Při psaní modulu můžete použít [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) k připojení k centru IoT Edge a využít výše uvedené funkce jako při použití IoT Hub s aplikací pro zařízení. Jediným rozdílem mezi IoT Edge moduly a aplikacemi IoT Device je, že musíte odkazovat na identitu modulu místo identity zařízení.

### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud

Aby bylo možné složitě zpracovávat zprávy typu zařízení-Cloud, služba IoT Edge hub poskytuje deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub. Deklarativní směrování povoluje modulů k zachycení a zpracování zpráv odesílaných ostatní moduly a jejich šíření kanálech složité. Další informace najdete v tématu [nasazení modulů a vytváření tras v IoT Edge](module-composition.md).

IoT Edge modul, na rozdíl od normální aplikace IoT Hub zařízení, může přijímat zprávy typu zařízení-Cloud, které jsou proxy servery, které jsou proxy prostřednictvím místního centra IoT Edge, aby je bylo možné zpracovat.

Centrum IoT Edge šíří zprávy do vašeho modulu na základě deklarativních tras popsaných v [manifestu nasazení](module-composition.md). Při vývoji modul IoT Edge, mohou přijímat tyto zprávy nastavením obslužné rutiny zpráv.

Pro zjednodušení vytváření tras IoT Edge přidává koncept *vstupních* a *výstupních* koncových bodů modulu. Modul může přijímat všechny zprávy typu zařízení cloud směrovat bez zadání jakékoli vstup a odesílat zprávy typu zařízení cloud bez zadání žádný výstup. Pomocí explicitní vstupy a výstupy, ale zjednodušuje pravidla směrování pochopit.

Zprávy typu zařízení cloud zpracovat Centrum Edge se nakonec razítkem s následujícími vlastnostmi systému:

| Vlastnost | Popis |
| -------- | ----------- |
| $connectionDeviceId | ID zařízení klienta, který zprávu odeslal |
| $connectionModuleId | ID modulu modulu, který zprávu odeslal |
| $inputName | Vstup, který se tato zpráva zobrazila. Může být prázdný. |
| $outputName | Výstup používá k odeslání zprávy. Může být prázdný. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Připojení k centru IoT Edge z modulu

Připojení k místní Centrum IoT Edge z modulu zahrnuje dva kroky:

1. Vytvořte v aplikaci instanci ModuleClient.
2. Zajistěte, aby že vaše aplikace přijímá certifikát předložený Centrum IoT Edge na tomto zařízení.

Vytvořte instanci ModuleClient pro připojení modulu k rozbočovači IoT Edge běžícímu na zařízení, podobně jako instance DeviceClient připojují zařízení IoT k IoT Hub. Další informace o třídě ModuleClient a jejích metodách komunikace najdete v referenčních informacích k rozhraní API pro preferovaný [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)jazyk sady SDK:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)nebo [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Podpora jazyků a architektur

IoT Edge podporuje různé operační systémy, architektury zařízení a vývojové jazyky, abyste mohli sestavit scénář vyhovující vašim potřebám. V této části můžete porozumět vašim možnostem při vývoji vlastních IoT Edgech modulů. Další informace o podpoře a požadavcích nástrojů pro jednotlivé jazyky získáte v [přípravě vývojového a testovacího prostředí pro IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

Pro všechny jazyky v následující tabulce IoT Edge podporuje vývoj pro zařízení AMD64 a ARM32 Linux.

| Jazyk vývoje | Vývojářské nástroje |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Podpora pro vývoj a ladění pro zařízení s ARM64 Linux je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace najdete v tématu [vývoj a ladění ARM64 IoT Edgech modulů v Visual Studio Code (Preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Pro všechny jazyky v následující tabulce IoT Edge podporuje vývoj pro zařízení AMD64 s Windows.

| Jazyk vývoje | Vývojářské nástroje |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (žádné možnosti ladění)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Další kroky

[Příprava vývojového a testovacího prostředí pro IoT Edge](development-environment.md)

[Vývoj C# modulů pro IoT Edge pomocí sady Visual Studio](how-to-visual-studio-develop-module.md)

[Použití Visual Studio Code k vývoji modulů pro IoT Edge](how-to-vs-code-develop-module.md)

[Pochopení a používání sad SDK pro Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md)
