---
title: (NEPOUŽÍVANÉ) Kurz Azure Container Service – aktualizace aplikace
description: Kurz Azure Container Service – Aktualizace aplikace
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 99e282b720bb29ed5fb94ad2c9779ae56a019836
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993520"
---
# <a name="deprecated-update-an-application-in-kubernetes"></a>(NEPOUŽÍVANÉ) Aktualizace aplikace v Kubernetes

> [!TIP]
> Aktualizovaná verze tohoto kurzu, který používá Azure Kubernetes Service, najdete v článku [kurz: aktualizace aplikace ve službě Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-app-update.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Aplikaci je možné po nasazení v Kubernetes aktualizovat zadáním nové image kontejneru nebo verze image. Aktualizace je přitom fázovaná, takže se současně aktualizuje jenom část nasazení. Tato fázovaná aktualizace umožňuje, aby aplikace během aktualizace běžela. Poskytuje také mechanismus vrácení zpět pro případ, že dojde k selhání nasazení. 

V tomto kurzu, který je šestou částí sedmidílné série, se aktualizuje aplikace Azure Vote. Úlohy, které provedete:

> [!div class="checklist"]
> * Aktualizace aplikačního kódu front-endu
> * Vytvoření aktualizované image kontejneru
> * Nahrání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované image kontejneru

V dalších kurzech se služba Log Analytics konfiguruje pro monitorování clusteru Kubernetes.

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. Aplikace se potom spustila v tomto clusteru Kubernetes. 

Naklonovalo se také úložiště aplikace, které zahrnuje zdrojový kód aplikace, a předem se vytvořil soubor nástroje Docker Compose použitý v tomto kurzu. Ověřte, že jste vytvořili klon úložiště a že jste změnili adresáře na klonovaný adresář. Uvnitř je adresář nazvaný `azure-vote` a soubor s názvem `docker-compose.yml`.

Pokud jste tyto kroky nedokončili a chcete je sledovat, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Aktualizace aplikace

V tomto kurzu dojde ke změně aplikace a aktualizovaná aplikace se nasadí do clusteru Kubernetes. 

Zdrojový kód aplikace najdete v adresáři `azure-vote`. Otevřete `config_file.cfg` soubor pomocí libovolného textového editoru nebo editoru kódu. V tomto příkladu se používá `vi`.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Změňte hodnoty pro `VOTE1VALUE` a `VOTE2VALUE`, a potom soubor uložte.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Uložte soubor a zavřete ho.

## <a name="update-container-image"></a>Aktualizace image kontejneru

Pomocí příkazu [docker-compose](https://docs.docker.com/compose/) znovu vytvořte image front-endu a spusťte aktualizovanou aplikaci. Jako instrukce pro Docker Compose, že se má znovu vytvořit image aplikace, se použije argument `--build`.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testování aplikace v místním prostředí

Přejděte na adresu http://localhost:8080 a prohlédněte si aktualizovanou aplikaci.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Označení a nahrání imagí

Označte image `azure-vote-front` pomocí názvu loginServer registru kontejneru. 

Název přihlašovacího serveru získáte pomocí příkazu [az acr list](/cli/azure/acr#az-acr-list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

K označení image použijte [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Místo `<acrLoginServer>` použijte název přihlašovacího serveru služby Azure Container Registry nebo název hostitele veřejného registru. Všimněte si také, že se verze image aktualizovala na `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Pomocí příkazu [docker push](https://docs.docker.com/engine/reference/commandline/push/) odešlete image do registru. Místo `<acrLoginServer>` použijte název přihlašovacího serveru služby Azure Container Registry.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Nasazení aktualizované aplikace

K zajištění maximální doby provozu musí být spuštěno několik instancí podu aplikace. Ověřte tuto konfiguraci pomocí příkazu [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```bash
kubectl get pod
```

Výstup:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Pokud nemáte několik podů se spuštěnou imagí azure-vote-front, škálujte nasazení `azure-vote-front`.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

K aktualizaci aplikace použijte příkaz [kubectl set](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set). Jako `<acrLoginServer>` použijte přihlašovací název serveru nebo hostitele vašeho registru kontejneru.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

K monitorování nasazení použijte příkaz [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get). Při nasazení aktualizované aplikace se vaše pody ukončí a vytvoří se znovu s novou imagí kontejneru.

```azurecli-interactive
kubectl get pod
```

Výstup:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Otestování aktualizované aplikace

Získejte externí IP adresu služby `azure-vote-front`.

```azurecli-interactive
kubectl get service azure-vote-front
```

Přejděte na tuto IP adresu a prohlédněte si aktualizovanou aplikaci.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste aktualizovali aplikaci a zavedli tuto aktualizaci do clusteru Kubernetes. Dokončili jste následující úlohy:

> [!div class="checklist"]
> * Aktualizace aplikačního kódu front-endu
> * Vytvoření aktualizované image kontejneru
> * Nahrání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované aplikace

V dalším kurzu se dozvíte, jak monitorovat Kubernetes s využitím služby Log Analytics.

> [!div class="nextstepaction"]
> [Monitorování Kubernetes pomocí Log Analytics](./container-service-tutorial-kubernetes-monitor.md)