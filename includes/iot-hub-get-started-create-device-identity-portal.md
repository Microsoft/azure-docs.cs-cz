---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ffd5da239f8e271a8c9b2aaf3f6d5fd9f885c79c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400439"
---
## <a name="create-a-device-identity"></a>Vytvoření identity zařízení

V této části použijete [webu Azure portal](https://portal.azure.com) k vytvoření identity zařízení v registru identit ve službě IoT hub. Zařízení lze připojit ke službě IoT Hub, pouze pokud má záznam v registru identit. Další informace najdete v části "Registr identit" [Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) použít **zařízení IoT** panelu portálu vygeneruje jedinečné ID a klíč pro vaše zařízení používat k identifikaci samotné do služby IoT Hub. V ID zařízení se rozlišují malá a velká písmena.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. Vyberte **všechny prostředky** a najít váš prostředek centra IoT.

3. Pokud váš IoT hub prostředek se otevře, klikněte na tlačítko **zařízení IoT** nástroje a potom klikněte na tlačítko **přidat** v horní části. 

    ![Vytvoření identity zařízení na portálu](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

4. Zadejte název nové zařízení, jako třeba **myDeviceId**a klikněte na tlačítko **Uložit**. Tato akce vytvoří novou identitu zařízení služby IoT hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Přidat nové zařízení](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

5. V seznamu zařízení, klikněte na nově vytvořený zařízení a zkopírujte **připojovací řetězec---primární klíč** pro pozdější použití.

    ![Připojovací řetězec zařízení](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají pouze identity zařízení za účelem bezpečného přístupu ke službě IoT Hub. Ukládají se zde ID zařízení a jejich klíče, které slouží jako zabezpečené přihlašovací údaje, a příznak povoleno/zakázáno, s jehož pomocí můžete zakázat přístup k jednotlivým zařízením. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Další informace najdete v tématu [Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
