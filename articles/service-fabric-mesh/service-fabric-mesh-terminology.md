---
title: Terminologie pro síť Fabric služby Azure
description: Tento článek podrobně popisuje terminologii používanou sítí Azure Service Fabric, která vám pomůže lépe porozumět pojmům používaným v dokumentaci.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 6df7a6b708bca81f1390ac879f24ea4c22c38bee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351977"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologie sítě Service Fabric Mesh

Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek podrobně popisuje terminologii používanou sítí Azure Service Fabric, která vám pomůže lépe porozumět pojmům používaným v dokumentaci.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) je platforma distribuovaných systémů s otevřeným zdrojovým kódem, která usnadňuje balení, nasazování a správu škálovatelných a spolehlivých mikroslužeb. Service Fabric je orchestrátor, který pohání service fabric mesh. Service Fabric poskytuje možnosti, jak můžete vytvářet a spouštět aplikace mikroslužeb. Můžete použít libovolný rámec k zápisu služby a zvolte, kde chcete spustit aplikaci z více možností prostředí.

## <a name="application-and-service-concepts"></a>Aplikační a servisní koncepty

**Service Fabric Mesh Application**: Service Fabric Mesh Aplikace jsou popsány [model prostředků](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (YAML a JSON soubory prostředků) a lze nasadit do libovolného prostředí, kde service fabric běží.

**Nativní aplikace Service Fabric**: Nativní aplikace Service Fabric jsou popsány [nativním aplikačním modelem](/azure/service-fabric/service-fabric-application-model) (manifesty aplikací a služeb založených na jazyce XML).  Nativní aplikace service fabric nelze spustit v síti Service Fabric.

**Aplikace**: Aplikace Service Fabric Mesh je jednotka nasazení, správy verzí a životnosti aplikace mesh. Životní cyklus každé instance aplikace lze spravovat nezávisle.  Aplikace se skládají z jednoho nebo více balíčků a nastavení kódu služby. Aplikace je definována pomocí schématu modelu prostředků Azure (RM).  Služby jsou popsány jako vlastnosti prostředku aplikace v šabloně RM.  Na sítě a svazky používané aplikací aplikace odkazuje.  Při vytváření aplikace jsou aplikace, služby, sítě a svazky modelovány pomocí modelu prostředků Service Fabric.

**Služba**: Služba v aplikaci představuje mikroslužbu a provádí úplnou a samostatnou funkci. Každá služba se skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu.  Počet replik služby v aplikaci lze škálovat dovnitř a ven.

**Balíček kódu**: Balíčky kódu popisují vše potřebné ke spuštění image kontejneru přidružené k balíčku kódu, včetně následujících:

* Název kontejneru, verze a registr
* Prostředky procesoru a paměti potřebné pro každý kontejner
* Koncové body sítě
* Svazky pro připojení do kontejneru, odkazující na samostatný prostředek svazku.

## <a name="deployment-and-application-models"></a>Modely nasazení a aplikace 

Chcete-li nasadit služby, musíte popsat, jak by měly být spuštěny. Service Fabric podporuje tři různé modely nasazení:

### <a name="resource-model"></a>Model prostředků
Prostředky Service Fabric jsou cokoli, co lze nasadit jednotlivě do Service Fabric; včetně aplikací, služeb, sítí a svazků. Prostředky jsou definovány pomocí souboru JSON, který lze nasadit do koncového bodu clusteru.  Pro síť Service Fabric se používá schéma modelu prostředků Azure. Schéma souboru YAML lze také použít ke snadněji vytvářet definiční soubory. Prostředky lze nasadit kdekoli service fabric spustí. Model prostředků je nejjednodušší způsob, jak popsat aplikace Service Fabric. Zaměřuje se především na jednoduché nasazení a správu kontejnerizovaných služeb. Další informace naleznete [v části Úvod do modelu prostředků service fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Nativní model
Nativní aplikační model poskytuje vašim aplikacím úplný nízkoúrovňový přístup k Service Fabric. Aplikace a služby jsou definovány jako registrované typy v souborech manifestů XML.

Nativní model podporuje rozhraní Reliable Services Framework, které poskytuje přístup k rozhraní mAČKÁM service fabric a rozhraní API pro správu clusteru v jazyce C# a java. Nativní model také podporuje libovolné kontejnery a spustitelné soubory.

Nativní model není podporován v prostředí Service Fabric Mesh.  Další informace naleznete v [tématu přehled programovacího modelu](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) je součástí projektu Docker. Service Fabric poskytuje omezenou podporu pro nasazení aplikací pomocí modelu Docker Compose.

## <a name="environments"></a>Prostředí

Service Fabric je technologie platformy s otevřeným zdrojovým kódem, na které je založeno několik různých služeb a produktů. Společnost Microsoft nabízí následující možnosti:

 - **Síť service fabric:** Plně spravovaná služba pro spouštění aplikací Service Fabric v Microsoft Azure.
 - **Azure Service Fabric:** Nabídka clusteru Azure hostované služby Fabric. Poskytuje integraci mezi Service Fabric a infrastruktury Azure, spolu s upgradem a správou konfigurace clusterů Service Fabric.
 - **Service Fabric samostatný**: Sada nástrojů pro instalaci a konfiguraci [pro nasazení clusterů Service Fabric kdekoli](/azure/service-fabric/service-fabric-deploy-anywhere) (místně nebo na libovolném poskytovateli cloudu). Není spravováno Azure.
 - **Service Fabric vývoj clusteru**: Poskytuje prostředí pro místní vývoj v systému Windows, Linux nebo Mac pro vývoj aplikací Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matice podpory modelu prostředí, architektury a nasazení
Různá prostředí mají různé úrovně podpory pro architektury a modely nasazení. Následující tabulka popisuje podporované kombinace architektury a modelu nasazení.

| Typ aplikace | Popsáno | Síť prostředků infrastruktury služby Azure | Clustery Fabric služby Azure (libovolný operační systém)| Místní cluster | Samostatný cluster |
|---|---|---|---|---|---|
| Síťové aplikace service fabric | Model prostředků (YAML & JSON) | Podporuje se |Nepodporuje se | Windows- podporované, Linux a Mac- není podporován | Systém Windows - není podporován |
|Nativní aplikace Service Fabric | Nativní aplikační model (XML) | Nepodporuje se| Podporuje se|Podporuje se|Systém Windows – podporován|

Následující tabulka popisuje různé modely aplikací a nástroje, které pro ně existují proti Service Fabric.

| Typ aplikace | Popsáno | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Síťové aplikace service fabric | Model prostředků (YAML & JSON) | VS 2017 |Nepodporuje se |Nepodporuje se | Podporováno – pouze síťové prostředí | Nepodporuje se|
|Nativní aplikace Service Fabric | Nativní aplikační model (XML) | VS 2017 a VS 2015| Podporuje se|Podporuje se|Podporuje se|Podporuje se|

## <a name="next-steps"></a>Další kroky

Další informace o službě Service Fabric Mesh najdete v článku s [přehledem služby Service Fabric Mesh](service-fabric-mesh-overview.md).

Najděte odpovědi na [běžné dotazy](service-fabric-mesh-faq.md).
