---
title: Obnovení přihlašovacích údajů pro cluster služby Azure Kubernetes Service (AKS)
description: Zjistěte, jak aktualizovat nebo resetovat instanční objekt nebo přihlašovací údaje aplikace AAD pro cluster služby Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: b7d652be3733cb130a3973909de59489047efe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475540"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aktualizace nebo otočení přihlašovacích údajů pro službu Azure Kubernetes Service (AKS)

Ve výchozím nastavení jsou clustery AKS vytvořeny s instančním objektem, který má dobu vypršení platnosti jeden rok. Pokud se blížíte datu vypršení platnosti, můžete obnovit pověření a prodloužit instanční objekt o další časové období. Pověření můžete také aktualizovat nebo otočit jako součást definované zásady zabezpečení. Tento článek podrobně popisuje, jak aktualizovat tato pověření pro cluster AKS.

Je možné, že jste také [integrovali cluster AKS se službou Azure Active Directory][aad-integration]a použili ho jako poskytovatele ověřování pro váš cluster. V takovém případě budete mít vytvořené další 2 identity pro váš cluster, aplikaci AAD Server a klientskou aplikaci AAD, můžete také obnovit tato pověření. 

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.65 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aktualizace nebo vytvoření nového objektu zabezpečení služeb pro cluster AKS

Pokud chcete aktualizovat pověření pro cluster AKS, můžete zvolit:

* aktualizovat pověření pro existující instanční objekt používaný clusterem nebo
* vytvořte instanční objekt a aktualizujte cluster tak, aby používal tato nová pověření.

### <a name="reset-existing-service-principal-credential"></a>Obnovit existující pověření hlavního povinného servisu

Chcete-li aktualizovat pověření pro existující instanční objekt, získejte ID instančního objektu clusteru pomocí příkazu [az aks show.][az-aks-show] Následující příklad získá ID pro cluster s názvem *myAKSCluster* ve skupině prostředků *myResourceGroup.* ID instančního objektu je nastaveno jako proměnná s názvem *SP_ID* pro použití v dalším příkazu.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

S proměnnou sadou, která obsahuje ID instančního objektu, nyní obnovte pověření pomocí [resetování pověření az ad sp][az-ad-sp-credential-reset]. Následující příklad umožňuje platformě Azure generovat nový zabezpečený tajný klíč pro instanční objekt. Tento nový tajný klíč zabezpečení je také uložen jako proměnná.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Nyní pokračujte v [aktualizaci clusteru AKS s novými pověřeními zaregistrovaný chod služby](#update-aks-cluster-with-new-service-principal-credentials). Tento krok je nezbytné pro změny instančního objektu k zamyšlení v clusteru AKS.

### <a name="create-a-new-service-principal"></a>Vytvořit nový instanční objekt

Pokud jste se rozhodli aktualizovat existující pověření instančního objektu v předchozí části, tento krok přeskočte. Pokračujte v [aktualizaci clusteru AKS s novými pověřeními zaregistrovaný chod služby](#update-aks-cluster-with-new-service-principal-credentials).

Chcete-li vytvořit instanční objekt a potom aktualizovat cluster AKS tak, aby používal tato nová pověření, použijte příkaz [az ad ad sp create-for-rbac.][az-ad-sp-create] V následujícím příkladu parametr `--skip-assignment` zakazuje jakékoli další přiřazení výchozích přiřazení:

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

Nyní definujte proměnné pro ID instančního objektu a tajný klíč klienta pomocí výstupu z vlastního [příkazu az ad ad sp create-for-rbac,][az-ad-sp-create] jak je znázorněno v následujícím příkladu. SP_ID *SP_ID* je *vaše appId*a *SP_SECRET* je vaše *heslo*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Nyní pokračujte v [aktualizaci clusteru AKS s novými pověřeními zaregistrovaný chod služby](#update-aks-cluster-with-new-service-principal-credentials). Tento krok je nezbytné pro změny instančního objektu k zamyšlení v clusteru AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aktualizace clusteru AKS s novými pověřeními zaregistrovaný chod service

Bez ohledu na to, zda jste se rozhodli aktualizovat pověření pro existující instanční objekt nebo vytvořit instanční objekt, nyní aktualizujete cluster AKS novými pověřeními pomocí příkazu [az aks update-credentials.][az-aks-update-credentials] Proměnné pro *--service-in a* *--client-secret* se používají:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Trvá několik okamžiků pro instanční pověření, které mají být aktualizovány v AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aktualizace clusteru AKS s novými přihlašovacími údaji aplikace AAD

Nové aplikace serveru AAD a klienta můžete vytvořit podle [kroků integrace ad][create-aad-app]. Nebo obnovte existující aplikace AAD [stejnou metodou jako pro obnovení instančního objektu](#reset-existing-service-principal-credential). Poté stačí aktualizovat pověření aplikace AAD clusteru pomocí [stejného příkazu az aks update-credentials,][az-aks-update-credentials] ale pomocí proměnných *--reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Další kroky

V tomto článku byly aktualizovány instanční objekt pro samotný cluster AKS a aplikace integrace AAD. Další informace o tom, jak spravovat identitu pro úlohy v rámci clusteru, naleznete [v tématu Doporučené postupy pro ověřování a autorizaci v AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
