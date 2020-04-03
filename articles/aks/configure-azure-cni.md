---
title: Konfigurace sítí Azure CNI ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nakonfigurovat azure cni (pokročilé) sítě ve službě Azure Kubernetes Service (AKS), včetně nasazení clusteru AKS do existující virtuální sítě a podsítě.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 6f194cb97850fcb24e4789ac0ba39b6f03d99e6e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617387"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurace sítí Azure CNI ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení clustery AKS používají [kubenet][kubenet]a virtuální síť a podsíť jsou vytvořeny za vás. S *kubenet*, uzly získat IP adresu z podsítě virtuální sítě. Překlad síťových adres (NAT) je pak nakonfigurován na uzlech a pody obdrží IP adresu "skrytou" za IP uzlem. Tento přístup snižuje počet adres IP, které je třeba rezervovat v síťovém prostoru pro pody k použití.

S [Azure Container Networking Interface (CNI)][cni-networking]získá každý pod IP adresu z podsítě a je přístupný přímo. Tyto adresy IP musí být jedinečné v celém síťovém prostoru a musí být naplánovány předem. Každý uzel má konfigurační parametr pro maximální počet podů, které podporuje. Ekvivalentní počet ADRES IP na uzel je pak rezervován předem pro tento uzel. Tento přístup vyžaduje více plánování a často vede k vyčerpání IP adresy nebo potřeba znovu sestavit clustery ve větší podsíti, jak vaše požadavky na aplikace rostou.

Tento článek ukazuje, jak pomocí sítě *Azure CNI* vytvořit a použít podsíť virtuální sítě pro cluster AKS. Další informace o možnostech sítě a jejich důležité informace naleznete v [tématu Síťové koncepty pro Kubernetes a AKS][aks-network-concepts].

## <a name="prerequisites"></a>Požadavky

* Virtuální síť pro cluster AKS musí umožňovat odchozí připojení k Internetu.
* Clustery AKS nesmí `169.254.0.0/16` `172.30.0.0/16`používat `172.31.0.0/16`, `192.0.2.0/24` , , nebo pro rozsah adres služby Kubernetes.
* Instanční objekt používaný clusterem AKS musí mít alespoň oprávnění [síťového přispěvatele](../role-based-access-control/built-in-roles.md#network-contributor) v podsíti ve vaší virtuální síti. Pokud chcete definovat [vlastní roli](../role-based-access-control/custom-roles.md) namísto použití předdefinované role přispěvatele sítě, jsou vyžadována následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Namísto instančního objektu můžete pro oprávnění použít systém přiřazenou spravovanou identitu. Další informace naleznete v tématu [Použití spravovaných identit](use-managed-identity.md).
* Podsíť přiřazená k fondu uzlů AKS nemůže být [delegovanou podsítí](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Plánování adresování IP adres pro váš cluster

Clustery nakonfigurované v síti Azure CNI vyžadují další plánování. Velikost virtuální sítě a její podsítě musí obsahovat počet podů, které chcete spustit, a počet uzlů pro cluster.

Ip adresy pro pody a uzly clusteru jsou přiřazeny ze zadané podsítě v rámci virtuální sítě. Každý uzel je nakonfigurován s primární IP adresou. Ve výchozím nastavení jsou 30 dalšíIP adresy předem nakonfigurované Azure CNI, které jsou přiřazeny pody naplánované na uzlu. Při horizontálním navýšení kapacity clusteru je každý uzel podobně nakonfigurován s adresami IP z podsítě. Můžete také zobrazit [maximální pody na uzel](#maximum-pods-per-node).

> [!IMPORTANT]
> Počet požadovaných adres IP by měl zahrnovat důležité informace pro operace upgradu a škálování. Pokud nastavíte rozsah IP adres tak, aby podporoval pouze pevný počet uzlů, nelze cluster upgradovat ani škálovat.
>
> - Při **upgradu** clusteru AKS se do clusteru nasadí nový uzel. Služby a úlohy začnou běžet na novém uzlu a starší uzel je odebrán z clusteru. Tento postupný proces upgradu vyžaduje k dispozici minimálně jeden další blok adres IP. Počet uzlů je `n + 1`potom .
>   - Tato úvaha je obzvláště důležité při použití fondů uzlů systému Windows Server (aktuálně ve verzi Preview v AKS). Uzly systému Windows Server v systému AKS nepoužívají automaticky aktualizace systému Windows, místo toho provádíte upgrade ve fondu uzlů. Tento upgrade nasazuje nové uzly s nejnovějšími bitovými kopiemi a opravami zabezpečení základního uzlu Window Server 2019. Další informace o inovaci fondu uzlů systému Windows Server naleznete [v tématu Upgrade fondu uzlů v AKS][nodepool-upgrade].
>
> - Při **škálování** clusteru AKS se do clusteru nasadí nový uzel. Služby a úlohy začnou běžet na novém uzlu. Rozsah IP adres musí vzít v úvahu, jak můžete chtít vertikálně navýšit počet uzlů a podů, které může váš cluster podporovat. Jeden další uzel pro operace upgradu by měly být také zahrnuty. Počet uzlů je `n + number-of-additional-scaled-nodes-you-anticipate + 1`potom .

Pokud očekáváte, že vaše uzly spustit maximální počet podů a pravidelně zničit a nasazovat pody, měli byste také faktor v některých dalších IP adres na uzel. Tyto další IP adresy vzít v úvahu může trvat několik sekund pro službu, která má být odstraněna a IP adresu uvolněna pro novou službu, která má být nasazena a získat adresu.

Plán IP adres pro cluster AKS se skládá z virtuální sítě, alespoň jedné podsítě pro uzly a pody a rozsahu adres služby Kubernetes.

| Rozsah adres / prostředek Azure | Limity a velikosti |
| --------- | ------------- |
| Virtuální síť | Virtuální síť Azure může být stejně velká jako /8, ale je omezena na 65 536 nakonfigurovaných IP adres. |
| Podsíť | Musí být dostatečně velký, aby vyhovovaly uzly, pody a všechny Kubernetes a prostředky Azure, které mohou být zřízeny ve vašem clusteru. Pokud například nasadíte interní Azure Load Balancer, jeho front-end OVÉ IP adresy jsou přiděleny z podsítě clusteru, nikoli veřejné IP adresy. Velikost podsítě by měla také brát v úvahu operace upgradu nebo budoucí potřeby škálování.<p />Výpočet *minimální* velikosti podsítě včetně dalšího uzlu pro operace upgradu:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Příklad pro cluster s 50 uzly: `(51) + (51  * 30 (default)) = 1,581` (/21 nebo větší)<p/>Příklad clusteru 50 uzlů, který také zahrnuje zřízení pro navýšení `(61) + (61 * 30 (default)) = 1,891` navýšení kapacity dalších 10 uzlů: (/21 nebo větší)<p>Pokud při vytváření clusteru nezadáte maximální počet podů na uzel, maximální počet podů na uzel je nastaven na *30*. Minimální počet požadovaných IP adres je založen na této hodnotě. Pokud vypočítáte minimální požadavky na IP adresu na jinou maximální hodnotu, podívejte [se, jak nakonfigurovat maximální počet podů na uzel](#configure-maximum---new-clusters) nastavit tuto hodnotu při nasazení clusteru. |
| Rozsah adres služby Kubernetes Service | Tento rozsah by neměl být používán žádným síťovým prvkem v této virtuální síti ani připojeným k této virtuální síti. Adresa služby CIDR musí být menší než /12. Tento rozsah můžete znovu použít v různých clusterech AKS. |
| IP adresa služby DNS Kubernetes | IP adresa v rozsahu adres služby Kubernetes, která bude použita při zjišťování clusterové služby (kube-dns). Nepoužívejte první IP adresu v rozsahu adres, například .1. První adresa v rozsahu podsítě se používá pro *adresu kubernetes.default.svc.cluster.local.* |
| Adresa mostu Docker | Síťová adresa mostu Docker představuje výchozí síťovou adresu mostu *docker0* přítomnou ve všech instalacích Dockeru. Zatímco *docker0* most není používán clustery AKS nebo pody sami, musíte nastavit tuto adresu nadále podporovat scénáře, jako je *například docker sestavení* v clusteru AKS. Je nutné vybrat CIDR pro síťovou adresu mostu Dockeru, protože jinak Docker automaticky vybere podsíť, která by mohla být v konfliktu s jinými cidr. Je nutné vybrat adresní prostor, který není kolidován se zbytkem CIDR v sítích, včetně služby CLUSTERU CIDR a pod CIDR. Výchozí hodnota 172.17.0.1/16. Tento rozsah můžete znovu použít v různých clusterech AKS. |

## <a name="maximum-pods-per-node"></a>Maximální počet lusků na uzel

Maximální počet podů na uzel v clusteru AKS je 250. *Výchozí* maximální počet podů na uzel se liší mezi *sítí kubenet* a *Azure CNI* a metodou nasazení clusteru.

| Metoda nasazení | Kubenet výchozí | Výchozí hodnota Azure CNI | Konfigurovatelné při nasazení |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ano (až 250) |
| Šablona Resource Manageru | 110 | 30 | Ano (až 250) |
| Portál | 110 | 30 | Ne |

### <a name="configure-maximum---new-clusters"></a>Konfigurace maximální - nové clustery

Maximální počet podů na uzel můžete nakonfigurovat *pouze v době nasazení clusteru*. Pokud nasadíte pomocí azure CLI nebo se šablonou Správce prostředků, můžete nastavit maximální pody na hodnotu uzlu až 250.

Minimální hodnota pro maximální pody na uzel je vynuceno zaručit místo pro pody systému důležité pro stav clusteru. Minimální hodnota, která může být nastavena pro maximální pody na uzel je 10 pouze v případě, že konfigurace každého fondu uzlů má místo pro minimálně 30 pods. Například nastavení maximální pody na uzel na minimum 10 vyžaduje, aby každý fond jednotlivých uzlů mít minimálně 3 uzly. Tento požadavek platí pro každý nový fond uzlů vytvořené také, takže pokud 10 je definovánjako maximální pods na uzel každý následující uzel fond přidán musí mít alespoň 3 uzly.

| Sítě | Minimální | Maximum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet (Kubenet) | 10 | 110 |

> [!NOTE]
> Minimální hodnota ve výše uvedené tabulce je přísně vynucena službou AKS. Nelze nastavit hodnotu maxPods nižší, než je minimální zobrazeno, protože to může zabránit spuštění clusteru.

* **Azure CLI**: `--max-pods` Zadejte argument při nasazení clusteru s příkazem [az aks create.][az-aks-create] Maximální hodnota je 250.
* **Šablona Správce**prostředků `maxPods` : Při nasazení clusteru pomocí šablony Správce prostředků zadejte vlastnost v objektu [ManagedClusterAgentPoolProfile.] Maximální hodnota je 250.
* **Azure Portal**: Maximální počet podů na uzel nemůžete změnit při nasazení clusteru s portálem Azure. Síťové clustery Azure CNI jsou omezené na 30 podů na uzel při nasazení pomocí portálu Azure.

### <a name="configure-maximum---existing-clusters"></a>Konfigurace maximálního počtu existujících clusterů

Maximální počet podů na uzel v existujícím clusteru AKS nelze změnit. Číslo můžete upravit pouze v případě, že cluster zpočátku nasazujete.

## <a name="deployment-parameters"></a>Parametry nasazení

Při vytváření clusteru AKS jsou následující parametry konfigurovatelné pro sítě Azure CNI:

**Virtuální síť**: Virtuální síť, do které chcete nasadit cluster Kubernetes. Pokud chcete vytvořit novou virtuální síť pro váš cluster, vyberte *Vytvořit nový* a postupujte podle pokynů v části Vytvořit *virtuální síť.* Informace o omezeních a kvótách pro virtuální síť Azure najdete v tématu [Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsíť**: Podsíť ve virtuální síti, do které chcete cluster nasadit. Pokud chcete vytvořit novou podsíť ve virtuální síti pro váš cluster, vyberte *Vytvořit nový* a postupujte podle kroků v části *Vytvořit podsíť.* U hybridního připojení by se rozsah adres neměl překrývat s jinými virtuálními sítěmi ve vašem prostředí.

**Rozsah adres služby Kubernetes**: Toto je sada virtuálních IP adres, které Kubernetes přiřazuje interním [službám][services] ve vašem clusteru. Můžete použít libovolný rozsah privátní adresy, který splňuje následující požadavky:

* Nesmí být v rozsahu IP adres virtuální sítě v clusteru.
* Nesmí se překrývat s jinými virtuálními sítěmi, se kterými se partnery virtuální sítě clusteru
* Nesmí se překrývat s místními IP adresy.
* Nesmí být v rozsahu `169.254.0.0/16` `172.30.0.0/16`, `172.31.0.0/16`, , nebo`192.0.2.0/24`

I když je technicky možné zadat rozsah adres služby ve stejné virtuální síti jako váš cluster, není to doporučeno. Nepředvídatelné chování může vést, pokud jsou použity překrývající se rozsahy IP adres. Další informace naleznete v části [Nejčastější dotazy](#frequently-asked-questions) v tomto článku. Další informace o službách Kubernetes naleznete v dokumentaci kubernetes. [Services][services]

**IP adresa služby Kubernetes DNS**: Adresa IP služby DNS clusteru. Tato adresa musí být v *rozsahu adres služby Kubernetes*. Nepoužívejte první IP adresu v rozsahu adres, například .1. První adresa v rozsahu podsítě se používá pro *adresu kubernetes.default.svc.cluster.local.*

**Adresa mostu Dockeru**: Síťová adresa mostu Dockerpředstavuje výchozí síťovou adresu *mostu docker0,* která se vyskytuje ve všech instalacích Dockeru. Zatímco *docker0* most není používán clustery AKS nebo pody sami, musíte nastavit tuto adresu nadále podporovat scénáře, jako je *například docker sestavení* v clusteru AKS. Je nutné vybrat CIDR pro síťovou adresu mostu Dockeru, protože jinak Docker automaticky vybere podsíť, která by mohla být v konfliktu s jinými cidr. Je nutné vybrat adresní prostor, který není kolidován se zbytkem CIDR v sítích, včetně služby CLUSTERU CIDR a pod CIDR.

## <a name="configure-networking---cli"></a>Konfigurace sítě – rozhraní příkazového příkazu

Když vytvoříte cluster AKS pomocí rozhraní příkazového příkazu k řešení Azure, můžete také nakonfigurovat sítě Azure CNI. Pomocí následujících příkazů vytvořte nový cluster AKS s povolenou sítí Azure CNI.

Nejprve získejte ID prostředku podsítě pro existující podsíť, do které bude připojen cluster AKS:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Pomocí příkazu [az aks create][az-aks-create] s argumentem `--network-plugin azure` vytvořte cluster s pokročilou sítí. Aktualizujte `--vnet-subnet-id` hodnotu ID podsítě shromážděnou v předchozím kroku:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Konfigurace sítě – portál

Následující snímek obrazovky z portálu Azure ukazuje příklad konfigurace těchto nastavení během vytváření clusteru AKS:

![Pokročilá konfigurace sítě na webu Azure Portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Následující otázky a odpovědi platí pro konfiguraci sítě **Azure CNI.**

* *Můžu nasadit virtuální ms v podsíti clusteru?*

  Ne. Nasazení virtuálních počítačů v podsíti používané clusterem Kubernetes není podporováno. Virtuální počítače mohou být nasazené ve stejné virtuální síti, ale v jiné podsíti.

* *Lze nakonfigurovat zásady sítě pro každý pod?*

  Ano, zásady sítě Kubernetes jsou k dispozici v AKS. Chcete-li začít, přečtěte si informace [o zabezpečení provozu mezi pody pomocí zásad sítě v AKS][network-policy].

* *Je maximální počet podů nasaditelný do uzlu konfigurovatelný?*

  Ano, když nasadíte cluster pomocí azure CLI nebo šablony Správce prostředků. Viz [Maximální pody na uzel](#maximum-pods-per-node).

  Maximální počet podů na uzel v existujícím clusteru nelze změnit.

* *Jak nakonfiguruji další vlastnosti pro podsíť vytvořenou během vytváření clusteru AKS? Například koncové body služby.*

  Úplný seznam vlastností pro virtuální síť a podsítě, které vytvoříte během vytváření clusteru AKS, lze nakonfigurovat na stránce konfigurace standardní virtuální sítě na webu Azure Portal.

* *Je možné použít jinou podsíť ve virtuální síti clusteru pro* **rozsah adres služby Kubernetes**?

  Nedoporučuje se, ale tato konfigurace je možná. Rozsah adres služby je sada virtuálních IP adres (IP), které Kubernetes přiřadí interním službám ve vašem clusteru. Azure Networking nemá žádný přehled o rozsahu IP adres služby clusteru Kubernetes. Vzhledem k nedostatečnému přehledu o rozsahu adres služeb clusteru je možné později vytvořit novou podsíť ve virtuální síti clusteru, která se překrývá s rozsahem adres služby. Pokud k takovému překrytí dojde, kubernetes může přiřadit službě IP adresu, která je již používána jiným prostředkem v podsíti, což způsobuje nepředvídatelné chování nebo selhání. Tím, že zajistíte použití rozsahu adres mimo virtuální síť clusteru, můžete se tomuto riziku překrývání vyhnout.

## <a name="next-steps"></a>Další kroky

Další informace o vytváření sítí v AKS najdete v následujících článcích:

- [Použití statické IP adresy s balancerem služby Azure Kubernetes Service (AKS)](static-ip.md)
- [Použití interního zařízení pro vyrovnávání zatížení se službou Azure Container Service (AKS)](internal-lb.md)

- [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat s dynamickou veřejnou IP adresou a konfigurace funkce Let's Encrypt tak, aby automaticky generovala certifikáty TLS][aks-ingress-tls]
- [Vytvoření řadiče příchozího přenosu dat se statickou veřejnou IP adresou a konfigurace funkce Let's Encrypt pro automatické generování certifikátů TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS motor

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] je open source projekt, který generuje šablony Azure Resource Manageru, které můžete použít k nasazení clusterů Kubernetes v Azure.

Clustery Kubernetes vytvořené pomocí Modulu AKS podporují moduly [kubenet][kubenet] i [Azure CNI.][cni-networking] Jako takové jsou oba síťové scénáře podporovány AKS Engine.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[Profil managedclusteragentpoolu]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
