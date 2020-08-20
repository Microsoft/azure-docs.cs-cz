---
title: Nastavení funkce Azure pro zpracování dat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit funkci Azure, která může přistupovat k digitálnímu vlákna a aktivovat je.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ab013a310997f43d1019d849e87c0cf1b0d151ee
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661091"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Připojení aplikací Azure Functions pro zpracování dat

Během období Preview se aktualizace digitálních vláken na základě dat zpracovává pomocí [**tras událostí**](concepts-route-events.md) prostřednictvím výpočetních prostředků, jako je [Azure Functions](../azure-functions/functions-overview.md). Funkce Azure může být použita k aktualizaci digitálního vlákna v reakci na:
* data telemetrie zařízení přicházejících z IoT Hub
* Změna vlastnosti nebo jiná data přicházející z jiného digitálního vlákna v rámci grafu s dvojitou přesností

Tento článek vás provede vytvořením funkce Azure pro použití s digitálními událostmi Azure. 

Tady je přehled kroků, které obsahuje:

1. Vytvoření aplikace Azure Functions v aplikaci Visual Studio
2. Zápis funkce Azure pomocí triggeru [Event Grid](../event-grid/overview.md)
3. Přidání ověřovacího kódu do funkce (aby bylo možné získat přístup k digitálním Vlákenám Azure)
4. Publikování aplikace Function App do Azure
5. Nastavte přístup [zabezpečení](concepts-security.md) . Aby funkce Azure Function získala přístupový token za běhu pro přístup ke službě, budete muset nakonfigurovat Identita spravované služby pro aplikaci Function App.

Zbývající část tohoto článku vás provede jednotlivými kroky nastavení Azure Functions, a to v jednom okamžiku.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Vytvoření aplikace Azure Functions v aplikaci Visual Studio

V aplikaci Visual Studio 2019 vyberte *soubor > nový projekt*. Vyhledejte šablonu *Azure Functions* , vyberte ji a stiskněte klávesu Next.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Zadejte název aplikace Function App a stiskněte tlačítko "vytvořit".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: dialogové okno Konfigurace projektu":::

Vyberte *aktivační událost Event Grid* a stiskněte tlačítko vytvořit.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: dialogové okno triggeru projektu Azure Functions":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Zápis funkce Azure pomocí triggeru Event Grid

Následující kód vytvoří krátkou funkci Azure, kterou můžete použít k protokolování událostí: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Toto je vaše základní funkce Azure Functions.

### <a name="run-and-debug-the-azure-function-app"></a>Spuštění a ladění aplikace funkce Azure Functions

Nyní můžete zkompilovat a spustit funkci. I když jsou služby Azure Functions nakonec určené ke spouštění v cloudu, můžete také spustit a ladit Azure Functions místně.

Další informace najdete v tématu [*ladění Event Grid triggeru místně*](../azure-functions/functions-debug-event-grid-trigger-local.md).

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Přidání sady Azure Digital revlákens SDK do vaší aplikace Azure Functions

Aplikace Function App spolupracuje s digitálními interakcemi Azure pomocí [klientské knihovny Azure IoT Digital vláken pro .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Aby bylo možné použít sadu SDK, musíte do projektu zahrnout následující balíčky:
* `Azure.DigitalTwins.Core` (verze `1.0.0-preview.2` )
* `Azure.Identity` (verze `1.1.1` )

Pro konfiguraci kanálu Azure SDK, který se má pro Azure Functions správně nastavit, budete potřebovat:
* `Azure.Net.Http`
* `Azure.Core`

V závislosti na zvolených nástrojích můžete to udělat pomocí Správce balíčků sady Visual Studio nebo `dotnet` nástroje příkazového řádku. 

Do funkce Azure Functions přidejte následující příkazy using.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Přidání ověřovacího kódu do funkce Azure Functions

Dále přidejte ověřovací kód, který umožní funkci přístup k digitálním Vlákenám Azure.

Přidejte proměnné do vaší třídy funkce pro tyto hodnoty: 
* ID aplikace pro digitální vlákna Azure
* Adresa URL instance digitálního vlákna Azure Je vhodné si přečíst adresu URL služby z proměnné prostředí, ale nemusíte ji pevně zakódovat do funkce.
* Statická proměnná pro uložení instance HttpClient HttpClient je poměrně nákladný k vytvoření a chceme se vyhnout nutnosti udělat si to pro každé vyvolání funkce.

Přidejte také místní proměnnou do vaší funkce, aby byla instance klienta Azure s digitálními podmnožinami uložena do projektu funkce. Nevytvářejte *tuto* statickou proměnnou uvnitř vaší třídy.

Nakonec změňte signaturu funkce na asynchronní.

Po těchto změnách by váš kód funkce měl vypadat nějak takto:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Aby vaše aplikace Functions mohla přistupovat k digitálním funkcím Azure, musí mít identitu spravovanou systémem a mít oprávnění pro přístup k instanci digitálních vláken Azure.

Pomocí následujícího příkazu vytvořte identitu spravovanou systémem. Poznamenejte si pole *principalId* ve výstupu.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Pomocí hodnoty *principalId* v následujícím příkazu přiřaďte identitě aplikace Function App roli *vlastníka digitálních vláken Azure (Preview)* pro instanci digitálního vlákna Azure. Tím se v instanci poskytne oprávnění aplikace Function App, aby se prováděly aktivity roviny dat.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Další informace o spravované identitě najdete v tématu [*Jak používat spravované identity pro App Service a Azure Functions*](../app-service/overview-managed-identity.md).

Nakonec můžete nastavit proměnnou prostředí tak, aby se adresa URL instance digitálního vlákna Azure pro vaši funkci přístupná. Další informace najdete v tématu [*proměnné prostředí*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables).

> [!TIP]
> Adresa URL instance digitálních vláken Azure se provede přidáním *https://* na začátek *názvu hostitele*instance digitálního vlákna Azure. Chcete-li zobrazit název hostitele spolu se všemi vlastnostmi vaší instance, můžete spustit `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Publikování aplikace funkce Azure Functions

Pokud chcete publikovat aplikaci Function App do Azure, klikněte pravým tlačítkem na projekt funkce (ne řešení) v Průzkumník řešení a zvolte *publikovat ()*.

Zobrazí se následující karta:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: dialogové okno Publikovat funkci, stránka 1":::

Vyberte nebo vytvořte plán App Service pro použití s Azure Functions. Pokud si nejste jistí, začněte s výchozím plánem spotřeby.

> [!IMPORTANT] 
> Publikování funkce Azure Functions bude mít za následek další poplatky na vaše předplatné, nezávisle na digitálních událostech Azure.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: dialogové okno Publikovat funkci, stránka 2":::

Na následující stránce zadejte požadovaný název nové aplikace Function App, skupiny prostředků a dalších podrobností.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Nastavení přístupu zabezpečení pro aplikaci Azure Function App

Seznámení se službou Azure Functions z předchozích příkladů vyžaduje předání nosného tokenu, aby bylo možné ho ověřit pomocí digitálních vláken Azure. Abyste se ujistili, že je tento nosný token předán, budete muset pro aplikaci Function App nastavit [Identita spravované služby (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . Tento postup je nutné provést pouze jednou pro každou aplikaci Function App.

Pokud ho chcete nastavit, přejděte na [Azure Portal](https://portal.azure.com/) a přejděte do aplikace Function App.

Na kartě *funkce platformy* vyberte *Identita*:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Azure Portal: Výběr identity pro funkci Azure Functions":::

Na stránce Identita nastavte přepínač *stav* na *zapnuto*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Azure Portal: zapnutí stavu identity":::

Všimněte si také **ID objektu** zobrazené na této stránce, jak bude použito v další části.

### <a name="assign-access-roles"></a>Přiřazení rolí přístupu

Vzhledem k tomu, že digitální vlákna Azure používá ke správě přístupu řízení přístupu na základě role (Další informace najdete v tématu [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md) ), musíte také přidat roli pro každou aplikaci Function App, kterou chcete zpřístupnit pro přístup k digitálním úlohám Azure.

K přiřazení role potřebujete **ID prostředku** instance digitálního vlákna Azure, kterou jste vytvořili. Pokud jste ho při vytváření instance nepoužili dříve, můžete ho načíst pomocí tohoto příkazu:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
ID prostředku bude součástí výstupu, jako dlouhý řetězec s názvem "ID", který začíná písmeny "/Subscriptions/...".

V níže uvedeném příkazu použijte ID prostředku spolu s ID objektu funkce Azure.

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste postupovali podle kroků pro nastavení funkce Azure pro použití s digitálními podmnožinami Azure. V dalším kroku se můžete přihlásit k odběru funkce Azure Functions, abyste Event Grid mohli naslouchat na koncovém bodu. Tento koncový bod může být:
* Event Grid koncový bod připojený k digitálním podprocesům Azure ke zpracování zpráv přicházejících z digitálních vláken Azure (například zpráv o změnách vlastností, zpráv telemetrie generovaných [digitálními](concepts-twins-graph.md) podprocesy ve dvojitých grafech nebo ve zprávách o životním cyklu).
* Témata systému IoT používaná IoT Hub k posílání telemetrie a dalších událostí zařízení
* Event Grid koncový bod přijímající zprávy z jiných služeb

Další informace najdete v tématu postup sestavení na základní funkci Azure pro ingestování IoT Hub dat do digitálních vláken Azure:
* [*Postupy: ingestování telemetrie z IoT Hub*](how-to-ingest-iot-hub-data.md)