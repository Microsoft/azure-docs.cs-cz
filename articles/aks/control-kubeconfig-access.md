---
title: Omezení přístupu ke službě kubeconfig ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak řídit přístup ke konfiguračnímu souboru Kubernetes (kubeconfig) pro správce clusteru a uživatele clusteru.
services: container-service
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 25c710cce2855d6af985d3f46082f47573bbc101
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259549"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Definování přístupu ke konfiguračnímu souboru Kubernetes ve službě Azure Kubernetes Service (AKS) pomocí ovládacích prvků přístupu založených na rolích Azure

Pomocí `kubectl` nástroje můžete pracovat s clustery Kubernetes. Azure CLI poskytuje snadný způsob, jak získat přístupová pověření a informace o `kubectl`konfiguraci pro připojení k clusterům AKS pomocí . Chcete-li omezit, kdo může získat informace o konfiguraci Kubernetes *(kubeconfig)* a omezit oprávnění, která pak mají, můžete použít ovládací prvky přístupu azure založené na rolích (RBAC).

Tento článek ukazuje, jak přiřadit role RBAC, které omezují, kdo může získat informace o konfiguraci pro cluster AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Tento článek také vyžaduje, abyste spouštěli Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Dostupná oprávnění rolí clusteru

Při interakci s clusterem AKS pomocí `kubectl` tohoto nástroje se používá konfigurační soubor, který definuje informace o připojení clusteru. Tento konfigurační soubor je obvykle uložen v *~/.kube/config*. V tomto *souboru kubeconfig* lze definovat více clusterů. Mezi clustery můžete přepínat pomocí příkazu [kubectl config use-context.][kubectl-config-use-context]

Příkaz [az aks get-credentials][az-aks-get-credentials] umožňuje získat přístupová pověření pro cluster AKS a sloučit je do souboru *kubeconfig.* Přístup k těmto přihlašovacím údajům můžete použít pomocí ovládacích prvků přístupu založených na rolích Azure (RBAC). Tyto role Azure RBAC umožňují definovat, kdo může načíst soubor *kubeconfig* a jaká oprávnění pak mají v rámci clusteru.

Dvě předdefinované role jsou:

* **Role správce clusteru Azure Kubernetes**  
  * Umožňuje přístup k volání rozhraní API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action.* Toto volání rozhraní API [obsahuje seznam pověření správce clusteru][api-cluster-admin].
  * Stáhne *kubeconfig* pro roli *clusterAdmin.*
* **Role uživatele clusteru služby Azure Kubernetes**
  * Umožňuje přístup k volání rozhraní API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action.* Toto volání rozhraní API [uvádí pověření uživatele clusteru][api-cluster-user].
  * Ke stažení *kubeconfig* pro *roli clusterUser.*

Tyto role RBAC lze použít pro uživatele nebo skupinu služby Azure Active Directory (AD).

> ! [POZNÁMKA] V clusterech, které používají Azure AD, mají uživatelé s rolí *clusterUser* prázdný soubor *kubeconfig,* který vyzve k přihlášení. Po přihlášení mají uživatelé přístup na základě nastavení uživatelů nebo skupin Azure AD. Uživatelé s rolí *clusterAdmin* mají přístup správce.
>
> Clustery, které nepoužívají Azure AD používat pouze role *clusteradmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Přiřazení oprávnění k rolím uživateli nebo skupině

Chcete-li přiřadit jednu z dostupných rolí, musíte získat ID prostředku clusteru AKS a ID uživatelského účtu nebo skupiny Azure AD. Následující ukázkové příkazy:

* Získejte ID prostředku clusteru pomocí příkazu [az aks show][az-aks-show] pro cluster s názvem *myAKSCluster* ve skupině prostředků *myResourceGroup.* Podle potřeby zadejte vlastní název clusteru a skupiny prostředků.
* K získání ID uživatele použijte příkazy [az účet show][az-account-show] a [az aad.][az-ad-user-show]
* Nakonec přiřaďte roli pomocí příkazu [vytvořit přiřazení role az.][az-role-assignment-create]

Následující příklad přiřadí *roli správce clusteru clusteru Azure Kubernetes* jednotlivým uživatelským účtům:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Pokud chcete přiřadit oprávnění ke skupině Azure AD, aktualizujte `--assignee` parametr zobrazený v předchozím příkladu s ID objektu pro *skupinu,* nikoli *uživatelem*. Chcete-li získat ID objektu pro skupinu, použijte příkaz [az ad group show.][az-ad-group-show] Následující příklad získá ID objektu pro skupinu Azure AD s názvem *appdev*:`az ad group show --group appdev --query objectId -o tsv`

Podle potřeby můžete změnit předchozí přiřazení na *roli uživatele clusteru.*

Následující ukázkový výstup ukazuje, že přiřazení role bylo úspěšně vytvořeno:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Získání a ověření informací o konfiguraci

S přiřazenými rolemi RBAC použijte příkaz [az aks get-credentials][az-aks-get-credentials] k získání definice *kubeconfig* pro váš cluster AKS. Následující příklad získá *--admin* pověření, které fungují správně, pokud uživatel i udělena *role správce clusteru*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Potom můžete použít příkaz [konfiguračního zobrazení kubectl][kubectl-config-view] k ověření, že *kontext* pro cluster ukazuje, že byly použity informace o konfiguraci správce:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Odebrání oprávnění role

Chcete-li odebrat přiřazení rolí, použijte příkaz [odstranění přiřazení role az.][az-role-assignment-delete] Zadejte ID účtu a ID prostředku clusteru, jak bylo získáno v předchozích příkazech. Pokud jste roli přiřadili spíše skupině než uživateli, zadejte pro parametr `--assignee` příslušné ID objektu skupiny, nikoli ID objektu účtu:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Další kroky

Chcete-li zvýšit zabezpečení při přístupu ke clusterům AKS, [integrujte ověřování služby Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
