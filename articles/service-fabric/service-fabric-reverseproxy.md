---
title: Reverzní proxy server Azure Service Fabric
description: Použijte service fabric reverzní proxy pro komunikaci s mikroslužeb z vnitřní a vně clusteru.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 4fa4c6e46dd786b833087f892d995e85b5d2ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282221"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Reverzní proxy server ve službě Azure Fabric
Reverzní proxy server integrovaný do Azure Service Fabric pomáhá mikroslužbám spuštěným v clusteru Service Fabric zjišťovat a komunikovat s dalšími službami, které mají koncové body http.

## <a name="microservices-communication-model"></a>Komunikační model mikroslužeb
Mikroslužeb v Service Fabric spustit na podmnožinu uzlů v clusteru a můžete migrovat mezi uzly z různých důvodů. V důsledku toho koncové body pro mikroslužby můžete dynamicky měnit. Chcete-li zjistit a komunikovat s jinými službami v clusteru, mikroslužby musí projít následujícíkroky:

1. Vyřešte umístění služby prostřednictvím služby pojmenování.
2. Připojte se ke službě.
3. Zalomit předchozí kroky ve smyčce, která implementuje řešení služby a opakování zásad použít na selhání připojení

Další informace naleznete v tématu [Připojení a komunikace se službami](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikace pomocí reverzního proxy serveru
Reverzní proxy server je služba, která běží na každém uzlu a zpracovává řešení koncového bodu, automatické opakování a další selhání připojení jménem klientských služeb. Reverzní proxy server lze nakonfigurovat tak, aby používat různé zásady, protože zpracovává požadavky z klientských služeb. Použití reverzníproxy umožňuje klientské služby používat všechny knihovny komunikace HTTP na straně klienta a nevyžaduje zvláštní rozlišení a logiku opakování ve službě. 

Reverzní proxy zpřístupňuje jeden nebo více koncových bodů v místním uzlu pro klientské služby pro odesílání požadavků do jiných služeb.

![Interní komunikace][1]

> [!NOTE]
> **Podporované platformy**
>
> Reverzní proxy server v Service Fabric aktuálně podporuje následující platformy
> * *Cluster Windows:* Windows 8 a novější nebo Windows Server 2012 a novější
> * *Cluster Linux*: Reverzní proxy server není v současné době k dispozici pro clustery Linuxu
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Oslovení mikroslužeb mimo cluster
Výchozí externí komunikační model pro mikroslužby je opt-in model, kde každá služba není přístupná přímo z externích klientů. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), což je síťová hranice mezi mikroslužbami a externími klienty, provádí překlad síťových adres a předává externí požadavky interním koncovým bodům IP:portu. Chcete-li koncový bod mikroslužby přímo zpřístupnit externím klientům, musíte nejprve nakonfigurovat nástroje pro vyrovnávání zatížení tak, aby přesměroval přenosy na každý port, který služba používá v clusteru. Kromě toho většina mikroslužeb, zejména stavové mikroslužeb, nežijí na všech uzlech clusteru. Mikroslužby se mohou pohybovat mezi uzly při převzetí služeb při selhání. V takových případech nelze efektivně určit umístění cílového uzlu replik, do kterého by měl předat provoz.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Oslovení mikroslužeb prostřednictvím reverzního proxy serveru mimo cluster
Namísto konfigurace portu jednotlivé služby v zařízení Load Balancer můžete nakonfigurovat pouze port reverzního proxy serveru v zařízení Load Balancer. Tato konfigurace umožňuje klientům mimo cluster dosáhnout služeb uvnitř clusteru pomocí reverzní proxy bez další konfigurace.

![Externí komunikace][0]

> [!WARNING]
> Při konfiguraci portu reverzní proxy v nástroj pro vyrovnávání zatížení, všechny mikroslužby v clusteru, které zveřejňují koncový bod HTTP jsou adresovatelné z mimo clusteru. To znamená, že mikroslužby, které mají být interní, mohou být zjistitelné určeným uživatelem se zlými úmysly. To potenciálně představuje závažné chyby zabezpečení, které lze zneužít; například:
>
> * Uživatel se zlými úmysly může spustit útok odmítnutí služby opakovaným voláním interní služby, která nemá dostatečně zpevněný prostor pro útok.
> * Uživatel se zlými úmysly může doručovat poškozené pakety do interní služby, což vede k neúmyslnému chování.
> * Služba, která má být interní, může vrátit soukromé nebo citlivé informace, které nejsou určeny k vystavení službám mimo cluster, a tím vystavit tyto citlivé informace uživateli se zlými úmysly. 
>
> Ujistěte se, že plně pochopit a zmírnit potenciální důsledky zabezpečení pro váš cluster a aplikace spuštěné na něm, než uděláte reverzní proxy port veřejné. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formát identifikátoru URI pro adresování služeb pomocí reverzního proxy serveru
Reverzní proxy server používá určitý formát identifikátoru uniformy (URI) k identifikaci oddílu služby, do kterého by měl být příchozí požadavek předán:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** Reverzní proxy server lze nakonfigurovat tak, aby přijímal přenosy HTTP nebo HTTPS. O předávání https, viz [Připojení k zabezpečené službě s reverzní proxy,](service-fabric-reverseproxy-configure-secure-communication.md) jakmile máte reverzní proxy nastavení poslouchat na HTTPS.
* **Cluster plně kvalifikovaný název domény (FQDN) | interní IP adresa:** Pro externí klienty můžete nakonfigurovat reverzní proxy server tak, aby byl dostupný prostřednictvím domény clusteru, například mycluster.eastus.cloudapp.azure.com. Ve výchozím nastavení je reverzní proxy server spuštěn na každém uzlu. Pro interní provoz reverzní proxy lze dosáhnout na localhost nebo na libovolné matné adresy ip, například 10.0.0.1.
* **Přístav:** Toto je port, například 19081, který byl určen pro reverzní proxy server.
* **ServiceInstanceName:** Toto je plně kvalifikovaný název instance nasazené služby, ke které se pokoušíte dosáhnout bez "fabric:/" Schéma. Například k dosažení *fabric:/myapp/myservice/* service, budete používat *myapp/myservice*.

    Název instance služby rozlišuje malá a velká písmena. Použití jiného písmene pro název instance služby v adrese URL způsobí, že požadavky se nezdaří s 404 (nebyl nalezen).
* **Přípona cesta:** Toto je skutečná cesta URL, například *myapi/values/add/3*, pro službu, ke které se chcete připojit.
* **PartitionKey:** Pro dělené služby se jedná o vypočítaný klíč oddílu oddílu, který chcete dosáhnout. Všimněte si, že toto *není* identifikátor GUID ID oddílu. Tento parametr není vyžadován pro služby, které používají schéma oddílu singleton.
* **Oddíldruh:** Toto je schéma oddílu služby. To může být 'Int64Range' nebo 'Pojmenované'. Tento parametr není vyžadován pro služby, které používají schéma oddílu singleton.
* **Název listeneru** Koncové body ze služby jsou ve tvaru {"Koncové body":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. Když služba zpřístupní více koncových bodů, identifikuje koncový bod, který by měl být předán požadavek klienta. To lze vynechat, pokud služba má pouze jeden naslouchací proces.
* **Odmítač replik target** Určuje, jak má být vybrána cílová replika nebo instance.
  * Pokud je cílová služba stavová, targetreplicaselector může být jeden z následujících: "PrimaryReplica", 'RandomSecondaryReplica' nebo 'RandomReplica'. Pokud tento parametr není zadán, výchozí hodnota je "PrimaryReplica".
  * Pokud je cílová služba bezstavová, reverzní proxy server vybere náhodnou instanci oddílu služby, na kterou má požadavek předat dál.
* **Časový čas:**  Určuje časový limit pro požadavek HTTP vytvořený reverzníproxy služby jménem požadavku klienta. Výchozí hodnota je 60 sekund. Toto je volitelný parametr.

### <a name="example-usage"></a>Příklad použití
Jako příklad vezměme *fabric:/MyApp/MyService službu,* která otevře naslouchací proces HTTP na následující adrese URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Jsou uvedeny zdroje pro službu:

* `/index.html`
* `/api/users/<userId>`

Pokud služba používá schéma dělení singleton, parametry řetězce dotazu *PartitionKey* a *PartitionKind* nejsou vyžadovány a služby lze dosáhnout pomocí brány jako:

* Externě:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Interně:`http://localhost:19081/MyApp/MyService`

Pokud služba používá jednotné schéma dělení Int64, parametry řetězce dotazu *PartitionKey* a *PartitionKind* musí být použity k dosažení oddílu služby:

* Externě:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Interně:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Chcete-li se dostat k prostředkům, které služba zveřejňuje, jednoduše umístěte cestu k prostředku za název služby v adrese URL:

* Externě:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Interně:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Brána pak předá tyto požadavky na adresu URL služby:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Zvláštní manipulace pro služby sdílení portů
Reverzní proxy server Service Fabric se pokusí znovu vyřešit adresu služby a opakujte požadavek, když není k zastižení služby. Obecně platí, že pokud službu nelze dosáhnout, instance služby nebo replika byla přesunuta do jiného uzlu jako součást jeho normální životní cyklus. V takovém případě může zobrazit chybu zpětného proxy serveru označující, že koncový bod již není otevřen na původně vyřešené adrese.

Repliky nebo instance služeb však mohou sdílet hostitelský proces a mohou také sdílet port, pokud je hostován webovým serverem založeným na protokolu http.sys, včetně:

* [Systém.Net.httplistener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET základní webový listener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

V takovém případě je pravděpodobné, že webový server je k dispozici v hostitelském procesu a reagovat na požadavky, ale vyřešené instance služby nebo replika již není k dispozici na hostiteli. V takovém případě brána obdrží odpověď HTTP 404 z webového serveru. Odpověď HTTP 404 tedy může mít dva odlišné významy:

- Případ #1: Adresa služby je správná, ale prostředek, který uživatel požadoval, neexistuje.
- Případ #2: Adresa služby je nesprávná a prostředek, který uživatel požadoval, může existovat v jiném uzlu.

První případ je normální HTTP 404, který je považován za chybu uživatele. V druhém případě však uživatel požádal o prostředek, který existuje. Reverzní proxy server jej nemohl najít, protože samotná služba byla přesunuta. Reverzní proxy server musí adresu znovu vyřešit a požadavek zopakovat.

Reverzní proxy proto potřebuje způsob, jak rozlišovat mezi těmito dvěma případy. Chcete-li tento rozdíl, je vyžadována nápověda ze serveru.

* Ve výchozím nastavení předpokládá reverzní proxy server případ #2 a pokusí se požadavek znovu vyřešit a vydat.
* Chcete-li označit případ #1 reverzní proxy server, služba by měla vrátit následující hlavičku odpovědi HTTP:

  `X-ServiceFabric : ResourceNotFound`

Tato hlavička odpovědi HTTP označuje normální situaci HTTP 404, ve které požadovaný prostředek neexistuje a reverzní proxy server se znovu nepokusí vyřešit adresu služby.

## <a name="special-handling-for-services-running-in-containers"></a>Speciální manipulace pro služby provozované v kontejnerech

Pro služby spuštěné uvnitř kontejnerů `Fabric_NodeIPOrFQDN` můžete použít proměnnou prostředí k vytvoření [adresy URL reverzní proxy](#uri-format-for-addressing-services-by-using-the-reverse-proxy) jako v následujícím kódu:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Pro místní cluster `Fabric_NodeIPOrFQDN` je ve výchozím nastavení nastavenna na "localhost". Spusťte místní `-UseMachineName` cluster s parametrem a ujistěte se, že kontejnery mohou dosáhnout reverzní proxy spuštěné na uzlu. Další informace naleznete v [tématu Konfigurace vývojářského prostředí pro ladění kontejnerů](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Služby Service Fabric, které běží v kontejnerech Docker Compose vyžadují speciální docker-compose.yml *porty sekce* http: nebo https: konfigurace. Další informace najdete [v tématu Podpora nasazení Dockeru Compose ve službě Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Další kroky
* [Nastavte a nakonfigurujte reverzní proxy server v clusteru](service-fabric-reverseproxy-setup.md).
* [Nastavení předávání pro zabezpečení služby HTTP pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnostika událostí reverzního proxy serveru](service-fabric-reverse-proxy-diagnostics.md)
* Podívejte se na příklad http komunikace mezi službami v [ukázkovém projektu na GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Vzdálená volání procedur pomocí vzdálené komunikace spolehlivých služeb](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, které používá OWIN ve spolehlivých službách](service-fabric-reliable-services-communication-webapi.md)
* [Komunikace WCF pomocí spolehlivých služeb](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
