---
title: Připojení a komunikace se službami v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak vyřešit, připojení a komunikace se službami v Service Fabric.
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
ms.openlocfilehash: f11d680330a43dd49b3c36c864f50b9dc869d172
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211848"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Připojení a komunikace se službami v Service Fabric
V Service Fabric služba někde běží v clusteru Service Fabric, obvykle distribuují na několika virtuálních počítačů. To můžete přesunout z jednoho místa do jiného, vlastník služby, nebo automaticky pomocí Service Fabric. Služby nejsou staticky vázán na konkrétní počítač nebo adresu.

Aplikace Service Fabric se obvykle skládá z mnoha různých služeb, kde každá služba provádí specializované úlohy. Tyto služby mohou komunikovat mezi sebou a vytvoří kompletní funkce, jako je vykreslování různých součástí webové aplikace. Existují také klientské aplikace připojit k a komunikace se službami. Tento dokument popisuje, jak nastavit komunikaci s a mezi služby v Service Fabric.

## <a name="bring-your-own-protocol"></a>Přenést váš vlastní protokol
Service Fabric pomáhá se správou životního cyklu služeb, ale neprovede rozhodnutí o informace o tom, co vaše služby. To zahrnuje komunikaci. Při otevření služby Service Fabric, který je vaše služba možnost nastavit koncový bod pro příchozí požadavky a pomocí libovolné protokolu nebo komunikace zásobník chcete. Vaše služba bude naslouchat na normální **IP: port** adresy pomocí jakékoli schéma adresování, jako je identifikátor URI. Víc replik nebo instancí služby můžou sdílet hostitelský proces, v takovém případě bude buď potřebujete použít jiné porty nebo používání sdílení portů mechanismu, jako je ovladač http.sys jádra ve Windows. V obou případech musí být každá instance služby nebo repliky v hostitelském procesu jedinečně adresovat.

![Koncové body služby][1]

## <a name="service-discovery-and-resolution"></a>Zjišťování služby a řešení
V distribuovaném systému služby může přesunete z jednoho počítače do druhého v čase. K tomu může dojít z různých důvodů, včetně Vyrovnávání zatížení, upgrady, převzetí služeb při selhání nebo horizontální navýšení kapacity prostředků. To znamená, že adresy koncových bodů služby změnit, protože služba přesune do uzlů pomocí různých IP adresách a může zobrazit na jiném portu, pokud služba používá dynamicky vybraný port.

![Distribuce služeb][7]

Service Fabric poskytuje službu zjišťování a řešení se říká služba pojmenování. Služba pojmenování udržuje tabulku, která mapuje s názvem instance služby s adresy koncových bodů, které jsou naslouchat. Všechny instance s názvem služby v Service Fabric mají jedinečné názvy, které jsou reprezentovány jako identifikátory URI, například `"fabric:/MyApplication/MyService"`. Název služby se nezmění během životního cyklu službu, ale pouze adresy koncových bodů, které lze změnit, pokud přesunout služby. To je obdobou weby, které mají konstantní adresy URL, ale pokud IP adresa se může změnit. A podobně jako DNS na webu, který přeloží adresy URL webu na IP adresy, Service Fabric má doménového registrátora, která mapuje názvy služeb jeho adresu koncového bodu.

![Koncové body služby][2]

Řešení a připojení ke službám zahrnuje následující kroky na opakování:

* **Vyřešit**: Získání koncového bodu, který publikoval služby ze služby pojmenování.
* **Připojit**: Připojení ke službě přes jakýkoli protokol používá na tomto koncovém bodu.
* **Zkuste**: Pokus o připojení může selhat z nejrůznějších důvodů, pro příklad: Pokud službu přesunula od posledního adresu koncového bodu byla vyřešena. V takovém případě předchozí řešení a kroky nutné opakovat připojení a tento cyklus se opakuje, dokud je připojení úspěšné.

## <a name="connecting-to-other-services"></a>Připojení k jiným službám
Služby připojení k sobě navzájem uvnitř clusteru obecně přímo přístupné koncovým bodům, dalších služeb, protože uzly v clusteru jsou ve stejné místní síti. Aby se lze snáze připojit mezi službami, Service Fabric nabízí další služby, které používají službu pojmenování. Služba DNS a reverzní proxy server služby.


### <a name="dns-service"></a>Služba DNS
Od mnoho služeb zejména kontejnerizovaných služeb, může mít existující název adresy URL, nebudou moct vyřešíte pomocí standardní DNS protokolu (spíše než protokol pojmenování Service) je velmi praktické, zejména ve scénářích aplikací "metodou lift and shift". To je přesně čemu služby DNS. Umožňuje mapovat názvy DNS na název služby a proto přeložit IP adresy koncového bodu. 

Jak je znázorněno v následujícím diagramu, služba DNS, spuštěného v clusteru Service Fabric mapuje názvy DNS na názvy služeb, které se pak vyřeší pojmenování Service vrátit adresy koncových bodů pro připojení k. V době vytvoření je zadaný název DNS pro službu. 

![Koncové body služby][9]

Pro další podrobnosti o tom, jak použít DNS služby viz [služba DNS v Azure Service Fabric](service-fabric-dnsservice.md) článku.

### <a name="reverse-proxy-service"></a>Reverzní proxy server služby
Reverzní proxy server adresy služeb v clusteru, který zveřejňuje koncové body HTTP, včetně protokolu HTTPS. Výrazně zjednodušuje tím, že konkrétní formát identifikátoru URI volání dalších služeb a jejich metod a zpracovává řešení reverzního proxy serveru, připojení, opakujte kroky potřebné k jedné službě komunikovat s jinou službu pojmenování. Jinými slovy skryje pojmenování Service od vás při volání jiné služby tak, že to stejně snadné jako volání adresy URL.

![Koncové body služby][10]

Další podrobnosti o tom, jak použít reverzní proxy server služby najdete v tématu [reverzní proxy server v Azure Service Fabric](service-fabric-reverseproxy.md) článku.

## <a name="connections-from-external-clients"></a>Připojení z externí klienti
Služby připojení k sobě navzájem uvnitř clusteru obecně přímo přístupné koncovým bodům, dalších služeb, protože uzly v clusteru jsou ve stejné místní síti. V některých prostředích ale clusteru může být za nástroj pro vyrovnávání zatížení, který směruje provoz externí příchozího přenosu dat prostřednictvím omezenou sadu portů. V těchto případech služby stále můžete komunikovat mezi sebou a přeložit adresy pomocí služby pojmenování, ale pár kroků navíc musí být přijata externí klienti pro připojení ke službám.

## <a name="service-fabric-in-azure"></a>Service Fabric v Azure
Cluster Service Fabric v Azure je umístěn za služby Azure Load Balancer. Všechny externí přenosy pro cluster musí projít přes nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení bude automaticky předávat provoz příchozích na daném portu na náhodnou *uzel* , který má stejný port otevřít. Azure Load Balancer ví pouze o na otevřené porty *uzly*, neví o otevřené porty podle jednotlivých *služby*.

![Azure Load balancer úrovně a Service Fabric topologie][3]

Například, pokud chcete přijmout externí přenosy na portu **80**, musí být nakonfigurované následující věci:

1. Psát služby, která naslouchá na portu 80. Konfigurace portu 80 ve vaší službě ServiceManifest.xml a otevřete naslouchací proces ve službě, například v místním prostředí webového serveru.

    ```xml    <Resources> <Endpoints> <Endpoint Name="WebEndpoint" Protocol="http" Port="80" /> </Endpoints> </Resources>
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
2. Vytvoření clusteru Service Fabric v Azure a zadejte port **80** jako port vlastní koncový bod pro typ uzlu, který bude hostitelem služby. Pokud máte více než jeden typ uzlu, můžete nastavit *omezení umístění* služby tak, aby se spustí jenom u uzlů typu, který má otevřený port vlastní koncový bod.

    ![Otevření portu v typu uzlu][4]
3. Po vytvoření clusteru nakonfigurujte nástroj pro vyrovnávání zatížení Azure ve skupině prostředků clusteru směrovat provoz na portu 80. Při vytváření clusteru pomocí webu Azure portal, nastavuje se to automaticky pro každý z portů vlastního koncového bodu, který byl nakonfigurován.

    ![Přesměrování provozu ve službě Azure Load Balancer][5]
4. Azure Load Balancer použije sondu k určení, jestli se mají posílat provoz na konkrétní uzel. Tato sonda kontroluje pravidelně koncový bod na každém uzlu k určení, zda je uzel reagovat. Pokud test selže po nakonfigurovaný počet trvá příjem odpovědi, nástroje pro vyrovnávání zatížení zastaví odesílání provozu do tohoto uzlu. Při vytváření clusteru pomocí webu Azure portal, test se automaticky nastaví pro každý z portů vlastního koncového bodu, který byl nakonfigurován.

    ![Přesměrování provozu ve službě Azure Load Balancer][8]

Je dobré si uvědomit, že Azure Load Balancer a kontroly vědí jenom o *uzly*, nikoli *služby* spuštění na uzlech. Azure Load Balancer vždy odešle provoz do uzlů, které reagují na test, abyste k zajištění, že služby jsou k dispozici na na uzly, které jsou schopné reagovat na testu musíte věnovat pozornost.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: Možnosti integrované komunikace rozhraní API
Reliable Services v rámci se dodává s několika možností předem sestavených komunikace. Rozhodnutí o tom, které jedna bude pro vás nejvhodnější, závisí na řadu programovací model, rozhraní komunikace a programovací jazyk, který vaše služby byly napsány v.

* **Žádný konkrétní protokol:**  Pokud nemáte konkrétní možnost komunikace rozhraní .NET framework, ale chcete něco si rychle zprovoznit, pak je ideální možnost pro vás [vzdálené komunikace služeb](service-fabric-reliable-services-communication-remoting.md), které umožňuje vzdálené procedury silného typu volání pro Reliable Services a Reliable Actors. Toto je nejjednodušší a nejrychlejší způsob, jak začít s komunikací služby. Vzdálená komunikace služby zpracovává překlad adres služby, připojení, zkuste to znovu a zpracování chyb. Tato možnost je dostupná pro obě C# a aplikací v Javě.
* **HTTP**: Protokol HTTP pro komunikaci bez ohledu na jazyk, poskytuje na standardní volbu nástroje a serverům HTTP, které jsou k dispozici v mnoha různých jazycích, všechna podporovaná službou Service Fabric. Služby můžete použít jakoukoli sadu protokolu HTTP k dispozici, včetně [rozhraní ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) pro C# aplikací. Klienti v C# můžete využít `ICommunicationClient` a `ServicePartitionClient` třídy, že pro Javu, použijte `CommunicationClient` a `FabricServicePartitionClient` třídy, [pro řešení služby, připojení prostřednictvím protokolu HTTP a zkuste to znovu smyčky](service-fabric-reliable-services-communication.md).
* **WCF**: Pokud máte existující kód, který používá WCF jako vaše architektura komunikace, pak můžete použít `WcfCommunicationListener` na straně serveru a `WcfCommunicationClient` a `ServicePartitionClient` třídy pro klienta. To ale je k dispozici pouze pro C# clustery založené na aplikace ve Windows. Další podrobnosti najdete v článku [provádění WCF komunikačního balíku](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Vlastní protokoly a další architektury komunikace
Služby můžete použít libovolný protokol nebo architekturu pro komunikaci, ať už jde o vlastní binární protokol TCP sockety nebo streamovaných událostí pomocí [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) nebo [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric zajišťuje komunikaci rozhraní API, která je možné připojit vaším zásobníkem komunikace, zatímco veškerou práci vyhledat a připojit je abstrahovaný od vás. Najdete v článku [komunikace modelu Reliable Service](service-fabric-reliable-services-communication.md) další podrobnosti.

## <a name="next-steps"></a>Další postup
Další informace o konceptech a k dispozici v rozhraní API [komunikace modelu Reliable Services](service-fabric-reliable-services-communication.md), pak rychle začít [vzdálené komunikace služeb](service-fabric-reliable-services-communication-remoting.md) nebo najdete podrobné informace o zápisu zprávy naslouchací proces pomocí [webového rozhraní API s OWIN hostování na vlastním serveru](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
