---
title: Konfigurace sítě Azure CNI ve službě Azure Kubernetes Service (AKS)
description: Naučte se konfigurovat sítě Azure CNI (pokročilé) ve službě Azure Kubernetes Service (AKS), včetně nasazení clusteru AKS do existující virtuální sítě a podsítě.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: afb98acf903f90ead137c9b372d33ce82b89f7b5
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062213"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurace sítě Azure CNI ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení používají clustery AKS [kubenet][kubenet]a pro vás vytvoří virtuální síť a podsíť. Pomocí *kubenet* uzly získají IP adresu z podsítě virtuální sítě. V uzlech se pak nakonfiguruje překlad síťových adres (NAT) a lusky obdrží IP adresu "skrytá" za IP adresou uzlu. Tento přístup snižuje počet IP adres, které je třeba vyhradit v síťovém prostoru pro použití v luskech.

Díky [rozhraní CNI (Azure Container Networking Interface)][cni-networking]každý z nich získá IP adresu z podsítě a dá se k nim získat přímý pøístup. Tyto IP adresy musí být v rámci vašeho síťového prostoru jedinečné a musí být plánovány předem. Každý uzel má parametr konfigurace maximálního počtu lusků, které podporuje. Pro tento uzel je pak rezervovaný i ekvivalentní počet IP adres na uzel. Tento přístup vyžaduje více plánování a často vede k vyčerpání IP adres nebo k nutnosti opětovnému sestavení clusterů ve větší podsíti, dokud vaše aplikace požaduje růst.

V tomto článku se dozvíte, jak pomocí sítě *Azure CNI* vytvořit a použít podsíť virtuální sítě pro cluster AKS. Další informace o možnostech a požadavcích sítě najdete v tématu [Koncepty sítě pro Kubernetes a AKS][aks-network-concepts].

## <a name="prerequisites"></a>Požadavky

* Virtuální síť pro cluster AKS musí umožňovat odchozí připojení k Internetu.
* Clustery AKS nemůžou `169.254.0.0/16` používat `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` Rozsah adres služby Kubernetes pod rozsahem adres nebo rozsah adres virtuální sítě clusteru. 
* Instanční objekt používaný clusterem AKS musí mít alespoň oprávnění [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) v podsíti v rámci vaší virtuální sítě. Pokud chcete místo používání předdefinované role přispěvatele sítě definovat [vlastní roli](../role-based-access-control/custom-roles.md) , vyžadují se následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Místo instančního objektu můžete pro oprávnění použít spravovanou identitu přiřazenou systémem. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).
* Podsíť přiřazená ke fondu uzlů AKS nemůže být [delegovaná podsíť](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Naplánování IP adres pro cluster

Clustery nakonfigurované pomocí sítě Azure CNI vyžadují další plánování. Velikost vaší virtuální sítě a její podsítě musí vyhovovat počtu lusků, které chcete spustit, a počtu uzlů pro cluster.

IP adresy pro lusky a uzly clusteru se přiřazují ze zadané podsítě v rámci virtuální sítě. Každý uzel je nakonfigurovaný s primární IP adresou. Ve výchozím nastavení jsou v Azure CNI předem nakonfigurované 30 dalších IP adres, které jsou přiřazené k částice naplánované na uzlu. Při horizontálním navýšení kapacity clusteru se každý uzel nakonfiguruje s využitím IP adres z podsítě. Můžete si také zobrazit [Maximum lusků na jeden uzel](#maximum-pods-per-node).

> [!IMPORTANT]
> Požadovaný počet IP adres by měl zahrnovat i požadavky na operace upgradu a škálování. Pokud nastavíte rozsah IP adres tak, aby podporoval pouze pevný počet uzlů, nelze upgradovat nebo škálovat cluster.
>
> - Při **upgradu** clusteru AKS se do clusteru nasadí nový uzel. Služby a úlohy začínají běžet na novém uzlu a z clusteru se odebere starší uzel. Tento postup upgradu vyžaduje minimálně jeden další blok IP adres, který je k dispozici. Počet uzlů je pak `n + 1` .
>   - Tato úvaha je zvláště důležitá, pokud používáte fondy uzlů Windows serveru. Uzly Windows serveru v AKS nepoužívají automatické aktualizace Windows, místo toho provedete upgrade ve fondu uzlů. Tento upgrade nasadí nové uzly pomocí nejnovější image základního uzlu Windows serveru 2019 a opravy zabezpečení. Další informace o upgradu fondu uzlů Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].
>
> - Při **škálování** clusteru AKS se do clusteru nasadí nový uzel. Služby a úlohy začínají běžet na novém uzlu. Rozsah IP adres musí vzít v úvahu, jak můžete chtít škálovat počet uzlů a lusky, které může váš cluster podporovat. Je potřeba zahrnout i jeden další uzel pro operace upgradu. Počet uzlů je pak `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Pokud očekáváte, že uzly budou spouštět maximální počet lusků, a pravidelně zničit a nasazovat lusky, měli byste také zvážit v některých dalších IP adresách na jeden uzel. Tyto další IP adresy berou v úvahu, že může trvat několik sekund, než se služba odstraní a IP adresa uvolněná pro novou službu, která se má nasadit a získat adresu.

Plán IP adres pro cluster AKS se skládá z virtuální sítě, minimálně jedné podsítě pro uzly a lusky a rozsahu adres služby Kubernetes.

| Rozsah adres/prostředek Azure | Omezení a velikost |
| --------- | ------------- |
| Virtuální síť | Virtuální síť Azure může být velká až/8, ale je omezená na 65 536 nakonfigurovaných IP adres. Před konfigurací adresního prostoru Zvažte všechny požadavky vaší sítě, včetně komunikace se službami v jiných virtuálních sítích. Pokud například nakonfigurujete příliš velkou část adresního prostoru, může dojít k problémům s překrývajícími se dalšími adresními prostory v rámci vaší sítě.|
| Podsíť | Musí být dostatečně velká, aby se vešly do uzlů, lusků a všech prostředků Kubernetes a Azure, které můžou být zřízené ve vašem clusteru. Pokud například nasadíte interní Azure Load Balancer, jeho front-endové IP adresy se přidělují z podsítě clusteru, nikoli z veřejných IP adres. Velikost podsítě by měla také vzít v úvahu operace upgradu nebo budoucí požadavky na škálování.<p />Výpočet *minimální* velikosti podsítě včetně dalšího uzlu pro operace upgradu: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Příklad pro cluster uzlu 50: `(51) + (51  * 30 (default)) = 1,581` (/21 nebo větší)<p/>Příklad pro cluster uzlu 50, který obsahuje také zřizování pro horizontální navýšení kapacity dalších 10 uzlů: `(61) + (61 * 30 (default)) = 1,891` (/21 nebo větší)<p>Pokud při vytváření clusteru neurčíte maximální počet lusků na uzel, je maximální počet lusků na uzel nastaven na hodnotu *30*. Minimální požadovaný počet IP adres je založen na této hodnotě. Pokud vypočítáte minimální požadavky na IP adresu pro jinou maximální hodnotu, přečtěte si téma [jak nakonfigurovat maximální počet lusků na uzel](#configure-maximum---new-clusters) a nastavit tuto hodnotu při nasazení clusteru. |
| Rozsah adres služby Kubernetes Service | Tento rozsah by neměl být použit žádným síťovým prvkem ani připojeným k této virtuální síti. CIDR adresy služby musí být menší než/12. Tento rozsah můžete znovu použít napříč různými AKS clustery. |
| IP adresa služby DNS Kubernetes | IP adresa v rámci rozsahu adres služby Kubernetes, který bude používán zjišťováním Clusterové služby. Nepoužívejte první IP adresu v rozsahu adres, například. 1. První adresa v rozsahu podsítě se používá pro *Kubernetes. default. svc. cluster. místní* adresa. |
| Adresa mostu Docker | Síťová adresa mostu Docker představuje výchozí síťovou adresu mostu *docker0* přítomnou ve všech instalacích Dockeru. I když se *docker0* most nepoužívá v clusterech AKS nebo v samotných luskech, musíte tuto adresu nastavit tak, aby pokračovala v podpoře scénářů, jako je například *sestavení Docker* v rámci clusteru AKS. Je nutné vybrat CIDR pro síťovou adresu mostu Docker, protože jinak Docker vybere podsíť automaticky, která by mohla být v konfliktu s jinými CIDRs. Je nutné vybrat adresní prostor, který není v konfliktu se zbytkem CIDRs ve vašich sítích, včetně směrování služeb v clusteru a pod ním. Výchozí hodnota pro 172.17.0.1/16. Tento rozsah můžete znovu použít napříč různými AKS clustery. |

## <a name="maximum-pods-per-node"></a>Maximální počet lusků na uzel

Maximální počet lusků na uzel v clusteru AKS je 250. *Výchozí* maximální počet lusků na uzel se liší mezi *Kubenet* a sítí *Azure CNI* a metodou nasazení clusteru.

| Metoda nasazení | Kubenet výchozí | Výchozí CNI Azure | Konfigurovatelné při nasazení |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ano (až 250) |
| Šablona Resource Manageru | 110 | 30 | Ano (až 250) |
| Portál | 110 | 110 (nakonfigurováno na kartě fondy uzlů) | No |

### <a name="configure-maximum---new-clusters"></a>Konfigurace maximálního počtu nových clusterů

Můžete nakonfigurovat maximální počet lusků na uzel během nasazování clusteru nebo při přidávání nových fondů uzlů. Pokud nasadíte pomocí Azure CLI nebo šablony Správce prostředků, můžete nastavit maximální počet lusků na jeden uzel na hodnotu vysoká jako 250.

Pokud při vytváření nových fondů uzlů nezadáte maxPods, zobrazí se výchozí hodnota 30 pro Azure CNI.

Minimální hodnota pro maximální počet lusků na uzel se vynutila tak, aby se zajistilo místo pro systémové lusky zásadní pro stav clusteru. Minimální hodnota, která se dá nastavit pro maximální počet lusků na uzel, je 10, pokud je a jenom v případě, že konfigurace každého fondu uzlů má prostor pro minimálně 30 lusků. Například nastavení maximálního počtu lusků na uzel na minimum na hodnotu 10 vyžaduje, aby každý fond jednotlivých uzlů měl minimálně 3 uzly. Tento požadavek platí také pro každý vytvořený fond uzlů, takže pokud je hodnota 10 definována jako maximální počet lusků na uzel, musí mít každý další fond uzlů aspoň 3 uzly.

| Sítě | Minimum | Maximum |
| -- | :--: | :--: |
| CNI Azure | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> Minimální hodnota v tabulce výše je striktně vynutila služba AKS. Nemůžete nastavit nižší hodnotu maxPods, než je minimální hodnota, která by mohla zabránit spuštění clusteru.

* **Azure CLI**: Určete `--max-pods` argument při nasazení clusteru pomocí příkazu [AZ AKS Create][az-aks-create] . Maximální hodnota je 250.
* **Správce prostředků šablona**: Určete `maxPods` vlastnost v objektu [ManagedClusterAgentPoolProfile] při nasazení clusteru se šablonou správce prostředků. Maximální hodnota je 250.
* **Azure Portal**: maximální počet lusků na uzel nejde změnit, když nasadíte cluster s Azure Portal. Při nasazení pomocí Azure Portal se síťové clustery Azure CNI na jeden uzel omezí na 30 lusků.

### <a name="configure-maximum---existing-clusters"></a>Konfigurace maximálního počtu existujících clusterů

Nastavení maxPod na uzel lze definovat při vytváření nového fondu uzlů. Pokud potřebujete zvětšit nastavení maxPod na uzel v existujícím clusteru, přidejte nový fond uzlů s novým požadovaným počtem maxPod. Po migraci lusků do nového fondu odstraňte starší fond. Chcete-li odstranit všechny starší fondy v clusteru, ujistěte se, že nastavujete režimy fondu uzlů definované v [dokumentu fondy systémových uzlů][system-node-pools].

## <a name="deployment-parameters"></a>Parametry nasazení

Při vytváření clusteru AKS se pro sítě Azure CNI dají nakonfigurovat následující parametry:

**Virtuální síť**: virtuální síť, do které chcete nasadit cluster Kubernetes. Pokud chcete pro svůj cluster vytvořit novou virtuální síť, vyberte *vytvořit novou* a postupujte podle kroků v části *vytvoření virtuální sítě* . Informace o limitech a kvótách pro virtuální síť Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsíť**: podsíť ve virtuální síti, do které chcete nasadit cluster. Pokud chcete vytvořit novou podsíť ve virtuální síti pro svůj cluster, vyberte *vytvořit novou* a postupujte podle kroků v části *Vytvoření podsítě* . V případě hybridního připojení by se rozsah adres neměl překrývat s ostatními virtuálními sítěmi ve vašem prostředí.

**Modul plug-in Azure Network plugin**: když se používá modul plug-in Azure Network, k interní službě Vyrovnávání zatížení s "ExternalTrafficPolicy = local" se nedá dostat z virtuálních počítačů s IP adresou v clusterCIDR, která nepatří do clusteru AKS.

**Rozsah adres služby Kubernetes**: Jedná se o sadu virtuálních IP adres, které Kubernetes přiřadí interním [službám][services] v clusteru. Můžete použít libovolný rozsah privátních adres, který splňuje následující požadavky:

* Nesmí spadat do rozsahu IP adres virtuálních sítí vašeho clusteru.
* Nesmí se překrývat s žádnou jinou virtuální sítí, se kterou se nacházejí partnerské uzly virtuální sítě clusteru.
* Nesmí se překrývat s žádnými místními IP adresami.
* Nesmí být v rozsahu `169.254.0.0/16` , `172.30.0.0/16` , `172.31.0.0/16` nebo `192.0.2.0/24`

I když je technicky možné zadat rozsah adres služby ve stejné virtuální síti jako cluster, nedoporučuje se to. Pokud se použijí překrývající se rozsahy IP adres, může dojít k nepředvídatelnému chování. Další informace najdete v části [Nejčastější dotazy](#frequently-asked-questions) v tomto článku. Další informace o službách Kubernetes Services najdete v tématu [služby][services] v dokumentaci k Kubernetes.

**KUBERNETES IP adresa služby DNS**: IP adresa služby DNS clusteru. Tato adresa musí být v *rozsahu adres služby Kubernetes*. Nepoužívejte první IP adresu v rozsahu adres, například. 1. První adresa v rozsahu podsítě se používá pro *Kubernetes. default. svc. cluster. místní* adresa.

**Adresa mostu Docker**: Síťová adresa mostu Docker představuje výchozí síťovou adresu *docker0* mostu přítomnou ve všech instalacích Docker. I když se *docker0* most nepoužívá v clusterech AKS nebo v samotných luskech, musíte tuto adresu nastavit tak, aby pokračovala v podpoře scénářů, jako je například *sestavení Docker* v rámci clusteru AKS. Je nutné vybrat CIDR pro síťovou adresu mostu Docker, protože jinak Docker vybere podsíť automaticky, která by mohla být v konfliktu s jinými CIDRs. Je nutné vybrat adresní prostor, který není v konfliktu se zbytkem CIDRs ve vašich sítích, včetně směrování služeb v clusteru a pod ním.

## <a name="configure-networking---cli"></a>Konfigurace sítě – rozhraní příkazového řádku

Když vytvoříte cluster AKS pomocí Azure CLI, můžete taky nakonfigurovat síť Azure CNI. Pomocí následujících příkazů vytvořte nový cluster AKS se zapnutou sítí Azure CNI.

Nejprve Získejte ID prostředku podsítě pro existující podsíť, do které se cluster AKS připojí:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Pomocí příkazu [AZ AKS Create][az-aks-create] s `--network-plugin azure` argumentem vytvořte cluster s pokročilými sítěmi. Aktualizujte `--vnet-subnet-id` hodnotu pomocí ID podsítě shromážděné v předchozím kroku:

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

Následující snímek obrazovky z Azure Portal ukazuje příklad konfigurace těchto nastavení během vytváření clusteru AKS:

![Pokročilá konfigurace sítě v Azure Portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Následující otázky a odpovědi se vztahují na konfiguraci sítě **Azure CNI** .

* *Můžu v naší podsíti clusteru nasazovat virtuální počítače?*

  Ano.

* *Jakou zdrojovou IP adresu externí systémy uvidí pro provoz pocházející z rozhraní pod Azure CNI-Enabled?*

  Systémy ve stejné virtuální síti jako cluster AKS se jako zdrojová adresa pro všechny přenosy z pod sebou zobrazí jako IP adresa pod. Systémy mimo virtuální síť clusteru AKS se jako zdrojová adresa pro jakýkoli provoz z uzlu pod zobrazí jako IP adresa uzlu. 

* *Můžu nakonfigurovat zásady sítě vázané na základě?*

  Ano, zásada sítě Kubernetes je k dispozici v AKS. Informace o tom, jak začít, najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě v AKS][network-policy].

* *Je maximální počet lusků nasazených pro uzel konfigurovatelný?*

  Ano, když nasadíte cluster pomocí Azure CLI nebo šablony Správce prostředků. Zobrazit [maximální počet lusků na uzel](#maximum-pods-per-node).

  V existujícím clusteru nemůžete změnit maximální počet lusků na uzel.

* *Návody nakonfigurovat další vlastnosti podsítě, kterou jsem vytvořil během vytváření clusteru AKS? Například koncové body služby.*

  Úplný seznam vlastností pro virtuální síť a podsítě, které vytvoříte během vytváření clusteru AKS, je možné nakonfigurovat na stránce konfigurace standardního virtuálního sítě v Azure Portal.

* *Je možné použít jinou podsíť v rámci své virtuální sítě v clusteru pro* **Rozsah adres služby Kubernetes**?

  Tato konfigurace se nedoporučuje, ale tato konfigurace je možná. Rozsah adres služby je sada virtuálních IP adres (VIP), které Kubernetes přiřadí interním službám v clusteru. Síť Azure nemá žádný přehled o rozsahu IP adres služby clusteru Kubernetes. Z důvodu nedostatku viditelnosti rozsahu adres služby clusteru je možné později vytvořit novou podsíť ve virtuální síti s clustery, která se překrývá s rozsahem adres služby. Pokud dojde k překrytí, může Kubernetes přiřadit službu IP, která je už používána jiným prostředkem v podsíti, což způsobí nepředvídatelné chování nebo selhání. Tím, že zajistíte používání rozsahu adres mimo virtuální síť clusteru, se můžete vyhnout tomuto překrytému riziku.

## <a name="next-steps"></a>Další kroky

Další informace o sítích v AKS najdete v těchto článcích:

- [Použití statické IP adresy se službou Azure Kubernetes Service (AKS) pro vyrovnávání zatížení](static-ip.md)
- [Použití interního nástroje pro vyrovnávání zatížení s Azure Container Service (AKS)](internal-lb.md)

- [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- [Vytvoření kontroleru příchozího přenosu dat s dynamickou veřejnou IP adresou a konfigurací šifry pro automatické generování certifikátů TLS][aks-ingress-tls]
- [Vytvoření kontroleru příchozího přenosu dat se statickou veřejnou IP adresou a nastavení šifrování, které umožňuje automatické generování certifikátů TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Modul AKS

Modul [AKS (Azure Kubernetes Service Engine)][aks-engine] je open source projekt, který generuje Azure Resource Manager šablony, které můžete použít k nasazení clusterů Kubernetes v Azure.

Clustery Kubernetes vytvořené pomocí AKS Engine podporují moduly plug-in [kubenet][kubenet] a [Azure CNI][cni-networking] . V takovém případě jsou oba scénáře sítě podporované modulem AKS.

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
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
