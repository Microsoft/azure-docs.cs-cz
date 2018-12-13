---
title: Terminologie služby Azure Service Fabric sítě | Dokumentace Microsoftu
description: Další informace o běžných termínů pro Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: fa2c34168423796a82f274db1b60b45ac10b5501
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888660"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologie Service Fabric mřížky

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje podrobnosti o terminologii používané službou Azure Service Fabric mřížky pochopit termínů používaných v dokumentaci.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) je open source platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric je orchestrátor, která je základem sítě pro Service Fabric. Service Fabric poskytuje možnosti pro jak můžete vytvářet a spouštět vaše aplikace mikroslužeb. K psaní služeb a rozhodnete, jak spustit aplikaci z několika možností prostředí můžete použít libovolné architektury.

## <a name="application-and-service-concepts"></a>Aplikace a koncepty služby

**Aplikace Service Fabric mřížky**: aplikace Service Fabric sítě jsou popsány pomocí [Model prostředků](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (zdrojové soubory pro YAML a JSON) a je možné nasadit do libovolného prostředí, kde běží Service Fabric.

**Nativní aplikace Service Fabric**: nativní aplikace Service Fabric jsou popsané [nativní aplikační Model](/azure/service-fabric/service-fabric-application-model) (založený na formátu XML manifestů aplikace a služby).  Nativní aplikace Service Fabric nelze spustit v Service Fabric mřížky.

**Aplikace**: aplikace A služby prostředků infrastruktury sítě je jednotka nasazení, správu verzí a životního cyklu aplikace sítě. Životní cyklus jednotlivých instancí aplikací se dají spravovat nezávisle.  Aplikace se skládají z jednoho nebo více balíčků kódu služby a nastavení. Aplikace je definována pomocí schématu modelu prostředků Azure (SV).  Služby jsou popsány jako vlastnosti prostředku aplikace do šablony správce prostředků.  Sítě a svazky, které používají aplikace se odkazuje aplikace.  Při vytváření aplikací, aplikací, služeb, sítě a svazky jsou modelovány pomocí modelu prostředků služby Service Fabric.

**Služba**: službu v aplikaci představuje mikroslužby a provádí kompletní a samostatné funkce. Každá služba se skládá z jednoho nebo více, balíčky kódu, které popisují vše potřebné pro spuštění image kontejneru, který je přidružený k balíčku kódu.  Počet replik služby v aplikaci je možné škálovat a oddálení.

**Balíček kódu**: balíčky kódu popisují vše potřebné pro spuštění image kontejneru, který je přidružený k balíčku kódu, včetně následujících:

* Název kontejneru, verzi a registru
* Prostředky procesoru a paměti vyžadované pro každý kontejner
* Koncové body sítě
* Svazky v kontejneru, odkazující na samostatný svazek prostředek připojení.

## <a name="deployment-and-application-models"></a>Modely nasazení a aplikace 

K nasazení služby, budete muset popisují, jak by měly být spuštěny. Service Fabric podporuje tři různé modely nasazení:

### <a name="resource-model"></a>Model prostředků
Prostředky topologie Fabric Service jsou cokoli, co je možné nasadit jednotlivě do Service Fabric; včetně aplikací, služeb, sítě a svazky. Prostředky jsou definovány pomocí souboru JSON, který je možné nasadit do koncového bodu clusteru.  Pro Service Fabric sítě se používá schéma modelu prostředků Azure. Schéma souboru YAML lze také snadněji vytvářet soubory definic. Prostředky je možné nasadit všude, kde běží Service Fabric. Model prostředků je nejjednodušší způsob, jak popisují své aplikace Service Fabric. Hlavní zaměřuje se na jednoduché nasazení a správu kontejnerizovaných služeb. Další informace najdete v článku [Úvod do modelu prostředků služby Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Nativní model
Model nativní aplikace poskytuje svoje aplikace s využitím úplného nízké úrovně přístupu do Service Fabric. Aplikace a služby jsou definovány jako registrovaných typů v souborech manifestu XML.

Nativní model podporuje rozhraní modelu Reliable Services, které poskytuje přístup k rozhraní API modulu runtime Service Fabric a rozhraní API v jazyce C# a Java pro správu clusteru. Nativní model také podporuje libovolný kontejnery a spustitelné soubory.

Nativní model není podporována v prostředí sítě pro Service Fabric.  Další informace najdete v tématu [Přehled programovacího modelu](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) je součástí projektu Dockeru. Service Fabric poskytuje omezenou podporu pro nasazení aplikací pomocí Docker Compose modelu.

## <a name="environments"></a>Prostředí

Service Fabric je technologie open source platforma, která na základě několika různými službami a produkty. Společnost Microsoft poskytuje následující možnosti:

 - **Service Fabric mřížky**: plně spravovaná služba pro spouštění aplikací Service Fabric v Microsoft Azure.
 - **Azure Service Fabric**: Azure hostované nabídky clusteru Service Fabric. Poskytuje integrace mezi službami Service Fabric a infrastrukturu Azure, spolu s upgradu a správy konfigurace clusterů Service Fabric.
 - **Service Fabric samostatné**: sadu nástrojů pro instalaci a konfiguraci pro [nasadit clustery Service Fabric kdekoli](/azure/service-fabric/service-fabric-deploy-anywhere) (v místním nebo jakýkoli jiný poskytovatel cloudu). Není spravuje Azure.
 - **Vývojový cluster Service Fabric**: poskytuje místní vývojové prostředí na Windows, Linux nebo Mac pro vývoj aplikací Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matice podpory prostředí, rozhraní a modelu nasazení
Různá prostředí mají různé úrovně podpory platformy a modely nasazení. Následující tabulka popisuje podporované architektury a kombinace modelu nasazení.

| Typ aplikace | Popisuje | Síť Azure Service Fabric | Clustery Azure Service Fabric (libovolný operační systém)| Místní cluster | Samostatný cluster |
|---|---|---|---|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML a JSON) | Podporováno |Nepodporuje se | Windows – podporováno, Linux a Mac – není podporované | Windows – není podporované |
|Nativní aplikace Service Fabric | Model nativní aplikace (XML) | Nepodporuje se| Podporováno|Podporováno|Windows – podporováno|

Následující tabulka popisuje různé aplikačních modelů a nástrojů, která existuje pro ně pro Service Fabric.

| Typ aplikace | Popisuje | Visual Studio | Eclipse | SFCTL | ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU AZ | PowerShell|
|---|---|---|---|---|---|---|---|---|---|
| Aplikace Service Fabric sítě | Model prostředků (YAML a JSON) | VS 2017 |Nepodporuje se |Nepodporuje se | Podporuje se – pouze prostředí sítě | Nepodporuje se|
|Nativní aplikace Service Fabric | Model nativní aplikace (XML) | VS 2017 a VS 2015| Podporováno|Podporováno|Podporováno|Podporováno|

## <a name="next-steps"></a>Další postup

Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).

Najděte odpovědi na [běžné dotazy](service-fabric-mesh-faq.md).
