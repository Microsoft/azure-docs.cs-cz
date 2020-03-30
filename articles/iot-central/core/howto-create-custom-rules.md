---
title: Rozšíření Azure IoT Central o vlastní pravidla a oznámení | Dokumenty společnosti Microsoft
description: Jako vývojář řešení nakonfigurujte aplikaci IoT Central tak, aby odesílala e-mailová oznámení, když zařízení přestane odesílat telemetrii. Toto řešení používá Azure Stream Analytics, Azure Functions a SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158142"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Rozšíření Azure IoT Central o vlastní pravidla pomocí Stream Analytics, Azure Functions a SendGrid



Tento návod vám jako vývojářřešení ukáže, jak rozšířit aplikaci IoT Central o vlastní pravidla a oznámení. Příklad ukazuje odeslání oznámení operátorovi, když zařízení přestane odesílat telemetrii. Řešení používá dotaz [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) ke zjištění, kdy zařízení přestalo odesílat telemetrii. Úloha Stream Analytics používá [funkce Azure](https://docs.microsoft.com/azure/azure-functions/) k odesílání e-mailů s oznámením pomocí [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Tento návod vám ukáže, jak rozšířit IoT Central nad rámec toho, co už může dělat s předdefinovanými pravidly a akcemi.

V tomto návodu se dozvíte, jak:

* Streamtele telemetrii z aplikace IoT Central pomocí *průběžného exportu dat*.
* Vytvořte dotaz Stream Analytics, který zjistí, kdy zařízení přestalo odesílat data.
* Odešlete e-mailové oznámení pomocí služeb Azure Functions a SendGrid.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto návodu, budete potřebovat aktivní předplatné Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

### <a name="iot-central-application"></a>Aplikace IoT Central

Vytvořte aplikaci IoT Central na webu [správce aplikací Azure IoT Central](https://aka.ms/iotcentral) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Cenový plán | Standard |
| Šablona aplikace | Analýza v obchodě – monitorování stavu |
| Název aplikace | Přijmout výchozí nastavení nebo zvolit vlastní jméno |
| zprostředkovatele identity | Přijmout výchozí hodnotu nebo zvolit vlastní jedinečnou předponu URL |
| Adresář | Váš klient Služby Azure Active Directory |
| Předplatné Azure | Vaše předplatné Azure |
| Region (Oblast) | Vaše nejbližší oblast |

Příklady a snímky obrazovky v tomto článku používají oblast **Spojených států.** Vyberte umístění blízko vás a ujistěte se, že vytvoříte všechny prostředky ve stejné oblasti.

Tato šablona aplikace obsahuje dvě simulovaná termostatová zařízení, která odesílají telemetrii.

### <a name="resource-group"></a>Skupina prostředků

Pomocí [portálu Azure vytvořte skupinu prostředků nazvanou](https://portal.azure.com/#create/Microsoft.ResourceGroup) **DetectStoppedDevices,** která bude obsahovat další prostředky, které vytvoříte. Vytvořte prostředky Azure ve stejném umístění jako vaše aplikace IoT Central.

### <a name="event-hubs-namespace"></a>Obor názvů služby Event Hubs

Na [webu Azure Portal můžete vytvořit obor názvů Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Name (Název)    | Zvolte název oboru názvů |
| Cenová úroveň | Basic |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Umístění | USA – východ |
| Jednotky propustnosti | 1 |

### <a name="stream-analytics-job"></a>Úloha Stream Analytics

Pomocí [portálu Azure vytvořte úlohu Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Name (Název)    | Vyberte si název své úlohy |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Umístění | USA – východ |
| Hostitelské prostředí | Cloud |
| Jednotky streamování | 3 |

### <a name="function-app"></a>Function App

Pomocí [portálu Azure vytvořte aplikaci funkcí](https://portal.azure.com/#create/Microsoft.FunctionApp) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| App name (Název aplikace)    | Zvolte název aplikace pro funkci |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Operační systém | Windows |
| Plán Hosting | Plán Consumption |
| Umístění | USA – východ |
| Zásobník modulu runtime | .NET |
| Úložiště | Vytvořit nový |

### <a name="sendgrid-account"></a>Účet SendGrid

Pomocí [portálu Azure vytvořte účet SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Name (Název)    | Zvolte název účtu SendGrid. |
| Heslo | Vytvoření hesla |
| Předplatné | Vaše předplatné |
| Skupina prostředků | DetectStoppedDevices |
| Cenová úroveň | F1 Free |
| Kontaktní informace | Vyplňte požadované informace |

Po vytvoření všech požadovaných prostředků vypadá vaše skupina prostředků **DetectStoppedDevices** jako následující snímek obrazovky:

![Zjišťování skupiny prostředků zastavených zařízení](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Můžete nakonfigurovat aplikaci IoT Central tak, aby průběžně exportovala telemetrii do centra událostí. V této části vytvoříte centrum událostí pro příjem telemetrie z aplikace IoT Central. Centrum událostí doručuje telemetrická data do úlohy Stream Analytics ke zpracování.

1. Na webu Azure Portal přejděte do oboru názvů Centra událostí a vyberte **+ Event Hub**.
1. Pojmenujte centrum událostí **centralexportace**a vyberte **Vytvořit**.

Obor názvů Event Hubs vypadá jako následující snímek obrazovky:

![Obor názvů služby Event Hubs](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Klíč rozhraní API GetGrid

Vaše aplikace funkce potřebuje pro odesílání e-mailových zpráv klíč rozhraní API SendGrid. Vytvoření klíče rozhraní API SendGrid:

1. Na webu Azure Portal přejděte na svůj účet SendGrid. Pak zvolte **Spravovat** pro přístup k účtu SendGrid.
1. V účtu SendGrid zvolte **Nastavení**a potom **klíče rozhraní API**. Zvolte **Vytvořit klíč rozhraní API**:

    ![Vytvořit klíč rozhraní API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na stránce **Vytvořit klíč rozhraní API** vytvořte klíč s názvem **AzureFunctionAccess** s **oprávněními úplného přístupu.**
1. Poznamenejte si klíč rozhraní API, který potřebujete při konfiguraci aplikace funkce.

## <a name="define-the-function"></a>Definování funkce

Toto řešení používá aplikaci Azure Functions k odeslání e-mailového oznámení, když úloha Stream Analytics zjistí zastavené zařízení. Vytvoření aplikace pro funkce:

1. Na webu Azure Portal přejděte na instanci **služby App Service** ve skupině prostředků **DetectStoppedDevices.**
1. Tuto **+** možnost vyberte, chcete-li vytvořit novou funkci.
1. Na stránce **ZVOLIT VÝVOJOVÉ PROSTŘEDÍ** zvolte **In-portal** a pak vyberte **Pokračovat**.
1. Na stránce **CREATE A FUNCTION** zvolte **Webhook + API** a pak vyberte **Create**.

Portál vytvoří výchozí funkci nazvanou **HttpTrigger1**:

![Výchozí aktivační funkce PROTOKOLU HTTP](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurace vazeb funkcí

Chcete-li odesílat e-maily s SendGrid, musíte nakonfigurovat vazby pro vaši funkci následujícím způsobem:

1. Vyberte **Integrovat**, zvolte výstupní **http ($return)** a pak vyberte **odstranit**.
1. Zvolte **+ Nový výstup**, pak zvolte **SendGrid**a pak zvolte **Vybrat**. Chcete-li nainstalovat rozšíření SendGrid, zvolte **Instalovat.**
1. Po dokončení instalace vyberte **použít vrácenou hodnotu funkce**. Přidejte platnou **adresu Chcete-li** dostávat e-mailová oznámení.  Přidejte platnou **adresu Od,** která se použije jako odesílatel e-mailu.
1. Vyberte **nový** vedle **nastavení aplikace Klíče rozhraní API SendGrid**. Jako klíč zadejte **sendgridAPIKey** a klíč rozhraní API SendGrid, který jste si dříve zavšimli jako hodnotu. Pak vyberte **Vytvořit**.
1. Zvolte **Uložit,** chcete-li uložit vazby SendGrid pro vaši funkci.

Nastavení integrace vypadá jako následující snímek obrazovky:

![Integrace aplikací funkcí](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Přidání kódu funkce

Chcete-li implementovat funkci, přidejte kód C# pro analýzu příchozího požadavku HTTP a odešlete e-maily následujícím způsobem:

1. Zvolte funkci **HttpTrigger1** ve vaší aplikaci funkcí a nahraďte kód C# následujícím kódem:

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

1. Chcete-li uložit **funkci,** vyberte uložit.

### <a name="test-the-function-works"></a>Otestujte funkční funkce funguje

Chcete-li otestovat funkci na portálu, nejprve zvolte **protokoly** v dolní části editoru kódu. Pak zvolte **Test** napravo od editoru kódu. Jako tělo požadavku použijte následující **json**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Zprávy protokolu funkcí se zobrazí v panelu **Protokoly:**

![Výstup protokolu funkcí](media/howto-create-custom-rules/function-app-logs.png)

Po několika minutách obdrží e-mailová adresa **Do** e-mail s následujícím obsahem:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Přidat dotaz Stream Analytics

Toto řešení používá dotaz Stream Analytics ke zjištění, kdy zařízení přestane odesílat telemetrii po dobu delší než 120 sekund. Dotaz používá telemetrická data z centra událostí jako jeho vstup. Úloha odešle výsledky dotazu do aplikace funkce. V této části nakonfigurujete úlohu Stream Analytics:

1. Na webu Azure Portal přejděte na úlohu Stream Analytics, v části **Topologie úloh** vyberte **Vstupy**, zvolte **+ Přidat vstup datového proudu**a pak zvolte Event **Hub**.
1. Pomocí informací v následující tabulce nakonfigurujte vstup pomocí dříve vytvořeného centra událostí a pak zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias vstupu | centrální telemetrie |
    | Předplatné | Vaše předplatné |
    | Obor názvů centra událostí | Obor názvů Centra událostí |
    | Název centra událostí | Použít existující - **centralexport** |

1. V části **Topologie úloh**vyberte **Výstupy**, zvolte **+ Přidat**a pak zvolte Azure **function**.
1. Ke konfiguraci výstupu použijte informace v následující tabulce a pak zvolte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Alias pro výstup | e-mailové oznámení |
    | Předplatné | Vaše předplatné |
    | Function App | Vaše funkční aplikace |
    | Funkce  | HttpTrigger1 |

1. V části **Topologie úloh**vyberte **Dotaz** a nahraďte existující dotaz následujícím SQL:

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
1. Chcete-li spustit úlohu Stream Analytics, zvolte **Přehled**, pak **Start**, then **Now**a then **Start**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Konfigurace exportu v centru IoT

Na webu [správce aplikací Azure IoT Central](https://aka.ms/iotcentral) přejděte na aplikaci IoT Central, kterou jste vytvořili ze šablony Contoso. V této části nakonfigurujete aplikaci pro streamování telemetrie ze simulovaných zařízení do centra událostí. Postup konfigurace exportu:

1. Přejděte na stránku **Export dat,** vyberte **+ Nový**a potom na Azure **Event Hubs**.
1. Ke konfiguraci exportu použijte následující nastavení a pak vyberte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zobrazovaný název | Export do centra událostí |
    | Povoleno | Zapnuto |
    | Obor názvů služby Event Hubs | Název oboru názvů Event Hubs |
    | Centrum událostí | centralexport |
    | Měření | Zapnuto |
    | Zařízení | Vypnuto |
    | Šablony zařízení | Vypnuto |

![Konfigurace průběžného exportu dat](media/howto-create-custom-rules/cde-configuration.png)

Než budete pokračovat, počkejte, dokud nebude **spuštěn** stav exportu.

## <a name="test"></a>Test

Chcete-li otestovat řešení, můžete zakázat nepřetržitý export dat z IoT Central do simulovaných zastavených zařízení:

1. V aplikaci IoT Central přejděte na stránku **Export dat** a vyberte konfiguraci exportu do centra **událostí.**
1. Nastavte **možnost Vypnuto** a zvolte **Uložit**. **Off**
1. Po nejméně dvou minutách obdrží e-mailová adresa **To** jeden nebo více e-mailů, které vypadají jako následující příklad obsahu:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Uklidit

Chcete-li uklidit po tomto návodu a vyhnout se zbytečným nákladům, odstraňte skupinu prostředků **DetectStoppedDevices** na webu Azure Portal.

Aplikaci IoT Central můžete odstranit ze stránky **Správa** v rámci aplikace.

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se naučili:

* Streamtele telemetrii z aplikace IoT Central pomocí *průběžného exportu dat*.
* Vytvořte dotaz Stream Analytics, který zjistí, kdy zařízení přestalo odesílat data.
* Odešlete e-mailové oznámení pomocí služeb Azure Functions a SendGrid.

Teď, když víte, jak vytvořit vlastní pravidla a oznámení, je navrhovaným dalším krokem naučit se [rozšířit Azure IoT Central pomocí vlastníanalýzy](howto-create-custom-analytics.md).
