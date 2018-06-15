---
title: Zabezpečená komunikace vzdálené komunikace služby v Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak k zabezpečení komunikace služby Vzdálená komunikace na základě spolehlivé služby, které jsou spuštěny v clusteru služby Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: cd7211ecda61ab2cca0f97e292d9ce2c47ed6933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210269"
---
# <a name="secure-service-remoting-communications-for-a-service"></a>Zabezpečená komunikace vzdálené komunikace služby pro službu
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní spolehlivé služby poskytuje několik předem komunikace zásobníky a nástroje, které můžete použít k vylepšení zabezpečení. Tento článek pojednává o tom, jak zlepšit zabezpečení, když používáte vzdálené komunikace služby.

Používáme existující [příklad](service-fabric-reliable-services-communication-remoting.md) to vysvětluje, jak nastavit vzdálenou komunikaci pro spolehlivé služby. Chcete-li pomoc se zabezpečením služby, pokud používáte vzdálenou komunikaci služby, postupujte takto:

1. Vytvořit rozhraní, `IHelloWorldStateful`, který definuje metody, které budou k dispozici pro vzdálené volání procedury vaší služby. Bude vaše služba používat `FabricTransportServiceRemotingListener`, kterého je deklarovaná v `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` oboru názvů. Jedná se `ICommunicationListener` implementace, která poskytuje funkce vzdálené komunikace.

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
2. Přidejte nastavení naslouchacího procesu a zabezpečovací pověření.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace vaší služby je nainstalována na všech uzlech v clusteru. Zadejte nastavení naslouchacího procesu a zabezpečovací pověření dvěma způsoby:

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
   2. Poskytněte pomocí [konfigurační balíček](service-fabric-application-and-service-manifests.md):

       Přidat `TransportSettings` v souborech settings.xml souboru.

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

        Pokud přidáte `TransportSettings` v souborech settings.xml souboru `FabricTransportRemotingListenerSettings ` načte všechna nastavení z tohoto oddílu ve výchozím nastavení.

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
3. Při volání metody ve službě zabezpečené pomocí vzdálené komunikace zásobníku, místo použití `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` třídy vytvořit proxy služby, použijte `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Předejte `FabricTransportRemotingSettings`, který obsahuje `SecurityCredentials`.

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

    Pokud kód klienta je spuštěn jako součást služby, můžete načíst `FabricTransportRemotingSettings` ze souboru souborech settings.xml. Vytvořte HelloWorldClientTransportSettings oddíl, který je podobný kódu služby, jako je uvedené výše. Na kód klienta, proveďte následující změny:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Pokud klient není spuštěna jako součást služby, můžete vytvořit soubor client_name.settings.xml ve stejném umístění, kde je client_name.exe. Pak vytvořte TransportSettings části v tomto souboru.

    Podobně jako službu, pokud přidáte `TransportSettings` část v klienta settings.xml/client_name.settings.xml `FabricTransportRemotingSettings` načte všechna nastavení z tohoto oddílu ve výchozím nastavení.

    V takovém případě je zjednodušený i další starší kód:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

Jako další krok, přečtěte si [webového rozhraní API s OWIN v spolehlivé služby](service-fabric-reliable-services-communication-webapi.md).
