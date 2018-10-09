---
title: Ověřování pomocí Azure Container Registry z Azure Container Instances
description: Zjistěte, jak poskytují přístup k obrázkům v váš privátní registr kontejnerů Azure Container Instances pomocí instančního objektu služby Azure Active Directory.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 32b1788d73e1c323d93b40b778bc64a1ba45c4ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855829"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Ověřování pomocí Azure Container Registry z Azure Container Instances

Instanční objekt Azure Active Directory (Azure AD) můžete použít k zajištění přístupu k vaší registry privátní kontejnerů ve službě Azure Container Registry.

V tomto článku zjistíte, jak vytvořit a nakonfigurovat instanční objekt Azure AD s *o přijetí změn* oprávnění k vašemu registru. Potom spusťte kontejner v Azure Container Instances (ACI), která si přetáhne jeho image z privátního registru, pomocí instančního objektu pro ověřování.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt služby

Instanční objekt služby byste měli použít pro ověření od ACI v **bezobslužného scénáře**, například aplikace nebo služby, které vytvářejí instance kontejnerů bezobslužné způsobem automatizované nebo jinak.

Například, pokud máte automatizovaný skript, který spouští každou noc a vytvoří [instance založené na úlohách kontejneru](../container-instances/container-instances-restart-policy.md) ke zpracování nějaká data, může použít instanční objekt služby s vyžádanou (čtenáři) oprávnění k ověření do registru. Potom můžete pravidelná Změna přihlašovacích údajů instančního objektu nebo odvolat přístup úplně bez ovlivnění dalších služeb a aplikací.

Instanční objekty by mělo být také použít, pokud registru [uživatele s rolí správce](container-registry-authentication.md#admin-account) je zakázaná.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Ověřování pomocí instančního objektu

Spusťte kontejner ve službě Azure Container Instances pomocí instančního objektu, zadejte své ID pro `--registry-username`a heslo pro `--registry-password`.

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

Předchozí ukázky skriptů pro rozhraní příkazového řádku Azure na Githubu najdete jako i verze pro prostředí Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Prostředí Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Další postup

Následující články obsahují další podrobnosti o práci s instančních objektů a ACR:

* [Ověřování služby Azure Container Registry pomocí instančních objektů](container-registry-auth-service-principal.md)
* [Ověřování pomocí Azure Container Registry z Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
