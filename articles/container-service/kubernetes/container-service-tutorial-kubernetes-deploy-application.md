---
title: Kurz Azure Container Service – Nasazení aplikace
description: Kurz Azure Container Service – Nasazení aplikace
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e7f9c0c3ad11cb6988f528503d614ab26dcc0968
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920291"
---
# <a name="run-applications-in-kubernetes"></a>Spuštění aplikací v Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

V tomto kurzu, který je čtvrtou částí sedmidílné série, se ukázková aplikace nasadí do clusteru Kubernetes. Mezi dokončené kroky patří:

> [!div class="checklist"]
> * Aktualizace souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Testování aplikace

V dalších kurzech se tato aplikace bude škálovat a aktualizovat a nakonfiguruje se služba Log Analytics pro monitorování clusteru Kubernetes.

V tomto kurzu se předpokládá základní znalost konceptů Kubernetes. Podrobné informace o Kubernetes najdete v [dokumentaci ke Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. 

K dokončení tohoto kurzu potřebujete předem vytvořený soubor manifestu Kubernetes `azure-vote-all-in-one-redis.yml`. Tento soubor se stáhnul se zdrojovým kódem aplikace v předchozí kurzu. Ověřte, že jste naklonovali úložiště a že jste změnili adresáře na klonované úložiště.

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Aktualizace souboru manifestu

V tomto kurzu se Azure Container Registry (ACR) používá k uložení image kontejneru. Před spuštěním aplikace je potřeba v souboru manifestu Kubernetes aktualizovat název přihlašovacího serveru ACR.

Název přihlašovacího serveru ACR získáte pomocí příkazu [az acr list](/cli/azure/acr#az-acr-list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

V předem vytvořeném souboru manifestu se jako název přihlašovacího serveru používá `microsoft`. Otevřete tento soubor pomocí libovolného textového editoru. V tomto příkladu se soubor otevírá pomocí `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Místo `microsoft` použijte název přihlašovacího serveru ACR. Tuto hodnotu najdete na řádku **47** souboru manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Uložte soubor a zavřete ho.

## <a name="deploy-application"></a>Nasazení aplikace

Pomocí příkazu [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) spusťte aplikaci. Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Výstup:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Otestování aplikace

Vytvoří se [služba Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/), která zveřejní aplikaci na internetu. Tento proces může trvat několik minut. 

Pomocí příkazu [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) s argumentem `--watch` můžete sledovat průběh.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Na začátku se bude adresa **EXTERNAL-IP** pro službu `azure-vote-front` zobrazovat ve stavu `pending`. Jakmile se stav adresy EXTERNAL-IP změní ze stavu `pending` na `IP address`, pomocí klávesové zkratky `CTRL-C` zastavte sledovací proces kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Pokud se chcete na aplikaci podívat, přejděte na externí IP adresu.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se aplikace Azure Vote nasadila do clusteru Kubernetes se službou Azure Container Service. Mezi dokončené úlohy patří:  

> [!div class="checklist"]
> * Stažení souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Otestování aplikace

V dalším kurzu se dozvíte, jak škálovat aplikaci Kubernetes i příslušnou infrastrukturu Kubernetes. 

> [!div class="nextstepaction"]
> [Škálování aplikace a infrastruktury Kubernetes](./container-service-tutorial-kubernetes-scale.md)