---
title: Konfigurace sítí kubenet ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nakonfigurovat kubenet (základní) síť ve službě Azure Kubernetes Service (AKS) k nasazení clusteru AKS do existující virtuální sítě a podsítě.
services: container-service
ms.topic: article
ms.date: 06/26/2019
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 119265efa7b6504f3faf2e89cb68b9e9bd70bf9f
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617246"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Použití sítí kubenet s vlastními rozsahy IP adres ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení clustery AKS používají [kubenet][kubenet]a virtuální síť Azure a podsíť jsou vytvořeny za vás. S *kubenet*, uzly získat IP adresu z podsítě virtuální sítě Azure. Pody získají IP adresu z logicky jiného adresního prostoru do podsítě virtuální sítě Azure uzlů. Překlad adres (NAT) se pak nakonfiguruje tak, aby pody mohly získat přístup k prostředkům ve virtuální síti Azure. Zdrojová IP adresa provozu je NAT'd na primární IP adresu uzlu. Tento přístup výrazně snižuje počet adres IP, které je třeba rezervovat v síťovém prostoru pro pody k použití.

S [Azure Container Networking Interface (CNI)][cni-networking]získá každý pod IP adresu z podsítě a je přístupný přímo. Tyto adresy IP musí být jedinečné v celém síťovém prostoru a musí být naplánovány předem. Každý uzel má konfigurační parametr pro maximální počet podů, které podporuje. Ekvivalentní počet ADRES IP na uzel je pak rezervován předem pro tento uzel. Tento přístup vyžaduje více plánování a často vede k vyčerpání IP adresy nebo potřeba znovu sestavit clustery ve větší podsíti, jak vaše požadavky na aplikace rostou.

Tento článek ukazuje, jak pomocí *kubenet* sítě vytvořit a použít podsíť virtuální sítě pro cluster AKS. Další informace o možnostech sítě a jejich důležité informace naleznete v [tématu Síťové koncepty pro Kubernetes a AKS][aks-network-concepts].

## <a name="prerequisites"></a>Požadavky

* Virtuální síť pro cluster AKS musí umožňovat odchozí připojení k Internetu.
* Nevytvářejte více než jeden cluster AKS ve stejné podsíti.
* Clustery AKS nesmí `169.254.0.0/16` `172.30.0.0/16`používat `172.31.0.0/16`, `192.0.2.0/24` , , nebo pro rozsah adres služby Kubernetes.
* Instanční objekt používaný clusterem AKS musí mít alespoň roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md#network-contributor) v podsíti ve vaší virtuální síti. Pokud chcete definovat [vlastní roli](../role-based-access-control/custom-roles.md) namísto použití předdefinované role přispěvatele sítě, jsou vyžadována následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Chcete-li použít fondy uzlů Windows Server (aktuálně ve verzi preview v AKS), musíte použít Azure CNI. Použití kubenet jako model sítě není k dispozici pro kontejnery Windows Server.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.65 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Přehled sítí kubenet s vlastní podsítí

V mnoha prostředích jste definovali virtuální sítě a podsítě s přidělenými rozsahy adres IP. Tyto prostředky virtuální sítě se používají k podpoře více služeb a aplikací. Chcete-li poskytnout připojení k síti, clustery AKS můžete použít *kubenet* (základní sítě) nebo Azure CNI *(rozšířené sítě).*

S *kubenet*, pouze uzly přijímat IP adresu v podsíti virtuální sítě. Lusky nemohou komunikovat přímo mezi sebou. Místo toho uživatelem definované směrování (UDR) a ip předávání se používá pro připojení mezi pody mezi uzly. Můžete také nasadit pody za službu, která přijímá přiřazenou IP adresu a zatížení vyvažování provozu pro aplikaci. Následující diagram znázorňuje, jak uzly AKS přijímají adresu IP v podsíti virtuální sítě, ale ne pody:

![Síťový model Kubenet s clusterem AKS](media/use-kubenet/kubenet-overview.png)

Azure podporuje maximálně 400 tras v UDR, takže nemůžete mít cluster AKS větší než 400 uzlů. [Virtuální uzly][virtual-nodes] AKS a zásady sítě Azure nejsou podporovány *pomocí kubenet*.  Můžete použít [zásady sítě Calico][calico-network-policies], protože jsou podporovány kubenet.

S *Azure CNI*, každý pod obdrží IP adresu v podsíti IP a můžete přímo komunikovat s ostatními pody a služby. Vaše clustery mohou být stejně velké jako rozsah IP adres, který zadáte. Rozsah IP adres však musí být naplánován předem a všechny adresy IP jsou spotřebovány uzly AKS na základě maximálního počtu podů, které mohou podporovat. Pokročilé síťové funkce a scénáře, jako jsou [virtuální uzly][virtual-nodes] nebo zásady sítě (Azure nebo Calico) jsou podporované pomocí *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Dostupnost a vyčerpání IP adresy

U *Azure CNI*je běžným problémem, že rozsah přiřazených ADRES IP je příliš malý na to, aby při škálování nebo upgradu clusteru přidal další uzly. Síťový tým také nemusí být schopen vydat dostatečně velký rozsah IP adres pro podporu očekávaných požadavků na aplikace.

Jako kompromis můžete vytvořit cluster AKS, který používá *kubenet* a připojit se k existující podsíti virtuální sítě. Tento přístup umožňuje uzlům přijímat definované adresy IP, aniž by bylo nutné rezervovat velký počet ADRES IP předem pro všechny potenciální pody, které by mohly být spuštěny v clusteru.

S *kubenet*, můžete použít mnohem menší rozsah IP adres a být schopen podporovat velké clustery a požadavky aplikací. Například i s rozsahem IP adres */27* můžete spustit cluster 20-25 uzlů s dostatkem místa pro škálování nebo upgrade. Tato velikost clusteru by podporovala až *2 200 až 2 750* podů (s výchozím maximálně 110 pody na uzel). Maximální počet podů na uzel, který můžete nakonfigurovat pomocí *kubenet* v AKS je 110.

Následující základní výpočty porovnávají rozdíl v síťových modelech:

- **kubenet** - jednoduchý */24* IP rozsah adres může podporovat až *251* uzlů v clusteru (každá podsíť virtuální sítě Azure rezervuje první tři IP adresy pro operace správy)
  - Tento počet uzlů může podporovat až *27 610* podů (s výchozím maximem 110 podů na uzel s *kubenet)*
- **Azure CNI** – stejný základní rozsah podsítě */24* může podporovat pouze maximálně *8* uzlů v clusteru
  - Tento počet uzlů může podporovat pouze až *240* podů (s výchozí maximálně 30 podů na uzel s *Azure CNI)*

> [!NOTE]
> Tato maxima neberou v úvahu operace upgradu nebo škálování. V praxi nelze spustit maximální počet uzlů, které podporuje rozsah ip adres podsítě. Některé adresy IP je nutné ponechat k dispozici pro použití během operací upgradu.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Partnerský vztah virtuální sítě a připojení ExpressRoute

Chcete-li zajistit místní připojení, přístupy k síti *kubenet* i *Azure-CNI* můžete použít [partnerský vztah virtuální sítě Azure][vnet-peering] nebo připojení [ExpressRoute][express-route]. Pečlivě naplánujte rozsahy IP adres, abyste zabránili překrytí a nesprávnému směrování provozu. Například mnoho místních sítí používá rozsah adres *10.0.0.0/8,* který je inzerován přes připojení ExpressRoute. Doporučujeme vytvořit clustery AKS do podsítí virtuálních sítí Azure mimo tento rozsah adres, například *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Výběr síťového modelu, který chcete použít

Volba síťového pluginu, který chcete použít pro cluster AKS, je obvykle rovnováhou mezi flexibilitou a pokročilými potřebami konfigurace. Následující důležité informace pomáhají nastínit, kdy každý model sítě může být nejvhodnější.

*Kubenet použijte,* když:

- Máte omezený adresní prostor IP.
- Většina komunikace pod je v clusteru.
- Nepotřebujete pokročilé funkce AKS, jako jsou virtuální uzly nebo zásady sítě Azure.  Používejte [zásady sítě Calico][calico-network-policies].

*Azure CNI* použijte v::

- Máte k dispozici adresní prostor IP.
- Většina komunikace pod je prostředky mimo cluster.
- Nechcete spravovat UDR.
- Potřebujete pokročilé funkce AKS, jako jsou virtuální uzly nebo zásady sítě Azure.  Používejte [zásady sítě Calico][calico-network-policies].

Další informace, které vám pomohou rozhodnout, který síťový model použít, naleznete [v tématu Porovnání síťových modelů a jejich rozsahu podpory][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Chcete-li začít s použitím *kubenet* a vlastní podsítě virtuální sítě, nejprve vytvořte skupinu prostředků pomocí příkazu [az group create.][az-group-create] Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pokud nemáte existující virtuální síť a podsíť, které chcete použít, vytvořte tyto síťové prostředky pomocí příkazu [vytvořit síť az síť.][az-network-vnet-create] V následujícím příkladu je virtuální síť pojmenována *myVnet* s předponou adresy *192.168.0.0/16*. Podsíť je vytvořena s názvem *myAKSSubnet* s předponou adresy *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Vytvoření instančního objektu a přiřazení oprávnění

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Instanční objekt musí mít oprávnění ke správě virtuální sítě a podsítě, které používají uzly AKS. Chcete-li vytvořit instanční objekt, použijte příkaz [az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Následující příklad výstupu ukazuje ID aplikace a heslo pro instanční objekt. Tyto hodnoty se používají v dalších krocích k přiřazení role instančnímu objektu a následnému vytvoření clusteru AKS:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Chcete-li přiřadit správné delegování ve zbývajících krocích, použijte příkazy [show az sítě][az-network-vnet-show] sítě [aaz sítě az k][az-network-vnet-subnet-show] získání požadovaných ID prostředků. Tato ID prostředků jsou uložena jako proměnné a odkazována ve zbývajících krocích:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Nyní přiřaďte instanční objekt pro oprávnění *přispěvatele* clusteru AKS ve virtuální síti pomocí příkazu [vytvořit přiřazení role az.][az-role-assignment-create] Zadejte vlastní * \<appId>* jak je znázorněno na výstupu z předchozího příkazu k vytvoření instančního objektu:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Vytvoření clusteru AKS ve virtuální síti

Nyní jste vytvořili virtuální síť a podsíť a vytvořili a přiřadili oprávnění pro instanční objekt k použití těchto síťových prostředků. Nyní vytvořte cluster AKS ve virtuální síti a podsíti pomocí příkazu [az aks create.][az-aks-create] Definujte vlastní * \<aplikační* zabezpečení služby>a * \<>hesla *, jak je znázorněno na výstupu z předchozího příkazu k vytvoření instančního objektu.

Následující rozsahy adres IP jsou také definovány jako součást procesu vytváření clusteru:

* *--service-cidr* se používá k přiřazení interních služeb v clusteru AKS k adrese IP. Tento rozsah ADRES IP by měl být adresní prostor, který se nepoužívá jinde v síťovém prostředí. Tento rozsah zahrnuje všechny rozsahy místnísítě, pokud se připojíte nebo plánujete připojit virtuální sítě Azure pomocí expresní trasy nebo připojení VPN site-to-site.

* Adresa *--dns-service-IP* by měla být adresa *.10* rozsahu IP adres vaší služby.

* *--pod-cidr* by měl být velký adresní prostor, který není používán jinde v síťovém prostředí. Tento rozsah zahrnuje všechny rozsahy místnísítě, pokud se připojíte nebo plánujete připojit virtuální sítě Azure pomocí expresní trasy nebo připojení VPN site-to-site.
    * Tento rozsah adres musí být dostatečně velký, aby se přizpůsobil počtu uzlů, které očekáváte, že navštěvujete kapacitu. Tento rozsah adres nelze po nasazení clusteru změnit, pokud potřebujete více adres pro další uzly.
    * Rozsah adres pod IP se používá k přiřazení adresního prostoru */24* každému uzlu v clusteru. V následujícím příkladu *--pod-cidr* *10.244.0.0/16* přiřadí první uzel *10.244.0.0/24*, druhý uzel *10.244.1.0/24*a třetí uzel *10.244.2.0/24*.
    * Jak se cluster škáluje nebo upgraduje, platforma Azure nadále přiřazuje rozsah IP adres podu každému novému uzlu.
    
* *Adresa --docker-bridge-address* umožňuje uzlům AKS komunikovat s platformou pro správu. Tato adresa IP nesmí být v rozsahu ip adres virtuální sítě v clusteru a neměla by se překrývat s jinými rozsahy adres, které se používají v síti.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Chcete-li povolit clusteru AKS zahrnout [zásady sítě Calico,][calico-network-policies] můžete použít následující příkaz.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Při vytváření clusteru AKS se vytvoří skupina zabezpečení sítě a směrovací tabulka. Tyto síťové prostředky jsou spravovány řídicí rovinou AKS. Skupina zabezpečení sítě je automaticky přidružena k virtuálním síťovým terminálem na vašich uzlech. Směrovací tabulka je automaticky přidružena k podsíti virtuální sítě. Pravidla skupiny zabezpečení sítě a směrovací tabulky jsou automaticky aktualizovány při vytváření a zpřístupňujení služby.

## <a name="next-steps"></a>Další kroky

Když je cluster AKS nasazený do vaší stávající podsítě virtuální sítě, můžete ho teď používat normálně. Začínáme s [vytvářením aplikací pomocí Azure Dev Spaces][dev-spaces] nebo [pomocí Konceptu][use-draft]nebo [nasazujte aplikace pomocí Helmu][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

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
[network-comparisons]: concepts-network.md#compare-network-models
