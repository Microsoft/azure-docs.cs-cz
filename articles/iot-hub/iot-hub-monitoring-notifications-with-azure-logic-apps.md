---
title: Sada IoT vzdálené monitorování a oznámení pomocí Azure Logic Apps | Dokumentace Microsoftu
description: Pomocí Azure Logic Apps pro IoT teploty monitorování ve službě IoT hub a automaticky odesílat e-mailová oznámení do poštovní schránky pro zjistili anomálie.
author: rangv
manager: ''
keywords: IOT iot oznámení monitorování monitorování teploty iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: adda4e948c11f84517b1e8dd01e6cfe42155e1ca
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409437"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Sada IoT vzdálené monitorování a oznámení pomocí Azure Logic Apps propojení vaší služby IoT hub a poštovní schránky

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Služba Azure Logic Apps poskytuje způsob, jak automatizovat procesy jako sérii kroků. Aplikace logiky se můžete připojit napříč různými službami a protokoly. Začíná triggerem například "Když je přidán účet" a za nímž následuje kombinací akcí, jako "poslání nabízeného oznámení". Díky této funkci Logic Apps ideální řešení IoT pro IoT monitorování, jako je například zachování výstrahy pro anomálie, mezi další scénáře využití.

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte, jak vytvořit aplikaci logiky, která propojuje službu IoT hub a poštovní schránky pro monitorování teploty a oznámení. Když je nad 30 C, označí klientská aplikace `temperatureAlert = "true"` ve zprávě, odešle do služby IoT hub. Zpráva aktivuje aplikace logiky k odesílání e-mailové oznámení.

## <a name="what-you-do"></a>Co můžete dělat

* Vytvořit obor názvů služby Service bus a přidejte do ní fronty.
* Přidáte koncový bod a pravidel směrování do služby IoT hub.
* Vytvoření, konfigurace a testování aplikace logiky.

## <a name="what-you-need"></a>Co potřebujete

* Kurz [nastavit zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončeno, která zahrnuje následující požadavky:
  * Aktivní předplatné Azure.
  * Azure IoT hub v rámci vašeho předplatného.
  * Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Vytvoření oboru názvů služby Service bus a k němu přidat frontu

### <a name="create-a-service-bus-namespace"></a>Vytvořit obor názvů služby Service bus

1. Na [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** > **podniková integrace** > **služby Service Bus**.
1. Zadejte tyto informace:

   **Název**: název služby Service bus.

   **Cenová úroveň**: klikněte na tlačítko **základní** > **vyberte**. Úroveň Basic je dostatečná pro účely tohoto kurzu.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: určit stejné umístění, která používá službu IoT hub.
1. Klikněte na možnost **Vytvořit**.

   ![Vytvořit obor názvů služby Service bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Přidat frontu služby Service bus

1. Otevřete obor názvů služby Service bus a pak klikněte na tlačítko **+ fronta**.
1. Zadejte název pro fronty a potom klikněte na tlačítko **vytvořit**.
1. Otevřít frontu služby Service bus a pak klikněte na tlačítko **zásady sdíleného přístupu** > **+ přidat**.
1. Zadejte název pro zásadu vrácení **spravovat**a potom klikněte na tlačítko **vytvořit**.

   ![Přidat frontu služby Service bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Přidat koncový bod a pravidel směrování do služby IoT hub

### <a name="add-an-endpoint"></a>Přidání koncového bodu

1. Otevřete své Centrum IoT, klikněte na koncové body > + Přidat.
1. Zadejte následující informace:

   **Název**: název koncového bodu.

   **Typ koncového bodu**: vyberte **frontu služby Service Bus**.

   **Obor názvů služby Service Bus**: Vyberte obor názvů, který jste vytvořili.

   **Fronty služby Service Bus**: vyberte frontu, vytvoříte.
1. Klikněte na **OK**.

   ![Přidat koncový bod do služby IoT hub na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Přidání pravidla směrování

1. Ve službě IoT hub, klikněte na tlačítko **trasy** > **+ přidat**.
1. Zadejte následující informace:

   **Název**: název pravidla směrování.

   **Zdroj dat**: vyberte **DeviceMessages**.

   **Koncový bod**: Vyberte koncový bod, který jste vytvořili.

   **Řetězec dotazu**: Zadejte `temperatureAlert = "true"`.
1. Klikněte na **Uložit**.

   ![Přidat pravidlo směrování na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Vytvoření a konfigurace aplikace logiky

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** > **podniková integrace** > **aplikace logiky**.
1. Zadejte následující informace:

   **Název**: název aplikace logiky.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: určit stejné umístění, která používá službu IoT hub.
1. Klikněte na možnost **Vytvořit**.

### <a name="configure-the-logic-app"></a>Konfigurace aplikace logiky

1. Otevřete aplikaci logiky, která se otevře v návrháři pro Logic Apps.
1. V návrháři pro Logic Apps, klikněte na tlačítko **prázdná aplikace logiky**.

   ![Začněte s prázdné aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Klikněte na tlačítko **Service Bus**.

   ![Výběr služby Service Bus a začněte vytvářet aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Klikněte na tlačítko **služby Service Bus – při doručení jedné či více zpráv do fronty (automatické dokončení)**.
1. Vytvoření připojení pomocí service bus.
   1. Zadejte název připojení.
   1. Klikněte na obor názvů služby Service bus > zásady service bus > **vytvořit**.

      ![Vytvoření připojení pomocí service bus pro vaši aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Klikněte na tlačítko **pokračovat** po vytvoření připojení služby Service bus.
   1. Vyberte frontu, kterou jste vytvořili a zadejte `175` pro **maximální počet zpráv**

      ![Zadejte maximální počet pro připojení služby Service bus ve vaší aplikaci logiky](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Klikněte na "tlačítko Uložit změny uložit".

1. Vytvořte připojení služby SMTP.
   1. Klikněte na tlačítko **nový krok** > **přidat akci**.
   1. Typ `SMTP`, klikněte na tlačítko **SMTP** služby ve výsledcích hledání a potom klikněte na tlačítko **SMTP – odeslat E-mail**.

      ![Vytvoření připojení k SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Zadejte informace o vaší poštovní schránce SMTP a potom klikněte na tlačítko **vytvořit**.

      ![Zadejte informace o připojení SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Získejte informace o protokolu SMTP pro [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), a [e-mailu Yahoo](https://help.yahoo.com/kb/SLN4075.html).
   1. Zadejte svou e-mailovou adresu pro **z** a **k**, a `High temperature detected` pro **subjektu** a **tělo**.
   1. Klikněte na **Uložit**.

Aplikace logiky je ve funkčním stavu, když ji uložíte.

## <a name="test-the-logic-app"></a>Otestování aplikace logiky

1. Spuštění klientské aplikace, který nasadíte do zařízení v [ESP8266 připojit ke službě Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Zvýšení teploty prostředí kolem SensorTag být vyšší než 30 C. Například světle svíčkového kolem vašeho SensorTag.
1. Měli byste obdržet oznámení o e-mailu odeslaného aplikací logiky.

   > [!NOTE]
   > Váš poskytovatel služeb e-mailu může potřebovat ověřit Identita odesílatele, aby se zajistilo, že se že jedná o vás, který odešle e-mail.

## <a name="next-steps"></a>Další postup

Úspěšně jste vytvořili aplikaci logiky, která propojuje službu IoT hub a poštovní schránky pro monitorování teploty a oznámení.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
