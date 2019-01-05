---
title: Omezit přístup k kubeconfig ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak řídit přístup ke konfigurační soubor Kubernetes (kubeconfig) pro správce clusteru a clusteru uživatele
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: iainfou
ms.openlocfilehash: ae45a268536f6a8fcb4ab27336a1281837cf5dc7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54045715"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Pomocí ovládacích prvků přístupu na základě rolí Azure definují přístup k konfigurační soubor Kubernetes ve službě Azure Kubernetes Service (AKS)

Můžete pracovat s clustery Kubernetes pomocí `kubectl` nástroj. Rozhraní příkazového řádku Azure poskytuje snadný způsob, jak získat přístup k přihlašovací údaje a informace o konfiguraci pro připojení k vaší AKS clusterů pomocí `kubectl`. Pro omezení, kdo může získat tuto konfiguraci Kubernetes (*kubeconfig*) informace a oprávnění, pak mají, můžete použít řízení přístupu Azure na základě rolí (RBAC).

Tento článek ukazuje, jak přiřadit role RBAC tohoto limitu, který můžete získat informace o konfiguraci pro AKS cluster.

## <a name="before-you-begin"></a>Před zahájením

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Tento článek také vyžaduje, že používáte Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Oprávnění role clusteru k dispozici

Když budete moct používat cluster AKS pomocí `kubectl` nástroj, konfigurační soubor se používá, který definuje informace o připojení ke clusteru. Tento konfigurační soubor je obvykle uložen ve *~/.kube/config*. Je možné definovat více clusterů v to *kubeconfig* souboru. Můžete přepínat mezi clustery pomocí [kontextu použití konfigurace kubectl] [ kubectl-config-use-context] příkazu.

[Az aks get-credentials] [ az-aks-get-credentials] příkaz slouží k získání přihlašovacích údajů pro přístup pro AKS cluster a sloučí je do *kubeconfig* souboru. Řízení přístupu Azure na základě rolí (RBAC) můžete řídit přístup k těmto přihlašovacím údajům. Pracovníci v těchto rolích Azure RBAC umožňují definovat, kdo může načíst *kubeconfig* soubor a co oprávnění, pak mají v rámci clusteru.

Jsou dvě předdefinované role:

* **Role Správce služby Azure Kubernetes Cluster**  
    * Umožňuje přístup ke *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* volání rozhraní API. Toto volání rozhraní API [obsahuje přihlašovací údaje Správce clusteru][api-cluster-admin].
    * Soubory ke stažení *kubeconfig* pro *clusterAdmin* role.
* **Role uživatele Cluster Azure Kubernetes Service**
    * Umožňuje přístup ke *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* volání rozhraní API. Toto volání rozhraní API [uvádí přihlašovacích údajů uživatele clusteru][api-cluster-user].
    * Soubory ke stažení *kubeconfig* pro *clusterUser* role.

## <a name="assign-role-permissions-to-a-user"></a>Přiřadit oprávnění role uživatele

Chcete-li uživateli přiřadit jednu z rolí Azure, získejte ID prostředku clusteru AKS a ID uživatelského účtu. Příkazy v následujícím příkladu proveďte následující kroky:

* Získá ID prostředku clusteru pomocí [az aks zobrazit] [ az-aks-show] příkazu pro cluster s názvem *myAKSCluster* v *myResourceGroup* Skupina prostředků. Zadejte vlastní název skupiny clusterů a prostředků podle potřeby.
* Používá [zobrazit účet az] [ az-account-show] a [az ad uživateli zobrazit] [ az-ad-user-show] příkazy získat vaše ID uživatele.
* Nakonec se přiřadí role pomocí [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu.

Následující příklad přiřadí *Role správce pro Cluster Azure Kubernetes Service*:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

Předchozí přiřazení můžete změnit *Role uživatele clusteru* podle potřeby.

Následující příklad výstupu ukazuje, že se že přiřazení role se úspěšně vytvořil:

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

## <a name="get-and-verify-the-configuration-information"></a>Získejte a ověřte informace o konfiguraci

S přiřazené role RBAC, použijte [az aks get-credentials] [ az-aks-get-credentials] příkazu získejte *kubeconfig* definice pro váš cluster AKS. Následující příklad získá *– správce* přihlašovací údaje, které fungovat správně, pokud mu byla udělena *roli Správce clusteru*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Pak můžete použít [zobrazení konfigurace kubectl] [ kubectl-config-view] příkazu ověřte, že *kontextu* pro cluster ukazuje, že byl použit informace o konfiguraci správce:

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

## <a name="remove-role-permissions"></a>Odebrat oprávnění role

Chcete-li odebrat přiřazení rolí, použijte [odstranit přiřazení role az] [ az-role-assignment-delete] příkazu. Zadejte ID účtu a ID prostředku clusteru získaný v předchozích příkazech:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Další postup

Pro lepší zabezpečení a přístup k clusteru AKS [integrace ověření služby Azure Active Directory][aad-integration].

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
[aad-integration]: aad-integration.md
