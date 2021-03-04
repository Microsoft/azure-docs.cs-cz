---
title: Správa služby Azure RBAC v Kubernetes z Azure
titleSuffix: Azure Kubernetes Service
description: Naučte se používat Azure RBAC pro autorizaci Kubernetes se službou Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 9d967884193ee228b67ab094059ce4e050f4c7ca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741238"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Použití Azure RBAC pro autorizaci Kubernetes (Preview)

Dnes už můžete využít [integrované ověřování mezi Azure Active Directory (Azure AD) a AKS](managed-aad.md). Pokud je tato integrace povolená, umožňuje zákazníkům používat pro uživatele, skupiny nebo instanční objekty Azure AD jako předměty v Kubernetes RBAC další [informace.](azure-ad-rbac.md)
Tato funkce uvolní, abyste nemuseli samostatně spravovat identity uživatelů a přihlašovací údaje pro Kubernetes. Pořád ale musíte nastavit a spravovat službu Azure RBAC a Kubernetes RBAC samostatně. Další podrobnosti o ověřování a autorizaci pomocí RBAC na AKS najdete [tady](concepts-identity.md).

Tento dokument popisuje nový přístup, který umožňuje jednotnou správu a řízení přístupu napříč prostředky Azure, AKS a Kubernetes prostředky.

## <a name="before-you-begin"></a>Než začnete

Možnost spravovat RBAC pro prostředky Kubernetes z Azure vám dává možnost spravovat RBAC pro prostředky clusteru, a to buď pomocí Azure, nebo nativních mechanismů Kubernetes. Pokud je povoleno, objekty zabezpečení Azure AD budou ověřovány výhradně pomocí Azure RBAC, zatímco pravidelné Kubernetes uživatele a účty služeb jsou exkluzivně ověřovány Kubernetes RBAC. Další podrobnosti o ověřování a autorizaci pomocí RBAC na AKS najdete [tady](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Požadavky 
- Ujistěte se, že máte Azure CLI verze 2.9.0 nebo novější.
- Ujistěte se, že máte `EnableAzureRBACPreview` povolený příznak funkce.
- Ujistěte se, že máte `aks-preview` nainstalovanou [příponu CLI][az-extension-add] v 0.4.55 nebo novější verzi.
- Ujistěte se, že máte nainstalovanou verzi [kubectl v 1.18.3 +][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-preview-feature"></a>`EnableAzureRBACPreview`Funkce Register Preview

Pokud chcete vytvořit cluster AKS, který používá Azure RBAC pro autorizaci Kubernetes, musíte `EnableAzureRBACPreview` u svého předplatného povolit příznak funkce.

Zaregistrujte `EnableAzureRBACPreview` příznak funkce pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření rozhraní příkazového řádku aks-preview

Pokud chcete vytvořit cluster AKS, který používá Azure RBAC, potřebujete rozšíření *AKS-Preview* CLI verze 0.4.55 nebo vyšší. Pomocí příkazu [AZ Extension Add][az-extension-add] nainstalujte rozšíření Azure CLI *AKS-Preview* , nebo pomocí příkazu [AZ Extension Update][az-extension-update] nainstalujte všechny dostupné aktualizace:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Omezení

- Vyžaduje [spravovanou integraci služby Azure AD](managed-aad.md).
- V rámci verze Preview nemůžete integrovat Azure RBAC pro autorizaci Kubernetes do existujících clusterů, ale budete mít k dispozici obecné dostupnosti (GA).
- Použijte [kubectl v 1.18.3 +][az-aks-install-cli].
- Pokud máte CRDs a vytváříte vlastní definice rolí, jediným způsobem, jak pokrýt CRDs dnes, je poskytnout `Microsoft.ContainerService/managedClusters/*/read` . AKS pracuje na poskytování podrobnějších oprávnění pro CRDs. Pro zbývající objekty můžete použít konkrétní skupiny rozhraní API, například: `Microsoft.ContainerService/apps/deployments/read` .
- Nové přiřazení rolí může trvat až 5 minut, než se rozšíří a aktualizuje autorizační Server.
- Vyžaduje, aby byl tenant Azure AD nakonfigurovaný pro ověřování stejný jako tenant u předplatného, které obsahuje cluster AKS. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Vytvoření nového clusteru pomocí Azure RBAC a spravované integrace služby Azure AD

Pomocí následujících příkazů rozhraní příkazového řádku vytvořte cluster AKS.

Vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Vytvořte cluster AKS s využitím spravované integrace služby Azure AD a Azure RBAC pro autorizaci Kubernetes.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Úspěšné vytvoření clusteru s integrací Azure AD a Azure RBAC pro autorizaci Kubernetes má v těle odpovědi následující oddíl:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Vytvoření přiřazení rolí pro uživatele, kteří budou mít přístup ke clusteru

AKS poskytuje následující čtyři předdefinované role:


| Role                                | Popis  |
|-------------------------------------|--------------|
| Čtečka RBAC služby Azure Kubernetes  | Povoluje přístup jen pro čtení k zobrazení většiny objektů v oboru názvů. Nepovoluje zobrazení rolí nebo vazeb rolí. Tato role nepovoluje zobrazení `Secrets` , protože čtení obsahu tajných kódů umožňuje přístup k přihlašovacím údajům ServiceAccount v oboru názvů, což by mohlo povolit přístup k rozhraní API jako libovolný ServiceAccount v oboru názvů (forma eskalace oprávnění).  |
| Zapisovač RBAC služby Azure Kubernetes | Povoluje přístup pro čtení a zápis většiny objektů v oboru názvů. Tato role nepovoluje prohlížení a úpravy rolí nebo vazeb rolí. Tato role však umožňuje přístup k `Secrets` luskům a jejich spuštění jako libovolných ServiceAccount v oboru názvů, takže se dá použít k získání úrovní přístupu rozhraní API všech ServiceAccount v oboru názvů. |
| Správce RBAC služby Azure Kubernetes  | Povoluje přístup správce, který má být udělen v rámci oboru názvů. Umožňuje přístup pro čtení a zápis většiny prostředků v oboru názvů (nebo oboru clusteru), včetně možnosti vytvářet role a vazby rolí v rámci oboru názvů. Tato role nepovoluje přístup pro zápis k kvótě prostředků nebo samotnému oboru názvů. |
| Správce clusteru RBAC služby Azure Kubernetes  | Umožňuje přístupu super uživatele k provedení jakékoli akce u libovolného prostředku. Poskytuje plnou kontrolu nad všemi prostředky v clusteru a ve všech oborech názvů. |


Přiřazení rolí s rozsahem do **celého clusteru AKS** můžete provést buď v okně Access Control (IAM) prostředku clusteru v Azure Portal nebo pomocí příkazů rozhraní příkazového řádku Azure CLI, jak je znázorněno níže:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

kde `<AAD-ENTITY-ID>` může být uživatelské jméno (například user@contoso.com ) nebo i ClientID objektu služby.

Můžete také vytvořit přiřazení rolí v oboru pro konkrétní **obor názvů** v rámci clusteru:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

V dnešní době je potřeba nakonfigurovat přiřazení rolí vymezená obory názvů prostřednictvím rozhraní příkazového řádku Azure CLI.


### <a name="create-custom-roles-definitions"></a>Vytvořit definice vlastních rolí

Volitelně se můžete rozhodnout vytvořit vlastní definici role a pak ji přiřadit výše.

Níže je uveden příklad definice role, který uživateli umožňuje číst jenom nasazení a nic jiného. Úplný seznam možných akcí najdete [tady](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


Zkopírujte níže JSON do souboru s názvem `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

Nahraďte `<YOUR SUBSCRIPTION ID>` ID z předplatného, které můžete získat spuštěním:

```azurecli-interactive
az account show --query id -o tsv
```


Nyní můžeme vytvořit definici role spuštěním následujícího příkazu ze složky, kam jste uložili `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Teď, když máte definici role, můžete ji přiřadit uživateli nebo jiné identitě, a to spuštěním:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Použití Azure RBAC pro autorizaci Kubernetes s `kubectl`

> [!NOTE]
> Zajistěte, abyste měli nejnovější kubectl spuštěním následujícího příkazu:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Je možné, že ho budete muset spustit s `sudo` oprávněními. 

Teď máte přiřazenou požadovanou roli a oprávnění. Můžete začít volat rozhraní Kubernetes API, například z `kubectl` .

Pro tento účel nejdřív načtěte kubeconfig clusteru pomocí příkazu níže:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> K provedení výše uvedeného kroku budete potřebovat integrovanou roli [uživatele clusteru Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Teď můžete kubectl použít například k vypsání uzlů v clusteru. Když ho spustíte poprvé, budete se muset přihlásit a následné příkazy budou používat příslušný přístupový token.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Použití Azure RBAC pro autorizaci Kubernetes s `kubelogin`

K odblokování dalších scénářů, jako jsou neinteraktivní přihlášení, starší `kubectl` verze nebo použití jednotného přihlašování napříč několika clustery bez nutnosti přihlašovat se k novému clusteru, jste udělili, že token je stále platný, AKS vytvořil modul plug-in exec [`kubelogin`](https://github.com/Azure/kubelogin) .

Můžete ji použít spuštěním:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Poprvé se budete muset přihlásit interaktivně jako při běžném kubectl, ale pak už nebudete muset ani pro nové clustery Azure AD (Pokud je váš token stále platný).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Vyčištění

### <a name="clean-role-assignment"></a>Vyčistit přiřazení role

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Zkopírujte ID nebo ID ze všech přiřazení, která jste provedli, a potom.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Vyčištění definice role

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Odstranit cluster a skupinu prostředků

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o ověřování AKS, autorizaci, Kubernetes RBAC a Azure [RBAC.](concepts-identity.md)
- Přečtěte si další informace o Azure RBAC [tady](../role-based-access-control/overview.md).
- Přečtěte si další informace o všech akcích, které můžete použít k podrobnému definování vlastních rolí Azure [pro autorizaci](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)Kubernetes.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
