---
title: Přehled Reliable Services komunikace | Dokumentace Microsoftu
description: Přehled komunikace modelu Reliable Services, včetně počáteční naslouchací procesy u služby, vyřešte koncových bodů a komunikace mezi službami.
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
ms.openlocfilehash: 49f5a74c2fcd45d03119bffbffad6fcf30e72440
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570551"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak používat rozhraní API komunikaci modelu Reliable Services
Azure Service Fabric jako platforma je zcela bez o komunikaci mezi službami. Všechny protokoly a balíčky jsou přijatelné, z UDP pro HTTP. Záleží jen na vývojářské služby vybrat komunikaci služby. Rozhraní framework aplikace Reliable Services poskytuje integrované komunikační balíky, jakož i rozhraní API, která vám umožní vytvářet vlastní komunikační součásti.

## <a name="set-up-service-communication"></a>Nastavení komunikace služeb
Reliable Services API používá jednoduché rozhraní pro komunikaci služby. Chcete-li otevřít koncový bod pro vaši službu, stačí pouze Implementujte toto rozhraní:

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

Poté můžete přidat vaši implementaci naslouchací proces komunikace vrácením v přepsání metody třídy založené na službách.

U bezstavových služeb:

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

V obou případech se vrátí kolekce naslouchacích procesů. To umožňuje službě k naslouchání na několik koncových bodů, potenciálně používající různé protokoly, pomocí více naslouchacích procesů. Například může mít naslouchací proces protokolu HTTP a samostatné naslouchací proces protokolu WebSocket. Každý naslouchací proces získá název a kolekci výsledný *název: adresa* dvojice jsou reprezentovány jako objekt JSON, když klient požádá o naslouchání adresy pro instance služby nebo oddíl.

Přepsání v bezstavovou službu, vrátí kolekci ServiceInstanceListeners. A `ServiceInstanceListener` obsahuje funkci, která vytvoří `ICommunicationListener(C#) / CommunicationListener(Java)` a dá jí název. Přepsání pro stavové služby, vrátí kolekci ServiceReplicaListeners. Tím se mírně liší od jeho protějšek bezstavové protože `ServiceReplicaListener` má možnost otevření `ICommunicationListener` na sekundárních replikách. Nejenže můžete použít několik naslouchacích procesů komunikace ve službě, ale můžete také určit, které naslouchací procesy přijímání požadavků na sekundárních replikách a ty, které naslouchá jenom na primární repliky.

Například můžete mít ServiceRemotingListener, který provede volání RPC pouze na primární repliky a druhý, vlastní naslouchací proces, který přijímá další požadavky na sekundárních replikách přes protokol HTTP:

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
> Při vytváření více naslouchacích procesů pro službu, každý naslouchací proces **musí** jedinečný název.
>
>

A konečně, popisuje koncové body, které jsou požadovány pro službu [manifestu služby](service-fabric-application-and-service-manifests.md) části na koncové body.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Naslouchací proces komunikace mají přístup k prostředkům koncový bod přiřazen z `CodePackageActivationContext` v `ServiceContext`. Naslouchací proces potom můžete spustit naslouchat žádostem, když je otevřen.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Koncový bod prostředků jsou společné pro balíček celou službu a jejich přidělení Service Fabric při aktivaci balíčku služby. Víc replik služby hostované ve stejném ServiceHost mohou sdílet stejný port. To znamená, že by měly podporovat naslouchací proces komunikace, sdílení portů. Doporučený způsob to dělat, je pro naslouchací proces komunikace použít oddíl ID a ID repliky a instance při generování adres naslouchání.
>
>

### <a name="service-address-registration"></a>Registrace adres služby
Volá se služba system *pojmenování Service* běží na clustery Service Fabric. Pojmenování Service je doménový Registrátor služeb a jejich adresy, které každou instancí nebo replik služby naslouchá. Když `OpenAsync(C#) / openAsync(Java)` metodu `ICommunicationListener(C#) / CommunicationListener(Java)` dokončí, její návratový hodnotu zaregistruje ve službě pojmenování Service. Tuto hodnotu, která získá publikována ve službě pojmenování Service je řetězec, jehož hodnota může být cokoli – vůbec. Tato hodnota řetězce se klienti zobrazí při jejich požádat o adresu pro službu z pojmenování Service.

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

Service Fabric poskytuje rozhraní API, díky kterým můžou klienti a dalším službám pak požádejte podle názvu služby pro tuto adresu. To je důležité, protože adresa služby není statická. Služby se přesouvat v clusteru za účelem vyrovnávání a dostupnost prostředků. Toto je mechanismus, který umožňují klientům přeložit adresu naslouchání pro službu.

> [!NOTE]
> Kompletní návod, jak o tom, jak psát naslouchací proces komunikace, naleznete v tématu [služeb Service Fabric webových rozhraní API s vlastním hostováním OWIN](service-fabric-reliable-services-communication-webapi.md) pro C#, že pro jazyk Java můžete napsat vlastní implementaci serveru HTTP, naleznete v tématu EchoServer aplikace – příklad v https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Komunikace se službou
Reliable Services API nabízí následující knihovny pro zápis klienty, kteří komunikují se službami.

### <a name="service-endpoint-resolution"></a>Rozlišení koncového bodu služby
Prvním krokem při komunikaci se službou se přeložit adresy koncového bodu oddílu nebo instance služby, kterou byste se obrátit. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` Utility třída je základní jednoduchého typu, které pomáhají klientům zjistit koncový bod služby za běhu. V Service Fabric terminologii procesu, který určuje koncový bod služby se označuje jako *rozlišení koncového bodu služby*.

Pro připojení ke službám v rámci clusteru, můžete vytvořit ServicePartitionResolver pomocí výchozího nastavení. Toto je doporučené použití pro většinu situací:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Pro připojení ke službám v jiném clusteru, ServicePartitionResolver vytvářet sadu koncových bodů clusteru brány. Všimněte si, že koncové body brány jsou právě různých koncových bodů pro připojení na stejném clusteru. Příklad:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternativně `ServicePartitionResolver` mohou být zadány funkce pro vytváření `FabricClient` pro vnitřní použití:

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

`FabricClient` je objekt, který se používá ke komunikaci s clusterem Service Fabric pro různé operace správy v clusteru. To je užitečné, pokud chcete mít větší kontrolu nad interakci překladače oddílu služby s vaším clusterem. `FabricClient` řeší ukládání v mezipaměti interně a je obecně jejich vytvoření náročné, takže je důležité pro opětovné použití `FabricClient` instance co největší míře.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Metodu resolve se pak použije k načtení adresu služby nebo oddíl služby oddílů služby.

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

Adresa služby lze vyřešit pomocí snadno ServicePartitionResolver, ale další práce je potřeba k tomu přeložit adresy může být použita správně. Váš klient potřebuje ke zjištění, zda pokus o připojení se nezdařilo z důvodu o přechodnou chybu a můžete opakovat (například služba přesunut nebo je dočasně nedostupná), nebo trvalé chybě (např. Služba se odstranila nebo požadovaný prostředek již existuje). Instance služby ani repliky můžete pohybovat z uzlu do uzlu v okamžiku z několika důvodů. Adresa služby prostřednictvím ServicePartitionResolver může být zastaralé v době, kdy se váš klientský kód se pokusí o připojení. V takovém případě znovu klient musí znovu přeložit adresu. Poskytuje předchozí `ResolvedServicePartition` znamená, že překladač měl znovu místo jednoduše načíst adresu v mezipaměti.

Obvykle klientský kód nemusí pracovat ServicePartitionResolver přímo. Je vytvořen a předán komunikace klienta továren v Reliable Services API. Překladač továren interně použít k vygenerování klientského objektu, který slouží ke komunikaci se službami.

### <a name="communication-clients-and-factories"></a>Komunikace klientů a objekty pro vytváření
Objekt pro vytváření knihovny komunikace implementuje typické modelu opakování zpracování chyb, který usnadňuje opakuje připojení ke koncovým bodům služby přeložit. Objekt pro vytváření knihovny poskytuje mechanismus opakování, zatímco poskytuje obslužné rutiny chyb.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` definuje základní rozhraní implementované factory komunikaci klienta, které vytvoří klienti, kteří můžou komunikovat s služby Service Fabric. Provádění CommunicationClientFactory závisí na komunikačního balíku využívané ve službě Service Fabric, kdy chce komunikaci klienta. Poskytuje rozhraní API služby Reliable Services `CommunicationClientFactoryBase<TCommunicationClient>`. To poskytuje základní implementaci rozhraní CommunicationClientFactory a provádí úlohy, které jsou společné pro všechny balíčky komunikace. (Tyto úlohy patří, použití ServicePartitionResolver k určení koncového bodu služby). Klienti obvykle implementace abstraktní třídy CommunicationClientFactoryBase pro zpracování logiky, která je specifická pro komunikačního balíku.

Komunikace klienta pouze obdrží adresu a použije ho k připojení ke službě. Klienta můžete použít jakýkoli protokol chce.

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

Objekt factory klienta primárně zodpovídá za vytvoření komunikace klientů. Pro klienty, kteří nemusíte udržovat trvalé připojení, jako je například klientovi HTTP musí objekt pro vytváření pouze k vytvoření a vrátí klientovi. Jiné protokoly, které udržují trvalé připojení, jako je například některé binární protokoly by měl být ověřen také výrobou určit, zda připojení musí být znovu vytvořen.  

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

Nakonec je odpovědností jakou akci chcete provést, když dojde k výjimce obslužné rutiny výjimky. Výjimky jsou rozdělené do kategorií **opakovatelná** a **neopakovatelného**.

* **Neopakovatelného** výjimky jednoduše získat znovu vyvolána zpět volajícímu.
* **Opakovatelná** výjimky se dále dělí do **přechodné** a **nepřechodných**.
  * **Přechodné** výjimky jsou ty, které můžete jednoduše opakovat bez opakování překladu adresy koncového bodu služby. Bude mezi ně patří, přechodné problémy se sítí nebo chybové odpovědi služby kromě těch, které naznačují, že adresa koncového bodu služby neexistuje.
  * **Non-transient** výjimky jsou ty, které vyžadují adresu koncového bodu služby, které je potřeba znovu vyřešit. Patří mezi ně výjimky, které označují, že koncový bod služby není dosažitelná, označující, služba se přesunul na jiný uzel.

`TryHandleException` Udělá rozhodnutí o daném výjimek. Pokud ho **nezná** jaká rozhodnutí o výjimku, měla by vrátit **false**. Pokud ho **vědět** jaká rozhodnutí, měl by odpovídajícím způsobem nastavit výsledek a vrátit **true**.

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
### <a name="putting-it-all-together"></a>Spojení všech součástí dohromady
Pomocí `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, a `IExceptionHandler(C#) / ExceptionHandler(Java)` vybudována okolo komunikační protokol, `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` obtéká všechno dohromady a poskytuje zpracování chyb a service oddíl adresu rozlišení smyčku okolo tyto komponenty.

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
* [ASP.NET Core s využitím Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Volání vzdálených procedur pomocí vzdálené komunikace modelu Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [WCF komunikace s využitím Reliable Services](service-fabric-reliable-services-communication-wcf.md)
