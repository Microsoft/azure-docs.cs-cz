---
title: Vzdálené sledování IoT a oznámení službou Azure Logic Apps | Microsoft Docs
description: Použít Azure Logic Apps pro IoT teploty sledování ve službě IoT hub a automaticky odesílat e-mailová oznámení do poštovní schránky pro případných zjištěných.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: IOT iot oznámení monitorování monitorování teploty iot
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: e54c36d0cfbaedb93db86ad6ce5f99b288b63c9c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT pro vzdálené monitorování a oznámení službou Azure Logic Apps připojení služby IoT hub a poštovní schránky

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Azure Logic Apps poskytuje způsob, jak automatizovat procesy jako řadu kroků. Aplikace logiky můžete připojit přes různé služby a protokoly. Začíná aktivační událost, jako "Při přidání účtu" a a kombinace akcí, jako "odesílat nabízená oznámení". Díky této funkci Logic Apps ideální řešení IoT pro IoT monitorování, jako je například zachování výstrahy anomálií mezi scénáře použití.

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte, jak vytvořit aplikaci logiky, která se připojuje služby IoT hub a poštovní schránky pro monitorování teploty a oznámení. Když je nad 30 C, klientská aplikace označí `temperatureAlert = "true"` ve zprávě, odešle do služby IoT hub. Zpráva se aktivuje aplikaci logiky můžete odeslat e-mailové oznámení.

## <a name="what-you-do"></a>Co dělat

* Vytvořit obor názvů sběrnice služby a přidejte do ní fronty.
* Přidáte koncový bod a pravidel směrování do služby IoT hub.
* Vytvoření, konfiguraci a testování aplikace logiky.

## <a name="what-you-need"></a>Co potřebujete

* Kurz [nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončit, která zahrnuje následující požadavky:
  * Aktivní předplatné Azure.
  * V rámci svého předplatného služby Azure IoT hub.
  * Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Vytvoření oboru názvů service bus a přidejte do ní fronty

### <a name="create-a-service-bus-namespace"></a>Vytvořit obor názvů sběrnice služby

1. Na [portál Azure](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** > **Enterprise integrace** > **Service Bus**.
1. Zadejte následující informace:

   **Název**: název služby service bus.

   **Cenová úroveň**: klikněte na tlačítko **základní** > **vyberte**. Úroveň Basic je dostačující pro účely tohoto kurzu.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: používalo stejné umístění, která používá službu IoT hub.
1. Klikněte na možnost **Vytvořit**.

   ![Vytvořit obor názvů sběrnice služby na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Přidat frontou service bus

1. Otevřete obor názvů sběrnice a pak klikněte na tlačítko **+ fronty**.
1. Zadejte název pro frontu a pak klikněte na tlačítko **vytvořit**.
1. Otevřete frontou service bus a pak klikněte na tlačítko **zásady sdíleného přístupu** > **+ přidat**.
1. Zadejte název zásady, zkontrolujte **spravovat**a potom klikněte na **vytvořit**.

   ![Přidat frontou service bus na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Přidat koncový bod a pravidel směrování do služby IoT hub

### <a name="add-an-endpoint"></a>Přidání koncového bodu

1. Otevřete službu IoT hub, klikněte na koncové body > + Přidat.
1. Zadejte následující informace:

   **Název**: název koncového bodu.

   **Typ koncového bodu**: vyberte **frontou Service Bus**.

   **Obor názvů sběrnice**: Vyberte obor názvů, který jste vytvořili.

   **Fronty Service Bus**: vyberte fronty, které jste vytvořili.
1. Klikněte na **OK**.

   ![Přidání koncového bodu do služby IoT hub v portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Přidat pravidlo směrování

1. Ve službě IoT hub, klikněte na tlačítko **trasy** > **+ přidat**.
1. Zadejte následující informace:

   **Název**: název pravidla směrování.

   **Zdroj dat**: vyberte **DeviceMessages**.

   **Koncový bod**: Vyberte koncový bod, který jste vytvořili.

   **Řetězec dotazu**: Zadejte `temperatureAlert = "true"`.
1. Klikněte na **Uložit**.

   ![Přidat pravidlo směrování na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Vytvoření a konfigurace aplikace logiky

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. V [portál Azure](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** > **Enterprise integrace** > **aplikace logiky**.
1. Zadejte následující informace:

   **Název**: název aplikace logiky.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: používalo stejné umístění, která používá službu IoT hub.
1. Klikněte na možnost **Vytvořit**.

### <a name="configure-the-logic-app"></a>Konfigurace aplikace logiky

1. Otevřete aplikaci logiky, které se otevře v Návrháři logiku aplikace.
1. V návrháři aplikace logiky, klikněte na tlačítko **prázdné aplikace logiky**.

   ![Začít s prázdnou logiku aplikace na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Klikněte na tlačítko **Service Bus**.

   ![Vyberte služby Service Bus chcete začít vytvářet aplikaci logiky na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Klikněte na tlačítko **Service Bus – při obdržení jeden nebo více zpráv ve frontě (automatické dokončování)**.
1. Umožňuje vytvořte připojení service bus.
   1. Zadejte název připojení.
   1. Klikněte na obor názvů service bus > zásady service bus > **vytvořit**.

      ![Vytvořte připojení sběrnice služby pro svou aplikaci logiky na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Klikněte na tlačítko **pokračovat** po vytvoření připojení k službě sběrnice.
   1. Vybrat fronty, kterou jste vytvořili a zadat `175` pro **maximální počet zpráv**

      ![Zadejte počet maximální zpráv pro připojení služby service bus v aplikaci logiky](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Klikněte na "tlačítko Uložit změny uložit".

1. Vytvoření připojení služby SMTP.
   1. Klikněte na tlačítko **nový krok** > **přidat akci**.
   1. Typ `SMTP`, klikněte **SMTP** služby ve výsledcích hledání a potom klikněte na **SMTP - odeslat E-mail**.

      ![Vytvoření připojení k SMTP ve vaší aplikaci logiky na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Zadejte informace SMTP poštovní schránky a pak klikněte na tlačítko **vytvořit**.

      ![Zadejte informace o připojení protokolu SMTP ve vaší aplikaci logiky na portálu Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Získat informace o SMTP [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [z Gmailu](https://support.google.com/a/answer/176600?hl=en), a [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Zadejte e-mailovou adresu pro **z** a **k**, a `High temperature detected` pro **subjektu** a **textu**.
   1. Klikněte na **Uložit**.

Aplikace logiky je ve funkčním stavu, když jste jej uložili.

## <a name="test-the-logic-app"></a>Otestování aplikace logiky

1. Spustit aplikaci klienta, který nasadíte do zařízení v [ESP8266 připojit ke službě Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Zvýšení teploty prostředí kolem SensorTag být vyšší než 30 C. Například light svíčkového grafu okolo vaší SensorTag.
1. Měli byste obdržet e-mail s oznámením poslal aplikaci logiky.

   > [!NOTE]
   > Poskytovatel služeb e-mailu možná muset ověřit identitu odesílatele a ujistěte se, že se že jedná, kdo odešle e-mailu.

## <a name="next-steps"></a>Další postup

Úspěšně jste vytvořili aplikaci logiky, která se připojuje služby IoT hub a poštovní schránky pro monitorování teploty a oznámení.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
