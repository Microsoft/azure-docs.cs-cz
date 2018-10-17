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
ms.openlocfilehash: 4809881276da752ac6eb08773fb8be145dc5f4c7
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49367154"
---
## <a name="create-a-device-identity"></a>Vytvoření identity zařízení

V této části použijete [webu Azure portal](https://portal.azure.com) k vytvoření identity zařízení v registru identit ve službě IoT hub. Zařízení lze připojit ke službě IoT Hub, pouze pokud má záznam v registru identit. Další informace najdete v části "Registr identit" [Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) použít **zařízení IoT** panelu portálu vygeneruje jedinečné ID a klíč pro vaše zařízení používat k identifikaci samotné do služby IoT Hub. V ID zařízení se rozlišují malá a velká písmena.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny prostředky** a najít váš prostředek centra IoT.

1. Pokud váš IoT hub prostředek se otevře, klikněte na tlačítko **zařízení IoT** nástroje a potom klikněte na tlačítko **přidat** v horní části. 

    ![Vytvoření identity zařízení na portálu](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. Zadejte název nové zařízení, jako třeba **myDeviceId**a klikněte na tlačítko **Uložit**. Tato akce vytvoří novou identitu zařízení služby IoT hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Přidat nové zařízení](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

1. V seznamu zařízení, klikněte na nově vytvořený zařízení a zkopírujte **připojovací řetězec---primární klíč** pro pozdější použití.

    ![Připojovací řetězec zařízení](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají pouze identity zařízení za účelem bezpečného přístupu ke službě IoT Hub. Ukládají se zde ID zařízení a jejich klíče, které slouží jako zabezpečené přihlašovací údaje, a příznak povoleno/zakázáno, s jehož pomocí můžete zakázat přístup k jednotlivým zařízením. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Další informace najdete v tématu [Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
