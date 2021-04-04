---
title: Klientská rozhraní API pro Azure Service Fabric Java
description: Generování a použití Service Fabric rozhraní API klienta Java pomocí Service Fabric specifikace REST API klienta
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324620"
---
# <a name="azure-service-fabric-java-client-apis"></a>Klientská rozhraní API pro Azure Service Fabric Java

Rozhraní API klienta Service Fabric umožňují nasazovat a spravovat aplikace a kontejnery založené na mikroslužbách v clusteru Service Fabric v Azure, místně, na místním vývojovém počítači nebo v jiném cloudu. Tento článek popisuje, jak vygenerovat a používat Service Fabric rozhraní API klienta Java nad Service Fabric rozhraní REST API klienta.

## <a name="generate-the-client-code-using-autorest"></a>Generování kódu klienta pomocí programu AutoRest

[AutoRest](https://github.com/Azure/autorest) je nástroj, který generuje klientské knihovny pro přístup k RESTful webovým službám. Vstup do AutoRest je specifikace, která popisuje REST API pomocí formátu specifikace OpenAPI. [Service Fabric rozhraní REST API klienta](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) dodržujte tuto specifikaci.

Použijte následující postup, chcete-li vygenerovat Service Fabric kód klienta Java pomocí nástroje AutoRest.

1. Instalace nodejs a NPM na počítači

    Pokud používáte Linux, pak:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Pokud používáte Mac OS X pak:
    ```bash
    brew install node
    ```

2. Nainstalujte AutoRest pomocí NPM.
    ```bash
    npm install -g autorest
    ```

3. Rozvětvení a klonování úložiště [specifikací Azure-REST-API](https://github.com/Azure/azure-rest-api-specs)  v místním počítači a přechod do klonovaného umístění z terminálu počítače.


4. Přejít do umístění uvedeného níže v klonovaném úložišti.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Pokud vaše verze clusteru není 6,0. *, potom v části stabilní složku otevřete příslušný adresář.
    >   

5. Spuštěním následujícího příkazu AutoRest vygenerujte kód klienta Java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Níže je uveden příklad, který demonstruje použití AutoRest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Následující příkaz přebírá ``servicefabric.json`` soubor specifikace jako vstup a generuje kód klienta Java ve ``java-rest-api-     code`` složce a uzavře kód v  ``servicefabricrest`` oboru názvů. Po provedení tohoto kroku najdete dvě složky a ``models`` ``implementation`` dva soubory ``ServiceFabricClientAPIs.java`` a ``package-info.java`` vygenerované ve ``java-rest-api-code`` složce.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Zahrnutí a použití vygenerovaného klienta v projektu

1. Přidejte do projektu generovaný kód odpovídajícím způsobem. Doporučujeme vytvořit knihovnu pomocí generovaného kódu a zahrnout tuto knihovnu do projektu.
2. Pokud vytváříte knihovnu, potom do projektu knihovny přidejte následující závislost. Pokud budete postupovat podle různých přístupů, zahrňte odpovídajícím způsobem závislost.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Například pokud používáte sestavovací systém Maven, zahrnuje následující do ``pom.xml`` souboru:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Vytvořte RestClient pomocí následujícího kódu:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Použijte objekt klienta a proveďte odpovídající volání podle potřeby. Zde je několik příkladů, které ukazují použití objektu klienta. Před použitím rozhraní API níže předpokládáme, že je balíček aplikace sestavený a nahraný do úložiště imagí.
    * Zřízení aplikace
    
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

## <a name="understanding-the-generated-code"></a>Princip vygenerovaného kódu
Pro každé rozhraní API najdete čtyři přetížení implementace. Pokud jsou k dispozici volitelné parametry, najdete čtyři další variace včetně těchto volitelných parametrů. Podívejte se například na rozhraní API ``removeReplica`` .
 1. **Public void removeReplica (řetězcový uzel, UUID, identifikátor UUID, identifikátor replicaId řetězce, Boolean forceRemove, dlouhý časový limit)**
    * Toto je synchronní varianta volání rozhraní removeReplica API.
 2. **Public ServiceFuture \<Void> removeReplicaAsync (String Node, UUID, identifikátor ReplicaID řetězce, logická forceRemove, dlouhý časový limit, finální ServiceCallback \<Void> ServiceCallback)**
    * Tato varianta volání rozhraní API se dá použít, pokud chcete použít budoucí asynchronní programování a používat zpětná volání.
 3. **Veřejná pozorovatelná \<Void> removeReplicaAsync (řetězec Node, UUID, identifikátor ReplicaID řetězce)**
    * Tato varianta volání rozhraní API se dá použít, pokud chcete použít reaktivní asynchronní programování.
 4. **Veřejná pozorovatelná \<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync (řetězec Node, UUID, identifikátor ReplicaID řetězce)**
    * Tato varianta volání rozhraní API se dá použít, pokud chcete použít reaktivní asynchronní programování a pracovat s nezpracovanými odpověďmi REST.

## <a name="next-steps"></a>Další kroky
* Další informace o [rozhraních REST api Service Fabric](/rest/api/servicefabric/)
