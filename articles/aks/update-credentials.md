---
title: Resetovat přihlašovací údaje pro cluster Azure Kubernetes Service (AKS)
description: Zjistěte, jak aktualizovat nebo obnovit instanční objekt služby přihlašovací údaje pro cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/30/2019
ms.author: iainfou
ms.openlocfilehash: d880615d0d132403c935fe39e8478d7b3fc48dbe
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490069"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Aktualizace nebo otočit přihlašovací údaje pro instanční objekt služby ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení AKS clustery jsou vytvořeny pomocí objektu služby, který má čas vypršení platnosti jeden rok. Jak jste téměř datum vypršení platnosti můžete resetovat přihlašovací údaje pro instanční objekt služby rozšířit další dobu. Můžete také chtít aktualizovat nebo otočit, přihlašovací údaje jako součást zásady zabezpečení. Tento článek podrobně popisuje, jak aktualizovat tyto přihlašovací údaje pro AKS cluster.

## <a name="before-you-begin"></a>Před zahájením

Musí mít Azure CLI verze 2.0.56 nebo později nainstalována a nakonfigurována. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Zvolte aktualizovat nebo vytvořit instanční objekt

Pokud chcete aktualizovat přihlašovací údaje pro AKS cluster, můžete:

* aktualizace přihlašovacích údajů pro existující instanční objekt používané clusterem, nebo
* Vytvoření instančního objektu a aktualizujte cluster používat tyto nové přihlašovací údaje.

Pokud chcete vytvořit instanční objekt a potom aktualizovat AKS cluster, zbývající kroky v této části přeskočit a pokračovat [vytvoření instančního objektu](#create-a-service-principal). Pokud chcete aktualizovat přihlašovací údaje pro existující instanční objekt používané clusterem AKS, pokračujte kroky v této části.

### <a name="get-the-service-principal-id"></a>Získejte ID instančního objektu služby

Chcete-li aktualizovat přihlašovací údaje pro existující instanční objekt, získejte ID instančního objektu služby clusteru pomocí [az aks zobrazit] [ az-aks-show] příkazu. Následující příklad získá ID pro cluster s názvem *myAKSCluster* v *myResourceGroup* skupinu prostředků. ID instančního objektu služby je nastaven jako proměnné pro použití v další příkaz.

```azurecli-interactive
SP_ID=$(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Aktualizace přihlašovacích údajů instančního objektu služby

Nastavení proměnné, která obsahuje ID instančního objektu služby, nyní obnovit přihlašovací údaje pomocí [přihlašovacích údajů az ad sp reset][az-ad-sp-credential-reset]. Následující příklad umožňuje vygenerovat nový tajný kód zabezpečení pro instanční objekt služby platformy Azure. Tento nový zabezpečené tajný kód je také uložena jako proměnnou.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Teď pokračujte na [clusteru AKS aktualizace s novými pověřeními](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Pokud jste se rozhodli aktualizovat existující pověření instančního objektu služby v předchozí části, tento krok přeskočte. I nadále [clusteru AKS aktualizace s novými pověřeními](#update-aks-cluster-with-new-credentials).

Chcete-li vytvořit instanční objekt a potom aktualizovat cluster AKS pomocí těchto nových přihlašovacích údajů, použijte [az ad sp create-for-rbac] [ az-ad-sp-create] příkazu. V následujícím příkladu parametr `--skip-assignment` zakazuje jakékoli další přiřazení výchozích přiřazení:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu. Poznamenejte si sami `appId` a `password`. Tyto hodnoty se používají v dalším kroku.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Nyní definujte proměnné, služby Hlavní ID a tajný kód klienta pomocí výstupu z vlastní [az ad sp create-for-rbac] [ az-ad-sp-create] příkaz, jak je znázorněno v následujícím příkladu. *SP_ID* je vaše *appId*a *SP_SECRET* je vaše *heslo*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Aktualizace clusteru AKS pomocí nových přihlašovacích údajů

Bez ohledu na to, zda jste se rozhodli aktualizovat přihlašovací údaje pro existující instanční objekt nebo vytvořte instanční objekt služby, je nyní aktualizovat AKS cluster pomocí nových přihlašovacích údajů [az aks update-credentials] [ az-aks-update-credentials] příkazu. Proměnné pro *--instanční objekt služby* a *--tajný kód klienta* se používají:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Trvá ještě chvilku na přihlašovací údaje instančního objektu služby v AKS.

## <a name="next-steps"></a>Další postup

V tomto článku se aktualizoval instanční objekt pro samotný cluster AKS. Další informace o tom, jak spravovat identitu pro úlohy v rámci clusteru najdete v tématu [osvědčené postupy pro ověřování a autorizace ve službě AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
