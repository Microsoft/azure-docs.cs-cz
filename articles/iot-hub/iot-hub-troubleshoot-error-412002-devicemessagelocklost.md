---
title: Poradce při potížích s chybou služby Azure IoT Hub 412002 DeviceMessageLockLost
description: Pochopit, jak opravit chybu 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960760"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Tento článek popisuje příčiny a řešení pro **412002 DeviceMessageLockLost** chyby.

## <a name="symptoms"></a>Příznaky

Při pokusu o odeslání zprávy cloud zařízení požadavek se nezdaří s chybou **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Příčina

Když zařízení obdrží zprávu cloud zařízení z fronty (například [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)pomocí) zpráva je uzamčena službou IoT Hub pro trvání časového doby uzamčení jedné minuty. Pokud se zařízení pokusí dokončit zprávu po vypršení časového limitu uzamčení, služba IoT Hub vyvolá tuto výjimku.

## <a name="solution"></a>Řešení

Pokud služba IoT Hub nezíská oznámení během trvání časového časového období uzamčení jedné minuty, nastaví zprávu zpět do stavu *Zařazeno do fronty.* Zařízení se může pokusit zprávu znovu přijmout. Chcete-li zabránit chybě v budoucnu, implementovat logiku na straně zařízení k dokončení zprávy do jedné minuty od přijetí zprávy. Tento jednominutový časový výtok nelze změnit.