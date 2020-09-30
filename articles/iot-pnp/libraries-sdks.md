---
title: technologie Plug and Play knihovny a sady SDK pro IoT
description: Informace o knihovnách zařízení a služeb, které jsou k dispozici pro vývoj řešení s podporou IoT technologie Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6ea80c88f2c16614c8568bc6ccfa3f4308e954b0
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577301"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK pro IoT technologie Plug and Play

Knihovny technologie Plug and Play IoT a sady SDK umožňují vývojářům vytvářet řešení IoT s využitím nejrůznějších programovacích jazyků na různých platformách. Následující tabulka obsahuje odkazy na ukázky a rychlé starty, které vám pomůžou začít:

## <a name="device-sdks"></a>Sady SDK pro zařízení

| Jazyk | Balíček | Úložiště kódu | Ukázky | Rychlé zprovoznění | Reference |
|---|---|---|---|---|---|
| C – zařízení | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Ukázky](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device-c.md) | [Odkaz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET – zařízení | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Připojení ke službě IoT Hub](quickstart-connect-device-csharp.md) | [Odkaz](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java – zařízení | [Maven 1.25.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Připojení ke službě IoT Hub](quickstart-connect-device-java.md) | [Odkaz](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python – zařízení | [2.3.0 PIP](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device-python.md) | [Odkaz](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Uzel – zařízení | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device-node.md) | [Odkaz](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |

## <a name="device-sdks-preview"></a>Sady SDK pro zařízení (Preview)

| Jazyk | Úložiště kódu/ukázky |
|---|---|
|Sada Azure SDK pro Embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Middleware Azure RTO IoT| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Úvodní příručky ke službě Azure RTO | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks"></a>Sady SDK pro služby

| Jazyk | Balíček | Úložiště kódu | Ukázky | Rychlé zprovoznění | Reference |
|---|---|---|---|---|---|
| .NET – IoT Hub služba | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | Není k dispozici | [Odkaz](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Služba IoT Hub Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | Není k dispozici | [Odkaz](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Služba IoT Hub Node | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | Není k dispozici | [Odkaz](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Služba Python-Digital s dvojitou čárkou | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interakce s rozhraním API IoT Hub digitálního vlákna](quickstart-service-python.md) | Není k dispozici |
| Služba nedigitálních vláken v uzlu | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interakce s rozhraním API IoT Hub digitálního vlákna](quickstart-service-node.md) | Není k dispozici |

## <a name="next-steps"></a>Další kroky

Chcete-li vyzkoušet sady SDK a knihovny, přečtěte si  [příručku pro vývojáře](concepts-developer-guide-device-csharp.md) a [rychlé starty zařízení](quickstart-connect-device-c.md) a [služby](quickstart-service-node.md).
