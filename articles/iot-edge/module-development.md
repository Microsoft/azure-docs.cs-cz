---
title: Vývoj modulů pro Azure IoT Edge | Dokumenty společnosti Microsoft
description: Vývoj vlastních modulů pro Azure IoT Edge, které můžou komunikovat s runtime a IoT Hubem
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271379"
---
# <a name="develop-your-own-iot-edge-modules"></a>Vyvíjejte si vlastní moduly IoT Edge

Moduly Azure IoT Edge se můžou spojit s dalšími službami Azure a přispět k většímu datovému kanálu cloudu. Tento článek popisuje, jak můžete vyvíjet moduly pro komunikaci s runtime IoT Edge a IoT Hub a proto zbytek cloudu Azure.

## <a name="iot-edge-runtime-environment"></a>Runtime prostředí IoT Edge

Modul runtime IoT Edge poskytuje infrastrukturu pro integraci funkcí více modulů IoT Edge a jejich nasazení do zařízení IoT Edge. Libovolný program může být zabalen jako modul IoT Edge. Chcete-li plně využít funkce komunikace a správy IoT Edge, program spuštěný v modulu můžete použít Azure IoT Device SDK pro připojení k místnímu centru IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Použití centra IoT Edge

Centrum IoT Edge poskytuje dvě hlavní funkce: proxy server služby IoT Hub a místní komunikaci.

### <a name="iot-hub-primitives"></a>Primitiva ioT Hubu

IoT Hub vidí instanci modulu analogicky k zařízení v tom smyslu, že:

* má dvojče modulu, které je odlišné a izolované od [dvojčete zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a ostatních dvojčat modulu tohoto zařízení;
* může [odesílat zprávy mezi zařízeními](../iot-hub/iot-hub-devguide-messaging.md)a cloudy ;
* může přijímat [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) zaměřené konkrétně na jeho identitu.

V současné době moduly nemohou přijímat zprávy z cloudu na zařízení ani používat funkci nahrávání souborů.

Při psaní modulu můžete použít [sdk zařízení Azure IoT k](../iot-hub/iot-hub-devguide-sdks.md) připojení k centru IoT Edge a použití výše uvedených funkcí, jako byste při používání služby IoT Hub s aplikací zařízení. Jediný rozdíl mezi moduly IoT Edge a aplikacemi zařízení IoT je, že musíte odkazovat na identitu modulu namísto identity zařízení.

### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud

Chcete-li povolit komplexní zpracování zpráv mezi zařízeními cloud, služba IoT Edge hub poskytuje deklarativní směrování zpráv mezi moduly a mezi moduly a službou IoT Hub. Deklarativní směrování umožňuje modulům zachytit a zpracovat zprávy odeslané jinými moduly a šířit je do složitých kanálů. Další informace najdete v [tématu nasazení modulů a vytvoření tras v IoT Edge](module-composition.md).

Modul IoT Edge, na rozdíl od běžné aplikace zařízení Služby IoT Hub, může přijímat zprávy zařízení cloud, které jsou proxied jeho místní centrum IoT Edge pro jejich zpracování.

Centrum IoT Edge šíří zprávy do modulu na základě deklarativních tras popsaných v [manifestu nasazení](module-composition.md). Při vývoji modulu IoT Edge můžete přijímat tyto zprávy nastavením obslužné rutiny zpráv.

Pro zjednodušení vytváření tras přidává IoT Edge koncept *vstupních* a *výstupních* koncových bodů modulu. Modul může přijímat všechny zprávy typu zařízení cloud, které jsou do něj směrovány, bez zadání jakéhokoli vstupu, a může odesílat zprávy typu zařízení cloud bez zadání výstupu. Použití explicitní vstupy a výstupy, i když umožňuje pravidla směrování jednodušší pochopit.

Nakonec jsou zprávy mezi zařízeními a cloudy zpracované centrem Edge označeny následujícími vlastnostmi systému:

| Vlastnost | Popis |
| -------- | ----------- |
| $connectionDeviceId | ID zařízení klienta, který zprávu odeslal |
| $connectionModuleId | ID modulu, který zprávu odeslal |
| $inputName | Vstup, který obdržel tuto zprávu. Může být prázdný. |
| $outputName | Výstup použitý k odeslání zprávy. Může být prázdný. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Připojení k rozbočovači IoT Edge z modulu

Připojení k místnímu rozbočovači IoT Edge z modulu zahrnuje dva kroky:

1. Vytvořte instanci ModuleClient ve vaší aplikaci.
2. Ujistěte se, že vaše aplikace přijímá certifikát předložený centrem IoT Edge na tomto zařízení.

Vytvořte instanci ModuleClient pro připojení modulu k rozbočovači IoT Edge spuštěnému na zařízení, podobně jako instance DeviceClient připojují zařízení IoT k centru IoT Hub. Další informace o třídě ModuleClient a jejích komunikačních metodách naleznete v odkazu rozhraní API pro upřednostňovaný jazyk sady SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)nebo [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Podpora jazyka a architektury

IoT Edge podporuje více operačních systémů, architektury zařízení a vývojové jazyky, takže můžete vytvořit scénář, který odpovídá vašim potřebám. V této části můžete pochopit možnosti vývoje vlastních modulů IoT Edge. Další informace o podpoře nástrojů a požadavcích na jednotlivé jazyky najdete v [aplikaci Příprava vývojového a testovacího prostředí pro IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

Pro všechny jazyky v následující tabulce podporuje IoT Edge vývoj pro zařízení AMD64 a ARM32 Linux.

| Vývojový jazyk | Vývojářské nástroje |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Vyvíjet a ladit podporu pro zařízení ARM64 Linux je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace naleznete v [tématu Vývoj a ladění modulů ARM64 IoT Edge v kódu Visual Studio (preview).](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

### <a name="windows"></a>Windows

Pro všechny jazyky v následující tabulce podporuje IoT Edge vývoj pro zařízení AMD64 se systémem Windows.

| Vývojový jazyk | Vývojářské nástroje |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (bez možností ladění)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Další kroky

[Příprava vývojového a testovacího prostředí pro IoT Edge](development-environment.md)

[Použití Visual Studia k vývoji modulů C# pro IoT Edge](how-to-visual-studio-develop-module.md)

[Použití kódu Visual Studio k vývoji modulů pro IoT Edge](how-to-vs-code-develop-module.md)

[Principy a používání sad SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md)
