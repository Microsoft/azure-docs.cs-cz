---
title: Vzdálené monitorování a oznámení IoT pomocí Azure Logic Apps | Microsoft Docs
description: Použijte Azure Logic Apps pro monitorování teploty IoT ve službě IoT Hub a automaticky zasílejte e-mailová oznámení do vaší poštovní schránky pro všechny zjištěné anomálie.
author: robinsh
keywords: monitorování IoT, oznámení IoT, monitorování teploty IoT
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 37f8016e087642ae0a7455e35f3ce18d7229e169
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146643"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Vzdálené monitorování a oznámení IoT pomocí Azure Logic Apps připojení ke službě IoT Hub a poštovní schránce

![Komplexní diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) vám může pomáhat při organizování pracovních postupů v místních i cloudových službách, v jednom nebo několika podnicích a v různých protokolech. Aplikace logiky začíná triggerem, který potom následuje jednou nebo více akcí, které lze sekvencovat pomocí integrovaných ovládacích prvků, jako jsou podmínky a iterátory. Tato flexibilita přináší Logic Apps ideální řešení IoT pro scénáře monitorování IoT. Například doručení dat telemetrie ze zařízení do IoT Hubho koncového bodu může iniciovat pracovní postupy Logic Apps, které skladují data v objektu blob Azure Storage, odesílají e-mailové výstrahy, které upozorňují na anomálie v datech, naplánování technici, pokud zařízení hlásí chybu, a tak dále.

## <a name="what-you-learn"></a>Co se naučíte

Naučíte se, jak vytvořit aplikaci logiky, která připojuje vaše centrum IoT a vaši poštovní schránku k monitorování a upozorňování na teplotu.

Klientský kód spuštěný v zařízení nastaví vlastnost aplikace, `temperatureAlert` v každé zprávě telemetrie, kterou odesílá do služby IoT Hub. Když kód klienta detekuje teplotu nad 30 C, nastaví tuto vlastnost na `true` ; v opačném případě nastaví vlastnost na `false` .

Zprávy přicházející ve službě IoT Hub vypadají podobně jako v následujícím příkladu s daty telemetrie obsaženými v těle a `temperatureAlert` vlastností obsaženými ve vlastnostech aplikace (systémové vlastnosti nejsou zobrazeny):

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

Další informace o formátu zpráv IoT Hub najdete v tématu [Vytvoření a čtení IoT Hub zpráv](iot-hub-devguide-messages-construct.md).

V tomto tématu nastavíte směrování ve službě IoT Hub pro odesílání zpráv, ve kterých `temperatureAlert` je vlastnost `true` do Service Busho koncového bodu. Pak nastavíte aplikaci logiky, která se spustí na zprávách přicházejících do Service Busho koncového bodu, a pošle vám e-mailové oznámení.

## <a name="what-you-do"></a>Co dělat

* Vytvořte obor názvů Service Bus a přidejte do něj frontu Service Bus.
* Přidejte vlastní koncový bod a pravidlo směrování do služby IoT Hub, abyste mohli směrovat zprávy, které obsahují upozornění na teplotu Service Bus fronty.
* Vytvoření, konfigurace a otestování aplikace logiky pro využívání zpráv z fronty Service Bus a odesílání e-mailů s oznámením požadovanému příjemci.

## <a name="what-you-need"></a>Co budete potřebovat

* Dokončete kurz [online simulátoru malin](iot-hub-raspberry-pi-web-simulator-get-started.md) . nebo v některém z kurzů zařízení; například [Malina Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto požadavky se týkají následujících požadavků:

  * Musíte mít aktivní předplatné Azure.
  * Azure IoT Hub v rámci vašeho předplatného.
  * Klientská aplikace spuštěná na vašem zařízení, která odesílá zprávy telemetrie do služby Azure IoT Hub.

## <a name="create-service-bus-namespace-and-queue"></a>Vytvořit Service Bus obor názvů a frontu

Vytvořte oboru názvů a frontu Service Bus. Později v tomto tématu vytvoříte pravidlo směrování ve službě IoT Hub, abyste mohli směrovat zprávy, které obsahují upozornění na teplotu, do fronty Service Bus, kde budou vyzvednuta aplikace logiky a aktivovat ji pro odesílání e-mailových oznámení.

### <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Busu

1. V [Azure Portal](https://portal.azure.com/)vyberte **+ vytvořit Service Bus pro**  >  **integraci**prostředků  >  **Service Bus**.

1. V podokně **vytvořit obor názvů** zadejte následující informace:

   **Name (název**): název oboru názvů služby Service Bus. Obor názvů musí být v rámci Azure jedinečný.

   **Cenová úroveň**: v rozevíracím seznamu vyberte **základní** . Úroveň Basic je pro tento kurz dostačující.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, kterou používá IoT Hub.

   **Umístění**: použijte stejné umístění, které používá vaše centrum IoT.

   ![Vytvoření oboru názvů služby Service Bus v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Vyberte **Vytvořit**. Než přejdete k dalšímu kroku, počkejte, než se nasazení dokončí.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Přidání fronty Service Bus do oboru názvů

1. Otevřete Service Bus obor názvů. Nejjednodušší způsob, jak získat Service Bus obor názvů, je vybrat **skupiny prostředků** v podokně prostředek, vybrat skupinu prostředků a potom v seznamu prostředků vybrat Service Bus obor názvů.

1. V podokně **Service Bus obor názvů** vyberte **+ fronta**.

1. Zadejte název fronty a pak vyberte **vytvořit**. Po úspěšném vytvoření fronty se zavře podokno **vytvořit frontu** .

   ![Přidat frontu služby Service Bus do Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Zpátky v podokně **obor názvů Service Bus** v části **entity**vyberte **fronty**. Otevřete Service Busovou frontu ze seznamu a pak vyberte **zásady sdíleného přístupu**  >  **+ Přidat**.

1. Zadejte název zásady, zaškrtněte **Spravovat**a pak vyberte **vytvořit**.

   ![Přidat zásadu fronty služby Service Bus do Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Přidání vlastního koncového bodu a pravidla směrování do služby IoT Hub

Přidáním vlastního koncového bodu pro frontu Service Bus do služby IoT Hub a vytvořením pravidla směrování zpráv můžete směrovat zprávy, které obsahují upozornění na teplotu tohoto koncového bodu, kde budou vyzvednuty vaší aplikací logiky. Pravidlo směrování používá dotaz směrování, `temperatureAlert = "true"` k posílání zpráv na základě hodnoty `temperatureAlert` Vlastnosti aplikace nastavené klientským kódem běžícím na zařízení. Další informace najdete v tématu [dotaz na směrování zpráv na základě vlastností zpráv](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Přidat vlastní koncový bod

1. Otevřete Centrum IoT. Nejjednodušší způsob, jak se dostat do služby IoT Hub, je vybrat **skupiny prostředků** v podokně prostředek, vybrat skupinu prostředků a pak ze seznamu prostředků vybrat centrum IoT.

1. V části **zasílání zpráv**vyberte **směrování zpráv**. V podokně **směrování zpráv** vyberte kartu **vlastní koncové body** a pak vyberte **+ Přidat**. V rozevíracím seznamu vyberte **fronta služby Service Bus**.

   ![Přidání koncového bodu do služby IoT Hub v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. V podokně **přidat koncový bod služby Service Bus** zadejte následující informace:

   **Název koncového bodu**: název koncového bodu.

   **Obor názvů služby Service Bus**: vyberte obor názvů, který jste vytvořili.

   **Fronta služby Service Bus**: Vyberte frontu, kterou jste vytvořili.

   ![Přidání koncového bodu do služby IoT Hub v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Vyberte **Vytvořit**. Po úspěšném vytvoření koncového bodu přejděte k dalšímu kroku.

### <a name="add-a-routing-rule"></a>Přidání pravidla směrování

1. Zpátky v podokně **směrování zpráv** vyberte kartu **trasy** a pak vyberte **+ Přidat**.

1. V podokně **Přidat trasu** zadejte následující informace:

   **Name**(název): název pravidla směrování.

   **Koncový bod**: vyberte koncový bod, který jste vytvořili.

   **Zdroj dat**: vyberte **zprávy telemetrie zařízení**.

   **Dotaz směrování**: zadejte `temperatureAlert = "true"` .

   ![Přidat pravidlo směrování do Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Vyberte **Uložit**. Podokno **směrování zpráv** můžete zavřít.

## <a name="create-and-configure-a-logic-app"></a>Vytvoření a konfigurace aplikace logiky

V předchozí části nastavíte službu IoT Hub pro směrování zpráv, které obsahují upozornění na teplotu, do fronty Service Bus. Teď nastavíte aplikaci logiky, která bude monitorovat Service Bus frontu a pošle e-mailové oznámení pokaždé, když se do fronty přidá zpráva.

### <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. Vyberte **vytvořit prostředek**  >  **Integration**  >  **Logic App**.

1. Zadejte následující informace:

   **Name**(název): název aplikace logiky.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, kterou používá IoT Hub.

   **Umístění**: použijte stejné umístění, které používá vaše centrum IoT.

   ![Vytvoření aplikace logiky v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Vyberte **Vytvořit**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurace triggeru aplikace logiky

1. Otevřete aplikaci logiky. Nejjednodušší způsob, jak se dostat do aplikace logiky, je vybrat **skupiny prostředků** v podokně prostředek, vybrat skupinu prostředků a pak ze seznamu prostředků vybrat vaši aplikaci logiky. Když vyberete aplikaci logiky, otevře se Návrhář Logic Apps.

1. V Návrháři Logic Apps přejděte dolů na **šablony** a vyberte **prázdná aplikace logiky**.

   ![Začněte s prázdnou aplikací logiky v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Vyberte kartu **vše** a pak vyberte **Service Bus**.

   ![Pokud chcete začít vytvářet aplikaci logiky v Azure Portal, vyberte Service Bus.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. V části **triggery**vyberte, **kdy se jedna nebo více zpráv dostane do fronty (automatické dokončování)**.

   ![Vyberte Trigger vaší aplikace logiky v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Vytvořte připojení ke službě Service Bus.
   1. Zadejte název připojení a v seznamu vyberte svůj obor názvů Service Bus. Otevře se další obrazovka.

      ![Vytvořte připojení ke službě Service Bus pro vaši aplikaci logiky v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Vyberte zásady služby Service Bus (RootManageSharedAccessKey). Pak vyberte  **vytvořit**.

      ![Vytvořte připojení ke službě Service Bus pro vaši aplikaci logiky v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na poslední obrazovce pro **název fronty**vyberte z rozevíracího seznamu frontu, kterou jste vytvořili. Zadejte `175` pro **maximální počet zpráv**.

      ![Zadejte maximální počet zpráv pro připojení služby Service Bus ve vaší aplikaci logiky.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. V nabídce v horní části návrháře Logic Apps vyberte **Uložit** a uložte provedené změny.

### <a name="configure-the-logic-app-action"></a>Konfigurace akce aplikace logiky

1. Vytvořte připojení služby SMTP.

   1. Vyberte **Nový krok**. V části **zvolit akci**vyberte kartu **vše** .

   1. Do `smtp` vyhledávacího pole zadejte, ve výsledku hledání vyberte službu **SMTP** a pak vyberte **Odeslat e-mail**.

      ![Vytvořte v aplikaci logiky v Azure Portal připojení SMTP.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Zadejte informace o protokolu SMTP pro poštovní schránku a potom vyberte **vytvořit**.

      ![V Azure Portal zadejte informace o připojení SMTP do aplikace logiky.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Získejte informace protokolu SMTP pro [Hotmail/Outlook. com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en)a [Yahoo mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > K navázání připojení možná budete muset protokol TLS/SSL zakázat. Pokud se jedná o případ a chcete znovu povolit protokol TLS po navázání připojení, přečtěte si volitelný krok na konci této části.

   1. V rozevíracím seznamu **Přidat nový parametr** v kroku **Odeslat E-mail** vyberte možnost **od**, **do**, **Předmět** a **text**. Kliknutím nebo klepnutím kamkoli na obrazovce zavřete pole výběr.

      ![Zvolit pole pro připojení k e-mailu SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Zadejte svou e-mailovou adresu **z** a **do**a `High temperature detected` pro **Předmět** a **text**. Pokud se otevře dialogové okno **Přidat dynamický obsah z aplikací a konektorů, které se používají v tomto okně Flow** , vyberte **Skrýt** a zavřete ho. V tomto kurzu nepoužíváte dynamický obsah.

      ![Vyplnit pole emailu pro připojení SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Vyberte **Uložit** a uložte připojení SMTP.

1. Volitelné Pokud jste museli zakázat protokol TLS pro navázání spojení s vaším poskytovatelem e-mailu a chcete ho znovu povolit, postupujte podle následujících kroků:

   1. V podokně **Aplikace logiky** v části **vývojové nástroje**vyberte **připojení rozhraní API**.

   1. V seznamu připojení rozhraní API vyberte připojení SMTP.

   1. V podokně **připojení SMTP API** v části **Obecné**vyberte **Upravit připojení rozhraní API**.

   1. V podokně **Upravit připojení rozhraní API** vyberte **Povolit SSL?**, znovu zadejte heslo pro váš e-mailový účet a vyberte **Uložit**.

      ![Upravte připojení SMTP API ve vaší aplikaci logiky v Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Vaše aplikace logiky je teď připravená na zpracování upozornění na teplotu z fronty Service Bus a odesílání oznámení na váš e-mailový účet.

## <a name="test-the-logic-app"></a>Otestování aplikace logiky

1. Spusťte na svém zařízení klientskou aplikaci.

1. Pokud používáte fyzické zařízení, pečlivě přeneste zdroj tepla poblíž tepelného senzoru, dokud teplota nepřekročí 30 stupňů C. Pokud používáte online simulátor, kód klienta bude náhodně vyvyšovat zprávy telemetrie, které překračují 30 C.

1. Zahajte příjem e-mailových oznámení odeslaných aplikací logiky.

   > [!NOTE]
   > Váš poskytovatel e-mailových služeb může potřebovat ověřit identitu odesilatele, aby se ujistil, že vám pošle e-mail.

## <a name="next-steps"></a>Další kroky

Úspěšně jste vytvořili aplikaci logiky, která připojuje vaše centrum IoT a vaši poštovní schránku k monitorování a upozorňování na teplotu.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]