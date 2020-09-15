---
title: Automatická správa zařízení pomocí DPS
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit automatizovaný proces pro zřizování a vyřazení zařízení IoT v digitálních událostech Azure pomocí služby Device Provisioning (DPS).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efc507cb69b3368a2102b6de0b905657d5806ef2
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561427"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Automatická správa zařízení v digitálních prostředcích Azure pomocí služby Device Provisioning (DPS)

V tomto článku se dozvíte, jak integrovat digitální vlákna Azure se [službou Device Provisioning (DPS)](../iot-dps/about-iot-dps.md).

Řešení popsané v tomto článku vám umožní automatizovat proces **_zřízení_** a **_vyřazení_** zařízení IoT Hub v digitálních prostředcích Azure pomocí služby Device Provisioning. 

Další informace o fázích _zřizování_ a _vyřazení_ a lepší pochopení sady obecných fází správy zařízení, které jsou společné pro všechny projekty IoT v podniku, najdete v [části *životní cyklus zařízení* ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) v dokumentaci správy zařízení IoT Hub.

## <a name="prerequisites"></a>Požadavky

Než budete moct nastavit zřizování, musíte mít **instanci digitálního vlákna Azure** , která obsahuje modely a vlákna. Tato instance by také měla být nastavená na možnost aktualizovat digitální informace na základě dat. 

Pokud toto nastavení ještě nemáte, můžete ho vytvořit pomocí kurzu digitálních vláken Azure [*: Připojte ucelené řešení*](tutorial-end-to-end.md). Tento kurz vás provede nastavením instance digitálního vlákna Azure s využitím modelů a vláken, připojené služby Azure [IoT Hub](../iot-hub/about-iot-hub.md)a několika [funkcí Azure](../azure-functions/functions-overview.md) pro šíření toku dat.

Následující hodnoty budete potřebovat později v tomto článku, od okamžiku, kdy jste nastavili instanci. Pokud potřebujete tyto hodnoty shromáždit znovu, použijte odkazy níže pro pokyny.
* **_Název hostitele_** instance digitálních vláken Azure ([najít v portálu](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_Připojovací řetězec_** připojovacího řetězce služby Azure Event Hubs ([najít v portálu](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

Tato ukázka také používá **simulátor zařízení** , který zahrnuje zřizování pomocí služby Device Provisioning. Simulátor zařízení je umístěný tady: [Ukázka digitálních vláken Azure a IoT Hub Integration Sample](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Získejte vzorový projekt na vašem počítači tak, že přejdete na vzorový odkaz a vyberete tlačítko *Stáhnout ZIP* pod nadpisem. Vyextrahování stažené složky.

Simulátor zařízení je založený na **Node.js**, verzi 10.0. x nebo novější. [*Příprava vývojového prostředí*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) popisuje, jak nainstalovat Node.js pro tento kurz v systému Windows nebo Linux.

## <a name="solution-architecture"></a>Architektura řešení

Následující obrázek znázorňuje architekturu tohoto řešení pomocí digitálních vláken Azure se službou Device Provisioning. Zobrazuje jak zřizování zařízení, tak i jejich vyřazení.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Zobrazení zařízení a několika služeb Azure v rámci kompletního scénáře. Data se převedou mezi zařízením termostata a DPS. Data také přecházejí z DPS do IoT Hub a do digitálních vláken Azure pomocí funkce Azure s označením "přidělení". Data z manuální akce odstranit zařízení přecházejí do IoT Hub > Event Hubs > Azure Functions digitálních vláken Azure.":::

Tento článek je rozdělen do dvou částí:
* [*Automatické zřizování zařízení pomocí služby Device Provisioning Service*](#auto-provision-device-using-device-provisioning-service)
* [*Automatické vyřazení zařízení pomocí IoT Hub událostí životního cyklu*](#auto-retire-device-using-iot-hub-lifecycle-events)

Podrobnější vysvětlení jednotlivých kroků v architektuře najdete v jejich jednotlivých částech dále v článku.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Automatické zřizování zařízení pomocí služby Device Provisioning Service

V této části budete připojovat službu Device Provisioning k digitálním Vlákenám Azure k automatickému zřizování zařízení přes níže uvedenou cestu. Toto je výňatek z plné architektury uvedené [výše](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Zřizovací tok – ukázka diagramu architektury řešení, s čísly, která se přidávají k označení částí toku. Data se převedou mezi zařízeními termostata a DPS (1 pro zařízení > DPS a 5 pro zařízení DPS >). Data také přecházejí z DPS do IoT Hub (4) a do digitálních vláken Azure (3) prostřednictvím funkce Azure s označením "přidělení" (2).":::

Tady je popis toku procesu:
1. Zařízení kontaktuje koncový bod DPS a předává identifikační informace, aby prokázal svoji identitu.
2. DPS ověří identitu zařízení tím, že ověří ID registrace a klíč proti seznamu registrací a zavolá [funkci Azure](../azure-functions/functions-overview.md) , která provede přidělení.
3. Funkce Azure ve službě Azure Digital [revláken pro zařízení vytvoří nový objekt](concepts-twins-graph.md) .
4. DPS registruje zařízení ve službě IoT Hub a naplní požadovaný doplněný stav zařízení.
5. IoT Hub vrátí zařízení informace o ID zařízení a informace o připojení ke službě IoT Hub. Zařízení se teď může připojit ke službě IoT Hub.

V následujících částech najdete postup nastavení tohoto toku zařízení pro Automatické zřizování.

### <a name="create-a-device-provisioning-service"></a>Vytvořit službu Device Provisioning Service

Když se ve službě Device Provisioning zřídí nové zařízení, v digitálních proobjektech Azure se dá vytvořit nové vlákna pro toto zařízení.

Vytvořte instanci služby Device Provisioning, která se použije ke zřízení zařízení IoT. Můžete buď použít níže uvedené pokyny pro Azure CLI, nebo použít Azure Portal: [*rychlý Start: nastavení IoT Hub Device Provisioning Service s Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Pomocí následujícího příkazu rozhraní příkazového řádku Azure se vytvoří služba Device Provisioning. Budete muset zadat název, skupinu prostředků a oblast. Příkaz se dá spustit v [Cloud Shell](https://shell.azure.com)nebo lokálně, pokud máte [na svém počítači nainstalované](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)rozhraní příkazového řádku Azure.

```azurecli
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Vytvořit funkci Azure

V dalším kroku vytvoříte funkci aktivovanou požadavkem HTTP uvnitř aplikace Function App. Můžete použít aplikaci Function App vytvořenou v rámci kompletního kurzu ([*kurz: připojení k*](tutorial-end-to-end.md)kompletnímu řešení) nebo vlastní.

Tuto funkci bude používat služba Device Provisioning ve [vlastních zásadách přidělení](../iot-dps/how-to-use-custom-allocation-policies.md) , které zřídí nové zařízení. Další informace o použití požadavků HTTP se službou Azure Functions najdete v tématu [*Trigger požadavku HTTP služby Azure pro Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md).

V projektu aplikace Function App přidejte novou funkci. Přidejte také nový balíček NuGet do projektu: `Microsoft.Azure.Devices.Provisioning.Service` .

V nově vytvořeném souboru s kódem funkce vložte následující kód.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);
            
            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);
            await client.CreateDigitalTwinAsync(dtId, System.Text.Json.JsonSerializer.Serialize<Dictionary<string, object>>(twinProps));
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

Uložte soubor a pak znovu publikujte aplikaci Function App. Pokyny k publikování aplikace Function App najdete v části [*publikování aplikace*](tutorial-end-to-end.md#publish-the-app) v tomto koncovém kurzu.

### <a name="configure-your-function"></a>Konfigurace funkce

V dalším kroku budete muset ve své aplikaci Function App nastavit proměnné prostředí, které obsahují odkaz na instanci digitálních vláken Azure, kterou jste vytvořili. Pokud jste použili kompletní kurz ([*kurz: připojení k*](tutorial-end-to-end.md)kompletnímu řešení), nastavení už se nakonfiguruje.

Přidejte nastavení pomocí tohoto příkazu Azure CLI:

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Zajistěte, aby přiřazení oprávnění a spravované role identity pro aplikaci Function App bylo správně nakonfigurované, jak je popsáno v části [*přiřazení oprávnění k aplikaci Function App*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) v tomto koncovém kurzu.

<!-- 
* Azure AD app registration **_Application (client) ID_** ([find in portal](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))

```azurecli
az functionapp config appsettings set --settings "AdtAppId=<Application (client)" ID> -g <resource group> -n <your App Service (function app) name> 
``` -->

### <a name="create-device-provisioning-enrollment"></a>Vytvořit registraci zařízení pro zřizování

V dalším kroku budete muset vytvořit registraci ve službě Device Provisioning pomocí **vlastní alokační funkce**. Postupujte podle pokynů v části [*Vytvoření registrace*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) a [*odvodit jedinečné klíče zařízení*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) v článku věnovaném službám Device Provisioning pro vlastní zásady přidělování.

Při procházení tohoto toku propojíte registraci k právě vytvořené funkci tak, že v kroku vyberete svou funkci a **vyberete způsob, jakým se mají zařízení přiřadit k rozbočovačům**. Po vytvoření registrace se název registrace a primární nebo sekundární klíč SAS použijí později ke konfiguraci simulátoru zařízení pro tento článek.

### <a name="set-up-the-device-simulator"></a>Nastavení simulátoru zařízení

Tato ukázka používá simulátor zařízení, který zahrnuje zřizování pomocí služby Device Provisioning. Simulátor zařízení je umístěný tady: [Ukázka digitálních vláken Azure a IoT Hub Integration Sample](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Pokud jste už ukázku nestáhli, Získejte ji hned tak, že přejdete na vzorový odkaz a v nadpisu vyberete tlačítko *Stáhnout ZIP* . Vyextrahování stažené složky.

Otevřete příkazové okno a přejděte do stažené složky a pak do adresáře *simulátoru zařízení* . Pomocí následujícího příkazu nainstalujte závislosti pro projekt:

```cmd
npm install
```

Potom zkopírujte soubor *. env. template* do nového souboru s názvem *. env*a zadejte tato nastavení:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Uložte soubor a zavřete ho.

### <a name="start-running-the-device-simulator"></a>Začněte používat simulátor zařízení.

V příkazovém okně v adresáři *simulátor zařízení* spusťte simulátor zařízení pomocí následujícího příkazu:

```cmd
node .\adt_custom_register.js
```

Mělo by se zobrazit, že zařízení je registrované a připojené k IoT Hub, a pak začít odesílat zprávy.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="okno Příkaz zobrazení registrace zařízení a odesílání zpráv":::

### <a name="validate"></a>Ověření

V důsledku toku, který jste nastavili v tomto článku, se zařízení automaticky zaregistruje v digitálních Vlákenách Azure. Pomocí následujícího příkazu [Azure Digital](how-to-use-cli.md) dostupní rozhraní PŘÍKAZového řádku můžete najít dvojitou adresu zařízení v instanci digitálních vláken Azure, kterou jste vytvořili.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Mělo by se zobrazit nevláken zařízení, které se nachází v instanci digitálních vláken Azure.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="okno Příkaz zobrazení nově vytvořeného vlákna":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Automatické vyřazení zařízení pomocí IoT Hub událostí životního cyklu

V této části budete připojovat IoT Hub události životního cyklu do digitálních vláken Azure k automatickému vyřazení zařízení pomocí níže uvedené cesty. Toto je výňatek z plné architektury uvedené [výše](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Vyřazení toku zařízení – výňatek diagramu architektury řešení, s čísly, která se přiřadí k oddílům toku. Zařízení termostatu se zobrazí bez připojení ke službám Azure v diagramu. Data z manuální akce odstranit zařízení nacházejí do IoT Hub (1) > Event Hubs (2) > Azure Functions > digitálních vláken Azure (3).":::

Tady je popis toku procesu:
1. Externí nebo ruční proces aktivuje odstranění zařízení v IoT Hub.
2. IoT Hub odstraní zařízení a vygeneruje událost [životního cyklu zařízení](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) , která bude směrována do [centra událostí](../event-hubs/event-hubs-about.md).
3. Funkce Azure odstraní v digitálních událostech Azure vlákna tohoto zařízení.

V následujících částech najdete postup nastavení tohoto toku automatického vyřazení zařízení.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Teď je potřeba vytvořit [centrum událostí](../event-hubs/event-hubs-about.md)Azure, které se použije pro příjem IoT Hubch událostí životního cyklu. 

Projděte si kroky popsané v rychlém startu [*vytvoření centra událostí*](../event-hubs/event-hubs-create.md) s použitím následujících informací:
* Pokud používáte kompletní kurz ([*kurz: připojení k*](tutorial-end-to-end.md)kompletnímu řešení), můžete znovu použít skupinu prostředků, kterou jste vytvořili pro kompletní kurz.
* Pojmenujte své centrum událostí *lifecycleevents*nebo něco jiného, co jste si zvolili, a zapamatujte si obor názvů, který jste vytvořili. Ty budete používat při nastavování funkce životního cyklu a IoT Hub trasy v dalších částech.

### <a name="create-an-azure-function"></a>Vytvořit funkci Azure

V dalším kroku vytvoříte funkci aktivovanou Event Hubs v rámci aplikace Function App. Můžete použít aplikaci Function App vytvořenou v rámci kompletního kurzu ([*kurz: připojení k*](tutorial-end-to-end.md)kompletnímu řešení) nebo vlastní. 

Pojmenujte aktivační událost centra událostí *lifecycleevents*a připojte Trigger centra událostí k centru událostí, které jste vytvořili v předchozím kroku. Pokud jste použili jiný název centra událostí, změňte jej tak, aby odpovídal následujícímu názvu aktivační události.

Tato funkce použije událost IoT Hub životní cyklus zařízení k vyřazení stávajícího zařízení. Další informace o událostech životního cyklu najdete v tématu [*IoT Hub událostí bez telemetrie*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Další informace o použití Event Hubs s Azure Functions najdete v tématu [*Trigger Azure Event Hubs pro Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

V rámci vaší publikované aplikace Function App přidejte novou třídu funkce *aktivační událost centra událostí*a vložte ji do následujícího kódu.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

Uložte projekt a pak znovu publikujte aplikaci Function App. Pokyny k publikování aplikace Function App najdete v části [*publikování aplikace*](tutorial-end-to-end.md#publish-the-app) v tomto koncovém kurzu.

### <a name="configure-your-function"></a>Konfigurace funkce

V dalším kroku budete muset ve své aplikaci Function App nastavit proměnné prostředí, které obsahují odkaz na instanci digitálních vláken Azure, kterou jste vytvořili, a v centru událostí. Pokud jste použili kompletní kurz ([*kurz: připojení k*](./tutorial-end-to-end.md)kompletnímu řešení), první nastavení se už nakonfiguruje.

Přidejte nastavení pomocí tohoto příkazu rozhraní příkazového řádku Azure CLI. Příkaz se dá spustit v [Cloud Shell](https://shell.azure.com)nebo lokálně, pokud máte [na svém počítači nainstalované](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)rozhraní příkazového řádku Azure.

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

V dalším kroku budete muset nakonfigurovat proměnnou prostředí funkcí pro připojení k nově vytvořenému centru událostí.

```azurecli
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Zajistěte, aby přiřazení oprávnění a spravované role identity pro aplikaci Function App bylo správně nakonfigurované, jak je popsáno v části [*přiřazení oprávnění k aplikaci Function App*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) v tomto koncovém kurzu.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Vytvoření trasy IoT Hub pro události životního cyklu

Nyní potřebujete nastavit trasu IoT Hub pro směrování událostí životního cyklu zařízení. V takovém případě budete specificky naslouchat událostem odstranění zařízení, které identifikoval `if (opType == "deleteDeviceIdentity")` . Tím se aktivuje odstranění položky digitálního vlákna, které dokončí vyřazení zařízení a jeho digitálního vlákna.

Pokyny k vytvoření IoT Hub trasy jsou popsány v tomto článku: [*použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů*](../iot-hub/iot-hub-devguide-messages-d2c.md). Oddíly, které *netelemetrie* využívají, popisují, že jako zdroj dat pro trasu můžete použít **události životního cyklu zařízení** .

Postup, který je třeba provést v této instalaci:
1. Vytvořte vlastní koncový bod centra událostí IoT Hub. Tento koncový bod by měl cílit na centrum událostí, které jste vytvořili v části [*vytvoření centra událostí*](#create-an-event-hub) .
2. Přidejte trasu *událostí životního cyklu zařízení* . Použijte koncový bod vytvořený v předchozím kroku. Události životního cyklu zařízení můžete omezit tak, aby se události odstranění odesílaly jenom tak, že přidáte dotaz směrování `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Přidat trasu":::

Jakmile projdete tento tok, vše je nastaveno na kompletní vyřazení zařízení do konce.

### <a name="validate"></a>Ověření

Chcete-li aktivovat proces vyřazení, je nutné ručně odstranit zařízení z IoT Hub.

V [první polovině tohoto článku](#auto-provision-device-using-device-provisioning-service)jste v IoT Hub vytvořili zařízení a odpovídající digitální vlákna. 

Nyní přejdete na IoT Hub a odstraňte toto zařízení (můžete to provést pomocí [příkazu Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) nebo v [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

Zařízení se automaticky odebere z digitálních vláken Azure. 

Pomocí následujícího příkazu [Azure Digital](how-to-use-cli.md) propouštějící rozhraní příkazového řádku ověříte, že se odstranila vlákna zařízení v instanci digitálních vláken Azure.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Měli byste vidět, že se vlákna zařízení v instanci digitálních vláken Azure už nenašly.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="okno Příkaz zobrazení vlákna Nenalezeno":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené v tomto článku nepotřebujete, odstraňte je pomocí těchto kroků.

Pomocí Azure Cloud Shell nebo místních rozhraní příkazového řádku Azure můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Tím odeberete skupinu prostředků. instance digitálního vlákna Azure; Centrum IoT a registrace zařízení v centru téma Event Grid a související odběry; obor názvů centra událostí a aplikace Azure Functions, včetně přidružených prostředků, jako je úložiště.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

```azurecli
az group delete --name <your-resource-group>
```
<!-- 
Next, delete the Azure AD app registration you created for your client app with this command:

```azurecli
az ad app delete --id <your-application-ID>
``` -->

Pak odstraňte ukázkovou složku projektu, kterou jste stáhli z místního počítače.

## <a name="next-steps"></a>Další kroky

Digitální vlákna vytvořená pro zařízení se ukládají jako Plochá hierarchie v rámci digitálních vláken Azure, ale dají se rozšířit pomocí informací o modelu a víceúrovňové hierarchie pro organizaci. Pokud se chcete dozvědět víc o tomto konceptu, přečtěte si:

* [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md)

Můžete napsat vlastní logiku, která automaticky poskytne tyto informace pomocí modelu a dat grafu, která jsou již uložena v rámci digitálních vláken Azure. Další informace o správě, upgradu a načítání informací z grafu vláken naleznete v následujících tématech:

* [*Postupy: Správa digitálního vlákna*](how-to-manage-twin.md)
* [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md)