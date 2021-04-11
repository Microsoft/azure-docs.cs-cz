---
title: Pochopení aktualizace zařízení u prostředků Azure IoT Hub | Microsoft Docs
description: Vysvětlení aktualizace zařízení pro prostředky Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662667"
---
# <a name="device-update-resources"></a>Prostředky aktualizace zařízení

Chcete-li použít aktualizaci zařízení pro IoT Hub, je nutné vytvořit účet aktualizace zařízení a prostředek instance. 

## <a name="device-update-account"></a>Účet aktualizace zařízení

Účet aktualizace zařízení je prostředek, který se vytvoří v rámci vašeho předplatného Azure. Na úrovni účtu aktualizace zařízení můžete vybrat oblast, ve které se vytvoří účet aktualizace vašeho zařízení. Můžete také nastavit oprávnění k autorizaci uživatelů, kteří budou mít přístup k aktualizaci zařízení.


## <a name="device-update-instance"></a>Instance aktualizace zařízení
Po vytvoření účtu je potřeba vytvořit instanci aktualizace zařízení. Instance je logický kontejner, který obsahuje aktualizace a nasazení přidružená ke konkrétní službě IoT Hub. Aktualizace zařízení používá centrum IoT jako adresář zařízení a komunikační kanál se zařízeními. 

Ve verzi Public Preview je možné vytvořit dva účty aktualizace zařízení pro každé předplatné. Pro každý účet lze také vytvořit dvě instance aktualizace zařízení.

## <a name="configuring-device-update-linked-iot-hub"></a>Konfigurace propojených IoT Hub aktualizace zařízení 

Aby aktualizace zařízení přijímala oznámení o změnách z IoT Hub, aktualizace zařízení se integruje do integrovaného centra událostí. Kliknutím na tlačítko Konfigurovat IoT Hub v rámci instance nakonfigurujete požadované trasy a zásady přístupu, které jsou potřeba ke komunikaci se zařízeními IoT. 

Pro aktualizace zařízení jsou nakonfigurované následující trasy zpráv:

|   Název trasy    | Dotaz směrování  | Description  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |Naslouchá událostem digitálního nevlákenných změn.  |
|  DeviceUpdate.DeviceLifeCycle | opType = ' deleteDeviceIdentity '  | Naslouchá u zařízení, která byla odstraněna. |
|  DeviceUpdate.TelemetryModelInformation | iothub-Interface-ID = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Naslouchá pro nové typy zařízení. |
|  DeviceUpdate.DeviceTwinEvents| (opType = ' updateTwin' nebo opType = ' replaceTwin') AND IS_DEFINED ($body. Tags. ADUGroup) | Čeká na nové skupiny aktualizací zařízení. |

## <a name="next-steps"></a>Další kroky

[Vytvoření prostředků aktualizace zařízení](./create-device-update-account.md)
