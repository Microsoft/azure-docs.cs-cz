---
title: Konfigurace kubenet sítě ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak konfigurovat sítě kubenet (základní) ve službě Azure Kubernetes Service (AKS) k nasazení clusteru AKS na existující virtuální síť a podsíť.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/31/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 6d2b6ce2804fce35af9c184c4a7c72c0b332f6fb
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701720"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Použít kubenet práce se sítěmi pomocí vlastní rozsahy IP adres ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení, AKS clusterů použijte [kubenet][kubenet], a virtuální síť Azure a podsíť se vytvoří za vás. S *kubenet*, uzly získat IP adresu z podsítě virtuální sítě Azure. Podů obdrží IP adresu z logicky jiným adresním prostorem podsítě virtuální sítě Azure z uzlů. Překlad síťových adres (NAT) je nakonfigurovaný tak, aby tyto pody může mít přístup k prostředkům ve službě Azure virtual network. Síťový provoz na IP adresu zdrojového je že NAT by uzlu primární IP adresa. Tento přístup výrazně snižuje počet IP adres, které je nutné rezervovat v prostoru podů použití vaší sítě.

S [služby Azure Container síťové rozhraní (CNI)][cni-networking], každý pod získá IP adresu z podsítě a je přístupný přímo. Tyto IP adresy musí být jedinečný v rámci prostor vaší sítě a je třeba se připravit předem. Každý uzel má parametr konfigurace pro maximální počet podů, které podporuje. Ekvivalentní počet IP adres na jedno uzlu jsou pak ještě před zahájením vyhrazené pro tento uzel. Tento přístup vyžaduje více plánování a často vede k vyčerpání IP adresu nebo nutnosti znovu sestavovat clusterů ve větší podsíť, jak rostou vaše požadavky aplikace růst.

V tomto článku se dozvíte, jak používat *kubenet* sítě a vytvořit podsítě virtuální sítě pro AKS cluster. Další informace o možnostech sítě a důležité informace najdete v tématu [síť koncepty pro Kubernetes a AKS][aks-network-concepts].

## <a name="before-you-begin"></a>Před zahájením

Musí mít Azure CLI verze 2.0.56 nebo později nainstalována a nakonfigurována. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Přehled práce se sítěmi pomocí vlastní podsítě kubenet

V mnoha prostředích jste definovali virtuální sítě a podsítě s přidělené rozsahy IP adres. Tyto prostředky virtuální sítě se používá pro podporu více služeb a aplikací. K zajištění síťového připojení, můžete použít AKS clustery *kubenet* (základní síť) nebo Azure CNI (*rozšířeného sítě*).

S *kubenet*pouze uzly v podsíti virtuální sítě, dostanou IP adresu. Podů nemůže přímo spolu vzájemně komunikovat. Místo toho předávání uživatelem definovanou směrování (UDR) a IP adresa se používá pro připojení mezi pody mezi uzly. Můžete také nasadit podů za službu, která přijímá přiřazenou IP adresu a vyrovnává zatížení provozu pro aplikace. Následující diagram znázorňuje, jak uzlů AKS, dostanou IP adresu v podsíti virtuální sítě, ale ne pody:

![Model síťového Kubenet s clusterem AKS](media/use-kubenet/kubenet-overview.png)

Azure podporuje maximálně 400 směrování v trase UDR, proto nemůže mít větší než 400 uzly clusteru AKS. AKS funkce, jako [virtuální uzly] [ virtual-nodes] nebo zásad sítě se nepodporují *kubenet*.

S *Azure CNI*, každý pod obdrží IP adresu v podsíti protokolu IP a můžete přímo komunikovat s ostatními podů a službami. Clusterům můžou být velké jako rozsah IP adres, které zadáte. Ale rozsah IP adres je třeba se připravit předem a všechny IP adresy se spotřebovávají uzlů AKS, které jsou založeny na maximální počet podů, které může podporovat. Pokročilé scénáře a síťové funkce, jako [virtuální uzly] [ virtual-nodes] nebo zásady sítě jsou podporovány s *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Dostupnost IP adresy a vyčerpání

S *Azure CNI*, běžný problémem je přiřazené rozsah IP adres je příliš malá, aby pak přidejte další uzly při škálování nebo upgradu clusteru. Tým síťových nemusí být také dokázala vystavovat dostatečně velký rozsah IP adres pro podporu vašich očekávané zatížení.

Jako ohrožení zabezpečení, můžete vytvořit cluster AKS, který používá *kubenet* a připojte se k existující podsíti virtuální sítě. Tento přístup umožňuje uzly přijímat definovaných IP adres, aniž by bylo nutné rezervovat velký počet IP adres ještě před zahájením pro všechny potenciální podů, které může běžet v clusteru.

S *kubenet*, můžete použít mnohem menší rozsah IP adres a být schopni poskytovat podporu velkých clusterech a požadavky na aplikace. Například v případě */27* rozsah IP adres, můžete spustit 20 25 uzel clusteru s dostatek místa pro škálování nebo upgradovat. Tato velikost clusteru by podporují až *2 200 2,750* pody (s výchozí maximální hodnoty 110 podů na uzel).

Následující základní výpočty porovnat rozdíl v modelech sítě:

- **kubenet** – jednoduchý */24* rozsah IP adres může podporovat až *251* uzly v clusteru (každé podsíti virtuální sítě Azure si vyhrazuje první tři IP adresy pro operace správy)
  - Tento počet uzlů může podporovat až *27,610* pody (s výchozí maximální hodnoty 110 podů na uzel s *kubenet*)
- **Azure CNI** -této jedné úrovně basic */24* rozsahu podsítě pouze může podporovat maximálně *8* uzly v clusteru
  - Tento počet uzlů může podporovat pouze až *240* pody (s výchozí maximální hodnoty počtu uzlů, které se 30 podů *Azure CNI*)

> [!NOTE]
> Maximální tyto hodnoty nemáte brát v upgradu účtu nebo operace škálování. V praxi nelze spustit maximální počet uzlů, které podporuje rozsah IP adres podsítě. Je třeba ponechat některé IP adresy, které jsou k dispozici pro použití během upgradu operací škálování.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Partnerský vztah virtuální sítě a připojení ExpressRoute

K zajištění připojení s místními i *kubenet* a *Azure CNI* přístupy sítě můžete použít [partnerský vztah virtuální sítě Azure] [ vnet-peering]nebo [připojení ExpressRoute][express-route]. Plánování rozsahů IP adres pečlivě, aby se zabránilo překrytí a směrování síťového provozu na nesprávné. Například použít mnoho místních sítí *10.0.0.0/8* adresu rozsahu, který inzerován prostřednictvím připojení ExpressRoute. Doporučuje se vytvořit clusterům AKS do podsítí virtuální sítě Azure mimo tento rozsah adres, například *172.26.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Zvolte model sítě pro použití

Volba, které modul plug-in sítě pro vaše AKS clusteru je obvykle potřebuje rovnováhu mezi flexibilitou a pokročilou konfiguraci. Následující aspekty pomoct osnovy každý model sítě může být nejvhodnější.

Použití *kubenet* při:

- Mají omezenou adresní prostor IP adres.
- Většina komunikace pod je v rámci clusteru.
- Není nutné pokročilé funkce, jako jsou virtuální uzly nebo zásady sítě.

Použití *Azure CNI* při:

- Máte k dispozici adresní prostor IP adres.
- Většina komunikace pod je na prostředky mimo cluster.
- Nechcete spravovat trasy definované uživatelem.
- Potřebujete pokročilé funkce, jako jsou virtuální uzly nebo zásady sítě.

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Abyste mohli začít s používáním *kubenet* a vlastní podsítě virtuální sítě, nejdřív vytvořte skupinu prostředků pomocí [vytvořit skupiny az] [ az-group-create] příkazu. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pokud nemáte existující virtuální síť a podsíť, která se pomocí služby, vytvoření těchto síťových prostředků pomocí [az network vnet vytvořit] [ az-network-vnet-create] příkazu. V následujícím příkladu je virtuální síť s názvem *myVnet* s předponou adresy *10.0.0.0/8*. Podsíť je vytvořena s názvem *myAKSSubnet* s předponou adresy *10.240.0.0/16*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Vytvoření instančního objektu a přiřazení oprávnění

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Instanční objekt musí mít oprávnění ke správě virtuální síť a podsíť, která pomocí uzlů AKS. Chcete-li vytvořit instanční objekt služby, použijte [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] příkaz:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Následující příklad výstupu ukazuje ID aplikace a heslo instančního objektu služby. Tyto hodnoty se používají v dalších krocích přiřazení role pro instanční objekt a potom vytvořit AKS cluster:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Chcete-li přiřadit správná delegace ve zbývajících krocích, použijte [az network vnet show] [ az-network-vnet-show] a [az network vnet podsíť zobrazit] [ az-network-vnet-subnet-show] příkazy získat požadované ID prostředků. Těchto ID prostředků jsou uloženy jako proměnné a odkazované ve zbývajících krocích:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Nyní přiřadit instanční objekt služby pro váš cluster AKS *Přispěvatel* oprávnění k používání virtuální sítě [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu. Zadejte vlastní */ <appId/>* jak znázorňuje výstup z předchozího příkazu k vytvoření instančního objektu služby:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Vytvoření clusteru AKS ve virtuální síti

Právě jste vytvořili virtuální síť a podsíť a vytvořit a přiřadit oprávnění pro objekt služby k použití těchto síťových prostředků. Teď vytvořte AKS cluster ve vaší virtuální sítě a podsítě pomocí [az aks vytvořit] [ az-aks-create] příkazu. Definovat vlastní instanční objekt služby */ <appId/>* a */ <password/>*, jak znázorňuje výstup z předchozího příkazu k vytvoření instančního objektu služby.

Tyto rozsahy IP adres jsou také definovány jako součást clusteru vytvořit proces:

* *--Cidr služby* slouží k přiřazení IP adresy interní služby v clusteru AKS. Tento rozsah IP adres by měl být adresní prostor, který se používá jinde ve vašem síťovém prostředí. To zahrnuje žádnými rozsahy adres místní sítě, pokud připojení, nebo v plánu připojit, virtuálním sítím Azure přes Express Route nebo připojení Site-to-Site VPN.

* *--Dns-service-ip* adresa by měla být *.10* adresu vašeho rozsahu IP adres služby.

* *– Pod cidr* by měl být velký adresní prostor, který se používá jinde ve vašem síťovém prostředí. To zahrnuje žádnými rozsahy adres místní sítě, pokud připojení, nebo v plánu připojit, virtuálním sítím Azure přes Express Route nebo připojení Site-to-Site VPN.
    * Tento rozsah adres musí být dostatečně velký, aby mohla pojmout množství uzly, které plánujete škálovat až. Tento rozsah adres nelze změnit, až se cluster nasazuje, pokud potřebujete další adresy pro další uzly.
    * Rozsah IP adres pod slouží k přiřazení */24* adresní prostor k jednotlivým uzlům v clusteru. V následujícím příkladu *– pod cidr* z *192.168.0.0/16* přiřadí první uzel *192.168.0.0/24*, druhý uzel *192.168.1.0/24*a třetí uzly *192.168.2.0/24*.
    * Jako škálování clusteru nebo upgrady Platforma Azure nadále jste rozsah IP adres pod přiřadit každého nového uzlu.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

## <a name="associate-network-resources-with-the-node-subnet"></a>Přidružit podsíť uzel síťových prostředků

Při vytváření clusteru AKS, se vytvoří tabulky skupiny a postupu zabezpečení sítě. Tyto síťové prostředky jsou spravována rovina řízení AKS a aktualizovat, protože můžete vytvořit tak a zpřístupnit služby. Přidružte síťové zabezpečení skupiny a směrovací tabulky podsítě vaší virtuální sítě následujícím způsobem:

```azurecli-interactive
# Get the MC_ resource group for the AKS cluster resources
MC_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)

# Get the route table for the cluster
ROUTE_TABLE=$(az network route-table list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Get the network security group
NODE_NSG=$(az network nsg list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Update the subnet to associate the route table and network security group
az network vnet subnet update \
    --route-table $ROUTE_TABLE \
    --network-security-group $NODE_NSG \
    --ids $SUBNET_ID
```

## <a name="next-steps"></a>Další postup

Cluster AKS nasadí do vaší existující podsíti virtuální sítě můžete nyní pomocí clusteru jako za normálních okolností. Začínáme s [vytváření aplikací s použitím Azure Dev prostory] [ dev-spaces] nebo [pomocí nástroje Draft][use-draft], nebo [nasazení aplikací pomocí Helm] [use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
