---
title: Příručka pro vývojáře služby – IoT technologie Plug and Play | Microsoft Docs
description: Popis technologie Plug and Play IoT pro vývojáře služeb
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f082e4d4c6c71e460842f80a5aa17130b6a41279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614219"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Příručka pro vývojáře služby IoT technologie Plug and Play

IoT technologie Plug and Play umožňuje vytvářet inteligentní zařízení, která budou inzerovat jejich schopnosti aplikacím Azure IoT. Zařízení IoT technologie Plug and Play nevyžadují ruční konfiguraci, když ji zákazník připojí k aplikacím s podporou IoT technologie Plug and Play.

IoT technologie Plug and Play umožňuje používat zařízení, která oznámila ID modelu ve službě IoT Hub. Můžete například získat přímý přístup k vlastnostem a příkazům zařízení.

Pokud chcete použít zařízení IoT technologie Plug and Play připojené ke službě IoT Hub, použijte jednu ze sad SDK služby IoT nebo IoT Hub REST API:

## <a name="service-sdks"></a>Sady SDK pro služby

Pomocí sad SDK služby Azure IoT ve vašem řešení můžete pracovat se zařízeními a moduly. Můžete například použít sady SDK služby ke čtení a aktualizaci dvojitých vlastností a vyvolání příkazů. Mezi podporované jazyky patří C#, Java, Node.js a Python.

Sady SDK služby umožňují přístup k informacím o zařízení z řešení, jako je například Desktop nebo webová aplikace. Sady SDK pro služby zahrnují dva obory názvů a objektové modely, které můžete použít k načtení ID modelu:

- Klient služby IoT Hub. Tato služba zpřístupňuje ID modelu jako vlastnost vlákna zařízení.

- Klient služby digitálního vyzdvojení. Nové digitální vlákna rozhraní API pracuje na špičkových konstrukcích, jako jsou komponenty, vlastnosti a příkazy, které jsou definované digitálním modelem s definicí s definicemi digitálních vláken. Digitální vlákna rozhraní API usnadňují tvůrcům řešení vytváření řešení IoT technologie Plug and Play.

| Platforma | Klient služby IoT Hub | Klient služby digitálního vyzdvojení |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Dokumentace](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Dokumentace](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [ukázky](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Dokumentace](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Ukázka](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Dokumentace](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Ukázka](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Dokumentace](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Ukázka](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Dokumentace](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Ukázka](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="rest-api"></a>REST API

Následující příklady používají REST API IoT Hub k interakci s připojeným zařízením IoT technologie Plug and Play. Aktuální verze rozhraní API je `2020-09-30` . Připojí `?api-version=2020-09-30` se k voláním REST PI.

> [!NOTE]
> Rozhraní API aktuálně nepodporuje vlákna modulu `digitalTwins` .

Pokud se zavolá zařízení termostata `t-123` , zobrazí se všechny vlastnosti všech rozhraní implementovaných vaším zařízením a volání REST API Get:

```REST
GET /digitalTwins/t-123
```

Toto volání bude obsahovat vlastnost JSON `$metadata.$model` s ID modelu oznámeným zařízením.

Všechny vlastnosti pro všechna rozhraní jsou k dispozici pomocí `GET /DigitalTwin/{device-id}` šablony REST API, kde `{device-id}` je identifikátor zařízení:

```REST
GET /digitalTwins/{device-id}
```

Můžete volat přímo technologie Plug and Play příkazy pro zařízení IoT. Pokud `Thermostat` komponenta v `t-123` zařízení obsahuje `restart` příkaz, můžete ji zavolat voláním REST API post:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Obecně lze příkazy volat pomocí této šablony REST API:

- `device-id`: identifikátor zařízení.
- `component-name`: název rozhraní z oddílu Implements v modelu schopností zařízení.
- `command-name`: název příkazu.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o modelování zařízení, tady je několik dalších prostředků:

- [Jazyk DTDL (Digital autodefinition Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Sada SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Komponenty modelu](./concepts-components.md)
- [Instalace a použití nástrojů pro tvorbu DTDL](howto-use-dtdl-authoring-tools.md)
