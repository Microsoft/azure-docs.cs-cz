---
title: Použití více fondy uzlů ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvářet a spravovat více fondy uzlů pro cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: 4af2e97e8ace432c37a770f1930514dd19e30944
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235754"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Ve verzi Preview – vytváření a správě více fondy uzlů pro cluster Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS), uzlů se stejnou konfigurací jsou seskupeny do *fondy uzlů*. Tyto fondy uzlů obsahovat základní virtuální počítače, na kterých běží vaše aplikace. Počáteční počet uzlů a jejich velikosti (SKU) jsou definována při vytvoření clusteru AKS, který vytvoří *výchozí fond uzlů*. Podpora aplikací, které mají různé výpočetní prostředky ani prostředky nároky na úložiště, můžete vytvořit fondy dalších uzlů. Tyto fondy dalšího uzlu například použijte k zadání GPU pro aplikace náročné na výpočetní nebo přístup k vysoce výkonné SSD úložiště.

V tomto článku se dozvíte, jak vytvořit a spravovat více fondy uzlů v clusteru AKS. Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb, vyjádřit výslovný souhlas. Jsou poskytovány shromažďovat zpětnou vazbu a chyb z naší komunitě. Ve verzi preview nejsou tyto funkce určené k použití v produkčním prostředí. Funkce ve verzi public preview spadají pod "co možná nejlepší" podporu. Pomoc od týmů AKS technická podpora je k dispozici během pracovní doby tichomořské časové pásmo (PST) pouze. Další informace najdete v tématu následující články o podpoře:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Než začnete

Musí mít Azure CLI verze 2.0.61 nebo později nainstalována a nakonfigurována. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření aks ve verzi preview rozhraní příkazového řádku

Jsou k dispozici v příkazů rozhraní příkazového řádku můžete vytvářet a spravovat více fondy uzlů *aks ve verzi preview* rozšíření rozhraní příkazového řádku. Nainstalujte *aks ve verzi preview* pomocí rozšíření Azure CLI [přidat rozšíření az] [ az-extension-add] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Pokud jste dříve nainstalovali *aks ve verzi preview* rozšíření, nainstalujte všechny dostupné aktualizace pomocí `az extension update --name aks-preview` příkazu.

### <a name="register-multiple-node-pool-feature-provider"></a>Zaregistrovat více uzlů fondu funkci zprostředkovatele

K vytvoření clusteru AKS, můžete použít více fondy uzlů, nejprve povolte dva příznaky funkcí v rámci předplatného. Clustery s několika uzly fondu pomocí škálovací sady virtuálních počítačů (VMSS) ke správě nasazení a konfigurace uzlů Kubernetes. Registrace *MultiAgentpoolPreview* a *VMSSPreview* příznaky funkcí pomocí [az funkce register] [ az-feature-register] příkaz, jak je znázorněno Následující příklad:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Libovolný cluster AKS, vytvoříte po úspěšném zaregistrování *MultiAgentpoolPreview* použít toto prostředí clusteru ve verzi preview. Pokračujte k vytvoření clusterů pravidelných a plně podporované nepovolí funkce ve verzi preview na předplatná pro produkční prostředí. Používejte samostatný testovací nebo vývojové předplatné Azure pro testování funkcí ve verzi preview.

Trvá několik minut, než se stav zobrazíte *registrované*. Vy můžete zkontrolovat stav registrace pomocí [seznam funkcí az] [ az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Až to budete mít, aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register] [ az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Omezení

Při vytváření a správě AKS clustery, které podporují více fondy uzlů se vztahují následující omezení:

* Více fondy uzlů jsou k dispozici v případě clusterů vytvořených po úspěšném zaregistrování pouze *MultiAgentpoolPreview* a *VMSSPreview* funkce pro vaše předplatné. Nelze přidat nebo spravovat fondy uzlů s existující cluster AKS vytvořili předtím, než tyto funkce byly úspěšně registrovány.
* Nelze odstranit první fond uzlů.
* Doplněk směrování aplikace HTTP nelze použít.
* Nelze přidat, aktualizace nebo odstranění uzlu fondů v existující šablonu Resource Manageru stejně jako u většiny operací. Místo toho [pomocí samostatné šablony Resource Manageru](#manage-node-pools-using-a-resource-manager-template) měnit fondy uzlů v clusteru AKS.

Tato funkce je ve verzi preview, platí následující další omezení:

* AKS cluster může mít maximálně osm fondy uzlů.
* AKS cluster můžou mít maximálně 400 uzlů ve fondech osmi uzlů.

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Abyste mohli začít, vytvořte AKS cluster s fondem jeden uzel. V následujícím příkladu [vytvořit skupiny az] [ az-group-create] příkazu vytvořte skupinu prostředků s názvem *myResourceGroup* v *eastus* oblast. Cluster AKS, s názvem *myAKSCluster* se pak vytvoří pomocí [az aks vytvořit] [ az-aks-create] příkazu. A *verze kubernetes –* z *1.12.6* ukazuje, jak aktualizovat fond uzlů v následujícím kroku. Můžete určit kterékoli [podporovaná verze Kubernetes][supported-versions].

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
    --kubernetes-version 1.12.6
```

Vytvoření clusteru bude trvat několik minut.

Pokud bude cluster připravený, použijte [az aks get-credentials] [ az-aks-get-credentials] příkazu získejte přihlašovací údaje clusteru pro použití s `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Přidat fond uzlů

Cluster vytvořený v předchozím kroku má fond jeden uzel. Přidejte druhý fond uzlu pomocí [přidat fond uzlů az aks] [ az-aks-nodepool-add] příkazu. Následující příklad vytvoří fond uzlů s názvem *mynodepool* , který spouští *3* uzly:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Chcete-li zobrazit stav vašich fondů uzlu, použijte [seznamu fond uzlů az aks] [ az-aks-nodepool-list] příkaz a zadejte název skupiny a cluster prostředku:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

Následující příklad výstupu ukazuje, že *mynodepool* se úspěšně vytvořila se třemi uzly ve fondu uzlů. Vytvoření clusteru AKS v předchozím kroku, výchozí *nodepool1* byl vytvořen s počtem uzlů *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Pokud ne *OrchestratorVersion* nebo *VmSize* určen při přidání uzlu fondu, uzly jsou na základě vytvořit výchozí hodnoty pro AKS cluster. V tomto příkladu, který byl verze Kubernetes *1.12.6* a velikost uzlu *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Upgrade fond uzlů

Při vytvoření clusteru AKS v prvním kroku, `--kubernetes-version` z *1.12.6* byl zadán. Umožňuje upgradovat *mynodepool* do Kubernetes *1.12.7*. Použití [příkazu az aks uzlu fondu upgrade] [ az-aks-nodepool-upgrade] příkaz pro upgrade fond uzlů, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Vypíše stav uzlu fondech znovu pomocí [seznamu fond uzlů az aks] [ az-aks-nodepool-list] příkazu. Následující příklad ukazuje, že *mynodepool* probíhá *upgrade* do stavu *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Trvá několik minut, než upgradujte uzly zadané verze.

Jako osvědčený postup měli byste upgradovat všechny fondy uzlů v clusteru AKS na stejnou verzi Kubernetes. Možný upgrade fondy jednotlivých uzlů umožňuje upgradu se zajištěním provozu a naplánovat podů mezi fondy uzlů Udržovat dobu provozu aplikací.

## <a name="scale-a-node-pool"></a>Škálovat fond uzlů

Jako vaši aplikaci měnících se požadavcích úloh, možná budete muset škálovat počet uzlů ve fondu uzlů. Počet uzlů můžete vertikálně navýšit nebo snížit.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Chcete-li škálovat počet uzlů ve fondu uzlu, použijte [az aks uzlu fondu škálování] [ az-aks-nodepool-scale] příkazu. Následující příklad nastaví počet uzlů v *mynodepool* k *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Vypíše stav uzlu fondech znovu pomocí [seznamu fond uzlů az aks] [ az-aks-nodepool-list] příkazu. Následující příklad ukazuje, že *mynodepool* probíhá *škálování* stavu a nový počet *5* uzly:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Trvá několik minut, než se operace škálování dokončí.

## <a name="delete-a-node-pool"></a>Odstranit fond uzlů

Pokud fond už nepotřebujete, můžete ho odstranit a odebrání uzlů základní virtuální počítač. Chcete-li odstranit fond uzlů, použijte [odstranit fond uzlů az aks] [ az-aks-nodepool-delete] příkaz a zadejte název fondu uzlů. Následující příklad odstraní *mynoodepool* vytvořený v předchozích krocích:

> [!CAUTION]
> Nejsou žádné možnosti obnovení pro ztráty dat, které mohou nastat při odstranění fondu uzlů. Pokud podů se nedají naplánovat na další fondy uzlů, tyto aplikace nejsou k dispozici. Ujistěte se, že když aplikace používané nemají zálohování dat nebo možnost spouštět na fondech další uzel v clusteru a neodstraňujte fond uzlů.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Následující příklad výstupu z [seznamu fond uzlů az aks] [ az-aks-nodepool-list] příkaz ukazuje, že *mynodepool* probíhá *odstranění* stavu:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Trvá několik minut, než odstranění uzlů a uzlů fondu.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Určení velikosti virtuálního počítače pro fond uzlů

V předchozích příkladech vytvořit fond uzlů se použil výchozí velikosti virtuálního počítače pro uzly vytvořeno v clusteru. Další běžný scénář, kdy je můžete vytvářet fondy uzlů s možnostmi a různé velikosti virtuálních počítačů. Můžete například vytvořit fond uzlů, který obsahuje uzly s velkým množstvím CPU, paměť nebo fond uzlů, který poskytuje podporou GPU. V dalším kroku při [použití poskvrnění a tolerations][#schedule-pods-using-taints-and-tolerations] říct jak omezit přístup k podů Kubernetes Plánovač, který může spouštět na těchto uzlech.

V následujícím příkladu, vytvořit fond uzlů založený na grafickém procesoru, který používá *Standard_NC6* velikost virtuálního počítače. Tyto virtuální počítače využívají karty NVIDIA Tesla K80. Informace o dostupných velikostech virtuálních počítačů najdete v tématu [velikostí pro virtuální počítače s Linuxem v Azure][vm-sizes].

Vytvoření fondu pomocí uzlu [přidat fond uzlů az aks] [ az-aks-nodepool-add] příkaz znovu. Tentokrát, zadejte název *gpunodepool*a použít `--node-vm-size` parametr k určení *Standard_NC6* velikost:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Následující příklad výstupu z [seznamu fond uzlů az aks] [ az-aks-nodepool-list] příkaz ukazuje, že *gpunodepool* je *vytváření* uzly se Zadaný *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Trvá několik minut, než *gpunodepool* úspěšně vytvořit.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Naplánujte použití poskvrnění a tolerations podů

Teď máte dva fondy uzlů v clusteru – výchozí uzel fond původně vytvořeno a fond uzlů založený na grafickém procesoru. Použití [kubectl get uzly] [ kubectl-get] příkazu zobrazte uzly ve vašem clusteru. Následující příklad výstupu ukazuje jeden uzel v každém uzlu fondu:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Plánovač Kubernetes můžete použít poskvrnění a tolerations omezit, co můžete spouštět úlohy na uzlech.

* A **taint** se použije na uzel, který označuje pouze konkrétní podů naplánovaných na ně.
* A **toleration** se následně použije na pod, které jim umožní *tolerovat* barvu uzlu.

Další informace o tom, jak pomocí rozšířených funkcí naplánované Kubernetes najdete v tématu [osvědčené postupy pro Plánovač pokročilé funkce ve službě AKS][taints-tolerations]

V tomto příkladu platí barvu na založený na grafickém procesoru uzlu pomocí [kubectl barvu uzel] [ kubectl-taint] příkazu. Zadejte název založený na grafickém procesoru uzlu z výstupu předchozího `kubectl get nodes` příkazu. Barvu se použije jako *klíč: hodnota* a pak možnost a plánování. V následujícím příkladu *sku = gpu* spárujte a definuje podů jinak *NoSchedule* možnost:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Manifest YAML následující základní příklad používá toleration k umožnění Plánovač Kubernetes spuštěný serveru NGINX pod v uzlu založený na grafickém procesoru. Příklad vhodnější, ale náročné na čas ke spuštění úlohy Tensorflow datové sadě mnist ručně, naleznete v tématu [GPU použijte pro úlohy náročné na výpočetní prostředky v AKS][gpu-cluster].

Vytvořte soubor s názvem `gpu-toleration.yaml` a kopie v následujícím příkladu YAML:

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

Naplánovat pod pomocí `kubectl apply -f gpu-toleration.yaml` příkaz:

```console
kubectl apply -f gpu-toleration.yaml
```

Trvá několik sekund naplánovat pod a vyžádejte si image serveru NGINX. Použití [kubectl popisují pod] [ kubectl-describe] příkazu zobrazíte stav pod. Následujícímu zhuštěnému příkladu výstup ukazuje *sku = gpu:NoSchedule* toleration platí. V části události Plánovač přiřadila pod celému *aks gpunodepool 28993262 vmss000000* založený na grafickém procesoru uzlu:

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

Pouze podů, které mají tento barvu použít můžete naplánovat na uzly v *gpunodepool*. Další pod by být naplánováno v *nodepool1* fond uzlů. Pokud vytvoříte fondy dalších uzlů, můžete použít další poskvrnění a tolerations omezit jaké podů můžete naplánovat na tyto prostředky uzlu.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Spravovat fondy uzlů pomocí šablony Resource Manageru

Při použití šablony Azure Resource Manageru k vytvoření a spravované prostředky, je obvykle aktualizovat nastavení v šabloně a opětovné nasazení pro aktualizaci prostředku. S nodepools ve službě AKS nelze aktualizovat profil počáteční nodepool po vytvoření clusteru AKS. Toto chování znamená, že nelze aktualizovat existující šablonu Resource Manageru, proveďte změnu na fondy uzlů a znovu nasadit. Místo toho musíte vytvořit samostatné šablony Resource Manageru, která aktualizuje pouze fondy agentů pro existující cluster AKS.

Vytvoření šablony, jako `aks-agentpools.json` a vložte následující příklad manifestu. Tato ukázková šablona konfiguruje následující nastavení:

* Aktualizace *Linux* fond agentů se s názvem *myagentpool* ke spuštění tři uzly.
* Nastaví uzly ve fondu, uzel, aby verzí Kubernetes *1.12.8*.
* Definuje velikost uzlu jako *Standard_DS2_v2*.

Tyto hodnoty upravte, jako je třeba aktualizovat, přidávat a odstraňovat fondy uzlů podle potřeby:

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
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Nasazení této šablony můžete použít [vytvořit nasazení skupiny pro az] [ az-group-deployment-create] příkaz, jak je znázorněno v následujícím příkladu. Zobrazí se výzva pro existující název clusteru AKS a umístění:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Může trvat několik minut na aktualizaci clusteru AKS v závislosti na nastavení uzlu fondu a operace, které definujete v šabloně Resource Manageru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jste vytvořili cluster AKS, který obsahuje uzly založený na grafickém procesoru. Abyste snížili náklady na zbytečné, můžete chtít odstranit *gpunodepool*, nebo celého clusteru AKS.

Chcete-li odstranit fond uzlů založený na grafickém procesoru, použijte [az aks nodepool odstranit] [ az-aks-nodepool-delete] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Chcete-li odstranit samotného clusteru, použijte [odstranění skupiny az] [ az-group-delete] příkazu k odstranění skupiny prostředků pro AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vytvořit a spravovat více fondy uzlů v clusteru AKS. Další informace o tom, jak řídit podů mezi fondy uzlů najdete v tématu [osvědčené postupy pro Plánovač pokročilé funkce ve službě AKS][operator-best-practices-advanced-scheduler].

Vytvořit a používat fondy uzlů kontejneru Windows serveru najdete v tématu [vytvoříte kontejner Windows Server ve službě AKS][aks-windows].

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
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-delete
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
