---
title: Vývoj ve službě Azure Kubernetes Service (AKS) s helmou
description: Pomocí helmu s AKS a Azure Container Registry zabalit a spustit kontejnery aplikací v clusteru.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873430"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Úvodní příručka: Vývoj ve službě Azure Kubernetes Service (AKS) s helmou

[Helm][helm] je open source obalový nástroj, který vám pomůže nainstalovat a spravovat životní cyklus aplikací Kubernetes. Podobně jako u linuxových správců balíčků, jako jsou *APT* a *Yum*, se Helm používá ke správě Kubernetesových grafů, což jsou balíčky předkonfigurovaných kubernetesových zdrojů.

Tento článek ukazuje, jak použít Helm k balení a spuštění aplikace na AKS. Další podrobnosti o instalaci existující aplikace pomocí helmu najdete v [tématu Instalace existujících aplikací s helmou v AKS][helm-existing].

## <a name="prerequisites"></a>Požadované součásti

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker je nainstalovaný a nakonfigurovaný. Docker nabízí balíčky pro konfiguraci Dockeru v systému [Mac][docker-for-mac], [Windows][docker-for-windows] nebo [Linux][docker-for-linux].
* [Helm v3 nainstalován][helm-install].

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
Chcete-li použít Helm ke spuštění aplikace v clusteru AKS, budete potřebovat Azure Container Registry pro ukládání ibi kontejnerů. Níže uvedený příklad používá [az acr create][az-acr-create] k vytvoření ACR s názvem *MyHelmACR* ve skupině prostředků *MyResourceGroup* se *základní* skladovou položkou. Měli byste zadat svůj vlastní jedinečný název registru. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Skladová položka *Basic* představuje vstupní bod optimalizovaný z hlediska nákladů pro účely vývoje a poskytuje vyváženou kombinaci úložiště a propustnosti.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Výstup se podobá následujícímu příkladu. Poznamenejte si hodnotu *loginServer* pro acr, protože bude použita v pozdějším kroku. V níže uvedeném příkladu je *myhelmacr.azurecr.io* *loginServer* pro *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Chcete-li použít instanci ACR, musíte se nejprve přihlásit. Pro přihlášení použijte příkaz [az acr login.][az-acr-login] Níže uvedený příklad se přihlásí k ACR s názvem *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

Příkaz vrátí *zprávu Úspěšné přihlášení* po dokončení.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Vytvořte cluster AKS. Níže uvedený příkaz vytvoří cluster AKS s názvem MyAKS a připojí MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Cluster AKS potřebuje přístup k acr k vytahování inicií kontejneru a jejich spuštění. Výše uvedený příkaz také uděluje clusteru *MyAKS* přístup k *myHelmACR* ACR.

## <a name="connect-to-your-aks-cluster"></a>Připojení ke clusteru AKS

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad získá pověření pro cluster AKS s názvem *MyAKS* v *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Tento rychlý start používá [ukázkovou aplikaci Node.js z ukázkového úložiště Azure Dev Spaces][example-nodejs]. Klonujte aplikaci z GitHubu `dev-spaces/samples/nodejs/getting-started/webfrontend` a přejděte do adresáře.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Vytvoření souboru Dockerfile

Vytvořte nový soubor *Dockerfile* pomocí následujících možností:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Sestavení a přesunutí ukázkové aplikace do ACR

Získejte přihlašovací adresu serveru pomocí příkazu [az acr list][az-acr-list] a dotazování na *loginServer*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Pomocí Dockeru můžete vytvářet, označovat a přesouvat ukázkový kontejner aplikace do acr:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Vytvoření grafu kormul

Pomocí příkazu `helm create` vygenerujte graf Helm.

```console
helm create webfrontend
```

Proveďte následující aktualizace *webfrontend/values.yaml*:

* Změnit `image.repository` na`<acrLoginServer>/webfrontend`
* Změnit `service.type` na`LoadBalancer`

Příklad:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Aktualizace `appVersion` `v1` na in *webfrontend/Chart.yaml*. Například

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Spuštění grafu Helm

Pomocí `helm create` příkazu nainstalujte aplikaci pomocí grafu Helm.

```console
helm install webfrontend webfrontend/
```

Trvá několik minut, než služba vrátí veřejnou IP adresu. Chcete-li sledovat průběh, použijte `kubectl get service` příkaz s parametrem *watch:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Přejděte do zařízení pro vyrovnávání zatížení `<EXTERNAL-IP>` aplikace v prohlížeči pomocí ukázkové aplikace.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pokud cluster již není potřeba, použijte příkaz [odstranění skupiny az][az-group-delete] k odebrání skupiny prostředků, clusteru AKS, registru kontejnerů, bitových kopií kontejnerů, které jsou v něm uloženy, a všech souvisejících prostředků.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, identita je spravována platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

Další informace o použití helmu naleznete v dokumentaci k helmu.

> [!div class="nextstepaction"]
> [Dokumentace kormidelníku][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
