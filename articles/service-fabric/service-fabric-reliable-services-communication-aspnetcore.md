---
title: Komunikace pomocí ASP.NET Core | Dokumentace Microsoftu
description: Zjistěte, jak používat ASP.NET Core v bezstavových a stavových Reliable Services.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939795"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core v Azure Service Fabric Reliable Services

ASP.NET Core je open source a multiplatformní rozhraní. Toto rozhraní je navržená pro vytváření aplikací založené na cloudu, připojené k Internetu, jako jsou například webové aplikace, aplikace IoT a mobilních back-endů.

Tento článek je podrobné příručce k hostování služeb ASP.NET Core v Service Fabric Reliable Services pomocí **Microsoft.ServiceFabric.AspNetCore.** sada balíčků NuGet.

Úvodní kurz k ASP.NET Core v Service Fabric a postup získání nastavení vývojového prostředí najdete v tématu [kurzu: Vytvoření a nasazení aplikace s front-end službou webového rozhraní API ASP.NET Core a stavovou back-end službu](service-fabric-tutorial-create-dotnet-app.md).

Zbývající část tohoto článku se předpokládá, že jste již obeznámeni s ASP.NET Core. Pokud ne, přečtěte si prosím [základy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core v Service Fabric prostředí

Aplikace ASP.NET Core a Service Fabric můžete spustit na .NET Core nebo úplné rozhraní .NET Framework. ASP.NET Core můžete použít v Service Fabric dvěma různými způsoby:
 - **Hostovaný jako spustitelný soubor typu Host**. Tímto způsobem se používá především k provozování existujících aplikací ASP.NET Core v Service Fabric s žádnými změnami kódu.
 - **Spouštět v službu reliable**. Tímto způsobem umožňuje lepší integraci s modulem runtime Service Fabric a stavové služby ASP.NET Core services.

Zbývající část tohoto článku vysvětluje, jak používat ASP.NET Core uvnitř spolehlivé služby pomocí ASP.NET základních komponent integrace, které se dodávají pomocí Service Fabric SDK.

## <a name="service-fabric-service-hosting"></a>Hostování služeb Service Fabric

V Service Fabric, jeden nebo více instancí nebo replik služby běží v *služby hostitelský proces*: spustitelný soubor, na kterém běží váš kód služby. Jako autor služby vlastníte hostitelského procesu služby a Service Fabric aktivuje a monitoruje za vás.

Tradiční technologie ASP.NET (až MVC 5) je těsně spjat s IIS prostřednictvím System.Web.dll. ASP.NET Core umožňuje oddělit mezi webovým serverem a webové aplikace. Toto oddělení umožňuje webových aplikací přenositelnost mezi jiné webové servery. Také umožňuje webovým serverům *v místním prostředí*. To znamená, že webový server můžete spustit ve vašem vlastním procesu, na rozdíl od procesu, který je vlastněn softwaru vyhrazený webový server, například službou IIS.

Kombinovat služby Service Fabric a ASP.NET, jako spustitelný soubor typu Host nebo spolehlivé služby, musí být schopni spustit ASP.NET uvnitř hostitelského procesu služby. ASP.NET Core s vlastním hostováním můžete to provést.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostování ASP.NET Core v reliable service
Obvykle v místním prostředí aplikací ASP.NET Core vytvořit webového hostitele ve vstupní bod aplikace, například `static void Main()` metoda ve `Program.cs`. Životní cyklus webového hostitele v tomto případě je vázán na životního cyklu procesu.

![Hostování v procesu ASP.NET Core][0]

Ale vstupní bod aplikace není na správném místě vytvořit WebHost spolehlivé služby. To je, protože vstupní bod aplikace slouží pouze k registraci modulu runtime Service Fabric, typ služby tak, že je možné vytvořit instance daného typu služby. Tomuto webovému hostiteli musí být vytvořené v samotné spolehlivé služby. V rámci hostitelského procesu služby instance služby a/nebo repliky můžete projít několik životní cykly. 

Instance spolehlivé služby představuje vaše služby třídu odvozenou z `StatelessService` nebo `StatefulService`. Komunikační balík pro službu je součástí `ICommunicationListener` implementaci ve třídě služby. `Microsoft.ServiceFabric.AspNetCore.*` Balíčky NuGet obsahovat implementace `ICommunicationListener` , který spouští a spravuje hostitele ASP.NET Core pro Kestrel nebo ovladač HTTP.sys spolehlivé služby.

![Diagram pro hostování ASP.NET Core v reliable service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener` Implementace pro Kestrel a HTTP.sys v `Microsoft.ServiceFabric.AspNetCore.*` balíčky NuGet mají podobné vzorce používání. Ale provádějí mírně odlišné akce specifické pro každý webový server. 

Obě naslouchací procesy komunikace poskytovat konstruktor, který používá následující argumenty:
 - **`ServiceContext serviceContext`** : Toto je `ServiceContext` objekt, který obsahuje informace o spuštěné služby.
 - **`string endpointName`** : Toto je název `Endpoint` konfigurace v souboru ServiceManifest.xml. Je primárně určen kde naslouchacích procesů dvou komunikačních lišit. Ovladač HTTP.sys *vyžaduje* `Endpoint` konfigurace, zatímco Kestrel nepodporuje.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Toto je výraz lambda, kterou implementujete v, který můžete vytvořit a vrátit `IWebHost`. Umožňuje nakonfigurovat `IWebHost` tak, jak byste normálně v aplikaci ASP.NET Core. Výraz lambda poskytuje, aby se vygenerovala adresa URL pro vás, v závislosti na možnosti integrace Service Fabric můžete použít a `Endpoint` konfigurace, které zadáte. Potom můžete upravit nebo použít tuto adresu URL ke spuštění webového serveru.

## <a name="service-fabric-integration-middleware"></a>Middleware pro integraci Service Fabric
`Microsoft.ServiceFabric.AspNetCore` Obsahuje balíček NuGet `UseServiceFabricIntegration` rozšiřující metody na `IWebHostBuilder` , který přidá Service Fabric dokáže rozpoznávat middleware. Tento middleware konfiguruje Kestrel nebo ovladač HTTP.sys `ICommunicationListener` zaregistrovat adresu URL služby jedinečný služba pojmenování Service Fabric. Pak ověří požadavků klientů a ujistěte se, že se klienti připojují k požadovanou službu. 

Tento krok je nezbytný, aby klienti nemohli omylem připojování ke službě nesprávné. To je proto, že ve sdílené hostitele prostředí jako je Service Fabric, můžete spustit na stejný fyzický nebo virtuální počítač více webových aplikací, ale nepoužívejte názvy hostitele. Tento scénář je popsaný v následující části podrobněji.

### <a name="a-case-of-mistaken-identity"></a>Případ chybné identity
Repliky služby, bez ohledu na protokol, naslouchat na kombinaci IP: jedinečný port. Po zahájení repliku služba naslouchá na koncový bod IP: port sestavy tuto adresu koncového bodu ke službě pojmenování Service Fabric. Existuje klienty nebo jiné služby, můžete jej zjistit. Pokud služby používat porty dynamicky přiřazené aplikace, repliku služby shodou použít stejný koncový bod IP: port jiné služby dříve na stejný fyzický nebo virtuální počítač. To může způsobit klient omylem připojit ke službě nesprávné. V tomto scénáři může dojít, pokud dojde k následujícímu pořadí událostí:

 1. Služby A naslouchá na 10.0.0.1:30000 přes protokol HTTP. 
 2. Klient řeší A služby a získá 10.0.0.1:30000 adresu.
 3. Služby A přesune na jiný uzel.
 4. Služba B je umístěn na 10.0.0.1 a shodou používá stejný port 30000.
 5. Klient se pokusí připojit k A služby s 10.0.0.1:30000 adres v mezipaměti.
 6. Klient je teď úspěšně připojil ke službě B, ne porozumění je připojen k chybě služby.

To může způsobit chyby v náhodných intervalech, které může být obtížné diagnostikovat.

### <a name="using-unique-service-urls"></a>Pomocí adresy URL služby jedinečný
Nechcete-li tyto chyby, můžete služby publikovat koncový bod na pojmenování Service s jedinečným identifikátorem a následně ověřit tento jedinečný identifikátor během požadavky klientů. Toto je kooperativní akce mezi službami v důvěryhodném prostředí nebezpečný tenanta. Neposkytuje služba zabezpečené ověřování v prostředí nebezpečný tenanta.

V případě důvěryhodného prostředí middleware, který se přidal `UseServiceFabricIntegration` metoda automaticky připojí jedinečný identifikátor na adresu publikované ve službě pojmenování. Ověřuje identifikátoru s každým požadavkem. Pokud identifikátor neodpovídá, middleware okamžitě vrátí odpověď HTTP 410 pryč.

Služby, které využívají dynamicky přiřazeného portu se ujistěte, použijte tento middleware.

Služby, které používají pevné jedinečné číslo portu nemají tento problém v prostředí spolupráce. Pevné jedinečné číslo portu se obvykle používá pro externí koncové služby, které potřebujete pro klientské aplikace pro připojení k dobře známém portu. Například většiny webových aplikací přístupem k Internetu, bude používat port 80 nebo 443 pro připojení webového prohlížeče. V takovém případě by se neměla povolovat jedinečný identifikátor.

Následující diagram znázorňuje tok požadavku s middlewarem povoleno:

![Integrace ASP.NET Core v Service Fabric][2]

Kestrel a HTTP.sys `ICommunicationListener` implementace používá tento mechanismus přesně stejným způsobem. I když ovladač HTTP.sys může interně rozlišovat žádosti na základě jedinečných cest URL pomocí základního **HTTP.sys** sdílení funkce, které funkce jsou portů *není* používané HTTP.sys `ICommunicationListener`implementace. Důvodem je, je výsledkem 503 protokolu HTTP a protokolu HTTP 404 chybové kódy stavu v podle scénáře popsaného výše. Který zase ztěžuje pro klienty určit záměr chybu, jak 503 protokolu HTTP a protokolu HTTP 404 se běžně používají k označení jiné chyby. 

Proto Kestrel i HTTP.sys `ICommunicationListener` implementace standardizovat používání na middleware poskytuje `UseServiceFabricIntegration` – metoda rozšíření. Klienti proto nutné provést akci znovu přeložit koncový bod služby na odpovědi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>Ovladač HTTP.sys v modelu Reliable Services
Ovladač HTTP.sys můžete použít v modelu Reliable Services importováním **Microsoft.ServiceFabric.AspNetCore.HttpSys** balíček NuGet. Tento balíček obsahuje `HttpSysCommunicationListener`, implementace `ICommunicationListener`. `HttpSysCommunicationListener` Umožňuje vytvořit hostitele ASP.NET Core uvnitř spolehlivé služby pomocí HTTP.sys jako webový server.

HTTP.sys postavena [rozhraní API Windows HTTP serveru](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Toto rozhraní API používá **HTTP.sys** ovladač jádra pro zpracování požadavků HTTP a směrovat do procesů, na kterých běží webové aplikace. To umožňuje více procesů na stejný fyzický nebo virtuální počítač pro hostování webových aplikací na stejném portu, jednoznačně rozlišit podle buď jedinečné adresy URL cesta nebo název hostitele. Tyto funkce jsou užitečné v Service Fabric pro hostování více webů ve stejném clusteru.

>[!NOTE]
>Implementace HTTP.sys funguje pouze na platformě Windows.

Následující diagram znázorňuje, jak pomocí HTTP.sys **HTTP.sys** ovladač jádra ve Windows pro sdílení portů:

![HTTP.sys diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>Ovladač HTTP.sys bezstavové služby
Použití `HttpSys` bezstavové služby přepsat `CreateServiceInstanceListeners` metoda a vraťte se `HttpSysCommunicationListener` instance:

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

### <a name="httpsys-in-a-stateful-service"></a>Ovladač HTTP.sys stavové služby

`HttpSysCommunicationListener` není aktuálně navrženy pro použití v stavové služby z důvodu komplikace se základní **HTTP.sys** funkci Sdílení portu. Další informace najdete v tématu v následující části na přidělování dynamických portů se souborem HTTP.sys. Pro stavové služby je Kestrel navrhované webový server.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu

`Endpoint` Konfigurace je nutná pro webové servery, které používají Windows HTTP serveru rozhraní API, včetně HTTP.sys. Webové servery, které používají rozhraní API Windows HTTP serveru musíte nejprve rezervovat jejich adresy URL se souborem HTTP.sys (to je obvykle provedeno pomocí [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) nástroje). 

Tato akce vyžaduje zvýšená oprávnění, které vaše služby nemají ve výchozím nastavení. Možnosti "http" nebo "https" `Protocol` vlastnost `Endpoint` konfigurace v souboru ServiceManifest.xml se používají výhradně pro dáte pokyn, aby modul runtime Service Fabric k registraci adresy URL se souborem HTTP.sys vaším jménem. Dělá to pomocí [ *silný zástupný znak* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) předponu adresy URL.

Například pro rezervaci `http://+:80` pro službu, použijte následující konfiguraci v souboru ServiceManifest.xml:

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

A název koncového bodu musí být předán `HttpSysCommunicationListener` konstruktor:

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

#### <a name="use-httpsys-with-a-static-port"></a>Ovladač HTTP.sys pomocí statického portu
Pro použití statického portu se souborem HTTP.sys, zadejte číslo portu `Endpoint` konfigurace:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Pomocí HTTP.sys dynamický port
Chcete-li použít dynamicky přiřazeného portu s HTTP.sys, vynechejte `Port` vlastnost v `Endpoint` konfigurace:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Dynamický port přidělené `Endpoint` konfigurace obsahuje pouze jeden port *za hostitelský proces*. Aktuální model hostingu Service Fabric umožňuje více instancí služby a/nebo replik zajistit také jejich hostování v rámci stejného procesu. To znamená, že každý z nich bude sdílet stejný port při přidělování prostřednictvím `Endpoint` konfigurace. Více **HTTP.sys** instance můžete sdílet port pomocí základního **HTTP.sys** funkci Sdílení portu. Není podporován, ale `HttpSysCommunicationListener` kvůli komplikace přináší pro požadavky klientů. Použití dynamických portů je Kestrel navrhované webový server.

## <a name="kestrel-in-reliable-services"></a>Kestrel v modelu Reliable Services
Kestrel můžete použít v modelu Reliable Services importováním **Microsoft.ServiceFabric.AspNetCore.Kestrel** balíček NuGet. Tento balíček obsahuje `KestrelCommunicationListener`, implementace `ICommunicationListener`. `KestrelCommunicationListener` Umožňuje vytvořit hostitele ASP.NET Core uvnitř spolehlivé služby s využitím Kestrel jako webový server.

Kestrel je že multiplatformní webový server pro ASP.NET Core podle libuv knihovnu asynchronní vstupně-výstupní operace napříč platformami. Na rozdíl od HTTP.sys nepoužívá Kestrel správce centralizované koncový bod. Na rozdíl od HTTP.sys Kestrel nepodporuje sdílení portů mezi více procesy. Každá instance Kestrel musíte použít jedinečný port.

![Kestrel diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel bezstavové služby
Použití `Kestrel` bezstavové služby přepsat `CreateServiceInstanceListeners` metoda a vraťte se `KestrelCommunicationListener` instance:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel stavové služby
Použití `Kestrel` stavové služby přepsat `CreateServiceReplicaListeners` metoda a vraťte se `KestrelCommunicationListener` instance:

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

V tomto příkladu instanci typu singleton `IReliableStateManager` je k dispozici do kontejneru pro vkládání závislosti tomuto webovému hostiteli. To není nezbytně nutné, ale umožňuje použití `IReliableStateManager` a spolehlivé kolekce v vaše metody akce kontroleru MVC.

`Endpoint` Se název konfigurace *není* poskytnuté `KestrelCommunicationListener` stavové služby. To je vysvětleno podrobněji v následující části.

### <a name="configure-kestrel-to-use-https"></a>Nakonfigurovat Kestrel k používání HTTPS
Při povolení protokolu HTTPS se Kestrel ve službě, budete muset nastavit několik možností, jak naslouchání. Aktualizace `ServiceInstanceListener` určený *EndpointHttps* koncového bodu a naslouchání na konkrétní port (například port 443). Při konfiguraci webového hostitele použití Kestrel webového serveru, je nutné nakonfigurovat Kestrel k naslouchání pro adresy IPv6 na všech síťových rozhraní: 

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

Úplný příklad v kurzu, najdete v části [nakonfigurovat Kestrel k používání HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfigurace koncového bodu
`Endpoint` Konfigurace není k využití Kestrel nutné. 

Kestrel je jednoduchý samostatný webový server. Na rozdíl od HTTP.sys (nebo HttpListener), není nutné `Endpoint` konfigurace v souboru ServiceManifest.xml vzhledem k tomu, že adresa URL registrace nevyžaduje před spuštěním. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel pomocí statického portu
Statický port v můžete nakonfigurovat `Endpoint` konfigurace ServiceManifest.xml pro použití s Kestrel. I když to není nezbytně nutné, nabízí dvě výhody:
 - Pokud port není spadat do rozsahu portů aplikace, je otevřít přes bránu firewall operačního systému Service Fabric.
 - Adresu URL, které prostřednictvím `KestrelCommunicationListener` použije tento port.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Pokud `Endpoint` je nakonfigurován, jeho název být předány do `KestrelCommunicationListener` konstruktor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Pokud ServiceManifest.xml nepoužívá `Endpoint` konfigurace, vypuštění názvu v `KestrelCommunicationListener` konstruktoru. V takovém případě použije dynamický port. V části Další informace o tomto.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Použití Kestrel prostřednictvím dynamického portu
Kestrel nemůžete použít automatické port přiřazení z `Endpoint` konfigurace v souboru ServiceManifest.xml. Důvodem je, že automatické přiřazení z portu `Endpoint` konfigurace přiřadí jedinečný port za *proces hostitele*, a jeden hostitelský proces může obsahovat několik instancí Kestrel. To nebude fungovat s Kestrel, protože nepodporuje sdílení portů. Každá instance Kestrel proto musí být otevřen na jedinečný port.

Chcete-li používat dynamické přiřazování portu Kestrel, vynechejte `Endpoint` konfigurace v souboru ServiceManifest.xml zcela a nepředávejte název koncového bodu pro `KestrelCommunicationListener` konstruktoru, následujícím způsobem:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

V této konfiguraci `KestrelCommunicationListener` automaticky vybere nepoužitého portu z rozsahu portů aplikace.

## <a name="service-fabric-configuration-provider"></a>Poskytovatel konfigurace pro Service Fabric
Konfigurace aplikace v ASP.NET Core je založená na páry klíč hodnota stanovené poskytovatele konfigurace. Čtení [konfigurace v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) pochopit další na obecné ASP.NET Core podpora konfigurace.

Tato část popisuje, jak poskytovatel konfigurace pro Service Fabric se dá integrovat s ASP.NET Core konfigurací importováním `Microsoft.ServiceFabric.AspNetCore.Configuration` balíček NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration spuštění rozšíření
Po importu `Microsoft.ServiceFabric.AspNetCore.Configuration` balíčku NuGet, budete muset zaregistrovat zdroj konfigurace Service Fabric pomocí rozhraní API ASP.NET Core konfigurace. To provedete tak, že zkontrolujete **AddServiceFabricConfiguration** rozšíření `Microsoft.ServiceFabric.AspNetCore.Configuration` obor názvů proti `IConfigurationBuilder`.

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

Služba ASP.NET Core teď můžete přístup k nastavení konfigurace Service Fabric, stejně jako ostatní nastavení aplikace. Například můžete použít možnosti vzor k načtení nastavení do objektů se silným typem.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Výchozí mapování kláves
Ve výchozím nastavení poskytovatel konfigurace Service Fabric obsahuje název balíčku, název oddílu a názvu vlastnosti. Dohromady vytvářejí konfigurační klíč ASP.NET Core, následujícím způsobem:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Například, pokud jste konfigurační balíček s názvem `MyConfigPackage` s následujícím obsahem, pak hodnota konfigurace bude k dispozici pro ASP.NET Core `IConfiguration` prostřednictvím *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Možnosti konfigurace pro Service Fabric
Poskytovatel konfigurace pro Service Fabric také podporuje `ServiceFabricConfigurationOptions` Chcete-li změnit výchozí chování mapování kláves.

#### <a name="encrypted-settings"></a>U šifrovaného nastavení
Service Fabric podporuje u šifrovaného nastavení, stejně jako poskytovatel konfigurace pro Service Fabric. U šifrovaného nastavení nejsou dešifrovat pro ASP.NET Core `IConfiguration` ve výchozím nastavení. Šifrované hodnoty uložených místo. Ale pokud chcete k dešifrování hodnoty pro uložení v ASP.NET Core s parametry IConfiguration, můžete nastavit *DecryptValue* příznak na hodnotu false v `AddServiceFabricConfiguration` rozšíření, následujícím způsobem:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Více balíčků konfigurace
Service Fabric podporuje více balíčků konfigurace. Ve výchozím nastavení název balíčku je součástí konfiguračního klíče. Ale můžete nastavit `IncludePackageName` příznak na hodnotu false, následujícím způsobem:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Vlastní klíče mapování, hodnota extrakce a naplnění dat
Poskytovatel konfigurace pro Service Fabric také podporuje pokročilejší scénáře přizpůsobení mapování kláves s `ExtractKeyFunc` a vlastní extrahovat hodnoty `ExtractValueFunc`. Můžete dokonce i změnit celý proces naplňování dat z konfigurace v Service Fabric do konfigurace ASP.NET Core s použitím `ConfigAction`.

Následující příklady ukazují, jak používat `ConfigAction` přizpůsobit pro naplnění dat:
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
Poskytovatel konfigurace pro Service Fabric také podporuje aktualizace konfigurace. Můžete používat ASP.NET Core `IOptionsMonitor` přijmout oznámení o změnách, a potom pomocí `IOptionsSnapshot` znovu načte konfigurační data. Další informace najdete v tématu [možnosti ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Tyto možnosti jsou podporovány ve výchozím nastavení. Žádné další kódování je potřeba, aby povolovala aktualizace konfigurace.

## <a name="scenarios-and-configurations"></a>Scénáře a konfigurace
Tato část obsahuje kombinaci webového serveru, konfigurace portů, možnosti integrace Service Fabric a různá nastavení, které doporučujeme k řešení potíží s následující scénáře:
 - Externě vystavený bezstavové služby ASP.NET Core
 - Jen pro interní bezstavové služby ASP.NET Core
 - Jen pro interní stavové služby ASP.NET Core

**Externě vystavené služby** je ten, který zpřístupňuje koncový bod, která je volána z mimo cluster, obvykle pomocí nástroje pro vyrovnávání zatížení.

**Pouze interní** služby je jedna jehož koncový bod je volat pouze z v rámci clusteru.

> [!NOTE]
> Koncové body stavové služby obecně by neměly být vystaveny k Internetu. Clustery za nástrojem pro vyrovnávání zatížení, které si nejsou vědomi řešení služby Service Fabric, jako je Azure Load Balancer, nebude možné vystavit stavové služby. To je, protože nástroj pro vyrovnávání zatížení nebude moci najít a směrovat provoz do repliky příslušnou stavovou službu. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externě vystavený bezstavové služby ASP.NET Core
Kestrel je navrhované webový server pro front-endové služby, které zpřístupňují koncové body HTTP externí, přístupem k Internetu. Na Windows můžete zadat HTTP.sys sdílení funkce port, který umožňuje hostovat několik webových služeb na stejnou sadu uzlů pomocí stejný port. V tomto scénáři jsou rozlišené webové služby podle názvu hostitele nebo cestu, bez nutnosti spoléhat se na front-endového serveru proxy nebo brány poskytují směrování protokolu HTTP.
 
Pokud přístup k Internetu, používejte bezstavovou službu dobře známé a stabilní koncový bod, který je dostupný prostřednictvím nástroje pro vyrovnávání zatížení. Tuto adresu URL budete poskytovat uživatelům vaší aplikace. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | Kestrel je upřednostňovaný webový server, jako je podporováno ve Windows a Linux. |
| Konfigurace portu | Statická | Dobře známé statický port by měl být nakonfigurovaný v `Endpoints` konfigurace ServiceManifest.xml, např. 80 pro protokol HTTP nebo 443 pro protokol HTTPS. |
| ServiceFabricIntegrationOptions | Žádný | Použití `ServiceFabricIntegrationOptions.None` možnost při konfiguraci middleware pro integraci Service Fabric, takže služba není k pokusu o ověření příchozích požadavků pro jedinečný identifikátor. Externí uživatelé vaší aplikace nepoznáte jedinečné identifikační informace, které používá middleware. |
| Počet instancí | -1 | V typické případy použití, nastavování počet instancí musí být nastavená na *-1*. To se provádí tak, aby instance je k dispozici na všech uzlech, které přijímají provoz z nástroje pro vyrovnávání zatížení. |

Pokud více externě vystavených službách sdílejí stejnou sadu uzlů, můžete použít ovladač HTTP.sys se jedinečné, ale stabilní cestu adresy URL. Můžete to provést změnou adresy URL použité při konfiguraci IWebHost. Všimněte si, že to platí pro ovladač HTTP.sys pouze.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Jen pro interní Bezstavová služba ASP.NET Core
Bezstavové služby, které jsou volány pouze z v rámci clusteru používejte jedinečné adresy URL a dynamicky přiřazované k zajištění spolupráce mezi více službami. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | I když používáte HTTP.sys pro interní bezstavové služby Kestrel je nejlepší serveru tak, aby víc instancí služby Sdílení hostitele.  |
| Konfigurace portu | dynamicky přiřadí | Víc replik stavové služby můžou sdílet proces hostitele nebo hostitelského operačního systému a proto bude nutné odlišné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | S přiřazením dynamický port toto nastavení zabrání problém chybné identity popsané výše. |
| InstanceCount | Všechny | Počet instancí nastavení lze nastavit na libovolnou hodnotu nezbytné pro provoz služby. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Jen pro interní stavová služba ASP.NET Core
Stavové služby, které jsou volány pouze z v rámci clusteru používejte dynamicky přidělovanou porty pro zajištění spolupráce mezi více službami. Doporučujeme následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | `HttpSysCommunicationListener` Není určen pro stavové služby, ve kterých repliky sdílejí hostitelský proces. |
| Konfigurace portu | dynamicky přiřadí | Víc replik stavové služby můžou sdílet proces hostitele nebo hostitelského operačního systému a proto bude nutné odlišné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | S přiřazením dynamický port toto nastavení zabrání problém chybné identity popsané výše. |

## <a name="next-steps"></a>Další postup
[Ladění aplikace Service Fabric pomocí Visual Studia](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
