---
title: Kurz Kubernetes v Azure – Nasazení clusteru
description: Kurz AKS – Nasazení clusteru
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1f31af4fcc7ef70889ad8bc021bea0796721e5f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-an-azure-container-service-aks-cluster"></a>Kurz: Nasazení clusteru Azure Container Service (AKS)

Kubernetes poskytuje distribuovanou platformu pro kontejnerizované aplikace. Se službou AKS je zřízení clusteru Kubernetes připraveného pro produkční prostředí snadné a rychlé. V tomto kurzu, který je třetí částí osmidílné série, se nasadí cluster Kubernetes ve službě AKS. Mezi dokončené kroky patří:

> [!div class="checklist"]
> * Nasazení clusteru Kubernetes AKS
> * Instalace rozhraní příkazového řádku Kubernetes (kubectl)
> * Konfigurace kubectl

V dalších kurzech se aplikace Azure Vote nasadí do clusteru a provede škálování a aktualizaci a služba Log Analytics se nakonfiguruje pro monitorování clusteru Kubernetes.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se vytvořila image kontejneru a nahrála se do instance služby Azure Container Registry. Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

## <a name="enable-aks-preview"></a>Povolení služby AKS verze Preview

Zatímco je služba AKS ve verzi Preview, k vytváření nových clusterů se vyžaduje příznak funkce na vašem předplatném. O tuto funkci můžete požádat pro libovolný počet předplatných, která chcete použít. Pomocí příkazu `az provider register` zaregistrujte poskytovatele služby AKS:

```azurecli
az provider register -n Microsoft.ContainerService
```

Po registraci budete připraveni vytvořit cluster Kubernetes se službou AKS.

## <a name="create-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Následující příklad vytvoří cluster s názvem `myAKSCluster` ve skupině prostředků s názvem `myResourceGroup`. Tato skupina prostředků se vytvořila v [předchozím kurzu][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Po několika minutách se nasazení dokončí a vrátí informace o nasazení služby AKS ve formátu JSON.

## <a name="install-the-kubectl-cli"></a>Instalace rozhraní příkazového řádku kubectl

Pokud se chcete připojit ke clusteru Kubernetes z klientského počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, kubectl je už nainstalován. Pokud ho chcete nainstalovat místně, spusťte následující příkaz:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Připojení přes kubectl

Pokud chcete nakonfigurovat kubectl pro připojení k vašemu clusteru Kubernetes, spusťte následující příkaz:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

Pokud chcete ověřit připojení k vašemu clusteru, spusťte příkaz [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Výstup:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

Po dokončení tohoto kurzu máte cluster AKS připravený pro úlohy. V následujících kurzech se do tohoto clusteru nasadí vícekontejnerová aplikace, horizontálně se navýší její kapacita, aktualizuje se a sleduje.

## <a name="configure-acr-authentication"></a>Konfigurace ověřování služby ACR

Mezi clusterem AKS a registrem ACR je potřeba nakonfigurovat ověřování. To zahrnuje udělení odpovídajících práv identitě AKS k přetahování imagí z registru ACR.

Nejprve získejte ID instančního objektu nakonfigurovaného pro službu AKS. Aktualizujte název skupiny prostředků a název clusteru AKS tak, aby odpovídaly vašemu prostředí.

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

Získejte ID prostředku registru ACR. Aktualizujte název registru na název vašeho registru ACR a skupinu prostředků na skupinu prostředků, ve které se registr ACR nachází.

```azurecli
ACR_ID=$(az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv)
```

Vytvořte přiřazení role udělující řádný přístup.

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu se nasadil cluster Kubernetes ve službě AKS. Dokončili jste následující kroky:

> [!div class="checklist"]
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