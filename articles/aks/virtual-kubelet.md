---
title: Spuštění Virtual Kubelet v clusteru služby Azure Kubernetes Service (AKS)
description: Naučte se používat Virtual Kubelet Azure Kubernetes Service (AKS) ke spuštění kontejnerů Linuxu a Windows v Azure Container Instances.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613859"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Virtual Kubelet pomocí služby Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) poskytuje hostované prostředí pro spouštění kontejnerů v Azure. Při použití ACI, není nutné ke správě základní infrastruktury pro výpočetní prostředky, tento management za vás postará Azure. Při spouštění kontejnerů v ACI, se účtují za sekundu pro každý spuštěný kontejner.

Při použití zprostředkovatele Virtual Kubelet pro Azure Container Instances, kontejnerů Linuxu a Windows můžete naplánovat na instanci kontejneru, jako by byl uzlu standardní Kubernetes. Tato konfigurace umožňuje využít možnosti Kubernetes a správu hodnotu a náklady na benefit služeb container instances.

> [!NOTE]
> AKS nyní obsahuje integrovanou podporu pro plánování kontejnery ve službě ACI, volá *virtuální uzly*. Tyto virtuální uzly aktuálně podporují instance kontejnerů Linuxu. Pokud je potřeba naplánovat instance kontejneru Windows, můžete pokračovat v používání Virtual Kubelet. V opačném případě používali virtuální uzly místo ruční Virtual Kubelet pokyny, které jste si poznamenali v tomto článku. Můžete začít s virtuálními uzly pomocí [rozhraní příkazového řádku Azure][virtual-nodes-cli] or [Azure portal][virtual-nodes-portal].
>
> Virtual Kubelet je experimentální opensourcový projekt a by měla sloužit jako takové. Abyste mohli přispívat, soubor problémů a přečtěte si další informace o virtual kubelet, najdete v článku [projektu z Githubu Virtual Kubelet][vk-github].

## <a name="before-you-begin"></a>Před zahájením

Tento dokument předpokládá, že máte AKS cluster. Pokud potřebujete AKS cluster, přečtěte si [rychlý start Azure Kubernetes Service (AKS)][aks-quick-start].

Musíte také Azure CLI verze **2.0.65** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Nainstalujte Virtual Kubelet, nainstalujte a nakonfigurujte [Helm][aks-helm] ve vašem clusteru AKS. Ujistěte se, že je vaše Tiller [nakonfigurován pro použití s Kubernetes RBAC](#for-rbac-enabled-clusters), v případě potřeby.

### <a name="register-container-instances-feature-provider"></a>Registrace poskytovatele funkce Container Instances

Pokud jste dříve nepoužili služby Azure Container Instance (ACI), zaregistrujte poskytovatele služby s vaším předplatným. Můžete zkontrolovat stav registrace poskytovatele ACI pomocí [az provider list][az-provider-list] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* poskytovatele hlásit jako *registrované*, jak je znázorněno v následujícím příkladu výstupu:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Pokud poskytovatel zobrazí jako *NotRegistered*, zaregistrujte poskytovatele pomocí [az provider register][az-provider-register] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Pro clustery s podporou RBAC

Pokud váš cluster AKS je povoleno RBAC, musíte vytvořit účet služby a role vazby pro použití s Tiller. Další informace najdete v tématu [řízení přístupu na základě rolí Helm][helm-rbac]. Pokud chcete vytvořit účet služby a role vazby, vytvořte soubor s názvem *rbac virtuální kubelet.yaml* a vložte následující definice:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Použít účet služby a vazbu s [použití kubectl][kubectl-apply] a určete vaše *rbac virtuální kubelet.yaml* souboru, jak je znázorněno v následujícím příkladu:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Nakonfigurujte Helm použít účet tiller service:

```console
helm init --service-account tiller
```

Teď můžete pokračovat instalací Virtual Kubelet do clusteru AKS.

## <a name="installation"></a>Instalace

Použití [az aks install-connector][aks-install-connector] příkaz k instalaci Virtual Kubelet. Tento příklad nasadí konektor Linux i Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Tyto argumenty jsou k dispozici pro [az aks install-connector][aks-install-connector] příkazu.

| Argument: | Popis | Požadováno |
|---|---|:---:|
| `--connector-name` | Název konektoru ACI.| Ano |
| `--name``-n` | Název spravovaného clusteru. | Ano |
| `--resource-group``-g` | Název skupiny prostředků. | Ano |
| `--os-type` | Typ operačního systému instance kontejneru. Povolené hodnoty: Both, Linux, Windows. Výchozí hodnota: Linux | Ne |
| `--aci-resource-group` | Skupina prostředků, ve kterém chcete vytvořit skupiny kontejnerů ACI. | Ne |
| `--location``-l` | Umístění pro vytvoření skupiny kontejnerů ACI. | Ne |
| `--service-principal` | Instanční objekt služby používat k ověřování do rozhraní API Azure. | Ne |
| `--client-secret` | Tajný klíč přidružený k objektu služby. | Ne |
| `--chart-url` | Adresa URL grafu helmu, který nainstaluje konektoru ACI. | Ne |
| `--image-tag` | Značka obrázku virtual kubelet Image kontejneru. | Ne |

## <a name="validate-virtual-kubelet"></a>Ověření Virtual Kubelet

Chcete-li ověřit, jestli je nainstalovaná Virtual Kubelet, vrátí seznam uzlů Kubernetes pomocí [kubectl get uzly][kubectl-get] příkaz:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Spuštění kontejneru Linuxu

Vytvořte soubor s názvem `virtual-kubelet-linux.yaml` a zkopírujte do následující kód YAML. Všimněte si, že [nodeSelector][node-selector] and [toleration][toleration] byly použity k naplánování kontejneru na uzlu.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Spusťte aplikaci [vytvořit kubectl][kubectl-create] příkazu.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Použití [kubectl get pods][kubectl-get] příkazů `-o wide` argument do výstupního seznam podů naplánované uzlu. Všimněte si, že `aci-helloworld` pod byla naplánována na `virtual-kubelet-virtual-kubelet-linux` uzlu.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Spuštění kontejneru Windows

Vytvořte soubor s názvem `virtual-kubelet-windows.yaml` a zkopírujte do následující kód YAML. Všimněte si, že [nodeSelector][node-selector] and [toleration][toleration] byly použity k naplánování kontejneru na uzlu.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Spusťte aplikaci [vytvořit kubectl][kubectl-create] příkazu.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Použití [kubectl get pods][kubectl-get] příkazů `-o wide` argument do výstupního seznam podů naplánované uzlu. Všimněte si, že `nanoserver-iis` pod byla naplánována na `virtual-kubelet-virtual-kubelet-windows` uzlu.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Odebrat Virtual Kubelet

Použití [az aks remove-connector][aks-remove-connector] příkazu odeberte Virtual Kubelet. Nahraďte hodnoty argumentů název konektoru, clusteru AKS a skupinu prostředků clusteru AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Pokud dojde k chybám odebrání oba typy konektorů operačního systému, nebo chcete odebrat jenom konektor Windows nebo Linux OS, můžete ručně zadat typ operačního systému. Přidat `--os-type` parametr na předchozí `az aks remove-connector` příkaz a zadejte `Windows` nebo `Linux`.

## <a name="next-steps"></a>Další postup

Možné problémy s Virtual Kubelet, najdete v článku [známé adaptivní a alternativní řešení][vk-troubleshooting]. To report problems with the Virtual Kubelet, [open a GitHub issue][vk-issues].

Další informace o Virtual Kubelet na [projektu z Githubu Virtual Kubelet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
