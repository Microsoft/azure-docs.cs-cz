---
title: Vytváření virtuálních uzlů pomocí portálu ve službě Azure Kubernetes Services (AKS)
description: Naučte se, jak pomocí Azure Portal vytvořit cluster Azure Kubernetes Services (AKS), který pomocí virtuálních uzlů spouští lusky.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: ab0aebf0b66ac01e19699795b14063df31cb9621
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263756"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Vytvoření a konfigurace clusteru Azure Kubernetes Services (AKS) pro použití virtuálních uzlů v Azure Portal

K rychlému nasazení úloh v clusteru Azure Kubernetes Service (AKS) můžete použít virtuální uzly. U virtuálních uzlů máte rychlé zřizování lusků a platíte za dobu jejich spuštění jenom za sekundu. Ve scénáři škálování nemusíte čekat, až nástroj pro automatické škálování clusteru Kubernetes nasadí výpočetní uzly pro virtuální počítače, aby se spouštěly další lusky. Virtuální uzly jsou podporované jenom se systémy Linux a uzly.

V tomto článku se dozvíte, jak vytvořit a nakonfigurovat prostředky virtuální sítě a cluster AKS s povolenými virtuálními uzly.

## <a name="before-you-begin"></a>Než začnete

Virtuální uzly umožňují síťovou komunikaci mezi lusky, které běží v ACI a v clusteru AKS. Pro zajištění této komunikace se vytvoří podsíť virtuální sítě a přiřadí se delegovaná oprávnění. Virtuální uzly fungují jenom s clustery AKS vytvořenými pomocí *pokročilých* sítí. Ve výchozím nastavení se clustery AKS vytvářejí se *základními* sítěmi. V tomto článku se dozvíte, jak vytvořit virtuální síť a podsítě a pak nasadit cluster AKS, který využívá pokročilé sítě.

Pokud jste ACI ještě dřív nepoužívali, zaregistrujte poskytovatele služeb u svého předplatného. Stav registrace poskytovatele ACI můžete zjistit pomocí příkazu [AZ Provider list][az-provider-list] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Poskytovatel *Microsoft. ContainerInstance* by měl hlásit jako *zaregistrovaný*, jak je znázorněno v následujícím příkladu výstupu:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Pokud se zprostředkovatel zobrazí jako *NotRegistered*, zaregistrujte poskytovatele pomocí [az Provider Register] [az-Provider-Register], jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regionální dostupnost

Pro nasazení virtuálních uzlů jsou podporovány následující oblasti:

* Austrálie – východ (australiaeast)
* Střed USA (centralus)
* Východní USA (eastus)
* Východní USA 2 (eastus2)
* Japonsko – východ (japaneast)
* Severní Evropa (northeurope)
* Jihovýchodní Asie (southeastasia)
* Středozápadní USA (westcentralus)
* Západní Evropa (westeurope)
* Západní USA (westus)
* Západní USA 2 (westus2)

## <a name="known-limitations"></a>Známá omezení
Funkce virtuálních uzlů je silně závislá na sadě funkcí ACI. Následující scénáře se zatím nepodporují s virtuálními uzly.

* Získání ACR imagí pomocí instančního objektu [Alternativním řešením](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) je používání [tajných klíčů Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Omezení Virtual Network](../container-instances/container-instances-vnet.md) , včetně partnerských vztahů virtuálních sítí, zásad sítě Kubernetes a odchozího provozu do Internetu se skupinami zabezpečení sítě.
* Inicializovat kontejnery
* [Aliasy hostitele](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) pro exec v ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nebude nasazovat lusky do virtuálního uzlu.
* [Uzly Windows serveru (aktuálně ve verzi Preview v AKS)](windows-container-cli.md) nejsou podporované současně s virtuálními uzly. Virtuální uzly můžete použít k naplánování kontejnerů Windows serveru bez nutnosti uzlů Windows serveru v clusteru AKS.

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Přihlaste se k Azure Portal v https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V levém horním rohu Azure Portal vyberte **vytvořit prostředek** > **Služba Kubernetes**.

Na stránce **základy** nakonfigurujte následující možnosti:

- *Podrobnosti o projektu*: vyberte předplatné Azure a pak vyberte nebo vytvořte skupinu prostředků Azure, například *myResourceGroup*. Zadejte **název clusteru Kubernetes**, například *myAKSCluster*.
- *Podrobnosti o clusteru*: Vyberte oblast, verzi Kubernetes a předponu názvu DNS pro cluster AKS.
- *Fond primárních uzlů*: vyberte velikost virtuálního počítače pro uzly AKS. Po nasazení clusteru AKS **nejde** změnit velikost virtuálního počítače.
     - Vyberte počet uzlů, které mají být do clusteru nasazeny. V tomto článku nastavte **počet uzlů** na *1*. Počet uzlů se **dá** upravit po nasazení clusteru.

Klikněte na **Další: škálovat**.

Na stránce **škálování** vyberte v části **virtuální uzly** *povoleno* .

![Vytvoření clusteru AKS a povolení virtuálních uzlů](media/virtual-nodes-portal/enable-virtual-nodes.png)

Ve výchozím nastavení je vytvořen Azure Active Directory instančního objektu. Tento instanční objekt se používá pro komunikaci clusteru a integraci s dalšími službami Azure.

Cluster je taky nakonfigurovaný pro pokročilé sítě. Virtuální uzly jsou nakonfigurované tak, aby používaly svou vlastní podsíť virtuální sítě Azure. Tato podsíť má delegovaná oprávnění k připojení prostředků Azure mezi clusterem AKS. Pokud ještě nemáte delegovanou podsíť, Azure Portal vytvoří a nakonfiguruje virtuální síť Azure a podsíť pro použití s virtuálními uzly.

Vyberte **zkontrolovat + vytvořit**. Po dokončení ověření vyberte **vytvořit**.

Vytvoření clusteru AKS a jeho připravení k použití trvá několik minut.

## <a name="connect-to-the-cluster"></a>Připojit ke clusteru

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má společné nástroje Azure, které jsou předinstalované a nakonfigurované pro použití s vaším účtem. Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. Klient `kubectl` je předem nainstalován v Azure Cloud Shell.

Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash). Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy ENTER ji spusťte.

Pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes. Následující příklad vrátí pověření pro název clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl Get][kubectl-get] , který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje, že byl vytvořen jeden uzel virtuálního počítače a pak virtuální uzel pro Linux, *Virtual-Node-ACI-Linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Nasazení ukázkové aplikace

V Azure Cloud Shell vytvořte soubor s názvem `virtual-node.yaml` a zkopírujte následující YAML. Pro naplánování kontejneru na uzlu jsou definovány [nodeSelector][node-selector] a [tolerování][toleration] . Tato nastavení umožňují, aby se uzel v poli naplánoval na virtuálním uzlu a zkontroloval, jestli je funkce úspěšně povolená.

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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Spusťte aplikaci pomocí příkazu [kubectl Apply][kubectl-apply] .

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Pro výstup seznamu lusků a naplánovaného uzlu použijte příkaz [kubectl Get lusks][kubectl-get] s argumentem `-o wide`. Všimněte si, že `virtual-node-helloworld` pod bylo naplánováno na uzlu `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod ní je přiřazena interní IP adresa z podsítě virtuální sítě Azure delegované pro použití s virtuálními uzly.

> [!NOTE]
> Pokud používáte Image uložené v Azure Container Registry, [nakonfigurujte a používejte tajný kód Kubernetes][acr-aks-secrets]. Aktuálním omezením virtuálních uzlů je, že nemůžete použít integrované ověřování instančního objektu služby Azure AD. Pokud nepoužíváte tajný kód, nespustí se v něm naplánované na virtuálních uzlech a nahlásí se chyba `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Test virtuálního uzlu pod

Chcete-li otestovat běžící na virtuálním uzlu, přejděte k ukázkové aplikaci pomocí webového klienta. V případě, že je pod přiřazená interní IP adresa, můžete toto připojení rychle otestovat z jiného seznamu pod clusterem AKS. Vytvořte test pod a připojte k němu relaci terminálu:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Nainstalovat `curl` v části pod pomocí `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Teď dostanete přístup k adrese vaší pod pomocí `curl`, jako je například *http://10.241.0.4* . Zadejte vlastní interní IP adresu uvedenou v předchozím příkazu `kubectl get pods`:

```azurecli-interactive
curl -L http://10.241.0.4
```

Zobrazí se ukázková aplikace, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Ukončete relaci terminálu s testem pod `exit`. Po ukončení relace je pod odstraněnou.

## <a name="next-steps"></a>Další kroky

V tomto článku se naplánovala pod virtuálním uzlem a přiřadila se privátní interní IP adresa. Místo toho můžete pomocí nástroje pro vyrovnávání zatížení nebo řadiče pro příjem dat vytvořit nasazení služby a směrovat provoz do svého zařízení pod ním. Další informace najdete v tématu [Vytvoření základního kontroleru][aks-basic-ingress]příchozího přenosu v AKS.

Virtuální uzly jsou jedna součást řešení škálování v AKS. Další informace o škálování řešení najdete v následujících článcích:

- [Použití automatického škálování Kubernetes vodorovně pod][aks-hpa]
- [Použití automatického škálování clusteru Kubernetes][aks-cluster-autoscaler]
- [Podívejte se na ukázku automatického škálování pro virtuální uzly.][virtual-node-autoscale]
- [Přečtěte si další informace o knihovně open source knihovny Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list