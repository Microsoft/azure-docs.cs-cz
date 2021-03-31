---
title: Zabezpečená komunikace vzdálené služby s jazykem Java
description: Naučte se zabezpečit komunikaci na základě vzdálené komunikace služby pro spolehlivé služby Java, které běží v clusteru Azure Service Fabric.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: 01a64fbcfef9f56abb0e1aa6cf7f5d821dd3763b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87325708"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Zabezpečená komunikace vzdálené služby ve službě Java
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní Reliable Services Application Framework poskytuje několik předem připravených komunikačních zásobníků a nástrojů, které můžete použít ke zvýšení zabezpečení. Tento článek popisuje, jak zvýšit zabezpečení při použití vzdálené komunikace služby v rámci služby Java. Vychází z existujícího [příkladu](service-fabric-reliable-services-communication-remoting-java.md) , který vysvětluje, jak nastavit vzdálenou komunikaci pro spolehlivé služby napsané v jazyce Java. 

Chcete-li zajistit zabezpečení služby při použití vzdálené komunikace služby se službami Java, postupujte takto:

1. Vytvořte rozhraní, `HelloWorldStateless` které definuje metody, které budou k dispozici pro vzdálené volání procedur ve vaší službě. Vaše služba bude používat `FabricTransportServiceRemotingListener` , která je deklarována v `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` balíčku. Toto je `CommunicationListener` implementace, která poskytuje možnosti vzdálené komunikace.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Přidejte nastavení naslouchacího procesu a zabezpečovací přihlašovací údaje.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace služby, je nainstalovaný na všech uzlech v clusteru. Pro služby spuštěné v systému Linux musí být certifikát k dispozici jako soubor PEM-formmatted; buď `.pem` soubor, který obsahuje certifikát a soukromý klíč `.crt` , nebo soubor, který obsahuje certifikát, a `.key` soubor, který obsahuje soukromý klíč. Další informace najdete v tématu [umístění a formát certifikátů X. 509 na uzlech se systémem Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Existují dva způsoby, jak můžete zadat nastavení naslouchacího procesu a přihlašovací údaje zabezpečení:

   1. Poskytněte je pomocí [konfiguračního balíčku](service-fabric-application-and-service-manifests.md):

       Přidejte do `TransportSettings` souboru settings.xml pojmenovaný oddíl.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       V tomto případě `createServiceInstanceListeners` bude metoda vypadat takto:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Pokud přidáte `TransportSettings` oddíl do souboru settings.xml bez předpony, `FabricTransportListenerSettings` bude ve výchozím nastavení načteno všechna nastavení z této části.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        V tomto případě `CreateServiceInstanceListeners` bude metoda vypadat takto:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Při volání metod v zabezpečené službě pomocí zásobníku vzdálené komunikace namísto použití `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` třídy k vytvoření proxy služby použijte `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory` .

    Pokud je klientský kód spuštěn jako součást služby, můžete načíst `FabricTransportSettings` ze souboru settings.xml. Vytvořte část TransportSettings, která je podobná kódu služby, jak je uvedeno výše. Proveďte následující změny kódu klienta:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
