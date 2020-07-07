---
title: Vývoj ve službě Azure Kubernetes Service (AKS) s využitím Helm
description: Použijte Helm s AKS a Azure Container Registry k zabalení a spuštění kontejnerů aplikací v clusteru.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 1f67605918e093e9ab28aa88be777d27acd831ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82169564"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Rychlý Start: vývoj ve službě Azure Kubernetes Service (AKS) s využitím Helm

[Helm][helm] je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako je *apt* a *Yumu*, se Helm používá ke správě Kubernetes grafů, což jsou balíčky předkonfigurovaných prostředků Kubernetes.

V tomto článku se dozvíte, jak pomocí Helm zabalit a spustit aplikaci na AKS. Další informace o instalaci existující aplikace pomocí Helm najdete v tématu [instalace existujících aplikací pomocí Helm v AKS][helm-existing].

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker je nainstalovaný a nakonfigurovaný. Docker nabízí balíčky pro konfiguraci Dockeru v systému [Mac][docker-for-mac], [Windows][docker-for-windows] nebo [Linux][docker-for-linux].
* [Nainstalovaná verze Helm V3][helm-install].

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
Pokud chcete používat Helm ke spuštění aplikace v clusteru AKS, budete potřebovat Azure Container Registry k ukládání imagí kontejneru. Následující příklad používá [AZ ACR Create][az-acr-create] k vytvoření ACR s názvem *MyHelmACR* ve skupině prostředků *MyResourceGroup* se *základní* SKU. Měli byste zadat vlastní jedinečný název registru. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Skladová položka *Basic* představuje vstupní bod optimalizovaný z hlediska nákladů pro účely vývoje a poskytuje vyváženou kombinaci úložiště a propustnosti.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Výstup se podobá následujícímu příkladu. Poznamenejte si hodnotu *loginServer* pro svůj ACR, protože se použije v pozdějším kroku. V následujícím příkladu je *Myhelmacr.azurecr.IO* *loginServer* pro *myhelmacr*.

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

Pokud chcete použít instanci ACR, musíte se nejdřív přihlásit. Přihlaste se pomocí příkazu [AZ ACR Login][az-acr-login] . Následující příklad se přihlásí k ACR s názvem *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

Příkaz po dokončení vrátí zprávu o *úspěšném přihlášení* .

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Vytvořte cluster AKS. Následující příkaz vytvoří cluster AKS s názvem MyAKS a připojí MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Váš cluster AKS potřebuje přístup k vašemu ACR, aby mohl načíst image kontejneru a spustit je. Výše uvedený příkaz také udělí *MyAKS* clusteru přístup k vašemu *MyHelmACR* ACR.

## <a name="connect-to-your-aks-cluster"></a>Připojení ke clusteru AKS

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad vrátí pověření pro cluster AKS s názvem *MyAKS* v *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

V tomto rychlém startu [se používá příklad Node.js aplikace z ukázkového úložiště Azure dev Spaces][example-nodejs]. Naklonujte aplikaci z GitHubu a přejděte do `dev-spaces/samples/nodejs/getting-started/webfrontend` adresáře.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Vytvoření souboru Dockerfile

Vytvořte nový soubor *souboru Dockerfile* pomocí následujících kroků:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Sestavení a vložení ukázkové aplikace do ACR

Adresu přihlašovacího serveru získáte pomocí příkazu [AZ ACR list][az-acr-list] a dotazování na *loginServer*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Pomocí Docker sestavíte, označíte a nahrajete vzorový kontejner aplikace do ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Vytvoření grafu Helm

Vygenerujte graf Helm pomocí `helm create` příkazu.

```console
helm create webfrontend
```

Proveďte následující aktualizace pro *webendu/Values. yaml*:

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

Aktualizace `appVersion` na `v1` *webendu/Chart. yaml* Například

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Spuštění grafu Helm

Použijte `helm install` příkaz k instalaci aplikace pomocí grafu Helm.

```console
helm install webfrontend webfrontend/
```

Vrácení veřejné IP adresy službou může trvat několik minut. Chcete-li monitorovat průběh, použijte `kubectl get service` příkaz s parametrem *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Přejděte do nástroje pro vyrovnávání zatížení vaší aplikace v prohlížeči pomocí nástroje, `<EXTERNAL-IP>` aby se zobrazila ukázková aplikace.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pokud už cluster nepotřebujete, odeberte pomocí příkazu [AZ Group Delete][az-group-delete] skupinu prostředků, cluster AKS, registr kontejnerů, uložené image kontejnerů a všechny související prostředky.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

Další informace o použití Helm najdete v dokumentaci k Helm.

> [!div class="nextstepaction"]
> [Dokumentace k Helm][helm-documentation]

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
