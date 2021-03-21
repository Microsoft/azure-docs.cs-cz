---
title: Zotavení po havárii pro vlastní témata v Azure Event Grid
description: Tento kurz vás provede postupem nastavení architektury událostí pro obnovení, pokud služba Event Grid v oblasti není v pořádku.
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e37cb6a0679ee2e249de4ed8fa31c40d5082ea4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020139"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Vytvoření vlastního zotavení po havárii pro vlastní témata v Event Grid
Zotavení po havárii se zaměřuje na obnovení z vážné ztráty funkčnosti aplikace. V tomto kurzu se dozvíte, jak nastavit architekturu událostí pro obnovení, pokud služba Event Grid v konkrétní oblasti nebude v pořádku.

V tomto kurzu se dozvíte, jak vytvořit architekturu pro převzetí služeb při selhání aktivní – pasivní pro vlastní témata v Event Grid. Převzetí služeb při selhání můžete provést zrcadlením vašich témat a odběrů ve dvou oblastech a potom můžete spravovat převzetí služeb při selhání, když se téma stane špatným. Architektura v tomto kurzu převezme všechny nové přenosy. je důležité, abyste s tímto nastavením věděli, že události, které už jsou v letadle, se nebudou obnovovat, dokud se ohrožená oblast opět neobjeví v pořádku.

> [!NOTE]
> Event Grid podporuje nyní na straně serveru automatické obnovení geografického zotavení po havárii (GeoDR). Pokud chcete mít větší kontrolu nad procesem převzetí služeb při selhání, můžete i nadále implementovat logiku zotavení po havárii na straně klienta. Podrobnosti o automatických GeoDR najdete [v tématu geografická zotavení po havárii na straně serveru v Azure Event Grid](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

K otestování konfigurace převzetí služeb při selhání budete potřebovat koncový bod pro příjem vašich událostí. Koncový bod není součástí vaší infrastruktury převzetí služeb při selhání, ale bude fungovat jako naše obslužná rutina události, aby bylo snazší ho testovat.

Chcete-li zjednodušit testování, nasaďte [předem vytvořenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazí zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`
Nezapomeňte tuto adresu URL poznamenat, protože ji budete potřebovat později.

1. Zobrazí se web, na který se však zatím neodeslaly žádné události.

   ![Zobrazení nového webu](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Vytvoření primárních a sekundárních témat

Nejprve vytvořte dvě Event Grid témata. Tato témata budou fungovat jako primární a sekundární. Ve výchozím nastavení budou vaše události procházet vaším primárním tématem. Pokud v primární oblasti dojde k výpadku služby, vaše sekundární převezme.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

1. V levém horním rohu hlavní nabídky Azure zvolte **všechny služby** > vyhledejte **Event Grid** > vyberte **Event Grid témata**.

   ![Nabídka Event Gridch témat](./media/custom-disaster-recovery/select-topics-menu.png)

    Vyberte hvězdičku vedle Event Grid témata a přidejte je do nabídky prostředků pro snadnější přístup v budoucnu.

1. V nabídce Event Grid témata vyberte **+ Přidat** a vytvořte tak primární téma.

   * Dejte tématu logický název a přidejte "-Primary" jako příponu, aby bylo snazší ho sledovat.
   * Oblast tohoto tématu bude vaší primární oblastí.

     ![Dialog Event Grid primárním vytvořením tématu](./media/custom-disaster-recovery/create-primary-topic.png)

1. Po vytvoření tématu přejděte do něj a zkopírujte **koncový bod tématu**. identifikátor URI budete potřebovat později.

    ![Event Grid – primární téma](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Získání přístupového klíče k tématu, které budete potřebovat později. V nabídce prostředků klikněte na **přístupové klíče** a zkopírujte klíč 1.

    ![Získat klíč primárního tématu](./media/custom-disaster-recovery/get-primary-access-key.png)

1. V okně tématu klikněte na **+ předplatné události** a vytvořte předplatné připojující web přijímače událostí, který jste provedli v rámci požadavků, do tohoto kurzu.

   * Zadejte pro odběr události logický název a přidejte "-Primary" jako příponu, aby bylo snazší ho sledovat.
   * Vyberte typ koncového bodu Webhook.
   * Nastavte koncový bod na adresu URL události vašeho přijímače událostí, který by měl vypadat nějak takto: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Snímek obrazovky zobrazující stránku "vytvořit odběr události – Basic" se zvýrazněnými hodnotami "název", "typ koncového bodu" a "koncový bod".](./media/custom-disaster-recovery/create-primary-es.png)

1. Opakováním stejného toku Vytvořte sekundární téma a předplatné. Tentokrát pro snazší sledování Nahraďte příponu "-Primary" pomocí "-Secondary". Nakonec se ujistěte, že jste ji umístili do jiné oblasti Azure. I když ho můžete umístit kdekoli, doporučujeme používat [spárované oblasti Azure](../best-practices-availability-paired-regions.md). Vložením sekundárního tématu a předplatného do jiné oblasti zajistíte, aby se vaše nové události vytekly i v případě, že dojde k výpadku primární oblasti.

Nyní byste měli mít následující:

   * Web přijímače událostí pro testování.
   * Primární téma v primární oblasti.
   * Primární odběr událostí, který připojuje primární téma k webu přijímače událostí.
   * Sekundární téma v sekundární oblasti.
   * Sekundární předplatné události připojující vaše primární téma k webu přijímače událostí.

## <a name="implement-client-side-failover"></a>Implementace převzetí služeb při selhání na straně klienta

Teď, když máte místně redundantní dvojici témat a odběrů, jste připraveni implementovat převzetí služeb při selhání na straně klienta. Existuje několik způsobů, jak to provést, ale všechny implementace převzetí služeb při selhání budou mít společnou funkci: Pokud už jedno téma není v pořádku, provoz se přesměruje na jiné téma.

### <a name="basic-client-side-implementation"></a>Základní implementace na straně klienta

Následující vzorový kód je jednoduchý Vydavatel .NET, který se vždycky pokusí publikovat do primárního tématu jako první. Pokud to nepomůže, bude převzetí služeb při selhání sekundárním tématem. V obou případech také kontroluje rozhraní API pro zjištění stavu druhého tématu `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health` . V případě, že je na koncovém bodu **/API/Health** proveden pokus o načtení, by mělo být v pořádku vždy reagovat na **200 OK** .

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Vyzkoušet

Teď, když máte všechny vaše komponenty, můžete otestovat implementaci převzetí služeb při selhání. Spusťte výše uvedený příklad v nástroji Visual Studio Code nebo ve vašem oblíbeném prostředí. Nahraďte následující čtyři hodnoty koncovými body a klíči z těchto témat:

   * primaryTopic – koncový bod pro primární téma.
   * secondaryTopic – koncový bod pro sekundární téma.
   * primaryTopicKey – klíč pro primární téma.
   * secondaryTopicKey – klíč pro sekundární téma.

Zkuste spustit vydavatele události. V prohlížeči Event Grid by se měly zobrazit vaše události testu jako v následujícím příkladu.

![Event Grid odběr primárních událostí](./media/custom-disaster-recovery/event-grid-viewer.png)

Pokud chcete zajistit, aby převzetí služeb při selhání fungovalo, můžete změnit několik znaků v primárním klíči tématu, aby už není platný. Zkuste spustit vydavatele znovu. V prohlížeči Event Grid se pořád zobrazují nové události, ale když se podíváte na konzolu, uvidíte, že se teď publikují prostřednictvím sekundárního tématu.

### <a name="possible-extensions"></a>Možná rozšíření

Existuje mnoho způsobů, jak tuto ukázku rozšiřuje podle vašich potřeb. U scénářů s vysokým objemem můžete chtít pravidelně kontrolovat rozhraní API stavu tématu nezávisle. Tímto způsobem by se při každém publikování nemusela Tato část kontrolovat. Když víte, že téma není v pořádku, můžete ho ve výchozím nastavení publikovat do sekundárního tématu.

Podobně můžete chtít implementovat logiku navrácení služeb po obnovení na základě konkrétních potřeb. Pokud je publikování do nejbližšího datového centra velmi důležité, abyste snížili latenci, můžete pravidelně testovat rozhraní API stavu tématu, u kterého došlo k převzetí služeb při selhání. Jakmile je opět v pořádku, budete mít jistotu, že je bezpečné navrácení služeb po obnovení do bližšího datového centra.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak [přijímat události na koncovém bodu http](./receive-events.md)
- Zjistěte, jak [směrovat události do Hybrid Connections](./custom-event-to-hybrid-connection.md)
- Přečtěte si o [zotavení po havárii pomocí Azure DNS a Traffic Manager](../networking/disaster-recovery-dns-traffic-manager.md)
