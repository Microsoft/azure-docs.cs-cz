---
title: Připojení zařízení a nepříjem telemetrie – digitální vlákna Azure Microsoft Docs
description: Naučte se, jak připojit, integrovat a poslat telemetrii ze zařízení IoT v Azure Digital provlákna.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: f9f0a74a6ca57f90ed8bd217d0d2f57e4bc16749
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660337"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Možnosti připojení zařízení a příchozí přenos telemetrických dat

Data telemetrie odesílaná zařízeními a snímači tvoří páteřní síť řešení IoT. Způsob reprezentace těchto různých prostředků a jejich správa v kontextu umístění jsou hlavními aspekty vývoje aplikací pro IoT. Funkce digitálních vláken Azure zjednodušuje proces vývoje řešení IoT pomocí jednotkových zařízení a senzorů pomocí grafu prostorových informací.

Začněte tím, že vytvoříte prostředek služby Azure IoT Hub v kořenovém adresáři prostorového grafu. Prostředek IoT Hub umožňuje všem zařízením pod kořenovým místem odesílat zprávy. Po vytvoření IoT Hub zaregistrujte zařízení se senzory v instanci digitálního vlákna. Zařízení můžou odesílat data do služby digitálního vlákna přes [sadu SDK pro zařízení Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Podrobný návod, jak přenést zařízení do desky, najdete v [kurzu nasazení a konfigurace digitálních vláken](tutorial-facilities-setup.md). Na první pohled je postup:

- Nasaďte z [Azure Portal](https://portal.azure.com)instanci digitálních vláken.
- Vytvořte v grafu mezery.
- Vytvořte prostředek IoT Hub a přiřaďte ho k prostoru v grafu.
- V grafu vytvořte zařízení a senzory a přiřaďte je prostorům vytvořeným v předchozích krocích.
- Vytvoří spárování pro filtrování zpráv telemetrie na základě podmínek.
- Vytvořte [uživatelsky definovanou funkci](concepts-user-defined-functions.md)a přiřaďte ji k prostoru v grafu pro vlastní zpracování zpráv telemetrie.
- Přiřaďte roli tak, aby umožňovala uživatelsky definované funkci přístup k datům grafu.
- Získejte připojovací řetězec IoT Hub zařízení z rozhraní API pro správu digitálních vláken.
- Nakonfigurujte připojovací řetězec zařízení v zařízení pomocí sady SDK pro zařízení Azure IoT.

V následujících částech se dozvíte, jak získat připojovací řetězec IoT Hub zařízení z rozhraní API pro správu digitálních vláken. Naučíte se také, jak pomocí IoT Hubho formátu zprávy telemetrie odeslat telemetrii založenou na senzorech. Digitální vlákna vyžadují, aby každý kus telemetrie, který obdrží, byl přidružen k snímači v prostorovém grafu. Tento požadavek zajistí, aby data byla zpracována a směrována v rámci příslušného prostorového kontextu.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Získání připojovacího řetězce zařízení IoT Hub z rozhraní API pro správu

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Pokud chcete získat připojovací řetězec IoT Hub zařízení, proveďte v rozhraní API zařízení volání GET s parametrem `includes=ConnectionString`. Pokud chcete najít dané zařízení, vyfiltrujte ho podle identifikátoru GUID zařízení nebo ID hardwaru.

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

Do datové části odpovědi zkopírujte vlastnost **ConnectionString** daného zařízení. Použijete ji při volání sady SDK pro zařízení Azure IoT k odesílání dat do digitálních vláken.

## <a name="device-to-cloud-message"></a>Zpráva ze zařízení do cloudu

Formát a datovou část svého zařízení můžete přizpůsobit tak, aby vyhovovaly potřebám vašeho řešení. Použijte všechny kontrakty dat, které je možné serializovat do bajtového pole nebo datového proudu podporovaného [třídou zprávy klienta zařízení Azure IoT, zprávy (Byte [] ByteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Může se jednat o vlastní binární formát, který si vyberete, pokud dekódujete kontrakt dat v odpovídající uživatelsky definované funkci. Pro zprávu typu zařízení-Cloud existuje pouze jeden požadavek. Udržujte sadu vlastností, abyste se ujistili, že je vaše zpráva správně směrována na výpočetní modul.

### <a name="telemetry-properties"></a>Vlastnosti telemetrie

 Obsah datové části **zprávy** může být libovolná data o velikosti až 256 KB. Pro vlastnosti [`Message.Properties`ho](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) typu se očekává několik požadavků. V tabulce jsou uvedeny požadované a volitelné vlastnosti, které systém podporuje.

| Název vlastnosti | Hodnota | Požaduje se | Popis |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Ano | Konstantní hodnota, která identifikuje zprávu pro systém. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Ano | Jedinečný identifikátor senzoru, který **zprávu**odesílá. Tato hodnota musí odpovídat vlastnosti **HardwareId** objektu, aby ji systém mohl zpracovat. Například, `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Ne | Řetězec formátovaného data [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) , který identifikuje dobu vzorkování datové části. Například, `2018-09-20T07:35:00.8587882-07:00`. |
| **ID** | `string` | Ne | Identifikátor UUID, který se používá k trasování událostí v rámci systému. Například, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Odeslání zprávy do digitálních vláken

K odeslání zprávy do digitálních vláken použijte volání DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) nebo [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) .

## <a name="next-steps"></a>Další kroky

- Pokud se chcete dozvědět víc o funkcích pro zpracování dat a uživatelsky definované funkce v Azure, přečtěte si téma [zpracování dat digitálních vláken Azure a uživatelsky definované funkce](concepts-user-defined-functions.md).
