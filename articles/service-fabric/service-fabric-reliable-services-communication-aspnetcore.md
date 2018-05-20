---
title: Služba komunikace s ASP.NET Core | Microsoft Docs
description: Další informace o použití ASP.NET Core v bezzstavovými i stavovými službami spolehlivé.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 7786e08e04d2ebce757b4c47b8ed599036c95958
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Jádro ASP.NET v Service Fabric spolehlivé služby

ASP.NET Core je nové open source a napříč platformami architektura pro vytváření moderní cloudové aplikace založené na připojené k Internetu, jako třeba webové aplikace, aplikace IoT a back-EndY mobilních. 

Tento článek představuje podrobný průvodce pro hostování služeb ASP.NET Core v Service Fabric spolehlivé služby pomocí **Microsoft.ServiceFabric.AspNetCore.** * sadu balíčků NuGet.

Úvodní kurz ASP.NET Core v Service Fabric a pokyny pro nastavení vývojového prostředí najdete v tématu [vytvořit aplikaci .NET](service-fabric-tutorial-create-dotnet-app.md).

Zbývající část tohoto článku předpokládá, že jste již obeznámeni s ASP.NET Core. Pokud ne, doporučujeme čtení [ASP.NET Core Základy](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core v prostředí Service Fabric

Když aplikace ASP.NET Core můžete spustit na .NET Core nebo na úplné rozhraní .NET Framework, Service Fabric služby aktuálně lze spustit jen u úplné rozhraní .NET Framework. To znamená, když vytváříte služby ASP.NET Core Service Fabric, musí stále cíle úplné rozhraní .NET Framework.

ASP.NET Core lze použít dvěma různými způsoby v Service Fabric:
 - **Hostovaný jako spustitelný soubor hosta**. To slouží především ke spouštění existujících aplikací ASP.NET Core v Service Fabric beze změn kódu.
 - **Spustit uvnitř spolehlivě**. To umožňuje lepší integraci s modulem runtime Service Fabric a umožňuje stavová ASP.NET Core services.

Zbývající část tohoto článku vysvětluje, jak pomocí ASP.NET Core uvnitř spolehlivě součásti integračních služeb ASP.NET Core, které jsou součástí pomocí Service Fabric SDK. 

## <a name="service-fabric-service-hosting"></a>Hostování služeb Service Fabric

V Service Fabric, jeden nebo více instancí a/nebo repliky služby běžet v *procesu hostitele služby*, spustitelný soubor, který spouští službu kódu. Můžete jako autor služby vlastní proces hostitele služby a aktivuje Service Fabric a sleduje za vás.

Tradiční ASP.NET (až MVC 5) je úzce párované pro službu IIS prostřednictvím System.Web.dll. ASP.NET Core zajišťuje oddělení mezi webovým serverem a webové aplikace. To umožňuje webových aplikací přenosný mezi jiné webové servery a také umožňuje webové servery jako *hostovanou na vlastním*, což znamená, že můžete spustit webovém serveru vlastní proces a proces, který je vlastněn vyhrazený webový software serveru jako je například IIS. 

Ke kombinování služby Service Fabric a ASP.NET, jako spustitelný soubor hosta nebo v spolehlivě, musí být možné spustit ASP.NET uvnitř vaší služby hostitelský proces. Vlastní hostování ASP.NET Core můžete to udělat.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostování ASP.NET Core v spolehlivě.
Obvykle vlastním hostováním aplikací ASP.NET Core vytvořte tomuto webovému hostiteli v vstupní bod aplikace, například `static void Main()` metoda v `Program.cs`. V takovém případě je vázána životní cyklus k tomuto webovému hostiteli životního cyklu procesu.

![Hostování v procesu ASP.NET Core][0]

Ale vstupní bod aplikace není na správném místě vytvořit tomuto webovému hostiteli spolehlivé služby, protože vstupní bod aplikace slouží pouze k registraci typ služby s modulem runtime Service Fabric, aby může vytvořit instance tohoto typu služby. K tomuto webovému hostiteli by měl být vytvořen v spolehlivě sám sebe. V rámci procesu hostitele služby instance služby nebo repliky můžete projít více životní cykly. 

Instance spolehlivé služby je reprezentována třídě služby odvozování z `StatelessService` nebo `StatefulService`. Je součástí komunikačního balíku pro službu `ICommunicationListener` implementace ve třídě služby. `Microsoft.ServiceFabric.Services.AspNetCore.*` Balíčky NuGet obsahovat implementace `ICommunicationListener` , spouštět a spravovat k tomuto webovému hostiteli ASP.NET Core Kestrel nebo HttpSys v spolehlivě.

![Hostování ASP.NET Core v spolehlivě.][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners ASP.NET Core
`ICommunicationListener` Implementace pro Kestrel a HttpSys v `Microsoft.ServiceFabric.Services.AspNetCore.*` balíčky NuGet podobné použití vzorce ale provádět mírně odlišné akce, které jsou specifické pro každý webový server. 

Obě naslouchací procesy komunikace poskytují konstruktor, který má následující argumenty:
 - **`ServiceContext serviceContext`**: `ServiceContext` Objekt, který obsahuje informace o spuštěné služby.
 - **`string endpointName`**: název `Endpoint` konfigurace v ServiceManifest.xml. Toto je primárně kde naslouchací procesy komunikace dvě liší: HttpSys **vyžaduje** `Endpoint` konfigurace, zatímco Kestrel neexistuje.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: lambda, který implementujete, ve kterém se vytváří a zpět `IWebHost`. To vám umožňuje nakonfigurovat `IWebHost` způsob, jakým byste obvykle v aplikaci ASP.NET Core. Argument lambda obsahuje adresu URL, která se generují můžete v závislosti na integraci Service Fabric možnostech, můžete použít a `Endpoint` konfigurace, které zadáte. Aby adresa URL lze potom změnit nebo použít jako-je spustí webový server.

## <a name="service-fabric-integration-middleware"></a>Middleware integrace Service Fabric
`Microsoft.ServiceFabric.Services.AspNetCore` Balíček NuGet obsahuje `UseServiceFabricIntegration` rozšiřující metody na `IWebHostBuilder` , přidá middleware podporující služby prostředků infrastruktury. Tento middleware nakonfiguruje Kestrel nebo HttpSys `ICommunicationListener` při registraci adresy URL jedinečné služby s Service Fabric Naming Service a poté ověří požadavky klientů, aby se klienti připojují k službě správné. To je nezbytné v prostředí s sdílené hostitele, jako jsou Service Fabric, kde můžete spustit na stejný fyzický nebo virtuální počítač více webových aplikací, ale nepoužívejte názvy hostitelů jedinečný, čímž zabráníte klientům omylem připojení ke službě nesprávný. Tento scénář je podrobně popsaná v další v další části.

### <a name="a-case-of-mistaken-identity"></a>V případě chybné identifikace
Repliky služby, bez ohledu na protokol, naslouchat na kombinaci jedinečné IP: port. Jakmile repliku služby bylo zahájeno naslouchání na koncový bod IP: port, sestav služby prostředků infrastruktury služby DNS kde můžete zjistit pomocí klienty nebo jiné služby pro tuto adresu koncového bodu. Pokud služby používat porty dynamicky přiřadit aplikace, může repliku služby shodou použijte stejný koncový bod IP: port jiné službě, která byla předtím na stejný fyzický nebo virtuální počítač. To může způsobit klienta tak, aby mistakely připojit ke službě nesprávný. K tomu může dojít, pokud dojde k následujícímu pořadí událostí:

 1. Služby A naslouchá na 10.0.0.1:30000 přes protokol HTTP. 
 2. Klienta přeloží A služby a získá adresu 10.0.0.1:30000
 3. Služby A přesune do jiného uzlu.
 4. Služba B je umístěn na 10.0.0.1 a shodou používá stejný port 30000.
 5. Klient se pokusí připojit k služby A s 10.0.0.1:30000 adres v mezipaměti.
 6. Klient je nyní úspěšně připojen na službu, která není porozumění ho B je připojena k nesprávné službě.

To může způsobit chyby v náhodných intervalech, které může být obtížné diagnostikovat. 

### <a name="using-unique-service-urls"></a>Pomocí adresy URL jedinečné služby
Chcete-li tomu zabránit, můžete služby účtovat koncový bod služby DNS s jedinečným identifikátorem a potom ověřit tento jedinečný identifikátor během požadavky klientů. Toto je akce spolupráci mezi službami v případě důvěryhodného prostředí nepřátelských klienta. To neposkytuje zabezpečené služby ověřování v prostředí s nepřátelských klienta.

V případě důvěryhodného prostředí middleware, který přidává `UseServiceFabricIntegration` metoda automaticky připojí jedinečný identifikátor pro adresu, která je odeslána do služby DNS a ověří, že identifikátor na každý požadavek. Pokud identifikátor neodpovídá, middleware okamžitě vrátí odpověď HTTP 410 zmizel.

Služby využívající port dynamicky přiřadit měli používat tento middlewaru.

Služby, které používají pevné jedinečné číslo portu není nutné tento problém v prostředí spolupráci. Pevné jedinečné číslo portu se obvykle používá pro externě směřujících služby, které potřebují dobře známé port pro klientské aplikace pro připojení k. Například většina internetových webových aplikací používat port 80 nebo 443 pro webové prohlížeče připojení. V takovém případě by nemělo být povoleno jedinečný identifikátor.

Následující diagram zobrazuje tok požadavku s middlewarem povoleno:

![Integrace služby Fabric ASP.NET Core][2]

Kestrel a HttpSys `ICommunicationListener` implementace použít tento mechanismus stejným způsobem. I když HttpSys může odlišovat interně požadavků podle jedinečné cesty adresy URL pomocí základní *http.sys* funkce, která je funkce Sdílení portů *není* používané HttpSys `ICommunicationListener` implementace vzhledem k tomu, které způsobí HTTP 503 a HTTP 404 chyba stavové kódy ve scénáři popsané výše. Naopak pak bude velmi obtížné pro klienty určit záměr chyby, jako HTTP 503 a HTTP 404 již běžně se používají k označení dalších chyb. Proto Kestrel i HttpSys `ICommunicationListener` implementace standardizovat na middleware poskytované `UseServiceFabricIntegration` metoda rozšíření tak, aby klienti nutné provést pouze koncového bodu služby znovu vyřešit akce HTTP 410 odpovědi.

## <a name="httpsys-in-reliable-services"></a>HttpSys v spolehlivé služby
HttpSys mohou být používány spolehlivě importováním **Microsoft.ServiceFabric.AspNetCore.HttpSys** balíček NuGet. Tento balíček obsahuje `HttpSysCommunicationListener`, implementace `ICommunicationListener`, který vám umožní vytvořit ASP.NET Core tomuto webovému hostiteli uvnitř spolehlivě pomocí HttpSys jako webový server.

HttpSys je založený na [rozhraní API systému Windows HTTP serveru](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Tato služba využívá *http.sys* ovladač jádra používaný službou IIS ke zpracování požadavků HTTP a směrovat je pro procesy spuštěné webové aplikace. To umožňuje více procesů na stejný fyzický nebo virtuální počítač na hostitele webové aplikace na stejném portu, od sebe jednoznačně rozlišeny jedinečná cesta adresy URL nebo název hostitele. Tyto funkce jsou užitečné v Service Fabric pro hostování více webů ve stejném clusteru.

Následující diagram znázorňuje, jak HttpSys používá *http.sys* ovladač jádra v systému Windows pro sdílení portů:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys v bezstavové služby
Použít `HttpSys` v bezstavové služby přepsat `CreateServiceInstanceListeners` metoda a vraťte se `HttpSysCommunicationListener` instance:

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

### <a name="httpsys-in-a-stateful-service"></a>HttpSys v stavové služby

`HttpSysCommunicationListener` není aktuálně určený pro použití v stavové služby z důvodu komplikace se základní *http.sys* funkce Sdílení portů. Další informace najdete v následující části na dynamický port přidělení httpsys. Pro stavové služby je Kestrel doporučené webového serveru.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu

`Endpoint` Konfigurace je nutná pro webové servery, které používají rozhraní API systému Windows HTTP serveru, včetně HttpSys. Webové servery, které používají rozhraní API systému Windows HTTP serveru, musíte nejprve rezervovat jejich adresu URL s *http.sys* (to se obvykle dosahuje [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) nástroj). Tato akce vyžaduje zvýšená oprávnění, které nemají vašim službám ve výchozím nastavení. Možnosti "http" nebo "https" `Protocol` vlastnost `Endpoint` konfigurace v *ServiceManifest.xml* používají určený speciálně pro dá pokyn modulu runtime Service Fabric registrace adresy URL s  *ovladač HTTP.sys* na váš jménem pomocí [ *silné zástupné* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) předponu adresy URL.

Například můžete vyhradit `http://+:80` pro služby, je třeba použít následující konfigurace v ServiceManifest.xml:

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

#### <a name="use-httpsys-with-a-static-port"></a>HttpSys pomocí statického portu
Pro použití statického portu httpsys, zadejte číslo portu v `Endpoint` konfigurace:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Pomocí HttpSys dynamický port
Pro použití dynamicky přiřazeného portu httpsys, vynechejte `Port` vlastnost `Endpoint` konfigurace:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Všimněte si, že dynamický port přidělena `Endpoint` konfigurace poskytuje jenom jeden port *za hostitelský proces*. Aktuální model hostování Service Fabric umožňuje více instancí služby a repliky pro hostování v rámci jednoho procesu, což znamená, že každé z nich budou sdílet stejný port při přidělení prostřednictvím `Endpoint` konfigurace. Více instancí HttpSys můžete sdílet port pomocí základní *http.sys* nepodporuje sdílení funkce, ale které portů `HttpSysCommunicationListener` z důvodu komplikace přináší pro požadavky klientů. Pro používání dynamických portů je Kestrel doporučené webového serveru.

## <a name="kestrel-in-reliable-services"></a>Kestrel v spolehlivé služby
Kestrel mohou být používány spolehlivě importováním **Microsoft.ServiceFabric.AspNetCore.Kestrel** balíček NuGet. Tento balíček obsahuje `KestrelCommunicationListener`, implementace `ICommunicationListener`, který vám umožní vytvořit ASP.NET Core tomuto webovému hostiteli uvnitř spolehlivě pomocí Kestrel jako webový server.

Kestrel je že napříč platformami webový server pro ASP.NET Core podle libuv, knihovny a platformy asynchronní vstupně-výstupní operace. Na rozdíl od HttpSys, Kestrel nepoužívá manažera centralizované koncový bod, jako *http.sys*. A na rozdíl od HttpSys, Kestrel nepodporuje sdílení portů mezi více procesy. Každá instance Kestrel musíte použít jedinečnou port.

![kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel v bezstavové služby
Použít `Kestrel` v bezstavové služby přepsat `CreateServiceInstanceListeners` metoda a vraťte se `KestrelCommunicationListener` instance:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel v stavové služby
Použít `Kestrel` v stavové služby přepsat `CreateServiceReplicaListeners` metoda a vraťte se `KestrelCommunicationListener` instance:

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

V tomto příkladu instanci typu singleton daného `IReliableStateManager` zajišťuje kontejneru pro vkládání závislosti tomuto webovému hostiteli. To není nezbytně nutné, ale umožňuje používat `IReliableStateManager` a spolehlivé kolekcí ve vaší metody akce kontroleru MVC.

Všimněte si, že `Endpoint` se název konfigurace **není** poskytované `KestrelCommunicationListener` v stavové služby. To je vysvětlené podrobněji v následující části.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu
`Endpoint` Konfigurace není potřeba použít Kestrel. 

Kestrel je jednoduchý samostatný webový server; na rozdíl od HttpSys (nebo HttpListener), není nutné `Endpoint` konfigurace v *ServiceManifest.xml* protože nevyžaduje registraci adresy URL před spuštěním. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel pomocí statického portu
Statický port se dá nakonfigurovat v `Endpoint` konfigurace ServiceManifest.xml pro použití s Kestrel. I když to není nezbytně nutné, poskytuje dvě potenciální výhody:
 1. Pokud port nespadá rozsah portů aplikace, protože je otevřen přes bránu firewall operačního systému pomocí Service Fabric.
 2. Adresy URL poskytnuté na vás `KestrelCommunicationListener` bude tento port použít.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Pokud `Endpoint` je nakonfigurován, jeho název, musí být předán do `KestrelCommunicationListener` konstruktor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Pokud `Endpoint` není použita konfigurace, vypuštění názvu v `KestrelCommunicationListener` konstruktor. V takovém případě se použije dynamický port. Najdete v části Další informace.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Pomocí Kestrel dynamický port
Kestrel nemůžete použít automatické port přiřazení z `Endpoint` konfigurace v ServiceManifest.xml, protože port automatické přiřazení z `Endpoint` konfigurace přiřadí jedinečné port na *hostitelském procesu*, a proces jeden hostitel může obsahovat několik instancí Kestrel. Vzhledem k tomu, že Kestrel nepodporuje sdílení portů, to nebude fungovat jako každá instance Kestrel musí být otevřen na portu jedinečné.

Pokud chcete použít dynamický port přiřazení s Kestrel, jednoduše vynechejte `Endpoint` konfigurace v ServiceManifest.xml zcela a nepředávejte název koncového bodu na `KestrelCommunicationListener` konstruktor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

V této konfiguraci `KestrelCommunicationListener` automaticky vybere nepoužitého portu z rozsahu portů aplikace.

## <a name="scenarios-and-configurations"></a>Scénáře a konfigurace
Tato část popisuje následující scénáře a poskytuje doporučené kombinace webového serveru, konfiguraci portů, možností integrace Service Fabric a ostatní nastavení dosáhnout správně funguje služby:
 - Externě zveřejněné bezstavové služby ASP.NET Core
 - Pouze interní ASP.NET Core bezstavové služby
 - Pouze interní ASP.NET Core stavové služby

**Externě zveřejněné** služba je ten, který zpřístupní koncový bod dosažitelný z mimo cluster, obvykle pomocí služby Vyrovnávání zatížení.

**Jen interní** služby je jedním jejichž koncový bod je pouze dosažitelný z v rámci clusteru.

> [!NOTE]
> Koncové body stavové služby obecně by neměly přístup k Internetu. Clustery, které jsou za nástroje pro vyrovnávání zatížení, které neberou v řešení služby Service Fabric, jako je například nástroj pro vyrovnávání zatížení Azure nelze vystavit stavové služby, protože nebude moci vyhledat a směrování provozu na příslušné nástroje pro vyrovnávání zatížení replika stavové služby. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externě zveřejněné bezstavové služby ASP.NET Core
Kestrel je doporučené webovém serveru pro front-endové služby, které zveřejňují externí, internetových koncových bodů protokolu HTTP. V systému Windows poskytovat funkce Sdílení portu, který umožňuje hostování více webových služeb na stejnou sadu uzly používá stejný port, rozlišené pomocí názvu hostitele nebo cestu, bez nutnosti spoléhat se na front-endu proxy nebo brány poskytují směrování protokolu HTTP lze HttpSys.
 
Pokud přístup k Internetu, bezstavové služby by měl použít dobře známé a stabilní koncový bod, který je dostupný prostřednictvím Vyrovnávání zatížení. Toto je adresa URL bude poskytovat uživatelům vaší aplikace. Doporučuje se následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | kestrel | Kestrel je upřednostňovaný webového serveru, jako je podporovaná ve Windows a Linux. |
| Konfigurace portu | statická | Dobře známé statický port by měl být nakonfigurovaný v `Endpoints` konfigurace ServiceManifest.xml, jako třeba 80 pro protokol HTTP nebo 443 pro protokol HTTPS. |
| ServiceFabricIntegrationOptions | Žádný | `ServiceFabricIntegrationOptions.None` By měl být použit při konfiguraci Service Fabric integrace middleware tak, aby služba nebude pokoušet o ověření příchozích požadavků na jedinečný identifikátor. Externí uživatele vaší aplikace nebude vědět jedinečné identifikační informace používané middleware. |
| Počet instancí | -1 | V typické případy použití musí být počet instancí nastavení nastavena "-1", aby instance k dispozici na všech uzlech, jež přijímat přenosy z pro vyrovnávání zatížení. |

Více externě zveřejněné služeb sdílet stejnou sadu uzlů, lze nastavit HttpSys s jedinečné, ale stabilní cestu adresy URL. To můžete udělat změnou adresy URL poskytnuté při konfiguraci IWebHost. Všimněte si, že platí pouze pro HttpSys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Pouze interní bezstavové služby ASP.NET Core
Bezstavové služby, které jsou pouze volat v rámci clusteru musí používat jedinečné adresy URL a dynamicky přiřadit porty k zajištění spolupráce mezi více služeb. Doporučuje se následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | kestrel | I když HttpSys mohou být použity pro interní bezstavové služby, je Kestrel doporučené serveru tak, aby víc instancí služby pro sdílení hostitele.  |
| Konfigurace portu | dynamicky přiřadit | Víc replik stavové služby může sdílet proces hostitele nebo hostitelského operačního systému a proto bude nutné odlišné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | S přiřazením dynamický port toto nastavení zabrání chybné identifikace problém popsané výše. |
| InstanceCount | libovolné | Počet instancí nastavení lze nastavit na jakoukoli hodnotu potřebné pro provoz služby. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Pouze interní stavová služba ASP.NET Core
Stavové služby, které jsou pouze volat v rámci clusteru používejte porty dynamicky přiřazené k zajištění spolupráce mezi více služeb. Doporučuje se následující konfiguraci:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | kestrel | `HttpSysCommunicationListener` Není určen pro stavové služby, ve kterých repliky sdílet hostitelském procesu. |
| Konfigurace portu | dynamicky přiřadit | Víc replik stavové služby může sdílet proces hostitele nebo hostitelského operačního systému a proto bude nutné odlišné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | S přiřazením dynamický port toto nastavení zabrání chybné identifikace problém popsané výše. |

## <a name="next-steps"></a>Další postup
[Ladění aplikace Service Fabric pomocí sady Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
