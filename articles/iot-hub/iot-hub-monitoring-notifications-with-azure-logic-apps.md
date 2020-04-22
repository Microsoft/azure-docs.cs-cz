---
title: Vzdálené monitorování ioT a oznámení pomocí aplikací Azure Logic Apps | Dokumenty společnosti Microsoft
description: Používejte Azure Logic Apps pro monitorování teploty IoT na vašem centru IoT a automaticky odesílat e-mailová oznámení do vaší poštovní schránky pro všechny zjištěné anomálie.
author: robinsh
keywords: iot monitoring, iot oznámení, iot monitorování teploty
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 2720f9acfa308294b30f9203ba80e3f9b426e1e9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680724"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Vzdálené monitorování ioT a oznámení pomocí aplikací Azure Logic Apps, které propojují vaše centrum IoT a poštovní schránku

![Diagram koncového bodu](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) vám pomůže organizovat pracovní postupy napříč místními a cloudovými službami, jedním nebo více podniky a různými protokoly. Aplikace logiky začíná aktivační událost, která je pak následuje jedna nebo více akcí, které lze sekvencovat pomocí integrovaných ovládacích prvků, jako jsou podmínky a iterátory. Díky této flexibilitě je aplikace Logic Apps ideálním řešením IoT pro scénáře monitorování IoT. Například příchod telemetrických dat ze zařízení v koncovém bodě služby IoT Hub může zahájit pracovní postupy aplikace logiky pro ukládání dat do objektu blob služby Azure Storage, odesílat e-mailová upozornění, aby varovali před anomáliemi dat, naplánovat návštěvu technika, pokud zařízení hlásí chybu a tak dále.

## <a name="what-you-learn"></a>Co se naučíte

Dozvíte se, jak vytvořit aplikaci logiky, která propojuje vaše centrum IoT a poštovní schránku pro monitorování teploty a oznámení.

Kód klienta spuštěný na vašem `temperatureAlert`zařízení nastaví vlastnost aplikace , na každé telemetrické zprávě, kterou odešle do vašeho centra IoT Hub. Když kód klienta detekuje teplotu vyšší než 30 `true`°C, nastaví tuto vlastnost na ; v opačném případě nastaví vlastnost na `false`.

Zprávy přicházející do služby IoT hub vypadají podobně jako následující, s telemetrickádata obsažená v těle a `temperatureAlert` vlastnost obsažená ve vlastnostech aplikace (vlastnosti systému nejsou zobrazeny):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Další informace o formátu zpráv služby IoT Hub najdete v [tématu Vytváření a čtení zpráv služby IoT Hub](iot-hub-devguide-messages-construct.md).

V tomto tématu nastavíte směrování v centru IoT `temperatureAlert` pro `true` odesílání zpráv, ve kterých je vlastnost koncového bodu service bus. Potom nastavíte aplikaci logiky, která se aktivuje na zprávy přicházející do koncového bodu service bus a odešle e-mailové oznámení.

## <a name="what-you-do"></a>Co děláte

* Vytvořte obor názvů service bus a přidejte do něj frontu služby Service Bus.
* Přidejte vlastní koncový bod a pravidlo směrování do centra IoT směrovat zprávy, které obsahují upozornění na teplotu do fronty Service Bus.
* Vytvořte, nakonfigurujte a otestujte aplikaci logiky tak, aby spotřebovávaly zprávy z fronty služby Service Bus a odesílala e-maily s oznámením požadovanému příjemci.

## <a name="what-you-need"></a>Co potřebujete

* Dokončete kurz [online simulátoru Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) nebo jeden z výukových programů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto požadavky se vztahují na tyto požadavky:

  * Aktivní předplatné Azure.
  * Azure IoT hub v rámci vašeho předplatného.
  * Klientská aplikace spuštěná na vašem zařízení, která odesílá telemetrické zprávy do vašeho centra Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Vytvoření oboru názvů a fronty služby Service Bus

Vytvořte oboru názvů a frontu Service Bus. Dále v tomto tématu vytvoříte pravidlo směrování ve službě IoT hub pro přímé zprávy, které obsahují upozornění na teplotu do fronty Service Bus, kde budou vyzvednuty aplikací logiky a spustí te za účelem odeslání e-mailu s oznámením.

### <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus

1. Na [portálu Azure](https://portal.azure.com/)vyberte + Vytvořit**integrační** > **sběrnici** **integrace prostředků** > .

1. V podokně **Vytvořit obor názvů** zadejte následující informace:

   **Název**: Název oboru názvů sběrnice služby. Obor názvů musí být jedinečný v rámci Azure.

   **Cenová úroveň**: V rozevíracím seznamu vyberte **Základní.** Základní úroveň je dostatečná pro tento kurz.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, kterou používá vaše centrum IoT Hub.

   **Umístění:** Použijte stejné umístění, které používá vaše centrum IoT hub.

   ![Vytvoření oboru názvů sběrnice služby na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Vyberte **Vytvořit**. Před přechodem k dalšímu kroku počkejte na dokončení nasazení.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Přidání fronty služby Service Bus do oboru názvů

1. Otevřete obor názvů Service Bus. Nejjednodušší způsob, jak se dostat do oboru názvů Service Bus, je vybrat **skupiny prostředků** z podokna prostředků, vybrat skupinu prostředků a potom vybrat obor názvů Service Bus ze seznamu prostředků.

1. V podokně **Obor oboru názvů služby Service Bus** vyberte možnost + **Fronta**.

1. Zadejte název fronty a pak vyberte **Vytvořit**. Po úspěšném vytvoření fronty se podokno **Vytvořit frontu** zavře.

   ![Přidání fronty sběrnice na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Zpět v podokně **Obor názvů služby Service Bus** v části **Entity**vyberte **Možnost Fronty**. Otevřete frontu služby Service Bus ze seznamu a vyberte >  **zásady sdíleného přístupu****+ Přidat**.

1. Zadejte název zásady, **zaškrtněte políčko Spravovat**a vyberte **příkaz Vytvořit**.

   ![Přidání zásad fronty služby Service Bus na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Přidání vlastního koncového bodu a pravidla směrování do služby IoT hub

Přidejte vlastní koncový bod pro frontu Service Bus do centra IoT a vytvořte pravidlo směrování zpráv pro přímé zprávy, které obsahují upozornění na teplotu do tohoto koncového bodu, kde budou vyzvednuty vaší aplikací logiky. Pravidlo směrování používá směrovací `temperatureAlert = "true"`dotaz , k předávání zpráv `temperatureAlert` na základě hodnoty vlastnosti aplikace nastavené klientským kódem spuštěným v zařízení. Další informace naleznete v [tématu Směrovací dotaz zprávy založený na vlastnostech zprávy](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Přidání vlastního koncového bodu

1. Otevřete centrum IoT. Nejjednodušší způsob, jak se dostat do centra IoT, je vybrat **skupiny prostředků** z podokna prostředků, vybrat skupinu prostředků a pak vybrat centrum IoT hub ze seznamu prostředků.

1. V části **Zasílání zpráv**vyberte **položku Směrování zpráv**. V podokně **Směrování zpráv** vyberte kartu Vlastní **koncové body** a pak vyberte + **Přidat**. V rozevíracím seznamu vyberte **frontu sběrnice služby**.

   ![Přidání koncového bodu do služby IoT hub na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. V podokně **Přidat koncový bod sběrnice** zadejte následující informace:

   **Název koncového bodu**: Název koncového bodu.

   **Obor názvů sběrnice**: Vyberte obor názvů, který jste vytvořili.

   **Fronta servisních sběrnic**: Vyberte frontu, kterou jste vytvořili.

   ![Přidání koncového bodu do služby IoT hub na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Vyberte **Vytvořit**. Po úspěšném vytvoření koncového bodu přejděte k dalšímu kroku.

### <a name="add-a-routing-rule"></a>Přidání pravidla směrování

1. Zpět v podokně **směrování zpráv** vyberte kartu **Trasy** a pak vyberte **+ Přidat**.

1. V **podokně Přidat trasu** zadejte následující informace:

   **Název**: Název pravidla směrování.

   **Koncový bod**: Vyberte koncový bod, který jste vytvořili.

   **Zdroj dat**: Vyberte **telemetrické zprávy zařízení**.

   **Směrový**dotaz `temperatureAlert = "true"`: Zadejte .

   ![Přidání pravidla směrování na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Vyberte **Uložit**. Podokno **směrování zpráv** můžete zavřít.

## <a name="create-and-configure-a-logic-app"></a>Vytvoření a konfigurace aplikace logiky

V předchozí části nastavíte službu IoT hub tak, aby směrovala zprávy obsahující upozornění na teplotu do fronty service bus. Nyní nastavíte aplikaci logiky pro sledování fronty service bus a odeslání e-mailové ho oznámení při každém přidání zprávy do fronty.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. Vyberte **možnost Vytvořit aplikaci** > **logiky****integrace** > prostředků .

1. Zadejte následující informace:

   **Název**: Název aplikace logiky.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, kterou používá vaše centrum IoT Hub.

   **Umístění:** Použijte stejné umístění, které používá vaše centrum IoT hub.

   ![Vytvoření aplikace logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Vyberte **Vytvořit**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurace aktivační události aplikace logiky

1. Otevřete aplikaci logiky. Nejjednodušší způsob, jak se dostat do aplikace **logiky,** je vybrat skupiny prostředků z podokna prostředků, vybrat skupinu prostředků a pak vybrat aplikaci logiky ze seznamu prostředků. Když vyberete aplikaci logiky, otevře se Návrhář logických aplikací.

1. V Návrháři aplikací **logiky** přejděte dolů na šablony a vyberte **Blank Logic App**.

   ![Začněte s prázdnou aplikací logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Vyberte kartu **Vše** a pak vyberte **Service Bus**.

   ![Vyberte Service Bus a začněte vytvářet aplikaci logiky na webu Azure Portal.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. V části **Aktivační události**vyberte Když jedna nebo více zpráv dorazí do **fronty (automatické dokončování).**

   ![Vyberte aktivační událost pro aplikaci logiky na webu Azure Portal.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Vytvořte připojení sběrnice.
   1. Zadejte název připojení a ze seznamu vyberte obor názvů služby Service Bus. Otevře se další obrazovka.

      ![Vytvoření připojení služby Service Bus pro aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Vyberte zásadu sběrnice (RootManageSharedAccessKey). Pak vyberte **Vytvořit**.

      ![Vytvoření připojení služby Service Bus pro aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na poslední obrazovce včásti **Název fronty**vyberte frontu, kterou jste vytvořili v rozevíracím souboru. Zadejte `175` **hodnotu Maximální počet zpráv**.

      ![Zadejte maximální počet zpráv pro připojení služby Service Bus v aplikaci logiky](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. V nabídce v horní části Návrháře aplikací logiky vyberte **Uložit,** chcete-li uložit změny.

### <a name="configure-the-logic-app-action"></a>Konfigurace akce aplikace logiky

1. Vytvořte připojení služby SMTP.

   1. Vyberte **Nový krok**. V **části Zvolte akci**vyberte kartu **Vše.**

   1. Zadejte `smtp` do vyhledávacího pole, ve výsledku hledání vyberte službu **SMTP** a pak vyberte **Odeslat e-mail**.

      ![Vytvoření připojení SMTP ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Zadejte informace smtp pro poštovní schránku a pak vyberte **Vytvořit**.

      ![Zadání informací o připojení SMTP v aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Získejte informace o smtp pro [Služby Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en)a [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Možná budete muset zakázat TLS/SSL k navázání připojení. Pokud se jedná o tento případ a chcete znovu povolit TLS po navázání připojení, naleznete volitelný krok na konci této části.

   1. V rozevíracím seznamu **Přidat nový parametr** v kroku **Odeslat e-mail** vyberte **Z**, **Do**, **Předmět** a **Tělo**. Kliknutím nebo klepnutím na libovolné místo na obrazovce zavřete výběrové pole.

      ![Zvolte pole e-mailu připojení SMTP.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Zadejte svou e-mailovou `High temperature detected` adresu pro **Od** a **Do**a pro **Předmět** a **Tělo**. Pokud se otevře **možnost Přidat dynamický obsah z aplikací a konektorů použitých v tomto dialogovém okně toku,** vyberte **Skrýt** a zavřete ho. V tomto kurzu nepoužíváte dynamický obsah.

      ![Vyplnit e-mailová pole připojení SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Chcete-li uložit připojení SMTP, vyberte **možnost Uložit.**

1. (Nepovinné) Pokud jste museli zakázat tls, abyste navázali spojení s poskytovatelem e-mailu a chcete ho znovu povolit, postupujte takto:

   1. V podokně **aplikace Logika** vyberte v části **Vývojové nástroje**připojení **rozhraní API**.

   1. Ze seznamu připojení rozhraní API vyberte připojení SMTP.

   1. V podokně **Připojení rozhraní smtp API** vyberte v části **Obecné**možnost Upravit připojení **rozhraní API**.

   1. V podokně **Upravit připojení rozhraní API** vyberte **Povolit ssl?**, znovu zadejte heslo pro svůj e-mailový účet a vyberte **Uložit**.

      ![Úprava připojení rozhraní SMTP API ve vaší aplikaci logiky na webu Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Vaše aplikace logiky je teď připravená zpracovat upozornění na teplotu z fronty Service Bus a odesílat oznámení na váš e-mailový účet.

## <a name="test-the-logic-app"></a>Otestování aplikace logiky

1. Spusťte klientskou aplikaci v zařízení.

1. Pokud používáte fyzické zařízení, opatrně přiveďte zdroj tepla v blízkosti tepelného senzoru, dokud teplota nepřekročí 30 stupňů C. Pokud používáte online simulátor, kód klienta bude náhodně výstup telemetrické zprávy, které přesahují 30 C.

1. Měli byste začít dostávat e-mailová oznámení odeslaná aplikací logiky.

   > [!NOTE]
   > Váš poskytovatel e-mailových služeb může potřebovat ověřit identitu odesílatele, abyste se ujistili, že e-mail odešlete vy.

## <a name="next-steps"></a>Další kroky

Úspěšně jste vytvořili aplikaci logiky, která spojuje vaše centrum IoT a poštovní schránku pro sledování teploty a oznámení.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
