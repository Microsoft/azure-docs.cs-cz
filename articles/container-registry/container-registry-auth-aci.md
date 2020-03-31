---
title: Přístup z instancí kontejneru
description: Zjistěte, jak poskytnout přístup k bitovým kopiím v registru privátního kontejneru z instance kontejneru Azure pomocí instancí služby Azure Active Directory.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456516"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Ověření pomocí registru kontejnerů Azure z instancí kontejnerů Azure

K poskytování přístupu k privátním registrům kontejnerů v registru kontejnerů Azure můžete použít instanční objekt služby Azure Active Directory (Azure AD).

V tomto článku se naučíte vytvořit a nakonfigurovat instanční objekt služby Azure AD s oprávněními *k vyžádat* do registru. Potom spustíte kontejner v Azure Container Instances (ACI), který vytáhne jeho image z vašeho privátního registru, pomocí instančního objektu pro ověřování.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt

Měli byste použít instanční objekt pro ověřování z ACI v **bezhlavých scénářích**, například v aplikacích nebo službách, které vytvářejí instance kontejneru automatizovaným nebo jinak bezobslužným způsobem.

Například pokud máte automatizovaný skript, který běží každou noc a vytvoří [instanci kontejneru na základě úloh](../container-instances/container-instances-restart-policy.md) ke zpracování některých dat, můžete použít instanční objekt s oprávněními pouze pro vyžádat k ověření v registru. Potom můžete otočit pověření instančního objektu nebo zcela odvolat jeho přístup bez ovlivnění jiných služeb a aplikací.

Instanční objekty by měly být také použity, když je [zakázán uživatel správce](container-registry-authentication.md#admin-account) registru.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Ověření pomocí instančního objektu

Chcete-li spustit kontejner v instanci kontejneru Azure `--registry-username`pomocí instancí `--registry-password`služby, zadejte jeho ID pro a jeho heslo pro .

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

Předchozí ukázkové skripty pro Azure CLI najdete na GitHubu a také verze pro Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Další kroky

Následující články obsahují další podrobnosti o práci s instančními objekty a ACR:

* [Ověřování registru kontejnerů Azure pomocí instančních objektů](container-registry-auth-service-principal.md)
* [Ověření pomocí registru kontejnerů Azure ze služby Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
