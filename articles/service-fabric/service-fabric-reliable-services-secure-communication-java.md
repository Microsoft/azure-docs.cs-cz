---
title: Zabezpečená komunikace služby vzdálené komunikace s využitím Javy v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak k zabezpečení komunikace podle vzdálené komunikace služby reliable services v Javě, které jsou spuštěny v clusteru Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666987"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Zabezpečená komunikace služeb vzdálené komunikace v služby v Javě
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

Zabezpečení je jedním z nejdůležitějších aspektů komunikace. Rozhraní framework aplikace Reliable Services poskytuje několik nástrojů, které můžete použít ke zlepšení zabezpečení a předem připravených komunikační balíky. Tento článek popisuje, jak zlepšit zabezpečení při použití vzdálené komunikace služeb v Javě service. Vychází z existujícího [příklad](service-fabric-reliable-services-communication-remoting-java.md) , který vysvětluje, jak nastavit vzdálené komunikace pro služby reliable services napsané v jazyce Java. 

Chcete-li pomoci zabezpečit službu při použití vzdálené komunikace služeb s služeb v Javě, postupujte takto:

1. Vytvoření rozhraní, `HelloWorldStateless`, který definuje metody, které bude k dispozici pro vzdálené volání procedury ve službě service. Vaše služba používat `FabricTransportServiceRemotingListener`, který je deklarován v `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` balíčku. Jedná se `CommunicationListener` implementace, která poskytuje funkce vzdálené komunikace.

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
2. Přidáte nastavení naslouchacího procesu a zabezpečovací přihlašovací údaje.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace služby je nainstalovaný na všech uzlech v clusteru. Pro služby spuštěné v Linuxu certifikát musí být k dispozici jako soubor PEM formmatted; buď `.pem` soubor, který obsahuje certifikát s privátním klíčem nebo `.crt` soubor, který obsahuje certifikát a `.key` soubor, který obsahuje privátní klíč. Další informace najdete v tématu [umístění a formátu X.509 certifikáty na uzly s Linuxem](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Existují dva způsoby, že můžete zadat nastavení naslouchacího procesu a zabezpečovací přihlašovací údaje:

   1. Zadejte pomocí [konfigurační balíček](service-fabric-application-and-service-manifests.md):

       Přidat pojmenovanou `TransportSettings` oddílu v souboru settings.xml.

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

       V takovém případě `createServiceInstanceListeners` metoda bude vypadat například takto:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Pokud chcete přidat `TransportSettings` oddílu v souboru settings.xml bez jakoukoli předponu `FabricTransportListenerSettings` načte všechna nastavení z tohoto oddílu ve výchozím nastavení.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        V takovém případě `CreateServiceInstanceListeners` metoda bude vypadat například takto:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Při volání metody službě zabezpečené pomocí vzdálené komunikace zásobníku, namísto použití `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` třída pro vytvoření proxy serveru služby použijte `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Pokud klientský kód je spuštěn jako součást služby, můžete načíst `FabricTransportSettings` ze souboru settings.xml. Vytvořte TransportSettings oddíl, který je podobný kódu služby, jak je uvedeno výše. Pro klientský kód, proveďte následující změny:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
