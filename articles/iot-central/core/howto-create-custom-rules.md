---
title: Rozšiřování IoT Central Azure s vlastními pravidly a oznámeními | Microsoft Docs
description: Jako vývojář řešení můžete nakonfigurovat aplikaci IoT Central, aby odesílala e-mailová oznámení v případě, že zařízení přestane odesílat telemetrii. Toto řešení používá Azure Stream Analytics, Azure Functions a SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: f6c8272f736e2f83b4d33f3d61ce83356aa40e5d
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126752"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Rozšíření Azure IoT Central o vlastní pravidla s využitím služeb Stream Analytics, Azure Functions a SendGrid

V této příručce se dozvíte, jak pomocí vývojářů řešení rozšíříte svou IoT Central aplikaci pomocí vlastních pravidel a oznámení. Příklad ukazuje odeslání oznámení do operátoru, když zařízení přestane odesílat telemetrii. Řešení používá [Azure Stream Analytics](../../stream-analytics/index.yml) dotaz k detekci, kdy zařízení zastavilo odesílání telemetrie. Stream Analytics úloha používá [Azure Functions](../../azure-functions/index.yml) k posílání e-mailů s oznámením pomocí [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

V této příručce se dozvíte, jak můžete IoT Central nad rámec toho, co už s vestavěnými pravidly a akcemi udělat.

V této příručce se dozvíte, jak:

* Pomocí *průběžného exportu dat*Streamujte telemetrii z IoT Central aplikace.
* Vytvořte Stream Analytics dotaz, který zjistí, kdy zařízení zastavilo odesílání dat.
* Odešlete e-mailové oznámení pomocí služeb Azure Functions a SendGrid.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

### <a name="iot-central-application"></a>IoT Central aplikace

Na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) vytvořte aplikaci IoT Central s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Cenový tarif | Standard |
| Šablona aplikace | Analýzy v obchodě – monitorování podmínek |
| Název aplikace | Přijměte výchozí nebo vyberte svůj vlastní název. |
| URL | Přijměte výchozí nebo vyberte vlastní jedinečnou předponu adresy URL. |
| Adresář | Váš tenant Azure Active Directory |
| Předplatné Azure | Vaše předplatné Azure |
| Oblast | Vaše nejbližší oblast |

Příklady a snímky obrazovky v tomto článku používají oblast **USA** . Vyberte umístění, které chcete zavřít, a ujistěte se, že jste vytvořili všechny prostředky ve stejné oblasti.

Tato šablona aplikace zahrnuje dvě simulovaná termostata zařízení, která odesílají telemetrii.

### <a name="resource-group"></a>Skupina prostředků

Pomocí [Azure Portal vytvořte skupinu prostředků](https://portal.azure.com/#create/Microsoft.ResourceGroup) s názvem **DetectStoppedDevices** , která bude obsahovat další prostředky, které vytvoříte. Vytvořte prostředky Azure ve stejném umístění jako vaše aplikace IoT Central.

### <a name="event-hubs-namespace"></a>Obor názvů služby Event Hubs

Pomocí [Azure Portal vytvořte obor názvů Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název    | Zvolit název oboru názvů |
| Cenová úroveň | Základní |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Umístění | USA – východ |
| Jednotky propustnosti | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics úlohy

Pomocí [Azure Portal vytvořte úlohu Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název    | Vyberte název vaší úlohy. |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Umístění | USA – východ |
| Hostitelské prostředí | Cloud |
| Jednotky streamování | 3 |

### <a name="function-app"></a>Aplikace funkcí

Pomocí [Azure Portal vytvořte aplikaci funkcí](https://portal.azure.com/#create/Microsoft.FunctionApp) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název aplikace    | Zvolit název aplikace Function App |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Operační systém | Windows |
| Plán Hosting | Plán Consumption |
| Umístění | USA – východ |
| Zásobník modulu runtime | .NET |
| Úložiště | Vytvořit nový |

### <a name="sendgrid-account"></a>Účet SendGrid

Pomocí [Azure Portal vytvořte účet SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název    | Vyberte název účtu SendGrid. |
| Heslo | Vytvořit heslo |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Cenová úroveň | F1 Free |
| Kontaktní informace | Vyplnit požadované informace |

Po vytvoření všech požadovaných prostředků vypadá vaše skupina prostředků **DetectStoppedDevices** jako na následujícím snímku obrazovky:

![Zjistit skupinu prostředků zastavených zařízení](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

IoT Central aplikaci můžete nakonfigurovat tak, aby průběžně exportovali telemetrii do centra událostí. V této části vytvoříte centrum událostí pro příjem telemetrie z vaší aplikace IoT Central. Centrum událostí doručí telemetrii do vaší Stream Analytics úlohy ke zpracování.

1. V Azure Portal přejděte na obor názvů Event Hubs a vyberte **+ centrum událostí**.
1. Pojmenujte centrum událostí **centralexport**a vyberte **vytvořit**.

Váš Event Hubs obor názvů vypadá jako na následujícím snímku obrazovky:

![Obor názvů služby Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Získat klíč rozhraní API pro SendGrid

Vaše aplikace Function App potřebuje k posílání e-mailových zpráv klíč rozhraní SendGrid API. Vytvoření klíče rozhraní API pro SendGrid:

1. V Azure Portal přejděte na svůj účet SendGrid. Pak zvolte **Spravovat** pro přístup k účtu SendGrid.
1. V účtu SendGrid zvolte **Nastavení**a potom **klíče rozhraní API**. Vyberte **vytvořit klíč rozhraní API**:

    ![Vytvoření klíče rozhraní API pro SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na stránce **vytvořit klíč rozhraní API** vytvořte klíč s názvem **AzureFunctionAccess** s oprávněním **úplný přístup** .
1. Poznamenejte si klíč rozhraní API, budete ho potřebovat při konfiguraci aplikace Function App.

## <a name="define-the-function"></a>Definovat funkci

Toto řešení používá aplikaci Azure Functions k odeslání e-mailového oznámení, když úloha Stream Analytics detekuje zastavené zařízení. Vytvoření aplikace Function App:

1. V Azure Portal přejděte na instanci **App Service** ve skupině prostředků **DetectStoppedDevices** .
1. Tuto možnost vyberte **+** , pokud chcete vytvořit novou funkci.
1. Na stránce **Zvolte vývojové prostředí** zvolte **in-Portal** a pak vyberte **pokračovat**.
1. Na stránce **vytvořit funkci** vyberte **Webhook + API** a pak vyberte **vytvořit**.

Portál vytvoří výchozí funkci nazvanou **HttpTrigger1**:

![Výchozí funkce triggeru HTTP](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurace vazeb funkcí

Pro posílání e-mailů pomocí SendGrid je nutné nakonfigurovat vazby pro funkci následujícím způsobem:

1. Vyberte možnost **integrace**, zvolte výstup **http ($Return)** a pak vyberte **Odstranit**.
1. Zvolte **+ Nový výstup**, pak zvolte **SendGrid**a pak zvolte **Vybrat**. Kliknutím na **nainstalovat** nainstalujte rozšíření SendGrid.
1. Po dokončení instalace vyberte **použít návratovou hodnotu funkce**. Přidejte platnou **adresu pro** příjem e-mailových oznámení.  Přidejte platnou **adresu z adresy** , kterou chcete použít jako odesílatele e-mailu.
1. V poli **nastavení aplikace klíč rozhraní API pro SendGrid**vyberte **Nový** . Jako klíč zadejte **SendGridAPIKey** a klíč rozhraní SendGrid API, který jste si dříve poznamenali jako hodnotu. Potom vyberte **Vytvořit**.
1. Kliknutím na **Uložit** uložte vazby SendGrid pro vaši funkci.

Nastavení integrace vypadají jako na následujícím snímku obrazovky:

![Integrace aplikací Function App](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Přidat kód funkce

K implementaci funkce přidejte kód jazyka C# k analýze příchozího požadavku HTTP a odešlete e-maily takto:

1. Ve své aplikaci Function App vyberte funkci **HttpTrigger1** a nahraďte kód v jazyce C# následujícím kódem:

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

    Může se zobrazit chybová zpráva, dokud neuložíte nový kód.

1. Vyberte **Uložit** a funkci uložte.

### <a name="test-the-function-works"></a>Testování funkce

Chcete-li otestovat funkci na portálu, nejprve v dolní části editoru kódu vyberte možnost **protokoly** . Pak zvolte **test** napravo od editoru kódu. Jako **Text žádosti**použijte následující JSON:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Zprávy protokolu funkcí se zobrazí na panelu **protokoly** :

![Výstup protokolu funkcí](media/howto-create-custom-rules/function-app-logs.png)

Po několika minutách **obdrží e-mailová** adresa e-mail s následujícím obsahem:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Přidat Stream Analytics dotaz

Toto řešení používá Stream Analytics dotaz k detekci, kdy se zařízení zastaví odesílání telemetrie na více než 120 sekund. Dotaz používá telemetrii z centra událostí jako svůj vstup. Úloha odešle výsledky dotazu do aplikace Function App. V této části nakonfigurujete úlohu Stream Analytics:

1. V Azure Portal přejděte na Stream Analytics úlohu, v části **topologie úlohy** vyberte **vstupy**, zvolte **+ Přidat vstup streamu**a pak zvolte **centrum událostí**.
1. Použijte informace v následující tabulce ke konfiguraci vstupu pomocí centra událostí, které jste předtím vytvořili, a pak zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias vstupu | centraltelemetry |
    | Předplatné | Vaše předplatné |
    | Obor názvů centra událostí | Obor názvů centra událostí |
    | Název centra událostí | Použít existující- **centralexport** |

1. V části **topologie úloh**vyberte **výstupy**, zvolte **+ Přidat**a pak zvolte **Azure Functions**.
1. Pomocí informací v následující tabulce nakonfigurujte výstup a pak zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias pro výstup | emailnotification |
    | Předplatné | Vaše předplatné |
    | Aplikace funkcí | Vaše aplikace Function App |
    | Funkce  | HttpTrigger1 |

1. V části **topologie úloh**vyberte **dotaz** a nahraďte stávající dotaz následujícím SQL:

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
1. Chcete-li spustit úlohu Stream Analytics, zvolte možnost **Přehled**, **Spustit**, **nyní**a potom **Spusťte**příkaz:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Konfigurace exportu v IoT Central

Na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) přejděte do IoT Central aplikace, kterou jste vytvořili ze šablony společnosti Contoso. V této části nakonfigurujete aplikaci pro streamování telemetrie z simulovaných zařízení do centra událostí. Konfigurace exportu:

1. Přejděte na stránku pro **Export dat** , vyberte **+ Nový**a pak **Azure Event Hubs**.
1. Pro konfiguraci exportu použijte následující nastavení a pak vyberte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zobrazovaný název | Exportovat do Event Hubs |
    | Povoleno | Zapnout |
    | Obor názvů služby Event Hubs | Název oboru názvů Event Hubs |
    | Centrum událostí | centralexport |
    | Měření | Zapnout |
    | Zařízení | Vypnout |
    | Šablony zařízení | Vypnout |

![Konfigurace kontinuálního exportu dat](media/howto-create-custom-rules/cde-configuration.png)

Než budete pokračovat, počkejte, než se **spustí** stav exportu.

## <a name="test"></a>Test

K otestování řešení můžete zakázat export průběžných dat z IoT Central na simulovaná zastavená zařízení:

1. V aplikaci IoT Central přejděte na stránku pro **Export dat** a vyberte **exportovat do Event Hubs** Exportovat konfiguraci.
1. Nastavte hodnotu **zapnuto** na **vypnuto** a klikněte na **Uložit**.
1. Po nejméně dvou minutách obdrží e-mailová adresa jeden nebo více e **-mailů** , které vypadají jako v následujícím ukázkovém obsahu:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Uklizený nahoru

Pokud se chcete uklizený po tomto postupu a vyhnout se zbytečným nákladům, odstraňte skupinu prostředků **DetectStoppedDevices** v Azure Portal.

Aplikaci IoT Central můžete odstranit ze stránky **pro správu** v rámci aplikace.

## <a name="next-steps"></a>Další kroky

V této příručce se naučíte:

* Pomocí *průběžného exportu dat*Streamujte telemetrii z IoT Central aplikace.
* Vytvořte Stream Analytics dotaz, který zjistí, kdy zařízení zastavilo odesílání dat.
* Odešlete e-mailové oznámení pomocí služeb Azure Functions a SendGrid.

Když teď víte, jak vytvářet vlastní pravidla a oznámení, je navržený další krok, kde se dozvíte, jak [rozšiřuje IoT Central Azure s vlastními analýzami](howto-create-custom-analytics.md).