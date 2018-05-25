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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
## <a name="create-a-device-identity"></a>Vytvoření identity zařízení

V této části můžete použít [portál Azure] [ lnk-azure-portal] k vytvoření identity zařízení v registru identit ve službě IoT hub. Zařízení lze připojit ke službě IoT Hub, pouze pokud má záznam v registru identit. Další informace najdete v části Registr identit v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity]. Použít **zařízení IoT** panelu portálu k vygenerování jedinečné ID zařízení a klíč pro vaše zařízení identifikovat do služby IoT Hub. V ID zařízení se rozlišují malá a velká písmena.

1. Přihlaste se na web [Azure Portal][lnk-azure-portal].

1. Vyberte **všechny prostředky** a najít prostředek centra IoT.

1. Po otevření prostředku centra IoT klikněte **zařízení IoT** nástroje a potom klikněte na **přidat** v horní části. 

    ![Vytvoření identity zařízení na portálu][img-add-device]

1. Zadejte název nové zařízení, jako třeba **myDeviceId**a klikněte na tlačítko **Uložit**. Tato akce vytvoří novou identitu zařízení pro službu IoT hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Přidat nové zařízení][img-create-device]

1. V seznamu zařízení, klikněte na nově vytvořený zařízení a zkopírujte **připojovací řetězec, primární klíč** pro pozdější použití.

    ![Řetězec připojení zařízení][img-connection-string]

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají pouze identity zařízení za účelem bezpečného přístupu ke službě IoT Hub. Ukládají se zde ID zařízení a jejich klíče, které slouží jako zabezpečené přihlašovací údaje, a příznak povoleno/zakázáno, s jehož pomocí můžete zakázat přístup k jednotlivým zařízením. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

