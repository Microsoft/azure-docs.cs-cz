---
title: Rozšíření Azure IoT Central pomocí vlastních pravidel a upozornění | Dokumentace Microsoftu
description: Jako vývojář řešení konfigurace aplikace IoT Central pro odeslání e-mailová oznámení, když se zastaví odesílání telemetrických dat ze zařízení. Toto řešení využívá Azure Stream Analytics a Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5248b9546ffe931b72123778d0d23574e5238405
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742409"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Rozšíření Azure IoT Central pomocí vlastního pravidla, které odesílají oznámení

Tato příručka ukazuje, jako vývojář řešení rozšíření IoT Central aplikace pomocí vlastního pravidla a oznámení. Příklad odesílání oznámení pro operátora, když se zastaví odesílání telemetrických dat ze zařízení. Toto řešení využívá [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) dotazu ke zjištění po zastavení odesílání telemetrických dat ze zařízení. Úlohy Stream Analytics používá [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) odesílat oznámení e-mailů pomocí [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Tato příručka ukazuje, jak rozšířit nad rámec jeho již přínosech s integrovanou pravidla a akce IoT Central.

V této příručce s postupy se dozvíte, jak:

* Stream telemetrická data z aplikace IoT Central s využitím *nepřetržitý export dat*.
* Vytvoření dotazu Stream Analytics, který zjistí, zastavil zařízení odesílá data.
* Odeslání e-mailové oznámení pomocí Azure Functions a služby SendGrid.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, budete potřebovat aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

### <a name="iot-central-application"></a>Aplikace IoT Central

Vytvořit aplikace IoT Central [Azure IoT Central – Moje aplikace](https://aka.ms/iotcentral) stránka s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Plán plateb | Průběžné platby |
| Šablona aplikace | Sample Contoso (Ukázka Contoso) |
| Název aplikace | Přijměte výchozí adresář nebo zvolte svůj vlastní název |
| zprostředkovatele identity | Přijměte výchozí adresář nebo zvolte vlastní jedinečnou předponu adresy URL |
| Adresář | Váš tenant Azure Active Directory |
| Předplatné Azure | Vaše předplatné Azure |
| Oblast | USA – východ |

Příklady a snímky obrazovky v tomto článku používají **USA – východ** oblasti. Vyberte umístění blízko vás a ujistěte se, že vytváření všech vašich prostředků ve stejné oblasti.

### <a name="resource-group"></a>Skupina prostředků

Použití [webu Azure portal vytvořte skupinu prostředků](https://portal.azure.com/#create/Microsoft.ResourceGroup) volá **DetectStoppedDevices** tak, aby obsahovala další prostředky, které vytvoříte. Vytvoření prostředků Azure ve stejném umístění jako aplikace IoT Central.

### <a name="event-hubs-namespace"></a>Obor názvů služby Event Hubs

Použití [webu Azure portal vytvořte obor názvů služby Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Name    | Vyberte název vašeho oboru názvů |
| Cenová úroveň | Basic |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Location | USA – východ |
| Jednotky propustnosti | 1 |

### <a name="stream-analytics-job"></a>Úlohy Stream Analytics

Použití [webu Azure portal k vytvoření úlohy Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Name    | Vyberte název požadované úlohy |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Location | USA – východ |
| Hostitelské prostředí | Cloud |
| Jednotky streamování | 3 |

### <a name="function-app"></a>Function App

Použití [webu Azure portal k vytvoření aplikace function app](https://portal.azure.com/#create/Microsoft.FunctionApp) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| App name (Název aplikace)    | Vyberte název vaší aplikace funkcí |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Operační systém | Windows |
| Plán hostování | Plán Consumption |
| Location | USA – východ |
| Zásobník modulu runtime | .NET |
| Úložiště | Vytvořit nový |

### <a name="sendgrid-account"></a>Účet SendGrid

Použití [webu Azure portal k vytvoření účtu SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Name    | Volba názvu účtu SendGrid |
| Heslo | Vytvořit heslo |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Cenová úroveň | F1 Free |
| Kontaktní informace | Vyplnění požadovaných informací |

Když vytvoříte všechny požadované prostředky, vaše **DetectStoppedDevices** skupiny prostředků vypadá jako na následujícím snímku obrazovky:

![Zjistit skupinu prostředků zastavené zařízení](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Můžete nakonfigurovat aplikace IoT Central můžete průběžně exportovat telemetrii do centra událostí. V této části vytvoříte Centrum událostí pro příjem telemetrických dat z vaší aplikace IoT Central. Centrum událostí přináší telemetrie vaší úlohy Stream Analytics ke zpracování.

1. Na webu Azure Portal, přejděte do svého oboru názvů služby Event Hubs a vyberte **+ Centrum událostí**.
1. Název vašeho centra událostí **centralexport**a vyberte **vytvořit**.

Váš obor názvů služby Event Hubs bude vypadat jako na následujícím snímku obrazovky:

![Obor názvů služby Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Získání klíče rozhraní API SendGrid

Aplikace function app vyžaduje klíč rozhraní API SendGrid k odesílání e-mailové zprávy. Chcete-li vytvořit klíč rozhraní API SendGrid:

1. Na webu Azure Portal přejděte do svého účtu SendGrid. Klikněte na tlačítko **spravovat** přístup ke svému účtu SendGrid.
1. Ve vašem účtu SendGrid vyberte **nastavení**, pak **klíče rozhraní API**. Zvolte **vytvořit klíč rozhraní API**:

    ![Vytvořit klíč rozhraní API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na **vytvořit klíč rozhraní API** stránky, vytvořte klíč s názvem **AzureFunctionAccess** s **úplný přístup** oprávnění.
1. Poznamenejte si klíč rozhraní API, budete potřebovat při konfiguraci aplikace function app.

## <a name="define-the-function"></a>Definovat funkci

Toto řešení používá k odesílání e-mailové oznámení, pokud úlohy Stream Analytics detekuje zastavené zařízení aplikaci Azure Functions. Pokud chcete vytvořit aplikaci function app:

1. Na webu Azure Portal, přejděte **služby App Service** instance v **DetectStoppedDevices** skupinu prostředků.
1. Vyberte **+** k vytvoření nové funkce.
1. Na **ZVOLTE VÝVOJOVÉ prostředí A** zvolte **na portálu** a pak vyberte **pokračovat**.
1. Na **CREATE A FUNCTION** zvolte **Webhook + API** a pak vyberte **vytvořit**.

Portál vytvoří výchozí funkci s názvem **HttpTrigger1**:

![Výchozí funkci triggeru HTTP](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Nakonfigurujte vazby – funkce

K odesílání e-mailů pomocí Sendgridu, budete muset následujícím způsobem nakonfigurujte vazby funkce:

1. Vyberte **integrace**, zvolte Výstup **HTTP ($return)** a pak vyberte **odstranit**.
1. Zvolte **+ nový výstup**, klikněte na tlačítko **SendGrid**a klikněte na tlačítko **vyberte**. Zvolte **nainstalovat** instalace rozšíření služby SendGrid.
1. Po dokončení instalace, vybrat **používat návratovou hodnotu funkce**. Přidejte platný **adresu** pro příjem e-mailová oznámení.  Přidejte platný **z adresy** pro použití jako e-mailu odesílatele.
1. Vyberte **nové** vedle **nastavení aplikace pro klíč rozhraní API SendGrid**. Zadejte **SendGridAPIKey** jako klíči a klíč rozhraní SendGrid API, který jste si poznamenali dříve jako hodnotu. Potom vyberte **Vytvořit**.
1. Zvolte **Uložit** uložit vazby služby SendGrid pro vaši funkci.

Nastavení integrace vypadat jako na následujícím snímku obrazovky:

![Integrace aplikace – funkce](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Přidejte kód – funkce

Chcete-li implementovat funkci, přidejte C# kód pro parsování příchozí žádost protokolu HTTP a odesílání e-mailů následujícím způsobem:

1. Zvolte **HttpTrigger1** fungovat ve vaší aplikaci funkcí a nahraďte C# kód následujícím kódem:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Až po uložení nového kódu, může se zobrazí chybová zpráva.

1. Vyberte **Uložit** uložit funkce.

### <a name="test-the-function-works"></a>Testování funkčnosti funkce

Chcete-li otestovat funkci na portálu, zvolte nejprve **protokoly** v dolní části editoru kódu. Klikněte na tlačítko **Test** napravo od editoru kódu. Použijte následující kód JSON, jako **text žádosti**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Zprávy protokolu funkce joinkind **protokoly** panelu:

![Výstup protokolu – funkce](media/howto-create-custom-rules/function-app-logs.png)

Po několika minutách **k** e-mailová adresa přijímá e-mail s následujícím obsahem:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Přidání dotazu Stream Analytics

Toto řešení používá dotazu Stream Analytics ke zjištění, kdy zařízení zastaví odesílání telemetrických dat pro více než 120 sekund. Dotaz používá jako vstup telemetrická data z centra událostí. Úloha odešle výsledky dotazu do aplikace function app. V této části můžete nakonfigurovat úlohu Stream Analytics:

1. Na webu Azure Portal, přejděte k vaší úlohy Stream analytics v části **topologie úlohy** vyberte **vstupy**, zvolte **+ přidat vstup streamu**a klikněte na tlačítko **událostí Centrum**.
1. Konfigurace vstupu používáte event hub, kterou jste předtím vytvořili pomocí informací v následující tabulce a pak zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias vstupu | centraltelemetry |
    | Předplatné | Vaše předplatné |
    | Obor názvů centra událostí | Váš obor názvů centra událostí |
    | Název centra událostí | Použít existující - **centralexport** |

1. V části **topologie úlohy**vyberte **výstupy**, zvolte **+ přidat**a klikněte na tlačítko **funkce Azure Functions**.
1. Použijte informace v následující tabulce ke konfiguraci výstupu a potom zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias pro výstup | EmailNotification |
    | Předplatné | Vaše předplatné |
    | Function App | Aplikace function app |
    | Funkce  | HttpTrigger1 |

1. V části **topologie úlohy**vyberte **dotazu** a nahraďte existující dotaz SQL následující:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Vyberte **Uložit**.
1. Spustit úlohu Stream Analytics, zvolte **přehled**, pak **spustit**, pak **nyní**a potom **Start**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Nakonfigurovat export v IoT Central

Přejděte [aplikace IoT Central](https://aka.ms/iotcentral) vytvoříte pomocí šablony Contoso. V této části nakonfigurujete aplikaci, aby datový proud telemetrie ze své simulovaných zařízení do vašeho centra událostí. Postup konfigurace exportu:

1. Přejděte **průběžný Export dat** stránce **+ nový**a potom **Azure Event Hubs**.
1. Použijte následující nastavení konfigurace exportu a pak vyberte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zobrazované jméno | Export do služby Event Hubs |
    | Enabled | Zapnuto |
    | Obor názvů služby Event Hubs | Název vašeho oboru názvů služby Event Hubs |
    | Centrum událostí | centralexport |
    | Měření | Zapnuto |
    | Zařízení | Vypnuto |
    | Šablony zařízení | Vypnuto |

![Souvislá datová exportovat konfiguraci](media/howto-create-custom-rules/cde-configuration.png)

Počkejte, dokud je stav exportu **systémem** předtím, než budete pokračovat.

## <a name="test"></a>Test

Otestování řešení, můžete zakázat nepřetržitý export dat z IoT Central pro zastavené Simulovaná zařízení:

1. Ve vaší aplikaci IoT Central, přejděte na **průběžný Export dat** stránku a vybrat **Export do služby Event Hubs** exportovat konfiguraci.
1. Nastavte **povoleno** k **vypnout** a zvolte **Uložit**.
1. Po nejméně dvě minuty **k** e-mailová adresa přijímá jeden nebo více e-mailů, které vypadají jako v následujícím příkladu obsahu:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Pořádek

Po tomto postupy pořádek a vyhnout se zbytečné náklady, odstranit **DetectStoppedDevices** skupinu prostředků na webu Azure Portal.

Můžete odstranit z aplikace IoT Central **správu** stránky v aplikaci.

## <a name="next-steps"></a>Další postup

V této příručce s postupy jste zjistili, jak:

* Stream telemetrická data z aplikace IoT Central s využitím *nepřetržitý export dat*.
* Vytvoření dotazu Stream Analytics, který zjistí, zastavil zařízení odesílá data.
* Odeslání e-mailové oznámení pomocí Azure Functions a služby SendGrid.

Teď, když víte, jak vytvořit vlastní pravidla a oznámení, navrhované dalším krokem je další způsob [rozšíření Azure IoT Central s vlastní analytics](howto-create-custom-analytics.md).
