---
title: Optimalizace Azure kódu v sadě Visual Studio | Microsoft Docs
description: Další informace o tom, jak Azure kódu optimalizace nástroje v sadě Visual Studio pomáhat kódu robustnější a lépe provádění.
services: visual-studio-online
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ed48ee06-e2d2-4322-af22-07200fb16987
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: a606c8e7d8b730b67bd8481656e099900eb39fbc
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2017
ms.locfileid: "23890835"
---
# <a name="optimizing-your-azure-code"></a>Optimalizace Azure kódu
Pokud jste programování aplikací, které používají Microsoft Azure, nejsou některé osvědčených kódovacích postupů, které byste měli postupovat, abyste se vyhnuli problémy s aplikací, škálovatelnost, chování a výkonu v cloudovém prostředí. Společnost Microsoft poskytuje nástroj pro analýzu kódu Azure, rozpozná a identifikuje některé z těchto problémů obvykle došlo a můžete je vyřešit. Můžete stáhnout nástroj v sadě Visual Studio prostřednictvím balíčku NuGet.

## <a name="azure-code-analysis-rules"></a>Azure pravidel analýzy kódu
Nástroj pro analýzu kódu Azure automaticky příznak Azure kódu, pokud najde známé problémy výkonových používá následující pravidla. Zjistil problémy se zobrazují jako upozornění nebo chyby kompilátoru. Prostřednictvím ikonou žárovky často zadat kód opravy nebo návrhy k vyřešení upozornění nebo chyby.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Nepoužívejte výchozí režim stavu relace (v procesu)
### <a name="id"></a>ID
AP0000

### <a name="description"></a>Popis
Pokud používáte výchozí režim stavu relace (v rámci procesu) pro cloudové aplikace, může dojít ke ztrátě stavu relace.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Ve výchozím nastavení je zadaný v souboru web.config režim stavu relace v procesu. Navíc pokud žádný záznam, zadaný v konfiguračním souboru, režim stavu relace výchozí v procesu. Režim v procesu ukládá stav relace v paměti na webovém serveru. Při restartování instance nebo novou instanci se používá pro vyrovnávání zatížení nebo podporu převzetí služeb při selhání, není uložen stav relace, které jsou uložené v paměti na webovém serveru. Tato situace brání aplikaci v právě škálovatelné v cloudu.

Stavu relace ASP.NET podporuje několik různých možností ukládání dat stavu relace: InProc, StateServer, SQL Server, vlastní a vypnutí. Doporučujeme používat vlastní režim k hostování dat. na externí úložiště stavu relace, například [poskytovatele stavu relace Azure Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Řešení
Jeden doporučené řešení je k ukládání stavu relace v mezipaměti spravované služby. Další informace o použití [poskytovatele stavu relace Azure Redis](http://go.microsoft.com/fwlink/?LinkId=401521) k uložení stavu vaší relace. Také můžete uložit stav relace v jiná místa a ujistěte se, že aplikace je škálovatelná v cloudu. Další informace o alternativní řešení přečtěte si prosím [režim stavu relace](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Metoda spouštění by neměl být asynchronní
### <a name="id"></a>ID
AP1000

### <a name="description"></a>Popis
Vytváření asynchronních metod (například [await](https://msdn.microsoft.com/library/hh156528.aspx)) mimo [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda a pak zavolají asynchronní metody z [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Deklarování [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) jako asynchronní metodu způsobí, že role pracovního procesu zadat smyčku restartování.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Volání asynchronních metod uvnitř [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda způsobí, že běh služby cloud recyklace role pracovního procesu. Když se spustí roli pracovního procesu, všechny spuštění programu probíhá uvnitř [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda. Ukončení [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda způsobí, že role pracovního procesu restartování. Pokud se modul runtime role pracovního procesu dotkne asynchronní metody, odešle zprávu všechny operace po asynchronní metody a vrátí. To způsobí, že ukončíte z role pracovního procesu [ [ [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda a restartování. V další iterace provádění role pracovního procesu dotkne asynchronní metody znovu a restartuje, způsobuje recyklace znovu i role pracovního procesu.

### <a name="solution"></a>Řešení
Umístit všechny asynchronní operace mimo [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda. Potom zavolejte rozdělili asynchronní metody z uvnitř [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metody, jako je .wait RunAsync (). Nástroj pro analýzu kódu Azure můžete tento problém vyřešit.

Následující fragment kódu ukazuje kód opravu tohoto problému:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Ověřování služby sběrnice sdíleného přístupového podpisu
### <a name="id"></a>ID
AP2000

### <a name="description"></a>Popis
Pomocí sdíleného přístupového podpisu (SAS) pro ověřování. Služby Řízení přístupu (ACS) je zastaralá pro ověřování sběrnice služby.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Pro zvýšení zabezpečení Azure Active Directory nahrazuje ověřování služby ACS se ověřování SAS. V tématu [Azure Active Directory je budoucí ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) informace o plánu přechodu.

### <a name="solution"></a>Řešení
Používejte ověřování SAS ve svých aplikacích. Následující příklad ukazuje, jak používat existující tokenu SAS pro přístup k oboru názvů service bus nebo entity.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Najdete v následujících tématech pro další informace.

* Přehled najdete v tématu [ověřování sdíleného přístupového podpisu službou Service Bus](https://msdn.microsoft.com/library/dn170477.aspx)
* [Jak používat ověřování sdíleného přístupového podpisu službou Service Bus](https://msdn.microsoft.com/library/dn205161.aspx)
* Ukázkový projekt, najdete v části [pomocí sdíleného přístupového podpisu (SAS) ověřování pomocí předplatných Service Bus](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Zvažte použití metody OnMessage předejdete "přijímat smyčka"
### <a name="id"></a>ID
AP2002

### <a name="description"></a>Popis
Aby se zabránilo probíhající do "přijímat smyčky," volání **OnMessage** metoda je lepší řešení pro příjem zprávy než volání **Receive** metoda. Ale pokud je nutné použít **Receive** metodu a zadejte čas čekání serveru jiné než výchozí, zkontrolujte, že doba čekání serveru je více než jedna minuta.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Při volání metody **OnMessage**, klient spustí vnitřní zpráva některé čerpadlo, který neustále dotazuje fronty nebo předplatné. Tato zpráva čerpadlo obsahuje nekonečnou smyčku, která vydává volání přijímat zprávy. Pokud vyprší časový limit volání, vydá nové volání. Interval vypršení časového limitu je dáno hodnotu [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) vlastnost [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)který se používá.

Výhodou použití **OnMessage** ve srovnání s **Receive** je, že uživatelé nemají k ručnímu dotazování na zprávy, zpracování výjimek, zpracovat více zpráv paralelně a dokončení zprávy.

Při volání **Receive** bez použití nastavení výchozí hodnoty, nezapomeňte *ServerWaitTime* hodnota je více než jedna minuta. Nastavení *ServerWaitTime* na více než jednu minutu zabrání serveru vypršel časový limit před plně doručení zprávy.

### <a name="solution"></a>Řešení
Podrobnosti viz následující příklady kódu pro použití, doporučené. Další podrobnosti najdete v tématu [QueueClient.OnMessage – metoda (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)a [QueueClient.Receive – metoda (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Pro zlepšení výkonu infrastruktury zasílání zpráv Azure, najdete v části vzor návrhu [asynchronní zasílání zpráv Úvod do](https://msdn.microsoft.com/library/dn589781.aspx).

Následuje příklad použití **OnMessage** přijímat zprávy.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Následuje příklad použití **Receive** se serverem výchozí doba čekání.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Následuje příklad použití **Receive** serveru jiné než výchozí doba čekání.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Zvažte použití asynchronních metod Service Bus
### <a name="id"></a>ID
AP2003

### <a name="description"></a>Popis
Asynchronní metody Service Bus používejte ke zlepšení výkonu pomocí zprostředkované zasílání zpráv.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Použití asynchronních metod umožňuje souběžnosti program aplikace, protože provádění jednotlivých volání neblokuje hlavního vlákna. Při použití služby Service Bus metody pro zasílání zpráv, provádění operace (odesílání, příjem, odstranit, apod) trvá určitou dobu. Tentokrát zahrnuje zpracování operace služby Service Bus kromě latence požadavku a odpovědi. Pokud chcete zvýšit počet operací za čas, musí současně provést operace. Další informace naleznete v [osvědčené postupy pro výkon vylepšení pomocí Service Bus zprostředkované zasílání zpráv](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Řešení
V tématu [QueueClient – třída (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) informace o tom, jak používat doporučené asynchronní metody.

Pro zlepšení výkonu infrastruktury zasílání zpráv Azure, najdete v části vzor návrhu [asynchronní zasílání zpráv Úvod do](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Vezměte v úvahu rozdělení fronty Service Bus a témat
### <a name="id"></a>ID
AP2004

### <a name="description"></a>Popis
Oddíl fronty Service Bus a témat pro lepší výkon s zasílání zpráv Service Bus.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Vytváření oddílů fronty sběrnice a témata zvýšíte dostupnost výkonu propustnost a služby, protože celkovou propustnost oddílů fronta nebo téma už není omezené podle výkonu zprostředkovatele jedné zprávy nebo úložišti pro přenos zpráv. Kromě toho dočasnému výpadku zasílání zpráv úložiště není znepřístupnit oddílů fronta nebo téma. Další informace najdete v tématu [dělení entity zasílání zpráv](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Řešení
Následující fragment kódu ukazuje, jak oddílu entit pro zasílání zpráv.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Další informace najdete v tématu [rozdělena na oddíly fronty služby Service Bus a témat | Microsoft Azure Blog](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) a podívejte se [Microsoft Azure Service Bus rozdělena na oddíly fronty](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) ukázka.

## <a name="do-not-set-sharedaccessstarttime"></a>Nenastavujte SharedAccessStartTime
### <a name="id"></a>ID
AP3001

### <a name="description"></a>Popis
Byste neměli používat SharedAccessStartTimeset na aktuální čas k okamžitému spuštění zásady sdíleného přístupu. Stačí tuto vlastnost nastavit, pokud chcete spustit později zásady sdíleného přístupu.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Synchronizace hodin způsobí, že mírné časový rozdíl mezi datovými centry. Například by logicky domníváte, že nastavení času spuštění úložiště SAS zásady jako aktuální čas pomocí DateTime.Now nebo podobné metody způsobí, že zásady SAS tak, aby se projeví okamžitě. Mírné časové rozdíly mezi datovými centry však může způsobit problémy s tím, vzhledem k tomu, že některé datacenter časy může být mírně pozdější než čas zahájení, zatímco jiní jej před jeho. V důsledku toho můžete zásady SAS vyprší rychle (nebo i okamžitě) Pokud je nastavená příliš krátká doba platnosti zásad.

Další informace o používání sdíleného přístupového podpisu na úložiště Azure najdete v části [představení tabulky SAS (sdíleného přístupového podpisu), fronta SAS a proveďte aktualizaci objektu Blob SAS – Blog týmu pro úložiště Azure Microsoft - lokality Domů - Blogy MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Řešení
Odeberte příkaz, který nastaví čas zahájení zásady sdíleného přístupu. Nástroj pro analýzu kódu Azure poskytuje opravu tohoto problému. Další informace o správu zabezpečení, najdete v tématu vzor návrhu [vzor klíč osobní služby](https://msdn.microsoft.com/library/dn568102.aspx).

Následující fragment kódu ukazuje opravu kód pro tento problém.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Sdílené zásady přístupu čas vypršení platnosti musí být delší než 5 minut
### <a name="id"></a>ID
AP3002

### <a name="description"></a>Popis
Může být co nejvíce pět minut rozdíl v hodiny mezi datovými centry v různých umístěních kvůli Stav známý jako "posun hodin." Abyste zabránili SAS token zásady vypršení platnosti dříve, než plánované, nastavte čas vypršení platnosti být více než pět minut.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Signál hodiny synchronizovat datových center v různých umístěních po celém světě. Protože trvá dobu hodiny signál k dostavit do různých umístění, může být čas odchylka mezi datovými centry v různých geografických umístěních i když všechno, co je zřejmě synchronizován. Tento časový rozdíl může ovlivnit sdíleného přístupu zásad počáteční čas a vypršení platnosti interval. Proto aby zásady sdíleného přístupu se okamžitě projeví, nezadávejte čas spuštění. Kromě toho zajistěte, aby byl čas vypršení platnosti více než 5 minut, aby se zabránilo časná časový limit.

Další informace o používání sdíleného přístupového podpisu na úložiště Azure najdete v tématu [představení tabulky SAS (sdíleného přístupového podpisu), fronta SAS a proveďte aktualizaci objektu Blob SAS – Blog týmu pro úložiště Azure Microsoft - lokality Domů - Blogy MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Řešení
Další informace o správu zabezpečení, najdete v části vzor návrhu [vzor klíč osobní služby](https://msdn.microsoft.com/library/dn568102.aspx).

Následuje příklad není zadávání čas spuštění zásady sdíleného přístupu.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Následuje příklad zadání čas spuštění zásady sdíleného přístupu s doba vypršení platnosti zásada, která je větší než pět minut.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Další informace najdete v tématu [vytváření a používání sdíleného přístupového podpisu](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Použití CloudConfigurationManager
### <a name="id"></a>ID
AP4000

### <a name="description"></a>Popis
Pomocí [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) třídy pro projekty, jako je Azure web a mobilní služby Azure nebude znamenat problémy modulu runtime. Jako osvědčený postup je však vhodné použít cloudové[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) jako jednotné způsob správy konfigurace pro všechny aplikace cloudu Azure.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
CloudConfigurationManager přečte vhodné prostředí aplikace konfigurační soubor.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Řešení
Refaktorovat kód pro použití [třída CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Nástroj pro analýzu kódu Azure zajišťuje opravy kódu pro tento problém.

Následující fragment kódu ukazuje opravu kód pro tento problém. Nahradit

`var settings = ConfigurationManager.AppSettings["mySettings"];`

S

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Tady je příklad toho, jak uložit nastavení konfigurace do souboru App.config nebo Web.config. Přidejte nastavení do oddílu appSettings konfiguračního souboru. Zde je soubor Web.config pro předchozí příklad kódu.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Nepoužívejte pevně připojovací řetězce
### <a name="id"></a>ID
AP4001

### <a name="description"></a>Popis
Pokud používáte pevně připojovací řetězce a je potřeba je aktualizovat později, budete muset provést změny vašeho zdrojového kódu a překompilování aplikace. Ale pokud uchováváte připojovací řetězce v konfiguračním souboru, můžete je později změníte jednoduše aktualizací konfiguračního souboru.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Pevně kódováno připojovací řetězce je chybný postup, protože by to zavedlo problémy, když je potřeba rychle změnit připojovací řetězce. Kromě toho pokud projektu musí být vráceny se změnami do správy zdrojového kódu, zavést pevně připojovací řetězce ohrožení zabezpečení, protože řetězce lze zobrazit ve zdrojovém kódu.

### <a name="solution"></a>Řešení
Ukládání připojovacích řetězců v prostředí Azure na konfigurační soubory.

* Pro samostatné aplikace pomocí souboru app.config uložit nastavení připojovacího řetězce.
* Pro hostované službou IIS webových aplikací použijte soubor web.config k ukládání připojovacích řetězců.
* Pro aplikace ASP.NET vNext použijte k ukládání připojovacích řetězců configuration.json.

Informace o používání soubory konfigurace, jako je soubor web.config nebo app.config najdete v tématu [pokyny pro ASP.NET Web konfigurace](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Informace o tom, jak Azure pracovní proměnné prostředí, najdete v části [weby Azure: fungování řetězců aplikace a připojovací řetězce fungovat](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Informace týkající se ukládání připojovací řetězec do správy zdrojového kódu najdete v tématu [neukládejte citlivé informace, jako je například připojovací řetězce v souborech, které jsou uložené v úložiště zdrojového kódu](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Použijte diagnostiku konfigurační soubor
### <a name="id"></a>ID
AP5000

### <a name="description"></a>Popis
Namísto konfigurace nastavení diagnostiky ve vašem kódu, například pomocí Microsoft.WindowsAzure.Diagnostics programovací rozhraní API, měli byste nakonfigurovat nastavení diagnostiky v souboru diagnostics.wadcfg. (Nebo, pokud používáte Azure SDK 2.5 diagnostics.wadcfgx). Díky tomu můžete změnit nastavení diagnostiky bez nutnosti její kompilace kódu.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Předtím, než pomocí několika různých metod se daly konfigurovat Azure SDK 2.5, (která používá Azure diagnostics 1.3), Azure Diagnostics (WAD): přidání do objektu blob konfigurace v úložišti pomocí imperativní kódu, deklarativní konfigurace nebo výchozí konfigurace. Upřednostňovaný způsob konfigurace diagnostiky je však můžete použít soubor XML konfigurace (diagnostics.wadcfg nebo diagnositcs.wadcfgx pro sadu SDK, 2.5 a novější) v projektu aplikace. V tomto přístupu soubor diagnostics.wadcfg úplně definuje konfiguraci a lze aktualizovat a znovu nasazena na bude. Kombinování použití konfiguračního souboru diagnostics.wadcfg pomocí programový metod nastavení konfigurace pomocí [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)nebo [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)třídy může vést k nejasnostem. V tématu [inicializovat nebo změna konfigurace diagnostiky Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) Další informace.

Počínaje WAD 1.3 (zahrnutá v Azure SDK 2.5), je již nebude možné použít ke konfiguraci diagnostiky kódu. V důsledku toho můžete zadat pouze při použití nebo aktualizaci rozšíření diagnostiky konfigurace.

### <a name="solution"></a>Řešení
Pomocí návrháře konfigurace diagnostiky přesunutí nastavení pro diagnostiku do konfiguračního souboru diagnostiky (diagnositcs.wadcfg nebo diagnositcs.wadcfgx pro sadu SDK, 2.5 a novější). Doporučujeme také nainstalovat [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) a použít nejnovější funkce diagnostiky.

1. V místní nabídce pro roli, kterou chcete nakonfigurovat, vyberte vlastnosti a potom vyberte na kartě konfigurace.
2. V **diagnostiky** část, ujistěte se, že **povolení diagnostiky** je zaškrtnuté políčko.
3. Vyberte **konfigurace** tlačítko.

   ![Přístup k možnost povolení diagnostiky](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   V tématu [konfigurace diagnostiky pro Azure Cloud Services a virtuální počítače](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) Další informace.

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Vyhněte se deklarace objektů DbContext jako statické
### <a name="id"></a>ID
AP6000

### <a name="description"></a>Popis
Pokud chcete uložit paměti, vyhněte se deklarace objektů DBContext jako statické.

Prosím sdílet své myšlenky a zpětnou vazbu na [zpětnou vazbu analýza kódu Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Důvod
Objekty DBContext ukládání výsledků dotazu z každé volání. Statické DBContext objekty nebyly použity, dokud doménu aplikace je odpojen. Proto statický objekt DBContext spotřebovat velké objemy paměti.

### <a name="solution"></a>Řešení
Jako místní proměnné nebo pole instance nestatické deklarovat DBContext, použijte pro úlohy a nechat ji bude zrušen z po použití.

V následujícím příkladu třída controller MVC ukazuje, jak použít objekt DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Další kroky
Další informace o optimalizaci a řešení potíží s aplikací Azure najdete v tématu [řešení potíží s webovou aplikaci v Azure App Service pomocí sady Visual Studio](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).
