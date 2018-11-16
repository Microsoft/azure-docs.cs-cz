---
title: Správa tajných kódů při práci s prostorem vývoj Azure | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
ms.technology: azds-kubernetes
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 49e9a974dc01548c250f0b0a8b9b5afac5f286ff
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705493"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Správa tajných kódů při práci s prostorem vývoj Azure

Služby můžou vyžadovat určité hesla, připojovacích řetězců a dalších tajných kódů, například pro databáze nebo jiných služeb zabezpečení Azure. Nastavením hodnoty těchto tajných kódů v konfiguračních souborech, můžete zpřístupnit je ve vašem kódu jako proměnné prostředí.  Toto musí být zpracován opatrně, aby se zabránilo ohrožení zabezpečení tajné klíče.

Azure Dev prostory poskytují dvě možnosti pro ukládání tajných kódů doporučené: v souboru values.dev.yaml a vložené přímo v azds.yaml. Není doporučuje tajné kódy ukládat v values.yaml.
 
## <a name="method-1-valuesdevyaml"></a>Metoda 1: values.dev.yaml
1. Otevřít VS Code s projektem, který je povolený pro Azure Dev mezery.
2. Přidejte do ní soubor _values.dev.yaml_ ve stejné složce jako existující _values.yaml_ a definovat tajného klíče a hodnoty, jako v následujícím příkladu:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Aktualizace _azds.yaml_ říct prostory Azure Dev do nové _values.dev.yaml_ souboru. Chcete-li to provést, přidejte tuto konfiguraci v části configurations.develop.container:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. Upravte kód služby k odkazování na tyto tajné kódy jako proměnné prostředí, jako v následujícím příkladu:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Aktualizace služby spuštěné v clusteru s těmito změnami. Na příkazovém řádku spusťte příkaz:

    ```
    azds up
    ```
 
6. (Volitelné) Z příkazového řádku zkontrolujte, zda byly vytvořeny těchto tajných kódů:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Ujistěte se, že přidáte _values.dev.yaml_ k _.gitignore_ souboru potvrzování tajné kódy ve správě zdrojového kódu.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metoda 2: Vložený přímo v azds.yaml
1.  V _azds.yaml_, nastavte tajných kódů v rámci yaml část konfigurace/vývoj/instalace. I když můžete zadat hodnoty tajného klíče přímo, se nedoporučuje, protože _azds.yaml_ vrácení se změnami do správy zdrojového kódu. Místo toho přidejte zástupné symboly pomocí syntaxe "$PLACEHOLDER".

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT_DEV"
                host: "$REDIS_HOST_DEV"
                key: "$REDIS_KEY_DEV"
    ```
     
2.  Vytvoření _.env_ ve stejné složce jako soubor _azds.yaml_. Zadejte tajných kódů pomocí standardní klíč = hodnota notace. Není potvrzení _.env_ soubor do správy zdrojového kódu. (Pokud chcete vynechat ze správy zdrojových kódů v systémy správy verzí z gitu, přidejte ji tak _.gitignore_ souboru.) Následující příklad ukazuje _.env_ souboru:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  Upravte zdrojový kód služby tak, aby odkazovaly těchto tajných kódů v kódu, jako v následujícím příkladu:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Aktualizace služby spuštěné v clusteru s těmito změnami. Na příkazovém řádku spusťte příkaz:

    ```
    azds up
    ```

4.  (volitelné) Zobrazení tajné kódy z kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Další postup

S těmito metodami můžete teď bezpečně připojit k databázi, Redis cache nebo přístup k zabezpečení služby Azure.
 