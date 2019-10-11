---
title: Kurz Kubernetes v Azure – nasazení clusteru
description: V tomto kurzu AKS (Azure Kubernetes Service) vytvoříte cluster AKS a pomocí kubectl se připojíte k hlavnímu uzlu Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 9f06aa44824c28369b331d4079e9e81417bf17c7
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263839"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Kurz: nasazení clusteru služby Azure Kubernetes (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnery aplikací. Pomocí AKS můžete rychle vytvořit cluster Kubernetes připravený pro produkční prostředí. V tomto kurzu, který je třetí částí 7, se v AKS nasadí cluster Kubernetes. Získáte informace o následujících postupech:

> [!div class="checklist"]
> * Nasazení clusteru Kubernetes AKS, který se může ověřit ve službě Azure Container Registry
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl pro připojení ke clusteru AKS

V dalších kurzech se hlasovací aplikace Azure nasadí do clusteru, škáluje a aktualizuje.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance Azure Container Registry. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte v [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Pokud chcete zjistit verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Clustery AKS můžou používat řízení přístupu na základě rolí Kubernetes (RBAC). Tyto ovládací prvky umožňují definovat přístup k prostředkům na základě rolí přiřazených uživatelům. Oprávnění jsou kombinována, pokud je uživateli přiřazena více rolí a oprávnění mohou být vymezena buď na jeden obor názvů, nebo v celém clusteru. Ve výchozím nastavení rozhraní příkazového řádku Azure CLI automaticky povolí RBAC při vytváření clusteru AKS.

Vytvořte cluster AKS pomocí [AZ AKS Create][]. Následující příklad vytvoří ve skupině prostředků s názvem *myResourceGroup*cluster s názvem *myAKSCluster* . Tato skupina prostředků se vytvořila v [předchozím kurzu][aks-tutorial-prepare-acr]. Aby mohl cluster AKS komunikovat s dalšími prostředky Azure, automaticky se vytvoří Azure Active Directory instančního objektu, protože jste ho neurčili. V tomto případě se tomuto instančnímu objektu [uděluje právo na vyžádání imagí][container-registry-integration] z instance Azure Container Registry (ACR), kterou jste vytvořili v předchozím kurzu.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Po několika minutách se nasazení dokončí a vrátí informace ve formátu JSON o nasazení AKS.

> [!NOTE]
> Aby cluster mohl spolehlivě fungovat, měli byste spustit aspoň 2 (dva) uzly.

## <a name="install-the-kubernetes-cli"></a>Instalace rozhraní příkazového řádku Kubernetes

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, je už nainstalovaná `kubectl`. Můžete ji také nainstalovat místně pomocí příkazu [AZ AKS Install-CLI][] :

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Připojení ke clusteru pomocí kubectl

Pokud chcete nakonfigurovat `kubectl` pro připojení ke clusteru Kubernetes, použijte příkaz [AZ AKS Get-Credentials][] . Následující příklad vrátí pověření pro cluster AKS s názvem *myAKSCluster* v *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, spusťte příkaz [kubectl Get Nodes][kubectl-get] :

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste v AKS nasadili cluster Kubernetes a nakonfigurovali jste `kubectl`, abyste se k němu mohli připojit. Zjistili jste, jak:

> [!div class="checklist"]
> * Nasazení clusteru Kubernetes AKS, který se může ověřit ve službě Azure Container Registry
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl pro připojení ke clusteru AKS

Přejděte k dalšímu kurzu, kde se dozvíte, jak nasadit aplikaci do clusteru.

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
[AZ AKS Create]: /cli/azure/aks#az-aks-create
[AZ AKS Install-CLI]: /cli/azure/aks#az-aks-install-cli
[AZ AKS Get-Credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
