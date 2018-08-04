---
title: Azure Service Fabric reverzní proxy server | Dokumentace Microsoftu
description: Použijte reverzní proxy Service Fabric pro komunikaci na mikroslužby z zevnitř i zvenčí clusteru.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 521a7b90b971ff3ba867945a4713b1f6dc8dbebc
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503515"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Reverzní proxy server v Azure Service Fabric
Reverzní proxy server, které jsou integrované do Azure Service Fabric pomáhá mikroslužeb spouštěných v clusteru Service Fabric, zjistit a komunikovat s ostatními službami, které mají koncových bodů http.

## <a name="microservices-communication-model"></a>Komunikační model Mikroslužeb
Mikroslužby v Service Fabric spusťte v podmnožině uzlů v clusteru a provést migraci mezi uzly z různých důvodů. Koncové body pro mikroslužby v důsledku toho můžete dynamicky měnit. Pokud chcete zjistit a komunikovat s dalšími službami v clusteru, mikroslužbách musí projít následující kroky:

1. Přeložit umístění služby ve službě pojmenování.
2. Připojení ke službě.
3. Zabalení předchozí kroky ve smyčce, který implementuje překlad služby a opakujte zásady uplatnit na chyby připojení

Další informace najdete v tématu [připojit a komunikace se službami](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikace pomocí reverzního proxy serveru
Reverzní proxy server je služba, která běží na všech uzlech a zpracovává rozlišení koncového bodu, automatické opakování a jiné chyby připojení jménem služby klienta. Reverzní proxy server je možné nakonfigurovat jako zpracovává požadavky od klientů služby použít různé zásady. Pomocí reverzního proxy serveru umožňuje službě klienta pomocí libovolné knihovny komunikace protokolu HTTP na straně klienta a nevyžadují speciální řešení a logika opakování v službě. 

Reverzní proxy server vystavuje jeden nebo více koncových bodů na místním uzlu pro klienta služby pro odesílání požadavků na jiné služby.

![Interní komunikace][1]

> [!NOTE]
> **Podporované platformy**
>
> Reverzní proxy server v Service Fabric v současné době podporuje tyto platformy
> * *Windows Cluster*: Windows 8 a novějším nebo Windows Server 2012 a novější
> * *Cluster s Linuxem*: reverzního proxy serveru není aktuálně k dispozici pro clustery s Linuxem
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Dosažení mikroslužeb z mimo cluster
Výchozí model externí komunikace pro mikroslužby je model opt-in kde každá služba nelze přistupovat přímo z externích klientů. [Nástroj Azure Load Balancer](../load-balancer/load-balancer-overview.md), což je hranice sítě mezi mikroslužbami a externí klienti provádí překlad síťových adres a předává externí požadavky do koncových bodů IP: interní port. Zpřístupnit koncový bod mikroslužeb přímo do externích klientů, musíte nejprve nakonfigurovat nástroj pro vyrovnávání zatížení směrují provoz na každý z portů, který službu používá v clusteru. Kromě toho většina mikroslužeb, zejména stavových mikroslužeb, není živé na všech uzlech clusteru. Mikroslužby, můžou přesouvat mezi uzly na převzetí služeb při selhání. V takovém případě nástroj pro vyrovnávání zatížení nelze určit efektivně umístění cílový uzel repliky, ke kterým předávat provoz.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Dosažení přes reverzní proxy server mimo cluster z mikroslužeb
Namísto konfigurace portu jednotlivé služby v nástroji pro vyrovnávání zatížení, můžete nakonfigurovat pouze port reverzního proxy serveru v nástroji pro vyrovnávání zatížení. Tato konfigurace umožňuje klientům mimo cluster používat služby v clusteru pomocí reverzního proxy serveru bez další konfigurace.

![Externí komunikace][0]

> [!WARNING]
> Když nakonfigurujete v nástroji pro vyrovnávání zatížení port reverzního proxy, všechny mikroslužby v clusteru, která zpřístupňují koncový bod HTTP adresovatelných z mimo cluster. To znamená, že mikroslužeb má být interní může být zjistitelné pomocí určené uživatel se zlými úmysly. Tato potenially uvede závažné ohrožení zabezpečení, které je někdo zneužije; Příklad:
>
> * Uživatel se zlými úmysly může spustit útoku DoS opakovaně voláním interní služba, která nemá dostatečně odolné útoky.
> * Uživatel se zlými úmysly může poskytovat chybně formovaným paketům interní služba následek nežádoucí chování.
> * Služba má být interní může vrátit privátní nebo citlivé informace, které nejsou určeny zpřístupní ke službám mimo cluster, tak vystavení citlivých informací k uživateli se zlými úmysly. 
>
> Ujistěte se, že plně pochopit a zmírnění potenciální bezpečnostní důsledky pro váš cluster a aplikace spuštěny, před zahájením zveřejnit port reverzního proxy. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formát identifikátoru URI pro adresování pomocí reverzního proxy serveru
Reverzní proxy server používá k identifikaci oddíl služby, ke které se mají předávat příchozího požadavku. Formát identifikátoru URI konkrétní URI:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** reverzního proxy serveru můžete nakonfigurovat tak, aby přijímal provoz protokolu HTTP nebo HTTPS. Předávání protokolu HTTPS, najdete v tématu [připojení k službě zabezpečené pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md) až budete mít nastavení reverzní proxy server tak, aby naslouchala na HTTPS.
* **Cluster plně kvalifikovaný název domény (FQDN) | interní IP adresa:** pro externí klienty můžete nakonfigurovat reverzní proxy server tak, aby byl dostupný prostřednictvím doména clusteru, jako je například mycluster.eastus.cloudapp.azure.com. Ve výchozím nastavení reverzní proxy server spouští na všech uzlech. Pro vnitřní provoz reverzní proxy server dostupný v místním hostiteli nebo na jakékoli IP adresy interní uzlu, například 10.0.0.1.
* **Port:** to je port, jako je například 19081, který byl zadán pro reverzní proxy server.
* **ServiceInstanceName:** jde o plně kvalifikovaný název, který se pokoušíte získat přístup bez instance nasazená služba "fabric: /" schéma. Například chcete-li dosáhnout *fabric: / myapp/Moje_služba/* služby, můžete využít *myapp/Moje_služba*.

    Název instance služby rozlišuje velká a malá písmena. Použití různých malých a velkých písmen pro název instance služby v adrese URL způsobí, že žádostí se nezdaří s 404 (Nenalezeno).
* **Přípona cesty:** to skutečné cesty adresy URL, jako je *myapi/hodnoty/přidání/3*, služby, který chcete připojit.
* **PartitionKey:** oddílů služby, jedná se o počítaný oddíl klíč oddílu, který chcete dosáhnout. Všimněte si, že toto je *není* ID identifikátoru GUID oddílu. Tento parametr není vyžadováno pro služby, které používají schéma oddílu typu singleton.
* **PartitionKind:** jde schéma oddílu služby. To může být "Int64Range" nebo "S názvem". Tento parametr není vyžadováno pro služby, které používají schéma oddílu typu singleton.
* **ListenerName** koncové body služby jsou ve tvaru {"Koncové body": {"Listener1": "Koncovém bodě 1", "Listener2": "Endpoint2"...}}. Když služba zveřejňuje několik koncových bodů, Určuje koncový bod, který požadavek klienta by měl být předán. To lze vynechat, pokud má služba pouze jeden naslouchací proces.
* **TargetReplicaSelector** to určuje, jak by měl vybrat cíl replik nebo instancí.
  * Po stavové cílovou službu TargetReplicaSelector může být jeden z následujících: "PrimaryReplica", 'RandomSecondaryReplica' nebo "RandomReplica". Pokud není tento parametr zadán, výchozí hodnota je "PrimaryReplica".
  * Po bezstavové cílovou službu reverzního proxy serveru vybere instanci náhodné oddílu služby k předání požadavku.
* **Časový limit:** Určuje časový limit požadavku HTTP vytvořené reverzní proxy ke službě jménem žádost klienta. Výchozí hodnota je 60 sekund. Toto je volitelný parametr.

### <a name="example-usage"></a>Příklad použití
Jako příklad, Pojďme se *fabric: / MyApp/Moje_služba* služba, která se otevře při naslouchání protokolu HTTP na následující adrese URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Prostředky pro službu jsou následující:

* `/index.html`
* `/api/users/<userId>`

Pokud služba používá schéma, vytváření oddílů singleton *PartitionKey* a *PartitionKind* parametrů řetězce dotazu nejsou vyžadovány a můžete získat přístup ke službě s použitím brány jako:

* Externě: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Interně: `http://localhost:19081/MyApp/MyService`

Pokud služba používá schéma rozdělení oddílů jednotné Int64, *PartitionKey* a *PartitionKind* parametrů řetězce dotazu musí být použité k dosažení oddílu služby:

* Externě: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Interně: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

K dosažení prostředky, které poskytuje službu, stačí umístíte cestu prostředku po názvu služby v adrese URL:

* Externě: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Interně: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Brány se potom přesměrovávala tyto žádosti na adresu URL služby:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Zvláštní zacházení sdílení portů služby
Reverzní proxy Service Fabric se pokusí přeložit adresu služby znovu a zkuste požadavek zopakovat, pokud služba není dostupný. Obecně platí Pokud služba není dostupný, instance služby nebo repliky se přesunul na jiný uzel jako součást své normální životního cyklu. Pokud k tomu dojde, reverzní proxy server může zobrazit chyba připojení sítě označující, že koncový bod je již otevřen v původně přeložit adresu.

Ale replik nebo instancí služby můžou sdílet hostitelského procesu a můžou sdílet také port, když jsou hostované na základě ovladače http.sys web server, včetně:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [WebListener ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

V takovém případě je pravděpodobné, že webový server je k dispozici v hostitelského procesu a reagování na žádosti, ale instance přeložit služby nebo replika již není k dispozici na hostiteli. V takovém případě brány se zobrazí odpověď HTTP 404 z webového serveru. Odpověď HTTP 404 proto může mít dvě různé významy:

- Případ #1: Adresa služby je správný, ale prostředek, který uživatel si vyžádal neexistuje.
- Případ #2: Adresa služby není správná a na prostředek, který uživatel si vyžádal mohou existovat na jiném uzlu.

Prvním případě je normální HTTP 404, která se považují za chybu uživatele. Nicméně v druhém případě uživatel požaduje prostředek, který neexistuje. Reverzní proxy server se nepodařilo najít, protože samotnou službu přesunula. Reverzní proxy server je potřeba znovu přeložit adresu a opakujte žádost.

Reverzní proxy server tedy v případě potřeby způsob k rozlišení mezi těmito dvěma případy. Chcete-li Ujistěte se, že rozdíl, je potřeba Nápověda ze serveru.

* Ve výchozím nastavení reverzní proxy server předpokládá případ #2 a pokusí přeložit a vydejte žádost znovu.
* K označení případ #1 pro reverzní proxy server, služba by měl vrátit následující hlavičku HTTP odpovědi:

  `X-ServiceFabric : ResourceNotFound`

Tuto hlavičku HTTP odpovědi označuje normální HTTP 404 situaci, ve kterém požadovaný prostředek neexistuje, a reverzní proxy server se nepokusí problém znovu vyřešit adresu služby.

## <a name="special-handling-for-services-running-in-containers"></a>Zvláštní zacházení služby spuštěné v kontejnerech

Služby uvnitř kontejnerů, můžete použít proměnnou prostředí `Fabric_NodeIPOrFQDN` k sestavení kompletních [zpětná adresa URL proxy serveru](#uri-format-for-addressing-services-by-using-the-reverse-proxy) stejně jako v následujícím kódu:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Pro místní cluster `Fabric_NodeIPOrFQDN` je ve výchozím nastavení "localhost". Spusťte místní cluster s `-UseMachineName` parametr Ujistěte se, že kontejnery dosáhnout reverzní proxy server běží na uzlu. Další informace najdete v tématu [nakonfigurovat prostředí pro vývojáře k ladění kontejnery](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

## <a name="next-steps"></a>Další postup
* [Nastavení a konfigurace reverzního proxy serveru v clusteru](service-fabric-reverseproxy-setup.md).
* [Nastavení předávání do zabezpečené služba HTTP přes reverzní proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Podívejte se příklad komunikaci pomocí protokolu HTTP mezi službami v [ukázkového projektu na Githubu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Volání vzdálených procedur pomocí vzdálené komunikace modelu Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API, která používá OWIN v modelu Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF komunikace s využitím Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
