---
title: Směrovat koncový bod vlastní webové služby Azure Media Services události | Microsoft Docs
description: Pomocí Azure událostí mřížky k odběru události změny stavu úlohy Media Services.
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
ms.openlocfilehash: 6a098f43819bb6581b2c5978fbcc4a378a8514c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638496"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Události služby Azure Media Services trasy pro koncový bod vlastní webové pomocí rozhraní příkazového řádku

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku použijte rozhraní příkazového řádku Azure k odběru události změny stavu úlohy Azure Media Services a aktivuje událost zobrazíte výsledek. 

Obvykle odesíláte události do koncového bodu, který na událost reaguje například webhookem nebo funkcí Azure Functions. Tento kurz ukazuje, jak vytvořit a nastavit webhook, jehož.

Po dokončení kroků popsaných v tomto článku uvidíte, že se data událostí odeslala do koncového bodu.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com) a spusťte **CloudShell**, abyste mohli provést příkazy CLI, jak můžete vidět v dalších krocích.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Ujistěte se, že mějte na paměti, hodnoty, které jste použili pro název účtu Media Services, úložiště a název prostředku.

## <a name="enable-event-grid-resource-provider"></a>Povolit zprostředkovatele prostředků událostí mřížky

První věc, kterou je potřeba udělat je, ujistěte se, že máte poskytovatele prostředků událostí mřížky na vaše předplatné povolená. 

V **Azure** portál postupujte takto:

1. Přejděte na předplatné.
2. Vyberte své předplatné.
3. V části Nastavení vyberte zprostředkovatelé prostředků.
4. Vyhledejte "EventGrid".
5. Zkontrolujte, zda že je zaregistrován událostí mřížky. Pokud ne, stiskněte **zaregistrovat** tlačítko.  

## <a name="create-a-generic-azure-function-webhook"></a>Vytvořte obecný webhook funkce Azure 

### <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Než se přihlásíte k odběru událostí mřížky článku, vytvořte koncový bod, který shromažďuje zprávy, aby se dala zobrazit.

Vytvoření funkce aktivovány obecný webhook, jak je popsáno v [obecný webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) článku. V tomto kurzu **C#** kód používá.

Po vytvoření webhooku zkopírujte adresu URL kliknutím na *získat adresu URL funkce* odkaz v horní části **Azure** okna portálu. Není nutné poslední část adresy URL (*& clientID = výchozí*).

![Vytvoření webhooku](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Ověření webhooku

Při registraci svůj vlastní koncový bod webhooku s událostí mřížky, odešle požadavek POST s kódem jednoduché ověření prokázat vlastnictví koncový bod. Vaše aplikace musí odpovídat tak, že odezva zpět ověřovacího kódu. Mřížky událostí není doručovat události Webhooku koncových bodů, které nebyly ověření proběhlo úspěšně. Další informace najdete v tématu [mřížky událostí zabezpečení a ověřování](https://docs.microsoft.com/azure/event-grid/security-authentication). Tento oddíl definuje dvě části, které musí být definovaná pro ověření předat.

#### <a name="update-the-source-code"></a>Aktualizujte zdrojový kód

Po vytvoření webhooku, **run.csx** souboru se zobrazí v prohlížeči. Ve výchozím kódu nahraďte následujícím kódem. 

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

#### <a name="update-test-request-body"></a>Aktualizace textu žádosti testu

Na pravé straně **Azure** okna portálu se zobrazí dvě karty: **zobrazit soubory** a **Test**. Vyberte kartu **Test**. V **text žádosti**, vložte následující kód json. Jej můžete vložit, protože je potřeba změnit všechny hodnoty.

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

Stiskněte klávesu **uložte a spusťte** v horní části okna.

![Tělo požadavku](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Registrace pro předplatné událostí mřížky 

Přihlášení k odběru článku říct události, které chcete sledovat událost mřížky. V následujícím příkladu se přihlásí k účtu Media Services, které jste vytvořili a předává adresu URL z webhooku funkce Azure, které jste vytvořili jako koncový bod pro oznámení o události. 

Nahraďte `<event_subscription_name>` s jedinečným názvem pro vaše předplatné událostí. Místo `<resource_group_name>` a `<ams_account_name>` použijte hodnoty názvu skupiny prostředků a názvu účtu úložiště, které jste vytvořili dříve.  Pro `<endpoint_URL>` vložte adresu URL vašeho koncového bodu. Odebrat *& clientID = výchozí* z adresy URL. Díky zadání koncového bodu při přihlašování k odběru bude služba Event Grid zpracovávat směrování událostí do tohoto koncového bodu. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Hodnota id prostředku účtu Media Services vypadá podobně jako tento:

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Test události

Spustíte úlohu kódování. Příklad, jak je popsáno v [Stream video soubory](stream-files-dotnet-quickstart.md) rychlý start.

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Přejděte do webhook, kterou jste vytvořili dříve. Klikněte na tlačítko **monitorování** a **aktualizovat**. Zobrazí události změny stavu úlohy: "Zařazených do fronty", "Plánovanou", "Zpracování", "Dokončeno", "Chyba", "Zrušit", "Zrušení".  Další informace najdete v tématu [Media Services událostí schémata](media-services-event-schemas.md).

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

[Reaktivní události](reacting-to-media-services-events.md)## viz také

## <a name="see-also"></a>Další informace najdete v tématech

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
