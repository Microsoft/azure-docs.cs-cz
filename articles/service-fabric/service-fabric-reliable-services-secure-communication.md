---
title: 'Zabezpečená komunikace vzdálené komunikace se společností C #'
description: Zjistěte, jak zabezpečit komunikaci vzdálenou komunikaci založenou na službě pro spolehlivé služby Jazyka C#, které jsou spuštěny v clusteru Azure Service Fabric.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ee2f1d70f4094ccc7d80edbfaf16509b5124f607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609617"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Komunikace vzdálené komunikace zabezpečené služby ve službě C#
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

Bezpečnost je jedním z nejdůležitějších aspektů komunikace. Architektura aplikací služby spolehlivé služby poskytuje několik předem vytvořených komunikačních zásobníků a nástrojů, které můžete použít ke zlepšení zabezpečení. Tento článek popisuje, jak zlepšit zabezpečení při použití vzdálené komunikace služby ve službě C#. Vychází z [existujícípříklad,](service-fabric-reliable-services-communication-remoting.md) který vysvětluje, jak nastavit vzdálenou řeč pro spolehlivé služby napsané v C#. 

Chcete-li zabezpečit službu při používání vzdálené komunikace služby c# pomocí služeb C#, postupujte takto:

1. Vytvořte rozhraní `IHelloWorldStateful`, které definuje metody, které budou k dispozici pro vzdálené volání procedury ve vaší službě. Vaše služba `FabricTransportServiceRemotingListener`bude používat , `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` který je deklarován v oboru názvů. Toto `ICommunicationListener` je implementace, která poskytuje možnosti vzdálené komunikace.

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
2. Přidejte nastavení posluchače a pověření zabezpečení.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace služby, je nainstalován ve všech uzlech v clusteru. 
    
    > [!NOTE]
    > V uzlech Linux musí být certifikát přítomen jako soubory ve formátu PEM v adresáři */var/lib/sfcerts.* Další informace najdete [v tématu Umístění a formát certifikátů X.509 v linuxových uzlech](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Nastavení naslouchací proces a pověření zabezpečení můžete zadat dvěma způsoby:

   1. Uveďte je přímo v servisním kódu:

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
   2. Poskytněte jim pomocí [konfiguračního balíčku](service-fabric-application-and-service-manifests.md):

       Přidejte `TransportSettings` pojmenovaný oddíl do souboru settings.xml.

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

        Pokud přidáte `TransportSettings` oddíl do souboru settings.xml , `FabricTransportRemotingListenerSettings` načte se ve výchozím nastavení všechna nastavení z této části.

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
3. Při volání metod na zabezpečené služby pomocí zásobníku vzdálené `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` komunikace, namísto použití `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`třídy k vytvoření proxy služby, použijte . Předak `FabricTransportRemotingSettings`, `SecurityCredentials`který obsahuje .

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

    Pokud je kód klienta spuštěn jako součást `FabricTransportRemotingSettings` služby, můžete se načíst ze souboru settings.xml. Vytvořte část HelloWorldClientTransportSettings, která je podobná kódu služby, jak je znázorněno výše. Proveďte následující změny klientského kódu:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Pokud klient není spuštěn jako součást služby, můžete vytvořit soubor client_name.settings.xml ve stejném umístění, kde je client_name.exe. Potom vytvořte oddíl TransportSettings v tomto souboru.

    Podobně jako služba, pokud `TransportSettings` přidáte oddíl v client settings.xml/client_name.settings.xml, `FabricTransportRemotingSettings` načte všechna nastavení z této části ve výchozím nastavení.

    V takovém případě je starší kód ještě více zjednodušen:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Jako další krok si přečtěte [webové rozhraní API s OWIN ve spolehlivých službách](service-fabric-reliable-services-communication-webapi.md).
