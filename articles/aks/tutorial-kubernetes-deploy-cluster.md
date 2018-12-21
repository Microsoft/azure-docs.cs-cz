---
title: Kurz Kubernetes v Azure – Nasazení clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) vytvoříte cluster a AKS a pomocí kubectl se připojíte se k hlavním uzlu Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7e5c78e1b30b311c6ce918453fe728ae86060dda
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720658"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Kurz: Nasaďte cluster Azure Kubernetes Service (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Díky službě AKS se můžete rychle vytvořit cluster Kubernetes připravený pro produkční prostředí. V tomto kurzu, který je třetí částí sedmidílné série, se nasadí cluster Kubernetes ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření instančního objektu pro interakce prostředků
> * Nasazení clusteru Kubernetes AKS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl pro připojení ke clusteru AKS

V dalších kurzech je aplikace Azure Vote nasadí do clusteru, škálovat a aktualizovat.

## <a name="before-you-begin"></a>Před zahájením

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance služby Azure Container Registry. Pokud jste tyto kroky neprovedli a chcete postupovat s námi, začínají [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, že používáte Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Tento instanční objekt se dá automaticky vytvořit pomocí rozhraní příkazového řádku Azure nebo portálu, nebo si ho můžete předem vytvořit a přiřadit další oprávnění. V tomto kurzu vytvoříte instanční objekt, udělíte přístup k instanci Azure Container Registry (ACR) vytvořené v předchozím kurzu a potom vytvoříte cluster AKS.

Vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac][]. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění. Ve výchozím nastavení tento instanční objekt je platný jeden rok.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Poznamenejte si *appId* a *password*. Tyto hodnoty se použijí v dalších krocích.

## <a name="configure-acr-authentication"></a>Konfigurace ověřování služby ACR

Pro přístup k imagím uloženým v ACR je nutné udělit instančnímu objektu AKS správná práva ke stahování imagí z ACR.

Nejprve pomocí příkazu [az acr show][] získejte ID prostředku služby ACR. Aktualizujte název registru `<acrName>` na název instance ACR a skupinu prostředků, ve které se instance ACR nachází.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

K udělení správného přístupu, aby cluster AKS používal image uložené v ACR, vytvořte přiřazení role pomocí příkazu [az role assignment create][]. Nahraďte `<appId`> a `<acrId>` hodnotami získanými v předchozích dvou krocích.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role Reader
```

## <a name="create-a-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Clustery AKS můžou využívat řízení přístupu na základě role (RBAC) v Kubernetes. Toto řízení umožňuje definovat přístup k prostředkům na základě rolí přiřazených uživatelům. Oprávnění jsou zkombinované, pokud má uživatel přiřazeno více rolí a oprávnění můžete obor, buď s jedním oborem názvů nebo celého clusteru. Ve výchozím nastavení Azure CLI automaticky povolí řízení přístupu na základě role při vytvoření clusteru AKS.

Vytvořte cluster AKS pomocí příkazu [az aks create][]. Následující příklad vytvoří cluster *myAKSCluster* ve skupině prostředků *myResourceGroup*. Tato skupina prostředků se vytvořila v [předchozím kurzu][aks-tutorial-prepare-acr]. Zadejte vlastní `<appId>` a `<password>` z předchozího kroku, ve kterém se vytvořil instanční objekt.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Po několika minutách se nasazení dokončí a vrátí informace o nasazení služby AKS s formátem JSON.

## <a name="install-the-kubernetes-cli"></a>Instalace rozhraní příkazového řádku Kubernetes

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Připojení ke clusteru pomocí kubectl

Ke konfiguraci `kubectl` pro připojení k vašemu clusteru Kubernetes, použijte [az aks get-credentials][] příkazu. Následující příklad získá přihlašovací údaje pro cluster AKS, s názvem *myAKSCluster* v *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení k vašemu clusteru, spusťte příkaz [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Další postup

V tomto kurzu se nasadil cluster Kubernetes ve službě AKS a nakonfigurovali jste `kubectl` pro připojení k tomuto clusteru. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření instančního objektu pro interakce prostředků
> * Nasazení clusteru Kubernetes AKS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl pro připojení ke clusteru AKS

V dalším kurzu se dozvíte, jak do clusteru nasadit aplikaci.

> [!div class="nextstepaction"]
> [Nasazení aplikace v Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
