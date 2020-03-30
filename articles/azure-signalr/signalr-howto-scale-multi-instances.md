---
title: Škálování s více instancemi – služba Azure SignalR
description: V mnoha scénářích škálování zákazník často potřebuje zřídit více instancí a nakonfigurovat je tak, aby je používalspolečně, aby vytvořil rozsáhlé nasazení. Například funkce je třeba podporovat více instancí.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158156"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Jak škálovat službu SignalR s více instancemi?
Nejnovější sada SDK služby SignalR podporuje více koncových bodů pro instance služby SignalR. Tuto funkci můžete použít ke škálování souběžných připojení nebo použít pro zasílání zpráv mezi oblastmi.

## <a name="for-aspnet-core"></a>Pro ASP.NET jádro

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak přidat více koncových bodů z config?

Config s `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` klíčem nebo pro připojovací řetězec signalr služby.

Pokud klíč začíná `Azure:SignalR:ConnectionString:`, by měl `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`být `Name` `EndpointType` ve formátu `ServiceEndpoint` , kde a jsou vlastnosti objektu a jsou přístupné z kódu.

Připojovací řetězce instancí `dotnet` můžete přidat pomocí následujících příkazů:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Jak přidat více koncových bodů z kódu?

Je `ServicEndpoint` zavedena třída popisující vlastnosti koncového bodu služby Azure SignalR.
Při použití sady Azure SignalR Service SDK můžete nakonfigurovat více koncových bodů instance prostřednictvím:
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

### <a name="how-to-customize-endpoint-router"></a>Jak přizpůsobit koncový směrovač?

Ve výchozím nastavení sada SDK používá k sběru koncových bodů [výchozí endpointrouter.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)

#### <a name="default-behavior"></a>Výchozí chování 
1. Směrování požadavků klienta

    Když `/negotiate` klient s aplikačním serverem. Ve výchozím nastavení sada SDK **náhodně vybere** jeden koncový bod ze sady dostupných koncových bodů služby.

2. Směrování zpráv serveru

    Při *odesílání zprávy na konkrétní **připojení**** a cílové připojení je směrováno na aktuální server, zpráva přejde přímo do tohoto připojeného koncového bodu. V opačném případě se zprávy vysílají do každého koncového bodu Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Přizpůsobení algoritmu směrování
Vlastní směrovač můžete vytvořit, pokud máte speciální znalosti k identifikaci koncových bodů, na které zprávy mají přejít.

Vlastní směrovač je definován níže jako příklad, když skupiny `east-` `east`začínající vždy přejdou na koncový bod s názvem :

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

Další příklad níže, který přepíše výchozí chování vyjednávání, chcete-li vybrat koncové body, závisí na tom, kde se nachází aplikační server.

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

Nezapomeňte zaregistrovat router do kontejneru DI pomocí:

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>Jak přidat více koncových bodů z config?

Config s `Azure:SignalR:ConnectionString` `Azure:SignalR:ConnectionString:` klíčem nebo pro připojovací řetězec signalr služby.

Pokud klíč začíná `Azure:SignalR:ConnectionString:`, by měl `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`být `Name` `EndpointType` ve formátu `ServiceEndpoint` , kde a jsou vlastnosti objektu a jsou přístupné z kódu.

Do programu `web.config`můžete přidat více připojovacích řetězců instancí:

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

Je `ServicEndpoint` zavedena třída popisující vlastnosti koncového bodu služby Azure SignalR.
Při použití sady Azure SignalR Service SDK můžete nakonfigurovat více koncových bodů instance prostřednictvím:

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

### <a name="how-to-customize-router"></a>Jak přizpůsobit router?

Jediný rozdíl mezi ASP.NET SignalR a ASP.NET Core SignalR je http typ kontextu pro `GetNegotiateEndpoint`. Pro ASP.NET SignalR je typu [IOwinContext.](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)

Níže je vlastní negotiate příklad pro ASP.NET SignalR:

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

Nezapomeňte zaregistrovat router do kontejneru DI pomocí:

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

## <a name="configuration-in-cross-region-scenarios"></a>Konfigurace ve scénářích mezi oblastmi

Objekt `ServiceEndpoint` má `EndpointType` vlastnost s `primary` `secondary`hodnotou nebo .

`primary`koncové body jsou upřednostňované koncové body pro příjem provozu klienta a jsou považovány za spolehlivější síťová připojení; `secondary` koncové body jsou považovány za méně spolehlivé síťová připojení a používají se pouze pro přenos serveru do klienta, například pro přenos zpráv z klienta, nikoli pro přenos mezi klienty.

V případech mezi oblastmi může být síť nestabilní. Pro jeden aplikační server umístěný v *USA – východ*– koncový bod služby SignalR umístěný ve stejné oblasti USA – *východ* lze nakonfigurovat jako `primary` koncové body v jiných oblastech označených jako `secondary`. V této konfiguraci mohou koncové body služby v jiných oblastech **přijímat** zprávy z tohoto serveru aplikací *v USA,* ale na tento aplikační server nebudou směrováni žádní klienti **napříč oblastmi.** Architektura je znázorněna na obrázku níže:

![Cross-Geo Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Když se `/negotiate` klient pokusí s aplikačním serverem s výchozím směrovačem, sada SDK `primary` náhodně **vybere** jeden koncový bod ze sady dostupných koncových bodů. Pokud primární koncový bod není k dispozici, sada SDK `secondary` pak náhodně vybere ze všech **dostupných** koncových bodů. Koncový bod je označen jako **dostupný,** pokud je aktivní připojení mezi serverem a koncovým bodem služby.

Ve scénáři mezi oblastmi, `/negotiate` když se klient pokusí s aplikačním serverem `primary` hostovaným v USA *–* ve výchozím nastavení vždy vrátí koncový bod umístěný ve stejné oblasti. Pokud nejsou k dispozici všechny koncové body *východní USA,* klient je přesměrován na koncové body v jiných oblastech. Část převzetí služeb při selhání níže podrobně popisuje scénář.

![Normální vyjednávání](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Převzetí služeb při selhání

Pokud `primary` všechny koncové body nejsou k `/negotiate` dispozici, výběry klienta z dostupných `secondary` koncových bodů. Tento mechanismus převzetí služeb při selhání `primary` vyžaduje, aby každý koncový bod sloužil jako koncový bod alespoň jeden aplikační server.

![Převzetí služeb při selhání](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli o tom, jak nakonfigurovat více instancí ve stejné aplikaci pro škálování, horizontálního oddílu a scénáře mezi oblastmi.

Více koncových bodů podporuje lze také použít ve scénářích vysoké dostupnosti a zotavení po havárii.

> [!div class="nextstepaction"]
> [Nastavení služby SignalR pro zotavení po havárii a vysokou dostupnost](./signalr-concept-disaster-recovery.md)
