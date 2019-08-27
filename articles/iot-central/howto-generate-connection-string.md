---
title: Vygenerovat připojovací řetězec zařízení pro Azure IoT Central | Microsoft Docs
description: Jak je vývojář zařízení, jak vygenerujem připojovací řetězec pro zařízení, které se potřebuje připojit k IoT Central aplikaci?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019782"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Vygenerování připojovacího řetězce zařízení pro připojení k aplikaci Azure IoT Central

Tento článek popisuje, jak jako vývojář zařízení vygeneruje připojovací řetězec pro zařízení, které se musí připojit k IoT Central aplikaci. Postup popsaný v tomto článku vám ukáže, jak rychle připojit jedno zařízení pomocí sdíleného přístupového podpisu (SAS). Tento přístup je užitečný při experimentování s IoT Central nebo testováním zařízení. Alternativní přístupy k použití v produkčním prostředí najdete v tématu [připojení zařízení v Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

- Aplikace Azure IoT Central. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
- Vývojový počítač s nainstalovanou aplikací [Node. js](https://nodejs.org/) verze 8.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="get-connection-information"></a>Získání informací o připojení

Následující postup popisuje, jak získat informace potřebné k vygenerování připojovacího řetězce SAS pro zařízení:

1. V **Device Explorer**Najděte reálné zařízení, které chcete připojit k aplikaci:

    ![Výběr reálného zařízení](media/howto-generate-connection-string/real-devices.png)

1. Na stránce **zařízení** vyberte **připojit**:

    ![Vyberte připojit](media/howto-generate-connection-string/connect.png)

1. Poznamenejte si podrobnosti o připojení, **ID oboru**, **ID zařízení**a **primární klíč zařízení**, aby bylo možné použít následující postup:

    ![Podrobnosti připojení](media/howto-generate-connection-string/device-connect.png)

    Hodnoty z této stránky můžete zkopírovat a uložit.

## <a name="generate-the-connection-string"></a>Vygenerovat připojovací řetězec

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Další postup

Teď, když jste vygenerovali připojovací řetězec pro reálné zařízení, které se má připojit k vaší aplikaci Azure IoT Central, najdete tady Doporučené další kroky:

* [Příprava a připojení zařízení DevKit (C)](howto-connect-devkit.md)
* [Příprava a připojení maliny PI (Python)](howto-connect-raspberry-pi-python.md)
* [Příprava a připojení maliny PI (C#)](howto-connect-raspberry-pi-csharp.md)
* [Příprava a připojení zařízení s Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)