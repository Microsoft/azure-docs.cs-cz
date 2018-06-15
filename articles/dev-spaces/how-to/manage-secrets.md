---
title: Jak spravovat tajné klíče při práci s prostorem Dev Azure | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
ms.technology: azds-kubernetes
description: Rychlý vývoj Kubernetes s kontejnery a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
manager: douge
ms.openlocfilehash: b77d862f578ddc374dbb58117b4ea58eb973e5fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198309"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Jak spravovat tajné klíče při práci s prostorem Dev Azure

Vaše služby může vyžadovat určité hesla, připojovací řetězce a jiné tajné údaje, například pro databáze nebo jinými službami zabezpečení Azure. Nastavením hodnoty těchto tajných klíčů v konfiguračních souborech, můžete je zpřístupnit v kódu jako proměnné prostředí.  Toto musí být zpracován dát pozor, aby se zabránilo ohrožení zabezpečení těchto tajných klíčů.

Azure prostory vývojářů poskytuje dvě možnosti pro ukládání tajné klíče doporučené: v souboru values.dev.yaml a vložený přímo v azds.yaml. Nedoporučujeme ukládat tajné klíče v values.yaml.
 
## <a name="method-1-valuesdevyaml"></a>Metoda 1: values.dev.yaml
1. Otevřete VS Code projektu, který je povolený pro Azure Dev prostory.
2. Přidejte soubor s názvem _values.dev.yaml_ ve stejné složce jako existující _values.yaml_ a definovat tajného klíče a hodnoty, jako v následujícím příkladu:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Aktualizace _azds.yaml_ říct prostory Dev Azure do nové _values.dev.yaml_ souboru. Chcete-li to provést, přidejte tuto konfiguraci v oddílu configurations.develop.container:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. Upravte kódu služby k odkazování na těchto tajných klíčů jako proměnné prostředí, jako v následujícím příkladu:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Aktualizace se služby spuštěné v clusteru se tyto změny. Na příkazovém řádku spusťte příkaz:

    ```
    azds up
    ```
 
6. (Volitelné) Z příkazového řádku zkontrolujte, zda byly vytvořeny těchto tajných klíčů:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Ujistěte se, že přidáte _values.dev.yaml_ k _.gitignore_ souboru, aby se zabránilo potvrzení tajných klíčů ve správě zdrojového kódu.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metoda 2: Vložený přímo v azds.yaml
1.  V _azds.yaml_, nastavte tajných klíčů v části yaml část konfigurace nebo vývoj nebo instalace. I když můžete zadat hodnoty tajného klíče přímo zde není doporučeno, protože _azds.yaml_ vrácení se změnami do správy zdrojového kódu. Místo toho přidejte zástupné symboly pomocí syntaxe "$PLACEHOLDER".

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
     
2.  Vytvoření _.env_ soubor ve stejné složce jako _azds.yaml_. Zadejte tajné klíče pomocí standardní klíč = hodnota zápis. Nemáte potvrdit _.env_ souboru do správy zdrojového kódu. (Pokud chcete vynechat od správy zdrojového kódu v systémech řízení na základě git verze, přidejte ho do _.gitignore_ souboru.) Následující příklad ukazuje _.env_ souboru:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  Upravte vašeho zdrojového kódu služby tak, aby odkazovaly těchto tajných klíčů v kódu, jako v následujícím příkladu:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Aktualizace se služby spuštěné v clusteru se tyto změny. Na příkazovém řádku spusťte příkaz:

    ```
    azds up
    ```

4.  (volitelné) Zobrazení tajné klíče z kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Další postup

Pomocí těchto metod můžete můžete teď zabezpečené připojení k databázi, mezipaměti Redis nebo služby Azure zabezpečený přístup.
 