---
title: Interakce s kontejnery Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Zjistěte, jak spustit Azure Dev Spaces v existujícím clusteru s kontejnery Windows
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, kontejnery s Windows
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240489"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interakce s kontejnery Windows pomocí Azure Dev Spaces

Azure Dev Spaces můžete povolit v nových i stávajících oborech názvů Kubernetes. Azure Dev Spaces se bude spouštět a instrumentuje služby, které běží na linuxových kontejnerech. Tyto služby mohou také pracovat s aplikacemi, které běží v kontejnerech systému Windows ve stejném oboru názvů. Tento článek ukazuje, jak pomocí funkce Dev Spaces spouštět služby v oboru názvů s existujícími kontejnery systému Windows. V tuto chvíli nelze ladit nebo připojit k kontejnerům Windows s Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Nastavení clusteru

Tento článek předpokládá, že již máte cluster s fondy uzlů Linux u Windows a Windows. Pokud potřebujete vytvořit cluster s fondy uzlů Linuxu a Windows, postupujte podle pokynů [zde][windows-container-cli].

Připojte se ke clusteru pomocí [kubectl][kubectl], klienta příkazového řádku Kubernetes. Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového příkazu Kubernetes tak, aby je používalo.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje cluster s windows a linuxovým uzlem. Ujistěte se, že stav je *připraven* pro každý uzel před pokračováním.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Na uzly systému Windows použijte [počitadlo.][using-taints] Počin na uzlech systému Windows zabraňuje dev spaces v plánování linuxových kontejnerů tak, aby se spouštěly na uzlech systému Windows. Následující příkaz příklad příkazu použije počitadlo *aksnpwin987654* uzel systému Windows z předchozího příkladu.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Při použití počin uzla, je nutné nakonfigurovat odpovídající tolerance v šabloně nasazení vaší služby ke spuštění služby v tomto uzlu. Ukázková aplikace je již nakonfigurována s [odpovídající tolerací][sample-application-toleration-example] s počitatým příkazem, který jste nakonfigurovali v předchozím příkazu.

## <a name="run-your-windows-service"></a>Spuštění služby Systému Windows

Spusťte službu Windows v clusteru AKS a ověřte, zda je ve *stavu Spuštěno.* Tento článek používá [ukázkovou aplikaci][sample-application] k předvedení služby Windows a Linux spuštěné v clusteru.

Klonujte ukázkovou aplikaci z GitHubu `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` a přejděte do adresáře:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Ukázková aplikace používá [Helm 3][helm-installed] ke spuštění služby Systému Windows v clusteru. Přejděte `charts` do adresáře a pomocí helmu spusťte službu Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Výše uvedený příkaz používá helmke spuštění služby systému Windows v oboru názvů *dev.* Pokud nemáte obor názvů s názvem *dev*, bude vytvořen.

Pomocí `kubectl get pods` příkazu ověřte, zda je služba systému Windows spuštěna v clusteru. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Povolení azure dev spaces

Povolte dev spaces ve stejném oboru názvů, který jste použili ke spuštění služby systému Windows. Následující příkaz povolí funkce Dev Spaces v oboru názvů *dev:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aktualizace služby Windows pro dev spaces

Pokud povolíte Dev Spaces v existujícím oboru názvů s kontejnery, které jsou již spuštěny, ve výchozím nastavení se dev Spaces pokusí instrumentovat všechny nové kontejnery, které běží v tomto oboru názvů. Dev Spaces se také pokusí instrumentovat všechny nové kontejnery vytvořené pro službu, která je již spuštěna v oboru názvů. Chcete-li zabránit tomu, aby aplikace Dev Spaces instrumentovala kontejner `deployment.yaml`spuštěný v oboru názvů, přidejte *do* pole .

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

Slouží `helm list` k zobrazení seznamu nasazení pro službu systému Windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

Ve výše uvedeném příkladu je název nasazení *windows-service*. Aktualizujte službu systému `helm upgrade`Windows novou konfigurací pomocí :

```cmd
helm upgrade windows-service . --namespace dev
```

Od aktualizace `deployment.yaml`aplikace , aplikace Dev Spaces se nepokusí o instrumentace vaší služby.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Spuštění linuxové aplikace pomocí Azure Dev Spaces

Přejděte `webfrontend` do adresáře `azds prep` `azds up` a pomocí příkazů a spusťte aplikaci Linux v clusteru.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Příkaz `azds prep --enable-ingress` generuje graf Helm a Dockerfiles pro vaši aplikaci.

> [!TIP]
> [Dockerfile a Helm graf](../how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt se používá Azure Dev Spaces k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

Příkaz `azds up` spustí službu v oboru názvů.

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

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL, která je zobrazena ve výstupu z příkazu azds up. V tomto příkladu je `http://dev.webfrontend.abcdef0123.eus.azds.io/`veřejná adresa URL . Přejděte na službu v prohlížeči a klikněte na *o o* v horní části. Ověřte, zda se zobrazí zpráva ze služby *mywebapi* obsahující verzi systému Windows, kterou kontejner používá.

![Ukázková aplikace zobrazující verzi Windows z mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj týmu v Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
