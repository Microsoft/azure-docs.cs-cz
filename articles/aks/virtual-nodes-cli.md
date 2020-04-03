---
title: Vytváření virtuálních uzlů pomocí azure cli ve službách Azure Kubernetes Services (AKS)
description: Zjistěte, jak pomocí azure cli vytvořit cluster služeb Azure Kubernetes Services (AKS), který používá virtuální uzly ke spouštění podů.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 05e32b6b0017e945044bc7593d4d6dbc543a5b64
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616460"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Vytvoření a konfigurace clusteru Služeb Azure Kubernetes (AKS) pro použití virtuálních uzlů pomocí rozhraní příkazového příkazu Azure

Chcete-li rychle škálovat úlohy aplikací v clusteru Služby Azure Kubernetes (AKS), můžete použít virtuální uzly. S virtuálníu uzly máte rychlé zřizování podů a platí pouze za sekundu za dobu jejich spuštění. Nemusíte čekat na automatické škálování clusteru Kubernetes k nasazení výpočetních uzlů virtuálních počítači ke spuštění dalších podů. Virtuální uzly jsou podporovány pouze s pody Linuxa a uzly.

Tento článek ukazuje, jak vytvořit a nakonfigurovat prostředky virtuální sítě a clusterAS a potom povolit virtuální uzly.

## <a name="before-you-begin"></a>Než začnete

Virtuální uzly umožňují síťovou komunikaci mezi pody, které běží v Azure Container Instances (ACI) a clusteru AKS. Pro poskytnutí této komunikace je vytvořena podsíť virtuální sítě a jsou přiřazena delegovaná oprávnění. Virtuální uzly fungují pouze s clustery AKS vytvořenými pomocí *pokročilých* sítí. Ve výchozím nastavení jsou clustery AKS vytvářeny se *základní* sítí. Tento článek ukazuje, jak vytvořit virtuální síť a podsítě a potom nasadit cluster AKS, který používá rozšířené sítě.

Pokud jste dříve aci nepoužívali, zaregistrujte poskytovatele služeb pomocí předplatného. Stav registrace zprostředkovatele ACI můžete zkontrolovat pomocí příkazu [seznamu zprostředkovatele az,][az-provider-list] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Zprostředkovatel *Microsoft.ContainerInstance* by měl hlásit jako *Registrovaný*, jak je znázorněno na následujícím příkladu výstupu:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Pokud se poskytovatel zobrazí jako *NotRegistered*, zaregistrujte zprostředkovatele pomocí [registru zprostředkovatele az,][az-provider-register] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regionální dostupnost

Pro nasazení virtuálních uzlů jsou podporovány následující oblasti:

* Austrálie východ (australiaeast)
* Centrální USA (centralus)
* Východní USA (eastus)
* Východní USA 2 (eastus2)
* Japonsko východ (japaneast)
* Severní Evropa (severní Evropa)
* Jihovýchodní Asie (jihovýchodní Asie)
* Západní střed USA (westcentralus)
* Západní Evropa (západní Evropa)
* Západní USA (westus)
* Západní USA 2 (westus2)

## <a name="known-limitations"></a>Známá omezení
Funkce virtuálních uzlů je silně závislá na sadě funkcí ACI. Následující scénáře ještě nejsou podporovány s virtuálními uzly

* Použití instančního objektu k vytahovat obrazy ACR. [Řešení](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) je použití [tajných kódů Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Omezení virtuální sítě,](../container-instances/container-instances-vnet.md) včetně partnerského vztahu virtuální sítě, zásady sítě Kubernetes a odchozího provozu na Internet se skupinami zabezpečení sítě.
* Init kontejnery
* [Hostitelské aliasy](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) pro exec v ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nebude nasazovat pody do virtuálního uzlu
* [Uzly systému Windows Server (aktuálně ve verzi preview v AKS)](windows-container-cli.md) nejsou podporovány vedle virtuálních uzlů. Virtuální uzly můžete použít k plánování kontejnerů systému Windows Server bez nutnosti uzlů Systému Windows Server v clusteru AKS.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Chcete-li otevřít prostředí Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/bash](https://shell.azure.com/bash)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a použití příkazového příkazu k místnímu použití, tento článek vyžaduje Azure CLI verze 2.0.49 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [vytvořit virtuální síť az.][az-network-vnet-create] Následující příklad vytvoří název virtuální sítě *myVnet* s předponou adresy *10.0.0.0/8*a podsíť s názvem *myAKSSubnet*. Předpona adresy této podsítě je výchozí hodnota *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Nyní vytvořte další podsíť pro virtuální uzly pomocí příkazu [vytvořit podsíť sítě AZ.][az-network-vnet-subnet-create] Následující příklad vytvoří podsíť s názvem *myVirtualNodeSubnet* s předponou adresy *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Tento instanční objekt se dá automaticky vytvořit pomocí rozhraní příkazového řádku Azure nebo portálu, nebo si ho můžete předem vytvořit a přiřadit další oprávnění.

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

Poznamenejte si *appId* a *password*. Tyto hodnoty se použijí v dalších krocích.

## <a name="assign-permissions-to-the-virtual-network"></a>Přiřazení oprávnění k virtuální síti

Chcete-li clusteru povolit používání a správu virtuální sítě, je nutné udělit instančnímu objektu Služby AKS správná práva k používání síťových prostředků.

Nejprve získejte ID prostředku virtuální sítě pomocí [az sítě vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Chcete-li clusteru AKS udělit správný přístup k použití virtuální sítě, vytvořte přiřazení role pomocí příkazu [az role create.][az-role-assignment-create] Nahraďte `<appId`> a `<vnetId>` hodnotami získanými v předchozích dvou krocích.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Cluster AKS nasadíte do podsítě AKS vytvořené v předchozím kroku. Získání ID této podsítě pomocí [show podsítě sítě AZ][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Nahraďte `<subnetId>` id získaným v předchozím `<appId>` `<password>` kroku a poté a s hodnotami shromážděnými v předchozí části.

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

Chcete-li povolit virtuální uzly, použijte nyní příkaz [az aks enable-addons.][az-aks-enable-addons] Následující příklad používá podsíť s názvem *myVirtualNodeSubnet* vytvořenou v předchozím kroku:

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

Následující příklad výstupu ukazuje jeden uzel virtuálního počítače vytvořené a pak virtuální uzel pro Linux, *virtual-node-aci-linux*:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Nasazení ukázkové aplikace

Vytvořte soubor `virtual-node.yaml` s názvem a zkopírujte v následujícím yaml. Chcete-li naplánovat kontejner na uzlu, jsou [definovány nodeSelector][node-selector] a [tolerace.][toleration]

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

Spusťte aplikaci pomocí příkazu [kubectl apply.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Pomocí příkazu [get pods kubectl][kubectl-get] s argumentem `-o wide` vyjádřujte seznam podů a naplánovaný uzel. Všimněte `aci-helloworld` si, že pod `virtual-node-aci-linux` byl naplánován na uzlu.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Podu je přiřazena interní IP adresa z podsítě virtuální sítě Azure delegovaná pro použití s virtuálními uzly.

> [!NOTE]
> Pokud používáte image uložené v Azure Container Registry, [nakonfigurujte a použijte tajný klíč Kubernetes][acr-aks-secrets]. Aktuální omezení virtuálních uzlů je, že nelze použít integrované ověřování instančního objektu Azure AD. Pokud nepoužíváte tajný klíč, pody naplánované na virtuálních uzlech se nespustí a ohlásí chybu `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testování podu virtuálního uzlu

Chcete-li otestovat pod spuštěný na virtuálním uzlu, přejděte na ukázkovou aplikaci s webovým klientem. Vzhledem k tomu, že podu je přiřazena interní IP adresa, můžete toto připojení rychle otestovat z jiného podu v clusteru AKS. Vytvořte testovací modul a připojte k němu terminálovou relaci:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
```

Nainstalujte `curl` do `apt-get`modulu pomocí :

```console
apt-get update && apt-get install -y curl
```

Nyní přístup k adrese `curl`pod pomocí *http://10.241.0.4*, například . Zadejte vlastní interní IP adresu `kubectl get pods` uvedenou v předchozím příkazu:

```console
curl -L http://10.241.0.4
```

Ukázková aplikace je zobrazena, jak je znázorněno na následujícím kondenzovaném příkladu výstupu:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zavřete terminálovou relaci testovacího modulu pomocí aplikace `exit`. Po ukončení relace je pod odstraněn.

## <a name="remove-virtual-nodes"></a>Odebrání virtuálních uzlů

Pokud již nechcete používat virtuální uzly, můžete je zakázat pomocí příkazu [az aks disable-addons.][az aks disable-addons] 

V případě potřeby [https://shell.azure.com](https://shell.azure.com) přejděte k otevření Služby Azure Cloud Shell ve vašem prohlížeči.

Nejprve odstraňte `aci-helloworld` pod spuštěný na virtuálním uzlu:

```console
kubectl delete -f virtual-node.yaml
```

Následující ukázkový příkaz zakáže virtuální uzly Linuxu:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Teď odeberte prostředky virtuální sítě a skupinu prostředků:

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

V tomto článku pod byl naplánován na virtuální uzel a přiřazena soukromá, interní IP adresa. Místo toho můžete vytvořit nasazení služby a směrovat provoz do podu prostřednictvím správce zatížení nebo řadiče příchozího přenosu dat. Další informace naleznete [v tématu Vytvoření základního řadiče příchozího přenosu dat v AKS][aks-basic-ingress].

Virtuální uzly jsou často jednou součástí škálování řešení v AKS. Další informace o škálovacích řešeních naleznete v následujících článcích:

- [Použití automatického měřítka kubernetesových horizontálních podů][aks-hpa]
- [Použití automatického škálování clusteru Kubernetes][aks-cluster-autoscaler]
- [Podívejte se na ukázku automatického škálování pro virtuální uzly][virtual-node-autoscale]
- [Přečtěte si více o open source knihovně Virtual Kubelet][virtual-kubelet-repo]

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
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
