---
title: Vytvoření instančního objektu s povolenou službou Azure ARC (verze Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Vytvoření instančního objektu s povolenou službou Azure ARC '
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.openlocfilehash: f9f750980d8a8b5d8190ba0b399fe068f1dd99c7
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680795"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Vytvoření instančního objektu s povolenou službou Azure ARC (verze Preview)

## <a name="overview"></a>Přehled

Když se cluster připojí do Azure, agenti běžící v clusteru se musí ověřit, aby se Azure Resource Manager jako součást registrace. `connectedk8s`Rozšíření Azure CLI má automatizované vytváření instančního objektu. Může ale existovat několik scénářů, kdy automatizace CLI nefunguje:

* Vaše organizace obecně omezuje vytváření instančních objektů.
* Uživatel, který registruje cluster, nemá dostatečná oprávnění k vytvoření instančních objektů.

Místo toho vytvořte instanční objekt vzdáleně a pak předejte objekt zabezpečení rozhraní příkazového řádku Azure CLI.

## <a name="create-a-new-service-principal"></a>Vytvořit nový instanční objekt

Vytvořte nový instanční objekt s informativním názvem. Všimněte si, že tento název musí být pro vašeho tenanta Azure Active Directory jedinečný:

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Výkonem**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Přiřazení oprávnění

Po vytvoření nového instančního objektu přiřaďte roli Azure ARC pro Kubernetes k nově vytvořenému objektu zabezpečení. Toto je integrovaná role Azure s omezenými oprávněními, která umožňuje, aby objekt zabezpečení registroval jenom clustery do Azure. Objekt zabezpečení nemůže v rámci předplatného aktualizovat, odstranit ani upravovat žádné jiné clustery ani prostředky.

S ohledem na omezené možnosti můžou zákazníci snadno použít tento objekt zabezpečení k připojování více clusterů.

Oprávnění se můžou dál omezovat předáním příslušného `--scope` argumentu při přiřazení role. To zákazníkům umožňuje omezit registraci clusteru. V různých parametrech jsou podporovány následující scénáře `--scope` :

| Prostředek  | Argument `scope`| Účinek |
| ------------- | ------------- | ------------- |
| Předplatné | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Instanční objekt může zaregistrovat libovolný cluster v existující skupině prostředků v daném předplatném. |
| Skupina prostředků | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Instanční objekt může registrovat __jenom__ clustery ve skupině prostředků.`myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the apropriate scope
```

**Výkonem**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Použití instančního objektu s rozhraním příkazového řádku Azure

Odkaz na nově vytvořený instanční objekt:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Další kroky

* [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
