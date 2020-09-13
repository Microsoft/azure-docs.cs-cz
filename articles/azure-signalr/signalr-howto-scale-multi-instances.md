---
title: Škálování s víc instancemi – služba signálu Azure
description: V mnoha scénářích škálování zákazník často potřebuje zřídit víc instancí a nakonfigurovat ho tak, aby je používal dohromady, aby se vytvořilo nasazení ve velkém měřítku. Například horizontálního dělení vyžaduje podporu více instancí.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439210"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Jak škálovat službu signálu pomocí více instancí?
Nejnovější sada SDK služby signalizace podporuje více koncových bodů pro instance služby Signal. Pomocí této funkce můžete škálovat souběžná připojení nebo je použít pro zasílání zpráv mezi oblastmi.

## <a name="for-aspnet-core"></a>Pro ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak přidat více koncových bodů z konfigurace?

Config pomocí klíče `Azure:SignalR:ConnectionString` nebo `Azure:SignalR:ConnectionString:` připojovacího řetězce služby signalizace.

Pokud klíč začíná `Azure:SignalR:ConnectionString:` , měl by být ve formátu `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` , kde `Name` a `EndpointType` jsou vlastnosti `ServiceEndpoint` objektu a jsou přístupné z kódu.

Připojovací řetězce s více instancemi můžete přidat pomocí následujících `dotnet` příkazů:

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak přidat více koncových bodů z kódu?

`ServicEndpoint`Je představena třída, která popisuje vlastnosti koncového bodu služby Azure Signal.
Pomocí sady SDK služby Azure Signaler můžete nakonfigurovat několik koncových bodů instance prostřednictvím:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Jak přizpůsobit směrovač koncových bodů?

Ve výchozím nastavení sada SDK používá [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) k výběru koncových bodů.

#### <a name="default-behavior"></a>Výchozí chování 
1. Směrování požadavků klientů

    Když klient `/negotiate` se serverem aplikace. Sada SDK ve výchozím nastavení **náhodně vybere** jeden koncový bod ze sady dostupných koncových bodů služby.

2. Směrování zpráv serveru

    Když se * posílá zpráva na konkrétní * * připojení * * * a cílové připojení se směruje na aktuální server, pošle se tato zpráva přímo do tohoto připojeného koncového bodu. V opačném případě se zprávy všesměrově vysílají do každého koncového bodu služby Azure Signal.

#### <a name="customize-routing-algorithm"></a>Přizpůsobení algoritmu směrování
Můžete vytvořit vlastní směrovač, pokud máte zvláštní znalosti, abyste určili, na které koncové body by měly zprávy jít.

Vlastní směrovač je definovaný níže jako příklad, kdy se skupiny začínající `east-` na vždycky vrátí ke koncovému bodu s názvem `east` :

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Další příklad, který přepisuje výchozí chování při vyjednání, na výběr koncových bodů závisí na umístění aplikačního serveru.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Nezapomeňte zaregistrovat směrovač do DI Container pomocí:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>Pro ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak přidat více koncových bodů z konfigurace?

Config pomocí klíče `Azure:SignalR:ConnectionString` nebo `Azure:SignalR:ConnectionString:` připojovacího řetězce služby signalizace.

Pokud klíč začíná `Azure:SignalR:ConnectionString:` , měl by být ve formátu `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` , kde `Name` a `EndpointType` jsou vlastnosti `ServiceEndpoint` objektu a jsou přístupné z kódu.

Do můžete přidat několik připojovacích řetězců více instancí `web.config` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak přidat více koncových bodů z kódu?

`ServicEndpoint`Je představena třída, která popisuje vlastnosti koncového bodu služby Azure Signal.
Pomocí sady SDK služby Azure Signaler můžete nakonfigurovat několik koncových bodů instance prostřednictvím:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Jak přizpůsobit směrovač?

Jediným rozdílem mezi signálem ASP.NET a signálem ASP.NET Core je typ kontextu http pro `GetNegotiateEndpoint` . Pro signál ASP.NET je typu [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) .

Níže je uveden vlastní příklad dohadování pro ASP.NET signál:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Nezapomeňte zaregistrovat směrovač do DI Container pomocí:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfigurace v různých scénářích pro různé oblasti

`ServiceEndpoint`Objekt má `EndpointType` vlastnost s hodnotou `primary` nebo `secondary` .

`primary` koncové body jsou upřednostňovanými koncovými body pro příjem klientského provozu a považují se za spolehlivější síťová připojení. `secondary` koncovým bodům se považují méně spolehlivá síťová připojení a používají se pouze k přebírání přenosů mezi servery, například při vysílání zpráv, nikoli pro přenos klientů na server.

V případech různých oblastí může být síť nestabilní. U jednoho serveru aplikace umístěného v *východní USA*se koncový bod služby Signal umístěný ve stejné *východní USA* oblasti dá nakonfigurovat jako `primary` a koncové body v jiných oblastech označených jako `secondary` . V této konfiguraci můžou koncové body služby v jiných oblastech **přijímat** zprávy z tohoto *východní USA* serveru aplikace, ale na tento aplikační server se nesměrují žádní klienti pro **různé oblasti** . Architektura se zobrazuje v následujícím diagramu:

![Mezi geografickým a geografickým infračerveným](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Když se klient pokusí použít `/negotiate` aplikační server s výchozím směrovačem, sada SDK **náhodně vybere** jeden koncový bod ze sady dostupných `primary` koncových bodů. Pokud primární koncový bod není k dispozici, sada SDK **náhodně vybere** ze všech dostupných `secondary` koncových bodů. Pokud je připojení mezi serverem a koncovým bodem služby aktivní, je koncový bod označený jako **dostupný** .

Pokud se v případě různých oblastí pokusí klient `/negotiate` s aplikačním serverem hostovaným v *východní USA*, ve výchozím nastavení vždycky vrátí `primary` koncový bod umístěný ve stejné oblasti. Pokud nejsou k dispozici všechny koncové body *východní USA* , klient bude přesměrován do koncových bodů v jiných oblastech. V části převzetí služeb při selhání níže se podrobně popisuje scénář.

![Normální dohadování](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Převzetí služeb při selhání

Pokud nejsou `primary` dostupné všechny koncové body, `/negotiate` z dostupných `secondary` koncových bodů klienta vybere. Tento mechanismus pro převzetí služeb při selhání vyžaduje, aby každý koncový bod měl sloužit jako `primary` koncový bod k aspoň jednomu aplikačnímu serveru.

![Převzetí služeb při selhání](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli o tom, jak nakonfigurovat více instancí ve stejné aplikaci pro scénáře škálování, horizontálního dělení a mezi oblastmi.

Ve scénářích pro vysokou dostupnost a zotavení po havárii lze také použít více koncových bodů.

> [!div class="nextstepaction"]
> [Nastavení služby signalizace pro zotavení po havárii a vysokou dostupnost](./signalr-concept-disaster-recovery.md)
