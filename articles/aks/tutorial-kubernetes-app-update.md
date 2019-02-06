---
title: Kurz Kubernetes v Azure – Aktualizace aplikace
description: V tomto kurzu Azure Kubernetes Service (AKS) zjistíte, jak aktualizovat existující nasazení aplikace do AKS o novou verzi kódu aplikace.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: eb1aa90cf7a1fafaa066e5a1109d1031b53034e2
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756423"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Kurz: Aktualizace aplikace ve službě Azure Kubernetes Service (AKS)

Aplikaci je možné po nasazení v Kubernetes aktualizovat zadáním nové image kontejneru nebo verze image. Aktualizace je vynášené, takže se současně aktualizuje jenom část nasazení. Tato fázovaná aktualizace umožňuje, aby aplikace během aktualizace běžela. Poskytuje také mechanismus vrácení zpět pro případ, že dojde k selhání nasazení.

V tomto kurzu, který je šestou částí sedmidílné série, se aktualizuje aplikace Azure Vote. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Aktualizace kódu front-endu aplikace
> * Vytvoření aktualizované image kontejneru
> * Odeslání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované image kontejneru

## <a name="before-you-begin"></a>Před zahájením

V předchozích kurzech se aplikace zabalila do image kontejneru. Tato image se odeslala do Azure Container Registry a vytvoření clusteru AKS. Aplikace se pak nasadí do clusteru AKS.

Také se naklonovalo úložiště aplikace, které zahrnuje zdrojový kód aplikace, a předem se vytvořil soubor Docker Compose použitý v tomto kurzu. Ověřte, že jste vytvořili klon úložiště a změnili adresáře na klonovaný adresář. Pokud jste tyto kroky nedokončili a chcete postupovat s námi, začněte tématem [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, že používáte Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="update-an-application"></a>Aktualizace aplikace

Teď upravíme ukázkovou aplikaci a pak aktualizujeme verzi, která je už nasazená v clusteru AKS. Ujistěte se, že jste v klonované *azure vote app-redis* adresáře. Zdrojový kód ukázkové aplikace můžete prvku najít *azure-vote* adresáře. V editoru, jako je například `vi`, otevřete soubor *config_file.cfg*:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Změnit hodnoty *VOTE1VALUE* a *VOTE2VALUE* na různé hodnoty, jako je například barvy. Následující příklad ukazuje aktualizovanými hodnotami:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Uložte soubor a zavřete ho. V `vi`, použijte `:wq`.

## <a name="update-the-container-image"></a>Aktualizace image kontejneru

Pokud chcete znovu vytvořit image front-endu a otestovat aktualizovanou aplikaci, použijte příkaz [docker-compose][docker-compose]. Jako instrukce pro Docker Compose, že se má znovu vytvořit image aplikace, se použije argument `--build`:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Test aplikace v místním prostředí

Pokud chcete ověřit, že se v aktualizované imagi kontejneru projevily provedené změny, otevřete místní webový prohlížeč a přejděte na adresu http://localhost:8080.

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

Aktualizované hodnoty, které jsou součástí *config_file.cfg* souboru se zobrazí ve spuštěné aplikaci.

## <a name="tag-and-push-the-image"></a>Označení a odeslání image

Abyste mohli aktualizovanou image správně používat, označte image *azure-vote-front* pomocí názvu přihlašovacího serveru vašeho registru ACR. Název přihlašovacího serveru získáte pomocí příkazu [az acr list](/cli/azure/acr):

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

K označení image použijte [docker tag][docker-tag]. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru ACR nebo názvem hostitele veřejného registru a aktualizujte verzi image na *:v2* následujícím způsobem:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Teď pomocí příkazu [docker push][docker-push] nahrajte image do registru. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru ACR. Pokud dochází k problémům doručením (push) do registru ACR, ujistěte se, že jste spustili [az acr login] [ az-acr-login] příkazu.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Nasazení aktualizované aplikace

Pokud chcete zadat maximální dobu provozu, musí být spuštěna více instancí podu aplikace. Pomocí příkazu [kubectl get pods][kubectl-get] ověřte počet spuštěných instancí front-endu:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Pokud nemáte několik podů se front-endu, škálování *azure-vote-front* nasazení následujícím způsobem:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

K aktualizaci aplikace použijte příkaz [kubectl set][kubectl-set]. Jako `<acrLoginServer>` použijte název přihlašovacího serveru nebo hostitele vašeho registru kontejneru a zadejte verzi aplikace *v2*:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

K monitorování nasazení použijte příkaz [kubectl get pod][kubectl-get]. Při nasazení aktualizované aplikace se vaše pody ukončí a vytvoří se znovu s novou imagí kontejneru.

```console
kubectl get pods
```

Následující příklad výstupu ukazuje ukončování podů a spouštění nových instancí v průběhu nasazení:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Test aktualizované aplikace

Pokud chcete zobrazit aktualizovanou aplikaci, nejprve získejte externí IP adresu služby `azure-vote-front`:

```console
kubectl get service azure-vote-front
```

Nyní otevřete místní webový prohlížeč na IP adresu vaší služby:

![Obrázek clusteru Kubernetes v Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste aktualizovali aplikaci a zavedli tuto aktualizaci do clusteru AKS. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Aktualizace kódu front-endu aplikace
> * Vytvoření aktualizované image kontejneru
> * Odeslání image kontejneru do služby Azure Container Registry
> * Nasazení aktualizované image kontejneru

V dalším kurzu se dozvíte, jak upgradovat cluster AKS na novou verzi Kubernetes.

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
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
