---
title: Úvod do modelu prostředků služby Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o modelu prostředků služby Service Fabric, jednodušší přístup k definování aplikací Service Fabric mřížky.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 702e1ef9c8593c2106be256e6fd7de602bf41aa7
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019992"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Úvod do modelu prostředků služby Service Fabric

Model Service Fabric prostředků popisuje jednoduchý přístup k definování prostředků, které tvoří aplikace Service Fabric mřížky. Jednotlivé prostředky je možné nasadit do libovolného prostředí Service Fabric.  Model prostředků Service Fabric je také kompatibilní s modelem Azure Resource Manageru. V tomto modelu jsou aktuálně podporovány následující typy prostředků:

- Aplikace a služby
- Sítě
- Brány
- Tajné klíče a tajné klíče/hodnoty
- Svazky

Každý prostředek je popsána deklarativně v souboru prostředků, což je jednoduchá YAML nebo dokument JSON, který popisuje použití mřížky a je zajištěno platformou Service Fabric.

## <a name="applications-and-services"></a>Aplikace a služby

Prostředek služby Application je jednotka nasazení, správu verzí a životního cyklu aplikace sítě. Se skládá z jednoho nebo více, prostředky služby, které představují mikroslužby. Každý prostředek služby, pak se skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné pro spuštění image kontejneru, který je přidružený k balíčku kódu.

![Aplikace a služby][Image1]

Prostředek služby deklaruje následující:

- Název kontejneru, verzi a registru
- Prostředky procesoru a paměti vyžadované pro každý kontejner
- Koncové body sítě
- Odkazy na další prostředky, jako jsou sítě, svazky a tajných kódů 

Všechny balíčky kódu definované jako součást služby prostředků se nasazují a aktivují společně jako skupina. Prostředek služby také popisuje, jak velký počet instancí služby ke spuštění a také odkazuje na další prostředky (například síťový prostředek) závisí na.

Pokud aplikace sítě se skládá z více než jedna služba, není zaručena pro spuštění společně na stejném uzlu. Navíc během upgradu aplikace selhání upgradu jedné služby způsobí všechny služby vracené zpět k předchozí verzi.

Jak alluded dříve, můžete spravovat nezávisle životního cyklu jednotlivých instancí aplikací. Například jedna instance aplikace je možné upgradovat nezávisle z jiné instance aplikace. Obvykle udržovali počet služeb v aplikaci docela malá jako další služby, které vložíte do aplikace, tím složitější bude spravovat jednotlivé služby nezávisle na sobě.

## <a name="networks"></a>Sítě

Síťový prostředek je jednotlivě nasaditelnému prostředku, nezávisle na prostředek aplikace nebo služby, který může označujeme jako jejich závislosti. Používá se k vytvoření sítě pro vaše aplikace. Více služeb z různých aplikací může být součástí stejné sítě.  Další informace, přečtěte si informace o [síťové aplikace Service Fabric mřížky](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Aktuální verze preview podporuje pouze mapování 1: 1 mezi aplikací a sítě

![Sítě a brány][Image2]

## <a name="gateways"></a>Brány
Prostředek brány připojení dvě sítě a směruje provoz.  Brána umožňuje vašich služeb ke komunikaci s externími klienty a poskytuje příchozí přenos dat do služby.  Brána lze také připojit síť aplikace s vlastním, existující virtuální sítě. Další informace, přečtěte si informace o [síťové aplikace Service Fabric mřížky](service-fabric-mesh-networks-and-gateways.md).

![Sítě a brány][Image2]

## <a name="secrets"></a>Tajné kódy

Prostředky tajné kódy jsou nezávislé na nasaditelný aplikace nebo služby prostředků, na který může odkazovat jako jejich závislosti. Používá se pro zabezpečené doručování tajných klíčů aplikací. Hodnoty stejný tajný klíč můžete odkazovat na více služeb z různých aplikací.

## <a name="volumes"></a>Svazky

Kontejnery často zpřístupnit dočasné disky. Dočasné disky jsou dočasné, ale tak získat nový dočasný disk a ke ztrátě informací, pokud dojde k chybě kontejneru. Je taky obtížné sdílet informace o dočasné disky s jiných kontejnerů. Svazky jsou adresáře, které se připojit k uvnitř instancí kontejneru, které můžete použít k uložení stavu. Svazky poskytují úložiště pro obecné účely souborů a umožňují čtení a zápis souborů pomocí rozhraní API normální disku vstupně-výstupní operace souboru. Prostředku svazku je deklarativní způsob, jak popisují, jak je připojený adresář a záložní úložiště pro něj (svazku soubory Azure nebo Service Fabric Reliable svazku).  Další informace najdete v článku [ukládání stavu](service-fabric-mesh-storing-state.md#volumes).

![Svazky][Image3]

## <a name="programming-models"></a>Programovací modely
Prostředek služby vyžaduje jenom image kontejneru pro spuštění, který se odkazuje v kódu balíčky přidružené k prostředku. Můžete spustit libovolný kód napsané v jakémkoli jazyce pomocí libovolné architektury uvnitř kontejneru bez nutnosti znát nebo pomocí sítě pro Service Fabric určitých rozhraní API. 

Kód aplikace zůstane přenosné i mimo službu prostředků infrastruktury sítě a nasazení vaší aplikace zůstaly konzistentní bez ohledu na jazyk nebo rozhraní používaný k implementaci služby. Zda je vaše aplikace ASP.NET Core, Go nebo jenom sadu procesů a skriptů, model nasazení prostředků sítě Service Fabric se nezmění. 

## <a name="packaging-and-deployment"></a>Balení a nasazení

Service Fabric mřížky aplikací založených na modelu prostředků, který jsou zabalené jako kontejnery Dockeru.  Kontejnery umožňují vysokou úroveň izolace sítě pro Service Fabric je sdílené víceklientských prostředí.  Tyto aplikace jsou popsány pomocí formátu JSON nebo YAML formátu (které je pak převedeno do formátu JSON). Při nasazování aplikace sítě k Azure Service Fabric sítě, je ve formátu JSON používá k popisu aplikace šablony Azure Resource Manageru. Prostředky se mapují k prostředkům Azure.  Při nasazování sítě aplikace do clusteru Service Fabric (samostatné nebo hostovaný v Azure), ve formátu JSON používá k popisu aplikace je ve formátu podobném šablony Azure Resource Manageru.  Po nasazení aplikace sítě je možné spravovat prostřednictvím HTTP rozhraní nebo rozhraní příkazového řádku Azure. 


## <a name="next-steps"></a>Další postup 
Další informace o Service Fabric sítě, naleznete v přehledu:
- [Přehled Service Fabric mřížky](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
