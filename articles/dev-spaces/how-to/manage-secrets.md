---
title: Správa tajných kódů při práci s vývojovým prostorem Azure
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 49f53683b2499e790414d139dcb0bc0833005647
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280013"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Správa tajných kódů při práci s vývojovým prostorem Azure

Vaše služby můžou vyžadovat určitá hesla, připojovací řetězce a další tajné kódy, například pro databáze nebo jiné zabezpečené služby Azure. Nastavením hodnot těchto tajných kódů v konfiguračních souborech je můžete zpřístupnit v kódu jako proměnné prostředí.  Ty je třeba zpracovat pečlivě, aby nedošlo k ohrožení zabezpečení tajných kódů.

Azure Dev Spaces poskytuje dvě Doporučené, zjednodušené možnosti pro ukládání tajných kódů v Helm grafech generovaných klientskými nástroji Azure Dev Spaces: v souboru Values. dev. yaml a přímo v azds. yaml. Nedoporučujeme ukládat tajné klíče do hodnot Values. yaml. Mimo dva přístupy k Helm grafům generovaných klientskými nástroji definovanými v tomto článku, pokud vytvoříte vlastní graf Helm, můžete použít graf Helm přímo ke správě a ukládání tajných klíčů.

## <a name="method-1-valuesdevyaml"></a>Metoda 1: hodnoty. dev. yaml
1. Otevřete VS Code s vaším projektem, který je povolený pro Azure Dev Spaces.
2. Přidejte soubor s názvem _Values. dev. yaml_ do stejné složky, ve které je existující _azds. yaml_ , a definujte svůj tajný klíč a hodnoty, jak je uvedeno v následujícím příkladu:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. yaml_ již odkazuje na hodnoty. Pokud existuje, soubor _vývoj. yaml_ existuje. Pokud dáváte přednost jinému názvu souboru, aktualizujte část Install. Values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Upravte kód služby tak, aby odkazoval na tyto tajné klíče jako proměnné prostředí, jako v následujícím příkladu:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Aktualizujte služby spuštěné v clusteru pomocí těchto změn. Na příkazovém řádku spusťte příkaz:

    ```
    azds up
    ```
 
6. Volitelné V příkazovém řádku ověřte, zda byly vytvořeny tyto tajné kódy:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Ujistěte se, že do souboru _. gitignore_ přidáte _hodnoty. dev. yaml_ , abyste se vyhnuli potvrzování tajných kódů ve správě zdrojového kódu.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metoda 2: vložená přímo do azds. yaml
1.  V _azds. yaml_nastavte tajné klíče v části YAML konfigurace/vývoj/instalace. I když můžete zadat tajné hodnoty přímo tam, nedoporučuje se, protože _azds. yaml_ se kontroluje do správy zdrojového kódu. Místo toho přidejte zástupné symboly pomocí syntaxe "$PLACEHOLDER".

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  Vytvořte soubor _. env_ ve stejné složce jako _azds. yaml_. Zadejte tajné kódy pomocí notace Standard Key = Value. Nepotvrzujte soubor _. env_ do správy zdrojových kódů. (Chcete-li vynechat ze správy zdrojového kódu v systémech správy verzí založených na Gitu, přidejte je do souboru _. gitignore_ .) Následující příklad ukazuje soubor _. env_ :

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Upravte zdrojový kód služby tak, aby odkazoval na tyto tajné kódy v kódu, jak je uvedeno v následujícím příkladu:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Aktualizujte služby spuštěné v clusteru pomocí těchto změn. Na příkazovém řádku spusťte příkaz:

    ```
    azds up
    ```

4.  volitelné Zobrazení tajných kódů z kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Další kroky

Pomocí těchto metod se teď můžete bezpečně připojit k databázi, službě Azure cache pro Redis nebo získat přístup k zabezpečeným službám Azure.
 
