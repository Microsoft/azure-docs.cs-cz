---
title: Použití Azure AD ve službě Azure Kubernetes
description: Zjistěte, jak používat Azure AD ve službě Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114645"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrace Azure AD ve službě Azure Kubernetes (Preview)

> [!Note]
> Stávající clustery AKS v1 s integrací služby AD nejsou novým prostředím AKS v2 ovlivněny.

Integrace Azure AD s AKS v2 je navržena tak, aby zjednodušila integraci Azure AD s prostředím AKS v1, kde uživatelé museli vytvořit klientskou aplikaci, serverovou aplikaci a vyžadovali, aby tenant Azure AD uděloval oprávnění ke čtení adresářů. V nové verzi spravuje zprostředkovatel prostředků AKS klientské a serverové aplikace za vás.

## <a name="limitations"></a>Omezení

* V současné době nelze upgradovat existující Azure AD povolené AKS v1 clusteru na prostředí v2.

> [!IMPORTANT]
> Funkce AKS preview jsou k dispozici samoobslužné a opt-in. Náhledy jsou poskytovány "tak, jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS náhledy jsou částečně pokryty zákaznickou podporou na základě nejlepšího úsilí. Jako takové tyto funkce nejsou určeny pro produkční použití. Další informace naleznete v následujících článcích podpory:
>
> - [Zásady podpory AKS](support-policies.md)
> - [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="before-you-begin"></a>Než začnete

Musíte mít nainstalované následující prostředky:

- Nastavení příkazového příkazu Azure verze 2.2.0 nebo novější
- Aks-preview 0.4.38 rozšíření
- Kubectl s minimální verzí [1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Chcete-li nainstalovat nebo aktualizovat rozšíření aks-preview nebo novější, použijte následující příkazy Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Chcete-li nainstalovat kubectl, použijte následující:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Tento [návod](https://kubernetes.io/docs/tasks/tools/install-kubectl/) použijte pro jiné operační systémy.

> [!CAUTION]
> Po registraci funkce na předplatné, nelze aktuálně zrušit registraci této funkce. Pokud povolíte některé funkce náhledu, výchozí hodnoty mohou být použity pro všechny clustery AKS vytvořené později v předplatném. Nepovolujte funkce náhledu v produkčních předplatných. Místo toho použijte samostatné předplatné k testování funkcí náhledu a získání zpětné vazby.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **Registrovaný**. Stav registrace můžete zkontrolovat pomocí příkazu [az feature list:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazí jako registrovaný, `Microsoft.ContainerService` aktualizujte registraci poskytovatele prostředků pomocí příkazu [registrovat poskytovatele az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Vytvoření clusteru AKS s povoleným Azure AD

Nyní můžete vytvořit cluster AKS pomocí následujících příkazů příkazového příkazu.

Nejprve vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Potom vytvořte cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Výše uvedený příkaz vytvoří cluster AKS se třemi uzny, ale uživatel, který cluster vytvořil, ve výchozím nastavení není členem skupiny, která má přístup k tomuto clusteru. Tento uživatel potřebuje vytvořit skupinu Azure AD, přidat se jako člen skupiny a pak aktualizovat cluster, jak je znázorněno níže. Postupujte podle pokynů [zde](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Po vytvoření skupiny a přidání sebe (a dalších) jako člena můžete aktualizovat cluster se skupinou Azure AD pomocí následujícího příkazu

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Případně pokud nejprve vytvoříte skupinu a přidáte členy, můžete povolit skupinu Azure AD v době vytvoření pomocí následujícího příkazu,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Úspěšné vytvoření clusteru Azure AD v2 má následující část v těle odpovědi
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Cluster je vytvořen během několika minut.

## <a name="access-an-azure-ad-enabled-cluster"></a>Přístup ke clusteru s povoleným službou Azure AD
Získání pověření správce pro přístup ke clusteru:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Nyní použijte příkaz kubectl get uzly k zobrazení uzlů v clusteru:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Získání pověření uživatele pro přístup ke clusteru:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Postupujte podle pokynů pro přihlášení.

**Obdržíte: Musíte být přihlášeni k serveru (Neautorizováno)**

Výše uvedený uživatel získá chybu, protože uživatel není součástí skupiny, která má přístup ke clusteru.

## <a name="next-steps"></a>Další kroky

Další informace o [řízení přístupu na základě rolí Azure AD][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
