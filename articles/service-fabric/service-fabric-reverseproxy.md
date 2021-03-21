---
title: Reverzní proxy server Azure Service Fabric
description: Pomocí reverzního proxy serveru Service Fabric můžete komunikovat s mikroslužbami zevnitř a mimo cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791100"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Reverzní proxy server v Azure Service Fabric
Reverzní proxy server integrovaný do Azure Service Fabric pomáhá mikroslužbám běžícím v clusteru Service Fabric zjišťovat a komunikovat s dalšími službami, které mají koncové body http.

## <a name="microservices-communication-model"></a>Komunikační model mikroslužeb
Mikroslužby v Service Fabric běží na podmnožině uzlů v clusteru a můžou migrovat mezi uzly z různých důvodů. V důsledku toho se koncové body pro mikroslužby můžou dynamicky měnit. Pro zjišťování a komunikaci s dalšími službami v clusteru musí mikroslužba projít následujícími kroky:

1. Vyřešte umístění služby prostřednictvím názvové služby.
2. Připojte se ke službě.
3. Zabalte předchozí kroky ve smyčce, které implementují řešení služeb a zásady opakování, které se použijí při selhání připojení.

Další informace najdete v tématu [připojení a komunikace se službami](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikace pomocí reverzního proxy serveru
Reverzní proxy je služba, která běží na všech uzlech a zpracovává překlad koncových bodů, automatické opakování a další selhání připojení jménem služeb klienta. Reverzní proxy server je možné nakonfigurovat tak, aby při zpracovávání požadavků od služeb klienta mohl používat různé zásady. Použití reverzního proxy umožňuje, aby služba klienta používala jakékoli komunikační knihovny HTTP na straně klienta a nevyžadovala ve službě speciální rozlišení a logiku opakování. 

Reverzní proxy zpřístupňuje jeden nebo více koncových bodů v místním uzlu pro klientské služby, které se použijí pro odesílání požadavků do jiných služeb.

![Interní komunikace][1]

> [!NOTE]
> **Podporované platformy**
>
> Reverzní proxy server v Service Fabric aktuálně podporuje následující platformy
> * *Cluster Windows*: Windows 8 a novější nebo Windows Server 2012 a novější
> * *Cluster se systémem Linux*: reverzní proxy server není aktuálně k dispozici pro clustery se systémem Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Dosahování mikroslužeb mimo cluster
Výchozí externí komunikační model pro mikroslužby je model souhlasu, ve kterém se ke každé službě nedá získat přímý pøístup z externích klientů. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), což je hranice sítě mezi mikroslužbami a externími klienty, provádí překlad síťových adres a předávají externí požadavky do interní IP adresy: koncové body portů. Aby byl koncový bod mikroslužeb přímo přístupný pro externí klienty, je nutné nejprve nakonfigurovat Load Balancer pro přenos provozu na každý port, který služba používá v clusteru. Kromě toho většina mikroslužeb, zejména stavové mikroslužby, není na všech uzlech clusteru živá. Mikroslužby se můžou přesouvat mezi uzly při převzetí služeb při selhání. V takových případech Load Balancer nemůže efektivně určit umístění cílového uzlu replik, na které by měl přesměrovat provoz.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Dosažení mikroslužeb prostřednictvím reverzního proxy serveru mimo cluster
Místo konfigurace portu jednotlivých služeb v Load Balancer můžete nakonfigurovat pouze port reverzního proxy serveru v Load Balancer. Tato konfigurace umožňuje klientům mimo cluster dosáhnout služeb v clusteru pomocí reverzního proxy serveru bez další konfigurace.

![Externí komunikace][0]

> [!WARNING]
> Když nakonfigurujete port reverzního proxy serveru v Load Balancer, všechny mikroslužby v clusteru, které zveřejňují koncový bod HTTP, se budou adresovat mimo cluster. To znamená, že mikroslužby, které mají být interní, mohou být zjistitelné zjištěným uživatelem se zlými úmysly. To potenciálně představuje závažné chyby zabezpečení, které je možné zneužít; například:
>
> * Uživatel se zlými úmysly může spustit útok s cílem odepření služby Opakovaným voláním interní služby, která nemá dostatečně posílenou plochu pro útoky.
> * Uživatel se zlými úmysly může doručovat poškozené pakety do interní služby, což vede k neúmyslnému chování.
> * Služba, která má být interní, může vracet soukromé nebo citlivé informace, které nejsou určené k zpřístupnění službám mimo cluster, takže tyto citlivé informace vystavuje uživatel se zlými úmysly. 
>
> Ujistěte se, že plně rozumíte a zmírnit potenciální důsledky zabezpečení pro váš cluster a aplikace, které jsou v něm spuštěné, a teprve potom zajistěte veřejný port reverzního proxy serveru. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formát identifikátoru URI pro adresování služeb pomocí reverzního proxy serveru
Reverzní proxy server používá specifický formát identifikátoru URI (Uniform Resource Identifier) k identifikaci oddílu služby, do nějž má být příchozí požadavek předán:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** Reverzní proxy server je možné nakonfigurovat tak, aby přijímal přenos HTTP nebo HTTPS. V případě předávání HTTPS se [pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md) přihlaste k zabezpečené službě, jakmile budete mít nastavení reverzního proxy serveru naslouchat na https.
* **Plně kvalifikovaný název domény clusteru (FQDN) | interní IP adresa:** U externích klientů můžete nakonfigurovat reverzní proxy server tak, aby byl dosažitelný prostřednictvím domény clusteru, například mycluster.eastus.cloudapp.azure.com. Ve výchozím nastavení se reverzní proxy spouští na všech uzlech. U interních přenosů můžete reverzní proxy dosáhnout na localhost nebo na jakékoli interní IP adresy uzlu, například 10.0.0.1.
* **Port:** Toto je port, například 19081, který byl zadán pro reverzní proxy.
* **ServiceInstanceName:** Toto je plně kvalifikovaný název nasazené instance služby, ke které se snažíte získat přístup bez "Fabric:/". programu. Například pro dosažení *prostředku Fabric:/MyApp/mojesluzba/* Service byste měli použít *MyApp/mojesluzba*.

    V názvu instance služby se rozlišují velká a malá písmena. Použití různých malých a velkých písmen v názvu instance služby v adrese URL způsobuje selhání požadavků s 404 (Nenalezeno).
* **Cesta k příponě:** Jedná se o skutečnou cestu URL, například *MyAPI/Values/Add/3*, pro službu, ke které se chcete připojit.
* **PartitionKey:** U dělené služby se jedná o vypočtený klíč oddílu oddílu, ke kterému chcete získat přístup. Všimněte si, že *se nejedná o identifikátor* GUID ID oddílu. Tento parametr není vyžadován pro služby, které používají schéma oddílu singleton.
* **PartitionKind:** Toto je schéma oddílu služby. Může to být "Int64Range" nebo "pojmenovaný". Tento parametr není vyžadován pro služby, které používají schéma oddílu singleton.
* **Naslouchací proces** Koncové body služby jsou ve formátu {"Endpoints": {"Listener1": "Endpoint1", "Listener2": "pro endpoint2 u"...}}. Když služba vystaví více koncových bodů, identifikuje koncový bod, na který se má klientský požadavek přeslat. Tato možnost může být vynechána, pokud má služba pouze jeden naslouchací proces.
* **TargetReplicaSelector** Tím se určuje, jak by měla být vybraná cílová replika nebo instance.
  * Když je cílová služba stavová, může být TargetReplicaSelector jedna z následujících: "PrimaryReplica", "RandomSecondaryReplica" nebo "RandomReplica". Pokud tento parametr není zadán, výchozí hodnota je ' PrimaryReplica '.
  * Pokud je cílová služba Bezstavová, reverzní proxy server vybere náhodnou instanci oddílu služby, aby předal požadavek.
* **Časový limit:**  Určuje časový limit pro požadavek HTTP vytvořený reverzním proxy serverem jménem žádosti klienta. Výchozí hodnota je 120 sekund. Toto je volitelný parametr.

### <a name="example-usage"></a>Příklad použití
Příklad: Pojďme využít službu *Fabric:/MyApp/mojesluzba* , která otevře NASLOUCHACÍ proces http na následující adrese URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Níže jsou uvedené prostředky pro službu:

* `/index.html`
* `/api/users/<userId>`

Pokud služba používá schéma dělení na oddíly singleton, parametry řetězce dotazu *PartitionKey* a *PartitionKind* se nevyžadují a služba může být dostupná pomocí brány jako:

* Externě `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Užit `http://localhost:19081/MyApp/MyService`

Pokud služba používá jednotné schéma dělení na oddíly (Int64), musí se pro přístup k oddílu služby použít parametry řetězce dotazu *PartitionKey* a *PartitionKind* :

* Externě `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Užit `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Pokud chcete dosáhnout prostředků, které služba zpřístupňuje, jednoduše umístěte cestu prostředku za název služby v adrese URL:

* Externě `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Užit `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Brána pak tyto požadavky přepošle na adresu URL služby:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Speciální zpracování pro služby pro sdílení portů
Service Fabric reverzní proxy server se znovu pokusí znovu přeložit adresu služby a pokusit se o tuto žádost znovu, pokud není dostupná služba. Obecně platí, že pokud nelze získat přístup k službě, instance služby nebo replika byla přesunuta do jiného uzlu v rámci normálního životního cyklu. V takovém případě může reverzní proxy obdržet chybu připojení k síti, což znamená, že koncový bod již není otevřen na původně přeložené adrese.

Repliky nebo instance služby však mohou sdílet hostitelský proces a mohou také sdílet port, pokud je hostován webovým serverem http.sys, včetně:

* [System .NET. HttpListener](/dotnet/api/system.net.httplistener)
* [ASP.NET Core weblisten](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

V takové situaci je pravděpodobný, že webový server je dostupný v hostitelském procesu a reaguje na požadavky, ale vyřešená instance služby nebo replika už není na hostiteli dostupná. V takovém případě brána dostane odpověď HTTP 404 od webového serveru. Proto odpověď HTTP 404 může mít dvě odlišná význam:

- Případ #1: adresa služby je správná, ale prostředek, který uživatel požaduje, neexistuje.
- Případ #2: adresa služby je nesprávná a prostředek, který uživatel požadoval, může existovat na jiném uzlu.

První případ je normální protokol HTTP 404, který se považuje za chybu uživatele. Ve druhém případě však uživatel požadoval prostředek, který existuje. Reverzní proxy server nebyl schopen najít, protože služba byla přesunuta. Reverzní proxy server musí tuto adresu znovu přeložit a opakovat požadavek.

Reverzní proxy server tak potřebuje způsob, jak rozlišovat mezi těmito dvěma případy. Aby se tento rozdíl rozvedl, je nutné zadat nápovědu ze serveru.

* Ve výchozím nastavení předpokládá reverzní proxy případ #2 a pokusí se požadavek vyřešit a vydat znovu.
* Chcete-li určit velikost písmen #1 reverznímu proxy, služba by měla vrátit následující hlavičku HTTP odpovědi:

  `X-ServiceFabric : ResourceNotFound`

Tato hlavička HTTP odpovědi označuje normální situaci HTTP 404, ve které požadovaný prostředek neexistuje, a reverzní proxy server se nebude pokoušet znovu přeložit adresu služby.

## <a name="special-handling-for-services-running-in-containers"></a>Speciální zpracování pro služby běžící v kontejnerech

Pro služby běžící uvnitř kontejnerů můžete použít proměnnou prostředí `Fabric_NodeIPOrFQDN` k vytvoření [adresy URL reverzního proxy serveru](#uri-format-for-addressing-services-by-using-the-reverse-proxy) , jak je uvedeno v následujícím kódu:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Pro místní cluster `Fabric_NodeIPOrFQDN` je ve výchozím nastavení nastavená možnost "localhost". Spusťte místní cluster s `-UseMachineName` parametrem, abyste se ujistili, že kontejnery mají přístup k reverznímu proxy serveru běžícímu na uzlu. Další informace najdete v tématu [Konfigurace vývojového prostředí pro ladění kontejnerů](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Service Fabric služby, které běží v kontejnerech Docker Compose, vyžadují speciální *část portů* Docker-Compose. yml http: nebo https: Configuration. Další informace najdete v tématu [Podpora nasazení Docker Compose ve službě Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Další kroky
* [Nastavení a konfigurace reverzního proxy serveru v clusteru](service-fabric-reverseproxy-setup.md).
* [Nastavení přesměrování na zabezpečenou službu HTTP pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnostika událostí reverzního proxy serveru](service-fabric-reverse-proxy-diagnostics.md)
* Podívejte se na příklad komunikace HTTP mezi službami ve [vzorovém projektu na GitHubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Vzdálená volání procedur s Reliable Services Vzdálená komunikace](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, které používá OWIN v Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Komunikace WCF pomocí Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
