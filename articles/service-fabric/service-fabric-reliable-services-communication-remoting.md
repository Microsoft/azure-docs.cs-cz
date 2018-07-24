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
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205812"
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
     return this.CreateServiceRemotingInstanceListeners();
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

Výchozí parametry opakování jsou k dispozici v [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Uživatel může konfigurovat tyto hodnoty předáním objektu OperationRetrySettings ServiceProxyFactory konstruktoru.

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
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
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

1. Upgradujte službu V1 na V2 služby s využitím níže atribut.
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

    c) přidejte atribut sestavení na vzdálené komunikace rozhraní pro použití V1 a V2 naslouchací proces a klienta V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Upgrade klienta V1 na V2 klienta pomocí atributu klienta V2.
Tento krok zajistí, že klient používá zásobník V2.
Není nutná žádná změna v projektu nebo služba klienta. Sestavení klientské projekty s aktualizované rozhraní sestavení je dostačující.

3. Tento krok je volitelný. Použijte atribut V2Listener a pak upgradovat službu V2.
Tento krok zajistí, že služba naslouchá jenom na naslouchací proces V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>Použití vzdálené komunikace V2(InterfaceCompatible) zásobníku
 Vzdálená komunikace V2 zásobníku (InterfaceCompatible neboli V2_1) má všechny funkce vzdálené komunikace V2 zásobníku kromě je rozhraní kompatibilní zásobníku do zásobníku vzdálené komunikace V1, ale není zpětně kompatibilní s V2 a V1. Aby bylo možné provést upgrade z V1 na V2_1 bez dopadu na dostupnost služby, pomocí následujícího článku[způsob upgradu z V1 na V2(InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible).


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>Použití vzdálené komunikace V2(InterfaceCompatible) zásobníku pomocí atributu sestavení.

Tady je postup pro změnu V2_1 zásobníku.

1. Přidáte prostředek koncového bodu s názvem jako "ServiceEndpointV2_1" v manifestu služby.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2.  Použití vzdálené komunikace rozšíření metody vytvoření naslouchacího procesu vzdálené komunikace.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Přidat [atribut sestavení](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) vzdálené komunikace rozhraní.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
Nevyžaduje žádné změny v projektu klienta.
Vytvoření klienta sestavení pomocí sestavení rozhraní, na zajišťuje, že nad sestavení je použit atribut.

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>Použití explicitní třídy vzdálené komunikace k vytvoření naslouchacího procesu / ClientFactory V2(InterfaceCompatible) verze.
Tady jsou kroky dodržovat.
1.  Přidáte prostředek koncového bodu s názvem jako "ServiceEndpointV2_1" v manifestu služby.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Použití [vzdálené komunikace V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Výchozí název prostředku koncový bod služby používá se "ServiceEndpointV2_1" a musí být definovaný v manifestu služby.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. Používat V2 [objektu Factory klienta](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>Postup upgradu ze vzdálené komunikace V1 na V2(InterfaceCompatible) vzdálené komunikace.
Aby bylo možné upgradovat z verze V1 na V2 (neboli V2_1 InterfaceCompatible), jsou požadovány upgrady kroku 2. Následující kroky, které mají být provedeny v pořadí uvedené.

1. Upgradujte službu V1 na V2_1 služby pomocí následujících atributů.
Tato změna zajišťuje, že služba naslouchá na V1 a V2_1 naslouchací proces.

    (a) přidejte prostředek koncového bodu s názvem jako "ServiceEndpointV2_1" v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
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

    c) přidejte atribut sestavení na vzdálené komunikace rozhraní použití V1, V2_1 naslouchací proces a V2_1 klienta.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Upgrade klienta V1 V2_1 klientovi pomocí atributu V2_1 klienta.
Tento krok zajistí, že klient používá V2_1 zásobníku.
Není nutná žádná změna v projektu nebo služba klienta. Sestavení klientské projekty s aktualizované rozhraní sestavení je dostačující.

3. Tento krok je volitelný. Verze V1 naslouchací proces odebrání atributů a pak upgradovat službu V2.
Tento krok zajistí, že služba naslouchá jenom na naslouchací proces V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>Jak používat vlastní serializace zabalené zprávou vzdálené komunikace.
Ve zprávě zabalené vzdálené komunikace vytvoříme zabalené jeden objekt se všemi parametry jako pole v ní.
Tady jsou kroky:

1. Implementovat rozhraní IServiceRemotingMessageSerializationProvider poskytnout implementaci pro vlastní serializace.
    Tady je fragment kódu způsob implementace vypadá.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
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
