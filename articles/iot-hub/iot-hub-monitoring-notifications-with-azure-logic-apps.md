---
title: Sada IoT vzdálené monitorování a oznámení pomocí Azure Logic Apps | Dokumentace Microsoftu
description: Pomocí Azure Logic Apps pro IoT teploty monitorování ve službě IoT hub a automaticky odesílat e-mailová oznámení do poštovní schránky pro zjistili anomálie.
author: robinsh
keywords: IOT iot oznámení monitorování monitorování teploty iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 5a277ac18bcbcb7e7acc6faf52f7bc72759c82a7
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677999"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Sada IoT vzdálené monitorování a oznámení pomocí Azure Logic Apps propojení vaší služby IoT hub a poštovní schránky

![Diagram začátku do konce](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Služba Azure Logic Apps poskytuje způsob, jak automatizovat procesy jako sérii kroků. Aplikace logiky se můžete připojit napříč různými službami a protokoly. Začíná triggerem například "Když je přidán účet" a za nímž následuje kombinací akcí, jako "poslání nabízeného oznámení". Díky této funkci Logic Apps ideální řešení IoT pro IoT monitorování, jako je například zachování výstrahy pro anomálie, mezi další scénáře využití.

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte, jak vytvořit aplikaci logiky, která propojuje službu IoT hub a poštovní schránky pro monitorování teploty a oznámení. Když je nad 30 C, označí klientská aplikace `temperatureAlert = "true"` ve zprávě, odešle do služby IoT hub. Zpráva aktivuje aplikace logiky k odesílání e-mailové oznámení.

## <a name="what-you-do"></a>Co můžete dělat

* Vytvořit obor názvů služby Service bus a přidejte do ní fronty.
* Přidáte koncový bod a pravidel směrování do služby IoT hub.
* Vytvoření, konfigurace a testování aplikace logiky.

## <a name="what-you-need"></a>Co potřebujete

* Dokončení [online simulátor Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) kurzu nebo jeden z kurzů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ty zahrnují následující požadavky:

  * Aktivní předplatné Azure.
  * Azure IoT hub v rámci vašeho předplatného.
  * Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Vytvoření oboru názvů služby Service bus a k němu přidat frontu

### <a name="create-a-service-bus-namespace"></a>Vytvořit obor názvů služby Service bus

1. Na [webu Azure portal](https://portal.azure.com/)vyberte **vytvořit prostředek** > **podniková integrace** > **služby Service Bus**.

2. Zadejte tyto informace:

   **Název**: Název služby Service bus.

   **Cenová úroveň**: Vyberte **základní** > **vyberte**. Úroveň Basic je dostatečná pro účely tohoto kurzu.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: Použijte stejné umístění, která používá službu IoT hub.

3. Vyberte **Vytvořit**.

   ![Vytvořit obor názvů služby Service bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Přidat frontu služby Service bus

1. Otevřete obor názvů služby Service bus a potom vyberte **+ fronta**.

1. Zadejte název fronty a pak vyberte **vytvořit**.

1. Otevření fronty service bus a pak vyberte **zásady sdíleného přístupu** > **+ přidat**.

1. Zadejte název pro zásadu vrácení **spravovat**a pak vyberte **vytvořit**.

   ![Přidat frontu služby Service bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-query-to-your-iot-hub"></a>Přidat koncový bod a směrování dotaz do služby IoT hub

Nyní přidejte koncový bod a směrování dotaz do služby Iot hub.

### <a name="add-an-endpoint"></a>Přidání koncového bodu

1. Otevřete své Centrum IoT, vyberte **koncové body** > **+ přidat**.

1. Zadejte následující informace:

   **Název**: Název koncového bodu.

   **Typ koncového bodu**: Vyberte **Fronta služby Service Bus**.

   **Obor názvů služby Service Bus**: Vyberte obor názvů, který jste vytvořili.

   **Fronty služby Service Bus**: Vyberte fronty, kterou jste vytvořili.

3. Vyberte **OK**.

   ![Přidat koncový bod do služby IoT hub na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Přidání pravidla směrování

1. Ve službě IoT hub, vyberte **trasy** > **+ přidat**.

2. Zadejte následující informace:

   **Název**: Název pravidla směrování.

   **Zdroj dat**: Vyberte **DeviceMessages**.

   **Koncový bod**: Vyberte koncový bod, který jste vytvořili.

   **Řetězec dotazu**: Zadejte `temperatureAlert = "true"`.

3. Vyberte **Uložit**.

   ![Přidat pravidlo směrování na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Vytvoření a konfigurace aplikace logiky

V dalším kroku vytvoříte a nakonfigurujete aplikaci logiky.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. V [webu Azure portal](https://portal.azure.com/)vyberte **vytvořit prostředek** > **podniková integrace** > **aplikace logiky**.

2. Zadejte následující informace:

   **Název**: Název aplikace logiky.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: Použijte stejné umístění, která používá službu IoT hub.

3. Vyberte **Vytvořit**.

### <a name="configure-the-logic-app"></a>Konfigurace aplikace logiky

1. Otevřete aplikaci logiky, která se otevře v návrháři pro Logic Apps.

2. V návrháři pro Logic Apps, vyberte **prázdná aplikace logiky**.

   ![Začněte s prázdné aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

3. Vyberte **Service Bus**.

   ![Výběr služby Service Bus a začněte vytvářet aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

4. Vyberte **služby Service Bus – při doručení jedné či více zpráv do fronty (automatické dokončení)**.

5. Vytvoření připojení pomocí service bus.

   1. Zadejte název připojení.

   2. Vyberte obor názvů služby Service bus > zásady service bus > **vytvořit**.

      ![Vytvoření připojení pomocí service bus pro vaši aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   3. Vyberte **pokračovat** po vytvoření připojení služby Service bus.

   4. Vyberte frontu, kterou jste vytvořili a zadejte `175` pro **maximální počet zpráv**.

      ![Zadejte maximální počet pro připojení služby Service bus ve vaší aplikaci logiky](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   5. Vyberte "Save (Uložit) tlačítko Uložit změny.

6. Vytvořte připojení služby SMTP.

   1. Vyberte **nový krok** > **přidat akci**.

   2. Typ `SMTP`, vyberte **SMTP** služby ve výsledcích hledání a potom vyberte **SMTP – odeslat E-mail**.

      ![Vytvoření připojení k SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   3. Zadejte informace o vaší poštovní schránce SMTP a pak vyberte **vytvořit**.

      ![Zadejte informace o připojení SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Získejte informace o protokolu SMTP pro [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), a [e-mailu Yahoo](https://help.yahoo.com/kb/SLN4075.html).

   4. Zadejte svou e-mailovou adresu pro **z** a **k**, a `High temperature detected` pro **subjektu** a **tělo**.

   5. Vyberte **Uložit**.

Aplikace logiky je ve funkčním stavu, když ji uložíte.

## <a name="test-the-logic-app"></a>Otestování aplikace logiky

1. Spuštění klientské aplikace, který nasadíte do zařízení v [ESP8266 připojit ke službě Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).

2. Zvýšení teploty prostředí kolem SensorTag být vyšší než 30 C. Například světle svíčkového kolem vašeho SensorTag.

3. Měli byste obdržet oznámení o e-mailu odeslaného aplikací logiky.

   > [!NOTE]
   > Váš poskytovatel služeb e-mailu může potřebovat ověřit Identita odesílatele, aby se zajistilo, že se že jedná o vás, který odešle e-mail.

## <a name="next-steps"></a>Další postup

Úspěšně jste vytvořili aplikaci logiky, která propojuje službu IoT hub a poštovní schránky pro monitorování teploty a oznámení.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
