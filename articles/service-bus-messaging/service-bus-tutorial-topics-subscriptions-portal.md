---
title: Aktualizace inventáře pomocí Azure Portal a témat/předplatných
description: V tomto kurzu se naučíte, jak odesílat a přijímat zprávy z tématu a odběru a jak přidávat a používat pravidla filtrů pomocí rozhraní .NET
author: spelluru
ms.author: spelluru
ms.date: 10/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: a7b03727c574dcee1cd56144a521f36de6dc48c6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861368"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Kurz: Aktualizace zásob pomocí portálu Azure Portal a témat/odběrů

Microsoft Azure Service Bus je víceklientská cloudová služba pro zasílání zpráv, která odesílá informace mezi aplikacemi a službami. Asynchronní operace umožňují flexibilní zprostředkované zasílání zpráv a také strukturované zasílání zpráv typu FIFO (first-in-first-out) a funkce pro publikování a přihlášení k odběru. Tento kurz ukazuje, jak můžete témata a odběry ve službě Service Bus využít ve scénáři aktualizace maloobchodních zásob. Pracuje se při tom s kanály publikování a přihlášení k odběru s využitím portálu Azure Portal a rozhraní .NET.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Pomocí portálu Azure Portal vytvořit téma služby Service Bus a k němu jeden nebo více odběrů
> * Přidávat filtry témat pomocí kódu .NET
> * Vytvořit dvě zprávy s různým obsahem
> * Odeslat zprávy a ověřit, že dorazily do očekávaných odběrů
> * Přijímat zprávy z odběrů

V tomto scénáři pracujeme s příkladem aktualizace sortimentu zásob pro několik prodejen. Každá prodejna nebo sada prodejen v našem příkladu přijímá zprávy s pokyny, jak konkrétně má aktualizovat sortiment. Tento kurz vysvětluje, jak tento scénář využívající odběry a filtry implementujete. Nejprve vytvoříte téma se třemi odběry, přidáte pravidla a filtry a potom z tématu a odběrů odešlete zprávy a přijmete je.

![téma](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][] před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:

- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější.
- [NET Core SDK](https://dotnet.microsoft.com/download) verze 2.0 nebo novější.

## <a name="service-bus-topics-and-subscriptions"></a>Témata a odběry služby Service Bus

Každý [odběr tématu](service-bus-messaging-overview.md#topics) může přijímat kopie všech zpráv. Témata jsou co do protokolu a sémantiky plně kompatibilní s frontami služby Service Bus. Témata služby Service Bus podporují širokou škálu pravidel pro výběr s podmínkami filtrů, včetně volitelných akcí, kterými se nastavují nebo upravují vlastnosti zprávy. Při každé shodě s pravidlem se vytvoří zpráva. Další informace o pravidlech, filtrech a akcích získáte pomocí tohoto [odkazu](topic-filters.md).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Vytvoření pravidel filtrů u odběrů

Pokud zřídíte obor názvů a téma s odběry a máte potřebná pověření, můžete u odběrů vytvořit pravidla filtrů a poté odesílat a přijímat zprávy. Kód si můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

Při spuštění kódu postupujte takto:

1. V příkazovém řádku nebo v příkazovém řádku PowerShellu naklonujte pomocí následujícího příkazu [úložiště Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Přejděte do složky s ukázkou `azure-service-bus\samples\DotNet\Azure.Messaging.ServiceBus\BasicSendReceiveTutorialwithFilters`.

3. Vyhledejte připojovací řetězec, který jste si zkopírovali do poznámkového bloku dříve v kurzu, v části Získání přihlašovacích údajů pro správu. Budete potřebovat také název tématu, které jste vytvořili v předchozí části.

4. Do příkazového řádku zadejte následující příkaz:

   ```shell
   dotnet build
   ```

5. Přejděte do složky `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp3.1`.

6. Zadáním následujícího příkazu spusťte program. `myConnectionString` nezapomeňte nahradit hodnotou, kterou jste předtím získali, a `myTopicName` názvem tématu, které jste vytvořili:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Podle pokynů v konzole nejprve vyberte vytvoření filtru. Součástí vytváření filtrů je odebrání výchozích filtrů. Pokud používáte prostředí PowerShell nebo rozhraní příkazového řádku, nemusíte výchozí filtr odebírat, pokud ale pracujete v kódu, je to nutné. Příkazy konzoly 1 a 3 vám pomůžou se správou filtrů u odběrů, které jste už vytvořili:

   - Příkaz 1 slouží k odebrání výchozích filtrů.
   - Příkazem 2 můžete přidat vlastní filtry.
   - Příkaz 3 umožňuje volitelně odebrat vlastní filtry. Upozorňujeme, že tímto krokem se znovu nevytvoří výchozí filtry.

     ![Na obrázku je vidět výstup příkazu 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Po vytvoření filtru můžete odesílat zprávy. Stiskněte klávesu 4 a sledujte, jak se do tématu odešle 10 zpráv:

    ![Výstup odeslání](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Stiskněte klávesu 5 a sledujte příjem zprávy. Pokud se vám nevrátilo 10 zpráv, zobrazte klávesou „m“ nabídku a pak stiskněte klávesu 5 znovu.

    ![Výstup příjmu](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte obor názvů a téma. Uděláte to tak, že prostředky vyberete na portálu a kliknete na **Odstranit**.

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Tato část obsahuje další podrobnosti o chování ukázkového kódu.

### <a name="get-connection-string-and-topic"></a>Získání připojovacího řetězce a tématu

Nejprve kód deklaruje sadu proměnných, které řídí další provádění programu.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Připojovací řetězec a název tématu se předají prostřednictvím parametrů příkazového řádku, jak je vidět v ukázce, a pak se načtou v metodě `Main()`:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Odebrání výchozích filtrů

Při vytváření odběru pro něj služba Service Bus vytvoří výchozí filtr. Ten umožňuje příjem všech zpráv odeslaných do tématu. Pokud chcete použít vlastní filtry, můžete výchozí filtr odebrat, jak je vidět v následujícím kódu:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
        foreach (var subscription in Subscriptions)
        {
            await client.DeleteRuleAsync(TopicName, subscription, CreateRuleOptions.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Vytváření filtrů

Následujícím kódem přidáte vlastní filtry definované v tomto kurzu:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Odebrání vašich vlastních filtrů

Pokud u odběru chcete odebrat všechny filtry, použijte následující kód:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            IAsyncEnumerator<RuleProperties> rules = client.GetRulesAsync(TopicName, subscription).GetAsyncEnumerator();
            while (await rules.MoveNextAsync())
            {
                await client.DeleteRuleAsync(TopicName, subscription, rules.Current.Name);
                Console.WriteLine($"Rule {rules.Current.Name} has been removed.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Odesílání zpráv

Odeslání zprávy do tématu je podobné jako odesílání zpráv do fronty. Tento příklad ukazuje, jak odesílat zprávy pomocí seznamu úloh a asynchronního zpracování:

```csharp
public async Task SendMessages()
{
    try
    {
        await using var client = new ServiceBusClient(ServiceBusConnectionString);
        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(client, Store[i]));
        }

        await Task.WhenAll(taskList);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(ServiceBusClient client, string store)
{
    // create the sender
    ServiceBusSender tc = client.CreateSender(TopicName);

    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        ServiceBusMessage message = item.AsMessage();
        message.To = store;
        message.ApplicationProperties.Add("StoreId", store);
        message.ApplicationProperties.Add("Price", item.GetPrice().ToString());
        message.ApplicationProperties.Add("Color", item.GetColor());
        message.ApplicationProperties.Add("Category", item.GetItemCategory());

        await tc.SendMessageAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Příjem zpráv

Zprávy se přijímají zase prostřednictvím seznamu úloh a kód využívá dávkové zpracování. Pomocí dávkování můžete zprávy odesílat i přijímat, tento příklad ale ukazuje jenom postup při dávkovém příjmu. Ve skutečnosti byste smyčku nepřerušovali, ale ponechali opakování ve smyčce a nastavili vyšší časový interval, třeba jednu minutu. Příjem volání u zprostředkovatele zůstává po tuto dobu otevřený a pokud dorazí zprávy, jsou okamžitě vráceny a je vydán nový příjem volání. Tato technika se označuje jako *dlouhé intervaly dotazování*. Častěji se používá zdroj přijímaných zpráv, jak můžete vidět v příručce [Rychlý start](service-bus-quickstart-portal.md) a v několika dalších ukázkách v úložišti.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    await using var client = new ServiceBusClient(ServiceBusConnectionString);
    ServiceBusReceiver receiver = client.CreateReceiver(TopicName, subscription);

    // In reality you would not break out of the loop like in this example but would keep looping. The receiver keeps the connection open
    // to the broker for the specified amount of seconds and the broker returns messages as soon as they arrive. The client then initiates
    // a new connection. So in reality you would not want to break out of the loop. 
    // Also note that the code shows how to batch receive, which you would do for performance reasons. For convenience you can also always
    // use the regular receive pump which we show in our Quick Start and in other github samples.
    while (true)
    {
        try
        {
            //IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            // Note the extension class which is serializing an deserializing messages and testing messages is null or 0.
            // If you think you did not receive all messages, just press M and receive again via the menu.
            IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(maxMessages: 100);

            if (messages.Any())
            {
                foreach (ServiceBusReceivedMessage message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IReadOnlyDictionary<string, object> myApplicationProperties = message.ApplicationProperties;
                        Console.WriteLine($"StoreId={myApplicationProperties["StoreId"]}");
                        if (message.Subject != null)
                        {
                            Console.WriteLine($"Subject={message.Subject}");
                        }
                        Console.WriteLine(
                            $"Item data: Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}");
                    }

                    await receiver.CompleteMessageAsync(message);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
}
```

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zřídili prostředky na portálu Azure Portal a poté odesílali a přijímali zprávy z tématu služby Service Bus a jeho odběrů. Naučili jste se:

> [!div class="checklist"]
> * Pomocí portálu Azure Portal vytvořit téma služby Service Bus a k němu jeden nebo více odběrů
> * Přidávat filtry témat pomocí kódu .NET
> * Vytvořit dvě zprávy s různým obsahem
> * Odeslat zprávy a ověřit, že dorazily do očekávaných odběrů
> * Přijímat zprávy z odběrů

Pokud vás zajímají další příklady odesílání a přijímání zpráv, pomůžou vám začít [ukázky služby Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Přejděte k dalšímu kurzu, kde se dozvíte více o možnostech publikování a přihlášení k odběru ve službě Service Bus.

> [!div class="nextstepaction"]
> [Reakce na události prostřednictvím Event Gridu](service-bus-to-event-grid-integration-example.md)

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
