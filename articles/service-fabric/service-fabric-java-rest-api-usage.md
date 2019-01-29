---
title: Rozhraní API klienta Azure Service Fabric Java | Dokumentace Microsoftu
description: Vygenerování a použití klientských rozhraní API Service Fabric v Javě pomocí specifikace rozhraní REST API služby Service Fabric klienta
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 116defb43126932c1a9ce0e7a9d588e731abff78
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182026"
---
# <a name="azure-service-fabric-java-client-apis"></a>Rozhraní API klienta Azure Service Fabric Java

Service Fabric klientských rozhraní API umožňuje nasazení a správu mikroslužeb založený na aplikace tak kontejnery v clusteru Service Fabric v Azure, místně, v místním vývojovém počítači nebo v jiný cloud. Tento článek popisuje, jak vygenerovat a použít Service Fabric v Javě klientských rozhraní API nad Service Fabric klienta REST API

## <a name="generate-the-client-code-using-autorest"></a>Generování kódu klienta pomocí AutoRest

[AutoRest](https://github.com/Azure/autorest) je nástroj, který generuje klientské knihovny pro přístup k rozhraní RESTful webových služeb. Vstup pro AutoRest je specifikace, které popisují rozhraní REST API ve formátu specifikace OpenAPI. [Service Fabric klienta REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) postupujte podle téhle specifikaci.

Postupujte podle pokynů uvedených níže pro generování kódu klienta Service Fabric v Javě pomocí nástroje AutoRest.

1. Instalace nodejs a NPM na počítači

    Pokud používáte Linux pak:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Pokud používáte Mac OS X klikněte:
    ```bash
    brew install node
    ```

2. Nainstalujte pomocí NPM AutoRest.
    ```bash
    npm install -g autorest
    ```

3. Rozvětvení a klonování [azure rest api specifikace](https://github.com/Azure/azure-rest-api-specs) úložiště v místním počítači a přejděte do naklonovaného umístění z terminálu vašeho počítače.


4. Přejdete na umístění uvedená níže v naklonovaného úložiště.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Pokud se vaší verze clusteru není 6.0. * přejděte do příslušného adresáře ve složce stabilní.
    >   

5. Spusťte následující příkaz autorest ke generování kódu klienta jazyka java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Níže je příklad ukazující využití autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Zadáním následujícího příkazu trvá ``servicefabric.json`` specifikace souboru jako vstup a vygeneruje kód klienta java v ``java-rest-api-     code`` složky a vloží kód v ``servicefabricrest`` oboru názvů. Po provedení tohoto kroku ekvivalent byste našli dvě složky ``models``, ``implementation`` a dva soubory ``ServiceFabricClientAPIs.java`` a ``package-info.java`` vygenerované v ``java-rest-api-code`` složky.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Zahrnout a pomocí generovaného klienta ve vašem projektu

1. Přidáte generovaný kód odpovídajícím způsobem ve vašem projektu. Doporučujeme vytvořit knihovny pomocí vygenerovaného kódu a zahrnout tuto knihovnu do projektu.
2. Pokud vytváříte knihovnu, zahrnout do své knihovny projektu následující závislost. Pokud postupujete jiný přístup pak uvést závislost odpovídajícím způsobem.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Například, pokud používáte Maven systém sestavení patří do vašeho ``pom.xml`` souboru:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Vytvoření RestClient pomocí následujícího kódu:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Pomocí objektu klienta a volat odpovídající podle potřeby. Tady je několik příkladů, které ukazují použití objektu klienta. Předpokládáme, že je balíček aplikace sestavena a nahrána do úložiště imagí před použitím nižší než rozhraní API.
    * Zřídit aplikace
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Vytvoření aplikace

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Principy generovaného kódu
Pro každé rozhraní API najdete čtyři přetížení implementace. Pokud jsou volitelné parametry by najít čtyři další změny, včetně těchto volitelné parametry. Představme si třeba rozhraní API ``removeReplica``.
 1. **public void removeReplica (řetězec nodeName, UUID partitionId, replicaId řetězec, logická forceRemove, dlouhý časový limit)**
    * Toto je synchronní variant volání removeReplica rozhraní API
 2. **veřejné ServiceFuture<Void> removeReplicaAsync (řetězec nodeName, UUID partitionId, replicaId řetězec, logická forceRemove, dlouhý časový limit, finální ServiceCallback<Void> serviceCallback)**
    * Tato varianta volání rozhraní API je možné, pokud chcete použít budoucí založené na asynchronní programování a použít zpětná volání
 3. **veřejné pozorovat<Void> removeReplicaAsync (nodeName řetězec, ID oddílu UUID, replicaId řetězec)**
    * Tato varianta volání rozhraní API lze použít, pokud chcete použít reaktivní asynchronní programování
 4. **veřejné pozorovat < ServiceResponse<Void>> removeReplicaWithServiceResponseAsync (nodeName řetězec, ID oddílu UUID, replicaId řetězec)**
    * Tato varianta volání rozhraní API je možné, pokud chcete použít reaktivní asynchronní programování a řešil odpověď NEZPRACOVANÁ rest

## <a name="next-steps"></a>Další postup
* Další informace o [Service Fabric rozhraní REST API](https://docs.microsoft.com/rest/api/servicefabric/)

