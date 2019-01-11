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
ms.openlocfilehash: 3bdb09de04ec5041d671f5af709a1a1e7133d141
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199506"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Pochopit a používat sady SDK služby Azure IoT Hub

Existují dvě kategorie software development Kit (SDK) pro práci se službou IoT Hub:

* **Sady SDK služby IoT Hub zařízení** umožňují vývoj aplikací pro zařízení IoT pomocí klienta zařízení nebo modul klienta. Tyto aplikace odesílat telemetrická data do služby IoT hub a volitelně přijímat zprávy, úlohu, metody nebo aktualizace dvojčat ze služby IoT hub.  Modul klienta můžete použít také k vytváření [moduly](../iot-edge/iot-edge-modules.md) pro [modul runtime Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **Sady SDK služby IoT Hub** povolit vám umožní vytvářet back-endové aplikace ke správě služby IoT hub a volitelně zasílání zpráv, plánování úloh, vyvolání přímých metod nebo odeslání požadovanou vlastnost aktualizací do zařízení IoT nebo moduly.

Kromě toho jsme také poskytují sadu SDK pro práci s [služby Device Provisioning](../iot-dps/about-iot-dps.md).
* **Zřizování sady SDK pro zařízení** můžete sestavovat aplikace, které běží na zařízeních IoT ke komunikaci se službou Device Provisioning.

* **Sady SDK služby zřizování** umožňují vytvářet back-endové aplikace spravovat vaše registrace ve službě Device Provisioning.

Další informace o [výhodami vývoje pomocí sad Azure IoT SDK](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Kompatibilita operačního systému platformy a hardwaru

Podporované platformy pro sady SDK najdete v [podpora platformy sady SDK Azure IoT](iot-hub-device-sdk-platform-support.md).

Další informace o kompatibilitě sady SDK s konkrétním hardwarovým zařízením, najdete v článku [programu Azure Certified for IoT zařízení katalogu](https://catalog.azureiotsuite.com/) nebo na individuální úložiště.

## <a name="azure-iot-hub-device-sdks"></a>Sady SDK pro zařízení Azure IoT Hub

Zařízení Microsoft Azure IoT SDK obsahovat kód, který usnadňuje vytváření aplikací, připojení k a jsou spravované pomocí služby Azure IoT Hub.

Azure IoT Hub device sady SDK pro .NET: 

* Stáhnout z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Obor názvů je Microsoft.Azure.Devices.Clients, která obsahuje klienty zařízení centra IoT (DeviceClient, ModuleClient).
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Odkaz na modul](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Azure IoT Hub device sady SDK pro jazyk C (ANSI C - C99):

* Nainstalujte z [apt-get, MBED, rozhraním Arduino IDE nebo iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilace sady SDK pro zařízení jazyka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Odkaz na modul](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Portování C SDK pro jiné platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentace pro vývojáře](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) o křížové kompilace, Začínáme na různých platformách, atd.

Azure IoT Hub device sady SDK pro Javu: 

* Přidat do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projektu
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Odkaz na modul](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Azure IoT Hub device sady SDK pro Node.js: 

* Nainstalujte z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Odkaz na modul](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub device sady SDK pro Python: 

* Nainstalujte z [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)
* Reference k rozhraní API: viz [reference k rozhraní API jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure IoT Hub device sady SDK pro iOS: 

* Nainstalujte z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Reference k rozhraní API: viz [reference k rozhraní API jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Sady SDK pro služby Azure IoT Hub

Sady SDK služby Azure IoT obsahují kód pro usnadnění vytváření aplikací, které spolupracují přímo se službou IoT Hub ke správě zařízení a zabezpečení.

Azure IoT Hub služba SDK pro .NET:

* Stáhnout z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Obor názvů je Microsoft.Azure.Devices, která obsahuje klienty služby Centrum IoT (RegistryManager, ServiceClients).
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub sady SDK služby pro Javu: 

* Přidat do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projektu
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub sady SDK služby pro Node.js: 

* Stáhnout z [npm](https://www.npmjs.com/package/azure-iothub)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Azure IoT Hub sady SDK služby pro jazyk Python: 

* Stáhnout z [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)

Azure IoT Hub sady SDK služby pro jazyk C: 

* Stáhnout z [apt-get, MBED, rozhraním Arduino IDE nebo Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub služba SDK pro iOS: 

* Nainstalujte z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Prohlédněte si soubory readme v úložištích GitHub. informace o používání jazyka a správců balíčků specifických pro platformu nainstalovat binární soubory a závislosti na vývojovém počítači.

## <a name="microsoft-azure-provisioning-sdks"></a>Sady SDK Microsoft Azure pro zřizování

**Sadami SDK zřizování služby Microsoft Azure** umožňují zřizovat zařízení do služby IoT Hub pomocí [služby Device Provisioning](../iot-dps/about-iot-dps.md).

Azure zřizování zařízení a služby sady SDK pro jazyk C#:

* Stáhnout z [sada SDK zařízení](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) a [služby SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) z NuGet.
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Azure zřizování zařízení a služby sady SDK pro jazyk C:

* Nainstalujte z [apt-get, MBED, rozhraním Arduino IDE nebo iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure zřizování zařízení a služby sady SDK pro Javu:

* Přidat do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projektu
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Azure zřizování zařízení a služby sady SDK pro Node.js:

* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Stáhněte si [sady SDK pro zařízení](https://badge.fury.io/js/azure-iot-provisioning-device) a [sady SDK služby](https://badge.fury.io/js/azure-iot-provisioning-service) z npm

Azure zřizování zařízení a služby sady SDK pro Python:

* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)
* Stáhněte si [sady SDK pro zařízení](https://pypi.org/project/azure-iot-provisioning-device-client/) a [sady SDK služby](https://pypi.org/project/azure-iothub-provisioningserviceclient/) pomocí pip

## <a name="next-steps"></a>Další postup

Sady SDK Azure IoT také poskytují sadu nástrojů, které pomůžou s vývojem:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): související nástroje příkazového řádku pro různé platformy pro usnadnění diagnostiky potíží s připojením službou IoT Hub.
* [Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): aplikace klasické pracovní plochy Windows k připojení ke službě IoT Hub.

Relevantní dokumentace související s vývojem pomocí sad SDK Azure IoT:
* Další informace o [Správa připojení a spolehlivé zasílání zpráv](iot-hub-reliability-features-in-sdks.md) pomocí sady SDK služby IoT Hub.
* Další informace o tom, jak [vývoj pro mobilní platformy](iot-hub-how-to-develop-for-mobile-devices.md) jako je iOS a Android.
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)


Další referenční témata v této příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md)
* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md)
* [Kvóty a omezování](iot-hub-devguide-quotas-throttling.md)
* [Podpora MQTT centra IoT](iot-hub-mqtt-support.md)
* [Reference k rozhraní API REST centra IoT](/rest/api/iothub/)
