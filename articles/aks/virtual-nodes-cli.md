---
title: Vytvořit virtuální uzly pomocí Azure CLI ve službě Azure Kubernetes služby (AKS)
description: Zjistěte, jak pomocí Azure CLI k vytvoření clusteru služby Azure Kubernetes (AKS), který používá ke spuštění podů virtuální uzly.
services: container-service
author: iainfoulds
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 7d12e0f53796713df83b1cbb9e55695598c29077
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607383"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Vytvoření a konfigurace clusteru služby Azure Kubernetes služby (AKS) používat virtuální uzly pomocí Azure CLI

Rychlé škálování úloh aplikací v clusteru služby Azure Kubernetes Service (AKS), můžete použít virtuální uzly. S virtuální uzly mají rychlé zřízení podů a platíte jenom za sekundu pro jejich spuštění. Nemusíte čekat automatického škálování clusteru Kubernetes nasadit virtuální počítač výpočetních uzlů pro spouštění budou další pody. Tento článek ukazuje, jak vytvořit a konfigurovat prostředky virtuální sítě a clusteru AKS a potom povolit virtuální uzly.

> [!IMPORTANT]
> Virtuální uzly pro AKS jsou nyní v **ve verzi preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Před zahájením

Virtuální uzly povolit síťovou komunikaci mezi pody spuštěné v ACI a AKS clusteru. Pro tuto komunikaci, se vytvoří podsíť virtuální sítě a jsou přiřazeny delegovaná oprávnění. Virtuální uzly fungovat jenom s clustery AKS vytvořeného *pokročilé* sítě. Ve výchozím nastavení, AKS clustery jsou vytvořeny pomocí *základní* sítě. Tento článek ukazuje, jak vytvořit virtuální síť a podsítě a pak Nasaďte cluster AKS, který používá rozšířeného sítě.

Pokud jste dříve nepoužili ACI, zaregistrujte poskytovatele služeb s vaším předplatným. Můžete zkontrolovat stav registrace poskytovatele ACI pomocí [az provider list] [ az-provider-list] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* poskytovatele hlásit jako *registrované*, jak je znázorněno v následujícím příkladu výstupu:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Pokud poskytovatel zobrazí jako *NotRegistered*, zaregistrujte poskytovatele pomocí [az provider register] [ az-provider-register] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Chcete-li spustit Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud chcete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.49 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create]. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě pomocí [az network vnet vytvořit] [ az-network-vnet-create] příkazu. Následující příklad vytvoří název virtuální sítě *myVnet* s předponou adresy *10.0.0.0/8*a podsíť s názvem *myAKSSubnet*. Výchozí hodnota předpony adresy dané podsítě *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Teď vytvořte další podsítě pro virtuální uzly pomocí [az podsíti virtuální sítě vytvořit] [ az-network-vnet-subnet-create] příkazu. Následující příklad vytvoří podsíť s názvem *myVirtualNodeSubnet* s předponou adresy *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefix 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Tento instanční objekt se dá automaticky vytvořit pomocí rozhraní příkazového řádku Azure nebo portálu, nebo si ho můžete předem vytvořit a přiřadit další oprávnění.

Vytvoření instančního objektu služby pomocí [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] příkazu. Parametr `--skip-assignment` nastavuje omezení, aby už nešla přidělovat žádná další oprávnění.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu:

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Poznamenejte si *appId* a *password*. Tyto hodnoty se použijí v dalších krocích.

## <a name="assign-permissions-to-the-virtual-network"></a>Přiřadit oprávnění k virtuální síti

Povolit clusteru využívat a spravovat virtuální sítě, je nutné udělit instanční objekt služby AKS správná oprávnění k použití síťových prostředků.

Nejprve Získejte ID prostředku virtuální sítě pomocí [az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Poskytnout správný přístup pro cluster AKS používat virtuální síť vytvořit přiřazení role pomocí [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu. Nahraďte `<appId`> a `<vnetId>` hodnotami získanými v předchozích dvou krocích.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Nasaďte cluster AKS do AKS podsíť vytvořená v předchozím kroku. Získejte ID podsítě pomocí [az network vnet podsíť zobrazit][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Nahraďte `<subnetId>` s ID, kterou jste získali v předchozím kroku a potom `<appId>` a `<password>` s 

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

## <a name="enable-virtual-nodes"></a>Povolit virtuální uzly

K poskytnutí dalších funkcí, konektor virtuální uzly používá rozšíření rozhraní příkazového řádku Azure. Před povolením konektoru virtuální uzly, nejprve nainstalujte pomocí rozšíření [přidat rozšíření az] [ az-extension-add] příkaz:

```azurecli-interactive
az extension add --source https://aksvnodeextension.blob.core.windows.net/aks-virtual-node/aks_virtual_node-0.2.0-py2.py3-none-any.whl
```

Povolit virtuální uzly, teď můžete [az aks enable-addons] [ az-aks-enable-addons] příkazu. V následujícím příkladu používá podsíť s názvem *myVirtualNodeSubnet* vytvořili v předchozím kroku:

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

Následující příklad výstupu ukazuje jediného uzlu virtuálního počítače vytvořena a pak virtuální uzel pro Linux, *virtuální node-aci-linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Nasaďte ukázkovou aplikaci

Vytvořte soubor s názvem `virtual-node.yaml` a zkopírujte do následující kód YAML. Naplánování kontejneru na uzlu, [nodeSelector] [ node-selector] a [toleration] [ toleration] jsou definovány.

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

Spusťte aplikaci [použití kubectl] [ kubectl-apply] příkazu.

```console
kubectl apply -f virtual-node.yaml
```

Použití [kubectl get pods] [ kubectl-get] příkazů `-o wide` argument do výstupního seznam podů a plánované uzlu. Všimněte si, že `aci-helloworld` pod byla naplánována na `virtual-node-aci-linux` uzlu.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod přiřazena vnitřní IP adresu z podsítě virtuální sítě Azure delegované pro použití s virtuálními uzly.

## <a name="test-the-virtual-node-pod"></a>Testovat virtuální uzel pod

K otestování spuštěný na virtuální uzel pod, přejděte na ukázkovou aplikaci pomocí webového klienta. Jak chcete pod přiřazena vnitřní IP adresu, můžete rychle otestovat toto připojení z jiného podu na clusteru AKS. Vytvoření testů pod a připojit se k němu Terminálové relaci:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Nainstalujte `curl` v podu pomocí `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Nyní přístup k adrese pod pomocí `curl`, jako například *http://10.241.0.4*. Zadejte vlastní interní IP adresa je znázorněno v předchozí `kubectl get pods` příkaz:

```console
curl -L http://10.241.0.4
```

Ukázkové aplikace se zobrazí, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zavřete relaci Terminálové služby pro váš test pod s `exit`. Po ukončení relace je pod odstraněným.

## <a name="remove-virtual-nodes"></a>Odebrat virtuální uzly

Pokud již nechcete používat virtuální uzly, můžete je zakázat pomocí [az aks disable-addons] [ az aks disable-addons] příkazu. Následující příklad zakazuje virtuální uzly Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Teď odeberte prostředky virtuální sítě a skupina prostředků:

```azurecli-interactive
# Change the name of your resource group and network resources as needed
RES_GROUP=myResourceGroup

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --remove delegations 0
```

## <a name="next-steps"></a>Další postup

V tomto článku pod byla naplánována na virtuální uzel a přiřazenou IP adresu privátní a interní. Místo toho byste třeba vytvořit nasazení služby a směrovat provoz do podu prostřednictvím nástroje pro vyrovnávání zatížení nebo kontroler příchozího přenosu dat. Další informace najdete v tématu [vytvoříte řadič základního příchozího přenosu dat ve službě AKS][aks-basic-ingress].

Virtuální uzly jsou často jedna komponenta škálování řešení ve službě AKS. Další informace o škálování řešení najdete v následujících článcích:

- [Použití automatického škálování vodorovné podů Kubernetes][aks-hpa]
- [Použití automatického škálování clusteru Kubernetes][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/

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
