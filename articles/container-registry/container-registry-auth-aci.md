---
title: Přístup k Azure Container Registry z Container Instances
description: Naučte se, jak poskytnout přístup k obrázkům v privátním registru kontejneru z Azure Container Instances pomocí instančního objektu služby Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 53cdca72587dafd39abd3f13ad488dcb460bf622
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931675"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Ověřování pomocí Azure Container Registry z Azure Container Instances

K poskytnutí přístupu k registrům privátních kontejnerů v Azure Container Registry můžete použít instanční objekt služby Azure Active Directory (Azure AD).

V tomto článku se naučíte, jak vytvořit a nakonfigurovat instanční objekt služby Azure AD s oprávněními pro *vyžádání* obsahu pro váš registr. Pak spustíte kontejner v Azure Container Instances (ACI), který načte jeho image z privátního registru, a to pomocí instančního objektu pro ověřování.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt

Pro ověřování z ACI v případě neintegrovaných **scénářů**byste měli použít instanční objekt, například v aplikacích nebo službách, které vytvářejí instance kontejnerů automatizovaným nebo jiným způsobem.

Pokud máte například automatizovaný skript, který běží v noci, a vytvoří [instanci kontejneru založenou na úlohách](../container-instances/container-instances-restart-policy.md) pro zpracování některých dat, může použít instanční objekt s oprávněním pro vyžádání obsahu k ověření do registru. Pak můžete znovu otočit přihlašovací údaje instančního objektu nebo odvolat jeho úplný přístup, aniž by to ovlivnilo jiné služby a aplikace.

Instanční objekty by se měly používat i v případě, že je [uživatel správce](container-registry-authentication.md#admin-account) registru zakázaný.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Ověřování pomocí instančního objektu

Pokud chcete spustit kontejner v Azure Container Instances pomocí instančního objektu, zadejte jeho ID pro `--registry-username`a heslo pro `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Ukázkové skripty

Předchozí ukázkové skripty pro Azure CLI najdete na GitHubu a také ve verzích pro Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Další kroky

Následující články obsahují další podrobnosti o práci s instančními objekty a ACR:

* [Azure Container Registry ověřování pomocí instančních objektů](container-registry-auth-service-principal.md)
* [Ověřování pomocí Azure Container Registry ze služby Azure Kubernetes (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
