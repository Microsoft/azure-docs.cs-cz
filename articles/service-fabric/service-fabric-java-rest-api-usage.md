---
title: Rozhraní API služby Azure Service Fabric Java klienta | Microsoft Docs
description: Vygenerování a použití rozhraní API klienta služby Fabric Java pomocí specifikace rozhraní REST API klienta Service Fabric
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
ms.openlocfilehash: 987959742335940dca8eb57c54d593aea90dec15
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111180"
---
# <a name="azure-service-fabric-java-client-apis"></a>Rozhraní API klienta služby Azure Service Fabric Java

Rozhraní API klienta Service Fabric umožňuje nasazení a správě mikroslužeb aplikace a kontejnery v clusteru Service Fabric na základě Azure, místně na místním vývojovém počítači nebo v jiných cloudu. Tento článek popisuje, jak vygenerovat a používat rozhraní API klienta služby Fabric Java nad klient REST API pro Service Fabric

## <a name="generate-the-client-code-using-autorest"></a>Generování kódu klienta pomocí AutoRest

[AutoRest](https://github.com/Azure/autorest) je nástroj, který generuje klientské knihovny pro přístup k RESTful webové služby. Vstup AutoRest je specifikace, která popisuje rozhraní API REST v OpenAPI specifikace formátu. [Service Fabric klienta REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) podle téhle specifikaci.

Postupujte podle kroků uvedených níže ke generování kódu klienta služby Fabric Java pomocí nástroje AutoRest.

1. Instalace nodejs a NPM na počítači

    Pokud používáte Linux pak:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Pokud používáte Mac OS X pak:
    ```bash
    brew install node
    ```

2. Nainstalujte pomocí NPM AutoRest.
    ```bash
    npm install -g autorest
    ```

3. Rozvětvení a klonování [azure rest api specifikace](https://github.com/Azure/azure-rest-api-specs) úložiště v místním počítači a přejděte do umístění, klonovaný z terminálu počítače.


4. Přejděte do umístění dál uvedených ve vaší klonovaný úložišti.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Pokud není váš cluster verze 6.0. * potom přejděte do příslušného adresáře ve složce stabilní.
    >   

5. Spusťte následující příkaz autorest ke generování kódu klienta java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Dole je příklad Ukázka použití autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Následující příkaz trvá ``servicefabric.json`` specifikaci souboru jako vstup a generuje kód klienta java v ``java-rest-api-     code`` složky a vloží kód v ``servicefabricrest`` oboru názvů. Po provedení tohoto kroku by najít dvě složky ``models``, ``implemenation`` a dva soubory ``ServiceFabricClientAPIs.java`` a ``package-info.java`` vygenerovaných ``java-rest-api-code`` složky.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Zahrnout a pomocí generovaného klienta ve vašem projektu

1. Do projektu přidejte generovaný kód správně. Doporučujeme vytvořit knihovny pomocí generovaného kódu a zahrnout do projektu této knihovny.
2. Pokud vytváříte knihovnu můžete zahrnout následující závislosti projektu své knihovny. Použijete-li jiný přístup pak uvést závislost správně.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Například, pokud používáte Maven systém sestavení patří ve vaší ``pom.xml`` souboru:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Vytvořte RestClient, pomocí následujícího kódu:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Pomocí klientského objektu a provádět odpovídající volání podle potřeby. Zde jsou některé příklady, které ukazují použití klientského objektu. Jsme předpokládají, že je balíček aplikace vytvořené a nahrál do úložiště bitové kopie, před použitím nižší než rozhraní API.
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
Pro každé rozhraní API najdete čtyři přetížení implementace. Pokud jsou volitelné parametry by najít čtyři další variace, včetně těchto volitelné parametry. Zvažte například rozhraní API ``removeReplica``.
 1. **veřejné void removeReplica (nodeName řetězec, UUID partitionId, replicaId řetězec, logická hodnota forceRemove, dlouhý časový limit)**
    * Toto je synchronní variant volání removeReplica rozhraní API
 2. **veřejné ServiceFuture<Void> removeReplicaAsync (řetězec nodeName, UUID partitionId, replicaId řetězec, logická hodnota forceRemove, dlouhé vypršel časový limit, poslední ServiceCallback<Void> serviceCallback)**
    * Tato varianta volání rozhraní API lze použít, pokud chcete využívat budoucí na základě asynchronního programování a zpětná volání
 3. **veřejné lze zobrazit<Void> removeReplicaAsync (řetězec nodeName, ID oddílu UUID, replicaId řetězec)**
    * Tato varianta volání rozhraní API lze použít, pokud chcete použít reaktivní asynchronní programování
 4. **veřejné lze zobrazit < ServiceResponse<Void>> removeReplicaWithServiceResponseAsync (řetězec nodeName, ID oddílu UUID, replicaId řetězec)**
    * Tato varianta volání rozhraní API lze použít, pokud chcete použít reaktivní asynchronní programování a řešit NEZPRACOVANÁ rest odpovědi

## <a name="next-steps"></a>Další postup
* Další informace o [služby Fabric rozhraní REST API](https://docs.microsoft.com/rest/api/servicefabric/)

