---
title: Vzdálená komunikace služby pomocí jazyka C# v Service Fabric | Dokumentace Microsoftu
description: Vzdálená komunikace Service Fabric umožňuje klientů a služeb ke komunikaci s services v C# s použitím vzdálené volání procedury.
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
ms.openlocfilehash: 9609a0fa5599bd34fa52f7c0311369fb27aaf955
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951154"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Vzdálená komunikace služby v jazyce C# s využitím Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou vázány na konkrétní komunikační protokol nebo zásobníku, jako je například WebAPI, Windows Communication Foundation (WCF) nebo jiné rozhraní modelu Reliable Services poskytuje mechanismus vzdálené komunikace rychle a snadno nastavit vzdálené volání procedur pro služby. Tento článek popisuje, jak nastavit vzdálené volání procedur pro služby, které jsou napsané v C#.

## <a name="set-up-remoting-on-a-service"></a>Nastavování vzdálené komunikace na službu
Nastavování vzdálené komunikace služby se provádí v dva jednoduché kroky:

1. Vytvoření rozhraní pro vaši službu k implementaci. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedur pro vaši službu. Musí být metody vracející úlohy asynchronní metody. Musí implementovat rozhraní `Microsoft.ServiceFabric.Services.Remoting.IService` signál, že má služba vzdálené komunikace rozhraní.
2. Použijte naslouchací proces vzdálené komunikace v rámci služby. Je RemotingListener `ICommunicationListener` implementace, která poskytuje funkce vzdálené komunikace. `Microsoft.ServiceFabric.Services.Remoting.Runtime` Obor názvů obsahuje metodu rozšíření`CreateServiceRemotingListener` bezstavové a stavové služby, které slouží k vytvoření naslouchacího procesu vzdálené komunikace pomocí výchozí přenosový protokol vzdálené komunikace.

>[!NOTE]
>`Remoting` Je k dispozici jako samostatné balíček NuGet s názvem oboru názvů `Microsoft.ServiceFabric.Services.Remoting`

Například následující Bezstavová služba zveřejňuje jedinou metodu k získání "Hello World" přes vzdálené volání procedury.

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
> Argumenty a návratové typy v rozhraní služby může být libovolný jednoduchý, komplexní nebo vlastní typy, ale musí být serializovatelný pomocí .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Volání metod vzdálené služby
Volání metod na službě s použitím vzdálené komunikace zásobníku se provádí pomocí místního proxy serveru do služby pomocí `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` třídy. `ServiceProxy` Metoda vytvoří místní proxy server s použitím stejného rozhraní, které služba implementuje. Pomocí proxy můžete volat metody rozhraní vzdáleně postupem.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Platforma pro vzdálenou spolupráci úloha šíří výjimek vyvolaných v klientovi služby. V důsledku toho při použití `ServiceProxy`, klient je zodpovědný za zpracování výjimky vyvolané službou.

## <a name="service-proxy-lifetime"></a>Doba platnosti služby Proxy
Vytvoření ServiceProxy je nenáročná operace, takže si můžete vytvořit tolik, je nutné. Instance Proxy služby můžete použít opakovaně, dokud se v případě potřeby zapíná. Pokud vzdálené volání procedury vyvolá výjimku, stále můžete znovu použít stejnou instanci proxy serveru. Každý ServiceProxy obsahuje komunikace klienta používaný k posílání zpráv přenosu. Při vyvolávání vzdálené volání, jsou provedeny interní kontroly k určení, zda komunikace klienta je platný. Na základě výsledků kontrol komunikaci klienta se znovu vytvořit v případě potřeby. Proto pokud dojde k výjimce, nepotřebujete znovu vytvořit `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Doba života ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) je objekt factory, který vytváří instance serveru proxy pro různé vzdálené komunikace rozhraní. Pokud používáte rozhraní api `ServiceProxy.Create` k vytváření proxy server, vytvoří rozhraní ServiceProxy typu singleton.
Je vhodné vytvořit jeden ručně, když budete chtít přepsat [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) vlastnosti.
Vytvoření objektu Factory je náročná operace. ServiceProxyFactory udržuje interní mezipaměť komunikace klienta.
Osvědčeným postupem je pro ukládání do mezipaměti ServiceProxyFactory co nejdéle.

## <a name="remoting-exception-handling"></a>Zpracování výjimek vzdálené komunikace
Všechny vzdálené výjimky vyvolané rozhraním API služby jsou odesílány zpět do klienta jako AggregateException. RemoteExceptions by měl být kontraktu dat DataContract serializovatelný; Pokud nejsou, proxy rozhraní API vyvolá [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) kvůli chybě serializace v ní.

ServiceProxy zpracovává všechny výjimky převzetí služeb při selhání pro službu oddíl, který je vytvořen pro. Znovu řeší koncových bodů, pokud existují výjimky převzetí služeb při selhání (nepřechodných výjimky) a počet opakování volání se na správný koncový bod. Počet opakovaných pokusů pro převzetí služeb při selhání výjimky jsou nekonečno.
Pokud dojde k přechodným výjimkám, proxy zopakuje pokus o volání.

Výchozí parametry opakování se poskytují pomocí [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Tyto hodnoty můžete nakonfigurovat předáním objektu OperationRetrySettings ServiceProxyFactory konstruktoru.

## <a name="how-to-use-the-remoting-v2-stack"></a>Použití vzdálené komunikace V2 zásobníku

Od verze balíčku NuGet vzdálené komunikace 2.8 máte možnost použití vzdálené komunikace V2 zásobníku. Vzdálená komunikace V2 zásobníku je výkonnější a poskytuje funkce, jako jsou vlastní serializace a více modulární rozhraní API.
Kód šablony i nadále používat vzdálenou komunikaci V1 zásobníku.
Vzdálená komunikace V2 není kompatibilní s V1 (předchozí vzdálené komunikace zásobníku), proto postupujte podle pokynů na [způsob upgradu z V1 na V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) bez dopadu na dostupnost služeb.

Následujících dvou přístupů je možné povolit zásobníku V2.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Pomocí atributu sestavení pro použití zásobníku V2

Tyto kroky změnit kód šablony pro použití zásobníku V2 pomocí atributu sestavení.

1. Změňte koncový bod prostředku z `"ServiceEndpoint"` k `"ServiceEndpointV2"` v manifestu služby.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Použití `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` rozšiřující metodu pro vytvoření naslouchacích procesů vzdálené komunikace (rovná V1 a V2).

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

Nevyžaduje žádné změny kódu v projektu klienta.
Vytvoření klienta sestavení pomocí sestavení rozhraní, abyste měli jistotu, že se používá atribut sestavení uvedený výše.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Použití zásobníku V2 pomocí explicitní třídy V2

Jako alternativu k použití atributu sestavení můžete se zásobníku V2 také aktivovat pomocí explicitní třídy V2.

Tyto kroky změnit kód šablony pro použití zásobník V2 pomocí explicitní třídy V2.

1. Změňte koncový bod prostředku z `"ServiceEndpoint"` k `"ServiceEndpointV2"` v manifestu služby.

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

3. Použití [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` obor názvů umožní vytvořit klientů.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Postup upgradu ze vzdálené komunikace V1 na V2 vzdálené komunikace.
Aby bylo možné upgradovat z V1 na V2, jsou požadovány upgrady kroku 2. Následující kroky, které mají být provedeny v pořadí uvedené.

1. Upgradujte službu V1 na V2 služby CompactListener atributem.
Tato změna zajišťuje, že služba naslouchá na V1 a V2 naslouchací proces.

    (a) přidejte prostředek koncového bodu s názvem jako "ServiceEndpointV2" v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    (b) použijte tuto metodu rozšíření k vytvoření naslouchacího procesu vzdálené komunikace.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) přidejte atribut sestavení na vzdálené komunikace rozhraní CompatListener a klient V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Upgrade klienta V1 na V2 klienta pomocí atributu klienta V2.
Tento krok zajistí, že klient používá zásobník V2.
Není nutná žádná změna v projektu nebo služba klienta. Sestavení klientské projekty s aktualizované rozhraní sestavení je dostačující.

3. Tento krok je volitelný. Použijte atribut V2Listener a pak upgradovat službu V2.
Tento krok zajistí, že služba naslouchá jenom na naslouchací proces V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Jak používat vlastní serializace pomocí vzdálené komunikace V2.
Následující příklad používá serializace Json pomocí vzdálené komunikace V2.
1. Implementovat rozhraní IServiceRemotingMessageSerializationProvider poskytnout implementaci pro vlastní serializace.
    Tady je fragment kódu způsob implementace vypadá.

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

2.    Přepište výchozí zprostředkovatel serializace s JsonSerializationProvider pro naslouchací proces vzdálené komunikace.

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

3.    Přepište výchozí zprostředkovatel serializace s JsonSerializationProvider objektu pro vytváření klienta vzdálené komunikace.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Další postup
* [Webové rozhraní API s OWIN v modelu Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF komunikace s Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Zabezpečení komunikace pro služby Reliable Services](service-fabric-reliable-services-secure-communication.md)

