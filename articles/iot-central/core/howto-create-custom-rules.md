---
title: Rozšiřování IoT Central Azure s vlastními pravidly a oznámeními | Microsoft Docs
description: Jako vývojář řešení můžete nakonfigurovat aplikaci IoT Central, aby odesílala e-mailová oznámení v případě, že zařízení přestane odesílat telemetrii. Toto řešení používá Azure Stream Analytics, Azure Functions a SendGrid.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 6146676121bac0089d5f520d60a97d74567a32bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179336"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Rozšíření Azure IoT Central o vlastní pravidla s využitím služeb Stream Analytics, Azure Functions a SendGrid

V této příručce se dozvíte, jak pomocí vývojářů řešení rozšíříte svou IoT Central aplikaci pomocí vlastních pravidel a oznámení. Příklad ukazuje odeslání oznámení do operátoru, když zařízení přestane odesílat telemetrii. Řešení používá [Azure Stream Analytics](../../stream-analytics/index.yml) dotaz k detekci, kdy zařízení zastavilo odesílání telemetrie. Stream Analytics úloha používá [Azure Functions](../../azure-functions/index.yml) k posílání e-mailů s oznámením pomocí [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

V této příručce se dozvíte, jak můžete IoT Central nad rámec toho, co už s vestavěnými pravidly a akcemi udělat.

V této příručce se dozvíte, jak:

* Pomocí *průběžného exportu dat* Streamujte telemetrii z IoT Central aplikace.
* Vytvořte Stream Analytics dotaz, který zjistí, kdy zařízení zastavilo odesílání dat.
* Odešlete e-mailové oznámení pomocí služeb Azure Functions a SendGrid.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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
| Cenová úroveň | Basic |
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
| Storage | Vytvořit nový |

### <a name="sendgrid-account-and-api-keys"></a>Účet SendGrid a klíče rozhraní API

Pokud nemáte účet SendGrid, vytvořte si [bezplatný účet](https://app.sendgrid.com/) před tím, než začnete.

1. V nastavení řídicího panelu SendGrid v levé nabídce vyberte možnost **klíče rozhraní API**.
1. Klikněte na **vytvořit klíč rozhraní API.**
1. Pojmenujte nový klíč rozhraní API **AzureFunctionAccess.**
1. Klikněte na tlačítko **vytvořit & zobrazení**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Snímek obrazovky s klíčem rozhraní API pro vytvoření SendGrid":::

Následně se vám bude předávat klíč rozhraní API. Uložte tento řetězec pro pozdější použití.

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

IoT Central aplikaci můžete nakonfigurovat tak, aby průběžně exportovali telemetrii do centra událostí. V této části vytvoříte centrum událostí pro příjem telemetrie z vaší aplikace IoT Central. Centrum událostí doručí telemetrii do vaší Stream Analytics úlohy ke zpracování.

1. V Azure Portal přejděte na obor názvů Event Hubs a vyberte **+ centrum událostí**.
1. Pojmenujte centrum událostí **centralexport** a vyberte **vytvořit**.

Váš Event Hubs obor názvů vypadá jako na následujícím snímku obrazovky: 

:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Snímek obrazovky s oborem názvů Event Hubs" border="false":::


## <a name="define-the-function"></a>Definovat funkci

Toto řešení používá aplikaci Azure Functions k odeslání e-mailového oznámení, když úloha Stream Analytics detekuje zastavené zařízení. Vytvoření aplikace Function App:

1. V Azure Portal přejděte na instanci **App Service** ve skupině prostředků **DetectStoppedDevices** .
1. Tuto možnost vyberte **+** , pokud chcete vytvořit novou funkci.
1. Vyberte **Trigger http**.
1. Vyberte **Přidat**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Obrázek výchozí funkce triggeru HTTP"::: 

## <a name="edit-code-for-http-trigger"></a>Upravit kód pro Trigger HTTP

Portál vytvoří výchozí funkci nazvanou **HttpTrigger1**:

:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Snímek obrazovky s funkcí upravit aktivační proceduru protokolu HTTP":::


1. Kód jazyka C# nahraďte následujícím kódem:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
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

## <a name="add-sendgrid-key"></a>Přidat klíč SendGrid

Pokud chcete přidat klíč rozhraní API SendGrid, musíte ho přidat k **klíčům funkcí** následujícím způsobem:

1. Vyberte **klíče funkce**.
1. Vyberte **+ nový klíč funkce**.
1. Zadejte *název* a *hodnotu* klíče rozhraní API, který jste vytvořili dříve.
1. Klikněte na tlačítko **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Snímek obrazovky s přidáním klíče Sangrid":::


## <a name="configure-httptrigger-function-to-use-sendgrid"></a>Konfigurace funkce HttpTrigger pro použití SendGrid

Pro posílání e-mailů pomocí SendGrid je nutné nakonfigurovat vazby pro funkci následujícím způsobem:

1. Vyberte **Integrace**.
1. V části **http ($Return)** vyberte **Přidat výstup** .
1. Vyberte **Odstranit.**
1. Vyberte **+ Nový výstup**.
1. Pro typ vazby zvolte **SendGrid**.
1. Pro typ nastavení klíč rozhraní SendGrid API klikněte na nový.
1. Zadejte *název* a *hodnotu* klíče rozhraní API SendGrid.
1. Přidejte následující informace:

| Nastavení | Hodnota |
| ------- | ----- |
| Název parametru zprávy | Zvolit jméno |
| Na adresu | Vyberte název, který chcete adresovat. |
| Z adresy | Vyberte jméno z adresy. |
| Předmět zprávy | Zadejte hlavičku předmětu |
| Text zprávy | Zadejte zprávu z integrace |

1. Vyberte **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Snímek obrazovky s přidáním výstupu SandGrid":::


### <a name="test-the-function-works"></a>Testování funkce

Chcete-li otestovat funkci na portálu, nejprve v dolní části editoru kódu vyberte možnost **protokoly** . Pak zvolte **test** napravo od editoru kódu. Jako **Text žádosti** použijte následující JSON:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Zprávy protokolu funkcí se zobrazí na panelu **protokoly** :

:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Výstup protokolu funkcí":::

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

1. V Azure Portal přejděte na Stream Analytics úlohu, v části **topologie úlohy** vyberte **vstupy**, zvolte **+ Přidat vstup streamu** a pak zvolte **centrum událostí**.
1. Použijte informace v následující tabulce ke konfiguraci vstupu pomocí centra událostí, které jste předtím vytvořili, a pak zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias vstupu | centraltelemetry |
    | Předplatné | Vaše předplatné |
    | Obor názvů centra událostí | Obor názvů centra událostí |
    | Název centra událostí | Použít existující- **centralexport** |

1. V části **topologie úloh** vyberte **výstupy**, zvolte **+ Přidat** a pak zvolte **Azure Functions**.
1. Pomocí informací v následující tabulce nakonfigurujte výstup a pak zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias pro výstup | emailnotification |
    | Předplatné | Vaše předplatné |
    | Aplikace funkcí | Vaše aplikace Function App |
    | Funkce  | HttpTrigger1 |

1. V části **topologie úloh** vyberte **dotaz** a nahraďte stávající dotaz následujícím SQL:

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
1. Chcete-li spustit úlohu Stream Analytics, zvolte možnost **Přehled**, **Spustit**, **nyní** a potom **Spusťte** příkaz:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Snímek obrazovky Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Konfigurace exportu v IoT Central 

Na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) přejděte na vytvořenou aplikaci IoT Central.

V této části nakonfigurujete aplikaci pro streamování telemetrie z simulovaných zařízení do centra událostí. Konfigurace exportu:

1. Přejděte na stránku pro **Export dat** , vyberte **+ Nový** a pak **Azure Event Hubs**.
1. Pro konfiguraci exportu použijte následující nastavení a pak vyberte **Uložit**: 

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zobrazovaný název | Exportovat do Event Hubs |
    | Povoleno | Zapnout |
    | Typ dat k exportu | Telemetrie |
    | Obohacení | Zadejte požadovaný klíč/hodnotu, jak chcete exportovat exportovaná data. | 
    | Cíl | Vytvořte nové a zadejte informace, kde se budou data exportovat. |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Snímek obrazovky s exportem dat":::

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

* Pomocí *průběžného exportu dat* Streamujte telemetrii z IoT Central aplikace.
* Vytvořte Stream Analytics dotaz, který zjistí, kdy zařízení zastavilo odesílání dat.
* Odešlete e-mailové oznámení pomocí služeb Azure Functions a SendGrid.

Když teď víte, jak vytvářet vlastní pravidla a oznámení, je navržený další krok, kde se dozvíte, jak [rozšiřuje IoT Central Azure s vlastními analýzami](howto-create-custom-analytics.md).
