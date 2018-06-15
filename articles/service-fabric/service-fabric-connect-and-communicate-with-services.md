---
title: Připojení a komunikovat se službami v Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak vyřešit, připojit a komunikovat se službami v Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 2b6fd2373a9cd0b376a6c8729d5952c5fc48ddf8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205582"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Připojení a komunikovat se službami v Service Fabric
V Service Fabric běží služba někde v clusteru Service Fabric, obvykle se distribuují napříč více virtuálními počítači. Ho může být z jednoho místa na jiný přesunout, pomocí vlastníka služby, nebo automaticky pomocí Service Fabric. Služby nejsou staticky vázaný na konkrétní počítač nebo na adresu.

Aplikace Service Fabric se obvykle skládá z mnoha různých služeb, kde každá služba provádí úlohu specializované. Tyto služby může komunikovat s jinými k dokončení funkce, jako je například vykreslování různé části webové aplikace. Existují také klientské aplikace, které se připojují k a komunikovat se službami. Tento dokument popisuje, jak nastavit komunikaci s a mezi vaší služby v Service Fabric.

Toto video Microsoft Virtual Academy taky popisuje komunikace služby: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=iYFCk76yC_6706218965">  
<img src="./media/service-fabric-connect-and-communicate-with-services/CommunicationVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="bring-your-own-protocol"></a>Přineste si vlastní protokol
Service Fabric pomáhá spravovat životní cyklus vašim službám, ale neprovede rozhodnutí o co dělat, vaše služby. To zahrnuje komunikaci. Při služby se otevře pomocí Service Fabric, která je vaše služba příležitost k nastavit koncový bod pro příchozí požadavky a pomocí ať protokolu nebo komunikace zásobníku chcete. Vaše služba bude naslouchat na normální **IP: port** adres pomocí žádné schéma adresování, jako je identifikátor URI. Více instancí služby nebo repliky může sdílet procesu hostitele, v takovém případě bude buď potřebovat používají jiné porty nebo použijte mechanismu sdílení portů, jako je ovladač http.sys jádra v systému Windows. V obou případech musí být jedinečně adresovat každé instance služby nebo repliky v hostitelském procesu.

![Koncové body služby][1]

## <a name="service-discovery-and-resolution"></a>Zjišťování služby a řešení
V systému distribuované služby může přesunout z jednoho počítače do jiného v čase. K tomu dochází z různých důvodů, včetně prostředků vyrovnávání upgrady, převzetí služeb při selhání nebo Škálováním na více systémů. To znamená, že adresy koncových bodů služby změnit, protože služba přesune do uzlů se různých IP adres a může otevřít v jiné porty, pokud služba používá dynamicky vybraný port.

![Distribuce služeb][7]

Service Fabric nabízí zjišťování a řešení služby názvem služby DNS. Služby DNS udržuje tabulku, která mapuje pojmenovaných instancí služby na koncový bod adresy, které budou naslouchat na portu. Všechny instance s názvem služby v Service Fabric mít jedinečné názvy reprezentován jako identifikátory URI, například `"fabric:/MyApplication/MyService"`. Název služby se nemění během životního cyklu služby, je pouze adresy koncových bodů, které můžou změnit při přesunutí služby. Toto je obdobou weby, které mají konstantní adresy URL, ale kde může změnit IP adresu. A podobně jako DNS na webu, který přeloží adresy URL webu na IP adresy, Service Fabric má doménového registrátora, která mapuje názvy služby jejich adresa koncového bodu.

![Koncové body služby][2]

Řešení a připojené ke službám zahrnuje následující kroky na opakování:

* **Vyřešte**: získání koncového bodu, který publikovali služby ze služby DNS.
* **Připojit**: připojit ke službě přes ať protokolu používá na tohoto koncového bodu.
* **Opakujte**: Pokus o připojení, které mohou být neúspěšné pro libovolný počet z důvodů pro příklad, pokud služba přesunul od posledního adresa koncového bodu byl vyřešen. V takovém případě předchozím vyřešit a připojení potřeba kroky opakovat a tento cyklus se opakuje, dokud je připojení úspěšné.

## <a name="connecting-to-other-services"></a>Připojení k jiným službám
Služby připojení k sobě navzájem v clusteru s podporou obecně můžete přímo přístup koncových bodů jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. Chcete-li je jednodušší připojit mezi službami, Service Fabric nabízí další služby, které používají služby DNS. Služba DNS a služby reverzní proxy server.


### <a name="dns-service"></a>Služba DNS
Od mnoho služeb zejména kontejnerizované služeb, může mít název existující adresu URL, bude možné, chcete-li tyto standardní DNS pomocí protokolu (nikoli protokol služby DNS) je velmi praktické, zejména v scénáře "navýšení a posunutí" aplikací. Toto je přesně jaké služby DNS. Umožňuje mapování názvů DNS pro název služby a proto přeložit koncový bod IP adresy. 

Jak je znázorněno v následujícím diagramu, služba DNS spuštěných v clusteru Service Fabric mapuje názvy služeb, které se pak vyřeší služby DNS vrátit adresy koncových bodů pro připojení k názvy DNS. V době vytvoření je zadaný název DNS pro službu. 

![Koncové body služby][9]

Pro další podrobnosti o tom, jak použít DNS služby najdete v tématu [služba DNS v Azure Service Fabric](service-fabric-dnsservice.md) článku.

### <a name="reverse-proxy-service"></a>Reverzní proxy server služby
Reverzní proxy server adresy služeb v clusteru, který zveřejňuje koncové body HTTP včetně HTTPS. Výrazně zjednodušuje volání jiných služeb a jejich metody tak, že konkrétní formát identifikátoru URI a zpracovává překlad reverzní proxy server, připojení, opakujte kroky potřebné k jedné službě komunikovat pomocí služby DNS. Jinými slovy skryje služby DNS od vás při volání metody jiných služeb tím, že to stejně jednoduché jako volání adresu URL.

![Koncové body služby][10]

Podrobnosti o tom, jak používat službu reverzního proxy serveru najdete v části [reverzní proxy server v Azure Service Fabric](service-fabric-reverseproxy.md) článku.

## <a name="connections-from-external-clients"></a>Připojení klientů, externí
Služby připojení k sobě navzájem v clusteru s podporou obecně můžete přímo přístup koncových bodů jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. V některých prostředích však clusteru může být za nástroj pro vyrovnávání zatížení, který směruje externí příchozí provoz prostřednictvím omezenou sadu portů. V těchto případech služby můžete stále vzájemně komunikovat a vyřešit adresy pomocí služby DNS, ale dodatečné kroky musí být přijata tak, aby externí klienti pro připojení ke službám.

## <a name="service-fabric-in-azure"></a>Service Fabric v Azure
Cluster Service Fabric v Azure je umístěn za pro vyrovnávání zatížení Azure. Nástroje pro vyrovnávání zatížení musí projít všechny externích přenosů do clusteru. Nástroje pro vyrovnávání zatížení bude automaticky přeposílal provoz na zadaný port pro náhodnou příchozí *uzlu* má stejný port otevřete. Nástroje pro vyrovnávání zatížení Azure zná pouze na otevřené porty *uzly*, neví o otevřené porty fyzická *služby*.

![Azure topologie pro vyrovnávání zatížení a Service Fabric][3]

Třeba, aby bylo možné přijímat externí přenosy na portu **80**, musí být nakonfigurované následujících akcí:

1. Zápis služba, která naslouchá na portu 80. Nakonfigurujte port 80 v ServiceManifest.xml služby a otevřete naslouchací proces ve službě, například vlastním hostováním webový server.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Vytvořte Service Fabric Cluster v Azure a zadejte port **80** jako vlastní koncový port pro typ uzlu, který bude hostitelem služby. Pokud máte více než jeden typ uzlu, můžete nastavit *omezení umístění* na službu, aby měli jistotu, že lze spustit pouze na typ uzlu, který má otevřený port vlastní koncový bod.

    ![Otevřete port typu uzlu][4]
3. Po vytvoření clusteru nakonfigurujte nástroj pro vyrovnávání zatížení Azure ve skupině prostředků clusteru, aby přeposílal provoz na portu 80. Při vytváření clusteru prostřednictvím portálu Azure, to je nastavený automaticky pro každé vlastní koncový port, který byl nakonfigurovaný.

    ![Přesměrovat provoz nástroji pro vyrovnávání zatížení Azure][5]
4. Služba Vyrovnávání zatížení Azure používá sondu k určení, zda chcete odesílat provoz na konkrétním uzlu. Tato kontrola pravidelně kontroluje koncový bod na každý uzel k určení, zda reaguje uzlu. Pokud tato kontrola se nepodaří po nakonfigurovanou stanovený počet, obdrží odpověď, nástroje pro vyrovnávání zatížení zastaví odesílání provozu do daného uzlu. Při vytváření clusteru prostřednictvím portálu Azure, sondu se automaticky nastaví pro každý koncový bod vlastní port, který byl nakonfigurován.

    ![Přesměrovat provoz nástroji pro vyrovnávání zatížení Azure][8]

Je důležité si pamatovat, že nástroj pro vyrovnávání zatížení Azure a kontroly pouze vědět o *uzly*, nikoli *služby* spuštěné v jednotlivých uzlech. Nástroje pro vyrovnávání zatížení Azure bude vždy odesílá data na uzly, které reagují na test, takže musí dát pozor na Ujistěte se, že služby jsou dostupné na uzlech, jež jsou schopné reagovat na tato kontrola.

## <a name="reliable-services-built-in-communication-api-options"></a>Spolehlivé služby: Možnosti integrované komunikace rozhraní API
Spolehlivé služby framework se dodává s několik předdefinovaných komunikace možností. Rozhodnutí o tom, které jeden bude nejlépe fungovat pro vás závisí na výběr programovací model, rozhraní komunikace a programovací jazyk, který vašim službám, které jsou napsané v.

* **Žádný konkrétní protokol:** Pokud nemáte konkrétní volbu komunikace framework, ale chcete něco si rychle a spustit, je možnost ideální pro vás [vzdálené komunikace služby](service-fabric-reliable-services-communication-remoting.md), což umožňuje silně typované vzdálené volání procedur pro Reliable Services a Reliable Actors. Toto je nejjednodušší a nejrychlejší způsob, jak začít s komunikace služby. Vzdálená komunikace služby provádí překlad adres služby, připojení, zkuste to znovu a zpracování chyb. Toto je k dispozici pro C# a aplikací v jazyce Java.
* **HTTP**: pro jazykově nezávislého komunikaci protokolu HTTP poskytuje na standardní volbu s nástroji a servery HTTP, které jsou k dispozici v mnoha různých jazycích vše s podporou Service Fabric. Služby mohou používat jakékoli zásobník protokolu HTTP k dispozici, včetně [rozhraní ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) pro aplikací v C#. Klienti, které jsou napsané v C# můžete využít `ICommunicationClient` a `ServicePartitionClient` třídy, zatímco pro jazyk Java, použijte `CommunicationClient` a `FabricServicePartitionClient` třídy, [pro řešení služby, připojení prostřednictvím protokolu HTTP a opakování smyčky](service-fabric-reliable-services-communication.md).
* **WCF**: Pokud máte existující kód, který používá WCF jako rozhraní vaší komunikace, pak můžete použít `WcfCommunicationListener` na straně serveru a `WcfCommunicationClient` a `ServicePartitionClient` třídy pro klienta. To ale je k dispozici pouze pro aplikace C# na clusterech systémem Windows. Další podrobnosti najdete v tématu Tento článek [provádění WCF komunikačního balíku](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Vlastní protokoly a další komunikaci rozhraní
Služby můžete použít libovolný protokol nebo framework pro komunikaci, jestli je vlastní binární protokol přes TCP sokety nebo streamování události prostřednictvím [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) nebo [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric nabízí komunikace rozhraní API, kterou můžete připojit vaše komunikačního balíku do, zatímco všechny pracovní vyhledat a připojit je abstrahované od vás. Najdete v článku [spolehlivá služba komunikační model](service-fabric-reliable-services-communication.md) další podrobnosti.

## <a name="next-steps"></a>Další postup
Další informace o konceptech a rozhraní API dostupná v [spolehlivé služby komunikační model](service-fabric-reliable-services-communication.md), pak rychle začít s [vzdálené komunikace služby](service-fabric-reliable-services-communication-remoting.md) nebo přejděte podrobné informace o psaní komunikace naslouchací proces pomocí [webového rozhraní API s OWIN hostování na vlastním](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
