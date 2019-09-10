---
title: Použití Azure Dev Spaces k interakci s kontejnery Windows
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/25/2019
ms.topic: conceptual
description: Naučte se spouštět Azure Dev Spaces v existujícím clusteru s kontejnery Windows.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, kontejnery Windows
ms.openlocfilehash: 6c15534d5d47ba384a0f368f5d212fb1350e5229
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858607"
---
# <a name="use-azure-dev-spaces-to-interact-with-windows-containers"></a>Použití Azure Dev Spaces k interakci s kontejnery Windows

Azure Dev Spaces můžete povolit pro nové i existující obory názvů Kubernetes. Azure Dev Spaces spustí služby a instrumentují služby, které běží na kontejnerech Linux. Tyto služby mohou také interagovat s aplikacemi, které běží na kontejnerech Windows ve stejném oboru názvů. V tomto článku se dozvíte, jak používat vývojové prostory ke spouštění služeb v oboru názvů s existujícími kontejnery Windows.

## <a name="set-up-your-cluster"></a>Nastavení clusteru

V tomto článku se předpokládá, že už máte cluster s fondy uzlů pro Linux i Windows. Pokud potřebujete vytvořit cluster s fondy uzlů pro Linux a Windows, můžete postupovat podle pokynů uvedených [tady][windows-container-cli].

Připojte se ke clusteru pomocí [kubectl][kubectl]a klienta příkazového řádku Kubernetes. Pokud chcete `kubectl` nakonfigurovat připojení ke clusteru Kubernetes, použijte příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje cluster s uzlem Windows i Linux. Než budete pokračovat, ujistěte se, že je stav *připraveno* pro každý uzel.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

Použijte k uzlům Windows [chuti][using-taints] . Chuti v uzlech Windows brání vývojovým prostorům v Naplánování spuštění kontejnerů Linux na uzlech systému Windows. Následující příkaz příkazu příklad aplikuje na *aksnpwin987654* uzel Windows z předchozího příkladu hodnotu chuti.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Když na uzel použijete ICM, musíte v šabloně nasazení vaší služby nakonfigurovat, aby se vaše služba na tomto uzlu spustila. Ukázková aplikace je už nakonfigurovaná tak, aby se [shodovala][sample-application-toleration-example] s tím, jak jste nakonfigurovali v předchozím příkazu.

## <a name="run-your-windows-service"></a>Spuštění služby systému Windows

Spusťte službu Windows v clusteru AKS a ověřte, zda je ve *spuštěném* stavu. Tento článek používá [ukázkovou aplikaci][sample-application] k předvedení služby systému Windows a Linux spuštěné v clusteru.

Naklonujte ukázkovou aplikaci z GitHubu a `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` přejděte do adresáře:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Ukázková aplikace používá [Helm][helm-installed] ke spuštění služby systému Windows ve vašem clusteru. Nainstalujte do clusteru Helm a udělte mu správná oprávnění:

```console
helm init --wait
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
``` 

Přejděte do `charts` adresáře a spusťte službu systému Windows:

```console
cd charts/
helm install . --namespace dev
```

Výše uvedený příkaz používá Helm ke spuštění služby Windows v oboru názvů pro *vývoj* . Pokud nemáte obor názvů s názvem *dev*, vytvoří se.

`kubectl get pods` Pomocí příkazu ověřte, že je služba Windows v clusteru spuštěná. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Povolit Azure Dev Spaces

Povolte vývojové prostory ve stejném oboru názvů, který jste použili ke spuštění služby systému Windows. Následující příkaz povoluje vývojové prostory v oboru názvů pro *vývoj* :

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aktualizace služby Windows pro vývojové prostory

Pokud povolíte vývojářské prostory v existujícím oboru názvů s kontejnery, které jsou již spuštěny, ve výchozím nastavení se vývojové prostory pokusí a instrumentují nové kontejnery, které jsou spuštěny v daném oboru názvů. Vývojové prostory taky vyzkouší a instrumentují všechny nové kontejnery vytvořené pro službu, která už je v oboru názvů spuštěná. Chcete-li zabránit vývojovým prostorům v instrumentaci kontejneru běžícího v oboru názvů, přidejte hlavičku *No-proxy* do `deployment.yaml`.

`azds.io/no-proxy: "true"` Přidat`existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` do souboru:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Slouží `helm list` k vypsání nasazení pro službu systému Windows:

```cmd
$ helm list
NAME            REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
gilded-jackal   1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

V tomto příkladu je název vašeho nasazení *Gilded-Jackal*. Aktualizujte službu Windows pomocí nové konfigurace pomocí `helm upgrade`:

```cmd
$ helm upgrade gilded-jackal . --namespace dev
Release "gilded-jackal" has been upgraded.
```

Od chvíle, kdy `deployment.yaml`jste aktualizovali své služby, nebudete si vyvíjet ani instrumentovat službu.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Spuštění aplikace pro Linux pomocí Azure Dev Spaces

Přejděte do `webfrontend` adresáře a `azds prep` pomocí příkazů a `azds up` spusťte aplikaci pro Linux v clusteru.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

`azds prep --public` Příkaz vygeneruje graf Helm a fázemi pro vaši aplikaci. `azds up` Příkaz spustí vaši službu v oboru názvů.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL, která se zobrazí ve výstupu příkazu azds nahoru. V tomto příkladu je `http://dev.webfrontend.abcdef0123.eus.azds.io/`veřejná adresa URL. Přejděte ke službě v prohlížeči a klikněte *na v horní* části. Ověřte, že se zobrazí zpráva od služby *mywebapi* obsahující verzi Windows, kterou kontejner používá.

![Ukázková aplikace zobrazující verzi Windows z mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Další postup

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj pro tým v Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
