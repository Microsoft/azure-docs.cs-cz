---
title: 'Zabezpečená komunikace vzdálené služby s C #'
description: Naučte se zabezpečit komunikaci na základě vzdálené komunikace služby pro spolehlivé služby C#, které běží v clusteru Azure Service Fabric.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ba68df53f1f21b9ff360772fe1a60c93c8df74d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86252211"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Zabezpečená komunikace vzdálené služby ve službě C#
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní Reliable Services Application Framework poskytuje několik předem připravených komunikačních zásobníků a nástrojů, které můžete použít ke zvýšení zabezpečení. Tento článek popisuje, jak zvýšit zabezpečení při použití vzdálené komunikace služby v rámci služby C#. Vychází z existujícího [příkladu](service-fabric-reliable-services-communication-remoting.md) , který vysvětluje, jak nastavit vzdálenou komunikaci pro spolehlivé služby napsané v jazyce C#. 

Chcete-li zajistit zabezpečení služby při použití vzdálené komunikace služby se službami C#, postupujte takto:

1. Vytvořte rozhraní, `IHelloWorldStateful` které definuje metody, které budou k dispozici pro vzdálené volání procedur ve vaší službě. Vaše služba bude používat `FabricTransportServiceRemotingListener` , která je deklarována v `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` oboru názvů. Toto je `ICommunicationListener` implementace, která poskytuje možnosti vzdálené komunikace.

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
2. Přidejte nastavení naslouchacího procesu a zabezpečovací přihlašovací údaje.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace služby, je nainstalovaný na všech uzlech v clusteru. 
    
    > [!NOTE]
    > V uzlech se systémem Linux musí být certifikát přítomen jako soubory ve formátu PEM v adresáři */var/lib/sfcerts* . Další informace najdete v tématu [umístění a formát certifikátů X. 509 na uzlech se systémem Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Existují dva způsoby, jak můžete zadat nastavení naslouchacího procesu a přihlašovací údaje zabezpečení:

   1. Poskytněte je přímo v kódu služby:

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
   2. Poskytněte je pomocí [konfiguračního balíčku](service-fabric-application-and-service-manifests.md):

       Přidejte do `TransportSettings` souboru settings.xml pojmenovaný oddíl.

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

       V tomto případě `CreateServiceReplicaListeners` bude metoda vypadat takto:

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

        Pokud přidáte do `TransportSettings` souboru settings.xml oddíl, `FabricTransportRemotingListenerSettings` všechna nastavení z této části se ve výchozím nastavení načtou.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        V tomto případě `CreateServiceReplicaListeners` bude metoda vypadat takto:

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
3. Při volání metod v zabezpečené službě pomocí zásobníku vzdálené komunikace namísto použití `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` třídy k vytvoření proxy služby použijte `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` . Předat do `FabricTransportRemotingSettings` , který obsahuje `SecurityCredentials` .

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

    Pokud je klientský kód spuštěn jako součást služby, můžete načíst `FabricTransportRemotingSettings` ze souboru settings.xml. Vytvořte část HelloWorldClientTransportSettings, která je podobná kódu služby, jak je uvedeno výše. Proveďte následující změny kódu klienta:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Pokud klient neběží jako součást služby, můžete vytvořit soubor client_name.settings.xml ve stejném umístění, kde je client_name.exe. Pak vytvořte v tomto souboru oddíl TransportSettings.

    Podobně jako u služby se při přidání `TransportSettings` části do klienta settings.xml/client_name.settings.xml `FabricTransportRemotingSettings` načte všechna nastavení z této části ve výchozím nastavení.

    V takovém případě je dřívější kód ještě dále zjednodušený:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


V dalším kroku si přečtete [webové rozhraní API s Owin v Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md).
