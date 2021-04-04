---
title: Přehled komunikace Reliable Services
description: Přehled komunikačního modelu Reliable Services, včetně otevření naslouchacího procesu pro služby, překladu koncových bodů a komunikace mezi službami.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 3436d29446e963faea9bda47f5a5247b7de7d859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97912610"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak používat rozhraní API pro komunikaci Reliable Services
Služba Azure Service Fabric jako platforma zcela nezávislá o komunikaci mezi službami. Všechny protokoly a zásobníky jsou přijatelné od UDP přes HTTP. Chcete-li zvolit, jak by měly služby komunikovat, je k tomu vývojář služby. Rozhraní Reliable Services Application Framework poskytuje integrované komunikační zásobníky i rozhraní API, které můžete použít k sestavení vlastních komunikačních komponent.

## <a name="set-up-service-communication"></a>Nastavení komunikace služby
Rozhraní Reliable Services API používá pro komunikaci se službou jednoduché rozhraní. Pro otevření koncového bodu pro vaši službu stačí implementovat toto rozhraní:

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

Pak můžete přidat implementaci naslouchacího procesu komunikace vrácením v přepsání metody třídy založené na službě.

Bezstavové služby:

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

V obou případech vrátíte kolekci posluchačů. Díky tomu může vaše služba naslouchat více koncovým bodům, potenciálně pomocí různých protokolů, a to pomocí více posluchačů. Můžete mít například naslouchací proces HTTP a samostatný naslouchací proces WebSocket. Každý naslouchací proces Získá název a výsledná kolekce *Název: páry adres* jsou reprezentovány jako objekt JSON, když klient požaduje naslouchající adresy pro instanci služby nebo oddíl.

V bezstavové službě vrátí přepsání kolekci ServiceInstanceListeners. `ServiceInstanceListener`Obsahuje funkci pro vytvoření `ICommunicationListener(C#) / CommunicationListener(Java)` a poskytuje název. U stavových služeb vrátí přepsání kolekci ServiceReplicaListeners. To se mírně liší od nestavového protějšku, protože `ServiceReplicaListener` má možnost otevřít `ICommunicationListener` na sekundárních replikách. V rámci služby můžete nejen použít více posluchačů komunikace, ale můžete také určit, které naslouchací procesy přijímají požadavky na sekundární repliky a které naslouchá pouze na primárních replikách.

Například můžete mít ServiceRemotingListener, který přijímá volání RPC pouze v primárních replikách, a druhý vlastní naslouchací proces, který přebírá žádosti o čtení na sekundárních replikách přes protokol HTTP:

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
> Při vytváření více posluchačů pro službu **musí** být každému posluchači přiřazen jedinečný název.
>
>

Nakonec popište koncové body, které jsou požadovány pro službu v [manifestu služby](service-fabric-application-and-service-manifests.md) , v části u koncových bodů.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Naslouchací proces komunikace má přístup k prostředkům koncového bodu přiděleným z v `CodePackageActivationContext` `ServiceContext` . Naslouchací proces pak může začít naslouchat požadavkům při jeho otevření.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Prostředky koncového bodu jsou společné pro celý balíček služby a při aktivaci balíčku služby se jim přidělují Service Fabric. Stejný port může sdílet několik replik služby hostovaných ve stejné třídě ServiceHost. To znamená, že naslouchací proces komunikace by měl podporovat sdílení portů. Doporučeným způsobem je, aby naslouchací proces komunikace při generování adresy naslouchání použil ID oddílu a repliku nebo ID instance.
>
>

### <a name="service-address-registration"></a>Registrace adresy služby
Systémová služba s názvem *Naming Service* běží v clusterech Service Fabric. Naming Service je registrátorem služeb a jejich adres, na kterých každá instance nebo replika služby naslouchá. Po `OpenAsync(C#) / openAsync(Java)` dokončení metody se `ICommunicationListener(C#) / CommunicationListener(Java)` vrátí jeho návratová hodnota do Naming Service. Tato návratová hodnota, která je publikována v Naming Service, je řetězec, jehož hodnota může být libovolná. Tato řetězcová hodnota je to, co klienti uvidí, když žádají o adresu pro službu z Naming Service.

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

Service Fabric poskytuje rozhraní API, která klientům a dalším službám umožní požádat o tuto adresu podle názvu služby. To je důležité, protože adresa služby není statická. Služby se v clusteru pohybují pro účely vyrovnávání a dostupnosti prostředků. Toto je mechanismus, který umožňuje klientům přeložit adresu naslouchání pro službu.

> [!NOTE]
> Úplný návod, jak napsat naslouchací proces komunikace, najdete v tématu [Service Fabric služby webového rozhraní API pomocí samoobslužného hostování](./service-fabric-reliable-services-communication-aspnetcore.md) pro C#, zatímco pro Java můžete napsat vlastní implementaci HTTP serveru, viz příklad aplikace EchoServer na adrese https://github.com/Azure-Samples/service-fabric-java-getting-started .
>
>

## <a name="communicating-with-a-service"></a>Komunikace se službou
Rozhraní Reliable Services API poskytuje následující knihovny pro zápis klientů, kteří komunikují se službami.

### <a name="service-endpoint-resolution"></a>Řešení koncového bodu služby
Prvním krokem ke komunikaci se službou je překlad adresy koncového bodu oddílu nebo instance služby, se kterou chcete mluvit. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)`Třída Utility je základní primitivní, která pomáhá klientům určit koncový bod služby za běhu. V terminologii Service Fabric se proces určení koncového bodu služby označuje jako *řešení koncového bodu služby*.

Pokud se chcete připojit ke službám v rámci clusteru, můžete ServicePartitionResolver vytvořit pomocí výchozích nastavení. Toto je doporučené použití ve většině situací:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Pro připojení ke službám v jiném clusteru se dá ServicePartitionResolver vytvořit pomocí sady koncových bodů brány clusteru. Všimněte si, že koncové body brány jsou pouze různými koncovými body pro připojení ke stejnému clusteru. Například:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternativně je `ServicePartitionResolver` možné předávat funkci pro vytvoření `FabricClient` interního použití:

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

`FabricClient` je objekt, který se používá ke komunikaci s clusterem Service Fabric pro různé operace správy v clusteru. To je užitečné, pokud chcete mít větší kontrolu nad tím, jak překladač oddílů služby komunikuje s clusterem. `FabricClient` provádí ukládání do mezipaměti interně a je obecně nákladné pro vytvoření, takže je důležité znovu použít `FabricClient` instance co nejvíce.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Metoda řešení se pak použije k načtení adresy služby nebo oddílu služby pro rozdělené služby.

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

Adresu služby lze snadno vyřešit pomocí ServicePartitionResolver, ale k zajištění správného použití přeložené adresy se vyžaduje více práce. Váš klient potřebuje zjistit, jestli se pokus o připojení nezdařil z důvodu přechodné chyby a pokusit se o opakování (např. služba přesunula nebo je dočasně nedostupná), nebo trvalá chyba (např., že služba byla odstraněna nebo pokud už neexistuje požadovaný prostředek). Instance služby nebo repliky se v jednotlivých uzlech můžou kdykoli pohybovat z několika důvodů. Adresa služby vyřešená přes ServicePartitionResolver může být zastaralá o čas, kdy se klientský kód pokusí připojit. V takovém případě bude nutné znovu přeložit adresu klienta. Zadáním předchozí `ResolvedServicePartition` znamená, že překladač musí zkusit znovu místo pouhého načtení adresy v mezipaměti.

Kód klienta obvykle nemusí pracovat s ServicePartitionResolver přímo. Je vytvořen a předán do komunikačních továrn klienta v rozhraní Reliable Services API. Továrny používají překladač interně ke generování objektu klienta, který se dá použít ke komunikaci se službami.

### <a name="communication-clients-and-factories"></a>Komunikační klienti a továrny
Knihovna služby Communications Factory implementuje typický způsob opakování zpracování chyb, který usnadňuje opakované pokusy o připojení k vyřešeným koncovým bodům služby. Knihovna Factory poskytuje mechanismus opakování při poskytování obslužných rutin chyb.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` definuje základní rozhraní implementované objektem pro komunikaci klienta, které vytváří klienty, kteří můžou komunikovat se službou Service Fabric. Implementace CommunicationClientFactory závisí na komunikačním zásobníku používaném službou Service Fabric, kde chce klient komunikovat. Rozhraní Reliable Services API poskytuje `CommunicationClientFactoryBase<TCommunicationClient>` . To poskytuje základní implementaci rozhraní CommunicationClientFactory a provádí úlohy, které jsou společné pro všechny komunikační balíky. (Tyto úlohy zahrnují použití ServicePartitionResolver k určení koncového bodu služby). Klienti obvykle implementují abstraktní třídu CommunicationClientFactoryBase pro zpracování logiky, která je specifická pro komunikační zásobník.

Komunikační klient jenom obdrží adresu a použije ho pro připojení ke službě. Klient může použít libovolný protokol, který chce.

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

Klientská továrna je primárně zodpovědná za vytváření komunikačních klientů. U klientů, kteří neudržují trvalé připojení, jako je například klient HTTP, potřebuje továrnu vytvořit a vrátit klienta. Jiné protokoly, které udržují trvalé připojení, jako jsou například některé binární protokoly, by měly být také ověřeny `ValidateClient(string endpoint, MyCommunicationClient client)` továrnou, aby bylo možné určit, zda je nutné znovu vytvořit připojení.  

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

Nakonec je zodpovědná obslužná rutina výjimky za účelem určení, jakou akci chcete provést, když dojde k výjimce. Výjimky jsou zařazeny do kategorií, **které** lze **Opakovat** a nelze je opakovat.

* **Neopakující se výjimky jednoduše** vrátí zpět volajícímu.
* výjimky, které lze **Opakovat** , jsou dále zařazeny do **přechodného** a **nepřechodné**.
  * **Přechodné** výjimky jsou ty, které je možné jednoduše opakovat bez opakovaného překladu adresy koncového bodu služby. Budou zahrnovat přechodné síťové problémy nebo odpovědi na chyby služby jiné než ty, které naznačují, že adresa koncového bodu služby neexistuje.
  * **Nepřechodnými** výjimkami jsou ty, které vyžadují opakované vyřešení adresy koncového bodu služby. Patří mezi ně výjimky, které naznačují, že koncový bod služby není dostupný, což značí, že se služba přesunula na jiný uzel.

`TryHandleException`Provede rozhodnutí o dané výjimce. Pokud **nevíte** , jaká rozhodnutí mají být v souvislosti s výjimkou, měla by vrátit **false**. Pokud to **poznáte** , jaké rozhodnutí má učinit, měl by odpovídajícím způsobem nastavit výsledek a vrátit **hodnotu true**.

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
Pomocí rozhraní `ICommunicationClient(C#) / CommunicationClient(Java)` , `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` a `IExceptionHandler(C#) / ExceptionHandler(Java)` postaveného na komunikačním protokolu, `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` zabalí vše dohromady a poskytuje smyčku pro řešení chyb a adres oddílu služby.

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
* [ASP.NET Core s Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Vzdálená volání procedur s Reliable Services Vzdálená komunikace](service-fabric-reliable-services-communication-remoting.md)
* [Komunikace WCF pomocí Reliable Services](service-fabric-reliable-services-communication-wcf.md)
