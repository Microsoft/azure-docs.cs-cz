---
title: Kurz Kubernetes v Azure – Nasazení clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) vytvoříte cluster a AKS a pomocí kubectl se připojíte se k hlavním uzlu Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d7e931a55ec0a9d46a8b92d4353bd2de8edd8818
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777828"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Kurz: Nasazení clusteru Azure Kubernetes Service (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Pomocí AKS můžete rychle vytvořit cluster Kubernetes připravený pro produkční prostředí. V tomto kurzu, který je třetí částí sedmidílné série, se nasadí cluster Kubernetes ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasazení clusteru Kubernetes AKS, který se může ověřit ve službě Azure Container Registry
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl pro připojení ke clusteru AKS

V novějších kurzech se aplikace hlasování Azure nasadí do clusteru, škáluje a aktualizuje.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance služby Azure Container Registry. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte v [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Clustery AKS můžou používat řízení přístupu na základě role Kubernetes (Kubernetes RBAC). Toto řízení umožňuje definovat přístup k prostředkům na základě rolí přiřazených uživatelům. Oprávnění jsou kombinována, pokud je uživateli přiřazena více rolí a oprávnění mohou být vymezena buď na jeden obor názvů, nebo v celém clusteru. Ve výchozím nastavení rozhraní příkazového řádku Azure CLI automaticky povolí Kubernetes RBAC při vytváření clusteru AKS.

Vytvořte cluster AKS pomocí příkazu [az aks create][]. Následující příklad vytvoří cluster *myAKSCluster* ve skupině prostředků *myResourceGroup*. Tato skupina prostředků se vytvořila v [předchozím kurzu][aks-tutorial-prepare-acr] v oblasti *eastus* . Následující příklad neurčuje oblast, takže cluster AKS je také vytvořen v oblasti *eastus* . Další informace najdete v tématu [kvóty, omezení velikosti virtuálních počítačů a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)][quotas-skus-regions] , kde najdete další informace o omezeních prostředků a dostupnosti oblastí pro AKS.

Aby mohl cluster AKS komunikovat s jinými prostředky Azure, vytvoří se automaticky identita clusteru, protože jste ji neurčili. Tady je tato identita clusteru [udělená právo na vyžádání imagí][container-registry-integration] z instance Azure Container Registry (ACR), kterou jste vytvořili v předchozím kurzu. Pokud chcete příkaz úspěšně spustit, musíte mít roli **vlastníka** nebo **správce účtu Azure** v předplatném Azure.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Abyste se vyhnuli nutnosti potřebovat roli **vlastníka** nebo **správce účtu Azure** , můžete ručně nakonfigurovat instanční objekt pro vyžádání imagí z ACR. Další informace najdete v tématech [ověřování ACR pomocí instančních objektů](../container-registry/container-registry-auth-service-principal.md) nebo [ověřování z Kubernetes s tajným klíčem pro vyžádání](../container-registry/container-registry-auth-kubernetes.md)obsahu. Alternativně můžete pro snazší správu použít [spravovanou identitu](use-managed-identity.md) místo instančního objektu.

Po několika minutách se nasazení dokončí a vrátí informace ve formátu JSON o nasazení AKS.

> [!NOTE]
> Aby cluster mohl spolehlivě fungovat, měli byste spustit aspoň 2 (dva) uzly.

## <a name="install-the-kubernetes-cli"></a>Instalace rozhraní příkazového řádku Kubernetes

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Připojení ke clusteru pomocí kubectl

Pomocí příkazu [az aks get-credentials][] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad vrátí pověření pro cluster AKS s názvem *myAKSCluster* v *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, spusťte příkaz [kubectl Get Nodes][kubectl-get] , který vrátí seznam uzlů clusteru:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu se nasadil cluster Kubernetes ve službě AKS a nakonfigurovali jste `kubectl` pro připojení k tomuto clusteru. Naučili jste se:

> [!div class="checklist"]
> * Nasazení clusteru Kubernetes AKS, který se může ověřit ve službě Azure Container Registry
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
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az acr show]: /cli/azure/acr#az_acr_show
[az role assignment create]: /cli/azure/role/assignment#az_role_assignment_create
[az aks create]: /cli/azure/aks#az_aks_create
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
