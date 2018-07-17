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
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075970"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Úvod do modelu prostředků služby Service Fabric

Modelu prostředků služby Service Fabric popisuje jednoduchý přístup k definování prostředků, které tvoří aplikace Service Fabric mřížky. V tomto modelu jsou aktuálně podporovány následující typy prostředků:

- Aplikace
- Služby
- Svazky
- Networks

Každý prostředek je popsána deklarativně v souboru prostředků, což je jednoduchá YAML nebo dokument JSON, který popisuje použití mřížky a je zajištěno platformou Service Fabric.

## <a name="resource-overview"></a>Přehled prostředků

### <a name="applications-and-services"></a>Aplikace a služby

Prostředek služby Application je jednotka nasazení, správu verzí a životního cyklu aplikace sítě. Skládá se z jednoho nebo více prostředků služeb, které představují mikroslužby. Každý prostředek služby, pak se skládá z jednoho nebo více balíčků kódu, které popisují vše potřebné pro spuštění image kontejneru, který je přidružený k balíčku kódu, včetně následujících:

- Název kontejneru, verzi a registru
- Prostředky procesoru a paměti vyžadované pro každý kontejner
- Koncové body sítě
- Svazky v kontejneru, odkazující na samostatný svazek prostředek připojení.

Všechny balíčky kódu definované jako součást služby prostředků se nasazují a aktivují společně jako skupina. Prostředek služby také popisuje, jak velký počet instancí služby ke spuštění a také odkazuje na další prostředky (například síťový prostředek) závisí na.

Pokud aplikace sítě se skládá z více než jedna služba, není zaručena pro spuštění společně na stejném uzlu. Navíc během upgradu aplikace selhání upgradu jedné služby způsobí všechny služby vracené zpět k předchozí verzi.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

Jak alluded dříve, můžete spravovat nezávisle životního cyklu jednotlivých instancí aplikací. Například jedna instance aplikace je možné upgradovat nezávisle z jiné instance aplikace. Obvykle udržovali počet služeb v aplikaci docela malá jako další služby, které vložíte do aplikace, tím složitější bude spravovat jednotlivé služby nezávisle na sobě.

### <a name="networks"></a>Networks

Síťový prostředek je jednotlivě nasaditelnému prostředku, nezávisle na prostředek aplikace nebo služby, který může označujeme jako jejich závislosti. Používá se k vytvoření privátní sítě pro vaše aplikace. Více služeb z různých aplikací může být součástí stejné sítě.

> [!NOTE]
> Aktuální verze preview podporuje pouze mapování 1: 1 mezi aplikací a sítě

### <a name="volumes"></a>Svazky

Svazky jsou adresáře, které se připojit k uvnitř instancí kontejneru, které můžete použít k uložení stavu. Prostředku svazku je deklarativní způsob, jak popisují, jak je připojený adresář a záložní úložiště pro něj.

## <a name="programming-models"></a>Programovací modely
Prostředek služby vyžaduje jenom image kontejneru pro spuštění, který se odkazuje v kódu balíčky přidružené k prostředku. Můžete spustit libovolný kód napsané v jakémkoli jazyce pomocí libovolné architektury uvnitř kontejneru bez nutnosti znát nebo pomocí sítě pro Service Fabric určitých rozhraní API. 

Kód aplikace zůstane přenosné i mimo službu prostředků infrastruktury sítě a nasazení vaší aplikace zůstaly konzistentní bez ohledu na jazyk nebo rozhraní používaný k implementaci služby. Zda je vaše aplikace ASP.NET Core, Go nebo jenom sadu procesů a skriptů, model nasazení prostředků sítě Service Fabric se nezmění. 

## <a name="deployment"></a>Nasazení

Při nasazování do sítě pro Service Fabric, jsou nasazené prostředky jako šablony Azure Resource Manageru do Azure pomocí protokolu HTTP nebo rozhraní příkazového řádku Azure. 


## <a name="next-steps"></a>Další postup 
Další informace o Service Fabric sítě, naleznete v přehledu:
- [Přehled Service Fabric mřížky](service-fabric-mesh-overview.md)
