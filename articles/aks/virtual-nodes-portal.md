---
title: Vytváření virtuálních uzlů pomocí portálu ve službě Azure Kubernetes Services (AKS)
description: Naučte se, jak pomocí Azure Portal vytvořit cluster Azure Kubernetes Services (AKS), který pomocí virtuálních uzlů spouští lusky.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 4c67d3608d2128385c273425ea495a02fa5a8c45
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180900"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Vytvoření a konfigurace clusteru Azure Kubernetes Services (AKS) pro použití virtuálních uzlů v Azure Portal

V tomto článku se dozvíte, jak použít Azure Portal k vytvoření a konfiguraci prostředků virtuální sítě a clusteru AKS s povolenými virtuálními uzly.

> [!NOTE]
> [Tento článek](virtual-nodes.md) vám poskytne přehled o dostupnosti a omezeních oblasti pomocí virtuálních uzlů.

## <a name="before-you-begin"></a>Než začnete

Virtuální uzly umožňují síťovou komunikaci mezi lusky, které běží v Azure Container Instances (ACI) a clusteru AKS. Pro zajištění této komunikace se vytvoří podsíť virtuální sítě a přiřadí se delegovaná oprávnění. Virtuální uzly fungují jenom s clustery AKS vytvořenými pomocí *pokročilých* sítí (Azure CNI). Ve výchozím nastavení se clustery AKS vytvářejí se *základními* sítěmi (kubenet). V tomto článku se dozvíte, jak vytvořit virtuální síť a podsítě a pak nasadit cluster AKS, který využívá pokročilé sítě.

Pokud jste ACI ještě dřív nepoužívali, zaregistrujte poskytovatele služeb u svého předplatného. Stav registrace poskytovatele ACI můžete zjistit pomocí příkazu [AZ Provider list][az-provider-list] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Poskytovatel *Microsoft. ContainerInstance* by měl hlásit jako *zaregistrovaný*, jak je znázorněno v následujícím příkladu výstupu:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Pokud se zprostředkovatel zobrazí jako *NotRegistered*, zaregistrujte poskytovatele pomocí [registru AZ Provider][az-provider-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

V levém horním rohu Azure Portal vyberte **vytvořit**  >  **službu Kubernetes** prostředku.

Na kartě **Basics** (Základy) nakonfigurujte následující možnosti:

- *PODROBNOSTI O PROJEKTU:* Vyberte předplatné Azure a pak vyberte nebo vytvořte skupinu prostředků Azure, například *myResourceGroup*. Zadejte **Název clusteru Kubernetes**, například *myAKSCluster*.
- *PODROBNOSTI O CLUSTERU:* Vyberte oblast, verzi Kubernetes a předponu názvu DNS pro cluster AKS.
- *Fond primárních uzlů*: vyberte velikost virtuálního počítače pro uzly AKS. Velikost virtuálního počítače **nejde** změnit po nasazení clusteru AKS.
     - Vyberte počet uzlů, které se mají do clusteru nasadit. V tomto článku nastavte **počet uzlů** na *1*. Počet uzlů **jde** upravit po nasazení clusteru.

Klikněte na **Další: škálovat**.

Na stránce **škálování** vyberte v části **virtuální uzly** *povoleno* .

![Vytvoření clusteru AKS a povolení virtuálních uzlů](media/virtual-nodes-portal/enable-virtual-nodes.png)

Ve výchozím nastavení je vytvořen Azure Active Directory instančního objektu. Tento instanční objekt se používá pro komunikaci clusteru a integraci s dalšími službami Azure. Alternativně můžete místo instančního objektu použít spravovanou identitu pro oprávnění. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).

Cluster je taky nakonfigurovaný pro pokročilé sítě. Virtuální uzly jsou nakonfigurované tak, aby používaly svou vlastní podsíť virtuální sítě Azure. Tato podsíť má delegovaná oprávnění k připojení prostředků Azure mezi clusterem AKS. Pokud ještě nemáte delegovanou podsíť, Azure Portal vytvoří a nakonfiguruje virtuální síť Azure a podsíť pro použití s virtuálními uzly.

Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**.

Vytvoření clusteru AKS a jeho příprava k použití trvá několik minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Ke správě clusteru Kubernetes použijte klienta příkazového řádku Kubernetes [kubectl][kubectl]. Klient `kubectl` je předinstalovaný ve službě Azure Cloud Shell.

Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad získá přihlašovací údaje pro název clusteru *myAKSCluster* ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
```

Následující příklad výstupu ukazuje, že byl vytvořen jeden uzel virtuálního počítače a pak virtuální uzel pro Linux, *Virtual-Node-ACI-Linux*:

```output
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
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
```

Spusťte aplikaci pomocí příkazu [kubectl Apply][kubectl-apply] .

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Použijte příkaz [kubectl Get lusks][kubectl-get] s `-o wide` argumentem pro výstup seznamu lusků a naplánovaného uzlu. Všimněte si, že `virtual-node-helloworld` uzel pod byl naplánován na `virtual-node-linux` uzlu.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod ní je přiřazena interní IP adresa z podsítě virtuální sítě Azure delegované pro použití s virtuálními uzly.

> [!NOTE]
> Pokud používáte Image uložené v Azure Container Registry, [nakonfigurujte a používejte tajný kód Kubernetes][acr-aks-secrets]. Aktuálním omezením virtuálních uzlů je, že nemůžete použít integrované ověřování instančního objektu služby Azure AD. Pokud nepoužíváte tajný kód, nespustí se u nich naplánované na virtuálních uzlech a nahlásí se chyba `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Test virtuálního uzlu pod

Chcete-li otestovat běžící na virtuálním uzlu, přejděte k ukázkové aplikaci pomocí webového klienta. V případě, že je pod přiřazená interní IP adresa, můžete toto připojení rychle otestovat z jiného seznamu pod clusterem AKS. Vytvořte test pod a připojte k němu relaci terminálu:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Nainstalujte `curl` v části pod pomocí `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Teď dostanete přístup k adrese vašeho pod pomocí, jako je například `curl` *http://10.241.0.4* . Zadejte vlastní interní IP adresu zobrazenou v předchozím `kubectl get pods` příkazu:

```console
curl -L http://10.241.0.4
```

Zobrazí se ukázková aplikace, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zavřete relaci Terminálové služby k vašemu testu pod `exit` . Po ukončení relace je pod odstraněnou.

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
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
