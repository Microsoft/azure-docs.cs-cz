---
title: Konfigurace sítě kubenet ve službě Azure Kubernetes Service (AKS)
description: Naučte se konfigurovat síť kubenet (Basic) ve službě Azure Kubernetes Service (AKS), která umožňuje nasadit cluster AKS do existující virtuální sítě a podsítě.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: c30b82e44833e413c1576bf64e8fef263c58b246
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91264605"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Používání sítě kubenet s vlastními rozsahy IP adres ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení clustery AKS používají [kubenet][kubenet]a pro vás vytvoří virtuální síť Azure a podsíť. Pomocí *kubenet*uzly získají IP adresu z podsítě virtuální sítě Azure. Pody získají IP adresu z logicky jiného adresního prostoru do podsítě virtuální sítě Azure uzlů. Překlad adres (NAT) se pak nakonfiguruje tak, aby pody mohly získat přístup k prostředkům ve virtuální síti Azure. Zdrojová IP adresa provozu je NAT k primární IP adrese uzlu. Tento přístup významně snižuje počet IP adres, které je třeba vyhradit v síťovém prostoru pro použití v luskech.

Díky [rozhraní CNI (Azure Container Networking Interface)][cni-networking]každý z nich získá IP adresu z podsítě a dá se k nim získat přímý pøístup. Tyto IP adresy musí být v rámci vašeho síťového prostoru jedinečné a musí být plánovány předem. Každý uzel má parametr konfigurace maximálního počtu lusků, které podporuje. Pro tento uzel je pak rezervovaný i ekvivalentní počet IP adres na uzel. Tento přístup vyžaduje více plánování a často vede k vyčerpání IP adres nebo k nutnosti opětovnému sestavení clusterů ve větší podsíti, dokud vaše aplikace požaduje růst. Můžete nakonfigurovat maximální počet lusků nasazení na uzel v clusteru vytvoření času nebo při vytváření nových fondů uzlů. Pokud při vytváření nových fondů uzlů nezadáte maxPods, zobrazí se výchozí hodnota 110 pro kubenet.

V tomto článku se dozvíte, jak pomocí sítě *kubenet* vytvořit a použít podsíť virtuální sítě pro cluster AKS. Další informace o možnostech a požadavcích sítě najdete v tématu [Koncepty sítě pro Kubernetes a AKS][aks-network-concepts].

## <a name="prerequisites"></a>Požadavky

* Virtuální síť pro cluster AKS musí umožňovat odchozí připojení k Internetu.
* Nevytvářejte více než jeden cluster AKS ve stejné podsíti.
* Clustery AKS nemůžou `169.254.0.0/16` používat `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` Rozsah adres služby Kubernetes pod rozsahem adres nebo rozsah adres virtuální sítě clusteru.
* Instanční objekt používaný clusterem AKS musí mít alespoň roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) v podsíti v rámci vaší virtuální sítě. K vytvoření instančního objektu a přiřazení oprávnění k němu musíte mít také příslušná oprávnění, jako je vlastník předplatného. Pokud chcete místo používání předdefinované role přispěvatele sítě definovat [vlastní roli](../role-based-access-control/custom-roles.md) , vyžadují se následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Pokud chcete používat fondy uzlů Windows serveru, musíte použít Azure CNI. Použití kubenet jako síťového modelu není k dispozici pro kontejnery Windows serveru.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.65 nebo novější.  `az --version`Verzi zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Přehled sítě kubenet s vlastní podsítí

V mnoha prostředích jste definovali virtuální sítě a podsítě s přidělenými rozsahy IP adres. Tyto prostředky virtuální sítě se používají k podpoře více služeb a aplikací. Pro zajištění připojení k síti můžou clustery AKS používat *kubenet* (základní sítě) nebo Azure CNI (*Pokročilé sítě*).

V případě *kubenet*obdrží jenom uzly IP adresu v podsíti virtuální sítě. Lusky spolu nemůžou komunikovat přímo. Místo toho se k propojení mezi různými uzly používá uživatelem definované směrování (UDR) a předávání IP. Ve výchozím nastavení je udr a konfigurace předávání IP adres vytvořena a udržována službou AKS, ale je nutné použít možnost k zařazení vlastní [směrovací tabulky pro vlastní správu tras][byo-subnet-route-table]. Můžete také nasadit lusky za službu, která obdrží přiřazenou IP adresu a zatížení pro vyrovnávání zatížení pro aplikaci. Následující diagram znázorňuje, jak uzly AKS obdrží IP adresu v podsíti virtuální sítě, ale ne lusky:

![Model sítě Kubenet s clusterem AKS](media/use-kubenet/kubenet-overview.png)

Azure podporuje maximálně 400 tras v UDR, takže nemůžete mít cluster AKS větší než 400 uzlů. [Virtuální uzly][virtual-nodes] AKS a zásady sítě Azure nejsou podporované s *kubenet*.  Můžete použít [zásady sítě Calico][calico-network-policies], protože jsou podporované v kubenet.

V případě *Azure CNI*každý pod tím obdrží IP adresu v podsíti protokolu IP a může přímo komunikovat s dalšími lusky a službami. Clustery můžou být tak velké jako rozsah IP adres, který zadáte. Rozsah IP adres se ale musí naplánovat předem a všechny IP adresy se spotřebovávají AKS uzly na základě maximálního počtu lusků, které můžou podporovat. *Azure CNI*podporuje pokročilé síťové funkce a scénáře, jako jsou [virtuální uzly][virtual-nodes] nebo zásady sítě (buď Azure nebo Calico).

### <a name="limitations--considerations-for-kubenet"></a>Omezení & důležitých informací pro kubenet

* V návrhu kubenet se vyžaduje další směrování, které přičítá k vedlejší latenci v rámci komunikace.
* Směrovací tabulky a uživatelem definované trasy jsou vyžadovány pro použití kubenet, což přináší složitost na operace.
* Přímé adresování pod není podporované pro kubenet z důvodu návrhu kubenet.
* Na rozdíl od clusterů Azure CNI nemůže více clusterů kubenet sdílet podsíť.
* Funkce, **které nejsou podporovány v kubenet** , zahrnují:
   * [Zásady sítě Azure](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), ale zásady sítě Calico jsou podporované v kubenet.
   * [Fondy uzlů Windows](windows-node-limitations.md)
   * [Doplněk virtuálních uzlů](virtual-nodes-portal.md#known-limitations)

### <a name="ip-address-availability-and-exhaustion"></a>Dostupnost a vyčerpání IP adres

V případě *Azure CNI*je běžný problém, že přiřazený rozsah IP adres je příliš malý a pak při škálování nebo upgradu clusteru nepřidá další uzly. Síťový tým také nemusí být schopný vystavit velký dostatečný rozsah IP adres, aby podporoval očekávané požadavky aplikace.

Jako kompromis můžete vytvořit cluster AKS, který používá *kubenet* , a připojit se k existující podsíti virtuální sítě. Tento přístup umožňuje uzlům přijímat definované IP adresy, aniž by bylo nutné vyhradit velký počet IP adres vpřed pro všechny možné lusky, které by mohly běžet v clusteru.

Pomocí *kubenet*můžete použít mnohem menší rozsah IP adres a být schopný podporovat velké clustery a požadavky aplikací. Například s rozsahem IP adres */27* v podsíti můžete spustit cluster 20-25 Node s dostatkem místa pro škálování nebo upgrade. Tato velikost clusteru by podporovala až *2200-2750* lusky (s výchozím maximem 110 až na jeden uzel). Maximální počet lusků na uzel, který můžete nakonfigurovat pomocí *kubenet* v AKS, je 110.

Následující základní výpočty porovnávají rozdíl v síťových modelech:

- **kubenet** – jednoduchý */24* rozsah IP adres může podporovat až *251* uzlů v clusteru (Každá podsíť virtuální sítě Azure rezervuje první tři IP adresy pro operace správy).
  - Tento počet uzlů může podporovat až *27 610* lusky (s výchozím maximem 110 lusků na uzel s *kubenet*).
- **Azure CNI** – stejný rozsah podsítě Basic */24* by mohl podporovat maximálně *8* uzlů v clusteru.
  - Tento počet uzlů by podporoval jenom až *240* lusky (s výchozím maximálně 30 lusky na uzel s *Azure CNI*).

> [!NOTE]
> Tato maximální hodnota nebere v úvahu operace upgradu nebo škálování. V praxi nemůžete spustit maximální počet uzlů, které podporuje rozsah IP adres podsítě. Při škálování operací upgradu musíte nechat nějaké IP adresy dostupné pro použití.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Připojení partnerských vztahů virtuálních sítí a ExpressRoute

Aby bylo možné poskytovat místní připojení, přístupy k síti *kubenet* a *Azure-CNI* mohou použít připojení peer-to-peer nebo [ExpressRoute připojení][express-route] [virtuální sítě Azure][vnet-peering] . Rozsahy IP adres pečlivě naplánujte, abyste zabránili překrytí a nesprávnému směrování provozu. Mnoho místních sítí například používá rozsah adres *10.0.0.0/8* , který je inzerován přes připojení ExpressRoute. Doporučuje se vytvářet clustery AKS v podsítích virtuálních sítí Azure mimo tento rozsah adres, například *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Vyberte model sítě, který se má použít.

Volba síťového modulu plug-in, který se má použít pro cluster AKS, je obvykle rovnováhu mezi flexibilitou a pokročilými požadavky na konfiguraci. Následující informace vám pomohou obejít osnovu, když se každý model sítě nejvíce hodí.

*Kubenet* použijte v těchto případech:

- Máte omezený adresní prostor IP adres.
- Většina komunikace pod clusterem je v clusteru.
- Nepotřebujete rozšířené funkce AKS, jako jsou virtuální uzly nebo zásady sítě Azure.  Použijte [zásady sítě Calico][calico-network-policies].

*CNI Azure* použijte v těchto případech:

- Máte k dispozici adresní prostor IP adres.
- Většina komunikace pod je na prostředky mimo cluster.
- Nechcete spravovat trasy definované uživatelem pro připojení pod.
- Potřebujete AKS pokročilé funkce, jako jsou virtuální uzly nebo zásady sítě Azure.  Použijte [zásady sítě Calico][calico-network-policies].

Další informace, které vám pomůžou určit, který model sítě se má použít, najdete v tématu [porovnání síťových modelů a jejich oboru podpory][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

Pokud chcete začít používat *kubenet* a vlastní podsíť virtuální sítě, vytvořte nejprve skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] . Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pokud nemáte existující virtuální síť a podsíť k použití, vytvořte tyto síťové prostředky pomocí příkazu [AZ Network VNet Create][az-network-vnet-create] . V následujícím příkladu je virtuální síť s názvem *myVnet* s předponou adresy *192.168.0.0/16*. Vytvoří se podsíť s názvem *myAKSSubnet* s předponou adresy *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Vytvoření instančního objektu a přiřazení oprávnění

Aby mohl cluster AKS pracovat a komunikovat s jinými prostředky Azure, používá se instanční objekt služby Azure Active Directory. Instanční objekt musí mít oprávnění ke správě virtuální sítě a podsítě, kterou používají uzly AKS. Objekt služby vytvoříte pomocí příkazu [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] :

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

Pokud chcete přiřadit správné delegace ve zbývajících krocích, použijte k získání požadovaných ID prostředků příkaz [AZ Network VNet show][az-network-vnet-show] a [AZ Network VNet Subnet show][az-network-vnet-subnet-show] Commands. Tato ID prostředků se ukládají jako proměnné a jsou odkazována ve zbývajících krocích:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Nyní přiřaďte instančnímu objektu oprávnění *přispěvatele sítě* AKS clusteru k virtuální síti pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] . Zadejte vlastní *\<appId>* , jak je znázorněno ve výstupu z předchozího příkazu k vytvoření instančního objektu:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Vytvoření clusteru AKS ve virtuální síti

Nyní jste vytvořili virtuální síť a podsíť a vytvořili a přiřadili jste oprávnění instančnímu objektu pro použití těchto síťových prostředků. Teď ve své virtuální síti a podsíti vytvořte cluster AKS pomocí příkazu [AZ AKS Create][az-aks-create] . Definujte vlastní instanční objekt *\<appId>* *\<password>* , jak je znázorněno ve výstupu z předchozího příkazu pro vytvoření instančního objektu.

V rámci procesu vytváření clusteru se definují taky tyto rozsahy IP adres:

* *Služba--Service-CIDR* slouží k přiřazení IP adresy interním službám v clusteru AKS. Tento rozsah IP adres by měl být adresní prostor, který se nepoužívá jinde v síťovém prostředí, včetně jakýchkoli místních síťových rozsahů, pokud se připojujete nebo plánujete připojit, k virtuálním sítím Azure pomocí expresního postupu nebo připojení VPN typu Site-to-site.

* Adresa *--DNS-Service-IP* musí být *.10* adresou rozsahu IP adres služby.

* *--Pod-CIDR* by měl být velký adresní prostor, který se ve vašem síťovém prostředí nepoužívá jinde. Tento rozsah zahrnuje všechny místní síťové rozsahy, pokud se připojujete nebo plánujete připojit virtuální sítě Azure pomocí Express Route nebo připojení VPN typu Site-to-site.
    * Tento rozsah adres musí být dostatečně velký, aby odpovídal počtu uzlů, na které očekáváte horizontální navýšení kapacity. Po nasazení clusteru nemůžete tento rozsah adres změnit, pokud potřebujete víc adres pro další uzly.
    * Rozsah IP adres pod se používá k přiřazení adresního prostoru */24* k jednotlivým uzlům v clusteru. V následujícím příkladu se *--pod-CIDR* *10.244.0.0/16* přiřadí první uzel *10.244.0.0/24*, druhý uzel *10.244.1.0/24*a třetí uzel *10.244.2.0/24*.
    * Při škálování a upgradování clusteru bude platforma Azure dál přiřazovat rozsah IP adres pod na každý nový uzel.
    
* *Adresa--Docker-most* umožňuje uzlům AKS komunikovat s podkladovou platformou správy. Tato IP adresa nesmí spadat do rozsahu IP adres virtuálních sítí vašeho clusteru a neměla by se překrývat s ostatními rozsahy adres, které se ve vaší síti používají.

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
> Pokud chcete povolit, aby cluster AKS zahrnoval [zásady sítě Calico][calico-network-policies] , můžete použít následující příkaz.

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

Při vytváření clusteru AKS se automaticky vytvoří skupina zabezpečení sítě a směrovací tabulka. Tyto síťové prostředky se spravují pomocí roviny ovládacího prvku AKS. Skupina zabezpečení sítě je automaticky přidružená k virtuálním síťovým kartám na vašich uzlech. Tabulka směrování je automaticky přidružena k podsíti virtuální sítě. Pravidla skupiny zabezpečení sítě a směrovací tabulky se automaticky aktualizují při vytváření a vystavování služeb.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Využijte vlastní podsíť a směrovací tabulku pomocí kubenet

V kubenet musí existovat směrovací tabulka v těchto podsítích clusteru. AKS podporuje uvedení vlastní existující podsítě a směrovací tabulky.

Pokud vaše vlastní podsíť neobsahuje směrovací tabulku, vytvoří AKS jednu za vás a přidá do ní pravidla v rámci životního cyklu clusteru. Pokud vaše vlastní podsíť obsahuje směrovací tabulku při vytváření clusteru, AKS potvrdí existující tabulku směrování během operací clusteru a přidá nebo aktualizuje pravidla pro operace poskytovatele cloudu.

> [!WARNING]
> Vlastní pravidla je možné přidat do vlastní tabulky směrování a aktualizovat. Poskytovatel cloudu Kubernetes ale přidají pravidla, která se nesmí aktualizovat ani odebrat. Pravidla, jako je například 0.0.0.0/0, musí existovat vždy v dané směrovací tabulce a mapovat na cíl vaší internetové brány, jako je například síťové virtuální zařízení nebo jiná výstupní brána. Při aktualizaci pravidel, která se mění jenom na vaše vlastní pravidla, postupujte opatrně.

Přečtěte si další informace o nastavení [Vlastní směrovací tabulky][custom-route-table].

Kubenet Networking vyžaduje uspořádaná pravidla směrovací tabulky pro úspěšné směrování požadavků. Z důvodu tohoto návrhu musí být pro každý cluster, který na něm závisí, pečlivě udržovány směrovací tabulky. Více clusterů nemůže sdílet směrovací tabulku, protože pod CIDRs z různých clusterů se může překrývat, což způsobí neočekávané a poškozené směrování. Při konfiguraci více clusterů ve stejné virtuální síti nebo při vyhradování virtuální sítě pro každý cluster se ujistěte, že jsou zvážena následující omezení.

Omezení:

* Oprávnění musí být přiřazena před vytvořením clusteru, ujistěte se, že používáte instanční objekt s oprávněním k zápisu do vlastní podsítě a vlastní směrovací tabulky.
* Spravované identity se v současnosti nepodporují s vlastními směrovacími tabulkami v kubenet.
* Vlastní směrovací tabulka musí být přidružena k podsíti před vytvořením clusteru AKS.
* Po vytvoření clusteru nelze aktualizovat prostředek přidružené směrovací tabulky. I když nelze prostředek směrovací tabulky aktualizovat, lze upravit vlastní pravidla v tabulce směrování.
* Každý cluster AKS musí používat jednu jedinečnou směrovací tabulku pro všechny podsítě přidružené ke clusteru. Směrovací tabulku s několika clustery nemůžete znovu použít kvůli potenciálu překrývající se pod CIDRs a konfliktní pravidla směrování.

Po vytvoření vlastní směrovací tabulky a jejím přidružení k podsíti ve vaší virtuální síti můžete vytvořit nový cluster AKS, který používá vaši směrovací tabulku.
Musíte použít ID podsítě, kde plánujete nasadit cluster AKS. Tato podsíť musí být také přidružená k vlastní směrovací tabulce.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Další kroky

Když je cluster AKS nasazený do stávající podsítě virtuální sítě, můžete teď cluster používat jako normální. Začněte s [vytvářením aplikací pomocí Azure dev Spaces][dev-spaces], [Nasaďte existující aplikace pomocí Helm][use-helm]nebo [vytvářejte nové aplikace pomocí Helm][develop-helm].

<!-- LINKS - External -->
[dev-spaces]: ../dev-spaces/index.yml
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
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
