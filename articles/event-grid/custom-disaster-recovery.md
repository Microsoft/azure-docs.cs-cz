---
title: Vytvářejte vlastní zotavení po havárii pro vlastní témata ve službě Azure Event Grid | Dokumentace Microsoftu
description: Překonání regionální výpadky zachovat připojené služby Azure Event Grid.
services: event-grid
author: banisadr
ms.service: even-grid
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: babanisa
ms.openlocfilehash: d1cf09df2db3abe65d7c3c048438e6791129940a
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701653"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Vytvářejte vlastní zotavení po havárii pro vlastní témata ve službě Event Grid

Zotavení po havárii se zaměřuje na obnovení závažné ztráty funkčnost aplikace. Tento kurz vás provede nastavení architektury zpracování událostí k obnovení v případě služby Event Grid nebude v pořádku v konkrétní oblasti.

V tomto kurzu se dozvíte, jak vytvořit architekturu aktivní pasivní převzetí služeb při selhání pro vlastní témata ve službě Event Grid. Budete provádět převzetí služeb při selhání pomocí zrcadlení témata a odběry ve dvou oblastech a následné správy převzetí služeb při selhání, když téma nebude v pořádku. Architektura v tomto kurzu převezme služby při selhání všechny nové přenosy. je důležité vědět, s tímto nastavením, události už je v letu nebude obnoven, dokud nebude ohrožených oblast opět v pořádku.

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Testování konfigurace převzetí služeb při selhání, bude nutné koncový bod pro příjem událostí v. Koncový bod, které nejsou součástí vaší infrastruktury převzetí služeb při selhání, ale bude fungovat jako naše obslužná rutina události, aby bylo snazší testování.

Pro zjednodušení testování, nasazení [předem vytvořené webové aplikace](https://github.com/Azure-Samples/azure-event-grid-viewer) , která zobrazuje zprávy o událostech. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`
Ujistěte se, že si tuto adresu URL, protože ji budete potřebovat později.

1. Zobrazí se web, na který se však zatím neodeslaly žádné události.

   ![Zobrazení nového webu](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Vytvoření primárního a sekundárního témata

Nejprve vytvořte dvě témata služby Event Grid. Tato témata bude fungovat jako primární a sekundární. Ve výchozím nastavení budou události směrovat přes primární tématu. Pokud dojde k výpadku služby v primární oblasti, bude mít vaše sekundární.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. V levém horním rohu v hlavní nabídce Azure zvolte **všechny služby** > vyhledejte **služby Event Grid** > vyberte **témata Event gridu**.

   ![Nabídka událostí témata mřížky](./media/custom-disaster-recovery/select-topics-menu.png)

    Vyberte hvězdičku vedle témata Event gridu v budoucnu přidat k nabídce prostředků pro snadnější přístup.

1. V nabídce témata Event Grid vyberte **+ přidat** vytvořit primární tématu.

    * Zadejte logický název tématu a přidejte "– primární" jako příponu k tomu, aby ke sledování.
    * Toto téma oblast bude primární oblasti.

    ![Dialog vytvořit primární téma Event gridu](./media/custom-disaster-recovery/create-primary-topic.png)

1. Po vytvoření tématu, přejděte na ni a zkopírujte **koncový bod tématu**. identifikátor URI budete potřebovat později.

    ![Primární téma Event gridu](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Získáte přístupový klíč pro téma, které budete také potřebovat později. Klikněte na **přístupové klíče** v nabídce prostředků a zkopírujte klíč 1.

    ![Získat téma primární klíč](./media/custom-disaster-recovery/get-primary-access-key.png)

1. V okně tématu, klikněte na tlačítko **+ odběr události** odběr připojování vašeho odběru webu příjemce událostí jste provedli v požadavcích najdete v tomto kurzu vytvoříte.

    * Logický název odběru událostí a přidejte "– primární" jako příponu k tomu, aby ke sledování.
    * Vyberte koncový bod typu Webhooku.
    * Nastavení koncového bodu adresy URL události příjemce událostí, které by měl vypadat přibližně jako: `https://<your-event-reciever>.azurewebsites.net/api/updates`

    ![Primární událost odběru Event gridu](./media/custom-disaster-recovery/create-primary-es.png)

1. Opakujte stejný tok vytvořit sekundární téma a odběr. Nahraďte tento čas "-primární" přípony s "-sekundární" pro jednodušší sledování. Nakonec se ujistěte, že jste ji vložili do jiné oblasti Azure. Přestože můžete ji umístit kamkoli chcete, je doporučeno používat [spárované oblasti Azure](../best-practices-availability-paired-regions.md). Vložení sekundární téma a odběr v jiné oblasti zajistí, že budou směrovat nové události, i v případě, že primární oblast přestane fungovat.

Teď byste měli mít:

   * Na webu příjemce událostí pro testování.
   * Primární téma ve vaší primární oblasti.
   * Předplatné primární události připojení primární tématu na webu příjemce událostí.
   * Sekundární téma v sekundární oblasti.
   * Předplatné sekundární události připojení primární tématu na webu příjemce událostí.

## <a name="implement-client-side-failover"></a>Provádění převzetí služeb při selhání na straně klienta

Teď, když máte regionálně redundantní dvojici nastavení témat a odběrů, budete připraveni provádět převzetí služeb při selhání na straně klienta. Existuje několik způsobů, jak toho dosáhnout, ale všechny implementace převzetí služeb při selhání bude mít to běžná funkce: Pokud jedno téma již není v pořádku, provoz se přesměruje na téma.

### <a name="basic-client-side-implementation"></a>Základní implementaci na straně klienta

Následující ukázkový kód je jednoduchý publisher .net, která se vždy pokusí nejprve publikovat do primární tématu. Pokud neproběhne úspěšně, dojde poté převzetí služeb při selhání sekundární tématu. V obou případech se také kontroluje stav rozhraní api tématu tímto způsobem GET na `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`. V pořádku tématu by měla vždy odpovídat s **200 OK** při provedení GET na **/api/stavu** koncového bodu.

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

Teď, když máte všechny vaše součásti na místě, můžete otestovat na implementaci převzetí služeb při selhání. Spusťte ukázku výše uvedené v aplikaci Visual Studio code nebo vašem oblíbeném prostředí. Nahraďte následující čtyři hodnoty koncových bodů a klíče z témat:

   * primaryTopic – koncový bod pro primární tématu.
   * secondaryTopic – koncový bod pro sekundární tématu.
   * primaryTopicKey - key pro vaše primární téma.
   * secondaryTopicKey – klíč pro sekundární tématu.

Zkuste spustit zdroj události. V prohlížeči služby Event Grid jako níže, měli byste vidět události pozemního vašeho testu.

![Primární událost odběru Event gridu](./media/custom-disaster-recovery/event-grid-viewer.png)

Pokud chcete mít jistotu, že vaše převzetí služeb při selhání funguje, můžete změnit několik znaků v klíč primárního tématu k němu už nebude platná. Zkuste znovu spustit vydavatele. Stále byste měli vidět nové události měly zobrazovat v prohlížeči vaší služby Event Grid, ale když se podíváte na konzolu, uvidíte, že jsou nyní publikují se prostřednictvím sekundární tématu.

### <a name="possible-extensions"></a>Je to možné rozšíření

Existuje mnoho způsobů, jak rozšířit tuto ukázku na základě vašich potřeb. U scénářů s vysokými objemy, můžete chtít pravidelně kontrolovat stav na téma api nezávisle na sobě. Tímto způsobem téma, přestanou fungovat, pokud není nutné obraťte se na každém publikování jednoho. Jakmile budete vědět, že se že na téma není v pořádku, můžete výchozí s publikováním na sekundární téma.

Podobně můžete implementovat logiku navrácení služeb po obnovení na základě vašich konkrétních potřeb. Pokud publikování na nejbližší datové centrum je důležité si můžete snížit latenci, můžete pravidelně testovat stav rozhraní api, která se má převzetí služeb při selhání. Jakmile je opět v pořádku, budete vědět, že je bezpečný pro navrácení služeb po obnovení do blíže datového centra.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [příjem událostí na koncový bod http](./receive-events.md)
- Objevte jak [směrování událostí do hybridních připojení](./custom-event-to-hybrid-connection.md)
- Další informace o [zotavení po havárii pomocí Azure DNS a Traffic Manageru](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)