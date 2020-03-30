---
title: Připojení zařízení a příchozí přenos dat telemetrie – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Zjistěte, jak se připojit, připojit a odesílat telemetrii ze zařízení IoT v Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862471"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Možnosti připojení zařízení a příchozí přenos telemetrických dat

Telemetrická data odeslaná zařízeními a senzory tvoří páteř jakéhokoli řešení IoT. Jak reprezentovat tyto různé prostředky a spravovat je v kontextu umístění jsou hlavní problémy ve vývoji aplikací IoT. Azure Digital Twins zjednodušuje proces vývoje řešení IoT sjednocením zařízení a senzorů pomocí grafu prostorové inteligence.

Chcete-li začít, vytvořte prostředek služby Azure IoT Hub v kořenovém adresáři prostorového grafu. Prostředek ioT hub umocní všechna zařízení pod kořenovým prostorem k odesílání zpráv. Po vytvoření služby IoT Hub zaregistrujte zařízení pomocí senzorů v rámci instance Digitální dvojčata. Zařízení můžou odesílat data do služby Digital Twins prostřednictvím [sady Azure IoT device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Podrobný návod, jak přenést zařízení na palubu, [načtete-li se v kurzu nasazení a konfigurace digitálních dvojčat](tutorial-facilities-setup.md). Na první pohled jsou kroky:

- Nasazení instance Digitální dvojčata z [webu Azure Portal](https://portal.azure.com).
- Vytvořte mezery v grafu.
- Vytvořte prostředek centra IoT Hub a přiřaďte ho k místu v grafu.
- Vytvořte zařízení a senzory v grafu a přiřaďte je k mezerám vytvořeným v předchozích krocích.
- Vytvořte matcher pro filtrování telemetrických zpráv na základě podmínek.
- Vytvořte [uživatelem definovanou funkci](concepts-user-defined-functions.md)a přiřaďte ji do mezery v grafu pro vlastní zpracování telemetrických zpráv.
- Přiřaďte roli, která umožní uživatelem definované funkci přístup k datům grafu.
- Získejte připojovací řetězec zařízení služby IoT Hub z rozhraní API pro správu digitálních dvojčat.
- Nakonfigurujte připojovací řetězec zařízení na zařízení pomocí sady Azure IoT device SDK.

V následujících částech se dozvíte, jak získat připojovací řetězec zařízení služby IoT Hub z rozhraní API pro správu digitálních dvojčat. Dozvíte se také, jak používat formát zpráv služby IoT Hub telemetrická data k odesílání telemetrie založené na senzorech. Digitální dvojčata vyžaduje každý kus telemetrie, který obdrží být spojeny se senzorem v rámci prostorového grafu. Tento požadavek zajišťuje, že data jsou zpracována a směrována v příslušném prostorovém kontextu.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Získání připojovacího řetězce zařízení služby IoT Hub z rozhraní API pro správu

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Do get volání na rozhraní `includes=ConnectionString` API zařízení s parametrem získat připojení řetězce zařízení služby IoT Hub. Chcete-li dané zařízení najít, můžete filtrovat podle identifikátoru GUID zařízení nebo ID hardwaru.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_DEVICE_GUID* | ID zařízení |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | ID hardwaru zařízení |

V datové části odpovědi zkopírujte vlastnost **connectionString** zařízení. Použijete ji při volání zařízení Azure IoT SDK k odesílání dat do digitálních dvojčat.

## <a name="device-to-cloud-message"></a>Zpráva mezi zařízeními a cloudy

Můžete přizpůsobit formát zpráv a datovou část zařízení tak, aby vyhovovala potřebám vašeho řešení. Použijte libovolnou datovou smlouvu, kterou lze serializovat do bajtového pole nebo datového proudu podporovaného [třídou Zprávy klienta zařízení Azure IoT, Message(byte[] byteArray).](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) Zpráva může být vlastní binární formát podle vašeho výběru, tak dlouho, dokud dekódovat kontrakt dat v odpovídající uživatelem definované funkce. Existuje pouze jeden požadavek na zprávu zařízení cloud. Udržovat sadu vlastností a ujistěte se, že vaše zpráva je směrována odpovídajícím způsobem do zpracovatelského stroje.

### <a name="telemetry-properties"></a>Vlastnosti telemetrie

 Datový obsah **message** může být libovolná data až 256 kB ve velikosti. Existuje několik požadavků očekávaných pro [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) vlastnosti typu. V tabulce jsou uvedeny požadované a volitelné vlastnosti podporované systémem.

| Název vlastnosti | Hodnota | Požaduje se | Popis |
|---|---|---|---|
| **DigitalTwins-Telemetrie** | 1.0 | Ano | Konstantní hodnota, která identifikuje zprávu do systému. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Ano | Jedinečný identifikátor senzoru, který odešle **zprávu**. Tato hodnota musí odpovídat vlastnosti **HardwareId** objektu, aby ji systém mohl zpracovat. Například, `00FF0643BE88-CO2`. |
| **Creationtimeutc** | `string` | Ne | Řetězec data ve formátu [ISO 8601,](https://www.iso.org/iso-8601-date-and-time-format.html) který identifikuje čas vzorkování datové části. Například, `2018-09-20T07:35:00.8587882-07:00`. |
| **Correlationid** | `string` | Ne | UUID, které se používá ke sledování událostí v celém systému. Například, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Odeslání zprávy digitálním dvojčatům

Pomocí volání DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) nebo [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) odešlete zprávu digitálním dvojčatům.

## <a name="next-steps"></a>Další kroky

- Informace o zpracování dat Azure Digital Twins a možnostech uživatelem definovaných funkcí najdete v článku [Zpracování dat Azure Digital Twins a uživatelem definované funkce](concepts-user-defined-functions.md).
