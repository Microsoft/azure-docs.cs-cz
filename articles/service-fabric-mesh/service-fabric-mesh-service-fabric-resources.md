---
title: Úvod k modelu prostředků prostředků infrastruktury služeb Azure
description: Další informace o modelu prostředků service fabric, zjednodušený přístup k definování aplikace Service Fabric Mesh.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259133"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Úvod do modelu prostředků prostředků prostředků infrastruktury služeb

Model prostředků Service Fabric popisuje jednoduchý přístup k definování prostředků, které tvoří aplikace Service Fabric Mesh. Jednotlivé prostředky lze nasadit do libovolného prostředí Service Fabric.  Model prostředků Service Fabric je taky kompatibilní s modelem Azure Resource Manager. V tomto modelu jsou aktuálně podporovány následující typy prostředků:

- Aplikace a služby
- Sítě
- Brány
- Tajemství a tajemství / hodnoty
- Svazky

Každý prostředek je deklarativně popsán v souboru prostředků, což je jednoduchý dokument YAML nebo JSON, který popisuje aplikaci sítě a je zřízen platformou Service Fabric.

## <a name="applications-and-services"></a>Aplikace a služby

Prostředek aplikace je jednotka nasazení, správy verzí a životnosti aplikace Mesh. Skládá se z jednoho nebo více prostředků služby, které představují mikroslužbu. Každý prostředek služby, podle pořadí se skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu.

![Aplikace a služby][Image1]

Prostředek služby deklaruje následující:

- Název kontejneru, verze a registr
- Prostředky procesoru a paměti potřebné pro každý kontejner
- Koncové body sítě
- Odkazy na jiné prostředky, jako jsou sítě, svazky a tajné kódy 

Všechny balíčky kódu definované jako součást prostředku služby jsou nasazeny a aktivovány společně jako skupina. Prostředek služby také popisuje, kolik instancí služby spustit a také odkazuje na jiné prostředky (například síťový prostředek) závisí na.

Pokud síťové aplikace se skládá z více než jednu službu, není zaručeno, že spustit společně na stejném uzlu. Také během upgradu aplikace selhání upgradu jedné služby bude mít za následek všechny služby jsou vráceny zpět do předchozí verze.

Jak již bylo uvedeno dříve, životní cyklus každé instance aplikace lze spravovat nezávisle. Například jednu instanci aplikace lze upgradovat nezávisle na ostatních instancích aplikace. Obvykle zachovat počet služeb v aplikaci poměrně malé, jako více služeb, které vložíte do aplikace, obtížnější je správa každé služby nezávisle.

## <a name="networks"></a>Sítě

Síťový prostředek je jednotlivě nasaditelný prostředek, nezávislý na prostředku aplikace nebo služby, který může odkazovat na to jako jejich závislost. Používá se k vytvoření sítě pro vaše aplikace. Součástí stejné sítě může být více služeb z různých aplikací.  Další informace naleznete [v informacích](service-fabric-mesh-networks-and-gateways.md)o vytváření sítí v aplikacích Service Fabric Mesh .

> [!NOTE]
> Aktuální náhled podporuje pouze mapování jeden ku jedné mezi aplikacemi a sítěmi.

![Síť a brána][Image2]

## <a name="gateways"></a>Brány
Prostředek brány spojuje dvě sítě a směruje provoz.  Brána umožňuje vašim službám komunikovat s externími klienty a poskytuje příchozí přenos dat do vašich služeb.  Bránu lze také použít k připojení aplikace Mesh s vlastní existující virtuální sítí. Další informace naleznete [v informacích](service-fabric-mesh-networks-and-gateways.md)o vytváření sítí v aplikacích Service Fabric Mesh .

![Síť a brána][Image2]

## <a name="secrets"></a>Tajné kódy

Prostředky tajných kódů jsou nasaditelné nezávisle na prostředku aplikace nebo služby, které se na něj mohou odkazovat jako na jejich závislost. Používá se k bezpečnému doručování tajných kódů do vašich aplikací. Více služeb z různých aplikací může odkazovat na hodnoty stejného tajného klíče.

## <a name="volumes"></a>Svazky

Kontejnery často zpřístupňují dočasné disky. Dočasné disky jsou však dočasné, takže získáte nový dočasný disk a ztratíte informace při havárii kontejneru. Je také obtížné sdílet informace o dočasných discích s jinými kontejnery. Svazky jsou adresáře, které se připevní uvnitř instancí kontejneru, které můžete použít k zachování stavu. Svazky umožňují ukládání souborů pro obecné účely a umožňují čtení a zápis souborů pomocí běžných souborů V/O disku. Prostředek svazku je deklarativní způsob, jak popsat, jak je adresář připojen a záložní úložiště pro něj (buď Svazek souborů Azure nebo Service Fabric spolehlivý svazek).  Další informace naleznete v [části Stav ukládání](service-fabric-mesh-storing-state.md#volumes).

![Svazky][Image3]

## <a name="programming-models"></a>Programovací modely
Prostředek služby vyžaduje pouze spuštění image kontejneru, na kterou se odkazuje v balíčku kódu přidruženém k prostředku. Můžete spustit libovolný kód, napsaný v libovolném jazyce, pomocí libovolné architektury uvnitř kontejneru bez nutnosti znát nebo používat rozhraní API specifické pro síť service fabric. 

Kód aplikace zůstane přenosný i mimo síť Service Fabric mesh a nasazení aplikací zůstanou konzistentní bez ohledu na jazyk nebo architekturu používanou k implementaci služeb. Bez ohledu na to, zda je vaše aplikace ASP.NET jádra, go nebo jen sadu procesů a skriptů, service fabric mesh resource nasazení modelu zůstává stejný. 

## <a name="packaging-and-deployment"></a>Balení a nasazení

Aplikace Service Fabric Mesh založené na modelu prostředků jsou zabaleny jako kontejnery Dockeru.  Service Fabric Mesh je sdílené prostředí s více tenanty a kontejnery poskytují vysokou úroveň izolace.  Tyto aplikace jsou popsány pomocí formátu JSON nebo formátu YAML (který je pak převeden na JSON). Při nasazování síťové aplikace do sítě Azure Service Fabric mesh je JSON použitý k popisu aplikace šablona Azure Resource Manager. Prostředky jsou mapovány na prostředky Azure.  Při nasazování síťové aplikace do clusteru Service Fabric (samostatný nebo hostovaný v Azure) json slouží k popisu aplikace je formát podobný šabloně Azure Resource Manager.  Po nasazení lze síťové aplikace spravovat prostřednictvím rozhraní HTTP nebo rozhraní příkazového řádku Azure. 


## <a name="next-steps"></a>Další kroky 
Další informace o síti Service Fabric Mesh najdete v přehledu:
- [Service Fabric Mesh – přehled](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
