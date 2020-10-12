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
ms.openlocfilehash: 02689dba32c8cc91e4a4a4de4dee98bc990b4dd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050066"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Vytvoření instančního objektu s povolenou službou Azure ARC (verze Preview)

## <a name="overview"></a>Přehled

Je možné používat instanční objekty s přiřazením rolí s omezenými oprávněními pro připojování clusterů Kubernetes do Azure ARC. To je užitečné v kanálech průběžné integrace a průběžného nasazování (CI/CD), jako jsou Azure Pipelines a akce GitHubu.

Následující kroky poskytují návod k používání instančních objektů pro připojování clusterů Kubernetes do Azure ARC.

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

Po vytvoření nového instančního objektu přiřaďte roli "cluster Kubernetes-Azure ARC" do nově vytvořeného objektu zabezpečení. Toto je integrovaná role Azure s omezenými oprávněními, která umožňuje, aby objekt zabezpečení registroval jenom clustery do Azure. Objekt zabezpečení nemůže v rámci předplatného aktualizovat, odstranit ani upravovat žádné jiné clustery ani prostředky.

S ohledem na omezené možnosti můžou zákazníci snadno použít tento objekt zabezpečení k připojování více clusterů.

Oprávnění se můžou dál omezovat předáním příslušného `--scope` argumentu při přiřazení role. To zákazníkům umožňuje omezit registraci clusteru. V různých parametrech jsou podporovány následující scénáře `--scope` :

| Prostředek  | Argument `scope`| Účinek |
| ------------- | ------------- | ------------- |
| Předplatné | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Instanční objekt může zaregistrovat libovolný cluster v existující skupině prostředků v daném předplatném. |
| Resource Group | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Instanční objekt může registrovat __jenom__ clustery ve skupině prostředků. `myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
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
