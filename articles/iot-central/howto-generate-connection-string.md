---
title: Generovat připojovací řetězec zařízení pro Azure IoT Central | Dokumentace Microsoftu
description: Jak se jako vývojář zařízení vygenerovat připojovací řetězec pro zařízení, která vyžaduje připojení k aplikaci IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3a5e8d15d9a705892fe54c50e9b79e6d42af78d9
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426688"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Generovat připojovací řetězec zařízení pro připojení k aplikaci Azure IoT Central

Tento článek popisuje, jak jako zařízení pro vývojáře k vygenerování připojovacího řetězce pro zařízení, která vyžaduje připojení k aplikaci IoT Central. Postup popsaný v tomto článku ukazují, jak se rychle připojit jedno zařízení pomocí sdíleného přístupového podpisu (SAS). Tento přístup je užitečný při experimentování s IoT Central nebo testování zařízení. Alternativní přístupy k použití v produkčním prostředí, najdete v části [připojení zařízení v Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

1. Aplikace Azure IoT Central. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
1. Vývojovém počítači s [Node.js](https://nodejs.org/) verze 8.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku k ověření verze. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="get-connection-information"></a>Získání informací o připojení

Následující kroky popisují, jak získat informace, je potřeba vygenerovat připojovací řetězec SAS pro zařízení:

1. V **Explorer**, najít skutečné zařízení, kterou chcete připojit k vaší aplikaci:

    ![Vyberte skutečných zařízení](media/howto-generate-connection-string/real-devices.png)

1. Na **zařízení** stránce **připojit**:

    ![Vyberte připojení](media/howto-generate-connection-string/connect.png)

1. Poznamenejte si podrobnosti o připojení **ID oboru**, **ID zařízení**, a **zařízení primární klíč**a chcete použít v následujících krocích:

    ![Podrobnosti připojení](media/howto-generate-connection-string/device-connect.png)

    Zkopírujte hodnoty z této stránky uložte.

## <a name="generate-the-connection-string"></a>Generovat připojovací řetězec

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Další postup

Teď, když si necháte vygenerovat připojovací řetězec pro skutečné zařízení pro připojení k aplikaci Azure IoT Central, tady jsou další navrhované kroky:

* [Příprava a připojení zařízení DevKit (C)](howto-connect-devkit.md)
* [Příprava a připojení Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Příprava a připojení Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Příprava a připojit zařízení s Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Připojit obecného klienta Node.js do aplikace Azure IoT Central](howto-connect-nodejs.md)