---
title: Pomůže zabezpečené komunikace pro služby v Azure Service Fabric | Microsoft Docs
description: Přehled o tom, jak pomoci zabezpečenou komunikaci pro spolehlivé služby, které jsou spuštěny v clusteru služby Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 624d9d358145fb8b41013d686821cb157693d3c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207991"
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Nápověda zabezpečené komunikace pro služby v Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java v Linuxu](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Pomoc se zabezpečením služby, pokud používáte vzdálenou komunikaci služby
Budeme používat existující [příklad](service-fabric-reliable-services-communication-remoting-java.md) to vysvětluje, jak nastavit vzdálenou komunikaci pro spolehlivé služby. Chcete-li pomoc se zabezpečením služby, pokud používáte vzdálenou komunikaci služby, postupujte takto:

1. Vytvořit rozhraní, `HelloWorldStateless`, který definuje metody, které budou k dispozici pro vzdálené volání procedury vaší služby. Bude vaše služba používat `FabricTransportServiceRemotingListener`, kterého je deklarovaná v `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` balíčku. Jedná se `CommunicationListener` implementace, která poskytuje funkce vzdálené komunikace.

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
2. Přidejte nastavení naslouchacího procesu a zabezpečovací pověření.

    Ujistěte se, že certifikát, který chcete použít k zabezpečení komunikace vaší služby je nainstalována na všech uzlech v clusteru. Zadejte nastavení naslouchacího procesu a zabezpečovací pověření dvěma způsoby:

   1. Poskytněte pomocí [konfigurační balíček](service-fabric-application-and-service-manifests.md):

       Přidat `TransportSettings` v souborech settings.xml souboru.

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

        Pokud přidáte `TransportSettings` v souborech settings.xml souboru bez jakékoli předpony `FabricTransportListenerSettings` načte všechna nastavení z tohoto oddílu ve výchozím nastavení.

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
3. Při volání metody ve službě zabezpečené pomocí vzdálené komunikace zásobníku, místo použití `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` třídy vytvořit proxy služby, použijte `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Pokud kód klienta je spuštěn jako součást služby, můžete načíst `FabricTransportSettings` ze souboru souborech settings.xml. Vytvořte TransportSettings oddíl, který je podobný kódu služby, jako je uvedené výše. Na kód klienta, proveďte následující změny:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
