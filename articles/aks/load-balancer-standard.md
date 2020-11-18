---
title: Použít veřejné Load Balancer
titleSuffix: Azure Kubernetes Service
description: Naučte se používat veřejný Nástroj pro vyrovnávání zatížení se standardní SKU k vystavování služeb pomocí Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: b42a952b096f533f916879a11fdb6b6583fa8592
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660351"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Použití veřejné Standard Load Balancer ve službě Azure Kubernetes (AKS)

Azure Load Balancer se nachází v L4 modelu OSI (Open Systems proconnection), který podporuje scénáře příchozího i odchozího připojení. Distribuuje příchozí toky, které dorazí na front-end nástroje pro vyrovnávání zatížení do instancí fondu back-end.

**Veřejné** Load Balancer při integraci s AKS slouží ke dvěma účelům:

1. Pro poskytování odchozích připojení k uzlům clusteru uvnitř virtuální sítě AKS. Dosahuje tohoto cíle překladem soukromé IP adresy uzlů na veřejnou IP adresu, která je součástí svého *odchozího fondu*.
2. K poskytnutí přístupu k aplikacím prostřednictvím služby Kubernetes Services typu `LoadBalancer` . Díky tomu můžete snadno škálovat aplikace a vytvářet vysoce dostupné služby.

**Interní (nebo soukromý)** Nástroj pro vyrovnávání zatížení se používá v případě, že jsou jako front-endu povoleny pouze privátní IP adresy. Interní nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení provozu v rámci virtuální sítě. Front-end Nástroj pro vyrovnávání zatížení je možné použít také z místní sítě v hybridním scénáři.

Tento dokument popisuje integraci s veřejným nástrojem pro vyrovnávání zatížení. Informace o interní integraci Load Balancer najdete v [dokumentaci k internímu nástroji pro vyrovnávání zatížení AKS](internal-lb.md).

## <a name="before-you-begin"></a>Než začnete

Azure Load Balancer je k dispozici ve dvou SKU – *Basic* a *Standard*. Ve výchozím nastavení se používá *standardní* SKU při vytváření clusteru AKS. Použijte *standardní* SKU, abyste měli přístup k přidaným funkcím, jako je větší back-end fond, [**více fondů uzlů**](use-multiple-node-pools.md)a [**zóny dostupnosti**](availability-zones.md). Pro AKS se doporučuje Load Balancer SKU.

Další informace o jednotkách *Basic* a *Standard* najdete v tématu [porovnání skladové položky služby Vyrovnávání zatížení Azure][azure-lb-comparison].

V tomto článku se předpokládá, že máte cluster AKS se *standardní* SKU Azure Load Balancer a projdete ho, jak používat a nakonfigurovat některé funkce a funkce nástroje pro vyrovnávání zatížení. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Pokud nechcete využívat Azure Load Balancer k poskytování odchozího připojení a místo toho máte pro tento účel vlastní bránu, bránu firewall nebo proxy server, můžete přeskočit vytvoření odchozího fondu nástroje pro vyrovnávání zatížení a odpovídající front-end IP adresy pomocí [**typu odchozího připojení jako UserDefinedRouting (udr)**](egress-outboundtype.md). Typ odchozího přenosu definuje metodu odchozího přenosu pro cluster a ve výchozím nastavení typ: Nástroj pro vyrovnávání zatížení.

## <a name="use-the-public-standard-load-balancer"></a>Použití veřejného nástroje pro vyrovnávání zatížení Standard

Po vytvoření clusteru AKS s odchozím typem: Load Balancer (výchozí) je cluster připravený k vystavování služeb taky pomocí nástroje pro vyrovnávání zatížení.

Pro tuto možnost můžete vytvořit veřejnou službu typu, `LoadBalancer` jak je znázorněno v následujícím příkladu. Začněte vytvořením manifestu služby s názvem `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Nasaďte manifest veřejné služby pomocí [kubectl použít][kubectl-apply] a zadejte název vašeho manifestu YAML:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure Load Balancer se nakonfigurují s novou veřejnou IP adresou, která bude frontou této nové služby. Vzhledem k tomu, že Azure Load Balancer může mít několik front-endové IP adresy, každá nově nasazená služba získá novou vyhrazenou IP adresu, která bude mít jedinečný přístup.

Můžete ověřit, že je vaše služba vytvořená a že je nástroj pro vyrovnávání zatížení nakonfigurovaný tak, že spustíte například:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Když si zobrazíte podrobnosti služby, zobrazí se veřejná IP adresa vytvořená pro tuto službu v nástroji pro vyrovnávání zatížení ve sloupci *External-IP* . Změna IP adresy z na skutečnou veřejnou IP adresu může trvat minutu nebo dvě *\<pending\>* , jak je znázorněno v předchozím příkladu.

## <a name="configure-the-public-standard-load-balancer"></a>Konfigurace veřejného nástroje pro vyrovnávání zatížení Standard

Při použití veřejného nástroje pro vyrovnávání zatížení Standard SKU je k dispozici sada možností, které lze přizpůsobit během vytváření nebo aktualizací clusteru. Tyto možnosti umožňují přizpůsobit Load Balancer pro splnění potřeb vašich úloh a měla by se odpovídajícím způsobem zkontrolovat. Pomocí nástroje Load Balancer úrovně Standard můžete:

* Nastavení nebo škálování počtu spravovaných odchozích IP adres
* Přineste si vlastní [odchozí IP adresy nebo předponu odchozích IP adres](#provide-your-own-outbound-public-ips-or-prefixes)
* Upravte počet přidělených odchozích portů na každý uzel clusteru.
* Konfigurace nastavení časového limitu pro nečinné připojení

> [!IMPORTANT]
> V daném okamžiku může být použita pouze jedna možnost odchozí IP adresa (spravované IP adresy, uvést vlastní IP adresu nebo předponu IP).

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Škálování počtu spravovaných odchozích veřejných IP adres

Azure Load Balancer poskytuje kromě příchozího i odchozí připojení z virtuální sítě. Odchozí pravidla usnadňují konfiguraci odchozího překladu síťových adres pro veřejné Standard Load Balancer.

Stejně jako všechna pravidla Load Balancer se odchozí pravidla řídí stejnou známou syntaxí jako vyrovnávání zatížení a příchozí pravidla překladu adres (NAT):

***front-endové IP adresy + parametry + fond back-endu** _

Odchozí pravidlo konfiguruje odchozí překlad adres (NAT) pro všechny virtuální počítače identifikované fondem back-end pro překlad do front-endu. Parametry a poskytují další jemně odstupňovanou kontrolu nad odchozím algoritmem NAT.

I když se odchozí pravidlo dá použít jenom s jednou veřejnou IP adresou, odchozí pravidla zjednodušují zátěž v konfiguraci pro škálování odchozího překladu adres (NAT). Pro plánování rozsáhlých scénářů můžete použít více IP adres a pomocí odchozích pravidel můžete zmírnit vzory náchylné k vyčerpání SNAT. Každá další IP adresa poskytovaná front-endu nabízí 64 dočasných portů, které Load Balancer použít jako porty SNAT. 

Když použijete nástroj pro vyrovnávání zatížení _Standard * SKU se spravovanými odchozími veřejnými IP adresami, které se ve výchozím nastavení vytvoří, můžete pomocí parametru škálovat počet spravovaných odchozích veřejných IP adres **`load-balancer-managed-ip-count`** .

Chcete-li aktualizovat existující cluster, spusťte následující příkaz. Tento parametr je také možné nastavit v době vytvoření clusteru a mít několik spravovaných odchozích veřejných IP adres.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Výše uvedený příklad nastaví počet spravovaných odchozích veřejných IP adres na *2* pro cluster *myAKSCluster* v *myResourceGroup*. 

Tento parametr můžete použít také **`load-balancer-managed-ip-count`** k nastavení počátečního počtu spravovaných odchozích veřejných IP adres při vytváření clusteru připojením **`--load-balancer-managed-outbound-ip-count`** parametru a jeho nastavením na požadovanou hodnotu. Výchozí počet spravovaných odchozích veřejných IP adres je 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Zadejte vlastní odchozí veřejné IP adresy nebo předpony

Když použijete nástroj pro vyrovnávání zatížení *Standard* SKU, ve výchozím nastavení cluster AKS automaticky vytvoří veřejnou IP adresu ve skupině prostředků infrastruktury spravované AKS a přiřadí ji do odchozího fondu nástroje pro vyrovnávání zatížení.

Veřejná IP adresa vytvořená pomocí AKS se považuje za spravovaný prostředek AKS. To znamená, že životní cyklus této veřejné IP adresy je určený ke správě pomocí AKS a nevyžaduje žádnou akci uživatele přímo na prostředku veřejné IP adresy. Alternativně můžete v době vytváření clusteru přiřadit vlastní předponu veřejné IP adresy nebo veřejné IP adresy. Vlastní IP adresy se taky dají aktualizovat ve vlastnostech nástroje pro vyrovnávání zatížení existujícího clusteru.

Požadavky na používání vlastní veřejné IP adresy nebo předpony:

- Vlastní veřejné IP adresy musí vytvořit a vlastnit uživatel. Spravované veřejné IP adresy vytvořené pomocí AKS se nedají znovu použít jako Přineste si vlastní IP adresu, protože může dojít ke konfliktům při správě.
- Musíte zajistit, aby identita clusteru AKS (instanční objekt nebo spravovaná identita) měla oprávnění pro přístup k odchozí IP adrese. Podle [seznamu požadovaných oprávnění veřejných IP adres](kubernetes-service-principal.md#networking).
- Ujistěte se, že splňujete [požadavky a omezení](../virtual-network/public-ip-address-prefix.md#constraints) nutná ke konfiguraci odchozích IP adres nebo předpon odchozích IP adres.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Aktualizujte cluster s vlastní odchozí veřejnou IP adresou.

Pomocí příkazu [AZ Network Public-IP show zobrazíte][az-network-public-ip-show] seznam ID veřejných IP adres.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID veřejné IP adresy *myPublicIP* ve skupině prostředků *myResourceGroup* .

Pomocí `az aks update` příkazu s **`load-balancer-outbound-ips`** parametrem aktualizujte svůj cluster pomocí veřejných IP adres.

V následujícím příkladu je použit `load-balancer-outbound-ips` parametr s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Aktualizujte cluster s vlastní odchozí předponou veřejné IP adresy.

Můžete také použít předpony veřejných IP adres pro výstup pomocí nástroje pro vyrovnávání zatížení *Standard* SKU. V následujícím příkladu se pomocí příkazu [AZ Network Public-IP prefix show zobrazí][az-network-public-ip-prefix-show] seznam ID předpon veřejných IP adres:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID předpony veřejné IP adresy *myPublicIPPrefix* ve skupině prostředků *myResourceGroup* .

V následujícím příkladu se používá parametr *Vyrovnávání zatížení-odchozí-IP-předpony* s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Vytvoření clusteru s vlastní veřejnou IP adresou nebo předponami

Můžete chtít použít vlastní IP adresy nebo předpony IP pro výstup v době vytváření clusteru pro podporu scénářů, jako je přidání koncových bodů odchozího přenosu do seznamu povolených. Přidejte stejné parametry uvedené výše do vašeho kroku vytvoření clusteru a definujte vlastní veřejné IP adresy a předpony IP adres na začátku životního cyklu clusteru.

Pomocí příkazu *AZ AKS Create* s parametrem *Load Balancer-Outbound-IPS* vytvořte nový cluster s vašimi veřejnými IP adresami na začátku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Pomocí příkazu *AZ AKS Create* s parametrem *Load Balance-Outbound-IP-prefixs* vytvořte nový cluster s předponami veřejné IP adresy na začátku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Konfigurace přidělených odchozích portů

> [!IMPORTANT]
> Pokud máte v clusteru aplikace, u kterých se očekává, že naváží velký počet připojení k malé sadě míst určení, např. mnoho instancí front-endu, které se připojují k databázi SQL DB, máte scénář, který je velmi náchylný k vyčerpání portů SNAT (výstup z portů pro připojení z). U těchto scénářů se důrazně doporučuje zvýšit přidělené odchozí porty a odchozí IP adresy front-endu v nástroji pro vyrovnávání zatížení. Zvýšení by mělo vzít v úvahu, že jedna (1) další IP adresa přidá 64 KB dalších portů pro distribuci mezi všechny uzly clusteru.


Pokud není uvedeno jinak, AKS použije výchozí hodnotu přidělených odchozích portů, které Standard Load Balancer definují při jejich konfiguraci. Tato hodnota je **null** v rozhraní AKS API nebo **0** rozhraní SLB API, jak ukazuje následující příkaz:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Předchozí příkazy zobrazí seznam odchozích pravidel pro nástroj pro vyrovnávání zatížení, například:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Tento výstup neznamená, že máte 0 portů, ale místo toho máte k dispozici [Automatické přiřazení odchozího portu založeného na velikosti fondu back-endu][azure-lb-outbound-preallocatedports], takže pokud má cluster minimálně 50 nebo méně uzlů, přidělí se porty 1024 portů pro každý uzel. při zvýšení počtu uzlů se vám postupně dostanou méně portů na jeden uzel.


Pokud chcete definovat nebo zvýšit počet přidělených odchozích portů, můžete postupovat podle následujícího příkladu:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

V tomto příkladu by vám 4000 přidělené odchozí porty pro každý uzel v clusteru a s 7 IP adresami, které byste měli mít *4000 portů na uzel * 100 uzly = 400 tisíc celkový počet portů < = 448k celkový počet portů = 7 IP × 64 KB portů na IP adresu*. To vám umožní bezpečně škálovat na uzly 100 a mít výchozí operaci upgradu. Je důležité přidělit dostatečné porty pro další uzly, které jsou potřeba pro upgrade a jiné operace. AKS ve výchozím nastavení jeden uzel vyrovnávací paměti pro upgrade, v tomto příkladu vyžaduje 4000 bezplatných portů v libovolném časovém okamžiku. Při použití [hodnot maxSurge](upgrade-cluster.md#customize-node-surge-upgrade-preview)vynásobte Odchozí porty na uzel hodnotou maxSurge.

Aby bylo možné bezpečně přejít nad 100 uzlů, je nutné přidat další IP adresy.


> [!IMPORTANT]
> *Abyste se* vyhnuli problémům s připojením nebo škálováním, musíte nejprve [Vypočítat požadovanou kvótu a ověřit požadavky][requirements] .

Parametry můžete použít také **`load-balancer-outbound-ports`** při vytváření clusteru, ale musíte také zadat buď **`load-balancer-managed-outbound-ip-count`** , **`load-balancer-outbound-ips`** nebo **`load-balancer-outbound-ip-prefixes`** i.  Například:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Konfigurace časového limitu nečinnosti nástroje pro vyrovnávání zatížení

Po vyčerpání prostředků portů SNAT dojde k selhání odchozích toků, dokud nebudou stávající toky vydány porty SNAT. Load Balancer redeklaracích portů SNAT po ukončení toku a nástroj pro vyrovnávání zatížení s nakonfigurovaným AKS používá při uvolnění portů SNAT z nečinných toků nečinný časový limit.
Můžete také použít přenos (například **`TCP keepalives`** ) nebo **`application-layer keepalives`** k aktualizaci toku nečinnosti a v případě potřeby resetovat tento časový limit nečinnosti. Tento časový limit můžete nakonfigurovat následujícím příkladem: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Pokud očekáváte, že budete mít krátká krátkodobá připojení, a žádné připojení, které jsou dlouhou dobu nečinné, například využití `kubectl proxy` nebo `kubectl port-forward` zvažte použití nízké hodnoty časového limitu, například 4 minuty. Při použití kontroly udržení protokolu TCP je také stačí povolit je na jedné straně připojení. Například stačí, abyste je povolili na straně serveru pouze k resetování časovače nečinnosti toku a není nutné, aby obě strany spouštěly kontroly stavu protokolu TCP. Pro aplikační vrstvu, včetně konfigurací klient-server databáze, existují podobné koncepty. Na straně serveru můžete zjistit, jaké možnosti se v případě kontroly a nastavení pro konkrétní aplikaci existují.

> [!IMPORTANT]
> AKS ve výchozím nastavení povoluje resetování protokolu TCP při nečinnosti a doporučuje, abyste tuto konfiguraci zachovali a využili je pro přesnější chování aplikace ve vašich scénářích.
> Protokol TCP RST se posílá pouze během připojení TCP v navázaném stavu. Další informace najdete [tady](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Požadavky na přizpůsobení přidělených odchozích portů a časového limitu nečinnosti

- Hodnota, kterou zadáte pro *allocatedOutboundPorts* , musí být také násobkem 8.
- Na základě počtu virtuálních počítačů uzlů a požadovaných odchozích portů musíte mít dostatek odchozích IP adres. Pokud chcete ověřit, že máte dostatek odchozích IP adres, použijte tento vzorec: 
 
*outboundIPs* \* 64 000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Pokud máte například 3 *nodeVMs* a 50 000 *desiredAllocatedOutboundPorts*, musíte mít aspoň 3 *outboundIPs*. Doporučuje se, abyste zahrnuli Další odchozí IP kapacitu nad rámec toho, co potřebujete. Kromě toho musíte při výpočtu kapacity odchozí IP adresy účtu pro automatické škálování clusteru a možnost upgradů fondu uzlů. Pro automatické škálování clusteru Zkontrolujte aktuální počet uzlů a maximální počet uzlů a použijte vyšší hodnotu. Pro upgrade můžete pro každý fond uzlů, který umožňuje upgradování, přihlédnout k virtuálnímu počítači pro další uzly.

- Při nastavování *IdleTimeoutInMinutes* na jinou hodnotu než výchozí hodnota 30 minut zvažte, jak dlouho budou vaše úlohy potřebovat odchozí připojení. Zvažte také výchozí hodnotu časového limitu pro nástroj pro vyrovnávání zatížení *Standard* SKU, který se používá mimo AKS, na 4 minuty. Hodnota *IdleTimeoutInMinutes* , která přesněji odráží konkrétní úlohu AKS, může přispět ke snížení vyčerpání SNAT způsobená vytvořením připojení, která se už nepoužívají.

> [!WARNING]
> Změna hodnot pro *AllocatedOutboundPorts* a *IdleTimeoutInMinutes* může významně změnit chování odchozího pravidla pro nástroj pro vyrovnávání zatížení a nemělo by se dělat lehce, aniž byste pochopili kompromisy a vzory připojení vaší aplikace, podívejte se do [části řešení potíží s aktualizací SNAT níže][troubleshoot-snat] a v Azure [Load Balancer zkontrolujte odchozí pravidla][azure-lb-outbound-rules-overview] a [odchozí připojení][azure-lb-outbound-connections] , abyste plně pochopili dopad vašich změn.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Omezení příchozího provozu do konkrétních rozsahů IP adres

Následující manifest používá *loadBalancerSourceRanges* k určení nového rozsahu IP adres pro příchozí externí provoz:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> Příchozí externí přenosové toky z nástroje pro vyrovnávání zatížení do virtuální sítě pro cluster AKS. Virtuální síť má skupinu zabezpečení sítě (NSG), která umožňuje veškerý příchozí provoz z nástroje pro vyrovnávání zatížení. Tento NSG používá k povolení provozu z nástroje pro vyrovnávání zatížení [značku služby][service-tags] *typu vyrovnávání* zatížení.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Udržovat IP adresu klienta při příchozích připojeních

Ve výchozím nastavení služba typu `LoadBalancer` [v Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) a v AKS nezachová IP adresu klienta v připojení k části pod. Zdrojová IP adresa v paketu, který se doručí do uzlu pod, bude privátní IP adresa uzlu. Chcete-li zachovat IP adresu klienta, je nutné nastavit `service.spec.externalTrafficPolicy` na `local` definici služby. Následující manifest ukazuje příklad:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Další přizpůsobení prostřednictvím poznámek Kubernetes

Níže je uveden seznam poznámek podporovaných pro služby Kubernetes Services s typem `LoadBalancer` . Tyto poznámky platí pouze pro **příchozí** toky:

| Poznámka | Hodnota | Popis
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` nebo `false`                     | Určete, zda má být Nástroj pro vyrovnávání zatížení interní. Pokud není nastavená, je výchozí nastavení veřejné.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Název podsítě                    | Určete podsíť, na kterou má být interní nástroj pro vyrovnávání zatížení vázán. Pokud není nastavená podsíť nakonfigurovaná v konfiguračním souboru cloudu, je nastavená jako výchozí.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Název DNS popisku na veřejných IP adresách   | Zadejte název popisku DNS pro **veřejnou** službu. Pokud je nastavené na prázdný řetězec, nebude se používat položka DNS ve veřejné IP adrese.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` nebo `false`                     | Určete, že by měla být služba vystavená pomocí pravidla zabezpečení Azure, které může být sdíleno s jinou službou, zajištěním konkrétního obchodování s pravidly pro zvýšení počtu služeb, které mohou být vystaveny. Tato poznámka spoléhá na funkci [Rozšířená pravidla zabezpečení](../virtual-network/network-security-groups-overview.md#augmented-security-rules) Azure u skupin zabezpečení sítě. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Název skupiny prostředků            | Zadejte skupinu prostředků pro veřejné IP adresy nástroje pro vyrovnávání zatížení, které nejsou ve stejné skupině prostředků jako infrastruktura clusteru (skupina prostředků uzlu).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Seznam povolených značek služby          | Zadejte seznam povolených [značek služby][service-tags] oddělený čárkou.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Vypršení časového limitu nečinnosti protokolu TCP v minutách          | Zadejte dobu v minutách, po kterou mají v nástroji pro vyrovnávání zatížení dojít k vypršení časového limitu nečinnosti připojení protokolu TCP. Výchozí a minimální hodnota je 4. Maximální hodnota je 30. Musí být celé číslo.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Zakázat `enableTcpReset` pro SLB


## <a name="troubleshooting-snat"></a>Řešení potíží s SNAT

Pokud víte, že spouštíte mnoho odchozích připojení TCP nebo UDP ke stejné cílové IP adrese a portu, a sledujete neúspěšné odchozí připojení nebo jsou na podporu, že vyčerpáte porty SNAT (předběžně přidělené dočasné porty používané PAT), máte k dispozici několik obecných možností zmírnění. Projděte si tyto možnosti a rozhodněte, co je k dispozici a co nejlépe vyhovuje vašemu scénáři. Je možné, že jeden nebo více může pomáhat při správě tohoto scénáře. Podrobné informace najdete v [Průvodci odstraňováním potíží odchozích připojení](../load-balancer/troubleshoot-outbound-connection.md).

Hlavní příčinou vyčerpání SNAT je i anti-vzor pro způsob, jakým se u odchozího připojení naváže, spravuje nebo konfigurovatelné časovače, které se mění z výchozích hodnot. Pečlivě si prostudujte tuto část.

### <a name="steps"></a>Postup
1. Ověřte, jestli vaše připojení netrvají po dlouhou dobu, a spoléhá se na výchozí časový limit nečinnosti pro uvolnění tohoto portu. Pokud ano, může být pro váš scénář nutné snížit výchozí časový limit 30 minut.
2. Prozkoumejte, jak vaše aplikace vytváří odchozí připojení (například revize kódu nebo zachycení paketů).
3. Určete, zda má tato aktivita očekávané chování nebo zda se aplikace nechová. Pomocí [metrik](../load-balancer/load-balancer-standard-diagnostics.md) a [protokolů](../load-balancer/load-balancer-monitor-log.md) v Azure monitor doložit vaše závěry. V případě metriky připojení SNAT použijte kategorii "neúspěšné".
4. Vyhodnotí, zda jsou následovány příslušné [vzory](#design-patterns) .
5. Vyhodnoťte, jestli se má vyčerpání portů SNAT zmírnit pomocí [dalších odchozích IP adres a dalších přidělených odchozích portů](#configure-the-allocated-outbound-ports) .

### <a name="design-patterns"></a>Způsoby návrhu
Kdykoli je to možné, využijte výhod opětovného použití připojení a sdružování připojení. Tyto vzory se vyhne problémům s vyčerpáním prostředků a mají za následek předvídatelné chování. Primitivní prvky pro tyto vzory se dají najít v řadě vývojových knihoven a architektur.

- Atomické žádosti (jedna žádost na připojení) obecně není vhodným návrhem. Taková omezení pro antipatterny se omezují na škálování, snižuje výkon a snižuje spolehlivost. Místo toho můžete znovu použít připojení HTTP/S a snížit tak počet připojení a přidružené porty SNAT. Škálování aplikace se zvýší a vylepšit výkon kvůli snížení nákladů na handshake, režijních a kryptografických operací při použití TLS.
- Pokud používáte mimo cluster nebo vlastní DNS nebo vlastní nadřazené servery na coreDNS, je třeba mít na paměti, že služba DNS může zavést mnoho jednotlivých toků na svazku, když klient neukládá do mezipaměti výsledky překladačů DNS. Než použijete vlastní servery DNS, nezapomeňte místo toho přizpůsobit coreDNS a definovat dobrou hodnotu pro ukládání do mezipaměti.
- Toky UDP (například vyhledávání DNS) přidělují porty SNAT po dobu nečinnosti časového limitu. Čím delší časový limit nečinnosti, tím vyšší je tlak na portech SNAT. Použijte krátký časový limit nečinnosti (například 4 minuty).
Pomocí fondů připojení natvarujte svazek připojení.
- Nikdy netiché opuštění toku TCP a spoléhá na časovače TCP, aby se vyčistil tok. Pokud nepovolíte, aby protokol TCP explicitně zavřel připojení, stav zůstane přidělený v zprostředkujících systémech a koncových bodech a zpřístupňuje porty SNAT pro další připojení. Tento model může aktivovat selhání aplikace a vyčerpání SNAT.
- Neměňte hodnoty časovače související s ukončením protokolu TCP na úrovni operačního systému bez odborných znalostí o dopadu. I když dojde k obnovení zásobníku protokolu TCP, výkon vaší aplikace může být negativně ovlivněn, pokud koncovým bodům připojení neodpovídají očekávání. Chcete-li změnit časovače, je obvykle znaménkem základního problému s návrhem. Přečtěte si následující doporučení.


Výše uvedený příklad aktualizuje pravidlo tak, aby povolovalo příchozí externí přenosy jenom z rozsahu *MY_EXTERNAL_IP_RANGE* . Pokud nahradíte *MY_EXTERNAL_IP_RANGE* s IP adresou interní podsítě, přenosy se omezují jenom na interní IP adresy clusteru. To neumožní přístup k nástroji pro vyrovnávání zatížení klientům mimo váš cluster Kubernetes.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Přesun ze základního nástroje pro vyrovnávání zatížení SKU na standardní SKU

Pokud máte existující cluster se základní Load Balancer SKU, při migraci na používání clusteru se standardní SKU Load Balancer je důležité rozdíly v chování.

Například provedení modrých a zelených nasazení pro migraci clusterů je běžným postupem, který je určen pro `load-balancer-sku` typ clusteru, lze definovat pouze v době vytváření clusteru. Základní nástroje pro vyrovnávání zatížení *SKU* ale používají *základní* IP adresy SKU, které nejsou kompatibilní se *standardními* nástroji pro vyrovnávání zatížení SKU, protože vyžadují standardní IP adresy *SKU* . Při migraci clusterů na upgrade Load Balancer SKU se vyžaduje nová IP adresa s kompatibilní SKLADOVOU adresou IP.

Další informace o tom, jak migrovat clustery, najdete [v naší dokumentaci týkající se migrace](aks-migration.md) , kde najdete seznam důležitých témat, která je potřeba vzít v úvahu při migraci. Při použití standardních nástrojů pro vyrovnávání zatížení SKU v AKS jsou také důležité rozdíly v chování.

## <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují Nástroj pro vyrovnávání zatížení se *standardní* SKU, platí následující omezení:

* Pro povolení odchozího provozu z clusteru AKS se vyžaduje aspoň jedna předpona veřejné IP adresy nebo IP adresy. K udržení připojení mezi řídicí rovinou a uzly agenta a zajištěním kompatibility s předchozími verzemi AKS se taky vyžaduje předpona veřejných IP adres nebo IP adres. Máte následující možnosti, jak zadat veřejné IP adresy nebo předpony IP pomocí nástroje pro vyrovnávání zatížení *Standard* SKU:
    * Poskytněte vlastní veřejné IP adresy.
    * Zadejte vlastní předpony veřejných IP adres.
    * Zadejte číslo až 100, aby mohl cluster AKS vytvořit tento počet veřejných IP adres *Standard* SKU ve stejné skupině prostředků, která se vytvořila jako cluster AKS, který se obvykle jmenuje s *MC_* na začátku. AKS přiřadí veřejnou IP adresu k nástroji pro vyrovnávání zatížení *Standard* SKU. Ve výchozím nastavení se jedna veřejná IP adresa automaticky vytvoří ve stejné skupině prostředků jako cluster AKS, pokud není zadaná žádná veřejná IP adresa, předpona veřejné IP adresy nebo počet IP adres. Je také nutné, abyste povolili veřejné adresy a nevytvořili Azure Policy, která zakazuje vytváření IP adres.
* Veřejná IP adresa vytvořená pomocí AKS se nedá znovu použít jako vlastní adresa vlastní veřejné IP adresy. Všechny vlastní IP adresy musí vytvořit a spravovat uživatel.
* Definování SKU nástroje pro vyrovnávání zatížení se dá provést jenom při vytváření clusteru AKS. SKU nástroje pro vyrovnávání zatížení nelze změnit po vytvoření clusteru AKS.
* V jednom clusteru můžete použít jenom jeden typ SKU nástroje pro vyrovnávání zatížení (Basic nebo Standard).
* *Standardní* Služby Vyrovnávání zatížení SKU podporují jenom IP adresy *standardních* SKU.


## <a name="next-steps"></a>Další kroky

Další informace o službách Kubernetes Services najdete v [dokumentaci ke službám Kubernetes][kubernetes-services].

Přečtěte si další informace o používání interního Load Balancer pro příchozí provoz v [dokumentaci k internímu Load Balancer AKS](internal-lb.md).

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
