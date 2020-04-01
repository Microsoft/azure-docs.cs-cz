---
title: Zotavení po havárii pro vlastní témata v Azure Event Grid
description: Tento kurz vás provede, jak nastavit architekturu událostí k obnovení, pokud se služba Event Grid stane v oblasti nefunkční.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 87f8f79e2cf125fa5735653153d8fcaa781f5200
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76511514"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Vytvořte si vlastní zotavení po havárii pro vlastní témata v Event Grid
Zotavení po havárii se zaměřuje na obnovení z vážné ztráty funkčnosti aplikace. Tento kurz vás provede, jak nastavit architekturu událostí k obnovení, pokud se služba Event Grid stane v určité oblasti nefunkční.

V tomto kurzu se dozvíte, jak vytvořit architekturu převzetí služeb při selhání s aktivní pasivní pro vlastní témata v event gridu. Převzetí služeb při selhání dosáhnete zrcadlením témat a předplatných ve dvou oblastech a následným správou převzetí služeb při selhání, když se téma stane nefunkčním. Architektura v tomto kurzu selže přes všechny nové přenosy. je důležité si uvědomit, že s tímto nastavením nebudou události, které jsou již za letu, obnoveny, dokud nebude ohrožená oblast opět v pořádku.

> [!NOTE]
> Event Grid nyní podporuje automatické geografické zotavení po havárii (GeoDR) na straně serveru. Stále můžete implementovat logiku zotavení po havárii na straně klienta, pokud chcete větší kontrolu nad procesem převzetí služeb při selhání. Podrobnosti o automatické geodr, na jdete na [straně serveru geo zotavení po havárii v Azure Event Grid](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Chcete-li otestovat konfiguraci převzetí služeb při selhání, budete potřebovat koncový bod pro příjem událostí na. Koncový bod není součástí infrastruktury převzetí služeb při selhání, ale bude fungovat jako naše obslužná rutina události, aby bylo snazší testovat.

Chcete-li zjednodušit testování, nasaďte [předem vytvořenou webovou aplikaci,](https://github.com/Azure-Samples/azure-event-grid-viewer) která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`
Nezapomeňte si tuto adresu URL poznamenat, protože ji budete později potřebovat.

1. Zobrazí se web, na který se však zatím neodeslaly žádné události.

   ![Zobrazení nového webu](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Vytvoření primárních a sekundárních témat

Nejprve vytvořte dvě témata mřížky událostí. Tato témata budou sloužit jako primární a sekundární. Ve výchozím nastavení budou události protékat primárním tématem. Pokud dojde k výpadku služby v primární oblasti, sekundární převezme.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

1. V levém horním rohu hlavní nabídky Azure zvolte **Všechny služby** > vyhledejte **Event Grid** > vyberte **Témata mřížky událostí**.

   ![Nabídka Témata mřížky událostí](./media/custom-disaster-recovery/select-topics-menu.png)

    Vyberte hvězdičku vedle položky Témata mřížky událostí a přidejte ji do nabídky zdrojů pro snadnější přístup v budoucnu.

1. V nabídce Témata mřížky událostí vyberte **možnost +PŘIDAT** a vytvořte primární téma.

   * Pojmenujte téma logickým názvem a přidejte jako příponu "-primární", abyste bylo snadné sledovat.
   * Oblast tohoto tématu bude primární oblastí.

     ![Primární dialog o vytváření tématu mřížky událostí](./media/custom-disaster-recovery/create-primary-topic.png)

1. Po vytvoření tématu přejděte na něj a zkopírujte **koncový bod tématu**. uri budete později potřebovat.

    ![Primární téma mřížky událostí](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Získejte přístupový klíč k tématu, který budete potřebovat i později. Klikněte na **přístupové klávesy** v nabídce zdrojů a zkopírujte klíč 1.

    ![Získat primární klíč tématu](./media/custom-disaster-recovery/get-primary-access-key.png)

1. V okně Téma klikněte na **+Event Subscription** a vytvořte předplatné spojující vaše přihlašovací informace k odběru webu příjemce události, který jste vytvořili v předpokladech kurzu.

   * Pojmenujte odběr událostí logickým názvem a přidejte jako příponu "-primary", abyste usnadnili sledování.
   * Vyberte webový hák typu koncového bodu.
   * Nastavte koncový bod na adresu URL události příjemce události, která by měla vypadat nějak takto:`https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Předplatné primární události mřížky událostí](./media/custom-disaster-recovery/create-primary-es.png)

1. Opakujte stejný tok a vytvořte sekundární téma a předplatné. Tentokrát nahraďte příponu "-primární" "sekundární" pro snadnější sledování. Nakonec se ujistěte, že jste ji umístili do jiné oblasti Azure. I když ji můžete umístit kamkolichcete, doporučujeme použít [spárované oblasti Azure](../best-practices-availability-paired-regions.md). Uvedení sekundární téma a předplatné v jiné oblasti zajišťuje, že vaše nové události poteče i v případě, že primární oblasti přejde dolů.

Nyní byste měli mít:

   * Web příjemce události pro testování.
   * Primární téma v primární oblasti.
   * Odběr primární události spojující primární téma s webem příjemce událostí.
   * Sekundární téma v sekundární oblasti.
   * Odběr sekundární události spojující primární téma s webem příjemce události.

## <a name="implement-client-side-failover"></a>Implementace převzetí služeb při selhání na straně klienta

Teď, když máte regionálně redundantní dvojici témat a předplatných nastavení, jste připraveni implementovat převzetí služeb při selhání na straně klienta. Existuje několik způsobů, jak toho dosáhnout, ale všechny implementace převzetí služeb při selhání budou mít společný rys: pokud jedno téma již není v pořádku, provoz bude přesměrovat na jiné téma.

### <a name="basic-client-side-implementation"></a>Základní implementace na straně klienta

Následující ukázkový kód je jednoduchý vydavatel rozhraní .NET, který se vždy pokusí publikovat na primární téma jako první. Pokud se mu to nepodaří, převezme převzetí služeb při selhání sekundární téma. V obou případech také zkontroluje rozhraní API stavu jiného `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`tématu tím, že dělá GET na . Zdravé téma by měl vždy reagovat s **200 OK** při GET je provedena na **/api/health** koncového bodu.

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

### <a name="try-it-out"></a>Vyzkoušejte si to.

Nyní, když máte všechny komponenty na místě, můžete otestovat implementaci převzetí služeb při selhání. Spusťte výše uvedenou ukázku v kódu sady Visual Studio nebo ve svém oblíbeném prostředí. Nahraďte následující čtyři hodnoty koncovými body a klávesami z témat:

   * primaryTopic - koncový bod pro primární téma.
   * secondaryTopic - koncový bod pro sekundární téma.
   * primaryTopicKey - klíč pro vaše primární téma.
   * secondaryTopicKey - klíč pro sekundární téma.

Zkuste spustit vydavatele události. Měli byste vidět vaše testovací události přistát v prohlížeči Event Grid jako níže.

![Předplatné primární události mřížky událostí](./media/custom-disaster-recovery/event-grid-viewer.png)

Chcete-li se ujistit, že převzetí služeb při selhání funguje, můžete změnit několik znaků v primárním klíči tématu, aby již nebyl platný. Zkuste vydavatele spustit znovu. V prohlížeči programu Event Grid byste se stále měli zobrazovat nové události, ale když se podíváte na konzoli, uvidíte, že jsou nyní publikovány prostřednictvím sekundárního tématu.

### <a name="possible-extensions"></a>Možná rozšíření

Existuje mnoho způsobů, jak rozšířit tuto ukázku na základě vašich potřeb. Pro scénáře s velkým objemem můžete pravidelně kontrolovat rozhraní API stavu tématu nezávisle. Tímto způsobem, pokud by se téma stalo, nemusíte ho kontrolovat s každým publikováním. Jakmile víte, že téma není v pořádku, můžete ve výchozím nastavení publikovat na sekundární téma.

Podobně můžete chtít implementovat logiku navrácení služeb po selhání na základě vašich konkrétních potřeb. Pokud publikování do nejbližšího datového centra je důležité pro snížení latence, můžete pravidelně sondovat rozhraní API stavu tématu, které převzala převzetí. Jakmile je to znovu zdravé, budete vědět, že je bezpečné navrácení služeb po selhání do bližšího datového centra.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [přijímat události v koncovém bodě http](./receive-events.md)
- Zjistěte, jak [směrovat události do hybridních připojení](./custom-event-to-hybrid-connection.md)
- Informace o [zotavení po havárii pomocí Azure DNS a Traffic Manageru](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)
