---
title: Správa tajných kódů při práci s Azure Dev Space
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Naučte se používat tajné kódy Kubernetes při spuštění nebo sestavení času při vývoji aplikací s Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438469"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Správa tajných kódů při práci s Azure Dev Space

Vaše služby mohou vyžadovat určitá hesla, připojovací řetězce a další tajné klíče, například pro databáze nebo jiné zabezpečené služby Azure. Nastavením hodnot těchto tajných kódů v konfiguračních souborech je můžete zpřístupnit v kódu jako proměnné prostředí.  Tyto konfigurační soubory musí být zpracovány s opatrností, aby nedošlo k ohrožení zabezpečení tajných kódů.

## <a name="storing-and-using-runtime-secrets"></a>Ukládání a používání tajných kódů runtime

Azure Dev Spaces poskytuje dvě doporučené, zjednodušené možnosti pro ukládání tajných kódů v `values.dev.yaml` grafech Helm generované `azds.yaml`nástroje klienta Azure Dev Spaces: v souboru a vložkem přímo v . Nedoporučuje se ukládat tajné `values.yaml`kódy do aplikace .

> [!NOTE]
> Následující přístupy ukazují, jak ukládat a používat tajné kódy pro grafy Helm generované nástrojem klienta. Pokud vytvoříte vlastní graf Helm, můžete použít graf Helm přímo ke správě a ukládání tajných kódů.

### <a name="using-valuesdevyaml"></a>Použití souboru values.dev.yaml

V projektu, který jste již připravili s `values.dev.yaml` Azure Dev Spaces, vytvořte soubor ve stejné složce, `azds.yaml` abyste definovali tajné klíče a hodnoty. Například:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Ověřte odkazy na `azds.yaml` soubory `values.dev.yaml` jako volitelné pomocí `?`rozhraní . Například:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Pokud máte další tajné soubory, můžete je přidat i zde.

Aktualizujte nebo ověřte, že vaše služba odkazuje na vaše tajné klíče jako proměnné prostředí. Například:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Spouštějte aktualizované `azds up`služby pomocí aplikace .

```console
azds up
```
 
Slouží `kubectl` k ověření, že byly vytvořeny vaše tajné klíče.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Není doporučeno ukládat tajné klíče v správě zdrojového kódu. Pokud používáte `values.dev.yaml` Git, `.gitignore` přidejte do souboru, abyste se vyhnuli potvrzení tajných kódů ve složce source.

### <a name="using-azdsyaml"></a>Použití azds.yaml

V projektu, který jste už připravili s Azure Dev Spaces, přidejte tajné klíče a `azds.yaml`hodnotu pomocí *syntaxe $PLACEHOLDER* v části *configurations.develop.install.set* v . Například:

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

> [!NOTE]
> Tajné hodnoty můžete zadávat přímo `azds.yaml`bez použití *syntaxe $PLACEHOLDER* v aplikaci . Tento přístup se však `azds.yaml` nedoporučuje, protože je uložen v správě zdrojového kódu.
     
Vytvořte `.env` soubor ve stejné `azds.yaml` složce jako pro definování *$PLACEHOLDER* hodnot. Například:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Není doporučeno ukládat tajné klíče v správě zdrojového kódu. Pokud používáte `.env` Git, `.gitignore` přidejte do souboru, abyste se vyhnuli potvrzení tajných kódů ve složce source.

Aktualizujte nebo ověřte, že vaše služba odkazuje na vaše tajné klíče jako proměnné prostředí. Například:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Spouštějte aktualizované `azds up`služby pomocí aplikace .

```console
azds up
```
 
Slouží `kubectl` k ověření, že byly vytvořeny vaše tajné klíče.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Použití tajných kódů jako argumentů sestavení

Předchozí část ukazuje, jak ukládat a používat tajné klíče pro použití v době běhu kontejneru. Můžete také použít libovolný tajný klíč v době sestavení kontejneru, `azds.yaml`jako je například heslo pro soukromé NuGet, pomocí .

V `azds.yaml`, nastavte tajné kódy času sestavení v *configurations.develop.build.args* pomocí `<variable name>: ${secret.<secret name>.<secret key>}` syntaxe. Například:

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

Ve výše uvedeném příkladu *mynugetsecret* je existující tajný klíč a *pattoken* je existující klíč.

>[!NOTE]
> Tajné názvy a `.` klíče mohou obsahovat znak. Slouží `\` k `.` útěku při předávání tajných kódů jako argumenty sestavení. Chcete-li například předat tajný klíč s názvem *foo.bar* s klíčem *tokenu*: `MYTOKEN: ${secret.foo\.bar.token}`. Kromě toho mohou být tajné klíče vyhodnocovány pomocí textu předpony a postfix. Například, `MYURL: eus-${secret.foo\.bar.token}-version1`. Také tajné kódy k dispozici v nadřazené a prarodiče prostory mohou být předány jako argumenty sestavení.

Ve vašem Dockerfile použijte direktivu *ARG* ke spotřebování tajného klíče a pak použijte stejnou proměnnou později v souboru Dockerfile. Například:

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

Pomocí těchto metod se teď můžete bezpečně připojit k databázi, azure cache pro Redis nebo přístup k zabezpečeným službám Azure.
 
