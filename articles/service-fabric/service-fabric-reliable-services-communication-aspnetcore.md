---
title: Komunikace pomocí ASP.NET Core | Dokumentace Microsoftu
description: Zjistěte, jak používat ASP.NET Core v bezstavových a stavových Reliable Services.
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
ms.date: 08/29/2018
ms.author: vturecek
ms.openlocfilehash: 384d0fa32b64706c9d9d9baa0e2e0bbb2ac3c522
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719592"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core v Service Fabric Reliable Services

ASP.NET Core je nové open-source a multiplatformní rozhraní pro vytváření moderních cloudových připojeného k Internetu aplikací, jako jsou webové aplikace, aplikace IoT a mobilních back-endů. 

Tento článek je podrobné příručce k hostování služby ASP.NET Core v Service Fabric Reliable Services pomocí **Microsoft.ServiceFabric.AspNetCore.** * sadu balíčků NuGet.

Úvodní kurz k ASP.NET Core v Service Fabric a postup získání nastavení vašeho vývojového prostředí najdete v tématu [vytvořit aplikaci .NET](service-fabric-tutorial-create-dotnet-app.md).

Zbývající část tohoto článku se předpokládá, že již máte zkušenosti s ASP.NET Core. Pokud ne, doporučujeme vám prostudovat [základy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core v Service Fabric prostředí

Aplikace ASP.NET Core a Service Fabric můžete spustit na .NET Core, jakož i úplné rozhraní .NET Framework. ASP.NET Core lze použít v Service Fabric dvěma různými způsoby:
 - **Hostovaný jako spustitelný soubor typu Host**. To se používá především k provozování existujících aplikací ASP.NET Core v Service Fabric s žádnými změnami kódu.
 - **Spouštět v službu Reliable**. To umožňuje lepší integraci s modulem runtime Service Fabric a stavové služby ASP.NET Core services.

Zbývající část tohoto článku vysvětluje, jak používat ASP.NET Core uvnitř spolehlivé služby pomocí ASP.NET základních komponent integrace, které se dodávají pomocí Service Fabric SDK. 

## <a name="service-fabric-service-hosting"></a>Hostování služeb Service Fabric

V Service Fabric, jeden nebo více instancí nebo replik služby běží v *služby hostitelský proces*, spustitelný soubor, na kterém běží váš kód služby. Jako autor služby vlastníte hostitelského procesu služby a Service Fabric aktivuje a monitoruje za vás.

Tradiční technologie ASP.NET (až MVC 5) je těsně spjat s IIS prostřednictvím System.Web.dll. ASP.NET Core umožňuje oddělit mezi webovým serverem a webové aplikace. To umožňuje přenosné mezi jiné webové servery webových aplikací a také umožňuje webovým serverům *v místním prostředí*, což znamená, že můžete spustit webový server ve vašem vlastním procesu, na rozdíl od procesu, který je vlastněn vyhrazený webový serverový software, například službou IIS. 

Aby bylo možné kombinovat služby Service Fabric a ASP.NET, jako spustitelný soubor typu Host nebo spolehlivé služby, musí být schopni spustit ASP.NET uvnitř hostitelského procesu služby. ASP.NET Core s vlastním hostováním můžete to provést.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostování technologie ASP.NET Core v Reliable Service
Obvykle v místním prostředí aplikací ASP.NET Core vytvořit webového hostitele ve vstupní bod aplikace, například `static void Main()` metoda ve `Program.cs`. V takovém případě životního cyklu tomuto webovému hostiteli je vázán na životního cyklu procesu.

![Hostování v procesu ASP.NET Core][0]

Ale vstupní bod aplikace není na správném místě vytvořit WebHost spolehlivé služby, protože vstupní bod aplikace slouží pouze k registraci modulu runtime Service Fabric, typ služby tak, aby ji může vytvořit instance daného typu služby. Tomuto webovému hostiteli musí být vytvořené v Reliable Service samotný. V rámci hostitelského procesu služby instance služby a/nebo repliky můžete projít více životního cyklu. 

Instance spolehlivé služby představuje vaše služby třídu odvozenou z `StatelessService` nebo `StatefulService`. Komunikační balík pro službu je součástí `ICommunicationListener` implementaci ve třídě služby. `Microsoft.ServiceFabric.AspNetCore.*` Balíčky NuGet obsahovat implementace `ICommunicationListener` , který spouští a spravuje hostitele ASP.NET Core pro Kestrel nebo HttpSys spolehlivé služby.

![Hostování technologie ASP.NET Core v Reliable Service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners ASP.NET Core
`ICommunicationListener` Implementace pro Kestrel a HttpSys v `Microsoft.ServiceFabric.AspNetCore.*` balíčky NuGet podobné vzorce používání, ale provádět mírně odlišné akce specifické pro každý webový server. 

Obě naslouchací procesy komunikace poskytovat konstruktor, který používá následující argumenty:
 - **`ServiceContext serviceContext`**: `ServiceContext` Objekt, který obsahuje informace o spuštěné služby.
 - **`string endpointName`**: název `Endpoint` konfigurace v souboru ServiceManifest.xml. To je především pokud naslouchacích procesů dvou komunikačních liší: HttpSys **vyžaduje** `Endpoint` konfigurace, ale nikoli Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: výraz lambda, kterou implementujete, ve kterém se vytváří a vrácení `IWebHost`. To umožňuje nakonfigurovat `IWebHost` tak, jak byste normálně v aplikaci ASP.NET Core. Výraz lambda obsahuje adresu URL, který je generován pro vás v závislosti na integraci Service Fabric možnosti použít a `Endpoint` konfigurace, které zadáte. Že adresa URL můžete potom změnit nebo použít jako-je spustit webový server.

## <a name="service-fabric-integration-middleware"></a>Middleware pro integraci Service Fabric
`Microsoft.ServiceFabric.AspNetCore` Obsahuje balíček NuGet `UseServiceFabricIntegration` rozšiřující metody na `IWebHostBuilder` , který přidá Service Fabric s ohledem na middleware. Tento middleware konfiguruje Kestrel nebo HttpSys `ICommunicationListener` k registraci ve službě pojmenování Service Fabric Service adresu URL jedinečné služby a poté ověří požadavky klientů, aby se klienti připojují k požadovanou službu. To je nezbytné v prostředí sdílené hostitele, jako je Service Fabric, ve kterém můžete spustit na stejný fyzický nebo virtuální počítač více webových aplikací, ale nepoužívejte názvy hostitele, čímž zabráníte klientům omylem připojování ke službě nesprávné. Tento scénář je popsaný v následující části podrobněji.

### <a name="a-case-of-mistaken-identity"></a>Případ chybné identity
Repliky služby, bez ohledu na protokol, naslouchat na kombinaci IP: jedinečný port. Jakmile repliku služby zahájil naslouchání na koncový bod IP: port, sestavy služba pojmenování Service Fabric ve kterém je možné zjistit pomocí klienty nebo jiné služby pro tuto adresu koncového bodu. Pokud služby používat porty dynamicky přiřazené aplikace, repliku služby shodou použít stejný koncový bod IP: port jiné službě, která byla předtím na stejný fyzický nebo virtuální počítač. To může způsobit klientovi mistakely připojit ke službě nesprávné. Můžete k tomu dojít, pokud dojde k následujícímu pořadí událostí:

 1. Služby A naslouchá na 10.0.0.1:30000 přes protokol HTTP. 
 2. Klient řeší A služby a získá 10.0.0.1:30000 adresa
 3. Služby A přesune na jiný uzel.
 4. Služba B je umístěn na 10.0.0.1 a shodou používá stejný port 30000.
 5. Klient se pokusí připojit k A služby s 10.0.0.1:30000 adres v mezipaměti.
 6. Klient je teď úspěšně připojil ke službě, která není porozumění B je připojen k chybě služby.

To může způsobit chyby v náhodných intervalech, které může být obtížné diagnostikovat. 

### <a name="using-unique-service-urls"></a>Pomocí adresy URL služby jedinečný
Chcete-li tomu zabránit, můžete služby publikovat koncový bod na pojmenování Service s jedinečným identifikátorem a následně ověřit tento jedinečný identifikátor během požadavky klientů. Toto je kooperativní akce mezi službami v důvěryhodném prostředí nebezpečný tenanta. To neposkytuje služba zabezpečené ověřování v prostředí nebezpečný tenanta.

V případě důvěryhodného prostředí middleware, který se přidal `UseServiceFabricIntegration` metoda automaticky připojí jedinečný identifikátor na adresu, kterou je odeslána do služby pojmenování a ověří identifikátoru s každým požadavkem. Pokud se neshoduje identifikátor, middleware okamžitě vrátí odpověď HTTP 410 pryč.

Služby, které využívají dynamicky přiřazeného portu se ujistěte, použijte tento middleware.

Služby, které používají pevné jedinečné číslo portu není nutné tento problém v prostředí spolupráce. Pevné jedinečné číslo portu se obvykle používá pro externí koncové služby, které potřebujete pro klientské aplikace pro připojení k dobře známém portu. Například většiny webových aplikací přístupem k Internetu, bude používat port 80 nebo 443 pro připojení webového prohlížeče. V takovém případě by se neměla povolovat jedinečný identifikátor.

Následující diagram znázorňuje tok požadavku s middlewarem povoleno:

![Integrace ASP.NET Core v Service Fabric][2]

Kestrel a HttpSys `ICommunicationListener` implementace používá tento mechanismus přesně stejným způsobem. I když HttpSys může interně rozlišovat žádosti na základě jedinečných cest URL pomocí základního *http.sys* sdílení funkce, které funkce jsou portů *není* používané HttpSys `ICommunicationListener` implementace vzhledem k tomu, které způsobí 503 protokolu HTTP a protokolu HTTP 404 chybové kódy stavu v podle scénáře popsaného výše. Který zase ztěžuje pro klienty určit záměr chybu, jak 503 protokolu HTTP a protokolu HTTP 404 jsou již běžně používá k označení jiné chyby. Proto Kestrel i HttpSys `ICommunicationListener` implementace standardizovat používání na middleware poskytuje `UseServiceFabricIntegration` – metoda rozšíření tak, aby klienti nutné provést pouze koncový bod služby znovu přeložit akce na odpovědi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys v modelu Reliable Services
HttpSys je možné v Reliable Service pomocí importu **Microsoft.ServiceFabric.AspNetCore.HttpSys** balíček NuGet. Tento balíček obsahuje `HttpSysCommunicationListener`, implementace `ICommunicationListener`, který vám umožní vytvořit hostitele ASP.NET Core uvnitř spolehlivé služby pomocí HttpSys jako webový server.

HttpSys postavena [rozhraní API Windows HTTP serveru](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Tady se používá *http.sys* ovladač jádra používaný službou IIS ke zpracování požadavků HTTP a přesměrovávala je pro procesy spuštěné webové aplikace. To umožňuje více procesů na stejný fyzický nebo virtuální počítač pro hostování webových aplikací na stejném portu, jednoznačně rozlišit jedinečná cesta URL nebo název hostitele. Tyto funkce jsou užitečné v Service Fabric pro hostování více webů ve stejném clusteru.

>[!NOTE]
>Implementace HttpSys funguje pouze na platformě Windows.

Následující diagram znázorňuje, jak používá HttpSys *http.sys* ovladač jádra ve Windows pro sdílení portů:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys bezstavové služby
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

### <a name="httpsys-in-a-stateful-service"></a>HttpSys stavové služby

`HttpSysCommunicationListener` není aktuálně určena pouze pro stavové služby z důvodu komplikace se základní *http.sys* funkci Sdílení portu. Další informace najdete v tématu v následující části na přidělování dynamických portů httpsys. Pro stavové služby je Kestrel doporučené webový server.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu

`Endpoint` Konfigurace je nutná pro webové servery, které používají Windows HTTP serveru rozhraní API, včetně HttpSys. Webové servery, které používají rozhraní API Windows HTTP serveru musíte nejprve rezervovat jejich adresy URL s *http.sys* (to je obvykle provedeno pomocí [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) nástroje). Tato akce vyžaduje zvýšená oprávnění, které nemají ve výchozím nastavení vaše služby. Možnosti "http" nebo "https" `Protocol` vlastnost `Endpoint` konfigurace v *ServiceManifest.xml* se používají výhradně pro dáte pokyn, aby modul runtime Service Fabric zaregistrovat adresu URL pomocí  *ovladač HTTP.sys* na váš účet pomocí [ *silný zástupný znak* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) předponu adresy URL.

Například pro rezervaci `http://+:80` pro služby, je třeba použít následující konfiguraci v souboru ServiceManifest.xml:

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
Pro použití statického portu httpsys, zadejte číslo portu `Endpoint` konfigurace:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Použití HttpSys prostřednictvím dynamického portu
Chcete-li použít dynamicky přiřazeného portu httpsys, vynechejte `Port` vlastnost v `Endpoint` konfigurace:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Dynamický port přidělené `Endpoint` konfigurace obsahuje pouze jeden port *za hostitelský proces*. Aktuální model hostingu Service Fabric umožňuje více instancí služby a/nebo replik zajistit také jejich hostování v rámci stejného procesu, což znamená, že každý z nich bude sdílet stejný port při přidělování prostřednictvím `Endpoint` konfigurace. Více instancí HttpSys můžete sdílet port pomocí základního *http.sys* portu, sdílení funkce, ale nepodporuje `HttpSysCommunicationListener` kvůli komplikace přináší pro požadavky klientů. Použití dynamických portů je Kestrel doporučené webový server.

## <a name="kestrel-in-reliable-services"></a>Kestrel v modelu Reliable Services
Kestrel je možné v Reliable Service pomocí importu **Microsoft.ServiceFabric.AspNetCore.Kestrel** balíček NuGet. Tento balíček obsahuje `KestrelCommunicationListener`, implementace `ICommunicationListener`, který vám umožní vytvořit hostitele ASP.NET Core uvnitř spolehlivé služby pomocí Kestrel jako webový server.

Kestrel je že multiplatformní webový server pro ASP.NET Core podle libuv knihovnu asynchronní vstupně-výstupní operace napříč platformami. Na rozdíl od HttpSys, Kestrel nepoužívá správce centralizované koncového bodu, jako *http.sys*. A na rozdíl od HttpSys, Kestrel nepodporuje sdílení portů mezi více procesy. Každá instance Kestrel musíte použít jedinečný port.

![Kestrel][4]

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

V tomto příkladu instanci typu singleton `IReliableStateManager` je k dispozici do kontejneru pro vkládání závislosti tomuto webovému hostiteli. To není nezbytně nutné, ale umožňuje používat `IReliableStateManager` a spolehlivé kolekce v vaše metody akce kontroleru MVC.

`Endpoint` Se název konfigurace **není** poskytnuté `KestrelCommunicationListener` stavové služby. To je vysvětleno podrobněji v následující části.

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu
`Endpoint` Konfigurace není k využití Kestrel nutné. 

Kestrel je jednoduchý samostatný webový server; na rozdíl od HttpSys (nebo HttpListener) nemusí `Endpoint` konfigurace v *ServiceManifest.xml* protože nevyžaduje registraci adresy URL před spuštěním. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel pomocí statického portu
Statický port se dá nakonfigurovat v `Endpoint` konfigurace ServiceManifest.xml pro použití s Kestrel. I když to není nezbytně nutné, poskytuje dvě výhody:
 1. Pokud port nespadá do rozsahu portů aplikace, je otevřít přes bránu firewall operačního systému Service Fabric.
 2. Adresu URL, které prostřednictvím `KestrelCommunicationListener` použije tento port.

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

Pokud `Endpoint` konfigurace se nepoužívá, vypuštění názvu v `KestrelCommunicationListener` konstruktoru. V takovém případě se použije dynamický port. V části Další informace.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Použití Kestrel prostřednictvím dynamického portu
Kestrel nemůžete použít automatické port přiřazení z `Endpoint` konfigurace v souboru ServiceManifest.xml, protože port automatické přiřazení `Endpoint` konfigurace přiřadí jedinečný port na *proces hostitele*, a jeden hostitelský proces může obsahovat několik instancí Kestrel. Protože Kestrel nepodporuje sdílení portů, to nebude fungovat jako každá instance Kestrel musí být otevřen na jedinečný port.

Chcete-li používat dynamické přiřazování portu Kestrel, vynechejte `Endpoint` konfigurace v souboru ServiceManifest.xml zcela a nepředávají název koncového bodu pro `KestrelCommunicationListener` konstruktor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

V této konfiguraci `KestrelCommunicationListener` automaticky vybere nepoužitého portu z rozsahu portů aplikace.

## <a name="scenarios-and-configurations"></a>Scénáře a konfigurace
Tato část popisuje následující scénáře a poskytuje doporučené kombinaci webového serveru, konfigurace portů, možnosti integrace Service Fabric a různá nastavení dosáhnout správně fungující služby:
 - Externě vystavený Bezstavová služba ASP.NET Core
 - Jen pro interní Bezstavová služba ASP.NET Core
 - Jen pro interní stavová služba ASP.NET Core

**Externě vystavený** služby je ten, který zpřístupňuje koncový bod dostupný z mimo cluster, obvykle pomocí nástroje pro vyrovnávání zatížení.

**Pouze interní** služby je jedna je pouze jehož koncový bod dostupný z v rámci clusteru.

> [!NOTE]
> Koncové body stavové služby obecně by neměly být vystaveny k Internetu. Clustery, které se nachází za nástrojem pro vyrovnávání zatížení, které si nejsou vědomi řešení služby Service Fabric, jako je Azure Load Balancer, nebude moci vystavit stavové služby, protože nástroj pro vyrovnávání zatížení nebude moci najít a směrovat provoz na příslušné replika stavové služby. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Externě vystavený bezstavové služby ASP.NET Core
Kestrel je doporučené webový server pro front-endové služby, které zpřístupňují koncové body HTTP externí, přístupem k Internetu. Na Windows HttpSys slouží k zajistit možnost sdílení portu, který umožňuje hostovat několik webových služeb na stejnou sadu uzlů pomocí stejný port, které jsou rozlišené pomocí názvu hostitele nebo cestu, bez nutnosti spoléhat se na front-endového serveru proxy nebo brány poskytují směrování protokolu HTTP.
 
Pokud přístup k Internetu, používejte bezstavovou službu dobře známé a stabilní koncový bod, který je dostupný prostřednictvím nástroje pro vyrovnávání zatížení. Toto je adresa URL bude poskytovat uživatelům vaší aplikace. Doporučuje se následující konfigurace:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | Kestrel je upřednostňovaný webový server, jako je podporováno ve Windows a Linux. |
| Konfigurace portu | statická | Dobře známé statický port by měl být nakonfigurovaný v `Endpoints` konfigurace ServiceManifest.xml, např. 80 pro protokol HTTP nebo 443 pro protokol HTTPS. |
| ServiceFabricIntegrationOptions | Žádný | `ServiceFabricIntegrationOptions.None` By měl být použit při konfiguraci middleware pro integraci Service Fabric, aby služba nepokusí k ověření příchozích požadavků pro jedinečný identifikátor. Externí uživatelé vaší aplikace nepoznáte jedinečné identifikační informace používané middleware. |
| Počet instancí | -1 | V typické případy použití musí být počet instancí nastavení nastaven na "-1" tak, aby instance je k dispozici na všech uzlech, které přijímají provoz z nástroje pro vyrovnávání zatížení. |

Pokud více externě vystavených službách sdílejí stejnou sadu uzlů, HttpSys jde použít s jedinečné, ale stabilní cestu adresy URL. Toho můžete docílit, že do adresy URL použité při konfiguraci IWebHost. Všimněte si, že to platí pouze pro HttpSys.

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
Bezstavové služby, které jsou volány pouze z v rámci clusteru používejte jedinečné adresy URL a dynamicky přiřazované k zajištění spolupráce mezi více službami. Doporučuje se následující konfigurace:

|  |  | **Poznámky** |
| --- | --- | --- |
| Webový server | Kestrel | I když HttpSys slouží pro interní bezstavové služby, je Kestrel doporučené serveru tak, aby víc instancí služby Sdílení hostitele.  |
| Konfigurace portu | dynamicky přiřadí | Víc replik stavové služby můžou sdílet proces hostitele nebo hostitelského operačního systému a proto bude nutné odlišné porty. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | S přiřazením dynamický port toto nastavení zabrání problém chybné identity popsané výše. |
| InstanceCount | libovolné | Počet instancí nastavení lze nastavit na libovolnou hodnotu nezbytné pro provoz služby. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Jen pro interní stavová služba ASP.NET Core
Stavové služby, které jsou volány pouze z v rámci clusteru používejte dynamicky přidělovanou porty pro zajištění spolupráce mezi více službami. Doporučuje se následující konfigurace:

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
