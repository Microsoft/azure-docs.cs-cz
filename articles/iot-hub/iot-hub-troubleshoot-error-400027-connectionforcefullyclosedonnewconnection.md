---
title: Řešení potíží s chybami Azure IoT Hub 400027 ConnectionForcefullyClosedOnNewConnection
description: Vysvětlení, jak opravit chybu 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960526"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Tento článek popisuje příčiny a řešení 400027 chyb **ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Příznaky

Operace s dvojitým cloudem (jako jsou vlastnosti hlášené při čtení nebo opravě) nebo volání přímé metody se nezdařila s kódem chyby **400027**.

## <a name="cause"></a>Příčina

Jiný klient vytvořil nové připojení k IoT Hub pomocí stejných přihlašovacích údajů, takže IoT Hub zavřel předchozí připojení. IoT Hub neumožňuje připojení více než jednoho klienta pomocí stejné sady přihlašovacích údajů.

## <a name="solution"></a>Řešení

Ujistěte se, že se každý klient připojuje k IoT Hub pomocí vlastní identity.