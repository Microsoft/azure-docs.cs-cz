---
title: Vytvoření instančního objektu pro Kubernetes povoleného ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Vytvoření instančního objektu s povoleným připojením ARC Azure '
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.openlocfilehash: 610b355073473f0e492350753a523b7943666f13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121741"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Vytvoření instančního objektu pro Kubernetes povoleného ARC Azure

## <a name="overview"></a>Přehled

Clustery Kubernetes můžete připojit ke službě Azure ARC pomocí instančních objektů s přiřazením rolí s omezenými oprávněními. Tato funkce je užitečná v kanálech průběžné integrace a průběžného nasazování (CI/CD), jako jsou Azure Pipelines a akce GitHubu.

Projděte si následující postup, ve kterém se dozvíte, jak používat instanční objekty pro připojení clusterů Kubernetes ke službě Azure ARC.

## <a name="create-a-new-service-principal"></a>Vytvořit nový instanční objekt

Vytvořte nový instanční objekt s informativním názvem, který je pro vašeho tenanta Azure Active Directory jedinečný.

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

Přiřaďte roli "cluster Kubernetes-Azure ARC" do nově vytvořeného instančního objektu. Tato integrovaná role Azure s omezenými oprávněními povoluje pouze hlavnímu objektu registrovat clustery do Azure. Objekt zabezpečení s touto přiřazenou rolí nemůže v rámci předplatného aktualizovat, odstranit ani upravovat žádné další clustery ani prostředky.

S ohledem na omezené možnosti můžou zákazníci snadno použít tento objekt zabezpečení k připojování více clusterů.

Můžete omezit oprávnění a při přiřazení role předat příslušnému `--scope` argumentu. To správcům umožňuje omezit registraci clusteru na předplatné nebo obor skupiny prostředků. V různých parametrech jsou podporovány následující scénáře `--scope` :

| Prostředek  | Argument `scope`| Účinek |
| ------------- | ------------- | ------------- |
| Předplatné | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Instanční objekt může zaregistrovat cluster v jakékoli skupině prostředků v rámci tohoto předplatného. |
| Skupina prostředků | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Instanční objekt může registrovat __jenom__ clustery ve skupině prostředků `myGroup` . |

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

Odkazujte na nově vytvořený instanční objekt pomocí následujících příkazů:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Další kroky

Konfigurace clusteru se řídí [pomocí Azure Policy](./use-azure-policy.md).
