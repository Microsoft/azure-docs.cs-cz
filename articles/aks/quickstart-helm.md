---
title: Vývoj ve službě Azure Kubernetes Service (AKS) s využitím Helm
description: Použijte Helm s AKS a Azure Container Registry k zabalení a spuštění kontejnerů aplikací v clusteru.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 4f5232920853908aa5ad714313ead201494caa0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493069"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Rychlý Start: vývoj ve službě Azure Kubernetes Service (AKS) s využitím Helm

[Helm][helm] je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako je *apt* a *Yumu*, Helm spravuje Kubernetes grafy, což jsou balíčky předem nakonfigurovaných prostředků Kubernetes.

V tomto rychlém startu použijete Helm k zabalení a spuštění aplikace v AKS. Další informace o instalaci existující aplikace pomocí Helm naleznete [v Průvodci instalací existujících aplikací pomocí Helm v AKS][helm-existing] .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli)
* [Nainstalovaná verze Helm V3][helm-install].

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry
Image kontejneru budete muset uložit do Azure Container Registry (ACR), aby se vaše aplikace spouštěla v clusteru AKS pomocí Helm. Zadejte vlastní název registru jedinečný v rámci Azure a obsahující 5-50 alfanumerických znaků. Skladová položka *Basic* představuje vstupní bod optimalizovaný z hlediska nákladů pro účely vývoje a poskytuje vyváženou kombinaci úložiště a propustnosti.

Následující příklad používá [AZ ACR Create][az-acr-create] k vytvoření ACR s názvem *MyHelmACR* v *MyResourceGroup* se *základní* SKU.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Výstup bude vypadat podobně jako v následujícím příkladu. Poznamenejte si hodnotu *loginServer* pro svůj ACR, protože ji budete používat v pozdějším kroku. V následujícím příkladu je *Myhelmacr.azurecr.IO* *loginServer* pro *myhelmacr*.

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

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Váš nový cluster AKS potřebuje přístup k vašemu ACR, aby mohl načíst image kontejneru a spustit je. Následující příkaz použijte k:
* Vytvořte cluster AKS s názvem *MyAKS* a připojte *MyHelmACR*.
* Udělte *MyAKS* clusteru přístup k vašemu *MyHelmACR* ACR.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Připojení ke clusteru AKS

Pokud chcete připojit cluster Kubernetes místně, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. `kubectl` je již nainstalován, pokud používáte Azure Cloud Shell. 

1. `kubectl`Místní instalace pomocí `az aks install-cli` příkazu:

    ```azurecli
    az aks install-cli
    ```

2. Nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí `az aks get-credentials` příkazu. Následující příklad příkazu vrátí pověření pro cluster AKS s názvem *MyAKS* v *MyResourceGroup*:  

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

Pomocí následujících příkazů vytvořte nový soubor *souboru Dockerfile* :

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

Pomocí předchozí souboru Dockerfile spuštěním příkazu [AZ ACR Build][az-acr-build] Sestavte a nahrajte image do registru. Na `.` konci příkazu nastaví umístění souboru Dockerfile (v tomto případě aktuální adresář).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Vytvoření grafu Helm

Vygenerujte graf Helm pomocí `helm create` příkazu.

```console
helm create webfrontend
```

Aktualizace *webendu/Values. yaml*:
* Nahraďte loginServer registru, který jste si poznamenali v předchozím kroku, jako je třeba *myhelmacr.azurecr.IO*.
* Změnit `image.repository` na `<loginServer>/webfrontend`
* Změnit `service.type` na `LoadBalancer`

Například:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
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

Pomocí příkazu nainstalujte svoji aplikaci pomocí grafu Helm `helm install` .

```console
helm install webfrontend webfrontend/
```

Vrácení veřejné IP adresy službou může trvat několik minut. Sledujte průběh pomocí `kubectl get service` příkazu s `--watch` argumentem.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Přejděte do nástroje pro vyrovnávání zatížení vaší aplikace v prohlížeči pomocí, `<EXTERNAL-IP>` aby se zobrazila ukázková aplikace.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Pomocí příkazu [AZ Group Delete][az-group-delete] odeberte skupinu prostředků, cluster AKS, registr kontejnerů, image kontejnerů uložené ve službě ACR a všechny související prostředky.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].
> 
> Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje odebrání.

## <a name="next-steps"></a>Další kroky

Další informace o použití Helm najdete v dokumentaci k Helm.

> [!div class="nextstepaction"]
> [Dokumentace k nástroji Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
