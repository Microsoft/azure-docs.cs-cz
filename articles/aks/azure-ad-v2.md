---
title: Použití Azure AD ve službě Azure Kubernetes
description: Naučte se používat Azure AD ve službě Azure Kubernetes Service (AKS).
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114645"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrace služby Azure AD ve službě Azure Kubernetes (Preview)

> [!Note]
> Stávající clustery AKS V1 s integrací AD neovlivní nové prostředí AKS v2.

Integrace Azure AD s AKS v2 je navržená tak, aby zjednodušila integraci Azure AD s prostředím AKS V1, kde si uživatelé museli vytvořit klientskou aplikaci, serverovou aplikaci a vyžadovala, aby tenant služby Azure AD udělil oprávnění ke čtení adresáře. V nové verzi poskytovatel prostředků AKS spravuje klientské a serverové aplikace za vás.

## <a name="limitations"></a>Omezení

* V současné době nemůžete upgradovat stávající cluster AKS V1 s povoleným Azure AD na prostředí v2.

> [!IMPORTANT]
> Funkce AKS ve verzi Preview jsou k dispozici na samoobslužné službě, na základě souhlasu. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepších úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace najdete v následujících článcích podpory:
>
> - [Zásady podpory AKS](support-policies.md)
> - [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="before-you-begin"></a>Před zahájením

Musíte mít nainstalované následující zdroje:

- Rozhraní příkazového řádku Azure, verze 2.2.0 nebo novější
- Rozšíření AKS-Preview 0.4.38
- Kubectl s minimální verzí [1,18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Pokud chcete nainstalovat nebo aktualizovat rozšíření AKS-Preview nebo novější, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

K instalaci kubectl použijte následující:

```azurecli
sudo az aks install-cli
kubectl version --client
```

[Tyto pokyny](https://kubernetes.io/docs/tasks/tools/install-kubectl/) použijte pro jiné operační systémy.

> [!CAUTION]
> Po registraci funkce v předplatném nemůžete tuto funkci v tuto chvíli zrušit. Pokud povolíte některé funkce verze Preview, můžete použít výchozí nastavení pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Místo toho použijte k testování funkcí ve verzi Preview samostatné předplatné a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Vytvoření clusteru AKS s povolenou službou Azure AD

Cluster AKS teď můžete vytvořit pomocí následujících příkazů rozhraní příkazového řádku.

Nejdřív vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Pak vytvořte cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Výše uvedený příkaz vytvoří cluster AKS se třemi uzly, ale uživatel, který cluster vytvořil, ve výchozím nastavení není členem skupiny, která má přístup k tomuto clusteru. Tento uživatel musí vytvořit skupinu Azure AD, přidat ji jako člena skupiny a pak aktualizovat cluster, jak je uvedeno níže. Postupujte podle [pokynů.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Po vytvoření skupiny a přidání sebe (a dalších) jako člena můžete cluster aktualizovat pomocí skupiny Azure AD pomocí následujícího příkazu.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Případně, pokud nejprve vytvoříte skupinu a přidáte členy, můžete skupinu Azure AD povolit při vytváření pomocí následujícího příkazu.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Úspěšné vytvoření clusteru Azure AD v2 má v těle odpovědi následující oddíl.
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

Cluster se vytvoří během několika minut.

## <a name="access-an-azure-ad-enabled-cluster"></a>Přístup ke clusteru s podporou Azure AD
Získání přihlašovacích údajů správce pro přístup ke clusteru:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Teď k zobrazení uzlů v clusteru použijte příkaz kubectl Get Nodes:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Získání přihlašovacích údajů uživatele pro přístup ke clusteru:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Pokud se chcete přihlásit, postupujte podle pokynů.

Dostanete se: **musíte se přihlásit k serveru (Neautorizováno)** .

Uživatel uvedený výše získá chybu, protože uživatel není součástí skupiny, která má přístup ke clusteru.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [Access Control založené na rolích Azure AD][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
