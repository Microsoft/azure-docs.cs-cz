---
title: Kurz – Aktualizace sortimentu zásob v maloobchodě s využitím kanálů pro publikování/odběr a filtrů témat na portálu Azure | Microsoft Docs
description: V tomto kurzu se naučíte, jak odesílat a přijímat zprávy z tématu a odběru a jak přidávat a používat pravidla filtrů pomocí rozhraní .NET
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 5d209d0290d8ca813101b531f18fc0f08a6edf8a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246884"
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
- [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

## <a name="service-bus-topics-and-subscriptions"></a>Témata a odběry služby Service Bus

Každý [odběr tématu](service-bus-messaging-overview.md#topics) může přijímat kopie všech zpráv. Témata jsou co do protokolu a sémantiky plně kompatibilní s frontami služby Service Bus. Témata služby Service Bus podporují širokou škálu pravidel pro výběr s podmínkami filtrů, včetně volitelných akcí, kterými se nastavují nebo upravují vlastnosti zprávy. Při každé shodě s pravidlem se vytvoří zpráva. Další informace o pravidlech, filtrech a akcích získáte pomocí tohoto [odkazu](topic-filters.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Nejprve přejděte na web [Azure Portal][Azure portal] a přihlaste se pomocí svého předplatného Azure. Prvním krokem je vytvoření oboru názvů služby Service Bus typu **zasílání zpráv**.

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus

Obor názvů Service Bus pro zasílání zpráv poskytuje jedinečný kontejner oboru (odkazuje se na něj [plně kvalifikovaným názvem domény][]), ve kterém můžete vytvořit jednu nebo více front, témat a odběrů. V následujícím příkladu vytvoříme obor názvů Service Bus pro zasílání zpráv v nové nebo existující [skupině prostředků](/azure/azure-resource-manager/resource-group-portal):

1. V levém navigačním podokně portálu klikněte na **+ Vytvořit prostředek**, potom klikněte na **Podniková integrace** a pak na **Service Bus**.
2. V dialogovém okně **Vytvořit obor názvů** zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.
3. Po kontrole, že je název oborů názvů k dispozici, zvolte cenovou úroveň (Standard nebo Premium).
4. V poli **Předplatné** zvolte předplatné Azure, ve které chcete vytvořit obor názvů.
5. V poli **Skupina prostředků** zvolte existující skupinu prostředků, ve které bude obor názvů fungovat, nebo vytvořte novou.      
6. V poli **Umístění**, vyberte zemi nebo oblast, ve které by měl být oboru názvů hostován.
7. Klikněte na možnost **Vytvořit**. Systém teď vytvoří obor názvů a povolí ho. Pravděpodobně budete muset několik minut počkat, než systém zřídí prostředky pro váš účet.

  ![Obor názvů](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>Získání přihlašovacích údajů pro správu

Vytvořením nového oboru názvů se automaticky vygeneruje počáteční pravidlo sdíleného přístupového podpisu (SAS) s přidruženým párem primárního a sekundárního klíče, které udělují úplnou kontrolu nad všemi aspekty tohoto oboru názvů. Pokud chcete zkopírovat počáteční pravidlo, postupujte následovně:

1. Klikněte na **Všechny prostředky** a pak klikněte na název nově vytvořeného oboru názvů.
2. V okně oboru názvů klikněte na **Zásady sdíleného přístupu**.
3. Na obrazovce **Zásady sdíleného přístupu** klikněte na **RootManageSharedAccessKey**.
4. V okně **Zásada: RootManageSharedAccessKey** klikněte na tlačítko **Kopírovat** vedle položky **Primární připojovací řetězec** a zkopírujte si připojovací řetězec do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.

    ![connection-string][connection-string]
5. Opakujte předchozí krok, zkopírujte si hodnotu pro **primární klíč** a vložte ji do dočasného umístění pro pozdější použití.

## <a name="create-a-topic-and-subscriptions"></a>Vytvoření tématu a odběrů

Pokud chcete vytvořit téma Service Bus, zadejte obor názvů, do kterého má téma spadat. Následující příklad ukazuje, jak vytvořit téma na portálu:

1. V levém navigačním podokně portálu klikněte na **Service Bus** (pokud položku **Service Bus** nevidíte, klikněte na **Všechny služby**).
2. Klikněte na obor názvů, ve kterém chcete téma vytvořit.
3. V okně oboru názvů klikněte na **Témata** a pak v okně **Témata** klikněte na **+ Témata**.
4. Zadejte **Název** tématu a ostatní hodnoty ponechte ve výchozím nastavení.
5. V dolní části okna klikněte na **Vytvořit**.
6. Název tématu si poznamenejte.
7. Vyberte téma, které jste právě vytvořili.
8. Klikněte na **+ Odběr**, zadejte název odběru **O1**a všechny ostatní hodnoty ponechte ve výchozím nastavení.
9. Předchozí krok ještě dvakrát zopakujte a vytvořte odběry s názvem **O2** a **O3**.

## <a name="create-filter-rules-on-subscriptions"></a>Vytvoření pravidel filtrů u odběrů

Pokud zřídíte obor názvů a téma s odběry a máte potřebná pověření, můžete u odběrů vytvořit pravidla filtrů a poté odesílat a přijímat zprávy. Kód můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/GettingStarted\BasicSendReceiveTutorialwithFilters).

### <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

Kód spustíte následovně:

1. V příkazovém řádku nebo v příkazovém řádku PowerShellu naklonujte pomocí následujícího příkazu [úložiště Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Přejděte do složky s ukázkou `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`.

3. Vyhledejte připojovací řetězec, který jste si zkopírovali do poznámkového bloku dříve v kurzu, v části [Získání přihlašovacích údajů pro správu](#obtain-the-management-credentials). Budete potřebovat také název tématu, které jste vytvořili v předchozí části.

4. Do příkazového řádku zadejte následující příkaz:

   ```shell
   dotnet build
   ```

5. Přejděte do složky `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0`.

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

Pokud už obor názvů a frontu nepotřebujete, odstraňte je. Uděláte to tak, že prostředky vyberete na portálu a kliknete na **Odstranit**.

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
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
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
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
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
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
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
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
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
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
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
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
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

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zřídili prostředky na portálu Azure Portal a poté odesílali a přijímali zprávy z tématu služby Service Bus a jeho odběrů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Pomocí portálu Azure Portal vytvořit téma služby Service Bus a k němu jeden nebo více odběrů
> * Přidávat filtry témat pomocí kódu .NET
> * Vytvořit dvě zprávy s různým obsahem
> * Odeslat zprávy a ověřit, že dorazily do očekávaných odběrů
> * Přijímat zprávy z odběrů

Pokud vás zajímají další příklady odesílání a přijímání zpráv, pomůžou vám začít [ukázky služby Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Přejděte k dalšímu kurzu, kde se dozvíte více o možnostech publikování a přihlášení k odběru ve službě Service Bus.

> [!div class="nextstepaction"]
> [Aktualizace zásob pomocí prostředí PowerShell a témat/odběrů](service-bus-tutorial-topics-subscriptions-powershell.md)

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[plně kvalifikovaným názvem domény]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png