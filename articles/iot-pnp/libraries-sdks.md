---
title: technologie Plug and Play knihovny a sady SDK pro IoT
description: Informace o knihovnách zařízení a služeb, které jsou k dispozici pro vývoj řešení s podporou IoT technologie Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064331"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>technologie Plug and Play knihovny a sady SDK pro IoT

Knihovny technologie Plug and Play IoT a sady SDK umožňují vývojářům vytvářet řešení IoT s využitím nejrůznějších programovacích jazyků na různých platformách. Následující tabulka obsahuje odkazy na ukázky a rychlé starty, které vám pomůžou začít:

## <a name="microsoft-supported-libraries-and-sdks"></a>Knihovny a sady SDK podporované společností Microsoft

| Platforma | Knihovna/balíček | Zdrojový kód | Ukázka | Rychlý start | Referenční informace |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Sada SDK pro zařízení v apt – get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHubu](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitální zdvojené ukázky klientů](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Referenční informace](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Sada SDK pro zařízení v Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHubu](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitální zdvojené ukázky klientů](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Referenční informace](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Sada SDK pro zařízení při vložení](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHubu](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitální zdvojené ukázky klientů](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referenční informace](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Sada SDK pro zařízení v integrovaném vývojovém prostředí Arduino](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHubu](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitální zdvojené ukázky klientů](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referenční informace](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Sada SDK pro zařízení v CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHubu](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Digitální zdvojené ukázky klientů](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referenční informace](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHubu](https://github.com/Azure/azure-iot-sdk-csharp) | [Digitální zdvojené ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Referenční informace](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHubu](https://github.com/Azure/azure-iot-sdk-java) | [Digitální zdvojené ukázky](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-java.md) | [Referenční informace](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHubu](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Digitální zdvojené ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-node.md) | [Referenční informace](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Podpora IoT Hub

Možnosti zařízení IoT technologie Plug and Play podporují jenom [bezplatné rozbočovače IoT a úrovně Standard](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Další kroky

Kromě sad SDK a knihoven zařízení můžete k interakci s úložišti modelu použít rozhraní REST API.