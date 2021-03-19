---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "70049010"
---
<!-- put the ## header in the file that includes this file -->

V této části vytvoříte identitu zařízení v registru identit ve službě IoT Hub. Zařízení se nemůže připojit k rozbočovači, pokud nemá záznam v registru identit. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations).

1. V navigační nabídce centra IoT otevřete **zařízení IoT** a pak vyberte **Nová** a přidejte zařízení do služby IoT Hub.

    ![Vytvoření identity zařízení na portálu](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. V nástroji **vytvořit zařízení** zadejte název nového zařízení, například **myDeviceId**, a vyberte **Uložit**. Tato akce vytvoří identitu zařízení pro Centrum IoT.

   ![Přidat nové zařízení](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Vytvořené zařízení otevřete v seznamu v podokně **Zařízení IoT**. Zkopírujte **primární připojovací řetězec** pro pozdější použití.

    ![Připojovací řetězec zařízení](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají pouze identity zařízení za účelem bezpečného přístupu ke službě IoT Hub. Ukládají se zde ID zařízení a jejich klíče, které slouží jako zabezpečené přihlašovací údaje, a příznak povoleno/zakázáno, s jehož pomocí můžete zakázat přístup k jednotlivým zařízením. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
