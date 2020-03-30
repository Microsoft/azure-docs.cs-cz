---
title: Použití správy rozhraní Azure API s mikroslužbami nasazenými ve službě Azure Kubernetes | Dokumenty společnosti Microsoft
description: Tento článek popisuje možnosti nasazení správy rozhraní API pomocí Služby AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480993"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Použití správy rozhraní Azure API s mikroslužbami nasazenými ve službě Azure Kubernetes

Mikroslužby jsou ideální pro vytváření api. Se [službou Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) můžete rychle nasadit a provozovat [architekturu založenou na mikroslužbách](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) v cloudu. Potom můžete využít [Azure API Management](https://aka.ms/apimrocks) (API Management) publikovat mikroslužeb jako rozhraní API pro interní a externí spotřebu. Tento článek popisuje možnosti nasazení správy rozhraní API s AKS. Předpokládá základní znalosti Kubernetes, API Management a Azure sítí. 

## <a name="background"></a>Pozadí

Při publikování mikroslužeb jako rozhraní API pro spotřebu může být náročné spravovat komunikaci mezi mikroslužeb a klienty, kteří je spotřebovávají. Existuje velké množství průřezových problémů, jako je ověřování, autorizace, škrcení, ukládání do mezipaměti, transformace a monitorování. Tyto obavy jsou platné bez ohledu na to, zda jsou mikroslužby vystaveny interním nebo externím klientům. 

Vzor [brány rozhraní API](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) řeší tyto obavy. Brána rozhraní API slouží jako přední dveře mikroslužeb, odděluje klienty od mikroslužeb, přidává další vrstvu zabezpečení a snižuje složitost mikroslužeb tím, že odstraňuje zátěž zpracování průřezových problémů. 

[Azure API Management](https://aka.ms/apimrocks) je řešení na klíč, které řeší vaše potřeby brány rozhraní API. Můžete rychle vytvořit konzistentní a moderní bránu pro vaše mikroslužby a publikovat je jako API. Jako řešení pro správu rozhraní API s celým životním cyklem poskytuje také další funkce, včetně samoobslužného vývojářského portálu pro zjišťování rozhraní API, správu životního cyklu rozhraní API a analýzy rozhraní API.

Při společném použití aks a api management poskytují platformu pro nasazení, publikování, zabezpečení, monitorování a správu rozhraní API založených na mikroslužbách. V tomto článku projdeme několik možností nasazení AKS ve spojení se správou rozhraní API. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes služby a API

V clusteru Kubernetes kontejnery jsou nasazeny v [pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/), které jsou dočasné a mají životní cyklus. Když pracovní uzel zemře, pody spuštěné na uzlu jsou ztraceny. Ip adresa podu se proto může kdykoli změnit. Nemůžeme se na to spoléhat, že bude komunikovat s modulem. 

K řešení tohoto problému kubernetes představil koncept [služeb](https://kubernetes.io/docs/concepts/services-networking/service/). Služba Kubernetes je vrstva abstrakce, která definuje logickou skupinu podů a umožňuje externí přenosovou expozici, vyrovnávání zatížení a zjišťování služeb pro tyto pody. 

Když jsme připraveni publikovat naše mikroslužby jako rozhraní API prostřednictvím správy rozhraní API, musíme přemýšlet o tom, jak mapovat naše služby v Kubernetes na rozhraní API ve správě rozhraní API. Neexistují žádná pravidla. Záleží na tom, jak jste na začátku navrhli a rozdělili své obchodní možnosti nebo domény do mikroslužeb. Například pokud pody za službou je zodpovědný za všechny operace na daný prostředek (např. zákazník), služba může být mapována na jedno rozhraní API. Pokud operace na prostředek jsou rozděleny do více mikroslužeb (například GetOrder, PlaceOrder), pak více služeb může být logicky agregovány do jednoho rozhraní API ve správě rozhraní API (viz obr. 1). 

Mapování se také může vyvíjet. Vzhledem k tomu, že správa rozhraní API vytváří fasádu před mikroslužbami, umožňuje nám v průběhu času refaktorovat a správně formátovat naše mikroslužby. 

![Mapování služeb na rozhraní API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Nasazení správy rozhraní API před AKS

Existuje několik možností nasazení správy rozhraní API před clusterem AKS. 

Zatímco cluster AKS je vždy nasazený ve virtuální síti (VNet), instance správy rozhraní API se nemusí nasazovat ve virtuální síti. Když správa rozhraní API není umístěna v rámci virtuální sítě clusteru, cluster AKS musí publikovat veřejné koncové body pro správu rozhraní API pro připojení. V takovém případě je potřeba zabezpečit připojení mezi správou rozhraní API a AKS. Jinými slovy, musíme zajistit, aby ke clusteru bylo možné přistupovat výhradně prostřednictvím správy rozhraní API. Projdeme si možnosti. 

### <a name="option-1-expose-services-publicly"></a>Možnost 1: Vystavit služby veřejně

Služby v clusteru AKS mohou být veřejně vystaveny pomocí [typů služeb](https://docs.microsoft.com/azure/aks/concepts-network) NodePort, LoadBalancer nebo ExternalName. V tomto případě jsou služby přístupné přímo z veřejného internetu. Po nasazení správy rozhraní API před clusterem, musíme zajistit, že veškerý příchozí provoz prochází api management použitím ověřování v mikroslužbách. Správa rozhraní API může například zahrnout přístupový token v každém požadavku provedeném do clusteru. Každá mikroslužba je zodpovědná za ověření tokenu před zpracováním požadavku. 


To může být nejjednodušší možnost nasadit správu rozhraní API před AKS, zejména pokud již máte logiku ověřování implementované ve vašich mikroslužeb. 

![Publikovat služby přímo](./media/api-management-aks/direct.png)

Profesionály:
* Snadná konfigurace na straně správa rozhraní API, protože nemusí být vložena do virtuální sítě clusteru
* Žádná změna na straně AKS, pokud služby jsou již vystaveny veřejně a logika ověřování již existuje v mikroslužbách

Nevýhody:
* Potenciální bezpečnostní riziko z důvodu veřejné viditelnosti koncových bodů služby
* Žádný jeden vstupní bod pro příchozí provoz clusteru
* Komplikuje mikroslužby s duplicitní logiku ověřování

### <a name="option-2-install-an-ingress-controller"></a>Možnost 2: Instalace řadiče příchozího přenosu dat

Ačkoli možnost 1 může být jednodušší, má pozoruhodné nevýhody, jak je uvedeno výše. Pokud instance správy rozhraní API není umístěna ve virtuální síti clusteru, vzájemné ověřování TLS (mTLS) je robustní způsob, jak zajistit, že provoz je zabezpečený a důvěryhodný v obou směrech mezi instancí správy rozhraní API a clusterem AKS. 

Vzájemné ověřování TLS je [nativně podporováno](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) správou rozhraní API a lze je povolit v Kubernetes [instalací řadiče příchozího přenosu dat](https://docs.microsoft.com/azure/aks/ingress-own-tls) (obr. 3). V důsledku toho bude ověřování provedeno v řadiči příchozího přenosu dat, což zjednodušuje mikroslužby. Kromě toho můžete přidat IP adresy správy rozhraní API do seznamu povolených ingress a ujistěte se, že pouze správa rozhraní API má přístup ke clusteru.  

 
![Publikovat prostřednictvím řadiče příchozího přenosu dat](./media/api-management-aks/ingress-controller.png)


Profesionály:
* Snadná konfigurace na straně správa rozhraní API, protože nemusí být vložena do virtuální sítě clusteru a mTLS je nativně podporována
* Centralizuje ochranu příchozího clusteru ve vrstvě řadiče příchozího přenosu dat.
* Snižuje bezpečnostní riziko minimalizací veřejně viditelných koncových bodů clusteru

Nevýhody:
* Zvyšuje složitost konfigurace clusteru díky další práci při instalaci, konfiguraci a údržbě řadiče příchozího přenosu dat a správě certifikátů používaných pro mTLS
* Bezpečnostní riziko z důvodu veřejné viditelnosti koncových bodů řadiče ingress


Při publikování rozhraní API prostřednictvím správy rozhraní API je snadné a běžné zabezpečit přístup k těmto rozhraním API pomocí klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, musí při volání těchto rozhraní API zahrnout platný klíč předplatného v požadavcích HTTP. V opačném případě jsou volání okamžitě odmítnuta bránou pro správu rozhraní API. Nejsou předávány do back-endových služeb.

Chcete-li získat klíč předplatného pro přístup k api, je vyžadováno předplatné. Předplatné je v podstatě pojmenovaný kontejner pro dvojici klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, můžou získat předplatná. A nepotřebují souhlas vydavatelů rozhraní API. Vydavatelé rozhraní API můžete také vytvářet odběry přímo pro spotřebitele rozhraní API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Možnost 3: Nasazení rozhraní APIM uvnitř virtuální sítě clusteru

V některých případech mohou zákazníci s regulačními omezeními nebo přísnými bezpečnostními požadavky najít možnost 1 a 2 jako neživotaschopná řešení z důvodu veřejně exponovaných koncových bodů. V jiných clusteru AKS a aplikace, které spotřebovávají mikroslužeb může být umístěn v rámci stejné virtuální sítě, proto není důvod vystavit clusteru veřejně jako všechny přenosy rozhraní API zůstane v rámci virtuální sítě. V těchto scénářích můžete nasadit službu API Management do virtuální sítě clusteru. [Úroveň API Management Premium](https://aka.ms/apimpricing) podporuje nasazení virtuální sítě. 

Existují dva režimy [nasazení správy rozhraní API do virtuální sítě](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) – externí a interní. 

Pokud se spotřebitelé rozhraní API nenacházejí ve virtuální síti clusteru, měl by se použít externí režim (obr. 4). V tomto režimu se brána pro správu rozhraní API vloží do virtuální sítě clusteru, ale bude přístupná z veřejného internetu prostřednictvím externího systému vyrovnávání zatížení. Pomáhá zcela skrýt clusteru a zároveň umožnit externím klientům využívat mikroslužeb. Kromě toho můžete použít síťové funkce Azure, jako jsou skupiny zabezpečení sítě (NSG) k omezení síťového provozu.

![Režim externí virtuální sítě](./media/api-management-aks/vnet-external.png)

Pokud jsou všichni uživatelé rozhraní API umístěni v rámci virtuální sítě clusteru, lze použít interní režim (obr. 5). V tomto režimu se brána pro správu rozhraní API vloží do virtuální sítě clusteru a bude přístupná jenom z této virtuální sítě prostřednictvím interního vytápěče zatížení. Neexistuje žádný způsob, jak se dostat k bráně api management nebo clusteru AKS z veřejného internetu. 

![Interní režim virtuální sítě](./media/api-management-aks/vnet-internal.png)

 V obou případech není cluster AKS veřejně viditelný. Ve srovnání s možností 2 nemusí být řadič příchozího přenosu dat nutný. V závislosti na scénáři a konfiguraci může být ověřování stále vyžadováno mezi správou rozhraní API a mikroslužbami. Například pokud je přijata síť služby, vždy vyžaduje vzájemné ověřování TLS. 

Profesionály:
* Nejbezpečnější možnost, protože cluster AKS nemá žádný veřejný koncový bod
* Zjednodušuje konfiguraci clusteru, protože nemá žádný veřejný koncový bod.
* Možnost skrýt správu rozhraní API i AKS uvnitř virtuální sítě pomocí interního režimu
* Možnost řízení síťového provozu pomocí síťových funkcí Azure, jako jsou skupiny zabezpečení sítě (NSG)

Nevýhody:
* Zvyšuje složitost nasazování a konfigurace správy rozhraní API pro práci uvnitř virtuální sítě

## <a name="next-steps"></a>Další kroky

* Další informace o [síťových konceptech pro aplikace v AKS](https://docs.microsoft.com/azure/aks/concepts-network)
* Další informace o [používání správy rozhraní API s virtuálními sítěmi](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





