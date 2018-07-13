---
title: Směrování událostí služby Azure Media Services do vlastního webového koncového bodu | Dokumentace Microsoftu
description: Přihlásit k odběru události změny stavu úlohy Media Services pomocí služby Azure Event Grid.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e9df0cd24ef890765b78c25a073d671889be10a7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724061"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Směrování událostí služby Azure Media Services do vlastního webového koncového bodu pomocí rozhraní příkazového řádku

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku přihlášení k odběru událostí změny stavu úlohy Azure Media Services pomocí rozhraní příkazového řádku Azure a aktivujete událost, abyste viděli výsledek. 

Obvykle odesíláte události do koncového bodu, který na událost reaguje například webhookem nebo funkcí Azure Functions. Tento kurz ukazuje, jak vytvořit a nastavit webhooku.

Po dokončení kroků popsaných v tomto článku uvidíte, že se data událostí odeslala do koncového bodu.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com) a spusťte **CloudShell**, abyste mohli provést příkazy CLI, jak můžete vidět v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Je nutné pamatovat si hodnoty, které jste použili pro název účtu Media Services, úložiště, název a název prostředku.

## <a name="enable-event-grid-resource-provider"></a>Povolit poskytovatele prostředků služby Event Grid

První věc, kterou je třeba provést je, ujistěte se, že máte povoleno na vaše předplatné poskytovatele prostředků služby Event Grid. 

V **Azure** portálu postupujte takto:

1. Přejdete na předplatná.
2. Vyberte své předplatné.
3. V části Nastavení vyberte poskytovatele prostředků.
4. Vyhledejte "EventGrid".
5. Ujistěte se, že služby Event Grid je zaregistrovaný. Pokud ne, stiskněte **zaregistrovat** tlačítko.  

## <a name="create-a-generic-azure-function-webhook"></a>Vytvořit obecný webhook funkce Azure functions 

### <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Než se přihlásíte k odběru článku služby Event Grid, vytvořte koncový bod, který shromažďovat zprávy, abyste je mohli zobrazit.

Vytvoření funkce aktivované obecným webhookem, jak je popsáno v [obecný webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) článku. V tomto kurzu **jazyka C#** kód se používá.

Po vytvoření webhooku, zkopírujte adresu URL po kliknutí na *získat adresu URL funkce* odkazu v horní části **Azure** okna portálu. Není nutné poslední část adresy URL (*& clientID = default*).

![Vytvořit webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Ověření se webhook.

Když si zaregistrujete vlastní koncový bod webhooku s využitím služby Event Grid, odešle požadavek POST s kódem jednoduché ověření pro koncový bod vlastnictví prokázat způsobem. Vaše aplikace potřebuje tak přečtou zpět ověřovacího kódu. Event Grid nebude doručení událostí Webhooku koncových bodů, které nebyly úspěšně. Další informace najdete v tématu [ověřování a zabezpečení služby Event Grid](https://docs.microsoft.com/azure/event-grid/security-authentication). Tento oddíl definuje dvě části, které pro ověření předat musí být definovaný.

#### <a name="update-the-source-code"></a>Aktualizace zdrojového kódu

Po vytvoření webhooku, **run.csx** souboru se zobrazí v prohlížeči. Nahraďte kód následujícím kódem. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Aktualizace textu požadavku testu

Na pravé straně **Azure** okna portálu se zobrazí dvě karty: **zobrazit soubory** a **Test**. Vyberte kartu **Test**. V **text žádosti**, vložte následující kód json. Můžete ho vložit je, není potřeba měnit všechny hodnoty.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Stisknutím klávesy **uložte a spusťte** v horní části okna.

![Tělo požadavku](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Zaregistrujte se na odběr služby Event Grid 

Se přihlásíte k odběru článku zjistit služby Event Grid, které události chcete sledovat. Následující příklad se přihlásí k účtu Media Services, které jste vytvořili a předá adresu URL z funkce Azure Functions webhook, který jste vytvořili jako koncový bod pro oznámení události. 

Nahraďte `<event_subscription_name>` s jedinečným názvem vašeho odběru událostí. Místo `<resource_group_name>` a `<ams_account_name>` použijte hodnoty názvu skupiny prostředků a názvu účtu úložiště, které jste vytvořili dříve.  Pro `<endpoint_URL>` vložte adresu URL koncového bodu. Odebrat *& clientID = default* z adresy URL. Díky zadání koncového bodu při přihlašování k odběru bude služba Event Grid zpracovávat směrování událostí do tohoto koncového bodu. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Hodnota id prostředku účtu Media Services bude vypadat nějak takto:

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Test události

Spustíte úlohu kódování. Příklad, jak je popsáno v [Stream videosoubory](stream-files-dotnet-quickstart.md) rychlý start.

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Přejděte k webhooku, který jste vytvořili dříve. Klikněte na tlačítko **monitorování** a **aktualizovat**. Zobrazí stav úlohy změní události: "Ve frontě", "Plánovanou", "Zpracování", "Dokončení", "Chyba", "zrušeno", "Zrušení".  Další informace najdete v tématu [schémata událostí služby Media Services](media-services-event-schemas.md).

Příklad:

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Test události](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s tímto účtem úložiště a odběru událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

Nahraďte `<resource_group_name>` názvem skupiny prostředků, kterou jste vytvořili výše.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Další postup

[Reakce na události](reacting-to-media-services-events.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
