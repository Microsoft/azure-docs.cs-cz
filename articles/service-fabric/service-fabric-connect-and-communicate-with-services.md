---
title: Připojení a komunikace se službami v Azure Service Fabric
description: Naučte se řešit, připojovat a komunikovat se službami v Service Fabric.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 11f525eba89dc963deee0ba9a86566361ef644de
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576294"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Připojení a komunikace se službami v Service Fabric
V Service Fabric se služba spouští někde v clusteru Service Fabric, který se obvykle distribuuje napříč několika virtuálními počítači. Dá se přesunout z jednoho místa na jiný, buď podle vlastníka služby, nebo automaticky pomocí Service Fabric. Služby nejsou staticky vázané na konkrétní počítač nebo adresu.

Aplikace Service Fabric se obecně skládá z mnoha různých služeb, kde každá služba provádí specializované úkoly. Tyto služby mohou vzájemně komunikovat, aby tvořily úplnou funkci, jako je například vykreslení různých částí webové aplikace. K dispozici jsou také klientské aplikace, které se připojují ke službám a komunikují s nimi. Tento dokument popisuje, jak nastavit komunikaci s a mezi vašimi službami v Service Fabric.

## <a name="bring-your-own-protocol"></a>Přineste si vlastní protokol
Service Fabric pomáhá spravovat životní cyklus vašich služeb, ale neprovádí rozhodnutí o tom, co vaše služby dělají. To zahrnuje komunikaci. Když je vaše služba otevřená Service Fabric, je to možnost pro nastavení koncového bodu pro příchozí požadavky, a to pomocí libovolného protokolu nebo komunikačního zásobníku, který chcete. Vaše služba bude naslouchat normální **IP adrese: adresa portu** pomocí schématu adresování, jako je například identifikátor URI. Proces hostitele může sdílet několik instancí služby nebo replik. v takovém případě bude nutné použít jiné porty nebo použít mechanismus pro sdílení portů, jako je například http.sys ovladače jádra ve Windows. V obou případech musí být každá instance služby nebo replika v hostitelském procesu jedinečně adresovatelná.

![koncové body služby][1]

## <a name="service-discovery-and-resolution"></a>Zjišťování a řešení služeb
V distribuovaném systému se služby můžou v průběhu času přesouvat z jednoho počítače do druhého. K tomu může dojít z různých důvodů, včetně vyrovnávání prostředků, upgradů, převzetí služeb při selhání nebo horizontálního navýšení kapacity. To znamená, že se adresy koncových bodů služby mění při přesunu služby na uzly s různými IP adresami a můžou se otevřít na různých portech, pokud služba používá dynamicky vybraný port.

![Distribuce služeb][7]

Service Fabric poskytuje službu zjišťování a rozlišení nazvanou Naming Service. Naming Service udržuje tabulku, která mapuje pojmenované instance služby na adresy koncových bodů, na kterých naslouchá. Všechny pojmenované instance služby v Service Fabric mají jedinečné názvy reprezentované jako identifikátory URI, například `"fabric:/MyApplication/MyService"` . Název služby se po celou dobu životnosti služby nemění. je to jenom adresa koncového bodu, které se můžou změnit při přesunu služeb. Je obdobou pro weby, které mají konstantní adresy URL, ale kde se může změnit IP adresa. A podobně jako u DNS na webu, který překládá adresy URL webových stránek na IP adresy, Service Fabric má registrátora, který mapuje názvy služeb na adresu koncového bodu.

![Diagram ukazuje, že Service Fabric má Registrátor, který mapuje názvy služeb na jejich adresu koncového bodu.][2]

Řešení a připojování ke službám zahrnuje následující kroky, které jsou spouštěny ve smyčce:

* **Řešení**: Získejte koncový bod, který služba publikovala z Naming Service.
* **Connect**: Připojte se ke službě přes libovolný protokol, který v daném koncovém bodu používá.
* **Opakování**: pokus o připojení může selhat z nějakého důvodu, například pokud se služba od posledního vyřešení adresy koncového bodu přesunula. V takovém případě je třeba opakovat předchozí kroky vyřešit a propojit a tento cyklus se opakuje, dokud nebude připojení úspěšné.

## <a name="connecting-to-other-services"></a>Připojení k jiným službám
Služby, které se vzájemně připojují v rámci clusteru, můžou mít přímý přístup k koncovým bodům jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. Aby bylo snazší se připojit mezi službami, Service Fabric poskytuje další služby, které používají Naming Service. Služba DNS a reverzní proxy služba.


### <a name="dns-service"></a>Služba DNS
Vzhledem k tomu, že řada služeb, zejména služba s využitím kontejnerů, může mít existující název adresy URL, který je schopný vyřešit pomocí standardního protokolu DNS (místo Naming Service protokolu), zejména ve scénářích "výtah a Shift". To je přesně to, co dělá služba DNS. Umožňuje mapovat názvy DNS na název služby, a proto vyřešte IP adresy koncových bodů. 

Jak je znázorněno v následujícím diagramu, služba DNS spuštěná v clusteru Service Fabric mapuje názvy DNS na názvy služeb, které jsou pak vyřešeny Naming Service a vrátí adresy koncových bodů, ke kterým se má připojit. Název DNS pro službu je k dispozici v době vytváření. 

![Diagram znázorňující, jak služba DNS při spuštění v clusteru Service Fabric mapuje názvy DNS na názvy služeb, které jsou pak vyřešeny Naming Service, aby vracely adresy koncových bodů, ke kterým se má připojit.][9]

Další informace o tom, jak používat službu DNS, najdete [v článku Služba DNS v Azure Service Fabric](service-fabric-dnsservice.md) .

### <a name="reverse-proxy-service"></a>Služba reverzního proxy serveru
Reverzní proxy adresují služby v clusteru, které zpřístupňují koncové body HTTP včetně protokolu HTTPS. Reverzní proxy značně zjednodušuje volání dalších služeb a jejich metod pomocí konkrétního formátu identifikátoru URI a zpracovává řešení, připojení, opakování kroků potřebných pro komunikaci jedné služby s jiným pomocí Naming Service. Jinými slovy, skryje Naming Service od vás při volání jiných služeb tím, že je tak jednoduché jako volání adresy URL.

![Diagram, který ukazuje, jak reverzní proxy adresují služby v clusteru, které zpřístupňují koncové body HTTP, včetně protokolu HTTPS.][10]

Další informace o tom, jak používat reverzní službu proxy, najdete [v článku reverzní proxy v Azure Service Fabric](service-fabric-reverseproxy.md) .

## <a name="connections-from-external-clients"></a>Připojení z externích klientů
Služby, které se vzájemně připojují v rámci clusteru, můžou mít přímý přístup k koncovým bodům jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. V některých prostředích se ale cluster může nacházet za nástrojem pro vyrovnávání zatížení, který směruje příchozí přenos dat prostřednictvím omezené sady portů. V těchto případech mohou služby vzájemně komunikovat a řešit adresy pomocí Naming Service, ale je nutné provést další kroky, aby se externí klienti mohli připojovat ke službám.

## <a name="service-fabric-in-azure"></a>Service Fabric v Azure
Cluster Service Fabric v Azure je umístěn za Azure Load Balancer. Veškerý externí provoz do clusteru musí projít nástrojem pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení automaticky přenese příchozí provoz na daný port do náhodného *uzlu* , který má stejný port otevřený. Azure Load Balancer zná jenom porty otevřené v *uzlech*, neví o portech otevřených jednotlivými *službami*.

![Topologie Azure Load Balancer a Service Fabric][3]

Chcete-li například přijmout externí provoz na portu **80**, je nutné nakonfigurovat následující položky:

1. Napište službu, která naslouchá na portu 80. Nakonfigurujte port 80 v ServiceManifest.xml služby a spusťte naslouchací proces ve službě, například webový server, který je místně hostovaný.

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
2. Vytvořte v Azure Cluster Service Fabric a jako typ uzlu, který bude hostitelem služby, zadejte port **80** jako vlastní port koncového bodu. Pokud máte více než jeden typ uzlu, můžete pro službu nastavit *omezení umístění* , aby se zajistilo, že se spustí jenom na typu uzlu, který má otevřený port vlastního koncového bodu.

    ![Otevření portu v typu uzlu][4]
3. Po vytvoření clusteru nakonfigurujte Azure Load Balancer ve skupině prostředků clusteru, abyste přesměrovali provoz na portu 80. Při vytváření clusteru prostřednictvím Azure Portal se nastavuje automaticky pro každý port vlastního koncového bodu, který byl nakonfigurován.

    ![Snímek obrazovky, který zvýrazní pole Port back-endu v části pravidla vyrovnávání zatížení.][5]
4. Azure Load Balancer používá sondu k určení, jestli se má provoz odesílat do konkrétního uzlu. Sonda pravidelně kontroluje koncový bod na jednotlivých uzlech a určuje, zda uzel reaguje. Pokud test neobdrží odpověď po nakonfigurovaném počtu, nástroj pro vyrovnávání zatížení zastaví odesílání provozu do tohoto uzlu. Při vytváření clusteru prostřednictvím Azure Portal se automaticky nastaví sonda pro každý port vlastního koncového bodu, který byl nakonfigurován.

    ![Přeposlání provozu v Azure Load Balancer][8]

Je důležité si uvědomit, že Azure Load Balancer a sonda ví pouze o *uzlech*, nikoli o *službách* spuštěných v uzlech. Azure Load Balancer bude vždycky odesílat data do uzlů, které reagují na test, proto je potřeba zajistit, aby byly služby dostupné v uzlech, které můžou na test reagovat.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: integrované možnosti rozhraní API pro komunikaci
Rozhraní Reliable Services se dodává s několika předem vytvořenými možnostmi komunikace. Rozhodnutí o tom, které z nich bude fungovat nejlépe, závisí na výběru programovacího modelu, komunikačního rozhraní a programovacího jazyka, ve kterém jsou vaše služby napsané.

* **Žádný specifický protokol:**  Pokud nemáte konkrétní volbu komunikačního rozhraní, ale chcete rychle začít pracovat, je ideální volbou pro vás [Vzdálená komunikace služby](service-fabric-reliable-services-communication-remoting.md), která umožňuje používat pro Reliable Services a Reliable Actors silné typy volání vzdálené procedury. Toto je nejjednodušší a nejrychlejší způsob, jak začít s komunikací služby. Vzdálená komunikace služby zajišťuje překlad adres služeb, připojení, opakování a zpracování chyb. To je k dispozici pro aplikace v jazyce C# i Java.
* **Http**: pro nezávislá komunikaci nabízí protokol HTTP standardní volbu s nástroji a servery http dostupnými v mnoha různých jazycích, které podporuje Service Fabric. Služby mohou používat libovolný dostupný zásobník HTTP, včetně [webového rozhraní API ASP.NET](./service-fabric-reliable-services-communication-aspnetcore.md) pro aplikace v jazyce C#. Klienti napsané v jazyce C# mohou `ICommunicationClient` využívat `ServicePartitionClient` třídy a, zatímco pro jazyk Java použijte `CommunicationClient` `FabricServicePartitionClient` třídy a, [pro řešení, připojení HTTP a smyčky opakování](service-fabric-reliable-services-communication.md).
* **WCF**: Pokud máte existující kód, který používá WCF jako komunikační rozhraní, můžete `WcfCommunicationListener` pro klienta použít pro stranu serveru a `WcfCommunicationClient` a `ServicePartitionClient` třídy. K dispozici je však pouze pro aplikace v jazyce C# v clusterech založených na systému Windows. Další podrobnosti najdete v tomto článku o [implementaci komunikačního zásobníku založeného na WCF](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Použití vlastních protokolů a dalších komunikačních rozhraní
Služby můžou používat libovolný protokol nebo rozhraní pro komunikaci, ať už jde o vlastní binární protokol přes sokety TCP, nebo streamování událostí prostřednictvím [azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) nebo [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric poskytuje komunikační rozhraní API, do kterých můžete připojit komunikační zásobník, zatímco veškerou práci, která se má zjistit a připojit, je z vás abstraktní. Další podrobnosti najdete v tomto článku o [komunikačním modelu spolehlivé služby](service-fabric-reliable-services-communication.md) .

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konceptech a rozhraní API dostupných v [Reliable Services komunikačním modelu](service-fabric-reliable-services-communication.md)a začněte rychle s využitím [vzdálené komunikace služby](service-fabric-reliable-services-communication-remoting.md) nebo pořiďte se podrobněji, abyste se dozvěděli, jak napsat naslouchací proces komunikace pomocí [webového rozhraní API s Owin samoobslužným hostováním](./service-fabric-reliable-services-communication-aspnetcore.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
