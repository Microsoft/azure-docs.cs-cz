---
title: Jak škálovat s více instancemi pro služby Azure SignalR
description: V mnoha scénářích škálování často zákazník potřebuje ke zřízení více instancí a konfigurace se dají použít společně k vytvoření nasazení ve velkém měřítku. Například horizontálního dělení vyžaduje že podporu více instancí.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809237"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Jak škálovat služby SignalR s více instancemi?
Nejnovější sady SDK služby SignalR podporuje několik koncových bodů pro instance služby SignalR. Můžete pomocí této funkce lze škálovat souběžných připojení, nebo ho použít pro zasílání zpráv mezi různými oblastmi.

## <a name="for-aspnet-core"></a>Pro ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Postup přidání více koncových bodů z konfigurace došlo k chybě?

Konfigurace s klíčem `Azure:SignalR:ConnectionString` nebo `Azure:SignalR:ConnectionString:` pro připojovací řetězec služby SignalR.

Pokud klíč začíná `Azure:SignalR:ConnectionString:`, musí být ve formátu `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, kde `Name` a `EndpointType` jsou vlastnosti `ServiceEndpoint` objektů a jsou přístupné z kódu.

Můžete přidat více instancí připojovací řetězce s využitím následující `dotnet` příkazy:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Postup přidání více koncových bodů z kódu?

A `ServicEndpoint` třída se používá k popisu vlastnosti koncového bodu služby Azure SignalR.
Můžete nakonfigurovat několik koncových bodů instance při použití sady SDK služby Azure SignalR prostřednictvím:
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

### <a name="how-to-customize-endpoint-router"></a>Jak přizpůsobit směrovače koncový bod?

Ve výchozím nastavení, sada SDK používá [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) ke sbírání koncových bodů.

#### <a name="default-behavior"></a>Výchozí chování 
1. Směrování žádostí klienta

    Když klient `/negotiate` s aplikačním serverem. Ve výchozím nastavení, sady SDK **náhodně vybere** jeden koncový bod ze sady dostupných koncových bodů služby.

2. Směrování zpráv serveru

    Když * odeslání zprávy na konkrétní ** připojení *** a cílové připojení se směruje na aktuálním serveru, dostane zprávu přímo do tohoto koncového bodu připojení. V opačném případě jsou vysílali zprávy pro každý koncový bod Azure SignalR.

#### <a name="customize-routing-algorithm"></a>Přizpůsobení algoritmus směrování
Pokud máte zvláštní znalost k identifikaci jaké koncové body se měli zúčastnit zprávy, můžete vytvořit vlastní směrovače.

Vlastní směrovač je definován pod jako příklad při o skupiny začínající `east-` vždy přejděte na koncový bod s názvem `east`:

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

Vyjednávání níže, další příklad, který přepíše výchozí chování, vyberte koncové body, závisí na níž je umístěn server aplikace.

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

Nezapomeňte k registraci na směrovač pomocí kontejnerů DI:

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

## <a name="for-aspnet"></a>Pro technologii ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Postup přidání více koncových bodů z konfigurace došlo k chybě?

Konfigurace s klíčem `Azure:SignalR:ConnectionString` nebo `Azure:SignalR:ConnectionString:` pro připojovací řetězec služby SignalR.

Pokud klíč začíná `Azure:SignalR:ConnectionString:`, musí být ve formátu `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`, kde `Name` a `EndpointType` jsou vlastnosti `ServiceEndpoint` objektů a jsou přístupné z kódu.

Můžete přidat více instancí připojovací řetězce ke službě `web.config`:

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>Postup přidání více koncových bodů z kódu?

A `ServicEndpoint` třída se používá k popisu vlastnosti koncového bodu služby Azure SignalR.
Můžete nakonfigurovat několik koncových bodů instance při použití sady SDK služby Azure SignalR prostřednictvím:

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

### <a name="how-to-customize-router"></a>Jak přizpůsobit směrovače?

Jediným rozdílem mezi funkce SignalR technologie ASP.NET a funkce SignalR technologie ASP.NET Core je typ kontextu http pro `GetNegotiateEndpoint`. Pro funkci SignalR technologie ASP.NET, je [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) typu.

Níže je příklad vyjednávání vlastní funkce SignalR technologie ASP.NET:

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

Nezapomeňte k registraci na směrovač pomocí kontejnerů DI:

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

`ServiceEndpoint` Objekt má `EndpointType` vlastnost s hodnotou `primary` nebo `secondary`.

`primary` Koncové body jsou upřednostňované koncových bodů pro příjem komunikace s klienty a jsou považovány za spolehlivější síťové připojení; `secondary` koncové body jsou považovány za méně spolehlivé připojení k síti a jsou používány pouze pro aktuální server komunikace s klienty, například všesměrové vysílání zpráv, nikoli pro klienta a současně provoz serveru.

V případech, mezi různými oblastmi může nestabilní sítě. Pro jednu aplikaci server umístěný v *USA – východ*, koncový bod služby SignalR umístěné ve stejné *USA – východ* oblasti se dají konfigurovat jako `primary` a označení koncových bodů v jiných oblastech `secondary`. V této konfiguraci můžete koncové body služby v jiných oblastech **přijímat** zprávy z tohoto *USA – východ* aplikačního serveru, ale nebudou mít žádný **mezi různými oblastmi** klienti směrovat do Tento server aplikace. Architektuře můžete vidět v následujícím diagramu:

![Mezi geografickými Infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Když klient zkusí `/negotiate` s aplikační server s výchozí směrovač SDK **náhodně vybere** ze sady k dispozici jeden koncový bod `primary` koncových bodů. Pokud koncový bod je k dispozici, pak SDK **náhodně vybere** ze všech dostupných `secondary` koncových bodů. Koncový bod je označen jako **dostupné** při zachování připojení mezi serverem a koncový bod služby.

Ve scénáři mezi různými oblastmi, pokud se klient pokusí `/negotiate` s aplikačním serverem hostovaný v *USA – východ*, je výchozí vždy vrátí `primary` koncový bod umístěný ve stejné oblasti. Když všechny *USA – východ* koncových bodů nejsou k dispozici, klient se přesměruje do koncových bodů v jiných oblastech. Následující části převzetí služeb při selhání popisuje scénář podrobněji.

![Normální vyjednávání](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Převzetí služeb při selhání

Když všechny `primary` koncových bodů nejsou k dispozici, klienta `/negotiate` vybere z dostupných `secondary` koncových bodů. Tento mechanismus převzetí služeb při selhání vyžaduje, aby každý koncový bod by měl sloužit jako `primary` koncový bod serveru aspoň jednu aplikaci.

![Převzetí služeb při selhání](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Další postup

V této příručce jste se dozvěděli o tom, jak nakonfigurovat více instancí ve stejné aplikaci pro škálování, horizontálního dělení a scénáře mezi oblastmi.

Podporuje více koncových bodů lze také ve scénářích vysokou dostupnost a obnovení.

> [!div class="nextstepaction"]
> [Nastavení služby SignalR pro zotavení po havárii a vysoká dostupnost](./signalr-concept-disaster-recovery.md)
