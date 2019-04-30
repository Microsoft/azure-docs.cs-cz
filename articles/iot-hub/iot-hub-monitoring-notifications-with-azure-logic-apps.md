---
title: Sada IoT vzdálené monitorování a oznámení pomocí Azure Logic Apps | Dokumentace Microsoftu
description: Pomocí Azure Logic Apps pro IoT teploty monitorování ve službě IoT hub a automaticky odesílat e-mailová oznámení do poštovní schránky pro zjistili anomálie.
author: robinsh
keywords: IOT iot oznámení monitorování monitorování teploty iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126085"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Sada IoT vzdálené monitorování a oznámení pomocí Azure Logic Apps propojení vaší služby IoT hub a poštovní schránky

![Diagram začátku do konce](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Služba Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) vám může pomoct Orchestrace pracovních postupů napříč místním a cloudovým službám, jeden nebo více podniky a v různých protokolů. Aplikace logiky začíná triggerem, který je následována jednu nebo více akcí, které mohou být sekvencování pomocí integrovaných ovládacích prvků, jako je například podmínky a iterátory. Díky této flexibilitě jsou Logic Apps ideální řešení IoT pro monitorování scénáře IoT. Například doručení telemetrických dat ze zařízení na koncový bod služby IoT Hub můžete zahájit pracovních postupů aplikace logiky do skladu dat v objektu blob služby Azure Storage, odeslání výstrah emailů upozornit data anomálií, naplánování návštěvu technika, pokud se zařízení ohlásí chybu , a tak dále.

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte, jak vytvořit aplikaci logiky, která propojuje službu IoT hub a poštovní schránky pro monitorování teploty a oznámení.

Klientský kód běžící na zařízení nastaví vlastnost aplikací, `temperatureAlert`na každý telemetrické zprávy odešle do služby IoT hub. Když klientský kód zjistí teploty vyšší než 30 C, je tato vlastnost nastaví na `true`; jinak, nastaví vlastnost na `false`.

V tomto tématu nastavíte směrování ve službě IoT hub pro odesílání zpráv, ve kterém `temperatureAlert = true` k Service Bus koncového bodu a nastavit aplikaci logiky, která aktivuje na zprávy přicházející v jiném koncovém bodu služby Service Bus a odesílá e-mailové oznámení.

## <a name="what-you-do"></a>Co můžete dělat

* Vytvoření oboru názvů služby Service Bus a přidejte do ní fronty Service Bus.
* Přidání vlastního koncového bodu a pravidel směrování do služby IoT hub pro směrování zpráv, které obsahují teploty upozornění do fronty služby Service Bus.
* Vytvoření, konfigurace a testování aplikace logiky využívat zprávy z fronty služby Service Bus a odesílat oznámení e-mailů na požadovanou příjemce.

## <a name="what-you-need"></a>Co potřebujete

* Dokončení [online simulátor Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) kurzu nebo jeden z kurzů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ty zahrnují následující požadavky:

  * Aktivní předplatné Azure.
  * Azure IoT hub v rámci vašeho předplatného.
  * Klientská aplikace běžící na zařízení, která odesílá telemetrické zprávy do služby Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Vytvoření oboru názvů služby Service Bus a fronty

Vytvořte oboru názvů a frontu Service Bus. Dále v tomto tématu vytvoříte pravidlo směrování ve službě IoT hub pro přímé zprávy, které obsahují teploty upozornění do fronty Service Bus, ve kterém neexistoval, použije aplikace logiky a aktivovat mohlo odesílat e-mailové oznámení.

### <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus

1. Na [webu Azure portal](https://portal.azure.com/)vyberte **+ vytvořit prostředek** > **integrace** > **služby Service Bus**.

1. Na **vytvoření oboru názvů** podokně zadejte následující informace:

   **Název**: Název oboru názvů služby Service bus. Obor názvů musí být jedinečný v Azure.

   **Cenová úroveň**: Vyberte **základní** z rozevíracího seznamu. Úroveň Basic je dostatečná pro účely tohoto kurzu.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: Použijte stejné umístění, která používá službu IoT hub.

   ![Vytvořit obor názvů služby Service bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Vyberte **Vytvořit**. Počkejte, až se nasazení dokončí, než budete pokračovat k dalšímu kroku.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Přidat frontu služby Service Bus do oboru názvů

1. Otevřete obor názvů služby Service Bus. Nejjednodušší způsob, jak získat do oboru názvů služby Service Bus je výběr **skupiny prostředků** v podokně prostředků, vyberte skupinu prostředků a pak ze seznamu prostředků vyberte obor názvů služby Service Bus.

1. Na **služby Service Bus Namespace** vyberte **+ fronta**.

1. Zadejte název fronty a pak vyberte **vytvořit**. Po úspěšném vytvoření fronty **vytvořit frontu** podokno se zavře.

   ![Přidat frontu služby Service bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Zpět na **služby Service Bus Namespace** podokně v části **entity**vyberte **fronty**. Otevření fronty služby Service Bus ze seznamu a pak vyberte **zásady sdíleného přístupu** > **+ přidat**.

1. Zadejte název pro zásadu vrácení **spravovat**a pak vyberte **vytvořit**.

   ![Přidat zásadu fronty služby Service bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Přidání vlastního koncového bodu a pravidlo směrování do služby IoT hub

Přidejte vlastní koncový bod pro frontu služby Service Bus do služby IoT hub a vytvořte pravidlo směrování zpráv na přímé zprávy, které obsahují teploty upozornění do tohoto koncového bodu, kde jsou neexistoval, použije ve vaší aplikaci logiky. Pravidlo směrování používá směrování dotazů `temperatureAlert = "true"`, aby předával zprávy založené na hodnotě `temperatureAlert` aplikace nastavenou klienta kódu spuštěného v příslušném zařízení. Další informace najdete v tématu [zprávy směrování pro dotazy podle vlastnosti zprávy](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Přidání vlastního koncového bodu

1. Otevřete své Centrum IoT. Nejjednodušší způsob, jak získat pro službu IoT hub je výběr **skupiny prostředků** z podokna prostředků vyberte skupinu prostředků a pak ze seznamu prostředků vyberte své Centrum IoT.

1. V části **zasílání zpráv**vyberte **směrování zpráv**. Na **směrování zpráv** podokně, vyberte **vlastní koncové body** kartu a potom vyberte **+ přidat**. V rozevíracím seznamu vyberte **frontu služby Service bus**.

   ![Přidat koncový bod do služby IoT hub na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Na **přidat koncový bod služby Service bus** podokně zadejte následující informace:

   **Název koncového bodu**: Název koncového bodu.

   **Obor názvů služby Service bus**: Vyberte obor názvů, který jste vytvořili.

   **Fronty služby Service bus**: Vyberte fronty, kterou jste vytvořili.

   ![Přidat koncový bod do služby IoT hub na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Vyberte **Vytvořit**. Po úspěšném vytvoření koncového bodu se pokračujte k dalšímu kroku.

### <a name="add-a-routing-rule"></a>Přidání pravidla směrování

1. Zpět na **směrování zpráv** podokně, vyberte **trasy** kartu a potom vyberte **+ přidat**.

1. Na **přidejte trasu** podokně zadejte následující informace:

   **Název**: Název pravidla směrování.

   **Koncový bod**: Vyberte koncový bod, který jste vytvořili.

   **Zdroj dat**: Vyberte **zařízení Telemetrických zpráv**.

   **Směrování dotazů**: Zadejte `temperatureAlert = "true"`.

   ![Přidat pravidlo směrování na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Vyberte **Uložit**. Můžete zavřít **směrování zpráv** podokně.

## <a name="create-and-configure-a-logic-app"></a>Vytvoření a konfigurace aplikace logiky

V předchozí části Nastavení služby IoT hub pro směrování zpráv obsahující teploty upozornění do fronty služby Service Bus. Nyní nastavíte aplikaci logiky, která sledovat frontu služby Service Bus a odeslat e-mailové oznámení pokaždé, když přidá zprávu do fronty.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. Vyberte **vytvořit prostředek** > **integrace** > **aplikace logiky**.

1. Zadejte následující informace:

   **Název**: Název aplikace logiky.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: Použijte stejné umístění, která používá službu IoT hub.

   ![Vytvoření aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Vyberte **Vytvořit**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurace triggeru aplikace logiky

1. Otevřete aplikaci logiky. Nejjednodušší způsob, jak získat aplikace logiky je výběr **skupiny prostředků** z podokna prostředků vyberte skupinu prostředků a pak ze seznamu prostředků vyberte svou aplikaci logiky. Když vyberete aplikaci logiky, otevře se návrhář pro Logic Apps.

1. V návrháři pro Logic Apps, přejděte dolů k položce **šablony** a vyberte **prázdná aplikace logiky**.

   ![Začněte s prázdné aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Vyberte **všechny** kartu a potom vyberte **služby Service Bus**.

   ![Výběr služby Service Bus a začněte vytvářet aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. V části **triggery**vyberte **při doručení jedné či více zpráv do fronty (automatické dokončení)**.

   ![Vyberte trigger pro vaše aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Vytvoření připojení pomocí service bus.
   1. Zadejte název připojení a zvolte svůj obor názvů služby Service Bus ze seznamu. Otevře se na další obrazovce.

      ![Vytvoření připojení pomocí service bus pro vaši aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Vyberte zásady service bus (RootManageSharedAccessKey). Potom vyberte **vytvořit**.

      ![Vytvoření připojení pomocí service bus pro vaši aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na poslední obrazovce pro **název fronty**, vyberte frontu, kterou jste vytvořili z rozevíracího seznamu. Zadejte `175` pro **maximální počet zpráv**.

      ![Zadejte maximální počet pro připojení služby Service bus ve vaší aplikaci logiky](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Vyberte **Uložit** v nabídce v horní části návrháře pro Logic Apps uložte provedené změny.

### <a name="configure-the-logic-app-action"></a>Konfigurace akce aplikace logiky

1. Vytvořte připojení služby SMTP.

   1. Vyberte **Nový krok**. V **zvolte akci**, vyberte **všechny** kartu.

   1. Typ `smtp` do vyhledávacího pole, vyberte **SMTP** služby ve výsledcích hledání a potom vyberte **odeslání e-mailu**.

      ![Vytvoření připojení k SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Zadejte informace o SMTP pro poštovní schránku a pak vyberte **vytvořit**.

      ![Zadejte informace o připojení SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Získejte informace o protokolu SMTP pro [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), a [e-mailu Yahoo](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Budete muset zakázat SSL, aby se navázalo připojení. Pokud tomu tak a chcete po vytvořilo se připojení znovu povolit protokol SSL, přečtěte si téma volitelný krok na konci této části.

   1. Z **přidat nový parametr** rozevírací seznam na **odeslání e-mailu** kroku, vyberte **z**, **k**, **subjektu**a **tělo**. Klikněte nebo klepněte na libovolné místo na obrazovce, zavřete dialogové okno Výběr.

      ![Zvolte pole e-mailové připojení SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Zadejte svou e-mailovou adresu pro **z** a **k**, a `High temperature detected` pro **subjektu** a **tělo**. Pokud **Přidat dynamický obsah z aplikací a konektorů, které používají v tomto toku** otevře se dialogové okno, vyberte **skrýt** ho zavřít. V tomto kurzu nepoužijete dynamický obsah.

      ![Pole e-mailové připojení SMTP vyplňování](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Vyberte **Uložit** uložte připojení SMTP.

1. (Volitelné) Pokud jste měli zakázat SSL k zahájení připojení u svého poskytovatele e-mailu a chcete ho znovu povolit, postupujte podle těchto kroků:

   1. Na **aplikace logiky** podokně v části **nástroje pro vývoj**vyberte **připojení rozhraní API**.

   1. V seznamu připojení rozhraní API vyberte připojení SMTP.

   1. Na **smtp pro propojení rozhraní API** podokně v části **Obecné**vyberte **připojení API. upravte**.

   1. Na **upravit připojení rozhraní API** vyberte **povolit SSL?**, znovu zadejte heslo pro e-mailový účet a vyberte **Uložit**.

      ![Upravit připojení rozhraní API SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Aplikace logiky je teď připravena ke zpracování teploty výstrahy z fronty služby Service Bus a odesílat oznámení e-mailový účet.

## <a name="test-the-logic-app"></a>Otestování aplikace logiky

1. Spuštění klientské aplikace na vašem zařízení.

1. Pokud používáte fyzické zařízení, heat zdroj téměř senzor heat dokud teplota překročí 30 C. stupňů pečlivě přeneste Pokud při použití online simulátoru, kód klienta náhodně výstup telemetrických zpráv, které překračují 30 C.

1. Měli byste začít e-mailových oznámení odeslané aplikací logiky.

   > [!NOTE]
   > Váš poskytovatel služeb e-mailu může potřebovat ověřit Identita odesílatele, aby se zajistilo, že se že jedná o vás, který odešle e-mail.

## <a name="next-steps"></a>Další postup

Úspěšně jste vytvořili aplikaci logiky, která propojuje službu IoT hub a poštovní schránky pro monitorování teploty a oznámení.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
