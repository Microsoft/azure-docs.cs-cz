---
title: Vzdálená komunikace služby pomocí jazyka C# v Service Fabric | Microsoft Docs
description: Vzdálená komunikace Service Fabric umožňuje služby a klienti komunikovat se službami C# s použitím vzdálené volání procedury.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: ad56580e73c06acff95b3146f6dc2d83ab2ba3ae
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945968"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Vzdálená komunikace služby v jazyce C# se službami Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou svázané s konkrétní komunikační protokol nebo zásobníku, například WebAPI, Windows Communication Foundation (WCF) nebo jiné spolehlivé služby framework poskytuje mechanismus vzdálenou komunikaci rychle a snadno nastavit vzdálených volání procedur pro služby. Tento článek popisuje postup nastavení vzdálených volání procedur pro služby, které jsou napsané v C#.

## <a name="set-up-remoting-on-a-service"></a>Nastavování vzdálené komunikace na službě
Nastavení vzdálené komunikace pro služby se provádí ve dvou jednoduchých kroků:

1. Vytvořte rozhraní služby k implementaci. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedury vaší služby. Metody musí být vrácení úloh asynchronních metod. Musí implementovat rozhraní `Microsoft.ServiceFabric.Services.Remoting.IService` signál, že služba má vzdálené komunikace rozhraní.
2. Použijte vzdálenou komunikaci naslouchací proces ve službě. Je RemotingListener `ICommunicationListener` implementace, která poskytuje funkce vzdálené komunikace. `Microsoft.ServiceFabric.Services.Remoting.Runtime` Obor názvů obsahuje metody rozšíření`CreateServiceRemotingListener` pro bezstavové a stavové služby, které lze použít k vytvoření naslouchací proces vzdálenou komunikaci pomocí protokolu přenosu výchozí vzdálenou komunikaci.

>[!NOTE]
>`Remoting` Obor názvů je k dispozici jako samostatný balíček NuGet s názvem `Microsoft.ServiceFabric.Services.Remoting`

Například následující bezstavové služby zpřístupní jedné metody přes vzdálené volání procedury získat "Hello World".

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Argumenty a návratové typy v rozhraní služby může být jakékoli jednoduchý, komplexní nebo vlastní typy, ale musí být serializovatelný pomocí .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Volání metody vzdálené služby
Volání metod pro službu pomocí vzdálené komunikace zásobníku se provádí pomocí místní proxy server a služby pomocí `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` třídy. `ServiceProxy` Metoda vytvoří místní proxy server pomocí stejné rozhraní, který implementuje službu. Pomocí proxy můžete volat metody na rozhraní vzdáleně.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Vzdálená komunikace framework rozšíří výjimky vyvolané službu do klienta. V důsledku toho, při použití `ServiceProxy`, klient je zodpovědná za zpracování výjimky vyvolané službu.

## <a name="service-proxy-lifetime"></a>Doby platnosti Proxy služby
Vytvoření ServiceProxy je lightweight operace, takže si můžete vytvořit libovolný počet. je nutné. Instance Proxy služby lze znovu použít, dokud jsou potřeba. Pokud vzdálené volání procedury, vyvolá výjimku, stále můžete opakovaně použít stejnou instanci proxy serveru. Každý ServiceProxy obsahuje komunikace klienta používá k odeslání zprávy prostřednictvím sítě. Při volání vzdáleného volání, budou provedeny interní kontroly k určení, jestli komunikace klienta je platný. Na základě výsledků z těchto kontrol komunikace klienta je znovu vytvořit v případě potřeby. Proto pokud dojde k výjimce, není nutné znovu vytvořit `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Doba platnosti ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) je objekt factory, který vytváří instance proxy pro různé vzdálené komunikace rozhraní. Pokud používáte rozhraní api `ServiceProxy.Create` k vytváření proxy serveru, pak rozhraní framework vytvoří ServiceProxy typu singleton.
Je vhodné vytvořit jednu ručně, až budete potřebovat k přepsání [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) vlastnosti.
Vytvoření objektu pro vytváření je náročná operace. ServiceProxyFactory udržuje interní mezipaměť komunikace klienta.
Osvědčeným postupem je pro ukládání do mezipaměti ServiceProxyFactory jako dlouho.

## <a name="remoting-exception-handling"></a>Vzdálená komunikace zpracování výjimek
Všechny vzdálené výjimky vyvolané rozhraní API služby jsou odesílány zpět do klienta v AggregateException. RemoteExceptions by měla být kontraktu serializovatelný; Pokud tomu tak není, vyvolá proxy rozhraní API [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) s Chyba serializace v ní.

ServiceProxy zpracuje všechny výjimky převzetí služeb při selhání pro oddíl služby, kterou je vytvořeno. Ho znovu přeloží koncových bodů, pokud jsou výjimky převzetí služeb při selhání (bez přechodná výjimky) a opakuje volání s správný koncový bod. Počet opakovaných pokusů pro převzetí služeb při selhání výjimky jsou neomezené.
Pokud dojde k přechodné výjimky, proxy server opakuje volání.

Výchozí parametry opakování se poskytují podle [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Tyto hodnoty můžete nakonfigurovat pomocí předání objektu OperationRetrySettings ServiceProxyFactory konstruktor.

## <a name="how-to-use-the-remoting-v2-stack"></a>Jak používat vzdálenou komunikaci V2 zásobníku

Od verze balíčku NuGet vzdálenou komunikaci 2.8 máte možnost použít vzdálenou komunikaci V2 zásobníku. Zásobník V2 vzdálené komunikace je další původce a poskytuje funkce, jako jsou vlastní serializace a více modulární rozhraní API.
Kód šablony budou nadále používat vzdálenou komunikaci V1 zásobníku.
Vzdálená komunikace V2 není kompatibilní s V1 (předchozí vzdálené komunikace zásobníku), proto postupujte podle pokynů na [postup upgradu z verze 1 na V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) bez dopadu na dostupnost služeb.

Následující přístupy jsou dostupné k povolení zásobníku V2.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Použití zásobníku V2 pomocí atributu sestavení

Tyto kroky změnit šablonu kódu pro použití v zásobníku V2 pomocí atributu sestavení.

1. Změnit prostředku koncového bodu z `"ServiceEndpoint"` k `"ServiceEndpointV2"` v service manifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Použití `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` rozšíření metodu pro vytvoření naslouchací procesy vzdálené komunikace (stejná pro V1 a V2).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Označte sestavení obsahující vzdálené komunikace rozhraní s `FabricTransportServiceRemotingProvider` atribut.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```

V projektu klienta se nevyžadují žádné změny kódu.
Vytvořte sestavení klienta s sestavení rozhraní a ujistěte se, že se používá atribut sestavení uvedený výše.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Použití zásobníku V2 pomocí explicitní třídy V2

Jako alternativu k použití atributu sestavení může být rovněž povolena zásobníku V2 pomocí explicitní třídy V2.

Tyto kroky změnit šablonu kódu pro použití v zásobníku V2 pomocí explicitní třídy V2.

1. Změnit prostředku koncového bodu z `"ServiceEndpoint"` k `"ServiceEndpointV2"` v service manifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Použití [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` oboru názvů.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Použití [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` oboru názvů k vytvoření klientů.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Postup upgradu ze vzdálené komunikace V1 na vzdálenou komunikaci V2.
Chcete-li upgrade z verze 1 na V2, krok 2 upgrady jsou povinné. Následující kroky jsou prováděna v pořadí uvedené.

1. Upgrade služby V1 službě V2 pomocí CompactListener atribut.
Tato změna zajišťuje, že služba naslouchá na V1 a V2 naslouchací proces.

    (a) přidáte prostředek koncový bod s názvem jako "ServiceEndpointV2" v service manifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) použít následující metody rozšíření k vytvoření naslouchacího procesu vzdálené komunikace.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) přidejte atribut sestavení na vzdálené komunikace rozhraní používat CompatListener a V2 klienta.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Upgrade klienta V1 V2 klienta pomocí atribut V2 klienta.
Tento krok zajistí, že klient používá zásobník V2.
Žádná změna v projektu nebo služba Klient je vyžadován. Sestavení projektů klienta s sestavení aktualizované rozhraní je dostačující.

3. Tento krok je volitelný. Pomocí atributu V2Listener a pak upgradovat službu V2.
Tento krok zajistí, že služby naslouchá jenom na V2 naslouchací proces.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Jak používat vlastní serializace s V2 vzdálenou komunikaci.
Následující příklad používá serializace Json s V2 vzdálenou komunikaci.
1. Implementujte rozhraní IServiceRemotingMessageSerializationProvider k zajištění implementace vlastní serializace.
    Zde je fragment kódu na tom, jak vypadá implementace.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    Přepište výchozí zprostředkovatel serializace s JsonSerializationProvider pro vzdálenou komunikaci naslouchacího procesu.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Přepište výchozí zprostředkovatel serializace s JsonSerializationProvider objektu pro vytváření vzdálené komunikace klienta.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Další postup
* [Webové rozhraní API s OWIN v spolehlivé služby](service-fabric-reliable-services-communication-webapi.md)
* [WCF komunikaci se službami Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Zabezpečení komunikace pro spolehlivé služby](service-fabric-reliable-services-secure-communication.md)

