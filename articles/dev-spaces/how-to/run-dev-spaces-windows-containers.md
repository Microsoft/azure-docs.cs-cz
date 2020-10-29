---
title: Interakce s kontejnery Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Naučte se spouštět Azure Dev Spaces v existujícím clusteru s kontejnery Windows.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, kontejnery Windows
ms.openlocfilehash: e6c4279717ef0a7bed0a66a9b0dba1d78e418835
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900149"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interakce s kontejnery Windows pomocí Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces můžete povolit pro nové i existující obory názvů Kubernetes. Azure Dev Spaces spustí služby a instrumentují služby, které běží na kontejnerech Linux. Tyto služby mohou také interagovat s aplikacemi, které běží na kontejnerech Windows ve stejném oboru názvů. V tomto článku se dozvíte, jak používat vývojové prostory ke spouštění služeb v oboru názvů s existujícími kontejnery Windows. V tuto chvíli nemůžete ladit nebo připojovat kontejnery Windows pomocí Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Nastavení clusteru

V tomto článku se předpokládá, že už máte cluster s fondy uzlů pro Linux i Windows. Pokud potřebujete vytvořit cluster s fondy uzlů pro Linux a Windows, můžete postupovat podle pokynů uvedených [tady][windows-container-cli].

Připojte se ke clusteru pomocí [kubectl][kubectl]a klienta příkazového řádku Kubernetes. Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

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
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Použijte k uzlům Windows [chuti][using-taints] . Chuti v uzlech Windows brání vývojovým prostorům v Naplánování spuštění kontejnerů Linux na uzlech systému Windows. Následující příkaz příkazu příklad aplikuje na *aksnpwin987654* uzel Windows z předchozího příkladu hodnotu chuti.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Když na uzel použijete ICM, musíte v šabloně nasazení vaší služby nakonfigurovat, aby se vaše služba na tomto uzlu spustila. Ukázková aplikace je už nakonfigurovaná tak, aby se [shodovala][sample-application-toleration-example] s tím, jak jste nakonfigurovali v předchozím příkazu.

## <a name="run-your-windows-service"></a>Spuštění služby systému Windows

Spusťte službu Windows v clusteru AKS a ověřte, zda je ve *spuštěném* stavu. Tento článek používá [ukázkovou aplikaci][sample-application] k předvedení služby systému Windows a Linux spuštěné v clusteru.

Naklonujte ukázkovou aplikaci z GitHubu a přejděte do `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` adresáře:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Ukázková aplikace používá [Helm 3][helm-installed] ke spuštění služby Windows v clusteru. Přejděte do `charts` adresáře a pomocí Helm spusťte službu systému Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Výše uvedený příkaz používá Helm ke spuštění služby Windows v oboru názvů pro *vývoj* . Pokud nemáte obor názvů s názvem *dev* , vytvoří se.

Pomocí `kubectl get pods` příkazu ověřte, že je služba Windows v clusteru spuštěná. 

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

Pokud povolíte vývojářské prostory v existujícím oboru názvů s kontejnery, které jsou již spuštěny, ve výchozím nastavení se vývojové prostory pokusí a instrumentují nové kontejnery, které jsou spuštěny v daném oboru názvů. Vývojové prostory taky vyzkouší a instrumentují všechny nové kontejnery vytvořené pro službu, která už je v oboru názvů spuštěná. Chcete-li zabránit vývojovým prostorům v instrumentaci kontejneru běžícího v oboru názvů, přidejte hlavičku *No-proxy* do `deployment.yaml` .

Přidat `azds.io/no-proxy: "true"` do `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` souboru:

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
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

V tomto příkladu je název vašeho nasazení *služba Windows-Service* . Aktualizujte službu Windows pomocí nové konfigurace pomocí `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Od chvíle, kdy jste aktualizovali své `deployment.yaml` služby, nebudete si vyvíjet ani instrumentovat službu.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Spuštění aplikace pro Linux pomocí Azure Dev Spaces

Přejděte do `webfrontend` adresáře a pomocí `azds prep` `azds up` příkazů a spusťte aplikaci pro Linux v clusteru.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

`azds prep --enable-ingress`Příkaz vygeneruje graf Helm a fázemi pro vaši aplikaci.

> [!TIP]
> [Graf souboru Dockerfile a Helm](../how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt je používán Azure dev Spaces k sestavení a spuštění kódu, ale tyto soubory lze změnit, pokud chcete změnit způsob sestavení a spuštění projektu.

`azds up`Příkaz spustí vaši službu v oboru názvů.

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

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL, která se zobrazí ve výstupu příkazu azds nahoru. V tomto příkladu je veřejná adresa URL `http://dev.webfrontend.abcdef0123.eus.azds.io/` . Přejděte ke službě v prohlížeči a klikněte *na v horní* části. Ověřte, že se zobrazí zpráva od služby *mywebapi* obsahující verzi Windows, kterou kontejner používá.

![Ukázková aplikace zobrazující verzi Windows z mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak Azure Dev Spaces funguje.

> [!div class="nextstepaction"]
> [Jak Azure Dev Spaces funguje](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
