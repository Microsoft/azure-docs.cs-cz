---
title: Přehled služby Service Fabric Mesh v Azure | Microsoft Docs
description: Přečtěte si podrobnosti o službě Azure Service Fabric Mesh. Pomocí Service Fabric Mesh můžete nasadit a škálovat aplikace bez starostí o zajištění potřebné infrastruktury.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d315ca0702b1d76e0f990d4d33a3807a1dc57935
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428189"
---
# <a name="what-is-service-fabric-mesh"></a>Co je Service Fabric Mesh?

Toto video obsahuje stručný přehled služby Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Aplikace hostované ve službě Service Fabric Mesh se spouštějí a škálují, aniž by bylo třeba starat se o jejich infrastrukturu.  Service Fabric Mesh tvoří clustery složené z tisíců počítačů.  Veškeré operace správy clusteru jsou před vývojáři skryté. Nahrajte svůj kód a zadejte prostředky, které potřebujete, požadavky na dostupnost a limity prostředků.  Service Fabric Mesh automaticky přidělí infrastrukturu a postará se o případná selhání infrastruktury, aby vaše aplikace měla trvale vysokou dostupnost. Na vás zbude jen starost o stav a rychlost reakcí aplikace, nikoli o infrastrukturu.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Tento článek obsahuje přehled klíčových výhod služby Service Fabric Mesh.

## <a name="great-developer-experience"></a>Skvělé prostředí pro vývojáře

Service Fabric Mesh podporuje každý programovací jazyk nebo architekturu, které lze spustit v kontejneru. Podpora nástrojů Visual Studio Code a Visual Studio 2019 poskytuje výkonné úpravy a ladění pro aplikace .NET a .NET Core. 

Služba Service Fabric Mesh umožňuje:

- Migraci existujících aplikací do kontejnerů metodou „lift and shift“ a tím i možnost modernizace a spouštění aplikací ve velkém měřítku.
- Sestavování a nasazování nových aplikací mikroslužeb ve velkém měřítku v Azure.  Integraci s jinými službami Azure nebo existujícími aplikacemi spouštěnými v kontejnerech. Každá mikroslužba je součástí aplikace zabezpečené, prostředí s izolací sítě. Mikroslužeb má definovaný pro procesor jader, paměti, místa na disku a další zásady správného řízení prostředků.
- Integraci a rozšiřování stávajících aplikací, aniž by bylo nutné je upravovat. Zapojení existujících aplikací do nové aplikace pomocí vaší vlastní virtuální sítě.  
- Modernizaci existujících aplikací Cloud Services migrací na službu Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Jednoduchý provozní životní cyklus

Snadná správa spuštěné aplikace, monitorování aplikací a ladění v produkčních prostředích. Tato správa zahrnuje upgrady aplikací a správy verzí. Tyto aplikace se mohou skládat z jedné mikroslužby, nebo z několika mikroslužeb izolovaných ve svých vlastních sítích. Aplikace běží efektivně díky rychlému nasazení, umístění a převzetí služeb při selhání.

Služba Service Fabric Mesh umožňuje:

- Nasazení a správu aplikací bez nutnosti explicitně zřizovat a spravovat infrastrukturu.  Service Fabric Mesh zřídí, aktualizuje, opravuje a udržuje potřebnou infrastrukturu za vás.
- Nastavení nepřetržité integrace pomocí integrovaných nástrojů pro snadné balíčkování a nasazování aplikací.
- Využijte všechny funkce prostředky Azure Resource Manageru. Tyto funkce příklady záznam pro audit a [řízení přístupu na základě rolí](/azure/role-based-access-control/overview)). Všechny prostředky, které nasadíte do služby Service Fabric sítě v Azure jsou prostředky Azure Resource Manageru. Sem patří aplikace, služby, tajných kódů a tak dále.
- Nasazení a správu prostředků pomocí [webu Azure Portal](https://portal.azure.com), šablon Správce prostředků nebo knihoven Azure CLI/PowerShell.
- Nastavení sledování provozu a upozorňování pomocí služby [Application Insights](/azure/application-insights/) (nebo jiného nástroje podle vašeho výběru) a zachycování provozních a diagnostických dat z platformy.
- Přístup k informacím o diagnostice získaným z aplikačního modelu pomocí [Application Insights](/azure/application-insights/) nebo jiného nástroje podle vašeho výběru.
- Optimalizujte využití prostředků tak, že zadáte pravidel automatického škálování pro služby v definici aplikace.

## <a name="mission-critical-platform-capabilities"></a>Možnosti platformy pro kritické aplikace

Service Fabric Mesh vytvoří kolekci clusterů, které překračují hranice [zóny dostupnosti Azure](/azure/availability-zones/az-overview) nebo místní geopolitické hranice. Aplikace se sadou záměrů, jako je například škálování, požadavky na hardware, požadavky na životnost a zásady zabezpečení sítě pro Service Fabric popisuje.  Po nasazení aplikace najde Service Fabric Mesh optimální místo, kde ji spustit.

Služba Service Fabric Mesh umožňuje:

- Využít vysokou dostupnost, škálování nahoru i dolů, vyhledatelnost, orchestraci, směrování zpráv, spolehlivé zasílání zpráv, upgrady bez výpadků, správu zabezpečení a tajných kódů, zotavení po havárii, správa stavů, správu konfigurací a distribuované transakce.
- Vybírat při vytváření aplikace z několika různých aplikačních modelů.
- Využít možností platformy poskytovaných koncovými body REST prostřednictvím vazeb specifických pro různé jazyky generovaných pomocí Swaggeru.
- Nasazení aplikací napříč [zónami dostupnosti](/azure/availability-zones/az-overview) a různými oblastmi pro geografickou spolehlivost.
- Využít všechny možnosti zabezpečení a dodržování předpisů, které Azure nabízí.

## <a name="next-steps"></a>Další postup

K nasazení ukázkového projektu ze sady Visual Studio stačí jen pár jednoduchých kroků. Další informace najdete v článku [Vytvoření webu ASP.NET Core](service-fabric-mesh-quickstart-dotnet-core.md). 

Najděte odpovědi na [běžné dotazy](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
