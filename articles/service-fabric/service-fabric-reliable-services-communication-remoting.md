---
title: Vzdálené komunikace služeb s použitím jazyka C# v Service Fabric | Dokumentace Microsoftu
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
ms.openlocfilehash: 4110b8f1b336a604c89180ac44ad470132765830
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820670"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Vzdálená komunikace služby v jazyce C# s využitím Reliable Services

> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou vázány na konkrétní komunikační protokol nebo zásobníku, jako je například webové rozhraní API, Windows Communication Foundation nebo jiné rozhraní modelu Reliable Services poskytuje mechanismus vzdálené komunikace rychle a snadno nastavit vzdálené volání procedur pro služby. Tento článek popisuje, jak nastavit vzdálené volání procedur pro služby, které jsou napsané v C#.

## <a name="set-up-remoting-on-a-service"></a>Nastavování vzdálené komunikace na službu

Můžete nastavit vzdálenou komunikaci pro službu v dva jednoduché kroky:

1. Vytvoření rozhraní pro vaši službu k implementaci. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedur pro vaši službu. Musí být metody vracející úlohy asynchronní metody. Musí implementovat rozhraní `Microsoft.ServiceFabric.Services.Remoting.IService` signál, že má služba vzdálené komunikace rozhraní.
2. Použijte naslouchací proces vzdálené komunikace v rámci služby. Naslouchací proces vzdálené komunikace je `ICommunicationListener` implementace, která poskytuje funkce vzdálené komunikace. `Microsoft.ServiceFabric.Services.Remoting.Runtime` Obor názvů obsahuje metodu rozšíření `CreateServiceRemotingListener` bezstavové a stavové služby, které slouží k vytvoření naslouchacího procesu vzdálené komunikace pomocí výchozí přenosový protokol vzdálené komunikace.

>[!NOTE]
>`Remoting` Obor názvů je k dispozici jako samostatné balíček NuGet s názvem `Microsoft.ServiceFabric.Services.Remoting`.

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
> Argumenty a návratové typy v rozhraní služby může být libovolný jednoduchý, komplexní nebo vlastní typy, ale musí být schopné bylo serializováno modulem .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Volání metod vzdálené služby

Volání metod na službě s použitím vzdálené komunikace zásobníku se provádí pomocí místního proxy serveru do služby pomocí `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` třídy. `ServiceProxy` Metoda vytvoří místní proxy server s použitím stejného rozhraní, které služba implementuje. Pomocí proxy můžete volat metody rozhraní vzdáleně postupem.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Platforma pro vzdálenou spolupráci úloha šíří výjimek vyvolaných v klientovi služby. Díky tomu, kdy `ServiceProxy`se použije, klient je zodpovědná za zpracování výjimky vyvolané službou.

## <a name="service-proxy-lifetime"></a>Doba platnosti služby proxy

Vytvoření služby proxy je nenáročná operace, takže si můžete vytvořit tolik, je nutné. Instance proxy služby můžete použít opakovaně pro, jako jsou potřeba. Pokud vzdálené volání procedury vyvolá výjimku, stále můžete znovu použít stejnou instanci proxy serveru. Každý proxy služby obsahuje komunikace klienta používaný k posílání zpráv přenosu. Při vyvolávání vzdálené volání, jsou provedeny interní kontroly k určení, zda komunikace klienta je platný. Na základě výsledků kontrol komunikaci klienta se znovu vytvoří v případě potřeby. Proto pokud dojde k výjimce, nepotřebujete k opětovnému vytvoření `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Doba platnosti objekt pro vytváření proxy služby

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) je objekt factory, který vytváří instance serveru proxy pro různé vzdálené komunikace rozhraní. Pokud používáte rozhraní API `ServiceProxyFactory.CreateServiceProxy` rozhraní vytvořit proxy server, vytvoří proxy služby typu singleton.
Je vhodné vytvořit jeden ručně, když budete chtít přepsat [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) vlastnosti.
Vytvoření objektu Factory je náročná operace. Objekt pro vytváření proxy služby udržuje interní mezipaměť klienta komunikace.
Osvědčeným postupem je ukládat do mezipaměti objekt proxy server služby pro vytváření pro co nejdéle.

## <a name="remoting-exception-handling"></a>Zpracování výjimek vzdálené komunikace

Všechny vzdálené výjimky vyvolané rozhraním API služby jsou odesílány zpět do klienta jako AggregateException. Vzdálené výjimky by měly být schopni bylo serializováno modulem kontraktu dat DataContract. Pokud nejsou, proxy rozhraní API vyvolá [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) kvůli chybě serializace v ní.

Proxy služby zpracovává všechny výjimky převzetí služeb při selhání pro službu oddíl, který je vytvořen pro. Znovu řeší koncových bodů, pokud existují výjimky převzetí služeb při selhání (nepřechodných výjimky) a počet opakování volání se na správný koncový bod. Počet opakovaných pokusů pro převzetí služeb při selhání výjimky je nekonečno.
Pokud dojde k přechodným výjimkám, proxy zopakuje pokus o volání.

Výchozí parametry opakování jsou k dispozici v [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Uživatele můžete nakonfigurovat tyto hodnoty předá objekt OperationRetrySettings ServiceProxyFactory konstruktoru.

## <a name="use-the-remoting-v2-stack"></a>Použití vzdálené komunikace V2 zásobníku

Od verze 2.8 vzdálené komunikace balíčku NuGet máte možnost použití vzdálené komunikace V2 zásobníku. Vzdálená komunikace V2 zásobníku vrací lepší výsledky. Také poskytuje funkce, jako jsou vlastní serializace a více modulární rozhraní API.
Kód šablony i nadále používat vzdálenou komunikaci V1 zásobníku.
Vzdálená komunikace V2 není kompatibilní s V1 (předchozí zásobníku vzdálené komunikace). Postupujte podle pokynů v článku [upgradovat z verze V1 na V2](#upgrade-from-remoting-v1-to-remoting-v2) , aby vliv na dostupnost služeb.

Následujících dvou přístupů je možné povolit zásobníku V2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Pomocí atributu sestavení můžete použít zásobníku V2

Tyto kroky změnit kód šablony V2 zásobníku pomocí atributu sestavení.

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

3. Označte sestavení, které obsahuje rozhraní vzdálené komunikace pomocí `FabricTransportServiceRemotingProvider` atribut.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
  ```

Nevyžaduje žádné změny kódu v projektu klienta.
Vytvoření klienta sestavení pomocí sestavení rozhraní, abyste měli jistotu, že se používá atribut sestavení bylo dříve uvedeno.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Použít explicitní třídy V2 k použití zásobníku V2

Jako alternativu k použití atributu sestavení je také můžete povolit V2 zásobníku pomocí explicitní třídy V2.

Tyto kroky změnit kód šablony V2 zásobníku pomocí explicitní třídy V2.

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

3. Použití [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` obor názvů umožní vytvořit klientů.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Upgradovat z verze vzdálené komunikace V1 na V2 vzdálené komunikace

K upgradu z V1 na V2, je potřeba dvoustupňové upgrady. Postupujte podle kroků v tomto pořadí.

1. Upgradujte službu V1 na V2 služby pomocí tohoto atributu.
Tato změna zajišťuje, že služba naslouchá na naslouchací proces V1 a V2.

    a. Přidáte prostředek koncového bodu s názvem "ServiceEndpointV2" v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Pomocí následující metody rozšíření můžete vytvořit naslouchací proces vzdálené komunikace.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Přidáte atribut sestavení na vzdálené komunikace rozhraní použití V1 a V2 naslouchací proces a klient V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Upgrade klienta V1 na V2 klienta pomocí atributu klienta V2.
Tento krok zajistí, že klient používá zásobník V2.
Není nutná žádná změna v projektu nebo služby klienta. Sestavení klientské projekty s aktualizované rozhraní sestavení je dostačující.

3. Tento krok je volitelný. Použijte atribut V2 naslouchací proces a pak upgradovat službu V2.
Tento krok zajistí, že služba naslouchá jenom na naslouchací proces V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Použití vzdálené komunikace V2 zásobníku (kompatibilní s rozhraní)

 Vzdálená komunikace V2 (rozhraní kompatibilní, označované jako V2_1) zásobník obsahuje všechny funkce zásobníku vzdálené komunikace V2. Svůj zásobník rozhraní je kompatibilní se zásobníkem V1 vzdálenou komunikaci, ale není zpětně kompatibilní s V2 a V1. Aktualizaci z V1 na V2_1 bez ovlivnění dostupnosti služby, postupujte podle kroků v článku o Upgrade z V1 na V2 (rozhraní kompatibilní).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Použití vzdálené komunikace V2 zásobníku (rozhraní kompatibilní) pomocí atributu sestavení

Použijte následující postup změna V2_1 zásobníku.

1. Přidáte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Vytvořit naslouchací proces vzdálené komunikace pomocí této metody rozšíření vzdálené komunikace.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Přidat [atribut sestavení](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) vzdálené komunikace rozhraní.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```

Nevyžaduje žádné změny v klientském projektu.
Vytvoření klienta sestavení pomocí sestavení rozhraní, abyste měli jistotu, že používá atribut předchozí sestavení.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Pomocí třídy explicitní vzdálené komunikace pro vytváření naslouchacího procesu/klientské verze V2 (kompatibilní s rozhraní)

Postupujte následovně:

1. Přidáte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Použití [naslouchací proces vzdálené komunikace V2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Název prostředku koncového bodu služby výchozí použitý je "ServiceEndpointV2_1." Musí být definovaný v manifestu služby.

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

3. Použít na úrovni V2 [objektu factory klienta](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Upgrade ze vzdálené komunikace V1 na V2 vzdálené komunikace (rozhraní kompatibilní)

Upgrade z V1 na V2 (rozhraní kompatibilní, označované jako V2_1), se vyžadují dvoustupňový upgrady. Postupujte podle kroků v tomto pořadí.

1. Upgradujte službu V1 na V2_1 služby s použitím následujícího atributu.
Tato změna zajišťuje, že služba naslouchá V1 a V2_1 naslouchacího procesu.

    a. Přidáte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Pomocí následující metody rozšíření můžete vytvořit naslouchací proces vzdálené komunikace.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Přidejte atribut sestavení na vzdálené komunikace rozhraní k použití rozhraní V1, V2_1 naslouchací proces a V2_1 klienta.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Upgrade klienta V1 V2_1 klientovi pomocí atributu V2_1 klienta.
Tento krok zajistí, že klient používá V2_1 zásobníku.
Není nutná žádná změna v projektu nebo služby klienta. Sestavení klientské projekty s aktualizované rozhraní sestavení je dostačující.

3. Tento krok je volitelný. Odebrat atribut verze V1 naslouchací proces a pak upgradovat službu V2.
Tento krok zajistí, že služba naslouchá jenom na naslouchací proces V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Vlastní serializace pomocí vzdálené komunikace zabalené zprávu

Vzdálená komunikace zabalené zprávy vytvoříme objekt jednoho zabalené se všemi parametry jako pole v ní.
Postupujte následovně:

1. Implementace `IServiceRemotingMessageSerializationProvider` rozhraní k implementaci pro vlastní serializace.
    Tento fragment kódu ukazuje, jak vypadá implementace.

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

2. Přepsat výchozí zprostředkovatel serializace s `JsonSerializationProvider` pro naslouchací proces vzdálené komunikace.

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

3. Přepsat výchozí zprostředkovatel serializace s `JsonSerializationProvider` objektu pro vytváření klienta vzdálené komunikace.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Další postup

* [Webové rozhraní API s OWIN v modelu Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation komunikace s Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Zabezpečená komunikace modelu Reliable Services](service-fabric-reliable-services-secure-communication.md)
