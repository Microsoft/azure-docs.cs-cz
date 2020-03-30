---
title: Zabezpečená komunikace vzdálené komunikace s Javou
description: Zjistěte, jak zabezpečit komunikaci založenou na vzdálené komunikaci pro spolehlivé služby Java, které běží v clusteru Azure Service Fabric.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609634"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Zabezpečená komunikace vzdálené komunikace ve službě Java
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

Bezpečnost je jedním z nejdůležitějších aspektů komunikace. Architektura aplikací služby spolehlivé služby poskytuje několik předem vytvořených komunikačních zásobníků a nástrojů, které můžete použít ke zlepšení zabezpečení. Tento článek popisuje, jak zlepšit zabezpečení, když používáte službu vzdálené komunikace ve službě Java. Vychází z existujícího [příkladu,](service-fabric-reliable-services-communication-remoting-java.md) který vysvětluje, jak nastavit vzdálenou účast pro spolehlivé služby napsané v jazyce Java. 

Chcete-li zabezpečit službu při používání vzdálené komunikace služby java, postupujte takto:

1. Vytvořte rozhraní `HelloWorldStateless`, které definuje metody, které budou k dispozici pro vzdálené volání procedury ve vaší službě. Vaše služba `FabricTransportServiceRemotingListener`bude používat , `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` který je deklarován v balíčku. Toto `CommunicationListener` je implementace, která poskytuje možnosti vzdálené komunikace.

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
2. Přidejte nastavení posluchače a pověření zabezpečení.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace služby, je nainstalován ve všech uzlech v clusteru. U služeb spuštěných v Systému Linux musí být certifikát k dispozici jako soubor ve formátu PEM; `.pem` soubor, který obsahuje certifikát a soukromý `.crt` klíč, nebo soubor, který obsahuje certifikát a `.key` soubor obsahující soukromý klíč. Další informace najdete [v tématu Umístění a formát certifikátů X.509 v linuxových uzlech](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Nastavení naslouchací proces a pověření zabezpečení můžete zadat dvěma způsoby:

   1. Poskytněte jim pomocí [konfiguračního balíčku](service-fabric-application-and-service-manifests.md):

       Přidejte `TransportSettings` pojmenovaný oddíl do souboru settings.xml.

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

        Pokud přidáte `TransportSettings` oddíl do souboru settings.xml bez `FabricTransportListenerSettings` jakékoli předpony, načte se ve výchozím nastavení všechna nastavení z této části.

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
3. Při volání metod na zabezpečené služby pomocí zásobníku vzdálené `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` komunikace, namísto použití `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`třídy k vytvoření proxy služby, použijte .

    Pokud je kód klienta spuštěn jako součást `FabricTransportSettings` služby, můžete se načíst ze souboru settings.xml. Vytvořte oddíl TransportSettings, který je podobný kódu služby, jak je znázorněno výše. Proveďte následující změny klientského kódu:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
