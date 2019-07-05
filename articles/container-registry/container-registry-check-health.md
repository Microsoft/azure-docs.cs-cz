---
title: Kontrola stavu registru ve službě Azure Container Registry
description: Zjistěte, jak spustit rychlé diagnostický příkaz k identifikaci běžné problémy při použití služby Azure container registry, včetně místní konfiguraci Dockeru a připojení k registru
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555095"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Kontrola stavu služby Azure container registry

Při použití služby Azure container registry, čas od času může dojít k potížím. Například nemusí být schopen stáhnout image kontejneru z důvodu problému s prostředím Docker v místním prostředí. Nebo potíže se sítí mohou by vám bránily v připojení k registru. 

Jako první krok diagnostiky spouštění [az acr-stav kontroly][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Spustit az acr-stav kontroly

Následující příklady znázorňují různé způsoby spuštění `az acr check-health` příkazu.

> [!NOTE]
> Pokud spustíte příkaz ve službě Azure Cloud Shell, není zaškrtnutá místním prostředí. Však můžete zkontrolovat přístup k registru cíl.

### <a name="check-the-environment-only"></a>Vrácení pouze prostředí

Zkontrolujte místní Docker démon, verze rozhraní příkazového řádku a konfiguraci klienta Helm, spusťte příkaz bez dalších parametrů:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Zkontrolujte prostředí a cílové registru

Zkontrolovat přístup do registru také provést kontroly místní prostředí, předejte název cílového registru. Příklad:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Odesílání sestav chyb

Příkaz protokoluje informace do standardního výstupu. Pokud se zjistí problém, poskytuje kód chyby a popis. Další informace o kódech a možná řešení, najdete v článku [referenci chyby](container-registry-health-error-reference.md).

Ve výchozím nastavení příkaz zastaví pokaždé, když dojde k chybě. Můžete použít také příkaz tak, že poskytuje výstup pro všechny kontroly stavu, i v případě chyby. Přidat `--ignore-errors` parametru, jak je znázorněno v následujícím příkladu:

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

Podrobnosti o kódů chyb vrácených nástrojem [az acr-stav kontroly][az-acr-check-health] naleznete [referenci chyby kontroly stavu](container-registry-health-error-reference.md).

Zobrazit [nejčastější dotazy k](container-registry-faq.md) pro nejčastější dotazy a dalších známých problémech o Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
