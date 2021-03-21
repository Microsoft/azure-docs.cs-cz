---
title: Seznámení s modelem prostředků Azure Service Fabric
description: Přečtěte si o modelu prostředků Service Fabric, zjednodušeném přístupu k definování Service Fabricch aplikací pro mřížku.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626905"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Seznámení s modelem Service Fabric prostředků

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Model prostředků Service Fabric popisuje jednoduchý přístup k definování prostředků, které tvoří Service Fabric mřížkovou aplikaci. Jednotlivé prostředky lze nasadit do libovolného Service Fabricho prostředí.  Model prostředků Service Fabric je také kompatibilní s modelem Azure Resource Manager. V tomto modelu se aktuálně podporují následující typy prostředků:

- Aplikace a služby
- Sítě
- Brány
- Tajné kódy a tajné klíče/hodnoty
- Svazky

Jednotlivé prostředky jsou popsány deklarativně v souboru prostředků, což je jednoduchý dokument YAML nebo JSON, který popisuje aplikaci sítě a je zajištěna Service Fabric platformou.

## <a name="applications-and-services"></a>Aplikace a služby

Prostředek aplikace je jednotka nasazení, správy verzí a životnosti aplikace sítě. Skládá se z jednoho nebo více prostředků služby, které reprezentují mikroslužby. Každý prostředek služby se pak skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu.

![Aplikace a služby][Image1]

Prostředek služby deklaruje následující:

- Název, verze a registr kontejneru
- Prostředky procesoru a paměti vyžadované pro každý kontejner
- Síťové koncové body
- Odkazy na jiné prostředky, jako jsou sítě, svazky a tajné klíče 

Všechny balíčky kódu definované jako součást prostředku služby se nasazují a aktivují dohromady jako skupina. Prostředek služby také popisuje, kolik instancí služby se má spustit, a také odkazuje na další prostředky (například na síťové prostředky), na kterých závisí.

Pokud se aplikace sítě skládá z více než jedné služby, není zaručena, aby běžela společně na stejném uzlu. Během upgradu aplikace dojde také k selhání upgradu jedné služby na všechny služby vracené zpět do jejich předchozí verze.

Jako zmiňovali dříve se dá životní cyklus jednotlivých instancí aplikace spravovat nezávisle. Například jedna instance aplikace může být upgradována nezávisle na ostatních instancích aplikace. Obvykle zachováte počet služeb v aplikaci poměrně malý, jako další služby, které do aplikace zadáte, tím obtížnější je spravovat každou službu nezávisle.

## <a name="networks"></a>Sítě

Síťový prostředek je samostatně nasaditelné prostředky nezávisle na prostředku aplikace nebo služby, který se na něj může odkazovat jako na jejich závislosti. Slouží k vytvoření sítě pro vaše aplikace. Několik služeb z různých aplikací může být součástí stejné sítě.  Další informace najdete v článku o [sítích v Service Fabricch aplikacích sítě](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Aktuální verze Preview podporuje jenom jedno mapování mezi aplikacemi a sítěmi.

![Síť a brána][Image2]

## <a name="gateways"></a>Brány
Prostředek brány spojuje dvě sítě a směruje provoz.  Brána umožňuje vašim službám komunikovat s externími klienty a poskytuje příchozí a příchozí přenosy vašich služeb.  Bránu můžete také použít k propojení vaší aplikace sítě s vlastní, existující virtuální sítí. Další informace najdete v článku o [sítích v Service Fabricch aplikacích sítě](service-fabric-mesh-networks-and-gateways.md).

![Síť a brána][Image2]

## <a name="secrets"></a>Tajné kódy

Prostředky tajných kódů lze nasadit nezávisle na prostředku aplikace nebo služby, který se na něj může odkazovat jako na jejich závislosti. Slouží k bezpečnému doručování tajných kódů do vašich aplikací. Různé služby z různých aplikací můžou odkazovat na hodnoty stejného tajného klíče.

## <a name="volumes"></a>Svazky

Kontejnery často zpřístupňují dočasné disky. Dočasné disky jsou ale dočasné, takže získáte nový dočasný disk a ztratíte informace, když dojde k selhání kontejneru. Je také obtížné sdílet informace o dočasných discích s jinými kontejnery. Svazky jsou adresáře, které se připevní do instancí kontejnerů, které můžete použít k trvalému stavu. Svazky poskytují úložiště souborů pro obecné účely a umožňují čtení a zápis souborů pomocí normálních rozhraní API v/v souborů na disku. Prostředek Volume je deklarativní způsob, jak popsat, jak je adresář připojený, a jeho záložní úložiště (buď svazek se soubory Azure nebo Service Fabric spolehlivý svazek).  Další informace najdete v tématu věnovaném [ukládání stavu](service-fabric-mesh-storing-state.md#volumes).

![Diagram znázorňuje službu, která se přesměruje na diskový svazek, který se bude nacházet mezi Service Fabric spolehlivým svazkem, pak s replikovaným místním diskem a svazkem souborů Azure a pak do síťového úložiště.][Image3]

## <a name="programming-models"></a>Programovací modely
Prostředek služby vyžaduje, aby se spustila image kontejneru, na kterou se odkazuje v balíčcích kódu přidružených k danému prostředku. Můžete spustit libovolný kód napsaný v jakémkoli jazyce pomocí libovolného rozhraní uvnitř kontejneru, aniž byste museli znát nebo používat Service Fabric rozhraní API specifická pro danou síť. 

Váš kód aplikace zůstane přenosný i mimo Service Fabricou a vaše nasazení aplikace zůstane konzistentní bez ohledu na jazyk nebo rozhraní používané k implementaci služeb. Bez ohledu na to, jestli je vaše aplikace ASP.NET Core, jít nebo jenom sada procesů a skriptů, zůstane model nasazení prostředků Service Fabric sítě stejný. 

## <a name="packaging-and-deployment"></a>Balení a nasazení

Aplikace Service Fabric sítě založené na modelu prostředků se zabalí jako kontejnery Docker.  Service Fabricová síť je sdílené, víceklientské prostředí a kontejnery poskytují vysokou úroveň izolace.  Tyto aplikace jsou popsány pomocí formátu JSON nebo formátu YAML (který se pak převede na JSON). Při nasazování aplikace sítě do sítě Azure Service Fabric síti je JSON, který se používá k popisu aplikace, šablonou Azure Resource Manager. Prostředky se mapují na prostředky Azure.  Při nasazování aplikace sítě do clusteru Service Fabric (samostatné nebo hostované v Azure) se formát JSON, který popisuje aplikaci, podobá Azure Resource Manager šabloně.  Po nasazení můžete aplikace sítě spravovat prostřednictvím rozhraní HTTP nebo Azure CLI. 


## <a name="next-steps"></a>Další kroky 
Další informace o Service Fabric sítě najdete v článku Přehled:
- [Přehled Service Fabric sítě](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
