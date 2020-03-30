---
title: Servisní komunikace s ASP.NET Core
description: Zjistěte, jak používat ASP.NET Core v bezstavových a stavových aplikacích spolehlivých služeb Azure Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639628"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET jádro ve spolehlivých službách Azure Service Fabric

ASP.NET Core je open-source a multiplatformní framework. Tento rámec je navržený pro vytváření cloudových aplikací připojených k internetu, jako jsou webové aplikace, aplikace IoT a mobilní back-endy.

Tento článek je podrobný mašlou k hostování ASP.NET základních služeb v Service Fabric reliable services pomocí **Microsoft.ServiceFabric.AspNetCore.** sada balíčků NuGet.

Úvodní kurz ASP.NET Core in Service Fabric a pokyny k nastavení vývojového prostředí najdete v [tématu Vytvoření a nasazení aplikace pomocí front-endové služby ASP.NET Core Web API a stavové back-endové služby](service-fabric-tutorial-create-dotnet-app.md).

Zbytek tohoto článku předpokládá, že jste již obeznámeni s ASP.NET Core. Pokud ne, přečtěte si [prosím základy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET core v prostředí Service Fabric

Aplikace ASP.NET Core i Service Fabric lze spustit v rozhraní .NET Core nebo v rámci .NET Framework. ASP.NET Core můžete použít dvěma různými způsoby v Service Fabric:
 - **Hostováno jako spustitelný soubor hosta**. Tento způsob se používá především ke spuštění existujících ASP.NET základní aplikace na Service Fabric bez změny kódu.
 - **Spusťte v rámci spolehlivé služby**. Tímto způsobem umožňuje lepší integraci s service fabric runtime a umožňuje stavové ASP.NET základní služby.

Zbývající část tohoto článku vysvětluje, jak používat ASP.NET Core uvnitř spolehlivé služby, a to prostřednictvím součásti integrace ASP.NET jádra, které jsou dodávány se sadou Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hosting služby Service Fabric

V service fabric, jeden nebo více instancí nebo repliky služby spustit v *procesu hostitele služby*: spustitelný soubor, který spouští kód služby. Vy jako autor služby vlastníte proces hostitele služby a Service Fabric jej aktivuje a monitoruje za vás.

Tradiční ASP.NET (až MVC 5) je pevně spojen se službou IIS prostřednictvím souboru System.Web.dll. ASP.NET Core poskytuje oddělení mezi webovým serverem a webovou aplikací. Toto oddělení umožňuje, aby webové aplikace byly přenosné mezi různými webovými servery. To také umožňuje webové servery, které mají být *self-hostované*. To znamená, že můžete spustit webový server ve vlastním procesu, na rozdíl od procesu, který je vlastněn dedikovaný software webového serveru, jako je například služba IIS.

Chcete-li kombinovat službu Service Fabric a ASP.NET, buď jako spustitelný soubor hosta nebo ve spolehlivé službě, musíte být schopni spustit ASP.NET uvnitř hostitelského procesu služby. ASP.NET Core self-hosting vám to umožní.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostování ASP.NET Core ve spolehlivé službě
Vlastní ASP.NET základní aplikace obvykle vytvářejí webhost v vstupním bodě aplikace, `static void Main()` jako `Program.cs`je například metoda v aplikaci . V tomto případě je životní cyklus WebHost vázán na životní cyklus procesu.

![Hostování ASP.NET jádra v procesu][0]

Vstupní bod aplikace však není tím pravým místem pro vytvoření WebHost ve spolehlivé službě. Je to proto, že vstupní bod aplikace se používá pouze k registraci typu služby s runtime Service Fabric, tak, aby mohl vytvořit instance tohoto typu služby. WebHost by měl být vytvořen ve spolehlivé službě samotné. V rámci procesu hostitele služby instance služby nebo repliky mohou projít více životními cykly. 

Instance Spolehlivé služby je reprezentována vaší `StatelessService` třídou služeb odvozenou z nebo `StatefulService`. Zásobník komunikace pro službu je `ICommunicationListener` obsažen v implementaci ve vaší třídě služeb. Balíčky `Microsoft.ServiceFabric.AspNetCore.*` NuGet obsahují `ICommunicationListener` implementace tohoto spuštění a správy ASP.NET Core WebHost pro Kestrel nebo HTTP.sys ve spolehlivé službě.

![Diagram pro hostování ASP.NET Core ve spolehlivé službě][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
Implementace `ICommunicationListener` pro Kestrel a HTTP.sys `Microsoft.ServiceFabric.AspNetCore.*` v Balíčcích NuGet mají podobné vzory použití. Ale provádějí mírně odlišné akce specifické pro každý webový server. 

Oba naslouchací procesy komunikace poskytují konstruktor, který má následující argumenty:
 - **`ServiceContext serviceContext`**: Toto `ServiceContext` je objekt, který obsahuje informace o spuštěné službě.
 - **`string endpointName`**: Toto je `Endpoint` název konfigurace v souboru ServiceManifest.xml. Je to především tam, kde se dva posluchači komunikace liší. HTTP.sys *requires* vyžaduje `Endpoint` konfiguraci, zatímco Kestrel ne.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Toto je lambda, které implementujete, ve kterém vytvoříte a vrátíte `IWebHost`. Umožňuje nakonfigurovat `IWebHost` způsob, jakým byste normálně v aplikaci ASP.NET Core. Lambda poskytuje adresu URL, která je generována pro vás, v závislosti `Endpoint` na možnosti integrace Service Fabric, které používáte a konfiguraci, kterou zadáte. Tuto adresu URL pak můžete upravit nebo použít ke spuštění webového serveru.

## <a name="service-fabric-integration-middleware"></a>Service Fabric integrace middleware
Balíček `Microsoft.ServiceFabric.AspNetCore` NuGet `UseServiceFabricIntegration` obsahuje metodu rozšíření, `IWebHostBuilder` která přidává middleware s vědomím Service Fabric. Tento middleware konfiguruje kestrel `ICommunicationListener` nebo HTTP.sys zaregistrovat jedinečnou adresu URL služby se službou Service Fabric Naming Service. Poté ověří požadavky klientů, aby bylo zajištěno, že se klienti připojují ke správné službě. 

Tento krok je nezbytný, aby se zabránilo klientům v chybném připojení k nesprávné službě. Je to proto, že v prostředí sdíleného hostitele, jako je Service Fabric, může být více webových aplikací spuštěno na stejném fyzickém nebo virtuálním počítači, ale nepoužívejte jedinečné názvy hostitelů. Tento scénář je popsán podrobněji v další části.

### <a name="a-case-of-mistaken-identity"></a>Případ záměny totožnosti
Repliky služby, bez ohledu na protokol, naslouchat na jedinečnou kombinaci IP:port. Jakmile replika služby začala naslouchat na koncovém bodu IP:port, hlásí tuto adresu koncového bodu službě Service Fabric Naming Service. Tam, klienti nebo jiné služby mohou objevit. Pokud služby používají dynamicky přiřazené porty aplikace, replika služby může shodou okolností použít stejný koncový bod IP:port jiné služby dříve na stejném fyzickém nebo virtuálním počítači. To může způsobit, že se klient omylem připojí k nesprávné službě. Tento scénář může mít za následek, pokud dojde k následující posloupnost událostí:

 1. Služba A naslouchá 10.0.0.1:30000 přes HTTP. 
 2. Klient vyřeší službu A a získá adresu 10.0.0.1:30000.
 3. Služba A se přesune do jiného uzlu.
 4. Služba B je umístěna na 10.0.0.1 a shodou okolností používá stejný port 30000.
 5. Klient se pokusí připojit ke službě A s adresou uloženou v mezipaměti 10.0.0.1:30000.
 6. Klient je nyní úspěšně připojen ke službě B, a nesiuvědomuje, že je připojen k nesprávné službě.

To může způsobit chyby v náhodných časech, které může být obtížné diagnostikovat.

### <a name="using-unique-service-urls"></a>Použití jedinečných adres URL služeb
Chcete-li zabránit těmto chybám, služby mohou zaúčtovat koncový bod do služby pojmenování s jedinečným identifikátorem a pak ověřit tento jedinečný identifikátor během požadavků klienta. Jedná se o kooperativní akce mezi službami v důvěryhodném prostředí bez nepřátelského klienta. Neposkytuje ověřování zabezpečené služby v prostředí nepřátelského klienta.

V důvěryhodném prostředí middleware, který je `UseServiceFabricIntegration` přidán metodou automaticky připojí jedinečný identifikátor na adresu zaúčtované do služby pojmenování. Ověří tento identifikátor na každém požadavku. Pokud se identifikátor neshoduje, middleware okamžitě vrátí odpověď HTTP 410 Gone.

Služby, které používají dynamicky přiřazený port, by měly využívat tento middleware.

Služby, které používají pevný jedinečný port nemají tento problém v kooperativním prostředí. Pevný jedinečný port se obvykle používá pro externě orientované služby, které potřebují známý port pro klientské aplikace pro připojení. Většina internetových webových aplikací bude například používat port 80 nebo 443 pro připojení webového prohlížeče. V takovém případě by neměl být povolen jedinečný identifikátor.

Následující diagram znázorňuje tok požadavků s povoleným middlewarem:

![Service Fabric ASP.NET integrace jádra][2]

Kestrel a HTTP.sys `ICommunicationListener` implementace použít tento mechanismus přesně stejným způsobem. Přestože soubor HTTP.sys může interně rozlišovat požadavky na základě jedinečných cest URL pomocí základní funkce sdílení **portů HTTP.sys,** tato funkce *není* implementací HTTP.sys `ICommunicationListener` využívána. Je to proto, že výsledkem jsou kódy chyb HTTP 503 a HTTP 404 ve scénáři popsaném výše. To zase ztěžuje klientům určit záměr chyby, jako HTTP 503 a HTTP 404 se běžně používají k označení jiných chyb. 

Proto kestrel a HTTP.sys `ICommunicationListener` implementace standardizovat na middleware `UseServiceFabricIntegration` poskytované metodou rozšíření. Klienti proto potřebují provést akci znovu vyřešit koncový bod služby na odpovědi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys ve spolehlivých službách
Soubor HTTP.sys můžete použít ve spolehlivých službách importem balíčku **Microsoft.ServiceFabric.AspNetCore.httpSys** NuGet. Tento balíček obsahuje `HttpSysCommunicationListener` `ICommunicationListener`provedení . `HttpSysCommunicationListener`umožňuje vytvořit ASP.NET Core WebHost uvnitř spolehlivé služby pomocí HTTP.sys jako webového serveru.

Soubor HTTP.sys je postaven na [rozhraní API systému Windows HTTP Server](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Toto rozhraní API používá ovladač jádra **HTTP.sys** ke zpracování požadavků HTTP a jejich směrování do procesů, které spouštějí webové aplikace. To umožňuje více procesů matného nebo virtuálního počítače hostovat webové aplikace na stejném portu, které jsou vyloučeny buď jedinečnou cestou url, nebo názvem hostitele. Tyto funkce jsou užitečné v Service Fabric pro hostování více webů ve stejném clusteru.

>[!NOTE]
>Implementace http.sys funguje pouze na platformě Windows.

Následující diagram znázorňuje, jak http.sys používá ovladač jádra **HTTP.sys** v systému Windows pro sdílení portů:

![Diagram HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys ve službě bez státní příslušnosti
Chcete-li použít `HttpSys` ve službě bez `CreateServiceInstanceListeners` stavů, `HttpSysCommunicationListener` přepsat metodu a vrátit instanci:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys ve stavové službě

`HttpSysCommunicationListener`Není aktuálně určen pro použití ve stavových službách z důvodu komplikací se základní funkcí sdílení **portů HTTP.sys.** Další informace naleznete v následující části o dynamickém přidělování portů pomocí protokolu HTTP.sys. Pro stavové služby, Kestrel je navrhovaný webový server.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Konfigurace `Endpoint` je vyžadována pro webové servery, které používají rozhraní API http serveru systému Windows, včetně rozhraní HTTP.sys. Webové servery, které používají rozhraní API pro systém Windows HTTP Server, musí nejprve rezervovat adresu URL pomocí souboru HTTP.sys (obvykle se tak provádí pomocí nástroje [netsh).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Tato akce vyžaduje zvýšená oprávnění, která vaše služby ve výchozím nastavení nemají. Možnosti "http" nebo "https" `Protocol` pro `Endpoint` vlastnost konfigurace v ServiceManifest.xml se používají konkrétně k pokynu service fabric runtime zaregistrovat adresu URL s HTTP.sys vaším jménem. Je to pomocí [*silné zástupné adresy*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL předpony.

Chcete-li například rezervovat `http://+:80` službu, použijte v souboru ServiceManifest.xml následující konfiguraci:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

A název koncového bodu musí `HttpSysCommunicationListener` být předán konstruktoru:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Použití souboru HTTP.sys se statickým portem
Chcete-li použít statický port se souborem HTTP.sys, zadejte číslo portu v konfiguraci: `Endpoint`

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Použití protokolu HTTP.sys s dynamickým portem
Chcete-li použít dynamicky přiřazený port s protokolem `Port` HTTP.sys, vyneche vlastnost v konfiguraci: `Endpoint`

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Dynamický port přidělený `Endpoint` konfigurací poskytuje pouze jeden port *na hostitelský proces*. Aktuální model hostování Service Fabric umožňuje více instancí služby nebo repliky, které mají být hostovány ve stejném procesu. To znamená, že každý z nich bude `Endpoint` sdílet stejný port při přidělení prostřednictvím konfigurace. Více instancí **HTTP.sys** může sdílet port pomocí základní funkce sdílení **portů HTTP.sys.** Ale to není podporováno `HttpSysCommunicationListener` kvůli komplikacím, které zavádí pro požadavky klientů. Pro dynamické využití portů je Kestrel navrhovaným webovým serverem.

## <a name="kestrel-in-reliable-services"></a>Poštolka ve spolehlivých službách
Kestrel můžete použít ve spolehlivých službách importem balíčku **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Tento balíček obsahuje `KestrelCommunicationListener` `ICommunicationListener`provedení . `KestrelCommunicationListener`umožňuje vytvořit ASP.NET Core WebHost uvnitř spolehlivé služby pomocí Kestrel jako webový server.

Kestrel je multiplatformní webový server pro ASP.NET Core. Na rozdíl od HTTP.sys Kestrel nepoužívá centralizovaný správce koncových bodů. Také na rozdíl od HTTP.sys, Kestrel nepodporuje sdílení portů mezi více procesy. Každá instance Kestrel musí používat jedinečný port. Další informace o Kestrel, naleznete [v podrobnosti implementace](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Kestrelův diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Poštolka ve službě bez státní příslušnosti
Chcete-li použít `Kestrel` ve službě bez `CreateServiceInstanceListeners` stavů, `KestrelCommunicationListener` přepsat metodu a vrátit instanci:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Poštolka ve stavové službě
Chcete-li použít `Kestrel` ve stavové `CreateServiceReplicaListeners` službě, přepište metodu a vraťte `KestrelCommunicationListener` instanci:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

V tomto příkladu `IReliableStateManager` je k dispozici instance singleton do kontejneru vkládání závislostí WebHost. To není nezbytně nutné, ale umožňuje `IReliableStateManager` používat a spolehlivé kolekce v metodách akce řadiče MVC.

Název `Endpoint` konfigurace *není* k `KestrelCommunicationListener` dispozici ve stavové službě. To je podrobněji vysvětleno v následující části.

### <a name="configure-kestrel-to-use-https"></a>Nakonfigurovat Kestrel k používání HTTPS
Při povolení protokolu HTTPS s kestrelem ve vaší službě budete muset nastavit několik možností naslouchání. Aktualizujte `ServiceInstanceListener` koncový *bod EndpointHttps* a poslouchejte na konkrétním portu (například port 443). Při konfiguraci webového hostitele pro použití webového serveru Kestrel je nutné nakonfigurovat kestrel tak, aby naslouchal adresám IPv6 ve všech síťových rozhraních: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Úplný příklad v kurzu najdete v [tématu Konfigurace Kestrel používat HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfigurace koncového bodu
Konfigurace `Endpoint` není nutné používat Kestrel. 

Kestrel je jednoduchý samostatný webový server. Na rozdíl od HTTP.sys (nebo HttpListener), nepotřebuje `Endpoint` konfiguraci v ServiceManifest.xml, protože nevyžaduje registraci adresy URL před spuštěním. 

#### <a name="use-kestrel-with-a-static-port"></a>Použití Kestrelu se statickým portem
Statický port můžete nakonfigurovat `Endpoint` v konfiguraci ServiceManifest.xml pro použití s kestrelem. I když to není nezbytně nutné, nabízí dvě potenciální výhody:
 - Pokud port nespadá do rozsahu portů aplikace, otevře se přes bránu firewall operačního systému service fabric.
 - Adresa URL, která `KestrelCommunicationListener` vám byla poskytnuta prostřednictvím, bude používat tento port.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Pokud `Endpoint` je nakonfigurován, jeho název `KestrelCommunicationListener` musí být předán do konstruktoru: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Pokud ServiceManifest.xml nepoužívá `Endpoint` konfiguraci, vynechejte `KestrelCommunicationListener` název v konstruktoru. V takovém případě bude používat dynamický port. Další informace naleznete v další části.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Použití Kestrelu s dynamickým portem
Kestrel nemůže použít automatické přiřazení portu z konfigurace v souboru `Endpoint` ServiceManifest.xml. Je to proto, že `Endpoint` automatické přiřazení portu z konfigurace přiřadí jedinečný port na *hostitelský proces*a jeden hostitelský proces může obsahovat více instancí Kestrel. To nefunguje s Kestrel, protože nepodporuje sdílení portů. Proto musí být každá instance Kestrel otevřena na jedinečném portu.

Chcete-li použít dynamické přiřazení portu s `Endpoint` Kestrel, vynechat konfiguraci v ServiceManifest.xml úplně `KestrelCommunicationListener` a nepředávat název koncového bodu konstruktoru, takto:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

V této `KestrelCommunicationListener` konfiguraci automaticky vybere nepoužívaný port z rozsahu portů aplikace.

Pro protokol HTTPS by měl mít koncový bod nakonfigurovaný protokolem HTTPS bez portu určeného v souboru ServiceManifest.xml a předat název koncového bodu konstruktoru KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Zprostředkovatel konfigurace service fabric
Konfigurace aplikace v ASP.NET Core je založena na párech klíč-hodnota vytvořené poskytovatelem konfigurace. Přečtěte si [konfiguraci v ASP.NET Core,](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) abyste lépe porozuměli obecné podpoře konfigurace ASP.NET Core.

Tato část popisuje, jak zprostředkovatel konfigurace Service Fabric integruje s konfigurací ASP.NET Core importem balíčku `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricKonfigurace spouštěcích rozšíření
Po importu `Microsoft.ServiceFabric.AspNetCore.Configuration` balíčku NuGet je třeba zaregistrovat zdroj konfigurace prostředků infrastruktury služeb pomocí rozhraní API konfigurace ASP.NET jádra. To provést kontrolou **AddServiceFabricConfiguration** rozšíření v `Microsoft.ServiceFabric.AspNetCore.Configuration` oboru `IConfigurationBuilder`názvů proti .

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Nyní ASP.NET služby Core přístup k nastavení konfigurace Service Fabric, stejně jako jakékoli jiné nastavení aplikace. Můžete například použít vzorek voleb k načtení nastavení do objektů silného typu.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Výchozí mapování klíčů
Ve výchozím nastavení zahrnuje zprostředkovatel konfigurace Service Fabric název balíčku, název oddílu a název vlastnosti. Společně tvoří konfigurační klíč ASP.NET jádra takto:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Pokud máte například konfigurační balíček pojmenovaný `MyConfigPackage` s následujícím obsahem, `IConfiguration` bude hodnota konfigurace k dispozici na ASP.NET Core prostřednictvím *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Možnosti konfigurace service fabric
Zprostředkovatel konfigurace Service Fabric `ServiceFabricConfigurationOptions` také podporuje změnit výchozí chování mapování klíčů.

#### <a name="encrypted-settings"></a>Šifrovaná nastavení
Service Fabric podporuje šifrovaná nastavení, stejně jako zprostředkovatel konfigurace Service Fabric. Šifrovaná nastavení nejsou dešifrována, aby ASP.NET Core ve `IConfiguration` výchozím nastavení. Místo toho jsou zde uloženy šifrované hodnoty. Ale pokud chcete dešifrovat hodnotu pro uložení v ASP.NET Core IConfiguration, `AddServiceFabricConfiguration` můžete nastavit *příznak DecryptValue* na false v rozšíření, takto:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Více konfiguračních balíčků
Service Fabric podporuje více konfiguračních balíčků. Ve výchozím nastavení je název balíčku součástí konfiguračního klíče. Ale můžete nastavit `IncludePackageName` příznak na false, takto:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Vlastní mapování klíčů, extrakce hodnot a plnění dat
Zprostředkovatel konfigurace Service Fabric také podporuje pokročilejší scénáře přizpůsobit `ExtractKeyFunc` mapování klíčů s `ExtractValueFunc`a vlastní extrahovat hodnoty s . Můžete dokonce změnit celý proces naplnění dat z konfigurace Service Fabric `ConfigAction`na ASP.NET konfiguraci jádra pomocí .

Následující příklady ilustrují použití `ConfigAction` k přizpůsobení populace dat:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Aktualizace konfigurace
Zprostředkovatel konfigurace Service Fabric také podporuje aktualizace konfigurace. Můžete použít ASP.NET `IOptionsMonitor` Core přijímat oznámení o `IOptionsSnapshot` změnách a pak použít k opětovnému načtení konfiguračních dat. Další informace naleznete [v tématu ASP.NET možnosti jádra](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Tyto možnosti jsou ve výchozím nastavení podporovány. K povolení aktualizací konfigurace není potřeba žádné další kódování.

## <a name="scenarios-and-configurations"></a>Scénáře a konfigurace
Tato část obsahuje kombinaci webového serveru, konfigurace portu, možností integrace Service Fabric a různých nastavení, které doporučujeme k řešení následujících scénářů:
 - Externě vystavené ASP.NET základní služby bez stavů
 - Služby bez stavové služby pouze pro interní služby ASP.NET Core
 - Pouze interní ASP.NET základní stavové služby

**Externě exponovaná služba** je služba, která zveřejňuje koncový bod, který je volán z mimo clusteru, obvykle prostřednictvím systému vyrovnávání zatížení.

Interní **služba** je služba, jejíž koncový bod je volána pouze z clusteru.

> [!NOTE]
> Koncové body stavové služby by obecně neměly být vystaveny internetu. Clustery za vyrovnávání zatížení, které nevědí o řešení služby Service Fabric, jako je například Azure Load Balancer, nebude moci vystavit stavové služby. Je to proto, že vykladač zatížení nebude moci vyhledat a směrovat provoz do příslušné repliky stavové služby. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externě vystavené ASP.NET základní služby bez stavů
Kestrel je navrhovaný webový server pro front-endové služby, které zveřejňují externí koncové body HTTP orientované na internet. V systému Windows může soubor HTTP.sys poskytovat možnost sdílení portů, což umožňuje hostovat více webových služeb na stejné sadě uzlů pomocí stejného portu. V tomto scénáři jsou webové služby rozlišeny podle názvu hostitele nebo cesty, aniž by se spoléhaly na front-endproxy nebo bránu, které poskytují směrování HTTP.
 
Při vystavení internetu by služba bez stavů měla používat známý a stabilní koncový bod, který je dostupný prostřednictvím vykladače zatížení. Tuto adresu URL poskytnete uživatelům aplikace. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | Kestrel je preferovaný webový server, protože je podporován v systémech Windows a Linux. |
| Konfigurace portu | static | Známý statický port by měl být `Endpoints` nakonfigurován v konfiguraci ServiceManifest.xml, například 80 pro HTTP nebo 443 pro protokol HTTPS. |
| ServiceFabricIntegrationMožnosti | Žádný | Použijte `ServiceFabricIntegrationOptions.None` možnost při konfiguraci middleware integrace Service Fabric tak, aby služba nepokouší ověřit příchozí požadavky na jedinečný identifikátor. Externí uživatelé aplikace nebudou znát jedinečné identifikační informace, které middleware používá. |
| Počet instancí | -1 | V typických případech použití by mělo být nastaveno nastavení počtu instancí na *-1*. To se provádí tak, aby instance je k dispozici na všech uzlech, které přijímají provoz z vykladače zatížení. |

Pokud více externě exponovaných služeb sdílí stejnou sadu uzlů, můžete použít http.sys s jedinečnou, ale stabilní adresou URL. Toho lze dosáhnout úpravou adresy URL poskytnuté při konfiguraci iWebHost. Všimněte si, že to platí pouze pro HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Služba ASP.NET Core bez stavů pouze bez provozu
Bezstavové služby, které jsou volány pouze z clusteru, by měly používat jedinečné adresy URL a dynamicky přiřazené porty k zajištění spolupráce mezi více službami. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | Přestože můžete použít http.sys pro interní bezstavové služby, Kestrel je nejlepší server povolit více instancí služby ke sdílení hostitele.  |
| Konfigurace portu | dynamicky přiřazeno | Více replik stavové služby může sdílet hostitelský proces nebo hostitelský operační systém, a proto bude potřebovat jedinečné porty. |
| ServiceFabricIntegrationMožnosti | UseUniqueServiceUrl | Při dynamickém přiřazení portu toto nastavení zabraňuje výše popsanému problému s chybnou identitou. |
| Počet instancí | jakékoli | Nastavení počtu instancí lze nastavit na libovolnou hodnotu potřebnou k provozu služby. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Pouze interní stavová ASP.NET základní služba
Stavové služby, které jsou volány pouze z clusteru, by měly používat dynamicky přiřazené porty k zajištění spolupráce mezi více službami. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | Není `HttpSysCommunicationListener` určen pro použití stavové služby, ve kterém repliky sdílet hostitelský proces. |
| Konfigurace portu | dynamicky přiřazeno | Více replik stavové služby může sdílet hostitelský proces nebo hostitelský operační systém, a proto bude potřebovat jedinečné porty. |
| ServiceFabricIntegrationMožnosti | UseUniqueServiceUrl | Při dynamickém přiřazení portu toto nastavení zabraňuje výše popsanému problému s chybnou identitou. |

## <a name="next-steps"></a>Další kroky
[Ladění aplikace Service Fabric pomocí Visual Studia](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
