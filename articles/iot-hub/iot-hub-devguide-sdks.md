---
title: Porozumění sadám SDK pro Azure IoT | Microsoft Docs
description: Příručka pro vývojáře – informace o a odkazy na různé sady SDK pro zařízení a služby Azure IoT, které můžete použít k vytváření aplikací pro zařízení a back-endové aplikace.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 8b3212dd7e1544d734c0f409bd4bcdf8ec22fbc2
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147615"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Principy a použití sad SDK služby Azure IoT Hub.

Pro práci s IoT Hub jsou k dispozici dvě kategorie sad SDK pro vývoj softwaru:

* **IoT Hub sady SDK pro zařízení** umožňují vytvářet aplikace, které se spouštějí na zařízeních IoT pomocí klienta zařízení nebo klienta modulu. Tyto aplikace odesílají telemetrická data do centra IoT a volitelně můžou z centra IoT přijímat zprávy, úlohy, metody nebo aktualizace dvojčat.  K vytváření [modulů](../iot-edge/iot-edge-modules.md) pro modul [runtime Azure IoT Edge](../iot-edge/about-iot-edge.md)můžete použít také klienta modulu.

* Sady **SDK pro IoT Hub služby** umožňují vytvářet back-endové aplikace pro správu služby IoT Hub a volitelně odesílat zprávy, plánovat úlohy, volat přímé metody nebo odesílat požadované aktualizace vlastností zařízením nebo modulům IoT.

Kromě toho poskytujeme také sadu sad SDK pro práci se [službou Device Provisioning](../iot-dps/about-iot-dps.md).

* **Zřizování sad SDK pro zařízení** vám umožní sestavovat aplikace, které běží na zařízeních IoT ke komunikaci se službou Device Provisioning.

* Sady **SDK služby zřizování** umožňují vytvářet back-endové aplikace pro správu registrací ve službě Device Provisioning.

Seznamte se s [výhodami vývoje pomocí sad SDK služby Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Platforma operačního systému a Kompatibilita hardwaru

Podporované platformy pro sady SDK najdete v [podpoře platforem Azure IoT SDK](iot-hub-device-sdk-platform-support.md).

Další informace o kompatibilitě sady SDK s konkrétními hardwarovými zařízeními najdete v [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/) nebo v jednotlivých úložišti.

## <a name="azure-iot-hub-device-sdks"></a>Sady SDK pro zařízení Azure IoT Hub

Sady SDK pro zařízení Microsoft Azure IoT obsahují kód, který usnadňuje sestavování aplikací, které se připojují ke službě Azure IoT Hub a spravuje je.

Sada SDK pro zařízení Azure IoT Hub pro .NET: 

* Stáhněte si z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Obor názvů je Microsoft. Azure. Devices. clients, který obsahuje IoT Hub klientů zařízení (DeviceClient, ModuleClient).
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referenční materiály k rozhraní API](/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Odkaz na modul](/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Sada SDK pro zařízení Azure IoT Hub pro jazyk C (ANSI C-C99):

* Instalace z [apt-get, MBED, Arduino IDE nebo iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilace sady SDK pro zařízení v jazyce C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Referenční materiály k rozhraní API](/azure/iot-hub/iot-c-sdk-ref/)
* [Odkaz na modul](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Přenos sady C SDK na jiné platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentace pro vývojáře](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) pro informace o křížovém kompilování, Začínáme na různých platformách atd.
* [Informace o spotřebě prostředků v Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Sada Azure IoT Hub Device SDK pro jazyk Java:

* Přidat do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [Referenční materiály k rozhraní API](/java/api/com.microsoft.azure.sdk.iot.device)
* [Odkaz na modul](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Sada SDK pro zařízení Azure IoT Hub pro Node.js:

* Instalace z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [Referenční materiály k rozhraní API](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Odkaz na modul](/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Sada SDK pro zařízení Azure IoT Hub pro Python:

* Nainstalovat z [PIP](https://pypi.org/project/azure-iot-device/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)
* [Referenční materiály k rozhraní API](/python/api/azure-iot-device)

Sada SDK pro zařízení Azure IoT Hub pro iOS:

* Instalace z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Reference k rozhraní API: viz [Reference k rozhraní API jazyka C](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Sady SDK pro služby Azure IoT Hub

Sady SDK služby Azure IoT obsahují kód, který usnadňuje sestavování aplikací, které komunikují přímo s IoT Hub pro správu zařízení a zabezpečení.

Sada SDK pro Azure IoT Hub Service pro .NET:

* Stáhněte si z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Obor názvů je Microsoft. Azure. Devices, který obsahuje klienty služby IoT Hub (RegistryManager, ServiceClients).
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referenční materiály k rozhraní API](/dotnet/api/microsoft.azure.devices)

Sada Azure IoT Hub Service SDK pro jazyk Java:

* Přidat do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [Referenční materiály k rozhraní API](/java/api/com.microsoft.azure.sdk.iot.service)

Sada Azure IoT Hub Service SDK pro Node.js:

* Stažení z [npm](https://www.npmjs.com/package/azure-iothub)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [Referenční materiály k rozhraní API](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Sada SDK pro Azure IoT Hub Service pro Python:

* Stáhnout z [PIP](https://pypi.python.org/pypi/azure-iot-hub/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [Referenční materiály k rozhraní API](/python/api/azure-iot-hub)

Sada Azure IoT Hub Service SDK pro jazyk C:

Sada SDK služby Azure IoT pro jazyk C už není v aktivním vývoji.
Budeme dál opravovat kritické chyby, jako jsou havárie, poškození dat a ohrožení zabezpečení. Nebudeme ale přidávat žádné nové funkce ani opravovat chyby, které nejsou důležité.

Podpora sady SDK služby Azure IoT je k dispozici v jazycích vyšší úrovně ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [Node](https://github.com/Azure/azure-iot-sdk-node), [Python](https://github.com/Azure/azure-iot-sdk-python)).

* Stažení z [apt-get, MBED, Arduino IDE nebo NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)

Sada Azure IoT Hub Service SDK pro iOS:

* Instalace z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> V souborech Readme v úložištích GitHub najdete informace o používání správců balíčků pro konkrétní platformu k instalaci binárních souborů a závislostí na vašem vývojovém počítači.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure zřizování sad SDK

Sady **SDK pro Microsoft Azure zřizování** vám umožní zřídit zařízení pro IoT Hub pomocí [služby Device Provisioning](../iot-dps/about-iot-dps.md).

Sady SDK pro zřizování zařízení a služeb pro jazyk C#:

* Stáhněte si ze [sady SDK zařízení](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) a [sady SDK služby](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) z nugetu.
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Referenční materiály k rozhraní API](/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Sady SDK pro zařízení a služby pro zřizování v Azure pro jazyk C:

* Instalace z [apt-get, MBED, Arduino IDE nebo iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Referenční materiály k rozhraní API](/azure/iot-hub/iot-c-sdk-ref/)

Sady SDK pro zařízení a služby pro zřizování v Azure pro Java:

* Přidat do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Referenční materiály k rozhraní API](/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Sady SDK pro zařízení a služby pro zřizování Azure pro Node.js:

* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Referenční materiály k rozhraní API](/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Stažení [sady SDK pro zařízení](https://badge.fury.io/js/azure-iot-provisioning-device) a [sady SDK služby](https://badge.fury.io/js/azure-iot-provisioning-service) z npm

Sady SDK pro zařízení a služby pro Python pro zřizování Azure:

* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)
* Stažení [sady SDK pro zařízení](https://pypi.org/project/azure-iot-device/) a [sady SDK služby](https://pypi.org/project/azure-iothub-provisioningserviceclient/) z PIP

## <a name="next-steps"></a>Další kroky

Sady SDK Azure IoT také poskytují sadu nástrojů, které vám pomůžou s vývojem:

* [iothub-Diagnostics](https://github.com/Azure/iothub-diagnostics): nástroj příkazového řádku pro různé platformy, který vám umožní diagnostikovat problémy související s připojením IoT Hub.
* [Azure-IoT-Explorer](https://github.com/Azure/azure-iot-explorer): desktopová aplikace pro různé platformy pro připojení k vašemu IoT Hub a přidání/Správa/komunikace se zařízeními IoT.

Relevantní dokumentace týkající se vývoje pomocí sad SDK služby Azure IoT:

* Přečtěte si, [jak spravovat připojení a spolehlivé zasílání zpráv](iot-hub-reliability-features-in-sdks.md) pomocí sad IoT Hub SDK.
* Přečtěte si, jak [vyvíjet na mobilních platformách](iot-hub-how-to-develop-for-mobile-devices.md) , jako jsou iOS a Android.
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)

Další referenční témata v tomto IoT Hub příručce pro vývojáře zahrnují:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md)
* [IoT Hub dotazovací jazyk pro vlákna, úlohy a směrování zpráv v zařízeních](iot-hub-devguide-query-language.md)
* [Kvóty a omezování](iot-hub-devguide-quotas-throttling.md)
* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Odkaz na IoT Hub REST API](/rest/api/iothub/)