---
title: Řešení potíží s chybami Azure IoT Hub 409002 LinkCreationConflict
description: Vysvětlení, jak opravit chybu 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: c354eed6e7bf9f2147f8b81a82a06e80a9337c84
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061039"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Tento článek popisuje příčiny a řešení 409002 chyb **LinkCreationConflict** .

## <a name="symptoms"></a>Příznaky

V protokolech se zobrazí chyba **409002 LinkCreationConflict** spolu s odpojením zařízení nebo selháním zprávy z cloudu na zařízení.

<!-- When using AMQP? -->

## <a name="cause"></a>Příčina

Obecně k této chybě dochází, když IoT Hub detekuje, že klient má více než jedno připojení. Pokud se ve skutečnosti do zařízení s existujícím připojením dorazí nový požadavek na připojení, IoT Hub ukončí stávající připojení s touto chybou.

### <a name="cause-1"></a>Příčina 1

V nejběžnějším případě samostatný problém (například [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) způsobí, že se zařízení odpojí. Zařízení se pokusí připojení okamžitě znovu vytvořit, ale IoT Hub se zařízení i nadále považovat za připojené. IoT Hub zavře předchozí připojení a zaznamená tuto chybu.

### <a name="cause-2"></a>Příčina 2

Chybná logika na straně zařízení způsobí, že zařízení naváže připojení, když je už jeden otevřený.

## <a name="solution"></a>Řešení

Tato chyba se obvykle zobrazuje v důsledku vedlejšího efektu jiného, přechodného problému, takže se můžete podívat na další chyby v protokolech k dalšímu řešení potíží. V opačném případě se ujistěte, že chcete vydat novou žádost o připojení pouze v případě, že se připojení uvolní.
