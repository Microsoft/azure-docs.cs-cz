---
title: Použití Azure API Management s mikroslužbami nasazenými ve službě Azure Kubernetes | Microsoft Docs
description: Tento článek popisuje možnosti nasazení API Management s využitím AKS.
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
ms.openlocfilehash: 36dfc8c906c52c6822e583db3a08c891306f7e78
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047928"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Použití Azure API Management s mikroslužbami nasazenými ve službě Azure Kubernetes

Mikroslužby jsou ideální pro vytváření rozhraní API. Pomocí [služby Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) můžete rychle nasadit a provozovat [architekturu založenou na mikroslužbách](/azure/architecture/guide/architecture-styles/microservices) v cloudu. Potom můžete využít [Azure API Management](https://aka.ms/apimrocks) (API Management) k publikování mikroslužeb jako rozhraní API pro interní a externí spotřebu. Tento článek popisuje možnosti nasazení API Management pomocí AKS. Předpokládá základní znalosti o Kubernetes, API Management a sítích Azure. 

## <a name="background"></a>Pozadí

Při publikování mikroslužeb jako rozhraní API pro spotřebu může být obtížné spravovat komunikaci mezi mikroslužbami a klienty, kteří je používají. Existuje velké množství různých otázek, jako je ověřování, autorizace, omezování, ukládání do mezipaměti, transformace a monitorování. Tyto aspekty jsou platné bez ohledu na to, jestli jsou mikroslužby vystavené interním nebo externím klientům. 

Tento model [brány rozhraní API](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) řeší tyto aspekty. Brána API slouží jako přední dveře ke mikroslužbám, rozděluje klienty od vašich mikroslužeb, přidává další vrstvu zabezpečení a snižuje složitost vašich mikroslužeb tím, že odstraňuje zatížení, které zpracovává obavy z hlediska vzájemného průřezu. 

[Azure API Management](https://aka.ms/apimrocks) je řešení klíč, které slouží k řešení vašich potřeb vašich bran API. Můžete rychle vytvořit konzistentní a moderní bránu pro vaše mikroslužby a publikovat je jako rozhraní API. Jako řešení pro správu rozhraní API pro celou životní cyklus poskytuje taky další možnosti, jako je Samoobslužný portál pro vývojáře pro zjišťování rozhraní API, správu životního cyklu rozhraní API a analýzy rozhraní API.

Při použití společně AKS a API Management poskytují platformu pro nasazení, publikování, zabezpečení, monitorování a správu rozhraní API založených na mikroslužbách. V tomto článku provedeme několik možností nasazení AKS ve spojení s API Management. 

## <a name="kubernetes-services-and-apis"></a>Služby a rozhraní API pro Kubernetes

V clusteru Kubernetes se kontejnery nasazují v [luskech](https://kubernetes.io/docs/concepts/workloads/pods/pod/), což jsou dočasné a mají životní cyklus. Když pracovní uzel zemře, dojde ke ztrátě lusků spuštěných v uzlu. Proto se IP adresa pod tím může kdykoli změnit. Nemůžeme na něj spoléhat, aby komunikoval s pod. 

Pro vyřešení tohoto problému Kubernetes představil koncept [služeb](https://kubernetes.io/docs/concepts/services-networking/service/). Služba Kubernetes je abstraktní vrstva, která definuje logickou skupinu lusků a umožňuje externí expozici provozu, Vyrovnávání zatížení a zjišťování služeb pro tyto lusky. 

Až budeme připraveni publikovat mikroslužby jako rozhraní API prostřednictvím API Management, musíme si představit, jak namapovat naše služby v Kubernetes na rozhraní API v API Management. Neexistují žádná pravidla pro nastavení. Záleží na tom, jak jste navrhli a rozdělili své obchodní možnosti nebo domény na mikroslužby na začátku. Pokud jsou například lusky za službu zodpovědné za všechny operace na daném prostředku (např. zákazník), služba může být namapována na jedno rozhraní API. Pokud jsou operace s prostředkem rozdělené do několika mikroslužeb (např. GetOrder, PlaceOrder), může být víc služeb logicky agregované do jednoho jediného rozhraní API ve službě API Management (viz obrázek. 1). 

Mapování je také možné vyvíjet. Vzhledem k tomu, že API Management vytvoří fasádu před mikroslužby, umožňuje nám Refaktorovat a napravovat velikost našich mikroslužeb v průběhu času. 

![Mapování služeb na rozhraní API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Nasadit API Management před AKS

Existuje několik možností nasazení API Management před clusterem AKS. 

I když je cluster AKS vždy nasazený ve virtuální síti (VNet), není nutné nasadit instanci API Management ve virtuální síti. Pokud se API Management nenachází v rámci virtuální sítě clusteru, musí cluster AKS publikovat veřejné koncové body, ke kterým se připojí API Management. V takovém případě je potřeba zabezpečit připojení mezi API Management a AKS. Jinými slovy musíme zajistit, aby byl cluster k dispozici pouze výhradně prostřednictvím API Management. Pojďme si projít možnosti. 

### <a name="option-1-expose-services-publicly"></a>Možnost 1: veřejně zveřejňuje služby

Služby v clusteru AKS mohou být veřejně vystaveny pomocí [typů služeb](../aks/concepts-network.md) NodePort, Vyrovnávání zatížení nebo extern. V takovém případě jsou služby přístupné přímo z veřejného Internetu. Po nasazení API Management před clusterem je potřeba zajistit, aby veškerý příchozí provoz procházel prostřednictvím API Management použitím ověřování v mikroslužbách. API Management například může do každého požadavku v clusteru zahrnovat přístupový token. Každá mikroslužba zodpovídá za ověření tokenu před zpracováním žádosti. 


To může být nejjednodušší způsob, jak nasadit API Management před AKS, zejména pokud už máte logiku ověřování implementovanou v mikroslužbách. 

![Publikování služeb přímo](./media/api-management-aks/direct.png)

IT
* Snadná konfigurace na straně API Management, protože nemusí být vložena do virtuální sítě clusteru
* Žádná změna na straně AKS, pokud už jsou služby veřejně vystavené, a logika ověřování už v mikroslužbách existuje.

Cons
* Potenciální bezpečnostní riziko kvůli veřejné viditelnosti koncových bodů služby
* Žádný jeden vstupní bod pro příchozí provoz clusteru
* Ztěžuje mikroslužby s duplicitní logikou ověřování.

### <a name="option-2-install-an-ingress-controller"></a>Možnost 2: instalace kontroleru příchozího přenosu dat

I když možnost 1 může být jednodušší, má významné nevýhody, jak je uvedeno výše. Pokud instance služby API Management není umístěná ve virtuální síti clusteru, vzájemné ověřování TLS (mTLS) je robustní způsob, jak zajistit, aby byl provoz zabezpečený a důvěryhodný v obou směrech mezi instancí API Management a clusterem AKS. 

Vzájemné ověřování TLS je [nativně podporované](./api-management-howto-mutual-certificates.md) API Management a je možné je povolit v Kubernetes [instalací kontroleru příchozího](../aks/ingress-own-tls.md) přenosu dat (obrázek. 3). V důsledku toho se ověřování provede v řadiči příchozího přenosu dat, což zjednodušuje mikroslužby. Příchozí přenos dat API Management taky můžete přidat do seznamu povolených IP adres, aby se zajistilo, že přístup ke clusteru bude mít jenom API Management.  

 
![Publikování prostřednictvím kontroleru příchozího přenosu dat](./media/api-management-aks/ingress-controller.png)


IT
* Snadná konfigurace na API Management straně, protože nemusí být vložena do virtuální sítě clusteru a služba mTLS je nativně podporovaná
* Centralizace ochrany pro příchozí provoz clusteru ve vrstvě řadiče příchozího přenosu dat
* Snižuje riziko zabezpečení minimalizací veřejně viditelných koncových bodů clusteru.

Cons
* Zvyšuje složitost konfigurace clusteru kvůli dodatečné práci při instalaci, konfiguraci a údržbě kontroleru příchozího přenosu dat a správě certifikátů používaných pro mTLS.
* Bezpečnostní riziko kvůli veřejné viditelnosti koncových bodů řadiče pro příchozí přenosy


Když publikujete rozhraní API prostřednictvím API Management, je snadné a běžné zabezpečení přístupu k těmto rozhraním API pomocí klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, musí při volání do těchto rozhraní API zahrnovat platný klíč předplatného v požadavcích HTTP. V opačném případě jsou volání okamžitě odmítnuta bránou API Management. Neodesílají se do back-endové služby.

Pokud chcete získat klíč předplatného pro přístup k rozhraním API, vyžaduje se předplatné. Předplatné je v podstatě pojmenovaný kontejner pro pár klíčů předplatného. Vývojáři, kteří potřebují využívat publikovaná rozhraní API, můžou získat odběry. A nepotřebují schválení od vydavatelů rozhraní API. Vydavatelé rozhraní API můžou také vytvářet odběry přímo pro uživatele rozhraní API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Možnost 3: nasazení APIM do virtuální sítě clusteru

V některých případech mohou zákazníci, kteří mají regulativní omezení nebo přísné požadavky na zabezpečení, najít možnost 1 a 2 neživotaschopná řešení kvůli veřejně přístupným koncovým bodům. V ostatních případech se cluster AKS a aplikace, které využívají mikroslužby, můžou nacházet v rámci stejné virtuální sítě, proto není důvod zveřejnit cluster veřejně, protože veškerý provoz rozhraní API zůstane v rámci virtuální sítě. V těchto scénářích můžete nasadit API Management do virtuální sítě clusteru. Nasazení virtuální sítě podporuje [API Management pro vývojáře a úrovně Premium](https://aka.ms/apimpricing) . 

Existují dva způsoby [nasazení API Management do virtuální](./api-management-using-with-vnet.md) sítě – externí a interní. 

Pokud se uživatelé rozhraní API nenacházejí ve virtuální síti clusteru, je třeba použít externí režim (obrázek. 4). V tomto režimu je API Management brána vložená do virtuální sítě clusteru, ale přístupná z veřejného Internetu prostřednictvím externího nástroje pro vyrovnávání zatížení. Pomáhá plně skrývat cluster, a přitom umožňuje externím klientům využívat mikroslužby. Kromě toho můžete k omezení síťového provozu použít možnosti sítě Azure, jako jsou třeba skupiny zabezpečení sítě (NSG).

![Režim externí virtuální sítě](./media/api-management-aks/vnet-external.png)

Pokud se všichni příjemci rozhraní API nacházejí v rámci virtuální sítě clusteru, můžete použít interní režim (obrázek. 5). V tomto režimu se API Management brána vloží do virtuální sítě clusteru a v rámci této virtuální sítě je dostupná jenom prostřednictvím interního nástroje pro vyrovnávání zatížení. Neexistuje žádný způsob, jak kontaktovat bránu API Management ani cluster AKS z veřejného Internetu. 

![Režim interní virtuální sítě](./media/api-management-aks/vnet-internal.png)

 V obou případech není cluster AKS veřejně viditelný. V porovnání s možností 2 nemusí být adaptér příchozího přenosu nutný. V závislosti na vašem scénáři a konfiguraci může být ověřování stále vyžadováno mezi API Management a vašimi mikroslužbami. Například pokud je přijata síť služby, vždy vyžaduje vzájemné ověřování TLS. 

IT
* Možnost nejbezpečnější, protože cluster AKS nemá žádný veřejný koncový bod
* Zjednodušuje konfiguraci clusteru, protože nemá žádný veřejný koncový bod.
* Možnost skrýt API Management i AKS uvnitř virtuální sítě pomocí interního režimu
* Možnost řízení síťového provozu pomocí možností sítě Azure, jako jsou skupiny zabezpečení sítě (NSG)

Cons
* Zvyšuje složitost nasazení a konfigurace API Management pro práci v rámci virtuální sítě.

## <a name="next-steps"></a>Další kroky

* Další informace o [principech sítě pro aplikace v AKS](../aks/concepts-network.md)
* Další informace o [použití API Management s virtuálními sítěmi](./api-management-using-with-vnet.md)
