---
title: Vysvětlení aktualizace zařízení pro konfigurační soubor Azure IoT Hub | Microsoft Docs
description: Pochopení aktualizace zařízení pro konfigurační soubor Azure IoT Hub.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662339"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Aktualizace zařízení pro konfigurační soubor IoT Hub

"adu-conf.txt" je volitelný soubor, který lze vytvořit pro správu následujících konfigurací.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["Manufacturer"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["model"]
* DeviceInformation. Manufacturer
* DeviceInformation. model
* Připojovací řetězec zařízení (pokud ho neznáte agent aktualizace zařízení).

## <a name="purpose"></a>Účel
Agent aktualizace zařízení se nejprve pokusí získat `manufacturer` `model` hodnoty a ze zařízení, které se má použít pro [vrstvu rozhraní](device-update-agent-overview.md#the-interface-layer). Pokud se to nezdaří, agent aktualizace zařízení příště vyhledá soubor adu-conf.txt a použije z něj hodnoty. Pokud se oba pokusy nezdařily, agent aktualizace zařízení použije [výchozí](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) hodnoty.

Přečtěte si další informace o rozhraní [adu Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) a [informace o zařízení](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Umístění souboru

V rámci systému Linux, v oddílu nebo na disku `adu` s názvem, vytvořte textový soubor s názvem "adu-conf.txt" s následujícími poli.

## <a name="list-of-fields"></a>Seznam polí

|Název|Popis|
|-----------|--------------------|
|connection_string|Předem zřízený připojovací řetězec, pomocí kterého může zařízení připojit k IoT Hub. Poznámka: nepožaduje se, pokud zřizujete agenta aktualizací zařízení prostřednictvím [služby Azure IoT identity](https://azure.github.io/iot-identity-service/) .|
|aduc_manufacturer|Hlášené `AzureDeviceUpdateCore:4.ClientMetadata:4` rozhraním pro klasifikaci zařízení pro cílení nasazení aktualizace.|
|aduc_model|Hlášené `AzureDeviceUpdateCore:4.ClientMetadata:4` rozhraním pro klasifikaci zařízení pro cílení nasazení aktualizace.|
|manufacturer|Hlášené agentem aktualizace zařízení jako součást `DeviceInformation` rozhraní.|
|model|Hlášené agentem aktualizace zařízení jako součást `DeviceInformation` rozhraní.|

## <a name="example-adu-conftxt-file-contents"></a>Příklad "adu-conf.txt" obsahu souboru

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
