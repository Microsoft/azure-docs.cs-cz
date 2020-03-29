---
title: Poradce při potížích s chybou služby Azure IoT Hub 400027 ConnectionForcefullyClosedOnNewConnection
description: Pochopit, jak opravit chybu 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960526"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Tento článek popisuje příčiny a řešení pro **400027 ConnectionForcefullyClosedOnNewConnection** chyby.

## <a name="symptoms"></a>Příznaky

Operace dvojčete zařízení cloudu (například vlastnosti oznamované čtením nebo opravou) nebo přímé vyvolání metody se nezdaří s kódem chyby **400027**.

## <a name="cause"></a>Příčina

Jiný klient vytvořil nové připojení k ioT hubu pomocí stejných přihlašovacích údajů, takže Služba IoT Hub uzavřela předchozí připojení. Služba IoT Hub neumožňuje připojení více než jednomu klientovi pomocí stejné sady přihlašovacích údajů.

## <a name="solution"></a>Řešení

Ujistěte se, že každý klient se připojí k služby IoT Hub pomocí vlastní identity.