---
title: Správa tajných kódů při práci s vývojovým prostorem Azure
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790171"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Správa tajných kódů při práci s vývojovým prostorem Azure

Vaše služby můžou vyžadovat určitá hesla, připojovací řetězce a další tajné kódy, například pro databáze nebo jiné zabezpečené služby Azure. Nastavením hodnot těchto tajných kódů v konfiguračních souborech je můžete zpřístupnit v kódu jako proměnné prostředí.  Ty je třeba zpracovat pečlivě, aby nedošlo k ohrožení zabezpečení tajných kódů.

Azure Dev Spaces poskytuje dvě Doporučené, zjednodušené možnosti pro ukládání tajných kódů v Helm grafech generovaných nástroji Azure Dev Spaces klienta: v souboru `values.dev.yaml` a přímo v `azds.yaml`. Nedoporučujeme ukládat tajné klíče do `values.yaml`. Mimo dva přístupy k Helm grafům generovaných klientskými nástroji definovanými v tomto článku, pokud vytvoříte vlastní graf Helm, můžete použít graf Helm přímo ke správě a ukládání tajných klíčů.

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
 
 
## <a name="method-2-azdsyaml"></a>Metoda 2: azds. yaml
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

## <a name="passing-secrets-as-build-arguments"></a>Předávání tajných klíčů jako argumentů sestavení

Předchozí části ukázaly, jak předat tajné klíče k použití v době běhu kontejneru. Můžete také předat tajný klíč v době sestavení kontejneru, jako je například heslo pro privátní NuGet, a to pomocí `azds.yaml`.

V `azds.yaml`nastavte tajné klíče času sestavení v souboru *configurations. vývoj. Build. args* pomocí syntaxe `<variable name>: ${secret.<secret name>.<secret key>}`. Například:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

Ve výše uvedeném příkladu je *mynugetsecret* stávající tajný klíč a *pattoken* je existující klíč.

>[!NOTE]
> Tajné názvy a klíče mohou obsahovat `.` znak. `.` při předávání tajných klíčů jako argumentů sestavení použijte `\`. Například pro předání tajného kódu s názvem *foo. bar* klíčem *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Kromě toho je možné tajné klíče vyhodnotit pomocí předpony a textu přípony. Například, `MYURL: eus-${secret.foo\.bar.token}-version1`. Tajné kódy, které jsou k dispozici v nadřazených a prarodičích, můžou být také předány jako argumenty sestavení.

Ve vašem souboru Dockerfile Použijte direktivu *arg* pro použití tajného klíče a pak použijte stejnou proměnnou později v souboru Dockerfile. Například:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Aktualizujte služby spuštěné v clusteru pomocí těchto změn. Na příkazovém řádku spusťte příkaz:

```
azds up
```

## <a name="next-steps"></a>Další kroky

Pomocí těchto metod se teď můžete bezpečně připojit k databázi, službě Azure cache pro Redis nebo získat přístup k zabezpečeným službám Azure.
 
