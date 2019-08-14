---
title: Spuštění služby Virtual Kubelet v clusteru Azure Kubernetes Service (AKS)
description: Naučte se používat Virtual Kubelet se službou Azure Kubernetes Service (AKS) ke spouštění kontejnerů Linux a Windows v Azure Container Instances.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613859"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Použití Virtual Kubelet se službou Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) poskytují hostované prostředí pro spouštění kontejnerů v Azure. Při používání ACI není nutné spravovat základní výpočetní infrastrukturu, Azure tuto správu zajišťuje za vás. Při spuštění kontejnerů v ACI se vám bude účtovat druhý za každý spuštěný kontejner.

Při použití poskytovatele Virtual Kubelet pro Azure Container Instances mohou být kontejnery Linux i Windows naplánovány na instanci kontejneru, jako by se jedná o standardní uzel Kubernetes. Tato konfigurace umožňuje využít výhod funkcí Kubernetes i hodnoty správy a zvýhodněné výhody pro instance kontejnerů.

> [!NOTE]
> AKS nyní obsahuje integrovanou podporu pro plánování kontejnerů v ACI, nazývaných *virtuální uzly*. Tyto virtuální uzly aktuálně podporují instance kontejnerů Linux. Pokud potřebujete naplánovat instance kontejnerů systému Windows, můžete pokračovat v používání Virtual Kubelet. V opačném případě byste měli použít virtuální uzly namísto ručních instrukcí Virtual Kubelet uvedených v tomto článku. Můžete začít s virtuálními uzly pomocí rozhraní příkazového [řádku Azure CLI][virtual-nodes-cli] nebo [Azure Portal][virtual-nodes-portal].
>
> Virtual Kubelet je experimentální open source projekt, který by se měl používat jako takový. Pokud chcete přispívat, problémy se soubory a přečtěte si další informace o Virtual kubelet, přečtěte si článek o [projektu GitHub Virtual kubelet][vk-github].

## <a name="before-you-begin"></a>Před zahájením

V tomto dokumentu se předpokládá, že máte cluster AKS. Pokud potřebujete cluster AKS, přečtěte si [rychlý Start pro Azure Kubernetes Service (AKS)][aks-quick-start].

Budete také potřebovat Azure CLI verze **2.0.65** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Pokud chcete nainstalovat Virtual Kubelet, nainstalujte a nakonfigurujte [Helm][aks-helm] v clusteru AKS. V případě potřeby se ujistěte, že je váš pokladna [nakonfigurovaný pro použití s KUBERNETES RBAC](#for-rbac-enabled-clusters).

### <a name="register-container-instances-feature-provider"></a>Registrovat Container Instances poskytovatele funkcí

Pokud jste dosud nepoužívali službu Azure Container instance (ACI), zaregistrujte poskytovatele služeb u svého předplatného. Stav registrace poskytovatele ACI můžete zjistit pomocí příkazu [AZ Provider list][az-provider-list] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Poskytovatel *Microsoft. ContainerInstance* by měl hlásit jako *zaregistrovaný*, jak je znázorněno v následujícím příkladu výstupu:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Pokud se zprostředkovatel zobrazí jako *NotRegistered*, zaregistrujte poskytovatele pomocí [registru AZ Provider][az-provider-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Pro clustery s podporou RBAC

Pokud váš cluster AKS má povolenou RBAC, je nutné vytvořit účet služby a vazbu role pro použití s nástrojem. Další informace najdete v tématu [Helm řízení přístupu na základě role][helm-rbac]. Pokud chcete vytvořit vazbu role a účtu služby, vytvořte soubor s názvem *RBAC-Virtual-kubelet. yaml* a vložte následující definici:

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

Použijte účet služby a vazbu s [kubectl použít][kubectl-apply] a zadejte soubor *RBAC-Virtual-kubelet. yaml* , jak je znázorněno v následujícím příkladu:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Nakonfigurujte Helm, aby používal účet služby pokladny:

```console
helm init --service-account tiller
```

Nyní můžete pokračovat v instalaci služby Virtual Kubelet do clusteru AKS.

## <a name="installation"></a>Instalace

K instalaci Virtual Kubelet použijte příkaz [AZ AKS Install-Connector][aks-install-connector] . Následující příklad nasadí Linux i konektor Windows Connector.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Tyto argumenty jsou k dispozici pro příkaz [AZ AKS Install-Connector][aks-install-connector] .

| Argument | Popis | Požadováno |
|---|---|:---:|
| `--connector-name` | Název konektoru ACI| Ano |
| `--name``-n` | Název spravovaného clusteru. | Ano |
| `--resource-group``-g` | Název skupiny prostředků | Ano |
| `--os-type` | Typ operačního systému instance kontejneru Povolené hodnoty: Systémy Linux a Windows. Výchozí: Linux | Ne |
| `--aci-resource-group` | Skupina prostředků, ve které se mají vytvořit skupiny kontejnerů ACI | Ne |
| `--location``-l` | Umístění pro vytvoření skupin kontejnerů ACI. | Ne |
| `--service-principal` | Instanční objekt používaný pro ověřování do rozhraní API Azure. | Ne |
| `--client-secret` | Tajný kód přidružený k instančnímu objektu | Ne |
| `--chart-url` | Adresa URL grafu Helm, který nainstaluje konektor ACI. | Ne |
| `--image-tag` | Značka image image virtuálního kubelet kontejneru | Ne |

## <a name="validate-virtual-kubelet"></a>Ověřit Virtual Kubelet

Pokud chcete ověřit, že se nainstaloval Virtual Kubelet, vraťte seznam uzlů Kubernetes pomocí příkazu [kubectl Get Nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Spustit kontejner Linux

Vytvořte soubor s názvem `virtual-kubelet-linux.yaml` a zkopírujte ho na následující YAML. Počítejte s tím, že se k naplánování kontejneru na uzlu používají [nodeSelector][node-selector] a [tolerování][toleration] .

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

Spusťte aplikaci pomocí příkazu [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Použijte příkaz [kubectl Get lusks][kubectl-get] s `-o wide` argumentem pro výstup seznamu lusků s plánovaným uzlem. Všimněte si, `aci-helloworld` že `virtual-kubelet-virtual-kubelet-linux` uzel pod byl naplánován na uzlu.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Spuštění kontejneru Windows

Vytvořte soubor s názvem `virtual-kubelet-windows.yaml` a zkopírujte ho na následující YAML. Počítejte s tím, že se k naplánování kontejneru na uzlu používají [nodeSelector][node-selector] a [tolerování][toleration] .

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

Spusťte aplikaci pomocí příkazu [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Použijte příkaz [kubectl Get lusks][kubectl-get] s `-o wide` argumentem pro výstup seznamu lusků s plánovaným uzlem. Všimněte si, `nanoserver-iis` že `virtual-kubelet-virtual-kubelet-windows` uzel pod byl naplánován na uzlu.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Odebrat Virtual Kubelet

Pomocí příkazu [AZ AKS Remove-Connector][aks-remove-connector] odeberte Virtual Kubelet. Hodnoty argumentů nahraďte názvem konektoru, clusterem AKS a skupinou prostředků clusteru AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Pokud narazíte na chyby při odebírání obou konektorů operačního systému nebo chcete odebrat jenom konektor operačního systému Windows nebo Linux, můžete ručně zadat typ operačního systému. Přidejte parametr do předchozího `az aks remove-connector` příkazu a zadejte `Windows` nebo `Linux`. `--os-type`

## <a name="next-steps"></a>Další postup

Možné problémy s Virtual Kubelet najdete v článku [známá adaptivní a alternativní řešení][vk-troubleshooting]. Pokud chcete nahlásit problémy s Virtual Kubelet, [otevřete problém GitHubu][vk-issues].

Přečtěte si další informace o Virtual Kubelet v [projektu GitHub Virtual Kubelet][vk-github].

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
