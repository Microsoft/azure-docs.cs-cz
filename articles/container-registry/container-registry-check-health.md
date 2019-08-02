---
title: Podívejte se na stav registru v Azure Container Registry
description: Naučte se, jak spustit příkaz Rychlý diagnostiky k identifikaci běžných problémů při použití služby Azure Container Registry, včetně místní konfigurace Docker a připojení k registru.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3511655d220ee85ce6b5744612e5d6fddafbe877
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309738"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Zkontroluje stav služby Azure Container Registry.

Při použití služby Azure Container Registry může občas docházet k problémům. Například je možné, že nebudete moci načíst image kontejneru z důvodu problému s Docker ve vašem místním prostředí. Případně může dojít k potížím se sítí, takže se nebudete moci připojit k registru. 

Jako první krok diagnostiky spusťte příkaz [AZ ACR check-Health][az-acr-check-health] , který získá informace o stavu prostředí a volitelně přístup k cílovému registru. Tento příkaz je k dispozici v Azure CLI verze 2.0.67 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Spuštění AZ ACR check-Health

Následující příklady ukazují různé způsoby spuštění `az acr check-health` příkazu.

> [!NOTE]
> Pokud příkaz spustíte v Azure Cloud Shell, místní prostředí se nekontroluje. Můžete ale kontrolovat přístup k cílovému registru.

### <a name="check-the-environment-only"></a>Kontrolovat jenom prostředí

Chcete-li ověřit místní rozhraní Docker démona, verzi rozhraní příkazového řádku a konfiguraci klienta Helm, spusťte příkaz bez dalších parametrů:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Zkontrolování prostředí a cílového registru

Chcete-li zkontrolovat přístup k registru a provádět kontroly místního prostředí, předejte název cílového registru. Příklad:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Hlášení chyb

Příkaz protokoluje informace do standardního výstupu. Pokud se zjistí problém, zobrazí se kód chyby a popis. Další informace o kódech a možných řešeních naleznete v referenčních informacích o [chybě](container-registry-health-error-reference.md).

Ve výchozím nastavení se příkaz zastaví pokaždé, když najde chybu. Můžete také spustit příkaz tak, aby poskytoval výstup pro všechny kontroly stavu, i když jsou zjištěny chyby. `--ignore-errors` Přidejte parametr, jak je znázorněno v následujících příkladech:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Ukázkový výstup:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Další postup

Podrobnosti o kódech chyb vrácených příkazem [AZ ACR check-Health][az-acr-check-health] najdete v referenčních informacích o [chybě kontroly stavu](container-registry-health-error-reference.md).

Nejčastější dotazy [](container-registry-faq.md) a další známé problémy týkající se Azure Container Registry najdete v nejčastějších dotazech.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
