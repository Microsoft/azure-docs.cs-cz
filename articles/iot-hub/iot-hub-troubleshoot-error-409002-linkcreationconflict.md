---
title: Poradce při potížích s chybou služby Azure IoT Hub 409002 LinkCreationConflict
description: Pochopit, jak opravit chybu 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960773"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Tento článek popisuje příčiny a řešení pro **409002 LinkCreationConflict** chyby.

## <a name="symptoms"></a>Příznaky

Zobrazí se chyba **409002 LinkCreationConflict** přihlášendiagnostické protokoly spolu s odpojení zařízení nebo selhání zprávy cloud-to-device. 

<!-- When using AMQP? -->

## <a name="cause"></a>Příčina

Obecně k této chybě dochází, když služba IoT Hub zjistí, že klient má více než jedno připojení. Ve skutečnosti při příchodu nové žádosti o připojení pro zařízení s existující připojení, IoT Hub zavře existující připojení s touto chybou.

### <a name="cause-1"></a>Příčina 1

V nejběžnějším případě samostatný problém (například [404104 DeviceConnectionClosedRemotely)](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)způsobí odpojení zařízení. Zařízení se pokusí okamžitě obnovit připojení, ale služba IoT Hub stále považuje zařízení za připojené. Služba IoT Hub ukončí předchozí připojení a zaznamená tuto chybu.

### <a name="cause-2"></a>Příčina 2

Chybná logika na straně zařízení způsobí, že zařízení naváže připojení, když je již otevřené.

## <a name="solution"></a>Řešení

Tato chyba se obvykle zobrazí jako vedlejší účinek jiného, přechodný problém, proto vyhledejte další chyby v protokolech k dalšímu řešení potíží. V opačném případě nezapomeňte vydat nový požadavek na připojení pouze v případě, že připojení klesne.
