---
title: Kurz Kubernetes v Azure – Aktualizace aplikace
description: Kurz AKS – Aktualizace aplikace
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 622cd17a93bf1b9fa9d3c138d385ca1d29426f3b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934052"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Kurz: Aktualizace aplikace ve službě Azure Kubernetes Service (AKS)

Aplikaci je možné po nasazení v Kubernetes aktualizovat zadáním nové image kontejneru nebo verze image. Aktualizace je přitom fázovaná, takže se současně aktualizuje jenom část nasazení. Tato fázovaná aktualizace umožňuje, aby aplikace během aktualizace běžela. Poskytuje také mechanismus vrácení zpět pro případ, že dojde k selhání nasazení.

V tomto kurzu, který je šestou částí osmidílné série, se aktualizuje aplikace Azure Vote. Úlohy, které provedete:

> [!div class="checklist"]
> * Aktualizace aplikačního kódu front-endu
> * Vytvoření aktualizované image kontejneru
> * Nahrání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované image kontejneru

V dalších kurzech se služba Log Analytics konfiguruje pro monitorování clusteru Kubernetes.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. Aplikace se potom spustila v tomto clusteru Kubernetes.

Naklonovalo se také úložiště aplikace, které zahrnuje zdrojový kód aplikace, a předem se vytvořil soubor nástroje Docker Compose použitý v tomto kurzu. Ověřte, že jste vytvořili klon úložiště a že jste změnili adresáře na klonovaný adresář. Uvnitř je adresář nazvaný `azure-vote` a soubor s názvem `docker-compose.yaml`.

Pokud jste tyto kroky nedokončili a chcete je sledovat, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

## <a name="update-application"></a>Aktualizace aplikace

V tomto kurzu dojde ke změně aplikace a aktualizovaná aplikace se nasadí do clusteru Kubernetes.

Zdrojový kód aplikace najdete v adresáři `azure-vote`. Otevřete `config_file.cfg` soubor pomocí libovolného textového editoru nebo editoru kódu. V tomto příkladu se používá `vi`.

```console
vi azure-vote/azure-vote/config_file.cfg
```

Změňte hodnoty pro `VOTE1VALUE` a `VOTE2VALUE`, a potom soubor uložte.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Uložte soubor a zavřete ho.

## <a name="update-container-image"></a>Aktualizace image kontejneru

Pomocí příkazu [docker-compose][docker-compose] znovu vytvořte image front-endu a spusťte aktualizovanou aplikaci. Jako instrukce pro Docker Compose, že se má znovu vytvořit image aplikace, se použije argument `--build`.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testování aplikace v místním prostředí

Přejděte na adresu http://localhost:8080 a prohlédněte si aktualizovanou aplikaci.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Označení a nahrání imagí

Označte image `azure-vote-front` pomocí názvu loginServer registru kontejneru.

Název přihlašovacího serveru získáte pomocí příkazu [az acr list](/cli/azure/acr#az_acr_list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

K označení image použijte [docker tag][docker-tag]. Místo `<acrLoginServer>` použijte název přihlašovacího serveru služby Azure Container Registry nebo název hostitele veřejného registru. Všimněte si také, že se verze image aktualizovala na `v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Pomocí příkazu [docker push][docker-push] odešlete image do registru. Místo `<acrLoginServer>` použijte název přihlašovacího serveru služby Azure Container Registry. Pokud máte s nahráváním do registru ACR problémy, ujistěte se, že jste spustili příkaz [az acr login][az-acr-login].

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-application"></a>Nasazení aktualizované aplikace

K zajištění maximální doby provozu musí být spuštěno několik instancí podu aplikace. Ověřte tuto konfiguraci pomocí příkazu [kubectl get pod][kubectl-get].

```
kubectl get pod
```

Výstup:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Pokud nemáte několik podů se spuštěnou imagí azure-vote-front, škálujte nasazení `azure-vote-front`.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

K aktualizaci aplikace použijte příkaz [kubectl set][kubectl-set]. Jako `<acrLoginServer>` použijte přihlašovací název serveru nebo hostitele vašeho registru kontejneru.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

K monitorování nasazení použijte příkaz [kubectl get pod][kubectl-get]. Při nasazení aktualizované aplikace se vaše pody ukončí a vytvoří se znovu s novou imagí kontejneru.

```azurecli
kubectl get pod
```

Výstup:

```
NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-updated-application"></a>Otestování aktualizované aplikace

Získejte externí IP adresu služby `azure-vote-front`.

```azurecli
kubectl get service azure-vote-front
```

Přejděte na tuto IP adresu a prohlédněte si aktualizovanou aplikaci.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste aktualizovali aplikaci a zavedli tuto aktualizaci do clusteru Kubernetes. Dokončili jste následující úlohy:

> [!div class="checklist"]
> * Aktualizace aplikačního kódu front-endu
> * Vytvoření aktualizované image kontejneru
> * Nahrání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované aplikace

Přejděte k dalšímu kurzu, kde se seznámíte s upgradem Kubernetes na novou verzi.

> [!div class="nextstepaction"]
> [Upgrade Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login