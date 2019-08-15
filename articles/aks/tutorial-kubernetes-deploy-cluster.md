---
title: Kurz Kubernetes v Azure – Nasazení clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) vytvoříte cluster a AKS a pomocí kubectl se připojíte se k hlavním uzlu Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: dadab604e95c375e6f963f2d5eb9b619ddad7880
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018843"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Kurz: Nasazení clusteru služby Azure Kubernetes (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Pomocí AKS můžete rychle vytvořit cluster Kubernetes připravený pro produkční prostředí. V tomto kurzu, který je třetí částí sedmidílné série, se nasadí cluster Kubernetes ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření instančního objektu pro interakce prostředků
> * Nasazení clusteru Kubernetes AKS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl pro připojení ke clusteru AKS

V dalších kurzech se hlasovací aplikace Azure nasadí do clusteru, škáluje a aktualizuje.

## <a name="before-you-begin"></a>Před zahájením

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance služby Azure Container Registry. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte v [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Tento instanční objekt se dá automaticky vytvořit pomocí rozhraní příkazového řádku Azure nebo portálu, nebo si ho můžete předem vytvořit a přiřadit další oprávnění. V tomto kurzu vytvoříte instanční objekt, udělíte přístup k instanci Azure Container Registry (ACR) vytvořené v předchozím kurzu a potom vytvoříte cluster AKS.

Vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac][]. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění. Ve výchozím nastavení je tento instanční objekt platný po dobu jednoho roku.

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

Chcete-li udělit správnému přístupu ke clusteru AKS k vyžádání imagí uložených v ACR, přiřaďte tuto `AcrPull` roli pomocí příkazu [AZ role Assignment Create][] . Nahraďte `<appId`> a `<acrId>` hodnotami získanými v předchozích dvou krocích.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Clustery AKS můžou využívat řízení přístupu na základě role (RBAC) v Kubernetes. Toto řízení umožňuje definovat přístup k prostředkům na základě rolí přiřazených uživatelům. Oprávnění jsou kombinována, pokud je uživateli přiřazena více rolí a oprávnění mohou být vymezena buď na jeden obor názvů, nebo v celém clusteru. Ve výchozím nastavení Azure CLI automaticky povolí řízení přístupu na základě role při vytvoření clusteru AKS.

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

Po několika minutách se nasazení dokončí a vrátí informace ve formátu JSON o nasazení AKS.

## <a name="install-the-kubernetes-cli"></a>Instalace rozhraní příkazového řádku Kubernetes

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Připojení ke clusteru pomocí kubectl

Pokud chcete `kubectl` nakonfigurovat připojení ke clusteru Kubernetes, použijte příkaz [AZ AKS Get-Credentials][] . Následující příklad vrátí pověření pro cluster AKS s názvem *myAKSCluster* v *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, spusťte příkaz [kubectl Get Nodes][kubectl-get] :

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.9
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
