---
title: Řešení potíží s chybami Azure IoT Hub 412002 DeviceMessageLockLost
description: Vysvětlení, jak opravit chybu 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: eb3d7f15109d3b217f651a7d927601ef7fae66c2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061022"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Tento článek popisuje příčiny a řešení 412002 chyb **DeviceMessageLockLost** .

## <a name="symptoms"></a>Příznaky

Když se pokusíte odeslat zprávu typu cloud-zařízení, požadavek se nezdařil s chybou **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Příčina

Když zařízení obdrží zprávu typu cloud-zařízení z fronty (například pomocí [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) ), zpráva se zamkne IoT Hub po dobu vypršení časového limitu zámku 1 minuty. Pokud se zařízení pokusí dokončit zprávu po vypršení časového limitu zámku, IoT Hub vyvolá tuto výjimku.

## <a name="solution"></a>Řešení

Pokud IoT Hub neobdrží oznámení v rámci uplynutí časového limitu zámku uzamčení, nastaví zprávu zpět na stav zařazeno do *fronty* . Zařízení se může pokusit o přijetí zprávy znovu. Aby nedošlo k chybě v budoucnu, implementujte na straně zařízení logiku, která dokončí zprávu během jedné minuty od přijetí zprávy. Tento časový limit 1. minut se nedá změnit.