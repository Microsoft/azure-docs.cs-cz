---
title: Terminologie služby Azure Service Fabric sítě | Dokumentace Microsoftu
description: Další informace o běžných termínů pro Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: db7a89ffa4ff1a2eb271869c93b7beb088fd13b3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076009"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologie Service Fabric mřížky

Sítě Azure Service Fabric je plně spravovaná služba, která vývojářům umožňuje nasadit mikroslužbových aplikací bez nutnosti spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje podrobnosti o terminologii používané službou Azure Service Fabric mřížky pochopit termínů používaných v dokumentaci.

## <a name="service-fabric"></a>Service Fabric

Service Fabric je open source platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb. Service Fabric je orchestrátor, která je základem sítě pro Service Fabric. Service Fabric poskytuje možnosti pro jak můžete vytvářet a spouštět vaše aplikace mikroslužeb. K psaní služeb a rozhodnete, jak spustit aplikaci z několika možností prostředí můžete použít libovolné architektury.

## <a name="deployment-and-application-models"></a>Modely nasazení a aplikace 

K nasazení služby, budete muset popisují, jak by měly být spuštěny. Service Fabric podporuje tři různé modely nasazení:

### <a name="resource-model"></a>Model prostředků
Prostředky jsou cokoli, co je možné nasadit jednotlivě do Service Fabric, včetně aplikací, služeb, sítě a svazky. Prostředky jsou definovány pomocí souboru YAML nebo soubor JSON s použitím schéma modelu prostředků Azure.

Model prostředků je nejjednodušší způsob, jak popisují své aplikace Service Fabric. Hlavní zaměřuje se na jednoduché nasazení a správu kontejnerizovaných služeb.

Prostředky je možné nasadit všude, kde běží Service Fabric.

### <a name="native-model"></a>Nativní model
Model nativní aplikace poskytuje svoje aplikace s využitím úplného nízké úrovně přístupu do Service Fabric. Aplikace a služby jsou definovány jako registrovaných typů v souborech manifestu XML.

Nativní model podporuje rozhraní modelu Reliable Services, které poskytuje přístup k rozhraní API modulu runtime Service Fabric a rozhraní API v jazyce C# a Java pro správu clusteru. Nativní model také podporuje libovolný kontejnery a spustitelné soubory.

Nativní model není podporována v prostředí sítě pro Service Fabric.

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

|Frameworks\Deployment modelu |Model prostředků |Manifest modelu | Vytvořit|
|---|---|---|---|
|Reliable Actors a Reliable Services |Nepodporuje se |Podporováno |Nepodporuje se |
|Jakékoli architektury nebo jazyka |Podporované v kontejnerech |Podporované jako procesy a v kontejnerech |Podporované v kontejnerech |

Následující tabulka popisuje podporované prostředí a nasazení modelu kombinace.

|Environment\Deployment modelu |Model prostředků |Manifest modelu |Vytvořit |
|---|---|---|---|
|Síť Azure Service Fabric |Podporováno |Nepodporuje se|Nepodporuje se |
|Všechna další prostředí |Podporované (některé prostředky mají požadavky pro práci v prostředí) |Podporováno |Omezená podpora |

## <a name="next-steps"></a>Další postup

Další informace o Service Fabric sítě, naleznete v přehledu:
- [Přehled Service Fabric mřížky](service-fabric-mesh-overview.md)
