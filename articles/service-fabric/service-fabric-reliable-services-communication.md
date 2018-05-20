---
title: Spolehlivé služby communication – přehled | Microsoft Docs
description: Přehled modelu komunikace spolehlivé služby, včetně otevírání moduly pro naslouchání na službách, vyřešte koncových bodů a komunikaci mezi službami.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 62c81368b8a3129b42262cb99cf23a5021744c1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak používat rozhraní API komunikaci spolehlivé služby
Azure Service Fabric jako platformu je zcela lhostejné o komunikaci mezi službami. Všechny protokoly a zásobníky jsou přijatelné, z UDP do HTTP. Je to na vývojáře služby zvolit komunikace služby. Rozhraní spolehlivé služby poskytuje zásobníky předdefinované komunikaci, jakož i rozhraní API, které můžete použít k vytvoření vlastních komunikační součásti.

## <a name="set-up-service-communication"></a>Nastavení komunikace služby
Spolehlivé služby API používá jednoduché rozhraní pro komunikace služby. Chcete-li spustit koncový bod služby, jednoduše toto rozhraní implementujte:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Poté můžete přidat implementaci naslouchací proces komunikace vrácením v přepsání metody založené na službě třídy.

Pro bezstavové služby:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Pro stavové služby:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

V obou případech vracet kolekci naslouchacího procesu. To umožňuje služba naslouchala na několik koncových bodů, potenciálně pomocí různých protokolů, s použitím více naslouchací procesy. Například můžete mít naslouchací proces protokolu HTTP a samostatné naslouchací proces protokolu WebSocket. Každý naslouchací proces získá název a výsledný kolekce *název: adresa* páry jsou reprezentovány jako objekt JSON, když klient požádá o naslouchání adresy pro instance služby nebo oddíl.

Přepsání v bezstavové služby vrátí kolekci ServiceInstanceListeners. A `ServiceInstanceListener` obsahuje funkci pro vytvoření `ICommunicationListener(C#) / CommunicationListener(Java)` a název. Přepsání pro stavové služby, vrátí kolekci ServiceReplicaListeners. To se mírně liší od jeho protějšku bezstavové, protože `ServiceReplicaListener` má možnost otevření `ICommunicationListener` na sekundárních replikách. Nejenže můžete použít více naslouchací procesy komunikace ve službě, ale můžete také určit, který naslouchací procesy přijímat požadavky na sekundárních replikách a ty, které naslouchat pouze primární repliky.

Například můžete mít ServiceRemotingListener, která přebírá volání RPC jenom na primární repliky a druhý, vlastní naslouchací proces, který přebírá čtení požadavky na sekundárních replikách prostřednictvím protokolu HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Při vytváření více naslouchací procesy pro službu, každý naslouchací proces **musí** mít jedinečný název.
>
>

Nakonec popisují koncové body, které jsou požadovány pro služby [service manifest](service-fabric-application-and-service-manifests.md) části u koncových bodů.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Naslouchací proces komunikace můžete přístup k prostředkům koncový bod přidělených z `CodePackageActivationContext` v `ServiceContext`. Naslouchací proces potom lze spustit naslouchaly žádostem, když je otevřen.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Koncový bod prostředků, které jsou společné pro celý balíček a jsou přiděleny pomocí Service Fabric, když je aktivován balíčku služby. Víc replik služby hostované ve stejném ServiceHost může sdílet stejný port. To znamená, že by měly podporovat komunikaci naslouchací proces sdílení portů. Doporučený způsob to provést, je pro komunikaci naslouchací proces pro použití oddílu, ID a ID repliky nebo instanci, když ji vygeneruje adresu naslouchání.
>
>

### <a name="service-address-registration"></a>Registrace adresy služby
Volá se služba system *služby DNS* běží na clusterů Service Fabric. Služby DNS je doménového registrátora služeb a jejich adresy, které naslouchá na každou instanci nebo repliky služby. Když `OpenAsync(C#) / openAsync(Java)` metodu `ICommunicationListener(C#) / CommunicationListener(Java)` dokončí, vraťte se jeho hodnota získá registrované ve službě pojmenování. Tato návratovou hodnotu, která zveřejnění ve službě pojmenování je řetězec, jehož hodnota může být jakýkoli vůbec. Tato hodnota řetězce je, co klienti uvidí, když vyzvou ze služby DNS pro adresu pro službu.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric poskytuje rozhraní API umožňující klientů a dalším službám, a pak požádejte podle názvu služby pro tuto adresu. To je důležité, protože není statickou adresu služby. Služby se přesouvají v clusteru pro účely vyrovnávání a dostupnosti prostředků. Toto je mechanismus, který klientům umožňují překlad adresu naslouchání pro službu.

> [!NOTE]
> Kompletní návod jak napsat naslouchací proces komunikace, najdete v části [Service Fabric webového rozhraní API služby s vlastním hostování OWIN](service-fabric-reliable-services-communication-webapi.md) C#, zatímco pro jazyk Java můžete napsat vlastní implementaci serveru HTTP, najdete v části EchoServer aplikace Příklad v https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Komunikace se službou
Spolehlivé služby API poskytuje následující knihovny zápis klienty, kteří komunikovat se službami.

### <a name="service-endpoint-resolution"></a>Rozlišení koncového bodu služby
Prvním krokem k komunikace se službou je k vyřešení adresu koncového bodu oddílu, nebo instance služby, kterou chcete, aby komunikoval s. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` Nástroj třída je základní Primitivum, které pomáhají klientům zjistit koncový bod služby za běhu. V terminologii Service Fabric, proces určování koncový bod služby se označuje jako *rozlišení koncového bodu služby*.

Pro připojení ke službám v rámci clusteru, můžete vytvořit ServicePartitionResolver pomocí výchozího nastavení. Toto je doporučený způsob většině situací:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Pro připojení ke službám v jiném clusteru, lze vytvořit ServicePartitionResolver sadu koncovým bodům clusteru brány. Upozorňujeme, že jsou koncové body brány právě různými koncovými body pro připojení do stejného clusteru. Příklad:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternativně `ServicePartitionResolver` možné přidělit funkce pro vytváření `FabricClient` interně použít:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` je objekt, který se používá ke komunikaci se cluster Service Fabric pro různé operace správy v clusteru. To je užitečné, pokud chcete mít větší kontrolu nad interakci překladač oddílu služby se váš cluster. `FabricClient` provede ukládání do mezipaměti interně a je obecně nákladné vytvořit, takže je potřeba znovu použít `FabricClient` instance co nejvíc.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Vyřešte metoda pak slouží k načtení adresu služby nebo služby u oddílů služby.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Adresa služby lze vyřešit pomocí snadno ServicePartitionResolver, ale je potřeba další práci zajistěte, aby byl že vyřešen adresu je možné použít správně. Váš klient potřebuje zjistit, zda pokus o připojení se nezdařilo z důvodu přechodná chyba a můžete zkusit znovu (například služba přesunut nebo je dočasně nedostupná), nebo trvalé chybě (např. Služba je Odstraněná nebo požadovaný prostředek již existuje). Instance služby nebo repliky můžete pohyb z uzlu do uzlu kdykoli z několika důvodů. Adresa služby přeložit prostřednictvím ServicePartitionResolver může být zastaralé dobu, kterou váš klientský kód pokusí o připojení. V takovém případě znovu klient musí znovu překlad adresy. Poskytnutí předchozí `ResolvedServicePartition` ukazuje, že překladač vyžaduje pokus místo jednoduše načíst adresu v mezipaměti.

Obvykle kód klienta nemusí pracovat ServicePartitionResolver přímo. Je vytvořen a k předání komunikace klienta továrny v spolehlivé rozhraní API služby. Překladač objekty Factory interně použít ke generování klientského objektu, který umožňuje komunikovat se službami.

### <a name="communication-clients-and-factories"></a>Komunikace klientů a objekty pro vytváření
Knihovna vytváření komunikace implementuje typický vzor opakování selhání zpracování, který usnadňuje Probíhá opakování připojení ke koncovým bodům služby vyřešený. Objekt pro vytváření knihovny poskytuje mechanismus opakování, zatímco poskytují chyba obslužné rutiny.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` definuje základní rozhraní implementované objekt factory komunikaci klienta, který vytváří klientů, které může kontaktovat službě Service Fabric. Implementace CommunicationClientFactory závisí na komunikačního balíku používá služba Service Fabric, kde se chce, aby klient komunikovat. Poskytuje spolehlivé rozhraní API služby `CommunicationClientFactoryBase<TCommunicationClient>`. To poskytuje základní implementaci rozhraní CommunicationClientFactory a provádí úlohy, které jsou společné pro všechny balíčky komunikace. (Tyto úlohy patří, použití ServicePartitionResolver k určení, koncový bod služby). Klienti obvykle implementovat abstraktní třídy CommunicationClientFactoryBase pro zpracování logiky, která je specifická pro komunikačního balíku.

Komunikace klienta pouze přijímá adresu a použije k připojení ke službě. Klienta můžete použít libovolnou protokol ho chce.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

Dodavatel klienta především zodpovídá za vytvoření komunikace klientů. Pro klienty, kteří nejsou udržovat trvalé připojení, jako je například klientovi HTTP musí objekt pro vytváření pouze k vytvoření a vrátí klientovi. Jiné protokoly, které udržují trvalé připojení, jako je například některé binární protokoly by měl být také ověřené pomocí objektu pro vytváření k určení, zda připojení musí být znovu vytvořena.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Nakonec obslužné rutiny výjimky je zodpovědná za určení, jaká opatření se mají provést, když dojde k výjimce. Výjimky jsou rozdělené do **opakovatelné** a **neopakovatelného**.

* **Neopakovatelného** výjimky jednoduše získat znovu vyvolány zpět k volajícímu.
* **Opakovatelná** výjimky se dále dělí do **přechodný** a **-pouze dočasné**.
  * **Přechodný** výjimky jsou ty, které můžete jednoduše opakovat bez znovu řešení adresa koncového bodu služby. To bude obsahovat přechodné problémy se sítí nebo služby chybové odpovědi kromě těch, které indikuje, že adresa koncového bodu služby neexistuje.
  * **Bez přechodná** výjimky jsou ty, které vyžadují adresa koncového bodu služby do znovu přeložit. Mezi ně patří výjimky, které označují, že není dostupný koncový bod služby, označující služby se přesunul do jiného uzlu.

`TryHandleException` Provádí rozhodnutí o dané výjimka. Pokud ho **neví** jaké rozhodnutí, která chcete-li o výjimku, měla by vrátit **false**. Pokud ho **vědět** jaké rozhodnutí, měl by odpovídajícím způsobem nastavit výsledek a vrátí **true**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {        

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Třeba umisťovat všechny společně
S `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, a `IExceptionHandler(C#) / ExceptionHandler(Java)` vytvořených na základě komunikační protokol, `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` všechny společně se zabalí a poskytuje řešení smyčky adresu oddílu zpracování chyb a služeb kolem těchto součástí.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Další postup
* [Jádro ASP.NET se službami Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Volání vzdálených procedur s vzdálenou komunikaci spolehlivé služby](service-fabric-reliable-services-communication-remoting.md)
* [Komunikace WCF pomocí spolehlivé služby](service-fabric-reliable-services-communication-wcf.md)
