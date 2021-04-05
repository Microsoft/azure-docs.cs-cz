---
title: Komunikace služby s ASP.NET Core
description: Naučte se používat ASP.NET Core v bezstavových a stavových aplikacích Azure Service Fabric Reliable Services.
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: a125c6a1972b51f518175a4c69248119f71ada7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791590"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core v Azure Service Fabric Reliable Services

ASP.NET Core je open source architektura pro různé platformy. Tato architektura je určená pro vytváření cloudových aplikací připojených k Internetu, jako jsou webové aplikace, aplikace IoT a mobilní back-endy.

Tento článek je podrobným průvodcem pro hostování ASP.NET Core Services v Service Fabric Reliable Services pomocí **Microsoft. ServiceFabric. AspNetCore.** sada balíčků NuGet.

Úvodní kurz o ASP.NET Core v Service Fabric a pokyny k nastavení vývojového prostředí najdete v tématu [kurz: vytvoření a nasazení aplikace s front-end službou ASP.NET Core Web API a stavovou back-end službou](service-fabric-tutorial-create-dotnet-app.md).

Ve zbývající části tohoto článku se předpokládá, že už jste obeznámeni s ASP.NET Core. Pokud ne, přečtěte si prosím [základy ASP.NET Core](/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core v prostředí Service Fabric

Aplikace ASP.NET Core i Service Fabric mohou běžet v rozhraní .NET Core nebo v plném .NET Framework. V Service Fabric můžete použít ASP.NET Core dvěma různými způsoby:
 - **Hostuje se jako spustitelný soubor hosta**. Tímto způsobem se primárně používá ke spouštění existujících aplikací ASP.NET Core v Service Fabric bez jakýchkoli změn kódu.
 - **Spouštějte v rámci spolehlivé služby**. Tímto způsobem umožníte lepší integraci s modulem runtime Service Fabric a povolíte stavové služby ASP.NET Core.

Ve zbývající části tohoto článku se dozvíte, jak používat ASP.NET Core v rámci spolehlivé služby prostřednictvím komponent ASP.NET Core Integration, které se dodávají s Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hostování služby Service Fabric

V Service Fabric jedna nebo víc instancí a replik služby běží v *procesu hostitele služby*: spustitelný soubor, který spouští váš kód služby. Vy, jako autor služby, vlastní hostitelský proces služby a Service Fabric ho aktivovat a monitorovat za vás.

Tradiční ASP.NET (až do MVC 5) je těsně spojená se službou IIS prostřednictvím System.Web.dll. ASP.NET Core poskytuje oddělení mezi webovým serverem a webovou aplikací. Díky tomuto oddělení můžou být webové aplikace přenosné mezi různými webovými servery. Umožňuje taky *samoobslužné hostování* webových serverů. To znamená, že můžete spustit webový server ve vlastním procesu, a to na rozdíl od procesu, který je vlastněn vyhrazeným softwarem webového serveru, jako je IIS.

Aby bylo možné kombinovat službu Service Fabric a ASP.NET, a to buď jako spustitelný soubor hosta, nebo ve spolehlivé službě, musíte být schopni spustit ASP.NET v rámci procesu hostitele služby. Tato funkce umožňuje samoobslužné hostování ASP.NET Core.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostování ASP.NET Core ve spolehlivé službě
Obvykle aplikace ASP.NET Core v místním prostředí vytvářejí webhosta v vstupním bodu aplikace, jako je například `static void Main()` metoda v `Program.cs` . V tomto případě je životní cyklus webhost svázán s životním cyklem procesu.

![Hostování ASP.NET Core v procesu][0]

Vstupní bod aplikace ale není správné místo pro vytvoření Webhostu ve spolehlivé službě. To je proto, že se vstupní bod aplikace používá pouze k registraci typu služby s modulem Service Fabric runtime, aby mohl vytvořit instance daného typu služby. Webhost by měl být vytvořen v samotné spolehlivé službě. V rámci procesu hostitele služby můžou instance služby nebo repliky procházet několika životními cykly. 

Spolehlivá instance služby je reprezentovaná vaší třídou služby odvozenou z `StatelessService` nebo `StatefulService` . Komunikační zásobník pro službu je obsažen v `ICommunicationListener` implementaci ve vaší třídě služby. `Microsoft.ServiceFabric.AspNetCore.*`Balíčky NuGet obsahují implementace `ICommunicationListener` , které spouští a spravují ASP.NET Core webhost pro Kestrel nebo HTTP.sys ve spolehlivé službě.

![Diagram pro hostování ASP.NET Core ve spolehlivé službě][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener`Implementace pro Kestrel a HTTP.sys v `Microsoft.ServiceFabric.AspNetCore.*` balíčcích NuGet mají podobné vzory použití. Ale provádějí mírně různé akce specifické pro každý webový server. 

Oba naslouchací procesy komunikace poskytují konstruktor, který přijímá následující argumenty:
 - **`ServiceContext serviceContext`**: Toto je `ServiceContext` objekt, který obsahuje informace o běžící službě.
 - **`string endpointName`**: Jedná se o název `Endpoint` konfigurace v ServiceManifest.xml. Primárně se dva komunikační naslouchací procesy liší. HTTP.sys *vyžaduje* `Endpoint` konfiguraci, zatímco Kestrel ne.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Toto je lambda, kterou implementujete, ve kterém vytvoříte a vrátíte `IWebHost` . Umožňuje nakonfigurovat `IWebHost` způsob, jakým normálně ASP.NET Core aplikaci. Lambda poskytuje adresu URL, která je vygenerována v závislosti na možnostech Service Fabric integrace, které používáte, a na `Endpoint` konfiguraci, kterou zadáte. Tuto adresu URL pak můžete změnit nebo použít ke spuštění webového serveru.

## <a name="service-fabric-integration-middleware"></a>Middleware pro integraci Service Fabric
`Microsoft.ServiceFabric.AspNetCore`Balíček NuGet zahrnuje `UseServiceFabricIntegration` metodu rozšíření pro `IWebHostBuilder` , která přidává middleware s podporou Service Fabric. Tento middleware nakonfiguruje Kestrel nebo HTTP.sys `ICommunicationListener` k registraci jedinečné adresy URL služby pomocí Naming Service Service Fabric. Potom ověří požadavky klienta, aby se klienti připojovali ke správné službě. 

Tento krok je nezbytný k tomu, aby se klienti nemohli omylem připojit k nesprávné službě. To je proto, že v prostředí Shared-Host, jako je například Service Fabric, může běžet více webových aplikací na stejném fyzickém nebo virtuálním počítači, ale nepoužívá jedinečné názvy hostitelů. Tento scénář je podrobněji popsán v následující části.

### <a name="a-case-of-mistaken-identity"></a>Případ chybné identity
Repliky služby, bez ohledu na protokol, naslouchají na jedinečné IP adrese: kombinace portů. Jakmile replika služby začne naslouchat na IP adrese: koncový bod portu, oznamuje, že adresa koncového bodu je Naming Service Service Fabric. V takovém případě je mohou zjistit klienti nebo jiné služby. Pokud služby používají dynamicky přiřazené porty aplikací, může replika služby ve stejném fyzickém nebo virtuálním počítači využívala stejnou IP adresu jako koncový bod portu jiné služby dřív. To může způsobit, že se klient omylem připojí k nesprávné službě. K tomuto scénáři může dojít, pokud dojde k následující sekvenci událostí:

 1. Služba A naslouchá na 10.0.0.1:30000 přes HTTP. 
 2. Klient přeloží službu a a získá adresu 10.0.0.1:30000.
 3. Služba A se přesune na jiný uzel.
 4. Služba B je umístěná na 10.0.0.1 a spoluincidentu používá stejný port 30000.
 5. Klient se pokusí připojit ke službě A s adresou v mezipaměti 10.0.0.1:30000.
 6. Klient je nyní úspěšně připojen ke službě B, nikoli při jeho invázání do nesprávné služby.

To může způsobit chyby v náhodných časech, které může být obtížné diagnostikovat.

### <a name="using-unique-service-urls"></a>Použití jedinečných adres URL služby
Chcete-li zabránit těmto chybám, mohou služby odeslat koncový bod do Naming Service s jedinečným identifikátorem a ověřit jedinečný identifikátor během požadavků klientů. Jedná se o spolupráci mezi službami v nepřátelských důvěryhodných klientech. Neposkytuje zabezpečené ověřování pomocí služby v nepřátelských prostředích klienta.

V důvěryhodném prostředí middleware, který je přidán metodou, `UseServiceFabricIntegration` automaticky připojí jedinečný identifikátor k adrese odeslané do Naming Service. Ověřuje tento identifikátor u každé žádosti. Pokud identifikátor neodpovídá, middleware okamžitě vrátí odpověď HTTP 410, která zmizela.

Služby, které používají dynamicky přiřazený port, by měly používat tento middleware.

Služby, které používají pevný jedinečný port, nemají tento problém v prostředí pro spolupráci. Pevný jedinečný port se obvykle používá pro externě orientované služby, které potřebují dobře známý port pro klientské aplikace pro připojení k. Například většina webových aplikací s přístupem k Internetu bude pro připojení webového prohlížeče používat port 80 nebo 443. V takovém případě by se měl jedinečný identifikátor povolit.

Následující diagram znázorňuje tok požadavků s povoleným middlewarem:

![Integrace ASP.NET Core Service Fabric][2]

Implementace Kestrel i HTTP.sys `ICommunicationListener` používají tento mechanismus přesně stejným způsobem. I když HTTP.sys může interně odlišit požadavky na základě jedinečných cest URL pomocí základní funkce pro sdílení portů **HTTP.sys** , tato funkce *se nepoužívá při* `ICommunicationListener` implementaci HTTP.sys. Důvodem je to, že ve výše popsaném scénáři má za následek stavové kódy chyb HTTP 503 a HTTP 404. Díky tomu je obtížné, aby klienti zjistili záměr této chyby, protože HTTP 503 a HTTP 404 se často používají k indikaci dalších chyb. 

Proto implementace Kestrel i HTTP.sys `ICommunicationListener` standardizaci pro middleware poskytované `UseServiceFabricIntegration` metodou rozšíření. Proto klienti musí provést akci opětovného překladu koncového bodu služby na odpovědích HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys v Reliable Services
Pomocí HTTP.sys můžete v Reliable Services importovat balíček NuGet **Microsoft. ServiceFabric. AspNetCore. HttpSys** . Tento balíček obsahuje `HttpSysCommunicationListener` implementaci `ICommunicationListener` . `HttpSysCommunicationListener` umožňuje vytvořit ASP.NET Core webhost v rámci spolehlivé služby pomocí HTTP.sys jako webového serveru.

HTTP.sys je postaven na [rozhraní API Windows HTTP serveru](/windows/win32/http/http-api-start-page). Toto rozhraní API používá **HTTP.sys** ovladač jádra ke zpracování požadavků HTTP a jejich směrování do procesů, které spouštějí webové aplikace. To umožňuje více procesů na stejném fyzickém nebo virtuálním počítači hostovat webové aplikace na stejném portu, a to v rámci jedinečné cesty URL nebo názvu hostitele. Tyto funkce jsou užitečné v Service Fabric pro hostování více webů ve stejném clusteru.

>[!NOTE]
>HTTP.sys implementace funguje pouze na platformě Windows.

Následující diagram znázorňuje, jak HTTP.sys používá **HTTP.sys** ovladač jádra ve Windows pro sdílení portů:

![Diagram HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys v bezstavové službě
Chcete-li použít `HttpSys` v bezstavové službě, přepište `CreateServiceInstanceListeners` metodu a vraťte `HttpSysCommunicationListener` instanci:

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

`HttpSysCommunicationListener` Služba není aktuálně navržena pro použití ve stavových službách z důvodu komplikací se základní funkcí sdílení portů **HTTP.sys** . Další informace najdete v následující části věnované dynamickému přidělování portů s HTTP.sys. Pro stavové služby je Kestrel doporučeným webovým serverem.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu

`Endpoint`Pro webové servery, které používají rozhraní Windows HTTP Server API, včetně HTTP.sys, je vyžadována konfigurace. Webové servery, které používají rozhraní API systému Windows HTTP server, musí nejprve rezervovat svou adresu URL pomocí HTTP.sys (to se obvykle provádí pomocí nástroje [netsh](/windows/win32/http/netsh-commands-for-http) ). 

Tato akce vyžaduje zvýšená oprávnění, která vaše služby ve výchozím nastavení nemají. Možnosti "http" nebo "https" pro `Protocol` vlastnost `Endpoint` konfigurace v ServiceManifest.xml jsou používány konkrétně k tomu, aby modul runtime Service Fabric mohl zaregistrovat adresu URL HTTP.sys vaším jménem. Používá předponu adresy URL se [*silným zástupným znakem*](/windows/win32/http/urlprefix-strings) .

Chcete-li například vyhradit `http://+:80` službu, použijte v ServiceManifest.xml následující konfiguraci:

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

A název koncového bodu musí být předán `HttpSysCommunicationListener` konstruktoru:

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

#### <a name="use-httpsys-with-a-static-port"></a>Použití HTTP.sys se statickým portem
Pokud chcete použít statický port s HTTP.sys, zadejte číslo portu v `Endpoint` konfiguraci:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Použití HTTP.sys s dynamickým portem
Pokud chcete použít dynamicky přiřazený port s HTTP.sys, vynechejte `Port` vlastnost v `Endpoint` konfiguraci:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Dynamický port přidělený `Endpoint` konfigurací poskytuje pouze jeden port *na proces hostitele*. Aktuální Service Fabric hostující model umožňuje hostování více instancí služby a/nebo replik v rámci stejného procesu. To znamená, že každá z nich bude při přidělování prostřednictvím konfigurace sdílet stejný port `Endpoint` . Více instancí **HTTP.sys** může sdílet port pomocí základní funkce sdílení portů **HTTP.sys** . Není to ale podporováno `HttpSysCommunicationListener` z důvodu komplikací, které zavádí pro požadavky klientů. V případě použití dynamického portu je Kestrel navržený webový server.

## <a name="kestrel-in-reliable-services"></a>Kestrel v Reliable Services
Pomocí Kestrel můžete v Reliable Services importovat balíček NuGet **Microsoft. ServiceFabric. AspNetCore. Kestrel** . Tento balíček obsahuje `KestrelCommunicationListener` implementaci `ICommunicationListener` . `KestrelCommunicationListener` umožňuje vytvořit ASP.NET Core webhost v rámci spolehlivé služby pomocí Kestrel jako webového serveru.

Kestrel je webový server pro různé platformy pro ASP.NET Core. Na rozdíl od HTTP.sys Kestrel nepoužívá centralizovaného správce koncových bodů. Kestrel také na rozdíl od HTTP.sys nepodporuje sdílení portů mezi několika procesy. Každá instance Kestrel musí používat jedinečný port. Další informace o Kestrel najdete v [podrobnostech implementace](/aspnet/core/fundamentals/servers/kestrel).

![Diagram Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel nestavové služby
Chcete-li použít `Kestrel` v bezstavové službě, přepište `CreateServiceInstanceListeners` metodu a vraťte `KestrelCommunicationListener` instanci:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel ve stavové službě
Chcete-li použít `Kestrel` ve stavové službě, přepište `CreateServiceReplicaListeners` metodu a vraťte `KestrelCommunicationListener` instanci:

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

V tomto příkladu je instance typu Singleton `IReliableStateManager` k dispozici pro kontejner vkládání závislostí webhost. To není nezbytně nutné, ale umožňuje používat `IReliableStateManager` a spolehlivé kolekce v metodách akcí KONTROLERU MVC.

`Endpoint`Ve stavové službě *není* zadaný název konfigurace `KestrelCommunicationListener` . To je podrobněji vysvětleno v následující části.

### <a name="configure-kestrel-to-use-https"></a>Nakonfigurovat Kestrel k používání HTTPS
Když v rámci služby povolíte HTTPS s Kestrel, budete muset nastavit několik možností naslouchání. Aktualizujte, `ServiceInstanceListener` aby používaly koncový bod *EndpointHttps* a naslouchaly na konkrétním portu (například port 443). Když nakonfigurujete webového hostitele tak, aby používal webový server Kestrel, musíte nakonfigurovat Kestrel na naslouchání adresám IPv6 na všech síťových rozhraních: 

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

Úplný příklad v kurzu najdete v tématu [Konfigurace Kestrel pro použití protokolu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfigurace koncového bodu
`Endpoint`Pro použití Kestrel není nutná konfigurace. 

Kestrel je jednoduchý samostatný webový server. Na rozdíl od HTTP.sys (nebo HttpListener) nevyžaduje `Endpoint` v ServiceManifest.xml konfiguraci, protože před zahájením nevyžaduje registraci adresy URL. 

#### <a name="use-kestrel-with-a-static-port"></a>Použití Kestrel se statickým portem
V konfiguraci ServiceManifest.xml můžete nakonfigurovat statický port `Endpoint` pro použití s Kestrel. I když to není nezbytně nutné, nabízí dvě možné výhody:
 - Pokud port nepadne do rozsahu portů aplikace, je otevřen pomocí brány firewall operačního systému pomocí Service Fabric.
 - Adresa URL, kterou jste dostali za vás, `KestrelCommunicationListener` použije tento port.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Pokud `Endpoint` je nakonfigurováno, musí se jeho název předat do `KestrelCommunicationListener` konstruktoru: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Pokud ServiceManifest.xml nepoužívá `Endpoint` konfiguraci, vynechejte název v `KestrelCommunicationListener` konstruktoru. V takovém případě bude používat dynamický port. Další informace najdete v další části.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Použití Kestrel s dynamickým portem
Kestrel nemůže použít automatické přiřazování portů z `Endpoint` konfigurace v ServiceManifest.xml. Důvodem je to, že automatické přiřazování portů z `Endpoint` konfigurace přiřadí jedinečný port pro každý *hostitelský proces* a jeden hostitelský proces může obsahovat několik instancí Kestrel. Nefunguje s Kestrel, protože nepodporuje sdílení portů. Proto musí být každá instance Kestrel otevřená na jedinečném portu.

Chcete-li použít dynamické přiřazování portů s Kestrel, vynechejte `Endpoint` konfiguraci v ServiceManifest.xml zcela a nepředávejte do konstruktoru název koncového bodu `KestrelCommunicationListener` , a to následujícím způsobem:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

V této konfiguraci `KestrelCommunicationListener` bude automaticky vybrán nepoužitý port z rozsahu portů aplikace.

V případě protokolu HTTPS by měl mít koncový bod nakonfigurovaný s protokolem HTTPS bez portu zadaného v ServiceManifest.xml a předá název koncového bodu konstruktoru KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Poskytovatel konfigurace Service Fabric
Konfigurace aplikací v ASP.NET Core vychází z párů klíč-hodnota, které vytvořil Poskytovatel konfigurace. Pokud chcete získat další informace o obecné podpoře konfigurace ASP.NET Core, přečtěte si téma [konfigurace v ASP.NET Core](/aspnet/core/fundamentals/configuration/) .

Tato část popisuje, jak se poskytovatel konfigurace Service Fabric integruje s konfigurací ASP.NET Core importem `Microsoft.ServiceFabric.AspNetCore.Configuration` balíčku NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration – spouštěcí rozšíření
Po importu `Microsoft.ServiceFabric.AspNetCore.Configuration` balíčku NuGet musíte zaregistrovat zdroj konfigurace Service Fabric pomocí rozhraní API pro ASP.NET Core konfigurace. Provedete to tak, že zkontrolujete rozšíření **AddServiceFabricConfiguration** v `Microsoft.ServiceFabric.AspNetCore.Configuration` oboru názvů `IConfigurationBuilder` .

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

Nyní může služba ASP.NET Core získat přístup k nastavení konfigurace Service Fabric stejným způsobem jako jakékoli jiné nastavení aplikace. Například můžete použít vzor možností pro načtení nastavení do objektů silně typovaného typu.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Výchozí mapování klíče
Ve výchozím nastavení zahrnuje Poskytovatel konfigurace Service Fabric název balíčku, název oddílu a název vlastnosti. Dohromady tyto ASP.NET Core konfigurační klíč, a to následujícím způsobem:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Pokud máte například konfigurační balíček s názvem `MyConfigPackage` s následujícím obsahem, bude hodnota konfigurace k dispozici na ASP.NET Core `IConfiguration` do *MyConfigPackage: MyConfigSection: MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Možnosti konfigurace Service Fabric
Poskytovatel konfigurace Service Fabric také podporuje `ServiceFabricConfigurationOptions` změnu výchozího chování mapování klíčů.

#### <a name="encrypted-settings"></a>Šifrované nastavení
Service Fabric podporuje šifrované nastavení, stejně jako poskytovatel konfigurace Service Fabric. Zašifrovaná nastavení nejsou ve výchozím nastavení dešifrována ASP.NET Core `IConfiguration` . Místo toho jsou uloženy šifrované hodnoty. Pokud ale chcete hodnotu dešifrovat pro ukládání do ASP.NET Core IConfiguration, můžete nastavit příznak *DecryptValue* na hodnotu false v `AddServiceFabricConfiguration` rozšíření následujícím způsobem:

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
Service Fabric podporuje více konfiguračních balíčků. Ve výchozím nastavení je název balíčku součástí konfiguračního klíče. Příznak ale můžete nastavit `IncludePackageName` na false následujícím způsobem:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapování vlastních klíčů, extrakce hodnot a naplnění dat
Poskytovatel konfigurace Service Fabric také podporuje pokročilejší scénáře pro přizpůsobení mapování klíče pomocí `ExtractKeyFunc` a vlastní extrakci hodnot pomocí `ExtractValueFunc` . Můžete dokonce změnit celý proces naplnění dat z konfigurace Service Fabric na ASP.NET Core konfiguraci pomocí `ConfigAction` .

Následující příklady ilustrují, jak použít `ConfigAction` k přizpůsobení datových napopulace:
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
Poskytovatel konfigurace Service Fabric podporuje taky aktualizace konfigurace. `IOptionsMonitor`K přijímání oznámení o změnách můžete použít ASP.NET Core a potom použít `IOptionsSnapshot` k opětovnému načtení konfiguračních dat. Další informace najdete v tématu [možnosti ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

Ve výchozím nastavení jsou tyto možnosti podporovány. K povolení aktualizací konfigurace není potřeba žádné další kódování.

## <a name="scenarios-and-configurations"></a>Scénáře a konfigurace
V této části najdete kombinaci možností webový server, konfigurace portů, Service Fabric možnosti integrace a různá nastavení, která doporučujeme řešit v následujících scénářích:
 - Externě exponované ASP.NET Core bezstavové služby
 - Jenom interní ASP.NET Core bezstavové služby
 - Jenom interní ASP.NET Core stavové služby

**Externě vystavená služba** je ta, která zveřejňuje koncový bod, který se volá mimo cluster, obvykle prostřednictvím nástroje pro vyrovnávání zatížení.

**Jenom interní** služba je taková, jejíž koncový bod se volá jenom v rámci clusteru.

> [!NOTE]
> Koncové body stavové služby by obecně neměly být vystaveny Internetu. Clustery na základě nástrojů pro vyrovnávání zatížení, které neznají Service Fabric překlad služeb, jako je například Azure Load Balancer, nebudou moci zveřejnit stavové služby. Důvodem je, že nástroj pro vyrovnávání zatížení nebude moci vyhledat a směrovat provoz do příslušné stavové repliky služby. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externě exponované ASP.NET Core bezstavové služby
Kestrel je doporučeným webovým serverem pro front-end služby, které zpřístupňují externí koncové body HTTP s přístupem k Internetu. V systému Windows může HTTP.sys poskytovat schopnost sdílení portů, která umožňuje hostovat více webových služeb na stejné sadě uzlů pomocí stejného portu. V tomto scénáři jsou webové služby odlišené podle názvu hostitele nebo cesty, aniž by se museli spoléhat na front-end proxy nebo bránu, aby poskytovaly směrování HTTP.
 
Při zpřístupnění Internetu by služba bez stavu měla používat známý a stabilní koncový bod, který je dosažitelný prostřednictvím nástroje pro vyrovnávání zatížení. Tuto adresu URL zadáte uživatelům vaší aplikace. Doporučujeme následující konfiguraci:

| Typ | Doporučení | Poznámky |
| ---- | -------------- | ----- |
| Webový server | Kestrel | Kestrel je upřednostňovaný webový server, jak je podporován v systémech Windows a Linux. |
| Konfigurace portů | static | V konfiguraci ServiceManifest.xml by měl být nakonfigurovaný dobře známý statický port `Endpoints` , třeba 80 pro protokol HTTP nebo 443 pro protokol HTTPS. |
| ServiceFabricIntegrationOptions | Žádné | Použijte `ServiceFabricIntegrationOptions.None` možnost při konfiguraci služby Service Fabric middleware pro integraci, aby se služba nepokoušela ověřit příchozí požadavky pro jedinečný identifikátor. Externí uživatelé vaší aplikace nebudou znát jedinečné identifikační údaje, které používá middleware. |
| Počet instancí | -1 | V typických případech použití by nastavení počet instancí mělo být nastavené na hodnotu *-1*. To se provádí tak, že je instance dostupná na všech uzlech, které přijímají provoz z nástroje pro vyrovnávání zatížení. |

Pokud více externě vystavených služeb sdílí stejnou sadu uzlů, můžete použít HTTP.sys s jedinečnou, ale stabilní cestou adresy URL. To můžete provést úpravou adresy URL, která je k dispozici při konfiguraci IWebHost. Všimněte si, že to platí jenom pro HTTP.sys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Jenom interní Bezstavová služba ASP.NET Core
Bezstavové služby, které se volají jenom v rámci clusteru, by měly používat jedinečné adresy URL a dynamicky přiřazené porty, aby se zajistila spolupráce mezi několika službami. Doporučujeme následující konfiguraci:

| Typ | Doporučení | Poznámky |
| ---- | -------------- | ----- |
| Webový server | Kestrel | I když můžete použít HTTP.sys pro interní bezstavové služby, je nejlepším serverem, který umožňuje sdílet hostitele více instancí služby.  |
| Konfigurace portů | dynamicky přiřazené | Víc replik stavové služby může sdílet hostitelský proces nebo hostitelský operační systém, takže bude potřebovat jedinečné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Díky dynamickému přiřazování portů toto nastavení zabrání problému chybné identity popsanému výše. |
| InstanceCount | Libovolný | Nastavení počtu instancí lze nastavit na libovolnou hodnotu nutnou k provozu služby. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Jenom interní služba stavového ASP.NET Core
Stavové služby, které jsou volány pouze v rámci clusteru, by měly používat dynamicky přiřazené porty, aby se zajistila spolupráce mezi několika službami. Doporučujeme následující konfiguraci:

| Typ | Doporučení | Poznámky |
| ---- | -------------- | ----- |
| Webový server | Kestrel | `HttpSysCommunicationListener`Není určen pro použití ve stavových službách, ve kterých repliky sdílí hostitelský proces. |
| Konfigurace portů | dynamicky přiřazené | Víc replik stavové služby může sdílet hostitelský proces nebo hostitelský operační systém, takže bude potřebovat jedinečné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Díky dynamickému přiřazování portů toto nastavení zabrání problému chybné identity popsanému výše. |

## <a name="next-steps"></a>Další kroky
[Ladění aplikace Service Fabric pomocí Visual Studia](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
