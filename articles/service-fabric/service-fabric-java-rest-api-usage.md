---
title: Azure Service Fabric java klientská api
description: Generování a používání rozhraní API klienta Service Fabric Java pomocí specifikace rozhraní REST ROZHRANÍ REST API klienta Service Fabric
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 0a243c1cd0ab0dcb93a1cc6169c89ba18606f346
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451669"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric java klientská api

Klientská api Service Fabric umožňují nasazovat a spravovat aplikace založené na mikroslužbách a kontejnery v clusteru Service Fabric v Azure, místně, v místním vývojovém počítači nebo v jiném cloudu. Tento článek popisuje, jak generovat a používat klientská api služby Fabric Java nad klientem Service Fabric klienta REST API

## <a name="generate-the-client-code-using-autorest"></a>Generovat kód klienta pomocí funkce AutoRest

[AutoRest](https://github.com/Azure/autorest) je nástroj, který generuje klientské knihovny pro přístup k webovým službám RESTful. Vstup do AutoRest je specifikace, která popisuje rozhraní REST API pomocí formátu Specifikace OpenAPI. [Service Fabric klienta REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) postupujte podle této specifikace .

Podle následujících kroků vygenerujte klientský kód Service Fabric Java pomocí nástroje AutoRest.

1. Instalace nodejs a NPM na počítači

    Pokud používáte Linux pak:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Pokud používáte Mac OS X, pak:
    ```bash
    brew install node
    ```

2. Nainstalujte autorest pomocí NPM.
    ```bash
    npm install -g autorest
    ```

3. Úložiště fork a clone [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) v místním počítači a přejděte na klonované umístění z terminálu vašeho počítače.


4. Přejděte na níže uvedené místo ve vašem klonovaném repo.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Pokud vaše verze clusteru není 6.0.* pak přejděte do příslušného adresáře ve stabilní složce.
    >   

5. Spusťte následující příkaz autorest a vygenerujte kód klienta java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Níže je uveden příklad prokazující použití autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Následující příkaz ``servicefabric.json`` přebírá soubor specifikace jako vstup a ``java-rest-api-     code`` generuje kód klienta ``servicefabricrest`` java ve složce a obklopuje kód v oboru názvů. Po tomto kroku byste ``models``našli ``implementation`` dvě ``ServiceFabricClientAPIs.java`` složky a dva soubory a ``package-info.java`` vygenerovali ve ``java-rest-api-code`` složce.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Zahrnout a použít generovaného klienta v projektu

1. Přidejte vygenerovaný kód odpovídajícím způsobem do projektu. Doporučujeme vytvořit knihovnu pomocí generovaného kódu a zahrnout tuto knihovnu do projektu.
2. Pokud vytváříte knihovnu, zahrňte do projektu knihovny následující závislost. Pokud se řídíte jiným přístupem, zahrňte závislost odpovídajícím způsobem.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Pokud například používáte systém sestavení Maven, ``pom.xml`` zahrňte do souboru následující:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Vytvořte restclient pomocí následujícího kódu:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Použijte objekt klienta a proveďte příslušná volání podle potřeby. Zde jsou některé příklady, které ukazují použití objektu klienta. Předpokládáme, že balíček aplikace je sestaven a odeslán do úložiště obrázků před použitím níže uvedené rozhraní API.
    * Zřízení žádosti
    
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
Pro každé API najdete čtyři přetížení implementace. Pokud existují volitelné parametry, pak byste našli další čtyři varianty včetně těchto volitelných parametrů. Zvažte například ``removeReplica``rozhraní API .
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Toto je synchronní varianta volání rozhraní API removeReplica.
 2. **public ServiceFuture\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean\<forceRemove, Long timeout, final ServiceCallback Void> serviceCallback)**
    * Tuto variantu volání rozhraní API lze použít, pokud chcete použít budoucí asynchronní programování a použít zpětná volání
 3. **veřejné pozorovatelné\<void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId)**
    * Tuto variantu volání rozhraní API lze použít, pokud chcete použít reaktivní asynchronní programování
 4. **veřejné Observable\<\<ServiceResponse Void>> removeReplicaWithServiceResponseAsync(String nodeName, UUID partitionId, String replicaId)**
    * Tuto variantu volání rozhraní API lze použít, pokud chcete použít reaktivní asynchronní programování a vypořádat se s odpovědí raw odpočinku

## <a name="next-steps"></a>Další kroky
* Další informace o [service fabric REST API](https://docs.microsoft.com/rest/api/servicefabric/)

