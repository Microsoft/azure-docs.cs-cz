---
title: Kurz Kubernetes v Azure – Nasazení clusteru
description: Kurz AKS – Nasazení clusteru
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341395"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Kurz: Nasazení clusteru Azure Kubernetes Service (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Díky AKS můžete rychle zřídit cluster Kubernetes připravený na produkční prostředí. V tomto kurzu, který je třetí částí sedmidílné série, se nasadí cluster Kubernetes ve službě AKS. Mezi dokončené kroky patří:

> [!div class="checklist"]
> * Vytvoření instančního objektu pro interakce prostředků
> * Nasazení clusteru Kubernetes AKS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl

V následujících kurzech se nasadí aplikace Azure Vote do clusteru, kde se provede škálování a aktualizace.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance služby Azure Container Registry. Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

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

Nejprve získejte ID prostředku služby ACR pomocí [az acr show][]. Aktualizujte název registru `<acrName>` na název instance ACR a skupinu prostředků, ve které se instance ACR nachází.

```azurecli
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

K udělení správného přístupu, aby cluster AKS používal image uložené v ACR, vytvořte přiřazení role pomocí [az role assignment create][]. Nahraďte `<appId`> a `<acrId>` hodnotami získanými v předchozích dvou krocích.

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Teď vytvořte cluster AKS pomocí [az aks create][]. Následující příklad vytvoří cluster *myAKSCluster* ve skupině prostředků *myResourceGroup*. Tato skupina prostředků se vytvořila v [předchozím kurzu][aks-tutorial-prepare-acr]. Zadejte vlastní `<appId>` a `<password>` z předchozího kroku, ve kterém jste vytvořili instanční objekt.

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

Po několika minutách se nasazení dokončí a vrátí informace o nasazení služby AKS ve formátu JSON.

## <a name="install-the-kubectl-cli"></a>Instalace rozhraní příkazového řádku kubectl

Pokud se chcete připojit ke clusteru Kubernetes z klientského počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, kubectl je už nainstalovaný. Můžete ho také nainstalovat místně pomocí [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Připojení přes kubectl

Pokud chcete nakonfigurovat kubectl k připojení k vašemu clusteru Kubernetes, použijte příkaz [az aks get-credentials][]. Následující příklad získá přihlašovací údaje pro název clusteru AKS *myAKSCluster* ve skupině *myResourceGroup*:

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

Pokud chcete ověřit připojení k vašemu clusteru, spusťte příkaz [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Výstup:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu se nasadil cluster Kubernetes ve službě AKS. Dokončili jste následující kroky:

> [!div class="checklist"]
> * Vytvoření instančního objektu pro interakce prostředků
> * Nasazení clusteru Kubernetes AKS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl

Přejděte k dalšímu kurzu, kde se seznámíte se spuštěním aplikace v tomto clusteru.

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
