---
title: Vzdálené komunikace pomocí jazyka C# v service fabric
description: Vzdálené volání Service Fabric umožňuje klientům a službám komunikovat se službami Jazyka C# pomocí vzdáleného volání procedury.
author: vturecek
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 0d59275f25931a11b2d551a2e9eb019838e4c1b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433873"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Vzdálená komunikace služby v C# se spolehlivými službami

> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou vázány na konkrétní komunikační protokol nebo zásobník, jako je například webové rozhraní API, Windows Communication Foundation nebo jiné, poskytuje rozhraní spolehlivé služby mechanismus vzdálené komunikace pro rychlé a snadné nastavení vzdálených volání procedur Služby. Tento článek popisuje, jak nastavit vzdálené procedury volání pro služby napsané s C#.

## <a name="set-up-remoting-on-a-service"></a>Nastavení vzdálené komunikace ve službě

Vzdálené komunikace pro službu můžete nastavit ve dvou jednoduchých krocích:

1. Vytvořte rozhraní pro vaši službu k implementaci. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedury ve vaší službě. Metody musí být úlohy vracející asynchronní metody. Rozhraní musí `Microsoft.ServiceFabric.Services.Remoting.IService` implementovat signál, že služba má rozhraní vzdálené komunikace.
2. Ve své službě použijte posluchače vzdálené komunikace. Naslouchací `ICommunicationListener` proces vzdálené komunikace je implementace, která poskytuje možnosti vzdálené komunikace. Obor `Microsoft.ServiceFabric.Services.Remoting.Runtime` názvů obsahuje metodu `CreateServiceRemotingInstanceListeners` rozšíření pro bezstavové i stavové služby, které lze použít k vytvoření vzdáleného vzdáleného naslouchací proces pomocí výchozího protokolu přenosu vzdálené komunikace.

>[!NOTE]
>Obor `Remoting` názvů je k dispozici jako `Microsoft.ServiceFabric.Services.Remoting`samostatný balíček NuGet s názvem .

Například následující bezstavová služba zpřístupňuje jednu metodu získat "Hello World" přes vzdálené volání procedury.

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
> Argumenty a návratové typy v rozhraní služby mohou být libovolné jednoduché, složité nebo vlastní typy, ale musí být možné serializovat pomocí .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Volání metod vzdálené služby

Volání metod ve službě pomocí zásobníku vzdálené komunikace se provádí pomocí místního proxy služby prostřednictvím `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` třídy. Metoda `ServiceProxy` vytvoří místní proxy server pomocí stejnérozhraní, které implementuje služba. S tímto proxy serverem můžete vzdáleně volat metody na rozhraní.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Rozhraní vzdálené komunikace šíří výjimky vyvolané službou klientovi. V důsledku toho `ServiceProxy`při použití klienta je zodpovědný za zpracování výjimky vyvoláné službou.

## <a name="service-proxy-lifetime"></a>Životnost proxy serveru služby

Vytvoření proxy serveru služby je zjednodušená operace, takže můžete vytvořit tolik, kolik potřebujete. Instance proxy služby lze znovu použít tak dlouho, jak jsou potřeba. Pokud vzdálené volání procedury vyvolá výjimku, můžete stále znovu použít stejnou instanci proxy. Každý proxy server služby obsahuje komunikačního klienta, který slouží k odesílání zpráv po drátě. Při vyvolání vzdálených volání se provádějí interní kontroly, aby se zjistilo, zda je komunikační klient platný. Na základě výsledků těchto kontrol je komunikační klient v případě potřeby znovu vytvořen. Proto pokud dojde k výjimce, není nutné `ServiceProxy`znovu vytvořit .

### <a name="service-proxy-factory-lifetime"></a>Životnost služby proxy

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) je továrna, která vytváří instance proxy pro různá rozhraní vzdálené komunikace. Pokud použijete `ServiceProxyFactory.CreateServiceProxy` rozhraní API k vytvoření proxy serveru, rozhraní vytvoří proxy služby singleton.
Je užitečné vytvořit ručně, když potřebujete přepsat [vlastnosti IServiceRemotingClientFactory.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory)
Tovární tvorba je nákladná operace. Továrna proxy služby udržuje vnitřní mezipaměť komunikačního klienta.
Osvědčeným postupem je ukládat do mezipaměti služby proxy factory tak dlouho, jak je to možné.

## <a name="remoting-exception-handling"></a>Zpracování výjimek vzdálené komunikace

Všechny vzdálené výjimky vyvoláné rozhraním API služby jsou odesílány zpět klientovi jako AggregateException. Vzdálené výjimky by měly být možné serializovat podle DataContract. Pokud nejsou, proxy rozhraní API vyvolá [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) s chybou serializace v něm.

Proxy služby zpracovává všechny výjimky převzetí služeb při selhání pro oddíl služby, pro který je vytvořen. Znovu vyřeší koncové body, pokud existují výjimky převzetí služeb při selhání (nepřechodné výjimky) a opakuje volání se správným koncovým bodem. Počet opakovaných pokusů o výjimky převzetí služeb při selhání je neurčitý.
Pokud dojde k přechodným výjimkám, proxy server znovu vyzkouší volání.

Výchozí parametry opakování jsou poskytovány [operationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Uživatel může nakonfigurovat tyto hodnoty předáním OperationRetrySettings objektu ServiceProxyFactory konstruktoru.

## <a name="use-the-remoting-v2-stack"></a>Použití zásobníku vzdálené komunikace V2

Od verze 2.8 balíčku vzdálené komunikace NuGet máte možnost použít vzdálené komunikace V2 zásobníku. Vzdálené komunikace V2 zásobníku funguje lépe. Poskytuje také funkce, jako je vlastní serializace a více připojitelné api.
Kód šablony nadále používá zásobník vzdálené komunikace V1.
Vzdálené komunikace V2 není kompatibilní s V1 (předchozí vzdálené komunikace zásobníku). Postupujte podle pokynů v článku [Upgrade z V1 na V2,](#upgrade-from-remoting-v1-to-remoting-v2) aby se zabránilo vliv na dostupnost služby.

Následující přístupy jsou k dispozici pro povolení zásobníku V2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Použití atributu sestavení k použití zásobníku V2

Tyto kroky změní kód šablony pro použití zásobníku V2 pomocí atributu sestavení.

1. Změňte prostředek koncového bodu z `"ServiceEndpoint"` do `"ServiceEndpointV2"` v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Použití `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` metody rozšíření k vytvoření vzdálené komunikace posluchače (stejné pro V1 a V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Označte sestavení, které obsahuje rozhraní `FabricTransportServiceRemotingProvider` vzdálené komunikace s atributem.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

V klientském projektu nejsou vyžadovány žádné změny kódu.
Sestavení sestavení klienta s sestavením rozhraní a ujistěte se, že je použit dříve zobrazený atribut sestavení.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Použití explicitních tříd V2 k použití zásobníku V2

Jako alternativu k použití atributu sestavení zásobníku V2 také lze povolit pomocí explicitní v2 třídy.

Tyto kroky změnit kód šablony pro použití zásobníku V2 pomocí explicitní V2 třídy.

1. Změňte prostředek koncového bodu z `"ServiceEndpoint"` do `"ServiceEndpointV2"` v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Použijte [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` z oboru názvů.

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

3. Pomocí [fabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` z oboru názvů vytvořte klienty.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Upgrade z remoting V1 na vzdálené hlasování V2

Chcete-li upgradovat z V1 na V2, jsou vyžadovány dvoustupňové upgrady. Postupujte podle kroků v tomto pořadí.

1. Upgradujte službu V1 na službu V2 pomocí tohoto atributu.
Tato změna zajišťuje, že služba naslouchá na naslouchací proces V1 a V2.

    a. Přidejte prostředek koncového bodu s názvem ServiceEndpointV2 v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. K vytvoření vzdáleného naslouchací proces použijte následující metodu rozšíření.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Přidejte atribut sestavení na rozhraní vzdálené komunikace pro použití naslouchací proces V1 a V2 a klienta V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Upgradujte klienta V1 na klienta V2 pomocí atributu klienta V2.
Tento krok zajišťuje, že klient používá zásobník V2.
Není nutná žádná změna v klientském projektu/službě. Vytváření klientských projektů s aktualizovaným sestavením rozhraní je dostačující.

3. Tento krok je volitelný. Použijte atribut naslouchací proces V2 a potom upgradujte službu V2.
Tento krok zajišťuje, že služba naslouchá pouze na naslouchací proces V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Použití zásobníku vzdálené komunikace V2 (kompatibilní s rozhraním)

 Vzdálené komunikace V2 (rozhraní kompatibilní, známý jako V2_1) zásobníku má všechny funkce zásobníku vzdálené komunikace V2. Jeho zásobník rozhraní je kompatibilní se sjednoceným zásobníkem V1, ale není zpětně kompatibilní s V2 a V1. Chcete-li upgradovat z V1 na V2_1 bez ovlivnění dostupnosti služby, postupujte podle pokynů v článku Upgrade z V1 na V2 (kompatibilní s rozhraním).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Použití atributu sestavení k použití zásobníku vzdálené komunikace V2 (kompatibilní s rozhraním)

Chcete-li přejít na V2_1 zásobníku, postupujte takto.

1. Přidejte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Použijte metodu rozšíření vzdálené komunikace k vytvoření vzdálené komunikace posluchače.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Přidejte [atribut sestavení](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) na rozhraní vzdálené komunikace.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

V klientském projektu nejsou vyžadovány žádné změny.
Sestavení sestavení klienta s sestavením rozhraní a ujistěte se, že je používán předchozí atribut sestavení.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Použití explicitních tříd vzdálené komunikace k vytvoření naslouchací/klientské továrny pro verzi V2 (kompatibilní s rozhraním)

Postupujte následovně:

1. Přidejte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Použijte [vzdálené volání v2 posluchače](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Výchozí název prostředku koncového bodu služby je "ServiceEndpointV2_1". Musí být definována v manifestu služby.

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

3. Použijte tovární [použití klienta](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)V2 .
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Upgrade z vzdálené komunikace V1 na vzdálené volání V2 (kompatibilní s rozhraním)

Chcete-li upgradovat z V1 na V2 (rozhraní kompatibilní, známé jako V2_1), jsou vyžadovány dvoustupňové upgrady. Postupujte podle kroků v tomto pořadí.

> [!NOTE]
> Při upgradu z V1 na `Remoting` V2 se ujistěte, že je obor názvů aktualizován tak, aby používal V2. Příklad: Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client
>
>

1. Upgradujte službu V1 na V2_1 službu pomocí následujícího atributu.
Tato změna zajišťuje, že služba naslouchá na V1 a naslouchací proces V2_1.

    a. Přidejte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. K vytvoření vzdáleného naslouchací proces použijte následující metodu rozšíření.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Přidejte atribut sestavení na rozhraní vzdálené komunikace pro použití V1, V2_1 naslouchací proces a V2_1 klienta.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Inovujte klienta V1 na V2_1 klienta pomocí atributu klienta V2_1.
Tento krok zajišťuje, že klient používá zásobník V2_1.
Není nutná žádná změna v klientském projektu/službě. Vytváření klientských projektů s aktualizovaným sestavením rozhraní je dostačující.

3. Tento krok je volitelný. Odeberte verzi naslouchací proces V1 z atributu a potom upgradujte službu V2.
Tento krok zajišťuje, že služba naslouchá pouze na naslouchací proces V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Použití vlastní serializace s zabalenou zprávou vzdálené komunikace

Pro zalomené zprávy vzdálené komunikace vytvoříme jeden zabalený objekt se všemi parametry jako pole v něm.
Postupujte následovně:

1. Implementujte `IServiceRemotingMessageSerializationProvider` rozhraní k zajištění implementace pro vlastní serializaci.
    Tento fragment kódu ukazuje, jak implementace vypadá.

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

2. Přepište výchozízprostředkovatel serializace `JsonSerializationProvider` s pro vzdálené naslouchací proces.

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

3. Přepište výchozího zprostředkovatele `JsonSerializationProvider` serializace pro vytvoření vzdálené komunikace.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Další kroky

* [Webové rozhraní API s OWIN ve spolehlivých službách](service-fabric-reliable-services-communication-webapi.md)
* [Komunikace windows foundation se spolehlivými službami](service-fabric-reliable-services-communication-wcf.md)
* [Bezpečná komunikace pro spolehlivé služby](service-fabric-reliable-services-secure-communication.md)
