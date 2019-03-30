---
title: Zabezpečená komunikace služeb vzdálené komunikace s C# v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak k zabezpečení komunikace služby vzdálené komunikace na základě C# modelu reliable services, na kterých běží v clusteru Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: b6d4a44a53ba553ab4fd514c81867156192b69f5
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662529"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Zabezpečená komunikace služeb vzdálené komunikace v C# služby
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní framework aplikace Reliable Services poskytuje několik nástrojů, které můžete použít ke zlepšení zabezpečení a předem připravených komunikační balíky. Tento článek popisuje, jak zlepšit zabezpečení při použití vzdálené komunikace služeb v C# služby. Vychází z existujícího [příklad](service-fabric-reliable-services-communication-remoting.md) , který vysvětluje, jak nastavit vzdálené komunikace pro služby reliable services v C#. 

Na pomoc se zabezpečením služby při použití vzdálené komunikace služeb s C# services, postupujte podle těchto kroků:

1. Vytvoření rozhraní, `IHelloWorldStateful`, který definuje metody, které bude k dispozici pro vzdálené volání procedury ve službě service. Vaše služba používat `FabricTransportServiceRemotingListener`, který je deklarován v `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` oboru názvů. Jedná se `ICommunicationListener` implementace, která poskytuje funkce vzdálené komunikace.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Přidáte nastavení naslouchacího procesu a zabezpečovací přihlašovací údaje.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace služby je nainstalovaný na všech uzlech v clusteru. 
    
    > [!NOTE]
    > Na uzly s Linuxem, certifikát musí být k dispozici jako soubory ve formátu PEM */var/lib/sfcerts* adresáře. Další informace najdete v tématu [umístění a formátu X.509 certifikáty na uzly s Linuxem](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Existují dva způsoby, že můžete zadat nastavení naslouchacího procesu a zabezpečovací přihlašovací údaje:

   1. Poskytněte přímo v kódu služby:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Zadejte pomocí [konfigurační balíček](service-fabric-application-and-service-manifests.md):

       Přidat pojmenovanou `TransportSettings` oddílu v souboru settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       V takovém případě `CreateServiceReplicaListeners` metoda bude vypadat například takto:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Pokud chcete přidat `TransportSettings` oddílu v souboru settings.xml `FabricTransportRemotingListenerSettings ` načte všechna nastavení z tohoto oddílu ve výchozím nastavení.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        V takovém případě `CreateServiceReplicaListeners` metoda bude vypadat například takto:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Při volání metody službě zabezpečené pomocí vzdálené komunikace zásobníku, namísto použití `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` třída pro vytvoření proxy serveru služby použijte `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Předejte `FabricTransportRemotingSettings`, který obsahuje `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Pokud klientský kód je spuštěn jako součást služby, můžete načíst `FabricTransportRemotingSettings` ze souboru settings.xml. Vytvořte HelloWorldClientTransportSettings oddíl, který je podobný kódu služby, jak je uvedeno výše. Pro klientský kód, proveďte následující změny:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Pokud klient není spuštěna jako součást služby, můžete vytvořit soubor client_name.settings.xml ve stejném umístění, kde je client_name.exe. Potom vytvořte oddíl TransportSettings v daném souboru.

    Podobně jako služba, pokud chcete přidat `TransportSettings` kapitoly klienta settings.xml/client_name.settings.xml `FabricTransportRemotingSettings` načte všechna nastavení z tohoto oddílu ve výchozím nastavení.

    V takovém případě je ještě více zjednodušit starší kód:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


V dalším kroku, přečtěte si [webového rozhraní API s OWIN v modelu Reliable Services](service-fabric-reliable-services-communication-webapi.md).
