---
title: Přehled komunikace spolehlivých služeb
description: Přehled modelu komunikace spolehlivé služby, včetně otevření naslouchacích procesů na služby, řešení koncových bodů a komunikaci mezi službami.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 3c1a6cfa5227369bf1cde4af087019727c22c0c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462948"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak používat komunikační api služby spolehlivé služby
Azure Service Fabric jako platforma je zcela agnostik o komunikaci mezi službami. Všechny protokoly a zásobníky jsou přijatelné, od UDP do HTTP. Je na vývojáři služby, aby si vybral, jak mají služby komunikovat. Architektura aplikací služby spolehlivé služby poskytuje integrované komunikační zásobníky, stejně jako rozhraní API, které můžete použít k vytvoření vlastní komunikační součásti.

## <a name="set-up-service-communication"></a>Nastavení komunikace se službami
Rozhraní API služby reliable services používá jednoduché rozhraní pro komunikaci služeb. Chcete-li otevřít koncový bod pro vaši službu, jednoduše implementujte toto rozhraní:

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

Potom můžete přidat implementaci naslouchací proces komunikace vrácením v přepsání metody třídy založené na službě.

Pro služby bez státní příslušnosti:

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

V obou případech vrátíte kolekci naslouchacích procesy. To umožňuje službě naslouchat na více koncových bodů, potenciálně pomocí různých protokolů, pomocí více naslouchacích procesy. Například můžete mít naslouchací proces HTTP a samostatný websocket naslouchací proces. Každý naslouchací proces získá název a výsledná kolekce *název : dvojice adres* jsou reprezentovány jako objekt JSON, když klient požaduje naslouchající adresy pro instanci služby nebo oddíl.

Ve službě bezstavové přepsání vrátí kolekci ServiceInstanceListeners. A `ServiceInstanceListener` obsahuje funkci pro `ICommunicationListener(C#) / CommunicationListener(Java)` vytvoření a dává jí název. Pro stavové služby přepsání vrátí kolekci ServiceReplicaListeners. To se mírně liší od jeho protějšek bez stavů, protože `ServiceReplicaListener` má možnost otevřít `ICommunicationListener` na sekundární repliky. Nejen, že můžete použít více naslouchacích procesy komunikace ve službě, ale můžete také určit, které posluchače přijímat požadavky na sekundární repliky a které z nich poslouchat pouze na primární repliky.

Můžete mít například ServiceRemotingListener, který přijímá volání Vzdálené volání pouze na primární repliky a druhý, vlastní naslouchací proces, který přijímá požadavky na čtení na sekundární repliky přes HTTP:

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
> Při vytváření více naslouchacích procesů pro službu **musí** být každému naslouchací procesu přidělen jedinečný název.
>
>

Nakonec popište koncové body, které jsou požadovány pro službu v [manifestu služby](service-fabric-application-and-service-manifests.md) v části koncové body.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Naslouchací proces komunikace přístup k `CodePackageActivationContext` prostředkům `ServiceContext`koncového bodu, které jsou mu přiděleny z v . Naslouchací proces pak můžete začít naslouchat požadavkům při otevření.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Prostředky koncového bodu jsou společné pro celý balíček služeb a jsou přiděleny Service Fabric při aktivaci balíčku služby. Více replik služby hostované ve stejném ServiceHost může sdílet stejný port. To znamená, že naslouchací proces komunikace by měl podporovat sdílení portů. Doporučený způsob, jak to udělat, je pro naslouchací proces komunikace použít ID oddílu a ID repliky/instance při generování adresy naslouchání.
>
>

### <a name="service-address-registration"></a>Registrace adresy služby
V clusterech Service Fabric je spuštěna systémová služba s názvem *Služba pojmenování.* Služba pojmenování je registrátor pro služby a jejich adresy, které každá instance nebo replika služby naslouchá. Po `OpenAsync(C#) / openAsync(Java)` dokončení metoda, jeho vrácená `ICommunicationListener(C#) / CommunicationListener(Java)` hodnota získá registrována ve službě pojmenování. Tato vrácená hodnota, která získá publikována ve službě pojmenování je řetězec, jehož hodnota může být vůbec cokoli. Tato hodnota řetězce je to, co klienti zobrazit, když se ptají na adresu pro službu od pojmenování služby.

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

Service Fabric poskytuje řešení API, která umožňují klientům a dalším službám požádat o tuto adresu podle názvu služby. To je důležité, protože adresa služby není statická. Služby jsou přesouvány v clusteru pro účely vyrovnávání prostředků a dostupnosti. Toto je mechanismus, který umožňuje klientům vyřešit naslouchání adresu pro službu.

> [!NOTE]
> Úplný návod, jak napsat naslouchací proces komunikace, najdete v článku [Service Fabric Web API služby s OWIN self-hosting](service-fabric-reliable-services-communication-webapi.md) pro https://github.com/Azure-Samples/service-fabric-java-getting-startedC#, vzhledem k tomu, pro Java můžete napsat vlastní implementaci http serveru, viz EchoServer aplikace příklad na .
>
>

## <a name="communicating-with-a-service"></a>Komunikace se službou
Rozhraní API služby reliable services poskytuje následující knihovny pro zápis klientů, kteří komunikují se službami.

### <a name="service-endpoint-resolution"></a>Rozlišení koncového bodu služby
Prvním krokem ke komunikaci se službou je vyřešit adresu koncového bodu oddílu nebo instance služby, se kterou chcete mluvit. Třída `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` nástroje je základní primitiv, který pomáhá klientům určit koncový bod služby za běhu. V terminologii Service Fabric se proces určování koncového bodu služby označuje jako *rozlišení koncového bodu služby*.

Chcete-li se připojit ke službám v rámci clusteru, servicepartitionresolver lze vytvořit pomocí výchozího nastavení. Toto je doporučené použití pro většinu situací:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Chcete-li se připojit ke službám v jiném clusteru, servicepartitionresolver lze vytvořit se sadou koncových bodů brány clusteru. Všimněte si, že koncové body brány jsou pouze různé koncové body pro připojení ke stejnému clusteru. Například:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternativně `ServicePartitionResolver` může být poskytnuta funkce `FabricClient` pro vytvoření pro použití interně:

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

`FabricClient`je objekt, který se používá ke komunikaci s clusterem Service Fabric pro různé operace správy v clusteru. To je užitečné, pokud chcete mít větší kontrolu nad tím, jak překládání oddílů služby spolupracuje s clusterem. `FabricClient`provádí ukládání do mezipaměti interně a je obecně nákladné `FabricClient` vytvořit, takže je důležité znovu použít instance co nejvíce.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Metoda resolve se pak používá k načtení adresy služby nebo oddílu služby pro dělené služby.

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

Adresu služby lze snadno přeložit pomocí ServicePartitionResolver, ale je zapotřebí více práce, aby bylo zajištěno, že vyřešená adresa může být použita správně. Klient musí zjistit, zda se pokus o připojení nezdařil z důvodu přechodné chyby a může být opakován (např. služba přesunuta nebo je dočasně nedostupná) nebo trvalou chybu (např. služba byla odstraněna nebo požadovaný prostředek již neexistuje). Instance služby nebo repliky se mohou kdykoli pohybovat z uzlu do uzlu z více důvodů. Adresa služby vyřešena prostřednictvím ServicePartitionResolver může být zastaralá v době, kdy se klientský kód pokusí o připojení. V takovém případě musí klient adresu znovu vyřešit. Poskytnutí předchozí `ResolvedServicePartition` označuje, že překládání musí zkusit znovu, nikoli jednoduše načíst adresu uloženou v mezipaměti.

Kód klienta obvykle nemusí pracovat s ServicePartitionResolver přímo. Je vytvořen a předán do továrny komunikačních klientů v rozhraní API spolehlivých služeb. Továrny používají překladač interně ke generování objektu klienta, který lze použít ke komunikaci se službami.

### <a name="communication-clients-and-factories"></a>Komunikační klienti a továrny
Knihovna továrny komunikace implementuje typický vzor opakování zpracování chyb, který usnadňuje opakování připojení k vyřešeným koncovým bodům služby. Tovární knihovna poskytuje mechanismus opakování při poskytování obslužné rutiny chyb.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`definuje základní rozhraní implementované totoovou továrnou komunikačního klienta, která vytváří klienty, kteří mohou komunikovat se službou Service Fabric. Implementace CommunicationClientFactory závisí na zásobníku komunikace používanéslužby Service Fabric, kde klient chce komunikovat. Rozhraní API služby `CommunicationClientFactoryBase<TCommunicationClient>`Reliable Services poskytuje rozhraní . To poskytuje základní implementaci rozhraní CommunicationClientFactory a provádí úlohy, které jsou společné pro všechny komunikační zásobníky. (Tyto úkoly zahrnují použití ServicePartitionResolver k určení koncového bodu služby). Klienti obvykle implementují abstraktní třídu CommunicationClientFactoryBase pro zpracování logiky, která je specifická pro zásobník komunikace.

Komunikační klient pouze obdrží adresu a použije ji k připojení ke službě. Klient může použít jakýkoliv protokol, který chce.

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

Klientská továrna je primárně zodpovědná za vytváření komunikačních klientů. Pro klienty, kteří neudržují trvalé připojení, jako je například klient HTTP, je potřeba pouze vytvořit a vrátit klienta. Jiné protokoly, které udržují trvalé připojení, jako jsou například některé binární protokoly, by měly být také ověřeny výrobcem, aby bylo možné určit, zda je třeba připojení znovu vytvořit.  

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

Nakonec obslužná rutina výjimky je zodpovědná za určení, jaké akce má být v případě výjimky. Výjimky jsou rozděleny do **opakovatelné** a **neopakovatelné**.

* **Neopakovatelné** výjimky jednoduše získat znovu vyvolána zpět volajícímu.
* **opakovatelné** výjimky jsou dále **rozděleny** do přechodné a **nepřechodné**.
  * **Přechodné** výjimky jsou ty, které lze jednoduše opakovat bez opětovného překladu adresy koncového bodu služby. Ty budou zahrnovat přechodné problémy se sítí nebo odpovědi na chyby služby jiné než ty, které označují, že adresa koncového bodu služby neexistuje.
  * **Nepřechodné** výjimky jsou ty, které vyžadují převyřešení adresy koncového bodu služby. Patří mezi ně výjimky, které označují koncový bod služby nelze dosáhnout, označující, že služba byla přesunuta do jiného uzlu.

Rozhoduje `TryHandleException` o dané výjimce. Pokud **neví,** jaká rozhodnutí učinit o výjimce, měla by vrátit **false**. Pokud **ví,** jaké rozhodnutí učinit, měl by podle toho nastavit výsledek a vrátit **hodnotu true**.

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
S `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, `IExceptionHandler(C#) / ExceptionHandler(Java)` a postavena kolem `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` komunikační protokol, obtéká vše dohromady a poskytuje zpracování chyb a service partition rozlišení smyčky řešení kolem těchto součástí.

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

## <a name="next-steps"></a>Další kroky
* [ASP.NET jádro se spolehlivými službami](service-fabric-reliable-services-communication-aspnetcore.md)
* [Vzdálená volání procedur pomocí vzdálené komunikace spolehlivých služeb](service-fabric-reliable-services-communication-remoting.md)
* [Komunikace WCF pomocí spolehlivých služeb](service-fabric-reliable-services-communication-wcf.md)
