---
title: Připojení a komunikace se službami v Azure Service Fabric
description: Zjistěte, jak řešit, připojit a komunikovat se službami v Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: e57d169decf482f8b8be1e3b31a07690bc222c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458241"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Připojení a komunikace se službami v service fabric
V Service Fabric služba běží někde v clusteru Service Fabric, obvykle distribuovány mezi více virtuálních počítačích. Může být přesunut z jednoho místa na druhé, buď vlastníkem služby, nebo automaticky service fabric. Služby nejsou staticky vázány na konkrétní počítač nebo adresu.

Aplikace Service Fabric se obvykle skládá z mnoha různých služeb, kde každá služba provádí specializované úlohy. Tyto služby mohou vzájemně komunikovat a vytvořit tak úplnou funkci, například vykreslovat různé části webové aplikace. Existují také klientské aplikace, které se připojují ke službám a komunikují s ním. Tento dokument popisuje, jak nastavit komunikaci s a mezi službami v Service Fabric.

## <a name="bring-your-own-protocol"></a>Přineste si vlastní protokol
Service Fabric pomáhá spravovat životní cyklus vašich služeb, ale nerozhoduje o tom, co vaše služby dělají. To zahrnuje komunikaci. Když je vaše služba otevřena Service Fabric, to je příležitost vaší služby nastavit koncový bod pro příchozí požadavky, pomocí libovolného protokolu nebo komunikační zásobníku, který chcete. Vaše služba bude naslouchat na normální **adresu IP:port** pomocí libovolného schématu adresování, jako je například identifikátor URI. Více instancí služby nebo repliky mohou sdílet hostitelský proces, v takovém případě budou muset použít různé porty nebo použít mechanismus sdílení portů, jako je například ovladač jádra http.sys v systému Windows. V obou případech musí být každá instance služby nebo replika v hostitelském procesu jednoznačně adresovatelná.

![koncové body služby][1]

## <a name="service-discovery-and-resolution"></a>Zjišťování a řešení služeb
V distribuovaném systému se služby mohou v průběhu času přesouvat z jednoho počítače do druhého. K tomu může dojít z různých důvodů, včetně vyrovnávání prostředků, upgradů, převzetí služeb při selhání nebo horizontálních navýšení kapacity. To znamená, že adresy koncového bodu služby se mění, když se služba přesune do uzlů s různými adresami IP, a může se otevřít na různých portech, pokud služba používá dynamicky vybraný port.

![Distribuce služeb][7]

Service Fabric poskytuje službu zjišťování a řešení problémů nazvanou Pojmenování. Služba pojmenování udržuje tabulku, která mapuje pojmenované instance služby na adresy koncového bodu, na kterých naslouchají. Všechny pojmenované instance služby v Service Fabric mají jedinečné `"fabric:/MyApplication/MyService"`názvy reprezentované jako identifikátory URI, například . Název služby se nemění po dobu životnosti služby, je to pouze adresy koncového bodu, který se může změnit při přesunutí služeb. To je obdobou webových stránek, které mají konstantní adresy URL, ale kde se může změnit IP adresa. A podobně jako DNS na webu, který řeší adresy URL webových stránek na IP adresy, Service Fabric má registrátora, který mapuje názvy služeb na jejich adresu koncového bodu.

![koncové body služby][2]

Řešení a připojení ke službám zahrnuje následující kroky spustit ve smyčce:

* **Vyřešit**: Získejte koncový bod, který služba publikovala ze služby pojmenování.
* **Připojení**: Připojte se ke službě přes jakýkoli protokol, který používá v tomto koncovém bodě.
* **Opakování**: Pokus o připojení může selhat z libovolného počtu důvodů, například pokud se služba od posledního vyřešení adresy koncového bodu přesunula. V takovém případě je třeba opakovat předchozí kroky řešení a připojení a tento cyklus se opakuje, dokud nebude připojení úspěšné.

## <a name="connecting-to-other-services"></a>Připojení k jiným službám
Služby, které se vzájemně připojují v rámci clusteru, mají obecně přímý přístup ke koncovým bodům jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. Chcete-li usnadnit připojení mezi službami, Service Fabric poskytuje další služby, které používají službu pojmenování. Služba DNS a reverzní proxy služba.


### <a name="dns-service"></a>Služba DNS
Vzhledem k tomu, že mnoho služeb, zejména kontejnerizované služby, může mít existující název adresy URL, je velmi pohodlné, že je možné je vyřešit pomocí standardního protokolu DNS (spíše než protokolu pojmenování), zejména ve scénářích aplikace "lift and shift". To je přesně to, co služba DNS dělá. Umožňuje mapovat názvy DNS na název služby a tím předávat adresy IP koncového bodu. 

Jak je znázorněno na následujícím diagramu, služba DNS spuštěná v clusteru Service Fabric mapuje názvy DNS na názvy služeb, které jsou poté službou Pojmenování vyřešeny a vracejí adresy koncového bodu, ke kterým se chcete připojit. Název DNS pro službu je k dispozici v době vytvoření. 

![koncové body služby][9]

Další podrobnosti o tom, jak používat službu DNS, najdete v článku Služba DNS v článku [Azure Service Fabric.](service-fabric-dnsservice.md)

### <a name="reverse-proxy-service"></a>Reverzní proxy služba
Reverzní proxy adresy služby v clusteru, který zveřejňuje koncové body HTTP včetně protokolu HTTPS. Reverzní proxy server výrazně zjednodušuje volání jiných služeb a jejich metod tím, že má určitý formát URI a zpracovává řešení, připojení, opakování kroky potřebné pro jednu službu komunikovat s jinou pomocí služby pojmenování. Jinými slovy, skryje službu pojmenování před vámi při volání jiných služeb tím, že je to tak jednoduché jako volání adresy URL.

![koncové body služby][10]

Další podrobnosti o tom, jak používat reverzní proxy služby naleznete reverzní proxy server v článku [Azure Service Fabric.](service-fabric-reverseproxy.md)

## <a name="connections-from-external-clients"></a>Připojení z externích klientů
Služby, které se vzájemně připojují v rámci clusteru, mají obecně přímý přístup ke koncovým bodům jiných služeb, protože uzly v clusteru jsou ve stejné místní síti. V některých prostředích však může být cluster za vyvyčovávacím souborem zatížení, který směruje externí příchozí přenosy dat prostřednictvím omezené sady portů. V těchto případech mohou služby stále komunikovat mezi sebou a řešit adresy pomocí služby pojmenování, ale je nutné podniknout další kroky, aby se externí klienti mohli připojit ke službám.

## <a name="service-fabric-in-azure"></a>Service Fabric v Azure
Cluster Service Fabric v Azure se umístí za Azure Balancer. Veškerý externí provoz do clusteru musí projít prostřednictvím systému vyrovnávání zatížení. Balancer automaticky přesměruje příchozí přenosy na daném portu do náhodného *uzlu,* který má otevřený stejný port. Azure Balancer ví jenom o portech otevřených na *uzlech*, neví o portech otevřených jednotlivými *službami*.

![Azure Load Balancer a service fabric topologie][3]

Chcete-li například přijmout externí provoz na portu **80**, je třeba nakonfigurovat následující věci:

1. Napište službu, která naslouchá na portu 80. Nakonfigurujte port 80 v souboru ServiceManifest.xml služby a otevřete naslouchací proces ve službě, například webový server s vlastním hostitelem.

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
2. Vytvořte cluster Service Fabric v Azure a zadejte port **80** jako vlastní port koncového bodu pro typ uzlu, který bude hostitelem služby. Pokud máte více než jeden typ uzlu, můžete nastavit *omezení umístění* na službu, aby bylo zajištěno, že běží pouze na typu uzlu, který má vlastní port koncového bodu otevřen.

    ![Otevření portu u typu uzlu][4]
3. Po vytvoření clusteru nakonfigurujte Azure Balancer ve skupině prostředků clusteru pro předávání provozu na portu 80. Při vytváření clusteru prostřednictvím portálu Azure se to toto nastavení nastavuje automaticky pro každý vlastní port koncového bodu, který byl nakonfigurován.

    ![Předápčit provoz v Azure Balancer][5]
4. Azure Balancer používá sondu k určení, zda chcete odeslat provoz do určitého uzlu. Sonda pravidelně kontroluje koncový bod v každém uzlu k určení, zda uzel reaguje. Pokud sonda neobdrží odpověď po nakonfigurovaném počtu, systém vyrovnávání zatížení přestane odesílat provoz do tohoto uzlu. Při vytváření clusteru prostřednictvím portálu Azure se automaticky nastaví sonda pro každý vlastní port koncového bodu, který byl nakonfigurovaný.

    ![Předápčit provoz v Azure Balancer][8]

Je důležité si uvědomit, že Azure Balancer a sonda vědět pouze o *uzly*, nikoli *služby* spuštěné na uzlech. Azure Balancer bude vždy odesílat provoz do uzlů, které reagují na sondu, takže je třeba dbát na to, aby služby jsou k dispozici na uzlech, které jsou schopny reagovat na sondu.

## <a name="reliable-services-built-in-communication-api-options"></a>Spolehlivé služby: Vestavěné možnosti rozhraní API pro komunikaci
Rámec spolehlivé služby je dodáván s několika předem sestavenými možnostmi komunikace. Rozhodnutí o tom, který z nich bude fungovat nejlépe pro vás závisí na výběru programovací model, komunikační rámec a programovací jazyk, který jsou zapsány v.

* **Žádný konkrétní protokol:**  Pokud nemáte konkrétní výběr komunikační horozhraní, ale chcete něco rychle zprovoznit, pak je ideální volbou pro vás [vzdálené komunikace služby](service-fabric-reliable-services-communication-remoting.md), která umožňuje volání vzdálenéprocedury silného typu pro spolehlivé služby a spolehlivé objekty actor. Jedná se o nejjednodušší a nejrychlejší způsob, jak začít s komunikací služeb. Vzdálené komunikace služby zpracovává řešení adres služby, připojení, opakování a zpracování chyb. To je k dispozici pro aplikace Jazyka C# i Java.
* **HTTP**: Pro komunikaci bez jazykového jazyka poskytuje protokol HTTP standardní volbu s nástroji a servery HTTP dostupnými v mnoha různých jazycích, všechny podporované službou Service Fabric. Služby mohou používat libovolný zásobník protokolu HTTP, který je k dispozici, včetně [ASP.NET webovérozhraní API](service-fabric-reliable-services-communication-webapi.md) pro aplikace jazyka C#. Klienti napsané v jazyce C# můžete využít `ICommunicationClient` a `CommunicationClient` `FabricServicePartitionClient` `ServicePartitionClient` třídy, vzhledem k tomu, pro Java, použijte a třídy, pro řešení [služeb, http připojení a opakování smyčky](service-fabric-reliable-services-communication.md).
* **WCF**: Pokud máte existující kód, který používá WCF jako `WcfCommunicationListener` komunikační rozhraní, `WcfCommunicationClient` `ServicePartitionClient` pak můžete použít pro straně serveru a třídy pro klienta. To je však k dispozici pouze pro aplikace jazyka C# v clusterech založených na systému Windows. Další podrobnosti naleznete v tomto článku o [implementaci zásobníku komunikace na základě WCF](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Použití vlastních protokolů a dalších komunikačních rámců
Služby můžete použít libovolný protokol nebo rámec pro komunikaci, ať už je to vlastní binární protokol přes tcp sokety nebo streamování událostí prostřednictvím [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) nebo [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Service Fabric poskytuje komunikační api, které můžete připojit zásobníkkomunikace do, zatímco všechny práce na zjišťování a připojení je abstrahována od vás. Další podrobnosti naleznete v tomto článku o [modelu komunikace spolehlivé služby.](service-fabric-reliable-services-communication.md)

## <a name="next-steps"></a>Další kroky
Další informace o konceptech a rozhraních API dostupných v [komunikačním modelu spolehlivých služeb](service-fabric-reliable-services-communication.md)můžete rychle začít s [vzdálenou službou](service-fabric-reliable-services-communication-remoting.md) nebo se podrobně dozvíte, jak napsat naslouchací proces komunikace pomocí [webového rozhraní API s vlastním hostitelem OWIN](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
