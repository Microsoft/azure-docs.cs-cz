---
title: Použití více fondů uzlů ve službě Azure Kubernetes Service (AKS)
description: Naučte se vytvářet a spravovat fondy více uzlů pro cluster ve službě Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 656934f00879b47669fac4deaac5156cb100e159
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898749"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Preview – vytvoření a Správa fondů více uzlů pro cluster ve službě Azure Kubernetes (AKS)

Ve službě Azure Kubernetes Service (AKS) jsou uzly stejné konfigurace seskupeny dohromady do *fondů uzlů*. Tyto fondy uzlů obsahují základní virtuální počítače, na kterých běží vaše aplikace. Počáteční počet uzlů a jejich velikost (SKU) jsou definovány při vytváření clusteru AKS, který vytváří *výchozí fond uzlů*. Aby bylo možné podporovat aplikace, které mají různé výpočetní požadavky nebo úložiště, můžete vytvořit další fondy uzlů. Tyto další fondy uzlů můžete například využít k poskytování GPU pro aplikace náročné na výpočetní výkon nebo přístup k vysoce výkonnému úložišti SSD.

> [!NOTE]
> Tato funkce umožňuje vyšší kontrolu nad tím, jak vytvořit a spravovat více fondů uzlů. V důsledku toho jsou pro vytvoření, aktualizaci nebo odstranění vyžadovány samostatné příkazy. Dříve clusterové operace `az aks create` prostřednictvím `az aks update` nebo používaly rozhraní managedCluster API a byly jedinou možností, jak změnit plochu ovládacího prvku a jeden fond uzlů. Tato funkce zpřístupňuje samostatnou sadu operací pro fondy agentů prostřednictvím rozhraní neznámá API a vyžaduje použití `az aks nodepool` sady příkazů ke spouštění operací ve fondu jednotlivých uzlů.

V tomto článku se dozvíte, jak vytvořit a spravovat více fondů uzlů v clusteru AKS. Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Před zahájením

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Nainstalovat rozšíření CLI AKS-Preview

Chcete-li použít více fondů uzlů, potřebujete rozšíření *AKS-Preview* CLI 0.4.1 nebo vyšší verze. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Registrovat poskytovatele funkcí více fondů uzlů

Pokud chcete vytvořit cluster AKS, který může používat víc fondů uzlů, povolte nejdřív ve svém předplatném dva příznaky funkcí. Clustery s více uzly používají ke správě nasazení a konfigurace uzlů Kubernetes virtuální počítač VMSS (Virtual Machine Scale set). Pomocí příkazu [AZ Feature Register][az-feature-register] Zaregistrujte příznaky funkcí *MultiAgentpoolPreview* a *VMSSPreview* , jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí verze Preview se můžou použít výchozí hodnoty pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Libovolný cluster AKS, který vytvoříte po úspěšném dokončení registrace *MultiAgentpoolPreview* , používá tuto verzi Preview prostředí clusteru. Pokud chcete pokračovat v vytváření běžných, plně podporovaných clusterů, nepovolujte funkce ve verzi Preview v produkčních předplatných. Pro testování funkcí ve verzi Preview použijte samostatný test nebo vývojové předplatné Azure.

Zobrazení stavu v *registraci*trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují více fondů uzlů, platí následující omezení:

* Pro clustery vytvořené po úspěšném zaregistrování funkcí *MultiAgentpoolPreview* a *VMSSPreview* pro vaše předplatné je k dispozici jen více fondů uzlů. Nemůžete přidat ani spravovat fondy uzlů s existujícím clusterem AKS vytvořeným před tím, než se tyto funkce úspěšně zaregistrovaly.
* Nemůžete odstranit první fond uzlů.
* Nelze použít doplněk směrování aplikace HTTP.
* Nemůžete přidat nebo aktualizovat ani odstranit fondy uzlů pomocí existující šablony Správce prostředků jako u většiny operací. Místo toho [použijte šablonu samostatného správce prostředků](#manage-node-pools-using-a-resource-manager-template) k provádění změn v fondech uzlů v clusteru AKS.

I když je tato funkce ve verzi Preview, platí následující další omezení:

* Cluster AKS může mít maximálně osm fondů uzlů.
* Cluster AKS může mít maximálně 400 uzlů v těchto osmi fondech uzlů.
* Všechny fondy uzlů se musí nacházet ve stejné podsíti.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Začněte tím, že vytvoříte cluster AKS s jedním fondem uzlů. Následující příklad používá příkaz [AZ Group Create][az-group-create] k vytvoření skupiny prostředků s názvem *myResourceGroup* v oblasti *eastus* . Pomocí příkazu [AZ AKS Create][az-aks-create] se pak vytvoří cluster AKS s názvem *myAKSCluster* . A *--Kubernetes-verze* *1.13.10* se používá k zobrazení způsobu aktualizace fondu uzlů v následujícím kroku. Můžete zadat libovolnou [podporovanou verzi Kubernetes][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.10
```

Vytvoření clusteru bude trvat několik minut.

Až bude cluster připravený, pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] Získejte přihlašovací údaje clusteru pro použití s `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Přidat fond uzlů

Cluster vytvořený v předchozím kroku má fond s jedním uzlem. Přidejte druhý fond uzlů pomocí příkazu [AZ AKS nodepool Add][az-aks-nodepool-add] . Následující příklad vytvoří fond uzlů s názvem *mynodepool* , který spouští *3* uzly:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.12.7
```

Stav fondů uzlů zobrazíte pomocí příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] a zadáním vaší skupiny prostředků a názvu clusteru:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Následující příklad výstupu ukazuje, že *mynodepool* byl úspěšně vytvořen se třemi uzly ve fondu uzlů. Když se v předchozím kroku vytvořil cluster AKS, vytvořil se výchozí *nodepool1* s počtem uzlů *1*.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Pokud při přidávání fondu uzlů nejsou zadány žádné *OrchestratorVersion* ani *VmSize* , vytvoří se uzly na základě výchozích hodnot pro cluster AKS. V tomto příkladu bylo Kubernetes verze *1.13.10* a velikost uzlu *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Upgrade fondu uzlů

> [!NOTE]
> Operace upgradu a škálování v clusteru nebo ve fondu uzlů se vzájemně vylučují. Cluster ani fond uzlů nemůžete současně upgradovat a škálovat. Místo toho musí být každý typ operace dokončen u cílového prostředku před dalším požadavkem na stejný prostředek. Další informace najdete v našem [Průvodci odstraňováním potíží](https://aka.ms/aks-pending-upgrade).

Pokud byl cluster AKS vytvořen v prvním kroku, `--kubernetes-version` byl zadán parametr *1.13.10* . Tím se nastaví verze Kubernetes pro rovinu ovládacího prvku i pro počáteční fond uzlů. K dispozici jsou různé příkazy pro upgrade verze Kubernetes roviny ovládacího prvku a fondu uzlů. Příkaz se používá k upgradu roviny ovládacího prvku, `az aks nodepool upgrade` zatímco se používá k upgradu samostatného fondu uzlů. `az aks upgrade`

Pojďme upgradovat *mynodepool* na Kubernetes *1.13.10*. Pomocí příkazu [AZ AKS Node upgrade Pool][az-aks-nodepool-upgrade] upgradujte fond uzlů, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

> [!Tip]
> Chcete-li upgradovat rovinu ovládacího prvku `az aks upgrade -k 1.14.6`na *1.14.6*, spusťte.

Seznam stavů fondů uzlů znovu vypište pomocí příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] . Následující příklad ukazuje, že *mynodepool* je ve stavu *upgradu* na *1.13.10*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Upgrade uzlů na zadanou verzi trvá několik minut.

V rámci osvědčeného postupu byste měli upgradovat všechny fondy uzlů v clusteru AKS na stejnou verzi Kubernetes. Možnost upgradovat fondy jednotlivých uzlů vám umožní provést postupný upgrade a naplánovat mezi fondy uzlů, aby se zachovala doba provozu aplikace v rámci výše zmíněných omezení.

> [!NOTE]
> Kubernetes používá standardní [sémantickou](https://semver.org/) verzi schématu správy verzí. Číslo verze se vyjádří jako *x. y. z*, kde *x* je hlavní verze, *y* je podverze a *z* je verze opravy. Například ve verzi *1.12.6*je 1 hlavní verze, 12 je dílčí verze a 6 je verze opravy. Při vytváření clusteru je nastavená verze Kubernetes řídicí roviny i počáteční fond uzlů. Všechny další fondy uzlů mají svou verzi Kubernetes nastavenou při jejich přidání do clusteru. Verze Kubernetes se mohou lišit mezi fondy uzlů i mezi fondem uzlů a rovinou ovládacího prvku, ale platí následující omezení:
> 
> * Verze fondu uzlů musí mít stejnou hlavní verzi jako rovina ovládacího prvku.
> * Verze fondu uzlů může být jedna podverze nižší než verze řídicí roviny.
> * Verze fondu uzlů může být libovolná verze opravy, pokud jsou dodržena jiná dvě omezení.
> 
> Chcete-li upgradovat verzi Kubernetes řídicí plochy, použijte `az aks upgrade`. Pokud má cluster pouze jeden fond uzlů, `az aks upgrade` příkaz bude také upgradovat verzi Kubernetes fondu uzlů.

## <a name="scale-a-node-pool-manually"></a>Ruční škálování fondu uzlů

V případě změny požadavků na úlohy aplikace možná budete muset škálovat počet uzlů ve fondu uzlů. Počet uzlů lze škálovat směrem nahoru nebo dolů.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Pokud chcete škálovat počet uzlů ve fondu uzlů, použijte příkaz [AZ AKS Node Pool Scale][az-aks-nodepool-scale] . Následující příklad škáluje počet uzlů v *mynodepool* na *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Seznam stavů fondů uzlů znovu vypište pomocí příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] . Následující příklad ukazuje, že *mynodepool* je ve stavu *škálování* s novým počtem *5* uzlů:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Dokončení operace škálování trvá několik minut.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Automatické škálování určitého fondu uzlů povolením automatického škálování clusteru

AKS nabízí ve verzi Preview samostatnou funkci pro automatické škálování fondů uzlů pomocí funkce s názvem [AutoScale clusteru](cluster-autoscaler.md). Tato funkce je doplněk AKS, který se dá povolit pro fond uzlů s jedinečným minimálním a maximálním počtem škálování na jeden fond uzlů. Naučte se [používat automatické škálování clusteru pro každý fond uzlů](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Odstranění fondu uzlů

Pokud už fond nepotřebujete, můžete ho odstranit a odebrat příslušné uzly virtuálních počítačů. Pokud chcete odstranit fond uzlů, použijte příkaz [AZ AKS Node Pool Delete][az-aks-nodepool-delete] a zadejte název fondu uzlů. Následující příklad odstraní *mynoodepool* vytvořená v předchozích krocích:

> [!CAUTION]
> Neexistují žádné možnosti obnovení pro ztrátu dat, ke kterým může dojít při odstranění fondu uzlů. Pokud lusky není možné naplánovat na jiné fondy uzlů, nejsou tyto aplikace k dispozici. Ujistěte se, že neodstraníte fond uzlů v případě, že aplikace v aplikaci neobsahují zálohy dat nebo že je možné spustit na jiných fondech uzlů v clusteru.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Následující příklad výstupu příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] ukazuje, že *mynodepool* je ve stavu *odstranění* :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Odstranění uzlů a fondu uzlů trvá několik minut.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Určení velikosti virtuálního počítače pro fond uzlů

V předchozích příkladech vytvoření fondu uzlů se pro uzly vytvořené v clusteru použila výchozí velikost virtuálního počítače. Častější scénář je vytvořit fondy uzlů s různými velikostmi a možnostmi virtuálních počítačů. Můžete například vytvořit fond uzlů, který obsahuje uzly s velkými objemy procesoru nebo paměti, nebo fond uzlů, který poskytuje podporu GPU. V dalším kroku použijete k informování plánovače Kubernetes, jak omezit přístup k luskům, které se na těchto uzlech můžou spouštět, [pomocí chuti a tolerování](#schedule-pods-using-taints-and-tolerations) .

V následujícím příkladu vytvořte fond uzlů založený na GPU, který používá velikost virtuálního počítače *Standard_NC6* . Tyto virtuální počítače jsou napájené kartou NVIDIA Tesla K80. Informace o dostupných velikostech virtuálních počítačů najdete v tématu [velikosti pro virtuální počítače se systémem Linux v Azure][vm-sizes].

Vytvořte fond uzlů pomocí příkazu [AZ AKS Node Pool Add][az-aks-nodepool-add] . Tentokrát zadejte název *gpunodepool*a pomocí `--node-vm-size` parametru určete velikost *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Následující příklad výstupu příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] ukazuje, že *gpunodepool* *vytváří* uzly se zadaným *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Úspěšné vytvoření *gpunodepool* trvá několik minut.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Naplánování lusků pomocí chuti a tolerovánosti

Nyní máte v clusteru dva fondy uzlů – výchozí fond uzlů byl původně vytvořen a fond uzlů na bázi GPU. K zobrazení uzlů v clusteru použijte příkaz [kubectl Get Nodes][kubectl-get] . Následující příklad výstupu ukazuje jeden uzel v každém fondu uzlů:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

Plánovač Kubernetes může pomocí chuti a omezení omezit, jaké úlohy je možné spouštět na uzlech.

* Pro uzel, který indikuje, že je možné naplánovat pouze určité lusky, se použije značka **chuti** .
* **Tolerování** se pak použije na uzel pod, který umožňuje *tolerovat* chuti v uzlu.

Další informace o použití pokročilých Kubernetes naplánovaných funkcí najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][taints-tolerations] .

V tomto příkladu aplikujte na uzel založený na GPU pomocí příkazu [kubectl chuti uzel][kubectl-taint] . Z výstupu předchozího `kubectl get nodes` příkazu zadejte název uzlu založeného na GPU. Chuti se použije jako *klíč: hodnota* a pak možnost plánování. Následující příklad používá dvojici *SKU = GPU* a definuje lusky, jinak mají možnost neplánovat:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Následující základní příklad YAML manifestu používá tolerovat, aby mohl Plánovač Kubernetes spustit NGINX pod uzlem založeným na GPU. Pro přesnější, ale časově náročný příklad spuštění úlohy Tensorflow s datovou sadou MNIST ručně zapsaných najdete informace v tématu [použití GPU pro úlohy náročné na výpočetní výkon v AKS][gpu-cluster].

Vytvořte soubor s názvem `gpu-toleration.yaml` a zkopírujte ho do následujícího příkladu YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Naplánujte pod pomocí `kubectl apply -f gpu-toleration.yaml` příkazu:

```console
kubectl apply -f gpu-toleration.yaml
```

Naplánování seznamu pod a vyžádání image NGINX trvá několik sekund. Chcete-li zobrazit stav pod, použijte příkaz [kubectl popsat pod][kubectl-describe] . Následující zhuštěný příklad výstupu ukazuje, že se používá nedovolená *položka SKU = GPU:-Schedule* . V části s událostmi plánovači přiřadil uzel pod k uzlu založenému na procesoru *AKS-gpunodepool-28993262-vmss000000* :

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

V uzlech v *gpunodepool*se dají naplánovat jenom lusky, které mají tuto chuti. Jakékoli jiné pod by se naplánovaly ve fondu uzlů *nodepool1* . Pokud vytvoříte další fondy uzlů, můžete použít další příchuti a tolerování k omezení, které z nich je možné naplánovat na tyto prostředky uzlu.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Správa fondů uzlů pomocí šablony Správce prostředků

Když použijete šablonu Azure Resource Manager k vytváření a správě prostředků, můžete obvykle aktualizovat nastavení v šabloně a znovu nasadit, aby se prostředek aktualizoval. U fondů uzlů v AKS nelze počáteční profil fondu uzlů aktualizovat po vytvoření clusteru AKS. Toto chování znamená, že nemůžete aktualizovat existující šablonu Správce prostředků, provést změnu v fondech uzlů a znovu nasadit. Místo toho je nutné vytvořit samostatnou šablonu Správce prostředků, která aktualizuje pouze fondy agentů pro existující cluster AKS.

Vytvořte šablonu, například `aks-agentpools.json` a vložte následující vzorový manifest. Tato příklad šablony konfiguruje následující nastavení:

* Aktualizuje fond agentů pro *Linux* s názvem *myagentpool* , aby běžel tři uzly.
* Nastaví uzly ve fondu uzlů tak, aby běžely Kubernetes verze *1.13.10*.
* Definuje velikost uzlu jako *Standard_DS2_v2*.

Upravte tyto hodnoty podle potřeby, pokud potřebujete aktualizovat, přidat nebo odstranit fondy uzlů:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of your existing AKS cluster."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location of your existing AKS cluster."
      }
    },
    "agentPoolName": {
      "type": "string",
      "defaultValue": "myagentpool",
      "metadata": {
        "description": "The name of the agent pool to create or update."
      }
    },
    "vnetSubnetId": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The Vnet subnet resource ID for your existing AKS cluster."
      }
    }
  },
  "variables": {
    "apiVersion": {
      "aks": "2019-04-01"
    },
    "agentPoolProfiles": {
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
    }
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.ContainerService/managedClusters/agentPools",
      "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
      "location": "[parameters('location')]",
      "properties": {
            "maxPods": "[variables('agentPoolProfiles').maxPods]",
            "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
            "count": "[variables('agentPoolProfiles').agentCount]",
            "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
            "osType": "[variables('agentPoolProfiles').osType]",
            "storageProfile": "ManagedDisks",
      "type": "VirtualMachineScaleSets",
            "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
            "orchestratorVersion": "1.13.10"
      }
    }
  ]
}
```

Tuto šablonu nasaďte pomocí příkazu [AZ Group Deployment Create][az-group-deployment-create] , jak je znázorněno v následujícím příkladu. Zobrazí se výzva k zadání názvu a umístění pro existující cluster AKS:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Aktualizace clusteru AKS může trvat několik minut v závislosti na nastaveních fondu uzlů a operacích, které definujete v šabloně Správce prostředků.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Přiřazení veřejné IP adresy na uzel v rámci fondu uzlů

> [!NOTE]
> Během období Preview existuje omezení používání této funkce s *Standard Load BALANCER SKU v AKS (Preview) v* důsledku toho, že pravidla nástroje pro vyrovnávání zatížení jsou v konfliktu s ZŘIZOVÁNÍM virtuálních počítačů. I když je ve verzi Preview, použijte *skladové položky Basic Load Balancer* , pokud potřebujete přiřadit veřejnou IP adresu na uzel.

AKS uzly nevyžadují pro komunikaci své vlastní veřejné IP adresy. Některé scénáře ale můžou vyžadovat, aby uzly ve fondu uzlů měly své vlastní veřejné IP adresy. Příkladem je hraní her, kde konzola potřebuje vytvořit přímé připojení k virtuálnímu počítači v cloudu, aby se minimalizovaly segmenty směrování. To je možné dosáhnout registrací pro samostatnou funkci verze Preview, veřejnou IP adresou uzlu (Preview).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Po úspěšné registraci nasaďte šablonu Azure Resource Manager podle [výše](#manage-node-pools-using-a-resource-manager-template) uvedených pokynů a přidejte do agentPoolProfiles následující logickou hodnotu "enableNodePublicIP". Nastavte tuto hodnotu `true` na jako výchozí `false` nastavení, pokud není zadané. Toto je vlastnost pouze pro dobu vytváření a vyžaduje minimální verzi rozhraní API 2019-06-01. Tato možnost se dá použít pro fondy uzlů pro Linux i Windows.

```
"agentPoolProfiles":[  
    {  
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]",
      "enableNodePublicIP":true
    }
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jste vytvořili cluster AKS, který obsahuje uzly založené na GPU. Pokud chcete snížit zbytečné náklady, můžete odstranit *gpunodepool*nebo celý cluster AKS.

Pokud chcete odstranit fond uzlů na bázi GPU, použijte příkaz [AZ AKS nodepool Delete][az-aks-nodepool-delete] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Pokud chcete samotný cluster odstranit, odstraňte skupinu prostředků AKS pomocí příkazu [AZ Group Delete][az-group-delete] :

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vytvořit a spravovat více fondů uzlů v clusteru AKS. Další informace o tom, jak ovládat lusky napříč fondy uzlů, najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][operator-best-practices-advanced-scheduler].

Informace o vytváření a používání fondů uzlů kontejnerů Windows serveru najdete v tématu [vytvoření kontejneru Windows serveru v AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
