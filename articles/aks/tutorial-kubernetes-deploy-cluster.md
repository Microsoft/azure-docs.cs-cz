---
title: Kurz Kubernetes v Azure – Nasazení clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) vytvoříte cluster a AKS a pomocí kubectl se připojíte se k hlavním uzlu Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 80b011f9df389098095f58c02008da891b2aa8a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920526"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Kurz: Nasazení clusteru Azure Kubernetes Service (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Díky AKS můžete rychle zřídit cluster Kubernetes připravený na produkční prostředí. V tomto kurzu, který je třetí částí sedmidílné série, se nasadí cluster Kubernetes ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření instančního objektu pro interakce prostředků
> * Nasazení clusteru Kubernetes AKS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl pro připojení ke clusteru AKS

V následujících kurzech se nasadí aplikace Azure Vote do clusteru, kde se provede škálování a aktualizace.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance služby Azure Container Registry. Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje použití Azure CLI verze 2.0.44 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Tento instanční objekt se dá automaticky vytvořit pomocí rozhraní příkazového řádku Azure nebo portálu, nebo si ho můžete předem vytvořit a přiřadit další oprávnění. V tomto kurzu vytvoříte instanční objekt, udělíte přístup k instanci Azure Container Registry (ACR) vytvořené v předchozím kurzu a potom vytvoříte cluster AKS.

Vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac][]. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění.

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

Clustery AKS můžou využívat řízení přístupu na základě role (RBAC) v Kubernetes. Toto řízení umožňuje definovat přístup k prostředkům na základě rolí přiřazených uživatelům. Oprávnění je možné kombinovat přiřazením několika rolí jednomu uživateli a oprávnění můžou být vymezená jedním oborem názvů nebo celým clusterem. Pro clustery AKS je řízení přístupu na základě role v Kubernetes aktuálně ve verzi Preview. Ve výchozím nastavení Azure CLI automaticky povolí řízení přístupu na základě role při vytvoření clusteru AKS.

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

Po několika minutách se nasazení dokončí a vrátí informace o nasazení služby AKS ve formátu JSON.

## <a name="install-the-kubernetes-cli"></a>Instalace rozhraní příkazového řádku Kubernetes

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Připojení ke clusteru pomocí kubectl

Pokud chcete nakonfigurovat `kubectl` pro připojení k vašemu clusteru Kubernetes, použijte příkaz [az aks get-credentials][]. Následující příklad získá přihlašovací údaje pro název clusteru AKS *myAKSCluster* ve skupině *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení k vašemu clusteru, spusťte příkaz [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.9
```

## <a name="next-steps"></a>Další kroky

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
