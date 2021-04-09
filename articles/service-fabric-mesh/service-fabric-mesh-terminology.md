---
title: Terminologie pro síť Azure Service Fabric
description: Tento článek podrobně popisuje terminologii, kterou používá Azure Service Fabric mřížka, která vám pomůže lépe pochopit výrazy používané v dokumentaci.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 1fda83ec00bed0f0c67d45e1e1af61baa5de5e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626752"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologie Service Fabricch mřížek

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek podrobně popisuje terminologii, kterou používá Azure Service Fabric mřížka, která vám pomůže lépe pochopit výrazy používané v dokumentaci.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](../service-fabric/index.yml) je open source platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric je nástroj Orchestrator, který je Service Fabric sítí. Service Fabric poskytuje možnosti, jak můžete vytvářet a spouštět aplikace mikroslužeb. Můžete použít libovolné rozhraní k zápisu služeb a zvolit, kde spustit aplikaci z více možností prostředí.

## <a name="application-and-service-concepts"></a>Koncepce aplikací a služeb

**Service Fabric aplikace sítě**: aplikace Service Fabric mřížku jsou popsány v [modelu prostředků](./service-fabric-mesh-service-fabric-resources.md) (soubory prostředků YAML a JSON) a lze je nasadit do jakéhokoli prostředí, kde Service Fabric běží.

**Service Fabric nativní aplikace**: Service Fabric nativní aplikace jsou popsány v rámci [nativního aplikačního modelu](../service-fabric/service-fabric-application-model.md) (aplikace založené na jazyce XML a manifesty služeb).  Service Fabric nativní aplikace nelze spouštět v Service Fabric sítě.

**Aplikace**: Service Fabric aplikace sítě je jednotka nasazení, správy verzí a životnosti aplikace sítě. Životní cyklus jednotlivých instancí aplikace je možné spravovat nezávisle.  Aplikace se skládají z jednoho nebo více balíčků kódu služby a nastavení. Aplikace je definovaná pomocí schématu Azure Resource model (RM).  Služby jsou popsány jako vlastnosti prostředku aplikace v šabloně RM.  Odkazy na sítě a svazky používané aplikací jsou odkazovány v aplikaci.  Při vytváření aplikace jsou aplikace, služby (y), sítě a svazky modelovány pomocí modelu Service Fabric prostředků.

**Služba**: služba v aplikaci představuje mikroslužbu a provede úplnou a samostatnou funkci. Každá služba se skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu.  Počet replik služby v aplikaci je možné škálovat a navýšit.

**Balíček kódu**: balíčky kódu popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu, včetně následujících:

* Název, verze a registr kontejneru
* Prostředky procesoru a paměti vyžadované pro každý kontejner
* Síťové koncové body
* Svazky, které se mají připojit do kontejneru a odkazují na samostatný prostředek svazku.

## <a name="deployment-and-application-models"></a>Modely nasazení a aplikace 

Chcete-li nasadit své služby, je třeba popsat, jak by měly být spuštěny. Service Fabric podporuje tři různé modely nasazení:

### <a name="resource-model"></a>Model prostředků
Prostředky Service Fabric jsou cokoli, co se dá nasadit jednotlivě do Service Fabric; včetně aplikací, služeb, sítí a svazků. Prostředky se definují pomocí souboru JSON, který se dá nasadit na koncový bod clusteru.  V případě Service Fabric sítě se používá schéma modelu Azure Resource model. Schéma souborů YAML lze také použít k snadnějšímu vytváření definičních souborů. Prostředky je možné nasadit kdekoli Service Fabric spuštění. Model prostředků představuje nejjednodušší způsob, jak popsání Service Fabricch aplikací. Hlavním fokusem je jednoduché nasazení a Správa kontejnerových služeb. Pokud se chcete dozvědět víc, přečtěte si [Úvod do modelu prostředků Service Fabric](./service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Nativní model
Nativní aplikační model poskytuje vašim aplikacím úplný přístup na nízké úrovni k Service Fabric. Aplikace a služby jsou definovány jako registrované typy v souborech manifestu XML.

Nativní model podporuje rozhraní Reliable Services Framework, které poskytuje přístup k rozhraním API Service Fabric runtime a k rozhraním API pro správu clusteru v jazycích C# a Java. Nativní model podporuje také libovolné kontejnery a spustitelné soubory.

Nativní model není podporován v prostředí Service Fabric sítě.  Další informace naleznete v tématu [Přehled programovacího modelu](../service-fabric/service-fabric-choose-framework.md).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) je součástí projektu Docker. Service Fabric poskytuje omezené podpory pro nasazení aplikací pomocí modelu Docker Compose.

## <a name="environments"></a>Prostředí

Service Fabric je open source technologie pro platformu, na které jsou založené různé služby a produkty. Společnost Microsoft poskytuje následující možnosti:

 - **Service Fabricová síť**: plně spravovaná služba pro spouštění Service Fabric aplikací v Microsoft Azure.
 - **Azure Service Fabric**: Nabídka Azure hosted Service Fabric clusteru. Poskytuje integraci mezi Service Fabric a infrastrukturou Azure spolu se správou aktualizací a konfigurací Service Fabricch clusterů.
 - **Service Fabric Standalone**: sada nástrojů pro instalaci a konfiguraci pro [nasazení Service Fabricch clusterů kdekoli](../service-fabric/service-fabric-deploy-anywhere.md) (místně nebo v jakémkoli poskytovateli cloudu). Nespravuje se v Azure.
 - **Service Fabric vývojový cluster**: poskytuje místní vývojové prostředí v systému Windows, Linux nebo Mac pro vývoj aplikací Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matice podpory pro prostředí, architekturu a model nasazení
Různá prostředí mají různé úrovně podpory pro architektury a modely nasazení. V následující tabulce jsou popsány podporované kombinace rozhraní a modelu nasazení.

| Typ aplikace | Popisuje | Síť Azure Service Fabric | Clustery Azure Service Fabric (libovolný operační systém)| Místní cluster | Samostatný cluster |
|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML & JSON) | Podporováno |Nepodporováno | Windows – podporováno, Linux a Mac – nepodporováno | Windows – nepodporováno |
|Service Fabric nativních aplikací | Nativní aplikační model (XML) | Nepodporuje se| Podporováno|Podporováno|Windows – podporováno|

V následující tabulce jsou popsány různé modely aplikací a nástroje, které pro ně existují pro Service Fabric.

| Typ aplikace | Popisuje | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML & JSON) | VS 2017 |Nepodporováno |Nepodporováno | Podporované – jenom pro prostředí sítě | Nepodporuje se|
|Service Fabric nativních aplikací | Nativní aplikační model (XML) | VS 2017 a VS 2015| Podporováno|Podporováno|Podporováno|Podporováno|

## <a name="next-steps"></a>Další kroky

Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).

Najděte odpovědi na [běžné dotazy](service-fabric-mesh-faq.md).
