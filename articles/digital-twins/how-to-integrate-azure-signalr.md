---
title: Integrace se službou Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Podívejte se, jak streamovat telemetrie digitálních vláken Azure na klienty pomocí služby Azure Signal
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0931a7e344d6546bd62ceb7513c4aa540f5b9638
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447823"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrace digitálních vláken Azure s využitím služby Azure Signal

V tomto článku se dozvíte, jak integrovat digitální vlákna Azure do [služby Azure Signal](../azure-signalr/signalr-overview.md).

Řešení popsané v tomto článku vám umožní doručovat data digitální nespojité do připojených klientů, jako je jediná webová stránka nebo mobilní aplikace. V důsledku toho se klienti aktualizují pomocí metrik a stavu v reálném čase ze zařízení IoT, a to bez nutnosti dotazování serveru nebo odeslání nových požadavků HTTP na aktualizace.

## <a name="prerequisites"></a>Požadavky

Tady jsou požadavky, které byste měli provést, než budete pokračovat:

* Před integrací vašeho řešení se službou Azure Signal Service v tomto článku byste měli dokončit kurz k digitálním [_**událostem Azure: Připojte ucelené řešení**_](tutorial-end-to-end.md), protože tento postup se sestavuje nad ním. Tento kurz vás provede nastavením instance digitálního vlákna Azure, která funguje s virtuálním zařízením IoT, aby aktivovala aktualizace digitálních vláken. Tento postup bude tyto aktualizace propojit s ukázkovou webovou aplikací pomocí služby Azure Signal.
    - Budete potřebovat název **tématu Event gridu** , které jste vytvořili v tomto kurzu.
* Máte na svém počítači nainstalovanou [**Node.js**](https://nodejs.org/) .

Můžete také pokračovat a přihlásit se k [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

## <a name="solution-architecture"></a>Architektura řešení

Pomocí níže uvedené cesty budete připojovat službu Azure Signal k digitálním Vlákenám Azure. Oddíly A, B a C v diagramu jsou pořízeny z diagramu architektury [kompletního kurzu](tutorial-end-to-end.md). v tomto postupu to vytvoříte přidáním oddílu D.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře. Znázorňuje tok dat ze zařízení do IoT Hub, prostřednictvím funkce Azure (šipka B) k instanci digitálních vláken Azure (oddíl A), pak prostřednictvím Event Grid na jinou funkci Azure pro zpracování (šipka C). V části D se zobrazuje tok dat ze stejného Event Grid na šipku C až po funkci Azure s označením "Broadcast". všesměrové vysílání komunikuje s jinou funkcí Azure s názvem Negotiate a komunikují všesměrově a Negotiate spolu s počítači." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Stažení ukázkových aplikací

Nejdřív stáhněte požadované ukázkové aplikace. Budete potřebovat obě z těchto možností:
* [**Ukázky digitálních vláken Azure**](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/): Tato ukázka obsahuje *AdtSampleApp* , ve kterém jsou podrženy dvě služby Azure Functions pro přesun dat kolem instance digitálních vláken Azure (Další informace o tomto scénáři najdete podrobněji v [*kurzu: připojení uceleného řešení*](tutorial-end-to-end.md)). Obsahuje také ukázkovou aplikaci *DeviceSimulator* , která simuluje zařízení IoT a generuje novou hodnotu teploty každou sekundu. 
    - Přejděte na vzorový odkaz a stisknutím tlačítka *Stáhnout ZIP* Stáhněte kopii ukázky do vašeho počítače, jak _**Azure_Digital_Twins_samples.zip**_. Rozbalte složku.
* [**Ukázka webové aplikace pro integraci signálů**](https://docs.microsoft.com/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): Jedná se o ukázkovou webovou aplikaci, která bude využívat data telemetrie Azure s digitálními podsítěmi ze služby signalizace Azure.
    -  Přejděte na vzorový odkaz a stisknutím tlačítka *Stáhnout ZIP* Stáhněte kopii ukázky do vašeho počítače, jak _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_. Rozbalte složku.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Nechte okno prohlížeče otevřené Azure Portal, jak ho budete znovu používat v další části.

## <a name="configure-and-run-the-azure-functions-app"></a>Konfigurace a spuštění aplikace Azure Functions

V této části vytvoříte dvě služby Azure Functions:
* **vyjednávat** – funkce TRIGGERu http Pomocí vstupní vazby *SignalRConnectionInfo* vygeneruje a vrátí platné informace o připojení.
* **Broadcast** – funkce triggeru [Event Grid](../event-grid/overview.md) . Přijímá data telemetrie Azure Digital procházejí prostřednictvím služby Event Grid a používá výstupní vazbu instance *signalizace* , kterou jste vytvořili v předchozím kroku, pro vysílání zprávy všem připojeným klientským aplikacím.

Nejprve přejdete do prohlížeče, kde je Azure Portal otevřeno, a provedením následujících kroků Získejte **připojovací řetězec** pro instanci signálu, kterou jste nastavili. Budete ho potřebovat ke konfiguraci těchto funkcí.
1. Potvrďte, že instance služby Signaled, kterou jste nasadili dřív, se úspěšně vytvořila. Můžete to udělat tak, že v horní části portálu vyhledáte jeho název ve vyhledávacím poli. Instanci vyberte a otevřete.

1. V nabídce instance vyberte **klíče** a zobrazte připojovací řetězce pro instanci služby signalizace.

1. Vyberte ikonu pro zkopírování primárního připojovacího řetězce.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Snímek obrazovky Azure Portal, který zobrazuje stránku klíčů pro instanci signalizace. Zvýrazní se ikona kopírovat do schránky vedle primárního PŘIPOJOVACÍho řetězce." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

Dále spusťte sadu Visual Studio (nebo jiný Editor kódu dle vašeho výběru) a otevřete řešení Code ve složce *Azure_Digital_Twins_samples > ADTSampleApp* . Potom postupujte podle následujících kroků a vytvořte funkce:

1. Vytvořte novou ostrost C# třídy s názvem **SignalRFunctions.cs** v projektu *SampleFunctionsApp* .

1. Obsah souboru třídy nahraďte následujícím kódem:

    ```C#
    using System;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System.Collections.Generic;
    
    namespace SampleFunctionsApp
    {
        public static class SignalRFunctions
        {
            public static double temperature;
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo GetSignalRInfo(
                [HttpTrigger(AuthorizationLevel.Anonymous, "post")] HttpRequest req,
                [SignalRConnectionInfo(HubName = "dttelemetry")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static Task SendMessage(
                [EventGridTrigger] EventGridEvent eventGridEvent,
                [SignalR(HubName = "dttelemetry")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {
                JObject eventGridData = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
    
                log.LogInformation($"Event grid message: {eventGridData}");
    
                var patch = (JObject)eventGridData["data"]["patch"][0];
                if (patch["path"].ToString().Contains("/Temperature"))
                {
                    temperature = Math.Round(patch["value"].ToObject<double>(), 2);
                }
    
                var message = new Dictionary<object, object>
                {
                    { "temperatureInFahrenheit", temperature},
                };
        
                return signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { message }
                    });
            }
        }
    }
    ```

1. V okně *konzoly Správce balíčků* sady Visual Studio nebo jakémkoli okně příkazového řádku na počítači ve složce *Azure_Digital_Twins_samples \adtsampleapp\samplefunctionsapp* spusťte následující příkaz, který nainstaluje `SignalRService` balíček NuGet do projektu:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    To by mělo vyřešit všechny problémy závislosti ve třídě.

V dalším kroku publikujte funkci do Azure pomocí postupu popsaného v části [ *publikování aplikace* v tématu](tutorial-end-to-end.md#publish-the-app) *připojení k řešení* v rámci začátku. Můžete ji publikovat do stejné aplikace App Service/Function App, kterou jste použili v rámci kompletního kurzu požadavků ohlásila, nebo vytvořit novou. můžete ji ale použít k minimalizaci duplicity. Také dokončete publikování aplikace pomocí následujících kroků:
1. Shromážděte **adresu URL koncového bodu http**funkce *Negotiate* . Provedete to tak, že přejdete na stránku [funkcí aplikace](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) Azure Portal a ze seznamu vyberete svoji aplikaci Function App. V nabídce aplikace vyberte *funkce* a zvolte funkci *Negotiate* .

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal zobrazení aplikace Function App se zvýrazněnou funkcí v nabídce. Seznam funkcí se zobrazí na stránce a zvýrazní se i funkce Negotiate.":::

    Stiskněte *získat adresu URL funkce* a zkopírujte hodnotu **nahoru přes _/API_ (nezahrnujte poslední _/Negotiate?_)**. Použijete ji později.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure Portal zobrazení funkce Negotiate. Tlačítko získat adresu URL funkce je zvýrazněné a část adresy URL od začátku do '/API '.":::

1. Nakonec pomocí následujícího příkazu rozhraní příkazového řádku Azure přidejte do nastavení aplikace funkce **připojovací řetězec** služby Azure Signal. Příkaz můžete spustit v [Azure Cloud Shell](https://shell.azure.com)nebo lokálně, pokud máte [v počítači nainstalované](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)rozhraní příkazového řádku Azure:
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Výstup tohoto příkazu vytiskne všechna nastavení aplikace nastavená pro funkci Azure Functions. Vyhledejte `AzureSignalRConnectionString` v dolní části seznamu a ověřte, že byla přidána.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Ukázka výstupu v příkazovém okně zobrazující položku seznamu s názvem ' AzureSignalRConnectionString '":::

#### <a name="connect-the-function-to-event-grid"></a>Připojení funkce k Event Grid

Dále přihlaste *se do* tématu služby **Event Grid** , které jste vytvořili během kurzu, přihlášení k odběru funkce Azure Functions [*: Připojte ucelené řešení*](tutorial-end-to-end.md) požadavků ohlásila. To umožní tok dat telemetrie z *thermostat67* vlákna prostřednictvím tématu Event Grid do funkce, která je může vysílat všem klientům.

Uděláte to tak, že vytvoříte **předplatné Event Grid** z tématu Event gridu do *vysílání* Azure Function jako koncový bod.

V [Azure Portal](https://portal.azure.com/)přejděte na téma Event gridu tak, že na horním panelu vyhledávání vyhledáte jeho název. Vyberte *+ Odběr události*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: odběr událostí Event Grid":::

Na stránce *vytvořit odběr události* vyplňte pole následujícím způsobem (pole vyplněná ve výchozím nastavení nejsou zmíněná):
* *Podrobnosti*  >  odběru události **Název**: zadejte název předplatného události.
* Podrobnosti koncového *bodu*  >  **Typ koncového bodu**: z možností nabídky vyberte *Azure Function* .
* Podrobnosti koncového *bodu*  >  **Koncový bod**: stiskněte odkaz *Vybrat koncový bod* . Otevře se okno *Vybrat Azure Function* :
    - Vyplňte svoje **předplatné**, **skupinu prostředků**, **aplikaci funkcí** a **funkci** (*všesměrové vysílání*). Některé z těchto možností mohou automaticky být vyplněny po výběru předplatného.
    - **Potvrďte výběr**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal zobrazení vytvoření odběru událostí. Výše uvedená pole jsou vyplněna a zvýrazní se tlačítka potvrdit výběr a vytvořit.":::

Zpátky na stránce *vytvořit odběr události* klikněte na **vytvořit**.

## <a name="configure-and-run-the-web-app"></a>Konfigurace a spuštění webové aplikace

V této části se zobrazí výsledek akce. Nejprve spustíte **ukázkovou aplikaci simulovaného zařízení** , která odesílá data telemetrie prostřednictvím instance digitálního vlákna Azure. Potom nakonfigurujete **ukázkovou klientskou webovou aplikaci** tak, aby se připojila ke službě Azure Signal flow, kterou jste nastavili. Potom byste měli být schopni zobrazit data, která v reálném čase aktualizují ukázkovou webovou aplikaci.

### <a name="run-the-device-simulator"></a>Spuštění simulátoru zařízení

V rámci kompletního předplatného kurzu jste [nakonfigurovali simulátor zařízení](tutorial-end-to-end.md#configure-and-run-the-simulation) , aby odesílal data prostřednictvím IoT Hub a instance digitálních vláken Azure.

Nyní stačí spustit projekt simulátoru, který je umístěn v *Azure_Digital_Twins_samples > DeviceSimulator > DeviceSimulator. sln*. Pokud používáte Visual Studio, můžete projekt otevřít a pak ho spustit pomocí tohoto tlačítka na panelu nástrojů:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Tlačítko Start pro Visual Studio (projekt DeviceSimulator)":::

Otevře se okno konzoly, ve kterém se zobrazí simulované zprávy telemetrie o teplotě. Odesílají se z instance digitálního vlákna Azure, kde je pak vybrala služba Azure functions a Signal.

V této konzole nemusíte nic dalšího dělat, ale při provádění dalších kroků ho nechte spuštěný.

### <a name="configure-the-sample-client-web-app"></a>Konfigurace ukázkové klientské webové aplikace

V dalším kroku nastavte **ukázku webové aplikace pro integraci signálu** pomocí těchto kroků:
1. Pomocí sady Visual Studio nebo jakéhokoli editoru kódu, který jste si vybrali, otevřete složku _**getAzure_Digital_Twins_SignalR_integration_web_app_sample**_ , kterou jste si stáhli v části [*požadavky*](#prerequisites) .

1. Otevřete soubor *Src/App.js* a nahraďte adresu URL v `HubConnectionBuilder` adrese URL koncového bodu http funkce **Negotiate** , kterou jste předtím uložili:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. V aplikaci Visual Studio *Developer Command Prompt* nebo jakémkoli okně příkazového řádku na svém počítači přejděte do složky *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* . Spuštěním následujícího příkazu nainstalujte balíčky závislého uzlu:

    ```cmd
    npm install
    ```

V dalším kroku nastavte oprávnění ve vaší aplikaci Function App na Azure Portal:
1. Na stránce [funkce aplikace](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) Azure Portal vyberte instanci aplikace Function App.
1. V nabídce instance se posuňte dolů a vyberte *CORS*. Na stránce CORS přidejte `http://localhost:3000` jako povolený počátek zadáním do prázdného pole. Zaškrtněte políčko *Povolit přístup-řízení – povolit-přihlašovací údaje* a klikněte na *Uložit*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Nastavení CORS ve službě Azure Functions":::

### <a name="see-the-results"></a>Zobrazení výsledků

Pokud chcete zobrazit výsledky v akci, spusťte **ukázku webové aplikace pro integraci signálu**. To můžete provést z jakéhokoli okna konzoly na Azure_Digital_Twins_SignalR_integration_web_app_sample umístění *\src* spuštěním tohoto příkazu:

```cmd
npm start
```

Tím se otevře okno prohlížeče, ve kterém je spuštěná ukázková aplikace, která zobrazuje měřič vizuální teploty. Po spuštění aplikace byste měli začít zobrazovat hodnoty telemetrie teplot z simulátoru zařízení, která se šíří přes digitální vlákna Azure, která se v reálném čase odráží v reálném čase.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Výňatek z ukázkové klientské webové aplikace zobrazující vizuální Měřič teploty. Reflektovaná teplota je 67,52.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené v tomto článku nepotřebujete, odstraňte je pomocí těchto kroků. 

Pomocí Azure Cloud Shell nebo místních rozhraní příkazového řádku Azure můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Odebrání skupiny prostředků se také odebere...
* instance digitálních vláken Azure (z kompletního kurzu)
* služby IoT Hub a registrace zařízení centra (z kompletního kurzu)
* téma Event gridu a související odběry
* aplikace Azure Functions, včetně všech tří funkcí a přidružených prostředků, jako je Storage
* instance služby signalizace Azure

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Pokud odstraňujete instanci digitálních vláken Azure, můžete také odstranit registraci aplikace služby Azure AD, kterou jste pro ni vytvořili, pomocí tohoto příkazu:

```azurecli
az ad app delete --id <your-application-ID>
```

Nakonec odstraňte ukázkové složky projektu, které jste stáhli do místního počítače (*Azure_Digital_Twins_samples.zip* a *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*).

## <a name="next-steps"></a>Další kroky

V tomto článku jste nastavili službu Azure Functions s nástrojem Signal, aby vysílaly události telemetrie digitálních vláken Azure do ukázkové klientské aplikace.

Další informace o službě Azure Signaler:
* [*Co je služba signalizace Azure?*](../azure-signalr/signalr-overview.md)

Nebo si přečtěte další informace o ověřování pomocí služby signalizace Azure pomocí Azure Functions:
* [*Ověřování služby signalizace Azure*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
