---
title: Seznamy SDK Azure IoT | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – informace o různých sadách SDK zařízení a služeb Azure IoT a odkazy na ně, které můžete použít k vytváření aplikací pro zařízení a back-endových aplikací.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom: mqtt
ms.openlocfilehash: 68186e777051e77deca7b6e5891eb392cac23260
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729040"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Principy a používání sad SDK služby Azure IoT Hub

Existují dvě kategorie sad pro vývoj softwaru (SDK) pro práci s IoT Hub:

* **Sady SDK služby IoT Hub** umožňují vytvářet aplikace spuštěné na vašich zařízeních IoT pomocí klienta nebo klienta modulu zařízení. Tyto aplikace odesílají telemetrická data do centra IoT a volitelně můžou z centra IoT přijímat zprávy, úlohy, metody nebo aktualizace dvojčat.  Klient modulu můžete také použít k vytváření [modulů](../iot-edge/iot-edge-modules.md) pro [moduly Azure IoT Edge runtime](../iot-edge/about-iot-edge.md).

* **Sady SDK služby IoT Hub** umožňují vytvářet back-endové aplikace pro správu centra IoT hub a volitelně odesílat zprávy, plánovat úlohy, vyvolávat přímé metody nebo odesílat požadované aktualizace vlastností do zařízení nebo modulů IoT.

Kromě toho také poskytujeme sadu sad SDK pro práci se [službou zřizování zařízení](../iot-dps/about-iot-dps.md).
* **Zřizovací sady SDK zařízení** umožňují vytvářet aplikace, které běží na vašich zařízeních IoT pro komunikaci se službou zřizování zařízení.

* **Zřizovací sady SDK služby** umožňují vytvářet back-endové aplikace pro správu registrací ve službě Device Provisioning Service.

Seznamte se s [výhodami vývoje pomocí sad Azure IoT SDK](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Kompatibilita platformy operačního systému a hardwaru

Podporované platformy pro sady SDK najdete v [podpoře platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md).

Další informace o kompatibilitě sady SDK s konkrétními hardwarovými zařízeními najdete v [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/) nebo v jednotlivých úložištích.

## <a name="azure-iot-hub-device-sdks"></a>Sady SDK služby Azure IoT Hub

Sady SDK zařízení Microsoft Azure IoT obsahují kód, který usnadňuje vytváření aplikací, které se připojují ke službám Azure IoT Hub a jsou jimi spravované.

Sada Azure IoT Hub SDK pro .NET: 

* Stáhnout z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Obor názvů je Microsoft.Azure.Devices.Clients, který obsahuje klienty zařízení služby IoT Hub (DeviceClient, ModuleClient).
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Odkaz na modul](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Sada Azure IoT Hub pro C (ANSI C – C99):

* Instalace z [apt-get, MBED, Arduino IDE nebo iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilace sady SDK zařízení C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Odkaz na modul](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Přenesení sady C SDK na jiné platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Vývojářská dokumentace](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) pro informace o křížové kompilaci, začínáme na různých platformách atd.
* [Informace o spotřebě prostředků služby Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Sada Azure IoT Hub SDK pro Jazyk Java: 

* Přidat do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Odkaz na modul](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Sada Azure IoT Hub pro soubor Node.js: 

* Instalace z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Odkaz na modul](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Sada Azure IoT Hub SDK pro Python: 

* Instalace z [pipu](https://pypi.org/project/azure-iot-device/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/python/api/azure-iot-device)

Sada Azure IoT Hub Pro SDK pro iOS: 

* Instalace z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Odkaz na rozhraní API: viz [odkaz na rozhraní C API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Sady SDK služby Azure IoT Hub

Sady SDK služby Azure IoT obsahují kód, který usnadňuje vytváření aplikací, které přímo spolupracují s ioT hubem pro správu zařízení a zabezpečení.

Sada Azure IoT Hub Service SDK pro rozhraní .NET:

* Stáhnout z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Obor názvů je Microsoft.Azure.Devices, který obsahuje klienty služby IoT Hub (RegistryManager, ServiceClients).
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Sada Azure IoT Hub Service SDK pro Jazyk Java: 

* Přidat do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Sada Azure IoT Hub Service SDK pro soubor Node.js: 

* Stáhnout z [npm](https://www.npmjs.com/package/azure-iothub)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Sada Azure IoT Hub SDK pro Python: 

* Stáhnout z [pipu](https://pypi.python.org/pypi/azure-iot-hub/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python/tree/master)

Sada Azure IoT Hub Service SDK pro C: 

* Stáhnout z [apt-get, MBED, Arduino IDE nebo NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)

Sada Azure IoT Hub Service SDK pro iOS: 

* Instalace z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Informace o používání správců balíčků pro jazyk a platformu k instalaci binárních souborů a závislostí na vývojovém počítači najdete v souborech readme v úložišti GitHub.

## <a name="microsoft-azure-provisioning-sdks"></a>Sady SDK zřizování Microsoft Azure

Sady **Microsoft Azure Provisioning SDK** umožňují zřídit zařízení do služby IoT Hub pomocí [služby Device Provisioning Service](../iot-dps/about-iot-dps.md).

Sady SDK zařízení a služeb Azure Provisioning pro c#:

* Stáhněte si ze [sady Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) a [service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) z NuGet.
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp/)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Sady SDK zřizovacího zařízení a služeb Azure pro C:

* Instalace z [apt-get, MBED, Arduino IDE nebo iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Sady SDK zřizovacích zařízení a služeb Azure pro Jazyk Java:

* Přidat do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Sady SDK zařízení azure provisioning zařízení a služeb pro soubor Node.js:

* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [referenční dokumentace k rozhraní API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Stáhnout [sadu SDK a](https://badge.fury.io/js/azure-iot-provisioning-device) [service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) z npm

Sady SDK zřizovacích zařízení a služeb Azure pro Python:

* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)
* Stáhnout [sady Device SDK](https://pypi.org/project/azure-iot-device/) a [service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) z pipu

## <a name="next-steps"></a>Další kroky

Sady Azure IoT SDK také poskytují sadu nástrojů, které vám pomohou s vývojem:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): nástroj příkazového řádku pro různé platformy, který pomáhá diagnostikovat problémy související s připojením k IoT Hubu.
* [azure-iot-explorer](https://github.com/Azure/azure-iot-explorer): desktopová aplikace pro různé platformy pro připojení k centru IoT Hub a přidávání/správu/komunikaci se zařízeními IoT.

Příslušné dokumenty související s vývojem pomocí sad Azure IoT SDK:
* Přečtěte [si, jak spravovat připojení a spolehlivé zasílání zpráv](iot-hub-reliability-features-in-sdks.md) pomocí sad SDK služby IoT Hub.
* Přečtěte si, jak [se vyvíjet pro mobilní platformy,](iot-hub-how-to-develop-for-mobile-devices.md) jako je iOS a Android.
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)


Mezi další referenční témata v tomto průvodci vývojáři služby IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md)
* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md)
* [Kvóty a omezování](iot-hub-devguide-quotas-throttling.md)
* [Podpora služby IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Odkaz na rozhraní REST centra IoT](/rest/api/iothub/)
