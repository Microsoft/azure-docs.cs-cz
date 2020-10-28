---
title: Vytváření virtuálních uzlů pomocí Azure CLI
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak pomocí Azure CLI vytvořit cluster Azure Kubernetes Services (AKS), který pomocí virtuálních uzlů spouští lusky.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 96c47ed59fd904f1523347d9f0ef7bc00edb866f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745654"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Vytvoření a konfigurace clusteru Azure Kubernetes Services (AKS) pro použití virtuálních uzlů pomocí Azure CLI

Pokud chcete rychle škálovat úlohy aplikace v clusteru AKS (Azure Kubernetes Service), můžete použít virtuální uzly. U virtuálních uzlů máte rychlé zřizování lusků a platíte za dobu jejich spuštění jenom za sekundu. Nemusíte čekat na automatické škálování clusteru Kubernetes, aby se nasadily výpočetní uzly virtuálních počítačů, aby se spouštěly další lusky. Virtuální uzly jsou podporované jenom se systémy Linux a uzly.

V tomto článku se dozvíte, jak vytvořit a nakonfigurovat prostředky virtuální sítě a cluster AKS, a pak povolit virtuální uzly.

## <a name="before-you-begin"></a>Než začnete

Virtuální uzly umožňují síťovou komunikaci mezi lusky, které běží v Azure Container Instances (ACI) a clusteru AKS. Pro zajištění této komunikace se vytvoří podsíť virtuální sítě a přiřadí se delegovaná oprávnění. Virtuální uzly fungují jenom s clustery AKS vytvořenými pomocí *pokročilých* sítí. Ve výchozím nastavení se clustery AKS vytvářejí se *základními* sítěmi. V tomto článku se dozvíte, jak vytvořit virtuální síť a podsítě a pak nasadit cluster AKS, který využívá pokročilé sítě.

Pokud jste ACI ještě dřív nepoužívali, zaregistrujte poskytovatele služeb u svého předplatného. Stav registrace poskytovatele ACI můžete zjistit pomocí příkazu [AZ Provider list][az-provider-list] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Poskytovatel *Microsoft. ContainerInstance* by měl hlásit jako *zaregistrovaný* , jak je znázorněno v následujícím příkladu výstupu:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Pokud se zprostředkovatel zobrazí jako *NotRegistered* , zaregistrujte poskytovatele pomocí [registru AZ Provider][az-provider-register] , jak je znázorněno v následujícím příkladu:

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
Funkce virtuálních uzlů je silně závislá na sadě funkcí ACI. Kromě [kvót a omezení pro Azure Container Instances](../container-instances/container-instances-quotas.md)se u virtuálních uzlů ještě nepodporují následující scénáře:

* Získání ACR imagí pomocí instančního objektu [Alternativním řešením](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) je používání [tajných klíčů Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Omezení Virtual Network](../container-instances/container-instances-vnet.md) , včetně partnerských vztahů virtuálních sítí, zásad sítě Kubernetes a odchozího provozu do Internetu se skupinami zabezpečení sítě.
* Inicializovat kontejnery
* [Aliasy hostitele](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) pro exec v ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nebude nasazovat lusky do virtuálního uzlu.
* Virtuální uzly podporují plánování lusků v systému Linux. K naplánování kontejnerů Windows serveru na ACI můžete ručně nainstalovat poskytovatele open source [Virtual KUBELET ACI](https://github.com/virtual-kubelet/azure-aci) .
* Virtuální uzly vyžadují clustery AKS s využitím Azure CNI Networking

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat** , vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.49 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westus* .

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [AZ Network VNet Create][az-network-vnet-create] . Následující příklad vytvoří virtuální síť s názvem *myVnet* s předponou adresy *10.0.0.0/8* a podsítí s názvem *myAKSSubnet* . Předpona adresy této podsítě je standardně *10.240.0.0/16* :

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Nyní vytvořte další podsíť pro virtuální uzly pomocí příkazu [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] . Následující příklad vytvoří podsíť s názvem *myVirtualNodeSubnet* s předponou adresy *10.241.0.0/16* .

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Vytvoření instančního objektu nebo použití spravované identity

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Tento instanční objekt se dá automaticky vytvořit pomocí rozhraní příkazového řádku Azure nebo portálu, nebo si ho můžete předem vytvořit a přiřadit další oprávnění. Alternativně můžete místo instančního objektu použít spravovanou identitu pro oprávnění. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).

Vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Poznamenejte si *appId* a *password* . Tyto hodnoty se použijí v dalších krocích.

## <a name="assign-permissions-to-the-virtual-network"></a>Přiřazení oprávnění k virtuální síti

Pokud chcete povolit, aby cluster používal a spravoval virtuální síť, musíte objektu služby AKS udělit správná práva k používání síťových prostředků.

Nejdřív Získejte ID prostředku virtuální sítě pomocí [AZ Network VNet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Chcete-li udělit správnému přístupu ke clusteru AKS použít virtuální síť, vytvořte přiřazení role pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] . Nahraďte `<appId`> a `<vnetId>` hodnotami získanými v předchozích dvou krocích.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Cluster AKS nasadíte do podsítě AKS vytvořené v předchozím kroku. Získání ID této podsítě pomocí [AZ Network VNet Subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Nahraďte `<subnetId>` identifikátorem získaným v předchozím kroku a pak `<appId>` a `<password>` hodnotami, které jste shromáždili v předchozí části.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

## <a name="enable-virtual-nodes-addon"></a>Povolit doplněk virtuálních uzlů

Chcete-li povolit virtuální uzly, použijte nyní příkaz [AZ AKS Enable-addons][az-aks-enable-addons] . V následujícím příkladu se používá podsíť s názvem *myVirtualNodeSubnet* vytvořená v předchozím kroku:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Pomocí příkazu [az aks get-credentials][az-aks-get-credentials] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Tímto krokem se stáhnou přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```console
kubectl get nodes
```

Následující příklad výstupu ukazuje, že byl vytvořen jeden uzel virtuálního počítače a pak virtuální uzel pro Linux, *Virtual-Node-ACI-Linux* :

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Nasazení ukázkové aplikace

Vytvořte soubor s názvem `virtual-node.yaml` a zkopírujte ho na následující YAML. Pro naplánování kontejneru na uzlu jsou definovány [nodeSelector][node-selector] a [tolerování][toleration] .

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

```console
kubectl apply -f virtual-node.yaml
```

Použijte příkaz [kubectl Get lusks][kubectl-get] s `-o wide` argumentem pro výstup seznamu lusků a naplánovaného uzlu. Všimněte si, že `aci-helloworld` uzel pod byl naplánován na `virtual-node-aci-linux` uzlu.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod ní je přiřazena interní IP adresa z podsítě virtuální sítě Azure delegované pro použití s virtuálními uzly.

> [!NOTE]
> Pokud používáte Image uložené v Azure Container Registry, [nakonfigurujte a používejte tajný kód Kubernetes][acr-aks-secrets]. Aktuálním omezením virtuálních uzlů je, že nemůžete použít integrované ověřování instančního objektu služby Azure AD. Pokud nepoužíváte tajný kód, nespustí se u nich naplánované na virtuálních uzlech a nahlásí se chyba `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Test virtuálního uzlu pod

Chcete-li otestovat běžící na virtuálním uzlu, přejděte k ukázkové aplikaci pomocí webového klienta. V případě, že je pod přiřazená interní IP adresa, můžete toto připojení rychle otestovat z jiného seznamu pod clusterem AKS. Vytvořte test pod a připojte k němu relaci terminálu:

```console
kubectl run -it --rm testvk --image=debian
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

## <a name="remove-virtual-nodes"></a>Odebrat virtuální uzly

Pokud už nechcete virtuální uzly používat, můžete je zakázat pomocí příkazu [AZ AKS Disable-addons][az aks disable-addons] . 

V případě potřeby [https://shell.azure.com](https://shell.azure.com) v prohlížeči otevřete Azure Cloud Shell.

Nejprve odstraňte pole `aci-helloworld` pod spuštěným ve virtuálním uzlu:

```console
kubectl delete -f virtual-node.yaml
```

Následující příklad příkazu zakáže virtuální uzly Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Nyní odeberte prostředky virtuální sítě a skupinu prostředků:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Další kroky

V tomto článku se naplánovala pod virtuálním uzlem a přiřadila se privátní interní IP adresa. Místo toho můžete pomocí nástroje pro vyrovnávání zatížení nebo řadiče pro příjem dat vytvořit nasazení služby a směrovat provoz do svého zařízení pod ním. Další informace najdete v tématu [Vytvoření základního kontroleru][aks-basic-ingress]příchozího přenosu v AKS.

Virtuální uzly jsou často jednou součástí řešení škálování v AKS. Další informace o škálování řešení najdete v následujících článcích:

- [Použití automatického škálování Kubernetes vodorovně pod][aks-hpa]
- [Použití automatického škálování clusteru Kubernetes][aks-cluster-autoscaler]
- [Podívejte se na ukázku automatického škálování pro virtuální uzly.][virtual-node-autoscale]
- [Přečtěte si další informace o knihovně open source knihovny Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
