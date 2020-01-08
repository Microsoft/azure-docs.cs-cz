---
title: Komunikace služby s ASP.NET Core
description: Naučte se používat ASP.NET Core v bezstavových a stavových aplikacích Azure Service Fabric Reliable Services.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639628"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core v Azure Service Fabric Reliable Services

ASP.NET Core je open source architektura pro různé platformy. Tato architektura je určená pro vytváření cloudových aplikací připojených k Internetu, jako jsou webové aplikace, aplikace IoT a mobilní back-endy.

Tento článek je podrobným průvodcem pro hostování ASP.NET Core Services v Service Fabric Reliable Services pomocí **Microsoft. ServiceFabric. AspNetCore.** sada balíčků NuGet.

Úvodní kurz o ASP.NET Core v Service Fabric a pokyny k nastavení vývojového prostředí najdete v tématu [kurz: vytvoření a nasazení aplikace s front-end službou ASP.NET Core Web API a stavovou back-end službou](service-fabric-tutorial-create-dotnet-app.md).

Ve zbývající části tohoto článku se předpokládá, že už jste obeznámeni s ASP.NET Core. Pokud ne, přečtěte si prosím [základy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core v prostředí Service Fabric

Aplikace ASP.NET Core i Service Fabric mohou běžet v rozhraní .NET Core nebo v plném .NET Framework. V Service Fabric můžete použít ASP.NET Core dvěma různými způsoby:
 - **Hostuje se jako spustitelný soubor hosta**. Tímto způsobem se primárně používá ke spouštění existujících aplikací ASP.NET Core v Service Fabric bez jakýchkoli změn kódu.
 - **Spouštějte v rámci spolehlivé služby**. Tímto způsobem umožníte lepší integraci s modulem runtime Service Fabric a povolíte stavové služby ASP.NET Core.

Ve zbývající části tohoto článku se dozvíte, jak používat ASP.NET Core v rámci spolehlivé služby prostřednictvím komponent ASP.NET Core Integration, které se dodávají s Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hostování služby Service Fabric

V Service Fabric jedna nebo víc instancí a replik služby běží v *procesu hostitele služby*: spustitelný soubor, který spouští váš kód služby. Vy, jako autor služby, vlastní hostitelský proces služby a Service Fabric ho aktivovat a monitorovat za vás.

Tradiční ASP.NET (až do MVC 5) je pevně spojená se službou IIS prostřednictvím System. Web. dll. ASP.NET Core poskytuje oddělení mezi webovým serverem a webovou aplikací. Díky tomuto oddělení můžou být webové aplikace přenosné mezi různými webovými servery. Umožňuje taky *samoobslužné hostování*webových serverů. To znamená, že můžete spustit webový server ve vlastním procesu, a to na rozdíl od procesu, který je vlastněn vyhrazeným softwarem webového serveru, jako je IIS.

Aby bylo možné kombinovat službu Service Fabric a ASP.NET, a to buď jako spustitelný soubor hosta, nebo ve spolehlivé službě, musíte být schopni spustit ASP.NET v rámci procesu hostitele služby. Tato funkce umožňuje samoobslužné hostování ASP.NET Core.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostování ASP.NET Core ve spolehlivé službě
Obvykle aplikace ASP.NET Core v místním prostředí vytvářejí webhosta v vstupním bodu aplikace, jako je například metoda `static void Main()` v `Program.cs`. V tomto případě je životní cyklus webhost svázán s životním cyklem procesu.

![Hostování ASP.NET Core v procesu][0]

Vstupní bod aplikace ale není správné místo pro vytvoření Webhostu ve spolehlivé službě. To je proto, že se vstupní bod aplikace používá pouze k registraci typu služby s modulem Service Fabric runtime, aby mohl vytvořit instance daného typu služby. Webhost by měl být vytvořen v samotné spolehlivé službě. V rámci procesu hostitele služby můžou instance služby nebo repliky procházet několika životními cykly. 

Spolehlivá instance služby je reprezentovaná vaší třídou služby odvozenou od `StatelessService` nebo `StatefulService`. Komunikační zásobník pro službu je součástí implementace `ICommunicationListener` ve vaší třídě služby. `Microsoft.ServiceFabric.AspNetCore.*` balíčky NuGet obsahují implementace `ICommunicationListener`, které spouštějí a spravují službu ASP.NET Core webhost pro buď Kestrel nebo HTTP. sys ve spolehlivé službě.

![Diagram pro hostování ASP.NET Core ve spolehlivé službě][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener` implementace pro Kestrel a HTTP. sys v balíčcích `Microsoft.ServiceFabric.AspNetCore.*` NuGet mají podobné vzory použití. Ale provádějí mírně různé akce specifické pro každý webový server. 

Oba naslouchací procesy komunikace poskytují konstruktor, který přijímá následující argumenty:
 - **`ServiceContext serviceContext`** : Jedná se o objekt `ServiceContext`, který obsahuje informace o běžící službě.
 - **`string endpointName`** : Jedná se o název konfigurace `Endpoint` v souboru ServiceManifest. XML. Primárně se dva komunikační naslouchací procesy liší. HTTP. sys *vyžaduje* konfiguraci `Endpoint`, zatímco Kestrel ne.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Toto je výraz lambda, který implementujete, ve kterém vytvoříte a vrátíte `IWebHost`. Umožňuje vám nakonfigurovat `IWebHost` způsob, jakým normálně ASP.NET Core aplikace. Lambda poskytuje adresu URL, která je vygenerována v závislosti na možnostech Service Fabric integrace, které používáte, a na konfiguraci `Endpoint`, kterou zadáte. Tuto adresu URL pak můžete změnit nebo použít ke spuštění webového serveru.

## <a name="service-fabric-integration-middleware"></a>Middleware pro integraci Service Fabric
Balíček NuGet `Microsoft.ServiceFabric.AspNetCore` zahrnuje metodu rozšíření `UseServiceFabricIntegration` na `IWebHostBuilder`, která přidává middleware s podporou Service Fabric. Tento middleware konfiguruje Kestrel nebo HTTP. sys `ICommunicationListener` k registraci jedinečné adresy URL služby pomocí Naming Service Service Fabric. Potom ověří požadavky klienta, aby se klienti připojovali ke správné službě. 

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

V důvěryhodném prostředí middleware, který je přidán pomocí metody `UseServiceFabricIntegration`, automaticky připojí jedinečný identifikátor k adrese odeslané do Naming Service. Ověřuje tento identifikátor u každé žádosti. Pokud identifikátor neodpovídá, middleware okamžitě vrátí odpověď HTTP 410, která zmizela.

Služby, které používají dynamicky přiřazený port, by měly používat tento middleware.

Služby, které používají pevný jedinečný port, nemají tento problém v prostředí pro spolupráci. Pevný jedinečný port se obvykle používá pro externě orientované služby, které potřebují dobře známý port pro klientské aplikace pro připojení k. Například většina webových aplikací s přístupem k Internetu bude pro připojení webového prohlížeče používat port 80 nebo 443. V takovém případě by se měl jedinečný identifikátor povolit.

Následující diagram znázorňuje tok požadavků s povoleným middlewarem:

![Integrace ASP.NET Core Service Fabric][2]

Kestrel i HTTP. sys `ICommunicationListener` implementace používají tento mechanismus naprosto stejným způsobem. I když soubor HTTP. sys dokáže interně odlišit požadavky na základě jedinečných cest URL pomocí základní funkce sdílení portů **http. sys** , tato funkce *se nepoužívá `ICommunicationListener`* implementaci pomocí protokolu HTTP. sys. Důvodem je to, že ve výše popsaném scénáři má za následek stavové kódy chyb HTTP 503 a HTTP 404. Díky tomu je obtížné, aby klienti zjistili záměr této chyby, protože HTTP 503 a HTTP 404 se často používají k indikaci dalších chyb. 

To znamená, že Kestrel i HTTP. sys `ICommunicationListener` implementace standardizace pro middleware poskytované metodou rozšíření `UseServiceFabricIntegration`. Proto klienti musí provést akci opětovného překladu koncového bodu služby na odpovědích HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP. sys v Reliable Services
Soubor HTTP. sys můžete použít v Reliable Services importem balíčku NuGet **Microsoft. ServiceFabric. AspNetCore. HttpSys** . Tento balíček obsahuje `HttpSysCommunicationListener`implementaci `ICommunicationListener`. `HttpSysCommunicationListener` umožňuje vytvořit ASP.NET Core webhost v rámci spolehlivé služby pomocí HTTP. sys jako webového serveru.

HTTP. sys je postaven na [rozhraní API systému Windows HTTP server](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Toto rozhraní API používá ovladač jádra **http. sys** ke zpracování požadavků HTTP a jejich směrování do procesů, které spouštějí webové aplikace. To umožňuje více procesů na stejném fyzickém nebo virtuálním počítači hostovat webové aplikace na stejném portu, a to v rámci jedinečné cesty URL nebo názvu hostitele. Tyto funkce jsou užitečné v Service Fabric pro hostování více webů ve stejném clusteru.

>[!NOTE]
>Implementace HTTP. sys funguje pouze na platformě Windows.

Následující diagram znázorňuje, jak HTTP. sys používá ovladač jádra **http. sys** ve Windows pro sdílení portů:

![Diagram HTTP. sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP. sys v bezstavové službě
Chcete-li použít `HttpSys` v bezstavové službě, přepište metodu `CreateServiceInstanceListeners` a vraťte instanci `HttpSysCommunicationListener`:

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP. sys ve stavové službě

`HttpSysCommunicationListener` není aktuálně navržena pro použití ve stavových službách z důvodu komplikací se základní funkcí sdílení portů **http. sys** . Další informace najdete v následující části věnované dynamickému přidělování portů pomocí HTTP. sys. Pro stavové služby je Kestrel doporučeným webovým serverem.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Pro webové servery, které používají rozhraní Windows HTTP Server API, včetně HTTP. sys, se vyžaduje konfigurace `Endpoint`. Webové servery, které používají rozhraní API systému Windows HTTP server, musí nejdřív rezervovat svou adresu URL pomocí HTTP. sys (to se obvykle provádí pomocí nástroje [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ). 

Tato akce vyžaduje zvýšená oprávnění, která vaše služby ve výchozím nastavení nemají. Možnosti "http" nebo "https" pro vlastnost `Protocol` konfigurace `Endpoint` v souboru ServiceManifest. XML jsou určeny konkrétně k tomu, aby modul runtime Service Fabric zaregistroval adresu URL pomocí souboru HTTP. sys vaším jménem. Používá předponu adresy URL se [*silným zástupným znakem*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) .

Chcete-li například vyhradit `http://+:80` pro službu, použijte následující konfiguraci v souboru ServiceManifest. XML:

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

A název koncového bodu musí být předán konstruktoru `HttpSysCommunicationListener`:

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

#### <a name="use-httpsys-with-a-static-port"></a>Použití HTTP. sys se statickým portem
Pokud chcete použít statický port se souborem HTTP. sys, zadejte číslo portu v konfiguraci `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Použití HTTP. sys s dynamickým portem
Pokud chcete použít dynamicky přiřazený port se souborem HTTP. sys, vynechejte vlastnost `Port` v konfiguraci `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Dynamický port přidělený konfigurací `Endpoint` poskytuje jenom jeden port *na proces hostitele*. Aktuální Service Fabric hostující model umožňuje hostování více instancí služby a/nebo replik v rámci stejného procesu. To znamená, že každá z nich bude sdílet stejný port, když se přidělí prostřednictvím konfigurace `Endpoint`. Port může sdílet několik instancí **http. sys** pomocí základní funkce sdílení portů **http. sys** . Ale není `HttpSysCommunicationListener` podporována v důsledku komplikací, které zavádí pro požadavky klientů. V případě použití dynamického portu je Kestrel navržený webový server.

## <a name="kestrel-in-reliable-services"></a>Kestrel v Reliable Services
Pomocí Kestrel můžete v Reliable Services importovat balíček NuGet **Microsoft. ServiceFabric. AspNetCore. Kestrel** . Tento balíček obsahuje `KestrelCommunicationListener`implementaci `ICommunicationListener`. `KestrelCommunicationListener` umožňuje vytvořit ASP.NET Core webhost v rámci spolehlivé služby pomocí Kestrel jako webového serveru.

Kestrel je webový server pro různé platformy pro ASP.NET Core. Na rozdíl od HTTP. sys Kestrel nepoužívá centralizovaného správce koncových bodů. I na rozdíl od HTTP. sys nepodporuje Kestrel sdílení portů mezi několika procesy. Každá instance Kestrel musí používat jedinečný port. Další informace o Kestrel najdete v [podrobnostech implementace](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Diagram Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel nestavové služby
Chcete-li použít `Kestrel` v bezstavové službě, přepište metodu `CreateServiceInstanceListeners` a vraťte instanci `KestrelCommunicationListener`:

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
Chcete-li použít `Kestrel` ve stavové službě, přepište metodu `CreateServiceReplicaListeners` a vraťte instanci `KestrelCommunicationListener`:

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

V tomto příkladu je instance typu Singleton `IReliableStateManager` poskytnuta kontejneru vkládání závislostí webhost. To není nezbytně nutné, ale umožňuje používat `IReliableStateManager` a spolehlivé kolekce v metodách akcí kontroleru MVC.

`KestrelCommunicationListener` ve stavové službě *není k dispozici název* konfigurace `Endpoint`. To je podrobněji vysvětleno v následující části.

### <a name="configure-kestrel-to-use-https"></a>Nakonfigurovat Kestrel k používání HTTPS
Když v rámci služby povolíte HTTPS s Kestrel, budete muset nastavit několik možností naslouchání. Aktualizujte `ServiceInstanceListener`, aby používala koncový bod *EndpointHttps* a naslouchala na specifickém portu (například port 443). Když nakonfigurujete webového hostitele tak, aby používal webový server Kestrel, musíte nakonfigurovat Kestrel na naslouchání adresám IPv6 na všech síťových rozhraních: 

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
Pro použití Kestrel není nutná konfigurace `Endpoint`. 

Kestrel je jednoduchý samostatný webový server. Na rozdíl od souboru HTTP. sys (nebo HttpListener) nepotřebuje konfiguraci `Endpoint` v souboru ServiceManifest. XML, protože před zahájením nevyžaduje registraci adresy URL. 

#### <a name="use-kestrel-with-a-static-port"></a>Použití Kestrel se statickým portem
Statický port můžete nakonfigurovat v konfiguraci `Endpoint` ServiceManifest. XML pro použití s Kestrel. I když to není nezbytně nutné, nabízí dvě možné výhody:
 - Pokud port nepadne do rozsahu portů aplikace, je otevřen pomocí brány firewall operačního systému pomocí Service Fabric.
 - Adresa URL, kterou jste dostali `KestrelCommunicationListener`, bude používat tento port.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Pokud je nakonfigurován `Endpoint`, musí se jeho název předat do konstruktoru `KestrelCommunicationListener`: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Pokud ServiceManifest. XML nepoužívá konfiguraci `Endpoint`, vynechejte název v konstruktoru `KestrelCommunicationListener`. V takovém případě bude používat dynamický port. Další informace najdete v další části.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Použití Kestrel s dynamickým portem
Kestrel nemůže použít automatické přiřazování portů z konfigurace `Endpoint` v souboru ServiceManifest. XML. Důvodem je to, že automatické přiřazování portů z konfigurace `Endpoint` přiřadí jedinečný port pro každý *hostitelský proces*a jeden hostitelský proces může obsahovat několik instancí Kestrel. Nefunguje s Kestrel, protože nepodporuje sdílení portů. Proto musí být každá instance Kestrel otevřená na jedinečném portu.

Pokud chcete použít dynamické přiřazování portů s Kestrel, vynechejte zcela konfiguraci `Endpoint` v souboru ServiceManifest. XML a nepředávejte do konstruktoru `KestrelCommunicationListener` název koncového bodu, jak je znázorněno níže:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

V této konfiguraci bude `KestrelCommunicationListener` automaticky vybírat nepoužitý port z rozsahu portů aplikace.

V případě protokolu HTTPS by měl mít koncový bod nakonfigurovaný s protokolem HTTPS bez portu zadaného v souboru ServiceManifest. XML a předávat název koncového bodu konstruktoru KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Poskytovatel konfigurace Service Fabric
Konfigurace aplikací v ASP.NET Core vychází z párů klíč-hodnota, které vytvořil Poskytovatel konfigurace. Pokud chcete získat další informace o obecné podpoře konfigurace ASP.NET Core, přečtěte si téma [konfigurace v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) .

Tato část popisuje, jak se poskytovatel konfigurace Service Fabric integruje s konfigurací ASP.NET Core importem balíčku NuGet `Microsoft.ServiceFabric.AspNetCore.Configuration`.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration – spouštěcí rozšíření
Po importu balíčku `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet musíte zaregistrovat Service Fabric zdroj konfigurace pomocí rozhraní API pro ASP.NET Core konfigurace. Provedete to tak, že zkontrolujete rozšíření **AddServiceFabricConfiguration** v oboru názvů `Microsoft.ServiceFabric.AspNetCore.Configuration` v `IConfigurationBuilder`.

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

Například pokud máte konfigurační balíček s názvem `MyConfigPackage` s následujícím obsahem, bude hodnota konfigurace k dispozici na ASP.NET Core `IConfiguration` prostřednictvím *MyConfigPackage: MyConfigSection: MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Možnosti konfigurace Service Fabric
Poskytovatel konfigurace Service Fabric také podporuje `ServiceFabricConfigurationOptions` ke změně výchozího chování mapování klíčů.

#### <a name="encrypted-settings"></a>Šifrované nastavení
Service Fabric podporuje šifrované nastavení, stejně jako poskytovatel konfigurace Service Fabric. Zašifrovaná nastavení se nešifrují ASP.NET Core `IConfiguration` ve výchozím nastavení. Místo toho jsou uloženy šifrované hodnoty. Pokud ale chcete hodnotu dešifrovat pro ukládání do ASP.NET Core IConfiguration, můžete nastavit příznak *DecryptValue* na hodnotu false v rozšíření `AddServiceFabricConfiguration` následujícím způsobem:

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
Service Fabric podporuje více konfiguračních balíčků. Ve výchozím nastavení je název balíčku součástí konfiguračního klíče. Můžete ale nastavit příznak `IncludePackageName` na hodnotu false následujícím způsobem:
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
Poskytovatel konfigurace Service Fabric také podporuje pokročilejší scénáře pro přizpůsobení mapování klíčů pomocí `ExtractKeyFunc` a vlastní extrakci hodnot pomocí `ExtractValueFunc`. Můžete dokonce změnit celý proces naplnění dat z konfigurace Service Fabric na ASP.NET Core konfiguraci pomocí `ConfigAction`.

Následující příklady ilustrují, jak použít `ConfigAction` k přizpůsobení datových naplnění dat:
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
Poskytovatel konfigurace Service Fabric podporuje taky aktualizace konfigurace. K získání oznámení o změně můžete použít ASP.NET Core `IOptionsMonitor` a pak pomocí `IOptionsSnapshot` znovu načíst konfigurační data. Další informace najdete v tématu [možnosti ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

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
Kestrel je doporučeným webovým serverem pro front-end služby, které zpřístupňují externí koncové body HTTP s přístupem k Internetu. V systému Windows může ovladač HTTP. sys poskytovat schopnost sdílení portů, která umožňuje hostovat více webových služeb na stejné sadě uzlů pomocí stejného portu. V tomto scénáři jsou webové služby odlišené podle názvu hostitele nebo cesty, aniž by se museli spoléhat na front-end proxy nebo bránu, aby poskytovaly směrování HTTP.
 
Při zpřístupnění Internetu by služba bez stavu měla používat známý a stabilní koncový bod, který je dosažitelný prostřednictvím nástroje pro vyrovnávání zatížení. Tuto adresu URL zadáte uživatelům vaší aplikace. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | Kestrel je upřednostňovaný webový server, jak je podporován v systémech Windows a Linux. |
| Konfigurace portu | static | Dobře známý statický port by měl být nakonfigurovaný v konfiguraci `Endpoints` ServiceManifest. XML, třeba 80 pro protokol HTTP nebo 443 pro protokol HTTPS. |
| ServiceFabricIntegrationOptions | Žádné | Možnost `ServiceFabricIntegrationOptions.None` použijte při konfiguraci služby Service Fabric Integration middleware, aby se služba nepokoušela ověřit příchozí požadavky pro jedinečný identifikátor. Externí uživatelé vaší aplikace nebudou znát jedinečné identifikační údaje, které používá middleware. |
| Počet instancí | -1 | V typických případech použití by nastavení počet instancí mělo být nastavené na hodnotu *-1*. To se provádí tak, že je instance dostupná na všech uzlech, které přijímají provoz z nástroje pro vyrovnávání zatížení. |

Pokud několik externě vystavených služeb sdílí stejnou sadu uzlů, můžete použít soubor HTTP. sys s jedinečnou, ale stabilní cestou adresy URL. To můžete provést úpravou adresy URL, která je k dispozici při konfiguraci IWebHost. Všimněte si, že to platí jenom pro HTTP. sys.

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

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | I když můžete použít protokol HTTP. sys pro interní bezstavové služby, je Kestrel nejlepším serverem, který umožňuje sdílet hostitele s víc instancemi služby.  |
| Konfigurace portu | dynamicky přiřazené | Víc replik stavové služby může sdílet hostitelský proces nebo hostitelský operační systém, takže bude potřebovat jedinečné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Díky dynamickému přiřazování portů toto nastavení zabrání problému chybné identity popsanému výše. |
| InstanceCount | jakýmikoli | Nastavení počtu instancí lze nastavit na libovolnou hodnotu nutnou k provozu služby. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Jenom interní služba stavového ASP.NET Core
Stavové služby, které jsou volány pouze v rámci clusteru, by měly používat dynamicky přiřazené porty, aby se zajistila spolupráce mezi několika službami. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | `HttpSysCommunicationListener` nejsou určeny pro použití ve stavových službách, ve kterých repliky sdílí hostitelský proces. |
| Konfigurace portu | dynamicky přiřazené | Víc replik stavové služby může sdílet hostitelský proces nebo hostitelský operační systém, takže bude potřebovat jedinečné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Díky dynamickému přiřazování portů toto nastavení zabrání problému chybné identity popsanému výše. |

## <a name="next-steps"></a>Další kroky
[Ladění aplikace Service Fabric pomocí Visual Studia](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
