---
title: Použití fondů více uzlů ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit a spravovat fondy více uzlů pro cluster ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 607419787bc0bab243d6cc2b8cbaa0ec22921e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422319"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Vytvoření a správa fondů více uzlů pro cluster ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) jsou uzly stejné konfigurace seskupeny do *fondů uzlů*. Tyto fondy uzlů obsahují základní virtuální počítače, které spouštějí vaše aplikace. Počáteční počet uzlů a jejich velikost (SKU) je definována při vytváření clusteru AKS, který vytvoří *výchozí fond uzlů*. Chcete-li podporovat aplikace, které mají různé požadavky na výpočetní prostředky nebo úložiště, můžete vytvořit další fondy uzlů. Tyto další fondy uzlů můžete například použít k poskytování grafických procesorů pro aplikace náročné na výpočetní výkon nebo přístupu k vysoce výkonnému úložišti SSD.

> [!NOTE]
> Tato funkce umožňuje vyšší kontrolu nad tím, jak vytvořit a spravovat více fondů uzlů. V důsledku toho jsou pro vytvoření/aktualizaci/odstranění vyžadovány samostatné příkazy. Dříve clusterové `az aks create` `az aks update` operace prostřednictvím nebo používal spravované clusterapi a byly jedinou možností změnit rovinu ovládacího prvku a jeden uzel fondu. Tato funkce zveřejňuje samostatnou sadu operací pro fondy agentů prostřednictvím rozhraní API agentPool a vyžaduje použití `az aks nodepool` sady příkazů ke spuštění operací v fondu jednotlivých uzlů.

Tento článek ukazuje, jak vytvořit a spravovat více fondů uzlů v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="limitations"></a>Omezení

Následující omezení platí při vytváření a správě clusterů AKS, které podporují více fondů uzlů:

* Viz [Kvóty, omezení velikosti virtuálního počítače a dostupnost oblasti ve službě Azure Kubernetes Service (AKS).][quotas-skus-regions]
* Fond systémových uzlů nelze odstranit, ve výchozím nastavení fond prvních uzlů.
* Cluster AKS musí používat standardní správce zatížení sku používat více fondů uzlů, funkce není podporována základní mise pro vyrovnávání zatížení skladových položk.
* Cluster AKS musí používat škálovací sady virtuálních strojů pro uzly.
* Název fondu uzlů může obsahovat pouze malá alfanumerická písmena a musí začínat s malou písmena. U fondů uzlů Linux musí být délka mezi 1 a 12 znaky, pro fondy uzlů systému Windows musí být délka mezi 1 a 6 znaky.
* Všechny fondy uzlů musí být umístěny ve stejné virtuální síti.
* Při vytváření více fondů uzlů v době vytváření clusteru musí všechny verze Kubernetes používané fondy uzlů odpovídat verzi nastavené pro rovinu ovládacího prvku. To lze aktualizovat po zřízení clusteru pomocí operací fondu uzlů.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Chcete-li začít, vytvořte cluster AKS s fondem jednoho uzlu. Následující příklad používá příkaz [az group create][az-group-create] k vytvoření skupiny prostředků s názvem *myResourceGroup* v oblasti *eastus.* Cluster AKS s názvem *myAKSCluster* je pak vytvořen pomocí příkazu [az aks create.][az-aks-create] A *--kubernetes-version* *1.15.7* se používá k zobrazení způsobu aktualizace fondu uzlů v následujícím kroku. Můžete zadat libovolnou [podporovanou verzi Kubernetes][supported-versions].

> [!NOTE]
> *Základní* skladová položka pro vyrovnávání zatížení **není podporována** při použití více fondů uzlů. Ve výchozím nastavení se clustery AKS vytvářejí pomocí *sku standardního* správce zatížení z Azure CLI a portálu Azure.

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
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

Vytvoření clusteru bude trvat několik minut.

> [!NOTE]
> Chcete-li zajistit, aby váš cluster fungoval spolehlivě, měli byste spustit alespoň 2 (dva) uzly ve výchozím fondu uzlů, protože základní systémové služby běží přes tento fond uzlů.

Když je cluster připraven, použijte příkaz [az aks get-credentials][az-aks-get-credentials] k `kubectl`získání pověření clusteru pro použití s :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Přidání fondu uzlů

Cluster vytvořený v předchozím kroku má fond jednoho uzlu. Přidáme druhý fond uzlů pomocí příkazu [az aks nodepool add.][az-aks-nodepool-add] Následující příklad vytvoří fond uzlů s názvem *mynodepool,* který běží *3* uzly:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> Název fondu uzlů musí začínat s velkým písmenem a může obsahovat pouze alfanumerické znaky. U fondů uzlů Linux musí být délka mezi 1 a 12 znaky, pro fondy uzlů systému Windows musí být délka mezi 1 a 6 znaky.

Chcete-li zobrazit stav fondů uzlů, použijte příkaz [seznamu seznamu fondu uzlů aks][az-aks-nodepool-list] a zadejte skupinu prostředků a název clusteru:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Následující příklad výstup ukazuje, že *mynodepool* byl úspěšně vytvořen se třemi uzly ve fondu uzlů. Při vytvoření clusteru AKS v předchozím kroku byl vytvořen výchozí *uzel1* s počtem uzlů *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
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
> Pokud při přidání fondu uzlů není zadán žádný *formát VmSize,* je výchozí velikost *Standard_DS2_v3* pro fondy uzlů systému Windows a *Standard_DS2_v2* pro fondy uzlů Linuxu. Pokud není zadán a) *orchestratorVersion,* výchozí verze jako rovina ovládacího prvku.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Přidání fondu uzlů s jedinečnou podsítí (náhled)

Úloha může vyžadovat rozdělení uzlů clusteru do samostatných fondů pro logickou izolaci. Tato izolace může být podporována samostatnými podsítěmi vyhrazenými pro každý fond uzlů v clusteru. To může řešit požadavky, jako je například nesouvislé virtuální sítě adresní prostor rozdělit mezi fondy uzlů.

#### <a name="limitations"></a>Omezení

* Všechny podsítě přiřazené k utržení musí patřit do stejné virtuální sítě.
* Systémové pody musí mít přístup ke všem uzlům v clusteru, aby poskytovaly důležité funkce, jako je například rozlišení DNS prostřednictvím služby coreDNS.
* Přiřazení jedinečné podsítě na fond uzlů je omezené na Azure CNI během náhledu.
* Použití zásad sítě s jedinečnou podsítí na fond uzlů není během náhledu podporováno.

Chcete-li vytvořit fond uzlů s vyhrazenou podsítí, předajte ID prostředku podsítě jako další parametr při vytváření fondu uzlů.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Upgrade fondu uzlů

> [!NOTE]
> Upgrade a škálování operací ve fondu clusteru nebo uzlu nemůže dojít současně, pokud je vrácena chyba. Místo toho musí každý typ operace dokončit cílový prostředek před dalším požadavkem na stejný prostředek. Přečtěte si více o tom na řešení [problémů průvodce](https://aka.ms/aks-pending-upgrade).

Při prvním kroku byl vytvořen cluster AKS, `--kubernetes-version` byl zadán soubor *1.15.7.* Tím nastavíte verzi Kubernetes pro řídicí rovinu i výchozí fond uzlů. Příkazy v této části vysvětlují, jak upgradovat fond jednoho konkrétního uzlu.

Vztah mezi upgradem verze Kubernetes řídicí roviny a fondu uzlů je vysvětlen v [části níže](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Verze bitové kopie operačního systému fondu uzlů je svázána s verzí clusteru Kubernetes. Po upgradu clusteru získáte pouze upgrady bitových bitových obrázků operačního systému.

Vzhledem k tomu, že existují dva fondy uzlů v tomto příkladu, musíme použít [upgrade uzlu az aks][az-aks-nodepool-upgrade] k upgradu fondu uzlů. Pojďme upgradovat *mynodepool* na Kubernetes *1.15.7*. Pomocí příkazu [az aks nodepool upgrade][az-aks-nodepool-upgrade] fondu uzlů, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Seznam stavu fondů uzlů znovu pomocí příkazu [seznamu seznamu az aks uzlu.][az-aks-nodepool-list] Následující příklad ukazuje, že *mynodepool* je ve stavu *Upgrade* na *1.15.7*:

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
    "orchestratorVersion": "1.15.7",
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

Jako osvědčený postup byste měli upgradovat všechny fondy uzlů v clusteru AKS na stejnou verzi Kubernetes. Výchozí chování `az aks upgrade` je inovovat všechny fondy uzlů spolu s rovinou ovládacího prvku k dosažení tohoto zarovnání. Možnost upgradovat fondy jednotlivých uzlů umožňuje provádět postupnou inovaci a plánovat pody mezi fondy uzlů, aby byla zachována doba provozu aplikace v rámci výše uvedených omezení.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Upgrade roviny řízení clusteru s více fondy uzlů

> [!NOTE]
> Kubernetes používá standardní schéma [samantického správu verzí.](https://semver.org/) Číslo verze je vyjádřeno jako *x.y.z*, kde *x* je hlavní verze, *y* je dílčí verze a *z* je verze opravy. Například ve verzi *1.12.6*je 1 hlavní verze, 12 je dílčí verze a 6 je verze opravy. Kubernetes verze roviny ovládacího prvku a počáteční uzel fondu jsou nastaveny během vytváření clusteru. Všechny další fondy uzlů mají svou verzi Kubernetes nastavenou při jejich přidání do clusteru. Verze Kubernetes se mohou lišit mezi fondy uzlů a také mezi fondem uzlů a rovinou ovládacího prvku.

Cluster AKS má dva objekty prostředků clusteru s přidruženými verzemi Kubernetes.

1. Kubernetesova verze řídicí roviny clusteru.
2. Fond uzlů s verzí Kubernetes.

Rovina ovládacího prvku se mapuje na jeden nebo více fondů uzlů. Chování operace upgradu závisí na tom, který příkaz Příkaz příkazu Azure CLI se používá.

Upgrade řídicí roviny AKS `az aks upgrade`vyžaduje použití . Tento příkaz inovuje verzi roviny ovládacího prvku a všechny fondy uzlů v clusteru.

Vydání příkazu `az aks upgrade` s `--control-plane-only` příznakem upgraduje pouze rovinu řízení clusteru. Žádný z přidružených fondů uzlů v clusteru se nezmění.

Inovace jednotlivých fondů uzlů `az aks nodepool upgrade`vyžaduje použití . Tento příkaz inovuje pouze fond cílových uzlů se zadanou verzí Kubernetes.

### <a name="validation-rules-for-upgrades"></a>Ověřovací pravidla pro upgrady

Platné upgrady Kubernetes pro řídicí rovinu clusteru a fondy uzlů jsou ověřeny následujícími sadami pravidel.

* Pravidla pro platné verze pro upgrade fondů uzlů:
   * Verze fondu uzlů musí mít stejnou *hlavní* verzi jako rovina ovládacího prvku.
   * *Dílčí* verze fondu uzlů musí být ve dvou *dílčích* verzích verze roviny ovládacího prvku.
   * Verze fondu uzlů nemůže být větší `major.minor.patch` než verze ovládacího prvku.

* Pravidla pro odeslání operace upgradu:
   * Nelze downgrade rovinu ovládacího prvku nebo uzel fondu Kubernetes verze.
   * Pokud není zadána verze fondu uzlů Kubernetes, chování závisí na používaném klientovi. Deklarace v šablonách Správce prostředků se vrátí zpět na existující verzi definovanou pro fond uzlů, pokud se používá, pokud není nastavena žádná verze roviny ovládacího prvku, která se používá k návratu.
   * Můžete upgradovat nebo škálovat rovinu ovládacího prvku nebo fond uzlů v daném čase, nelze odeslat více operací na jeden řídicí rovinu nebo prostředek fondu uzlů současně.

## <a name="scale-a-node-pool-manually"></a>Ruční škálování fondu uzlů

Jak se mění vaše pracovní vytížení aplikací, možná budete muset škálovat počet uzlů ve fondu uzlů. Počet uzlů lze škálovat nahoru nebo dolů.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Chcete-li škálovat počet uzlů ve fondu uzlů, použijte příkaz [měřítko fondu uzlů az aks.][az-aks-nodepool-scale] Následující příklad změní velikost počtu uzlů v *mynodepoolu* na *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Seznam stavu fondů uzlů znovu pomocí příkazu [seznamu seznamu az aks uzlu.][az-aks-nodepool-list] Následující příklad ukazuje, že *mynodepool* je ve stavu *Škálování* s novým počtem *5* uzlů:

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

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Automatické škálování fondu určitých uzlů povolením automatického škálování clusteru

AKS nabízí samostatnou funkci pro automatické škálování fondů uzlů pomocí funkce nazývané [automatické škálování clusteru](cluster-autoscaler.md). Tuto funkci lze povolit na fond uzlů s jedinečným minimálním a maximálním počtem škálování na fond uzlů. Přečtěte si, jak [používat automatický škálátor clusteru pro jeden fond uzlů](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Odstranění fondu uzlů

Pokud už fond nepotřebujete, můžete ho odstranit a odebrat základní uzly virtuálního papíru. Chcete-li odstranit fond uzlů, použijte příkaz [delete fondu uzlů a][az-aks-nodepool-delete] zadejte název fondu uzlů. Následující příklad odstraní *mynoodepool* vytvořený v předchozích krocích:

> [!CAUTION]
> Neexistují žádné možnosti obnovení pro ztrátu dat, ke kterému může dojít při odstranění fondu uzlů. Pokud pody nelze naplánovat na jiných fondů uzlů, tyto aplikace nejsou k dispozici. Ujistěte se, že neodstraníte fond uzlů, pokud v provozu aplikace nemají zálohy dat nebo možnost spouštět v jiných fondech uzlů ve vašem clusteru.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Následující ukázkový výstup ze seznamu [seznamu uzlů azaks][az-aks-nodepool-list] ukazuje, že *mynodepool* je ve stavu *Odstranění:*

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

V předchozích příkladech pro vytvoření fondu uzlů byla pro uzly vytvořené v clusteru použita výchozí velikost virtuálního počítače. Běžnější scénář je pro vytváření fondů uzlů s různými velikostmi a možnostmi virtuálních aplikací. Můžete například vytvořit fond uzlů, který obsahuje uzly s velkým množstvím procesoru nebo paměti, nebo fond uzlů, který poskytuje podporu GPU. V dalším kroku [použijete počitadla a tolerance](#schedule-pods-using-taints-and-tolerations) sdělit Plánovač Kubernetes, jak omezit přístup k pody, které lze spustit na těchto uzlech.

V následujícím příkladu vytvořte fond uzlů založených na GPU, který používá *Standard_NC6* velikost virtuálního počítače. Tyto virtuální měny jsou poháněny kartou NVIDIA Tesla K80. Informace o dostupných velikostech virtuálních počítačů najdete [v tématu Velikosti pro virtuální počítače s Linuxem v Azure][vm-sizes].

Vytvořte fond uzlů pomocí příkazu přidat fond [uzlů az aks][az-aks-nodepool-add] znovu. Tentokrát zadejte název *gpunodepool*a `--node-vm-size` pomocí parametru určete *velikost Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Následující příklad výstupu z příkazu [seznamu seznamu uzly azaks][az-aks-nodepool-list] ukazuje, že *gpunodepool* *vytváří* uzly se zadanou *velikostí VmSize*:

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

Trvá několik minut pro *gpunodepool* úspěšně vytvořen.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Naplánujte si moduly pomocí počitadla a tolerance

Nyní máte dva fondy uzlů v clusteru – výchozí fond uzlů původně vytvořený a fond uzlů založený na GPU. Pomocí příkazu [kubectl get nodes][kubectl-get] zobrazte uzly v clusteru. Následující ukázkový výstup ukazuje uzly:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Plánovač Kubernetes můžete použít potu a tolerace omezit úlohy lze spustit na uzlech.

* Na uzel se použije **počin,** který označuje, že na nich mohou být naplánovány pouze určité pody.
* **Tolerace** se pak aplikuje na lusk, který jim umožňuje *tolerovat* počin uzlu.

Další informace o používání pokročilých naplánovaných funkcí Kubernetes najdete [v tématu Doporučené postupy pro pokročilé funkce plánovače v AKS][taints-tolerations]

V tomto příkladu použijte počin na uzlu založeném na GPU pomocí příkazu --node-taints. Z výstupu předchozího `kubectl get nodes` příkazu zadejte název uzlu založeného na GPU. Počitadla se použije jako *key:value* a pak možnost plánování. Následující příklad používá *sku= gpu* pár a definuje pody jinak mají *NoSchedule* schopnost:

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Následující základní příklad Manifestu YAML používá tolerance k tomu, aby plánovač Kubernetes mohl spustit pod NGINX v uzlu založeném na GPU. Vhodnější, ale časově náročný příklad spuštění úlohy Tententory proti datové sadě MNIST najdete v [tématu Použití gpu pro úlohy náročné na výpočetní výkon na AKS][gpu-cluster].

Vytvořte soubor `gpu-toleration.yaml` s názvem a zkopírujte v následujícím příkladu YAML:

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

Naplánujte pod `kubectl apply -f gpu-toleration.yaml` pomocí příkazu:

```console
kubectl apply -f gpu-toleration.yaml
```

Naplánování modulu a vytažení obrazu NGINX trvá několik sekund. Pomocí příkazu [kubectl describe pod][kubectl-describe] zobrazte stav modulu. Následující zhuštěný příklad výstupu ukazuje *sku = gpu:NoSchedule* toleration je použita. V části události plánovač přiřadil pod *k uzlu aks-gpunodepool-28993262-vmss0000000* GPU:

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

Pouze pody, které mají tento potu použít lze naplánovat na uzly v *gpunodepool*. Všechny ostatní pod by být naplánováno ve fondu uzlu *1* uzlu. Pokud vytvoříte další fondy uzlů, můžete použít další počina a tolerance k omezení, jaké pody lze naplánovat na tyto prostředky uzlu.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Určení počin, popisek nebo značka pro fond uzlů

Při vytváření fondu uzlů můžete do tohoto fondu uzlů přidat počin, popisky nebo značky. Když přidáte počin, popisek nebo značku, všechny uzly v rámci tohoto fondu uzlů také získat, že počin, popisek nebo značku.

Chcete-li vytvořit fond uzlů s počinem, použijte [přidání uzlu az aks][az-aks-nodepool-add]. Zadejte název *taintnp* `--node-taints` a pomocí parametru určete *sku=gpu:NoSchedule* pro podobu.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Následující příklad výstupu z příkazu [az aks nodepool list][az-aks-nodepool-list] ukazuje, že *taintnp* *vytváří* uzly se zadanými *uzly Taints*:

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
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

Informace o pozměnění jsou viditelné v Kubernetes pro zpracování pravidel plánování pro uzly.

Můžete také přidat popisky do fondu uzlů během vytváření fondu uzlů. Popisky nastavené ve fondu uzlů jsou přidány do každého uzlu ve fondu uzlů. Tyto [popisky jsou viditelné v Kubernetes][kubernetes-labels] pro zpracování pravidel plánování pro uzly.

Chcete-li vytvořit fond uzlů s popiskem, použijte [az aks nodepool přidat][az-aks-nodepool-add]. Zadejte název *labelnp* `--labels` a pomocí parametru *určete dept=IT* a *costcenter=9999* pro popisky.

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
> Popisek lze nastavit pouze pro fondy uzlů během vytváření fondu uzlů. Popisky musí být také dvojice klíč/hodnota a musí mít [platnou syntaxi][kubernetes-label-syntax].

Následující ukázkový výstup z příkazu [seznamu uzlů azaks][az-aks-nodepool-list] ukazuje, že *labelnp* *vytváří* uzly se zadanými *uzly*:

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

Značku Azure můžete použít pro fondy uzlů ve vašem clusteru AKS. Značky aplikované na fond uzlů jsou použity pro každý uzel v rámci fondu uzlů a jsou trvalé prostřednictvím upgradů. Značky jsou také použity na nové uzly přidané do fondu uzlů během operací horizontálního navýšení kapacity. Přidání značky může pomoci s úkoly, jako je sledování zásad nebo odhad nákladů.

Vytvořte fond uzlů pomocí přidání [uzlu az aks][az-aks-nodepool-add]. Zadejte *název tagnodepool* `--tag` a pomocí parametru *určete dept=IT* a *costcenter=9999* pro značky.

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
> Parametr můžete použít `--tags` také při použití [příkazu aktualizace uzlu az aks][az-aks-nodepool-update] a také při vytváření clusteru. Během vytváření clusteru `--tags` parametr použije značku na počáteční fond uzlů vytvořený s clusterem. Všechny názvy značek musí dodržovat omezení v [použití značek k uspořádání prostředků Azure][tag-limitation]. Aktualizace fondu uzlů s `--tags` parametrem aktualizuje všechny existující hodnoty značek a připojí všechny nové značky. Například pokud váš fond uzlů měl *dept= IT* a *costcenter = 9999* pro značky a aktualizovali jste ho *team=dev* a *costcenter=111* pro značky, uzel by měl *dept= IT*, *costcenter = 111*a *team = dev* pro značky.

Následující ukázkový výstup z příkazu [seznamu aks uzlu][az-aks-nodepool-list] ukazuje, že *tagnodepool* *vytváří* uzly se zadanou *značkou*:

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

Když použijete šablonu Azure Resource Manager k vytvoření a spravované prostředky, můžete obvykle aktualizovat nastavení v šabloně a znovu nasadit aktualizovat prostředek. S fondy uzlů v AKS, počáteční profil fondu uzlů nelze aktualizovat, jakmile byl vytvořen cluster AKS. Toto chování znamená, že nelze aktualizovat existující šablonu Správce prostředků, provést změnu fondů uzlů a znovu nasadit. Místo toho je nutné vytvořit samostatnou šablonu Správce prostředků, která aktualizuje pouze fondy uzlů pro existující cluster AKS.

Vytvořte šablonu, například `aks-agentpools.json` a vložte následující ukázkový manifest. Tato ukázková šablona konfiguruje následující nastavení:

* Aktualizuje fond uzlů *Linux* s názvem *myagentpool* pro spuštění tří uzlů.
* Nastaví uzly ve fondu uzlů tak, aby spouštěly Kubernetes verze *1.15.7*.
* Definuje velikost uzlu jako *Standard_DS2_v2*.

Podle potřeby upravte tyto hodnoty podle potřeby k aktualizaci, přidání nebo odstranění fondů uzlů:

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

Nasadit tuto šablonu pomocí příkazu [vytvořit nasazení skupiny az,][az-group-deployment-create] jak je znázorněno v následujícím příkladu. Zobrazí se výzva k zadání existujícího názvu a umístění clusteru AKS:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Značku můžete přidat do fondu uzlů přidáním *vlastnosti značky* do šablony, jak je znázorněno v následujícím příkladu.
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

Aktualizace clusteru AKS může trvat několik minut v závislosti na nastavení fondu uzlů a operacích, které definujete v šabloně Správce prostředků.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Přiřazení veřejné IP adresy na uzel pro fond uzlů (náhled)

> [!WARNING]
> Během náhledu přiřazení veřejné IP adresy na uzel ji nelze použít se *standardní skladovou jednotkou pro vyrovnávání zatížení v AKS* z důvodu možného vyrovnání zatížení, která jsou v konfliktu s zřizováním virtuálních počítačů. V důsledku tohoto omezení nejsou fondy agentů systému Windows podporovány s touto funkcí náhledu. Ve verzi Preview musíte použít *skladovou položku základního vykladače zatížení,* pokud potřebujete přiřadit veřejnou IP adresu na uzel.

AKS uzly nevyžadují své vlastní veřejné IP adresy pro komunikaci. Scénáře však může vyžadovat uzly ve fondu uzlů přijímat své vlastní vyhrazené veřejné IP adresy. Běžný scénář je pro herní úlohy, kde konzole potřebuje vytvořit přímé připojení ke cloudovému virtuálnímu počítači, aby se minimalizovalo směrování. Tohoto scénáře lze dosáhnout na AKS registrací funkce náhledu, Uzel Veřejné IP (náhled).

Zaregistrujte se pro funkci veřejné IP uzlu vydáním následujícího příkazu Azure CLI.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Po úspěšné registraci nasaďte šablonu Azure Resource Manager podle stejných pokynů jako [výše](#manage-node-pools-using-a-resource-manager-template) a přidejte logickou vlastnost `enableNodePublicIP` do agentPoolProfiles. Nastavte hodnotu `true` jako ve výchozím `false` nastavení, je nastavena jako by nebyla zadána. 

Tato vlastnost je vlastnost pouze pro vytvoření a vyžaduje minimální verzi rozhraní API 2019-06-01. To lze použít pro fondy uzlů Linux u Windows a Windows.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jste vytvořili cluster AKS, který zahrnuje uzly založené na GPU. Chcete-li snížit zbytečné náklady, můžete odstranit *gpunodepool*nebo celý cluster AKS.

Chcete-li odstranit fond uzlů založených na GPU, použijte příkaz [delete az aks nodepool,][az-aks-nodepool-delete] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Chcete-li odstranit samotný cluster, odstraňte skupinu prostředků AKS pomocí příkazu [delete skupiny az:][az-group-delete]

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vytvořit a spravovat více fondů uzlů v clusteru AKS. Další informace o tom, jak řídit pody ve fondech uzlů, naleznete [v tématu Doporučené postupy pro pokročilé funkce plánovače v AKS][operator-best-practices-advanced-scheduler].

Informace o vytvoření a použití fondů uzlů kontejnerů systému Windows Server naleznete [v tématu Vytvoření kontejneru windows serveru v aks][aks-windows].

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
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md