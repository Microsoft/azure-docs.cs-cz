---
title: Pochopení SDK služby Azure IoT | Microsoft Docs
description: Příručka vývojáře – informace a odkazy na různých Azure IoT zařízení a služby sadách SDK, které můžete použít k vytváření aplikací pro zařízení a aplikací back-end.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: dobett
ms.openlocfilehash: 31fed4ad1c9a790f771f8943775804976070fc0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633379"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Rady pro pochopení a použití sady SDK služby Azure IoT Hub

Existují dvě kategorie software development Kit (SDK) pro práci se službou IoT Hub:

* **Sady SDK zařízení** umožňují vytvářet aplikace, které běží na zařízení IoT. Tyto aplikace odesílat telemetrická data do služby IoT hub a volitelně přijímat zprávy, úlohy, metoda nebo twin aktualizací ze služby IoT hub.

* **Služba SDK** vám umožní spravovat služby IoT hub a volitelně odesílat zprávy, plánování úloh, volat metody pro přímý nebo odeslání požadovanou vlastnost aktualizací do zařízení IoT.

Další informace o výhodách vývoj pomocí sady SDK služby Azure IoT [sem][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure SDK zařízení IoT

Microsoft Azure IoT zařízení sady SDK obsahovat kód, který usnadňuje vytváření zařízení a aplikací, které se připojují k a spravuje služby Azure IoT Hub.

Azure IoT Hub zařízení SDK pro .NET: 
* Nainstalujte z [Nuget][lnk-nuget-csharp-device]
* [Zdrojový kód][lnk-dotnet-sdk]
* [Referenční dokumentace rozhraní API][lnk-dotnet-ref]

Azure IoT Hub zařízení SDK C: napsané v jazyce ANSI C (C99) přenositelnost široký kompatibilitu a platformy
* Nainstalujte z [výstižný get MBED, Arduino IDE nebo Nuget][lnk-c-package]
* [Zdrojový kód][lnk-c-sdk]
* [Referenční dokumentace rozhraní API][lnk-c-ref]

Azure IoT Hub zařízení SDK pro jazyk Java: 
* Přidejte do [Maven] [ lnk-maven-device] projektu
* [Zdrojový kód][lnk-java-sdk]
* [Referenční dokumentace rozhraní API][lnk-java-ref]

Azure IoT Hub zařízení SDK pro Node.js: 
* Nainstalujte z [npm][lnk-npm-device]
* [Zdrojový kód][lnk-node-sdk]
* [Referenční dokumentace rozhraní API][lnk-node-ref]

Azure IoT Hub zařízení SDK pro jazyk Python: 
* Nainstalujte z [pip][lnk-pip-device]
* [Zdrojový kód][lnk-python-sdk]

> [!NOTE]
> Prohlédněte si soubory readme v úložišť GitHub pro informace o použití jazyka a specifické pro platformu balíček správci nainstalovat binární soubory a závislosti na vývojovém počítači.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Kompatibilita operačního systému platformy a hardwaru

Další informace o kompatibilitě sady SDK s konkrétním hardwarovým zařízení najdete v tématu [Azure certifikované pro katalog zařízení IoT] [ lnk-certified] nebo jednotlivých úložiště.

## <a name="azure-iot-service-sdks"></a>Služby sady SDK služby Azure IoT

Sady SDK služby Azure IoT obsahovat kód, který usnadňuje vytváření aplikace, které spolupracují přímo službou IoT Hub pro správu zařízení a zabezpečení.

Azure IoT Hub služba SDK pro .NET:
* Stáhnout z [Nuget][lnk-nuget-csharp-service]
* [Zdrojový kód][lnk-dotnet-sdk]
* [Referenční dokumentace rozhraní API][lnk-dotnet-service-ref]

Azure IoT Hub služba SDK pro jazyk Java: 
* Přidejte do [Maven] [ lnk-maven-service] projektu
* [Zdrojový kód][lnk-java-sdk]
* [Referenční dokumentace rozhraní API][lnk-java-service-ref]

Azure IoT Hub služba SDK pro Node.js: 
* Stáhnout z [npm][lnk-npm-service]
* [Zdrojový kód][lnk-node-sdk]
* [Referenční dokumentace rozhraní API][lnk-node-service-ref]

Azure IoT Hub služba SDK pro jazyk Python: 
* Stáhnout z [pip][lnk-pip-service]
* [Zdrojový kód][lnk-python-sdk]

Azure IoT Hub sady SDK služby pro C: 
* Stáhnout z [výstižný get MBED, Arduino IDE nebo Nuget][lnk-c-package]
* [Zdrojový kód][lnk-c-sdk]

> [!NOTE]
> Prohlédněte si soubory readme v úložišť GitHub pro informace o použití jazyka a specifické pro platformu balíček správci nainstalovat binární soubory a závislosti na vývojovém počítači.


## <a name="next-steps"></a>Další postup

Zahrnout další referenční témata v této příručce pro vývojáře IoT Hub:

* [Koncové body centra IoT][lnk-devguide-endpoints]
* [IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv][lnk-devguide-query]
* [Kvóty a omezení][lnk-devguide-quotas]
* [Podpora MQTT centra IoT][lnk-devguide-mqtt]
* [Referenční dokumentace rozhraní API REST centra IoT][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
