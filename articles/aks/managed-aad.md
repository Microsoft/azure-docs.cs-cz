---
title: Použití Azure AD ve službě Azure Kubernetes
description: Naučte se používat Azure AD ve službě Azure Kubernetes Service (AKS).
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 896986775f0132ef08b17bdfefc00e5e06cf3d9f
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448125"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integrace Azure Active Directory spravovaná v AKS

Integrace služby Azure AD spravovaná pomocí AKS je navržená tak, aby zjednodušila integrační prostředí Azure AD, kde se předtím vyžadovalo vytvoření klientské aplikace, serverové aplikace a požadovaného tenanta Azure AD pro udělení oprávnění ke čtení adresáře. V nové verzi poskytovatel prostředků AKS spravuje klientské a serverové aplikace za vás.

## <a name="azure-ad-authentication-overview"></a>Přehled ověřování Azure AD

Správci clusteru můžou nakonfigurovat řízení přístupu na základě role (RBAC) Kubernetes na základě identity uživatele nebo členství ve skupině adresáře. Ověřování Azure AD je k dispozici pro clustery AKS s OpenID Connect. OpenID Connect je vrstva identity postavená nad protokolem OAuth 2,0. Další informace o OpenID připojení najdete v dokumentaci k [otevřenému ID Connect][open-id-connect].

Přečtěte si další informace o postupu integrace AAD v [dokumentaci k Azure Active Directory v konceptech integrace](concepts-identity.md#azure-active-directory-integration).

## <a name="region-availability"></a>Dostupnost v oblastech

Integrace Azure Active Directory spravovaná v AKS je dostupná ve veřejných oblastech, kde [se podporuje AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

* Azure Government se momentálně nepodporuje.
* Azure Čína 21Vianet se momentálně nepodporuje.

## <a name="limitations"></a>Omezení 

* Integraci služby Azure AD spravovanou v AKS nejde zakázat.
* pro integraci AAD spravované v AKS se nepodporují clustery s podporou non RBAC.
* Změna tenanta Azure AD přidruženého k integraci AAD spravovaného přes AKS se nepodporuje.

> [!IMPORTANT]
> Funkce AKS ve verzi Preview jsou k dispozici na samoobslužné službě, na základě souhlasu. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepších úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace najdete v následujících článcích podpory: 
> - [Zásady podpory AKS](support-policies.md) 
> - [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="prerequisites"></a>Předpoklady

* Azure CLI verze 2.9.0 nebo novější
* Kubectl s minimální verzí [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

> [!Important]
> Je nutné použít Kubectl s minimální verzí 1,18.

K instalaci kubectl použijte následující příkazy:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
```

[Tyto pokyny](https://kubernetes.io/docs/tasks/tools/install-kubectl/) použijte pro jiné operační systémy.

```azurecli-interactive 
az feature register --name AAD-V2 --namespace Microsoft.ContainerService    
``` 

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) : 

```azurecli-interactive 
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"    
``` 

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) :    

```azurecli-interactive 
az provider register --namespace Microsoft.ContainerService 
``` 


## <a name="before-you-begin"></a>Než začnete

Pro váš cluster potřebujete skupinu Azure AD. Tato skupina je potřeba, jako skupina správců, aby cluster udělil oprávnění správce clusteru. Můžete použít existující skupinu Azure AD nebo vytvořit novou. Poznamenejte si ID objektu vaší skupiny Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Pokud chcete pro správce clusteru vytvořit novou skupinu Azure AD, použijte následující příkaz:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Vytvoření clusteru AKS s povolenou službou Azure AD

Pomocí následujících příkazů rozhraní příkazového řádku vytvořte cluster AKS.

Vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Vytvoření clusteru AKS a povolení přístupu pro správu pro skupinu Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Úspěšné vytvoření clusteru Azure AD spravovaného AKS má následující část v těle odpovědi.
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Jakmile je cluster vytvořený, můžete k němu začít přistupovat.

## <a name="access-an-azure-ad-enabled-cluster"></a>Přístup ke clusteru s podporou Azure AD

K provedení následujících kroků budete potřebovat integrovanou roli [uživatele clusteru Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Získat přihlašovací údaje uživatele pro přístup ke clusteru:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Pokud se chcete přihlásit, postupujte podle pokynů.

K zobrazení uzlů v clusteru použijte příkaz kubectl Get Nodes:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Nakonfigurujte [řízení přístupu na základě role v Azure (Azure RBAC)](./azure-ad-rbac.md) a nakonfigurujte další skupiny zabezpečení pro clustery.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Řešení potíží s přístupem k Azure AD

> [!Important]
> Následující kroky popisují normální ověřování skupiny služby Azure AD. Používejte je pouze v naléhavém případě.

Pokud jste trvale zablokovali aplikaci, která nemá přístup k platné skupině Azure AD s přístupem ke clusteru, můžete i nadále získat přihlašovací údaje správce pro přímý přístup ke clusteru.

K provedení těchto kroků budete potřebovat přístup k předdefinované roli [Správce clusteru služby Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgrade na integraci služby Azure AD spravované na AKS

Pokud váš cluster používá starší integraci služby Azure AD, můžete upgradovat na integraci služby Azure AD spravovanou v AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Úspěšná migrace clusteru Azure AD spravovaného AKS má následující část v těle odpovědi.

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Pokud chcete získat přístup ke clusteru, postupujte podle kroků uvedených [tady][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Neinteraktivní přihlášení pomocí kubelogin

Existují některé neinteraktivní scénáře, jako jsou kanály průběžné integrace, které nejsou aktuálně k dispozici v kubectl. Můžete použít [`kubelogin`](https://github.com/Azure/kubelogin) pro přístup ke clusteru pomocí neinteraktivního přihlašování instančního objektu.

## <a name="next-steps"></a>Další kroky

* Další informace o [integraci Azure RBAC pro autorizaci Kubernetes][azure-rbac-integration]
* Přečtěte si o [integraci Azure AD s KUBERNETES RBAC][azure-ad-rbac].
* Pomocí [kubelogin](https://github.com/Azure/kubelogin) můžete získat přístup k funkcím pro ověřování Azure, které nejsou dostupné v kubectl.
* Přečtěte si další informace o [konceptech identit AKS a Kubernetes][aks-concepts-identity].
* Pomocí [šablon Azure Resource Manager (ARM)][aks-arm-template] můžete vytvářet clustery s podporou Azure AD s povolenou správou AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
