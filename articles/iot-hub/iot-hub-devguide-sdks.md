---
title: Principy sad SDK Azure IoT | Dokumentace Microsoftu
description: Příručka pro vývojáře – informace a odkazy na různé Azure IoT zařízení a služby sady SDK, které můžete použít k vytváření aplikací pro zařízení a back endové aplikace.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: ba06617762650afc8cd3eecb2fcddda6d24f4228
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734986"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Pochopit a používat sady SDK služby Azure IoT Hub

Existují tři kategorie software development Kit (SDK) pro práci se službou IoT Hub:

* **Sady SDK pro zařízení** umožňují vývoj aplikací pro zařízení IoT pomocí klienta zařízení nebo modul klienta. Tyto aplikace odesílat telemetrická data do služby IoT hub a volitelně přijímat zprávy, úlohu, metody nebo aktualizace dvojčat ze služby IoT hub.  Modul klienta můžete použít také k vytváření [moduly](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) pro [modul runtime Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/about-iot-edge).

* **Sady SDK pro služby** vám umožní spravovat vaše Centrum IoT a volitelně zasílání zpráv, plánování úloh, vyvolání přímých metod nebo odeslání požadovanou vlastnost aktualizací do zařízení IoT nebo moduly.

* **Sady SDK zařízení pro zřizování** umožňují zřizovat zařízení do služby IoT Hub pomocí [služby Device Provisioning](../iot-dps/about-iot-dps.md).

Seznamte se s vývojem pomocí sad SDK Azure IoT výhody [tady][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT SDK pro zařízení

Zařízení Microsoft Azure IoT SDK obsahovat kód, který usnadňuje vytváření zařízení a aplikací, připojení k a jsou spravované pomocí služby Azure IoT Hub.

Azure IoT Hub device sady SDK pro .NET: 
* Nainstalujte z [Nuget][lnk-nuget-csharp-device]
* [Zdrojový kód][lnk-dotnet-sdk]
* [Reference k rozhraní API][lnk-dotnet-ref]
* [Odkaz na modul](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

IoT Hub device Azure SDK pro jazyk C: napsané ve standardu ANSI C (C99) pro přenositelnost a kompatibility pro různé platformy
* Nainstalujte z [apt-get, MBED, rozhraním Arduino IDE nebo Nuget][lnk-c-package]
* [Zdrojový kód][lnk-c-sdk]
* [Reference k rozhraní API][lnk-c-ref]
* [Odkaz na modul](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Azure IoT Hub device sady SDK pro Javu: 
* Přidat do [Maven] [ lnk-maven-device] projektu
* [Zdrojový kód][lnk-java-sdk]
* [Reference k rozhraní API][lnk-java-ref]
* [Odkaz na modul](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

Azure IoT Hub device sady SDK pro Node.js: 
* Nainstalujte z [npm][lnk-npm-device]
* [Zdrojový kód][lnk-node-sdk]
* [Reference k rozhraní API][lnk-node-ref]
* [Odkaz na modul](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub device sady SDK pro Python: 
* Nainstalujte z [pip][lnk-pip-device]
* [Zdrojový kód][lnk-python-sdk]
* Reference k rozhraní API: viz [reference k rozhraní API jazyka C][lnk-c-ref]

Azure IoT Hub device sady SDK pro iOS: 
* Nainstalujte z [CocoaPod][lnk-cocoa-device]
* [Ukázky][lnk-ios-sample]
* Reference k rozhraní API: viz [reference k rozhraní API jazyka C][lnk-c-ref]

> [!NOTE]
> Prohlédněte si soubory readme v úložištích GitHub. informace o používání jazyka a správců balíčků specifických pro platformu nainstalovat binární soubory a závislosti na vývojovém počítači.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Kompatibilita operačního systému platformy a hardwaru

Podporované platformy pro sady SDK najdete v tomto [dokumentu](iot-hub-device-sdk-platform-support.md).
Další informace o kompatibilitě sady SDK s konkrétním hardwarovým zařízením, najdete v článku [programu Azure Certified for IoT zařízení katalogu] [ lnk-certified] nebo na individuální úložiště.

## <a name="azure-iot-service-sdks"></a>Sady SDK pro služby Azure IoT

Sady SDK služby Azure IoT obsahují kód pro usnadnění vytváření aplikací, které spolupracují přímo se službou IoT Hub ke správě zařízení a zabezpečení.

Azure IoT Hub služba SDK pro .NET:
* Stáhnout z [Nuget][lnk-nuget-csharp-service]
* [Zdrojový kód][lnk-dotnet-sdk]
* [Reference k rozhraní API][lnk-dotnet-service-ref]

Azure IoT Hub sady SDK služby pro Javu: 
* Přidat do [Maven] [ lnk-maven-service] projektu
* [Zdrojový kód][lnk-java-sdk]
* [Reference k rozhraní API][lnk-java-service-ref]

Azure IoT Hub sady SDK služby pro Node.js: 
* Stáhnout z [npm][lnk-npm-service]
* [Zdrojový kód][lnk-node-sdk]
* [Reference k rozhraní API][lnk-node-service-ref]

Azure IoT Hub sady SDK služby pro jazyk Python: 
* Stáhnout z [pip][lnk-pip-service]
* [Zdrojový kód][lnk-python-sdk]

Azure IoT Hub sady SDK služby pro jazyk C: 
* Stáhnout z [apt-get, MBED, rozhraním Arduino IDE nebo Nuget][lnk-c-package]
* [Zdrojový kód][lnk-c-sdk]

Azure IoT Hub služba SDK pro iOS: 
* Nainstalujte z [CocoaPod][lnk-cocoa-service]
* [Ukázky][lnk-ios-sample]

> [!NOTE]
> Prohlédněte si soubory readme v úložištích GitHub. informace o používání jazyka a správců balíčků specifických pro platformu nainstalovat binární soubory a závislosti na vývojovém počítači.

## <a name="device-provisioning-sdks"></a>Zřizování sady SDK pro zařízení

**Sadami SDK zřizování služby Microsoft Azure** umožňují zřizovat zařízení do služby IoT Hub pomocí [služby Device Provisioning](../iot-dps/about-iot-dps.md).

Azure zřizování zařízení a služby sady SDK pro jazyk C#:
* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Azure zřizování zařízení a služby sady SDK pro Javu:
* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning-device-client)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Azure zřizování zařízení a služby sady SDK pro Node.js:
* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Azure zřizování zařízení a služby sady SDK pro Python:
* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Azure zřizování zařízení a služby sady SDK pro jazyk C:
* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning/service)

## <a name="next-steps"></a>Další postup

Sady SDK Azure IoT také poskytují sadu nástrojů, které pomůžou s vývojem:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): související nástroje příkazového řádku pro různé platformy pro usnadnění diagnostiky potíží s připojením službou IoT Hub.
* [Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): aplikace klasické pracovní plochy Windows k připojení ke službě IoT Hub.

Další referenční témata v této příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu][lnk-devguide-endpoints]
* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv][lnk-devguide-query]
* [Kvóty a omezování][lnk-devguide-quotas]
* [Podpora MQTT centra IoT][lnk-devguide-mqtt]
* [Reference k rozhraní API REST centra IoT][lnk-rest-ref]
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)

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
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
[lnk-cocoa-device]: https://cocoapods.org/pods/AzureIoTHubClient
[lnk-ios-sample]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-cocoa-service]: https://cocoapods.org/pods/AzureIoTHubServiceClient
