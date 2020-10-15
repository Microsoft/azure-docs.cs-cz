---
title: Použití více fondů uzlů ve službě Azure Kubernetes Service (AKS)
description: Naučte se vytvářet a spravovat fondy více uzlů pro cluster ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 024b7adb254980ec87084b4794a9ced3eaea95eb
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074511"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Vytvoření a správa více fondů uzlů pro cluster ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) jsou uzly stejné konfigurace seskupeny dohromady do *fondů uzlů*. Tyto fondy uzlů obsahují základní virtuální počítače, na kterých běží vaše aplikace. Počáteční počet uzlů a jejich velikost (SKU) je definován při vytváření clusteru AKS, který vytváří [fond uzlů systému][use-system-pool]. Pokud chcete podporovat aplikace, které mají různé výpočetní prostředky nebo požadavky na úložiště, můžete vytvořit další *fondy uživatelských uzlů*. Fondy systémových uzlů slouží jako primární účel hostování důležitých systémových lusků, jako jsou například CoreDNS a tunnelfront. Fondy uživatelských uzlů slouží jako primární účel hostování aplikace. V případě, že chcete mít v clusteru AKS jenom jeden fond, je ale možné naplánovat použití lusků na uzlech systému. Fondy uživatelských uzlů jsou tam, kde umístíte jednotlivé lusky pro jednotlivé aplikace. Pomocí těchto dalších fondů uživatelských uzlů můžete například poskytnout GPU pro aplikace náročné na výpočetní výkon nebo přístup k vysoce výkonnému úložišti SSD.

> [!NOTE]
> Tato funkce umožňuje vyšší kontrolu nad tím, jak vytvořit a spravovat více fondů uzlů. V důsledku toho jsou pro vytvoření, aktualizaci nebo odstranění vyžadovány samostatné příkazy. Dříve clusterové operace prostřednictvím `az aks create` nebo `az aks update` používaly rozhraní managedCluster API a byly jedinou možností, jak změnit plochu ovládacího prvku a jeden fond uzlů. Tato funkce zpřístupňuje samostatnou sadu operací pro fondy agentů prostřednictvím rozhraní neznámá API a vyžaduje použití `az aks nodepool` sady příkazů ke spouštění operací ve fondu jednotlivých uzlů.

V tomto článku se dozvíte, jak vytvořit a spravovat více fondů uzlů v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují více fondů uzlů, platí následující omezení:

* Podívejte se [na téma kvóty, omezení velikosti virtuálních počítačů a dostupnost oblasti ve službě Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Můžete odstranit fondy systémových uzlů, pokud máte jiný fond uzlů systému, aby bylo možné provést místo v clusteru AKS.
* Systémové fondy musí obsahovat alespoň jeden uzel a fondy uživatelských uzlů můžou obsahovat nula nebo víc uzlů.
* Cluster AKS musí používat standardní nástroj pro vyrovnávání zatížení SKU pro použití více fondů uzlů, ale funkce nástroje pro vyrovnávání zatížení Basic SKU není podporována.
* Cluster AKS musí pro uzly používat sadu škálování virtuálních počítačů.
* Název fondu uzlů může obsahovat jenom malé alfanumerické znaky a musí začínat malým písmenem. U fondů uzlů se systémem Linux musí být délka v rozmezí od 1 do 12 znaků, v případě fondů uzlů systému Windows musí být délka mezi 1 a 6 znaky.
* Všechny fondy uzlů se musí nacházet ve stejné virtuální síti.
* Při vytváření více fondů uzlů v době vytváření clusteru se musí všechny verze Kubernetes používané fondy uzlů shodovat s verzí nastavenou pro plochu ovládacího prvku. Dá se aktualizovat po zřízení clusteru pomocí operací pro fond uzlů.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

> [!Important]
> Pokud pro cluster AKS spustíte jeden fond uzlů systému v produkčním prostředí, doporučujeme pro fond uzlů použít aspoň tři uzly.

Začněte tím, že vytvoříte cluster AKS s jedním fondem uzlů. Následující příklad používá příkaz [AZ Group Create][az-group-create] k vytvoření skupiny prostředků s názvem *myResourceGroup* v oblasti *eastus* . Pomocí příkazu [AZ AKS Create][az-aks-create] se pak vytvoří cluster AKS s názvem *myAKSCluster* .

> [!NOTE]
> SKU nástroje Load Balancer úrovně *Basic* není při použití více fondů uzlů **podporována** . Ve výchozím nastavení se clustery AKS s využitím služby Load Balancer *úrovně Standard* (SKU) z Azure CLI a Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

Vytvoření clusteru bude trvat několik minut.

> [!NOTE]
> Aby se zajistilo, že váš cluster funguje spolehlivě, měli byste spustit aspoň 2 (dva) uzly ve výchozím fondu uzlů, protože v rámci tohoto fondu uzlů běží základní systémové služby.

Až bude cluster připravený, pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] Získejte přihlašovací údaje clusteru pro použití s `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Přidání fondu uzlů

Cluster vytvořený v předchozím kroku má fond s jedním uzlem. Přidejte druhý fond uzlů pomocí příkazu [AZ AKS nodepool Add][az-aks-nodepool-add] . Následující příklad vytvoří fond uzlů s názvem *mynodepool* , který spouští *3* uzly:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Název fondu uzlů musí začínat malým písmenem a může obsahovat jenom alfanumerické znaky. U fondů uzlů se systémem Linux musí být délka v rozmezí od 1 do 12 znaků, v případě fondů uzlů systému Windows musí být délka mezi 1 a 6 znaky.

Stav fondů uzlů zobrazíte pomocí příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] a zadáním vaší skupiny prostředků a názvu clusteru:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Následující příklad výstupu ukazuje, že *mynodepool* byl úspěšně vytvořen se třemi uzly ve fondu uzlů. Když se v předchozím kroku vytvořil cluster AKS, vytvořil se výchozí *nodepool1* s počtem uzlů *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Pokud při přidávání fondu uzlů není zadaný žádný *VmSize* , výchozí velikost se *Standard_D2s_v3* pro fondy uzlů Windows a *Standard_DS2_v2* pro fondy uzlů Linux. Pokud není zadán žádný *OrchestratorVersion* , použije se ve výchozím nastavení stejná verze jako plocha ovládacího prvku.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Přidat fond uzlů s jedinečnou podsítí (Preview)

Zatížení může vyžadovat rozdělení uzlů clusteru do samostatných fondů pro logickou izolaci. Tuto izolaci je možné podporovat samostatnými podsítěmi, které jsou vyhrazeny pro každý fond uzlů v clusteru. To může adresovat požadavky, jako je například nesouvislý adresní prostor virtuální sítě pro rozdělení mezi fondy uzlů.

#### <a name="limitations"></a>Omezení

* Všechny podsítě přiřazené k nodepools musí patřit do stejné virtuální sítě.
* Systémové lusky musí mít přístup ke všem uzlům v clusteru, aby poskytovaly důležité funkce, jako je například překlad DNS prostřednictvím coreDNS.
* Přiřazení jedinečné podsítě na jeden fond uzlů je ve verzi Preview omezené na Azure CNI.
* Použití zásad sítě s jedinečnou podsítí na jeden fond uzlů není ve verzi Preview podporováno.

Pokud chcete vytvořit fond uzlů s vyhrazenou podsítí, předejte ID prostředku podsítě jako další parametr při vytváření fondu uzlů.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Upgrade fondu uzlů

> [!NOTE]
> Operace upgradu a škálování na clusteru nebo ve fondu uzlů se nemůžou vyskytovat současně, pokud se k chybě vrátí. Místo toho musí být každý typ operace dokončen u cílového prostředku před dalším požadavkem na stejný prostředek. Další informace najdete v našem [Průvodci odstraňováním potíží](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade).

Příkazy v této části vysvětlují, jak upgradovat jeden konkrétní fond uzlů. Vztah mezi upgradem verze Kubernetes roviny ovládacího prvku a fondem uzlů je vysvětlen v [níže uvedené části](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Verze bitové kopie operačního systému fondu uzlů je svázána s verzí Kubernetes clusteru. Po upgradu clusteru budete dostávat jenom upgrady imagí operačního systému.

Vzhledem k tomu, že v tomto příkladu existují dva fondy uzlů, je pro upgrade fondu uzlů nutné použít příkaz [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] . Pokud chcete zobrazit dostupné upgrady, použijte [příkaz AZ AKS Get-Upgrades][az-aks-get-upgrades] .

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Pojďme upgradovat na *mynodepool*. Pomocí příkazu [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] provedete upgrade fondu uzlů, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Seznam stavů fondů uzlů znovu vypište pomocí příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] . Následující příklad ukazuje, že *mynodepool* je ve stavu *upgradu* na *KUBERNETES_VERSION*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Upgrade uzlů na zadanou verzi trvá několik minut.

V rámci osvědčeného postupu byste měli upgradovat všechny fondy uzlů v clusteru AKS na stejnou verzi Kubernetes. Výchozím chováním `az aks upgrade` je upgrade všech fondů uzlů společně s řídicí rovinou, aby bylo možné toto zarovnání dosáhnout. Možnost upgradovat fondy jednotlivých uzlů vám umožní provést postupný upgrade a naplánovat mezi fondy uzlů, aby se zachovala doba provozu aplikace v rámci výše zmíněných omezení.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Upgrade řídicí plochy clusteru s více fondy uzlů

> [!NOTE]
> Kubernetes používá standardní [sémantickou](https://semver.org/) verzi schématu správy verzí. Číslo verze se vyjádří jako *x. y. z*, kde *x* je hlavní verze, *y* je podverze a *z* je verze opravy. Například ve verzi *1.12.6*je 1 hlavní verze, 12 je dílčí verze a 6 je verze opravy. Při vytváření clusteru je nastavená verze Kubernetes řídicí roviny a počáteční fond uzlů. Všechny další fondy uzlů mají svou verzi Kubernetes nastavenou při jejich přidání do clusteru. Verze Kubernetes se mohou lišit mezi fondy uzlů i mezi fondem uzlů a rovinou ovládacího prvku.

Cluster AKS má dva objekty prostředků clusteru s přidruženými verzemi Kubernetes.

1. Verze Kubernetes roviny ovládacího prvku clusteru
2. Fond uzlů s verzí Kubernetes

Rovina ovládacího prvku se mapuje na jeden nebo více fondů uzlů. Chování operace upgradu závisí na použitém příkazu rozhraní příkazového řádku Azure.

Upgrade roviny ovládacího prvku AKS vyžaduje použití `az aks upgrade` . Tento příkaz provede upgrade verze řídicí roviny a všech fondů uzlů v clusteru.

Vydání `az aks upgrade` příkazu s `--control-plane-only` příznakem upgradu pouze na rovinu řízení clusteru. Žádný z přidružených fondů uzlů v clusteru se nemění.

Upgrade jednotlivých fondů uzlů vyžaduje použití `az aks nodepool upgrade` . Tento příkaz upgraduje pouze cílový fond uzlů s určenou verzí Kubernetes.

### <a name="validation-rules-for-upgrades"></a>Ověřovací pravidla pro upgrady

Platné upgrady Kubernetes pro řídicí plochu clusteru a fondy uzlů jsou ověřovány následujícími sadami pravidel.

* Pravidla pro platné verze pro upgrade fondů uzlů:
   * Verze fondu uzlů musí mít stejnou *Hlavní* verzi jako rovina ovládacího prvku.
   * Podverze fondu *uzlů* musí být *ve dvou podverzích verze řídicí* roviny.
   * Verze fondu uzlů nemůže být větší než verze ovládacího prvku `major.minor.patch` .

* Pravidla pro odeslání operace upgradu:
   * Nelze downgradovat plochu ovládacího prvku nebo Kubernetes verzi fondu uzlů.
   * Pokud není zadána verze Kubernetes fondu uzlů, závisí chování na používaném klientovi. Deklarace v šablonách Správce prostředků se vrátí k existující verzi definované pro fond uzlů, pokud se použije, pokud není nastavená žádná hodnota řídicí roviny, která se použije k vrácení zpět.
   * Můžete buď upgradovat, nebo škálovat plochu ovládacího prvku nebo fondu uzlů v daném čase, nemůžete současně odeslat více operací na jeden řídicí rovinu nebo prostředek fondu uzlů.

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

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
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

AKS nabízí samostatnou funkci pro automatické škálování fondů uzlů pomocí funkce s názvem [AutoScale clusteru](cluster-autoscaler.md). Tato funkce se dá povolit pro fond uzlů s jedinečným minimálním a maximálním počtem škálování na jeden fond uzlů. Naučte se [používat automatické škálování clusteru pro každý fond uzlů](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Odstranění fondu uzlů

Pokud už fond nepotřebujete, můžete ho odstranit a odebrat příslušné uzly virtuálních počítačů. Pokud chcete odstranit fond uzlů, použijte příkaz [AZ AKS Node Pool Delete][az-aks-nodepool-delete] a zadejte název fondu uzlů. Následující příklad odstraní *mynoodepool* vytvořená v předchozích krocích:

> [!CAUTION]
> Neexistují žádné možnosti obnovení pro ztrátu dat, ke kterým může dojít při odstranění fondu uzlů. Pokud lusky není možné naplánovat na jiné fondy uzlů, nejsou tyto aplikace k dispozici. Ujistěte se, že neodstraníte fond uzlů v případě, že aplikace v aplikaci neobsahují zálohy dat nebo že je možné spustit na jiných fondech uzlů v clusteru.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Následující příklad výstupu příkazu [AZ AKS Node Pool list][az-aks-nodepool-list] ukazuje, že *mynodepool* je ve stavu *odstranění* :

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
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

Vytvořte fond uzlů pomocí příkazu [AZ AKS Node Pool Add][az-aks-nodepool-add] . Tentokrát zadejte název *gpunodepool*a `--node-vm-size` parametr použijte k určení velikosti *Standard_NC6* :

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

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
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

Nyní máte v clusteru dva fondy uzlů – výchozí fond uzlů byl původně vytvořen a fond uzlů na bázi GPU. K zobrazení uzlů v clusteru použijte příkaz [kubectl Get Nodes][kubectl-get] . Následující příklad výstupu ukazuje uzly:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Plánovač Kubernetes může pomocí chuti a omezení omezit, jaké úlohy je možné spouštět na uzlech.

* Pro uzel, který indikuje, že je možné naplánovat pouze určité lusky, se použije značka **chuti** .
* **Tolerování** se pak použije na uzel pod, který umožňuje *tolerovat* chuti v uzlu.

Další informace o použití pokročilých Kubernetes naplánovaných funkcí najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][taints-tolerations] .

V tomto příkladu aplikujte na uzel založený na GPU pomocí příkazu--Node-chutis hodnotu chuti. Z výstupu předchozího příkazu zadejte název uzlu založeného na GPU `kubectl get nodes` . Hodnota chuti se aplikuje jako dvojice *klíč = hodnota* a pak možnost plánování. Následující příklad používá dvojici *SKU = GPU* a definuje lusky, jinak mají možnost *neplánovat* :

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
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
kubectl describe pod mypod
```

```output
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

V uzlech v *gpunodepool*se dají naplánovat jenom lusky, které mají tuto tolerovánost nastavenou. Jakékoli jiné pod by se naplánovaly ve fondu uzlů *nodepool1* . Pokud vytvoříte další fondy uzlů, můžete použít další příchuti a tolerování k omezení, které z nich je možné naplánovat na tyto prostředky uzlu.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Určení značky, značky nebo značky pro fond uzlů

### <a name="setting-nodepool-taints"></a>Nastavení nodepool chuti

Při vytváření fondu uzlů můžete do tohoto fondu uzlů přidat značky, popisky nebo značky. Když přidáte značku, popisek nebo značku, budou mít všechny uzly v tomto fondu uzlů také tuto značku, popisek nebo značku.

Chcete-li vytvořit fond uzlů s příchuti, použijte příkaz [AZ AKS nodepool Add][az-aks-nodepool-add]. Zadejte název *taintnp* a použijte `--node-taints` parametr k určení *SKU = GPU: inschedule* pro chuti.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Soubor. chuti lze nastavit pouze pro fondy uzlů během vytváření fondu uzlů.

Následující příklad výstupu příkazu [AZ AKS nodepool list][az-aks-nodepool-list] ukazuje, že *taintnp* *vytváří* uzly se zadaným *nodeTaints*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Informace o chuti jsou viditelné v Kubernetes pro zpracování pravidel plánování pro uzly.

### <a name="setting-nodepool-labels"></a>Nastavení popisků nodepool

Při vytváření fondu uzlů můžete do fondu uzlů přidat také popisky. Popisky nastavené ve fondu uzlů se přidají do každého uzlu ve fondu uzlů. Tyto [popisky jsou viditelné v Kubernetes][kubernetes-labels] pro zpracování pravidel plánování pro uzly.

Chcete-li vytvořit fond uzlů s popiskem, použijte příkaz [AZ AKS nodepool Add][az-aks-nodepool-add]. Zadejte název *labelnp* a použijte `--labels` parametr pro určení *oddělení = IT* a *CostCenter = 9999* pro popisky.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Popisek lze nastavit pouze pro fondy uzlů během vytváření fondu uzlů. Popisky musí také obsahovat dvojici klíč/hodnota a mít [platnou syntaxi][kubernetes-label-syntax].

Následující příklad výstupu příkazu [AZ AKS nodepool list][az-aks-nodepool-list] ukazuje, že *labelnp* *vytváří* uzly se zadaným *nodeLabels*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Nastavení značek Azure nodepool

V clusteru AKS můžete použít značku Azure na fondy uzlů. Značky použité pro fond uzlů se aplikují na každý uzel v rámci fondu uzlů a ukládají se prostřednictvím upgradů. Značky jsou také aplikovány na nové uzly přidané do fondu uzlů během operace škálování na více instancí. Přidání značky může pomáhat s úkoly, jako je sledování zásad nebo odhad nákladů.

Značky Azure obsahují klíče, které rozlišují velká a malá písmena pro operace, jako je například při načítání značky, hledáním klíče. V takovém případě bude značka s daným klíčem aktualizována nebo načtena bez ohledu na velikost písmen. Hodnoty značek rozlišují velká a malá písmena.

V AKS platí, že pokud je více značek nastaveno s identickými klíči, ale různými velikostmi písmen, použije se první tag v abecedním pořadí. Například výsledky se zadávají `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` `Key1` a `val1` nastavují.

Vytvořte fond uzlů pomocí direktivy [AZ AKS nodepool Add][az-aks-nodepool-add]. Zadejte název *tagnodepool* a použijte `--tag` parametr pro určení *oddělení = IT* a *CostCenter = 9999* pro značky.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Parametr můžete použít také `--tags` při použití příkazu [AZ AKS nodepool Update][az-aks-nodepool-update] a při vytváření clusteru. Při vytváření clusteru `--tags` použije parametr značku na počáteční fond uzlů vytvořený s clusterem. Všechny názvy značek musí vyhovovat omezením v [použití značek k uspořádání prostředků Azure][tag-limitation]. Aktualizace fondu uzlů s `--tags` parametrem aktualizuje všechny existující hodnoty značek a připojí všechny nové značky. Například pokud váš fond uzlů měl *oddělení = IT* a *CostCenter = 9999* pro značky a Vy jste ho aktualizovali pomocí *Team = dev* a *CostCenter = 111* for Tags, budete nodepool mít *oddělení = IT*, *CostCenter = 111*a *Team = dev* for Tags.

Následující příklad výstupu příkazu [AZ AKS nodepool list][az-aks-nodepool-list] ukazuje, že *tagnodepool* *vytváří* uzly se zadanou *značkou*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Správa fondů uzlů pomocí šablony Správce prostředků

Když použijete šablonu Azure Resource Manager k vytváření a správě prostředků, můžete obvykle aktualizovat nastavení v šabloně a znovu nasadit, aby se prostředek aktualizoval. U fondů uzlů v AKS nelze počáteční profil fondu uzlů aktualizovat po vytvoření clusteru AKS. Toto chování znamená, že nemůžete aktualizovat existující šablonu Správce prostředků, provést změnu v fondech uzlů a znovu nasadit. Místo toho je nutné vytvořit samostatnou šablonu Správce prostředků, která aktualizuje pouze fondy uzlů pro existující cluster AKS.

Vytvořte šablonu, například `aks-agentpools.json` a vložte následující vzorový manifest. Tato příklad šablony konfiguruje následující nastavení:

* Aktualizuje fond uzlů pro *Linux* s názvem *myagentpool* , aby se spouštěly tři uzly.
* Nastaví uzly ve fondu uzlů tak, aby běžely Kubernetes verze *1.15.7*.
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
            "aks": "2020-01-01"
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
            "apiVersion": "2020-01-01",
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
                "orchestratorVersion": "1.15.7"
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

> [!TIP]
> Do fondu uzlů můžete přidat značku přidáním vlastnosti *tag* do šablony, jak je znázorněno v následujícím příkladu.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Aktualizace clusteru AKS může trvat několik minut v závislosti na nastaveních fondu uzlů a operacích, které definujete v šabloně Správce prostředků.

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>Přiřazení veřejné IP adresy na uzel pro vaše fondy uzlů (Preview)

> [!WARNING]
> Abyste mohli používat funkci veřejné IP adresy na uzel, musíte nainstalovat rozšíření CLI Preview 0.4.43 nebo novější.

AKS uzly nevyžadují pro komunikaci své vlastní veřejné IP adresy. Scénáře ale můžou vyžadovat, aby uzly ve fondu uzlů přijímaly vlastní vyhrazené veřejné IP adresy. Běžným scénářem je použití herních úloh, kde konzola potřebuje vytvořit přímé připojení k virtuálnímu počítači v cloudu, aby se minimalizovaly segmenty směrování. Tento scénář je možné dosáhnout v AKS registrací pro funkci verze Preview, veřejné IP adresy uzlu (Preview).

K instalaci a aktualizaci nejnovějšího rozšíření AKS-Preview použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

Zaregistrujte funkci veřejné IP adresy uzlu pomocí následujícího příkazu Azure CLI:

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
Registrace funkce může trvat několik minut.  Stav můžete zjistit pomocí následujícího příkazu:

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

Po úspěšné registraci vytvořte novou skupinu prostředků.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Vytvořte nový cluster AKS a připojte veřejnou IP adresu pro vaše uzly. Každý uzel ve fondu uzlů obdrží jedinečnou veřejnou IP adresu. Můžete to ověřit tak, že prohlížíte instance sady škálování virtuálních počítačů.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Pro existující clustery AKS můžete také přidat nový fond uzlů a pro své uzly připojit veřejnou IP adresu.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> Během období Preview Azure Instance Metadata Service v současné době nepodporuje načítání veřejných IP adres pro SKU virtuálních počítačů úrovně Standard. Z důvodu tohoto omezení nemůžete použít příkazy kubectl k zobrazení veřejných IP adres přiřazených k uzlům. IP adresy se ale přiřazují a fungují tak, jak mají. Veřejné IP adresy pro vaše uzly jsou připojené k instancím ve vaší sadě škálování virtuálních počítačů.

Veřejné IP adresy pro uzly můžete vyhledat různými způsoby:

* Použití příkazu příkazového řádku Azure [AZ VMSS list-instance-Public-IP][az-list-ips]
* Použijte [Příkazy PowerShellu nebo bash][vmss-commands]. 
* Veřejné IP adresy můžete zobrazit také v Azure Portal zobrazením instancí v sadě škálování virtuálního počítače.

> [!Important]
> [Skupina prostředků uzlu][node-resource-group] obsahuje uzly a jejich veřejné IP adresy. Použijte skupinu prostředků uzlu při provádění příkazů k vyhledání veřejných IP adres pro vaše uzly.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
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

Můžete také odstranit další cluster, který jste vytvořili pro veřejnou IP adresu pro fondy uzlů.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [fondech uzlů systému][use-system-pool].

V tomto článku jste zjistili, jak vytvořit a spravovat více fondů uzlů v clusteru AKS. Další informace o tom, jak ovládat lusky napříč fondy uzlů, najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][operator-best-practices-advanced-scheduler].

Informace o vytváření a používání fondů uzlů kontejnerů Windows serveru najdete v tématu [vytvoření kontejneru Windows serveru v AKS][aks-windows].

Používejte [skupiny umístění pro Proximity][reduce-latency-ppg] , abyste snížili latenci pro aplikace AKS.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md