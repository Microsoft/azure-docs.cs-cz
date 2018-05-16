---
title: Kurz Kubernetes v Azure – Nasazení aplikace
description: Kurz AKS – Nasazení aplikace
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8649fc12bd36172f3a10e1a2c9da891685c0b821
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Kurz: Spouštění aplikací ve službě Azure Kubernetes Service (AKS)

V tomto kurzu, který je čtvrtou částí osmidílné série, se ukázková aplikace nasadí do clusteru Kubernetes. Mezi dokončené kroky patří:

> [!div class="checklist"]
> * Aktualizace souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Testování aplikace

V dalších kurzech se tato aplikace bude škálovat a aktualizovat a nakonfiguruje se služba Log Analytics pro monitorování clusteru Kubernetes.

V tomto kurzu se předpokládá základní znalost konceptů Kubernetes. Podrobné informace o Kubernetes najdete v [dokumentaci ke Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes.

K dokončení tohoto kurzu potřebujete předem vytvořený soubor manifestu Kubernetes `azure-vote-all-in-one-redis.yaml`. Tento soubor se stáhnul se zdrojovým kódem aplikace v předchozí kurzu. Ověřte, že jste naklonovali úložiště a že jste změnili adresáře na klonované úložiště.

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

## <a name="update-manifest-file"></a>Aktualizace souboru manifestu

V tomto kurzu se Azure Container Registry (ACR) používá k uložení image kontejneru. Před spuštěním aplikace je potřeba v souboru manifestu Kubernetes aktualizovat název přihlašovacího serveru ACR.

Název přihlašovacího serveru ACR získáte pomocí příkazu [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

V předem vytvořeném souboru manifestu se jako název přihlašovacího serveru používá `microsoft`. Otevřete tento soubor pomocí libovolného textového editoru. V tomto příkladu se soubor otevírá pomocí `nano`.

```console
nano azure-vote-all-in-one-redis.yaml
```

Místo `microsoft` použijte název přihlašovacího serveru ACR. Tuto hodnotu najdete na řádku **47** souboru manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Výše uvedený kód pak bude vypadat takto:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Uložte soubor a zavřete ho.

## <a name="deploy-application"></a>Nasazení aplikace

Pomocí příkazu [kubectl create][kubectl-create] spusťte aplikaci. Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Výstup:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Otestování aplikace

Vytvoří se [služba Kubernetes][kubernetes-service], která zveřejní aplikaci na internetu. Tento proces může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```azurecli
kubectl get service azure-vote-front --watch
```

Zpočátku se *EXTERNAL-IP* (Externí IP adresa) pro službu *azure-vote-front* bude zobrazovat ve stavu *probíhá*.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Jakmile se stav adresy *EXTERNAL-IP* změní ze stavu *Probíhá* na hodnotu *IP adresa*, pomocí klávesové zkratky `CTRL-C` zastavte sledovací proces kubectl.

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Pokud se chcete na aplikaci podívat, přejděte na externí IP adresu.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Pokud se aplikace nenačetla, může být důvodem problém s ověřením v registru imagí.

Postupujte podle těchto kroků a [povolte přístup prostřednictvím tajného klíče Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se aplikace Azure Vote nasadila do clusteru Kubernetes ve službě AKS. Mezi dokončené úlohy patří:

> [!div class="checklist"]
> * Stažení souborů manifestu Kubernetes
> * Spuštění aplikace v Kubernetes
> * Otestování aplikace

V dalším kurzu se dozvíte, jak škálovat aplikaci Kubernetes i příslušnou infrastrukturu Kubernetes.

> [!div class="nextstepaction"]
> [Škálování aplikace a infrastruktury Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
