---
title: Instanční objekt pro cluster Azure Kubernetes
description: Vytvoření a správa instančního objektu služby Azure Active Directory pro cluster Kubernetes v AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4dbb8b7abf6da77115d0e1d12621ec20ec60d174
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035196"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Instanční objekty se službou Azure Kubernetes Service (AKS)

Cluster AKS vyžaduje [instanční objekt služby Azure Active Directory][aad-service-principal] pro interakci s rozhraními API Azure. Instanční objekt je potřeba k dynamickému vytváření a správě prostředků, jako je například [Azure Load Balancer][azure-load-balancer-overview].

Tento článek ukazuje různé možnosti nastavení instančního objektu pro cluster Kubernetes ve službě AKS.

## <a name="before-you-begin"></a>Než začnete


Abyste mohli vytvořit instanční objekt služby Azure AD, musíte mít oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném. Pokud nemáte potřebná oprávnění, možná budete muset požádat správce služby Azure AD nebo předplatného o jejich přiřazení nebo vytvořit instanční objekt pro cluster Kubernetes předem.

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.27 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Vytvoření instančního objektu s clusterem AKS

Při nasazování clusteru AKS pomocí příkazu `az aks create` máte možnost automaticky vygenerovat instanční objekt.

V následujícím příkladu se vytvoří cluster AKS, a protože není zadaný existující instanční objekt, vytvoří se pro cluster nový instanční objekt. Pro dokončení této operace musí mít váš účet příslušná práva k vytvoření instančního objektu.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Použití existujícího instančního objektu

Pro použití s clusterem AKS je možné použít existující instanční objekt služby Azure AD nebo ho předem vytvořit. To je užitečné při nasazování clusteru z webu Azure Portal, kdy se vyžaduje zadání informací o instančním objektu. Při použití existujícího instančního objektu musí být tajný klíč klienta nakonfigurovaný jako heslo.

## <a name="pre-create-a-new-sp"></a>Vytvoření nového instančního objektu předem

Pokud chcete vytvořit instanční objekt pomocí Azure CLI, použijte příkaz [az ad sp create-for-rbac][az-ad-sp-create].

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup je podobný tomuto. Poznamenejte si hodnoty `appId` a `password`. Tyto hodnoty se používají při vytváření clusteru AKS.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Použití existujícího instančního objektu

Pokud chcete použít předem vytvořený instanční objekt, zadejte `appId` a `password` jako hodnoty argumentů příkazu `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Pokud nasazujete cluster AKS pomocí webu Azure Portal, zadejte ve formuláři konfigurace clusteru AKS hodnotu `appId` do pole **ID klienta instančního objektu** a hodnotu `password` do pole **Tajný kód klienta instančního objektu**.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Další aspekty

Při práci s instančními objekty služeb Azure AD a AKS mějte na paměti následující informace.

* Instanční objekt pro Kubernetes je součástí konfigurace clusteru. K nasazení clusteru ale nepoužívejte identitu.
* Každý instanční objekt je přidružený k aplikaci Azure AD. Instanční objekt pro cluster Kubernetes může být přidružený k jakémukoli platnému názvu aplikace Azure AD (například `https://www.contoso.org/example`). Adresa URL aplikace nemusí být skutečný koncový bod.
* Při zadávání **ID klienta** instančního objektu použijte hodnotu `appId` (jak je ukázáno v tomto článku) nebo odpovídající `name` instančního objektu (například `https://www.contoso.org/example`).
* Na hlavním virtuálním počítači a virtuálních počítačích uzlů v clusteru Kubernetes jsou pověření instančního objektu uložená v souboru `/etc/kubernetes/azure.json`.
* Pokud použijete příkaz `az aks create` k automatickému vygenerování instančního objektu, zapíší se přihlašovací údaje instančního objektu do souboru `~/.azure/aksServicePrincipal.json` na počítači, který jste ke spuštění příkazu použili.
* Při odstraňování clusteru AKS vytvořeného příkazem `az aks create` se instanční objekt, který se vytvořil automaticky, neodstraní. Pokud chcete tento instanční objekt odstranit, nejdřív získejte jeho ID pomocí příkazu [az ad app list][az-ad-app-list]. Následující příkaz se dotazuje na cluster nazvaný *myAKSCluster* a potom odstraní ID aplikace pomocí příkazu [az ad app delete][az-ad-app-delete]. Tyto názvy nahraďte vlastními hodnotami:

    ```azurecli-interactive
    az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
    az ad app delete --id <appId>
    ```

## <a name="next-steps"></a>Další kroky

Další informace o instančních objektech služby Azure Active Directory najdete v dokumentaci k aplikacím Azure AD.

> [!div class="nextstepaction"]
> [Aplikační a instanční objekty][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
