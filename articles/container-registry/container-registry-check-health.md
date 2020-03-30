---
title: Kontrola stavu registru
description: Zjistěte, jak spustit příkaz rychlé diagnostiky k identifikaci běžných problémů při používání registru kontejnerů Azure, včetně místní konfigurace Dockeru a připojení k registru.
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456406"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Kontrola stavu registru kontejnerů Azure

Při použití registru kontejneru Azure může občas dojít k problémům. Například nemusí být možné vytáhnout image kontejneru z důvodu problému s Dockerem v místním prostředí. Nebo problém se sítí může bránit připojení k registru. 

Jako první diagnostický krok spusťte příkaz [az acr check-health,][az-acr-check-health] abyste získali informace o stavu prostředí a volitelně přístup k cílovému registru. Tento příkaz je k dispozici v Azure CLI verze 2.0.67 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Spustit az acr check-zdraví

Následující příklady ukazují různé způsoby spuštění příkazu. `az acr check-health`

> [!NOTE]
> Pokud spustíte příkaz v Azure Cloud Shell, místní prostředí není zaškrtnuto. Můžete však zkontrolovat přístup k cílovému registru.

### <a name="check-the-environment-only"></a>Zkontrolujte pouze prostředí

Chcete-li zkontrolovat místní konfiguraci klienta Docker, CLI a helmu, spusťte příkaz bez dalších parametrů:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Kontrola prostředí a cílového registru

Chcete-li zkontrolovat přístup k registru a provést kontroly místního prostředí, předajte název cílového registru. Například:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Zasílání zpráv o chybách

Příkaz protokoluje informace do standardního výstupu. Pokud je zjištěn problém, poskytuje kód chyby a popis. Další informace o kódech a možných řešeních naleznete v [odkazu na chybu](container-registry-health-error-reference.md).

Ve výchozím nastavení se příkaz zastaví vždy, když najde chybu. Příkaz můžete také spustit tak, aby poskytoval výstup pro všechny kontroly stavu, a to i v případě, že jsou nalezeny chyby. Přidejte `--ignore-errors` parametr, jak je znázorněno v následujících příkladech:

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



## <a name="next-steps"></a>Další kroky

Podrobnosti o kódech chyb vrácených příkazem [az acr check-health][az-acr-check-health] naleznete v [odkazu na chybu kontroly stavu](container-registry-health-error-reference.md).

Nejčastější dotazy a další známé problémy týkající se registru kontejnerů Azure najdete v [nejčastějších](container-registry-faq.md) dotazech.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
