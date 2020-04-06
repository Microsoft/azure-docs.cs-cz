---
title: Použití standardního účetního čítitele skladového úskladu skladového úpisu
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak pomocí vyrovnávání zatížení se standardní skladovou položkou vystavit své služby pomocí služby Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 17e474de9c221126d67cc2982ba11c6ff75e7aa3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668497"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Použití standardního vyvyčovávače zatížení skladových položk ve službě Azure Kubernetes Service (AKS)

Chcete-li poskytnout přístup k aplikacím prostřednictvím služeb Kubernetes typu `LoadBalancer` ve službě Azure Kubernetes Service (AKS), můžete použít Azure Load Balancer. Systém pro vyrovnávání zatížení běžící na AKS lze použít jako interní nebo externí systém vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení zpřístupňuje službu Kubernetes pouze aplikacím spuštěným ve stejné virtuální síti jako cluster AKS. Externí správce zatížení obdrží jeden nebo více veřejných IP adresy pro příchozí přenos dat a zpřístupní službu Kubernetes externě pomocí veřejných IP služeb.

Azure Load Balancer je k dispozici ve dvou sku - *základní* a *standardní*. Ve výchozím nastavení se *standardní* skladová položka používá při vytváření clusteru AKS. Použití *standardního* vyvyčovávače skladového zdroje poskytuje další funkce a funkce, jako je například větší velikost fondu back-endu a zóny dostupnosti. Je důležité, abyste pochopili rozdíly mezi *standardními* a *základními* vykladači zatížení před výběrem, které chcete použít. Po vytvoření clusteru AKS nelze změnit skladovou položku správce zatížení pro tento cluster. Další informace o *základních* a *standardních* skladových položkách naleznete v [tématu porovnání skladových položk služby vyrovnávání zatížení Azure][azure-lb-comparison].

Tento článek předpokládá základní znalosti kubernetes a Azure Balancer koncepty. Další informace najdete v tématu [Kubernetes základní koncepty pro službu Azure Kubernetes Service (AKS)][kubernetes-concepts] a [co je Azure Load Balancer?][azure-lb].

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.81 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte cluster AKS se *standardním* sku Azure Balancer. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Objekt zabezpečení clusterové služby AKS potřebuje také oprávnění ke správě síťových prostředků, pokud používáte existující podsíť nebo skupinu prostředků. Obecně přiřaďte roli *přispěvatele sítě* k instančnímu objektu v delegovaných prostředcích. Další informace o oprávněních najdete [v tématu Delegate AKS přístup k jiným prostředkům Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Přechod ze základního účetního vytížení skladové položky na standardní skladovou položku

Pokud máte existující cluster se základním vyvyčovávacím programem skladových časových stavů, existují důležité rozdíly v chování, které je třeba si uvědomit při migraci, abyste mohli použít cluster se standardním vyvažovačem zatížení skladové položky.

Například provádění modrých/zelených nasazení pro migraci clusterů je běžnou praxí vzhledem k tomu, že `load-balancer-sku` typ clusteru lze definovat pouze v době vytvoření clusteru. Základní *vykladače* zatížení skladových položk však používají základní adresy IP *skladových* dUŽIn, které nejsou kompatibilní se standardními vykladači zatížení *skladových* účto služeb, protože vyžadují standardní adresy IP *skladových* položk. Při migraci clusterů za účelem upgradu skladových položk pro vyrovnávání zatížení bude vyžadována nová adresa IP s kompatibilní skladovou položkou IP adresy.

Další informace o migraci clusterů naleznete v [naší dokumentaci k aspektům migrace](aks-migration.md) a prohlédněte si seznam důležitých témat, která je třeba vzít v úvahu při migraci. Níže uvedená omezení jsou také důležité behaviorální rozdíly na vědomí při použití standardních skladových položk y vyrovnávání zatížení v AKS.

### <a name="limitations"></a>Omezení

Následující omezení platí při vytváření a správě clusterů AKS, které podporují vyrovnávání zatížení se *standardní* skladovou položkou:

* Pro povolení odchozích přenosů z clusteru AKS je vyžadována alespoň jedna veřejná předpona IP nebo IP adresy. Veřejná ip nebo IP předpona je také vyžadována k udržení připojení mezi řídicí rovinou a uzly agenta a k zachování kompatibility s předchozími verzemi AKS. Máte následující možnosti pro určení veřejných IP adres nebo předpon IP pomocí *standardního* nástroje pro vyrovnávání zatížení skladové položky:
    * Zadejte své vlastní veřejné IP adresy.
    * Zadejte vlastní veřejné IP předpony.
    * Zadejte číslo do 100, aby cluster AKS mohl vytvořit tolik veřejných IP serverů *standardní* sku ve stejné skupině prostředků vytvořené jako cluster AKS, který je obvykle pojmenován *MC_* na začátku. AKS přiřadí veřejnou IP adresu *standardnímu* účetnímu vytížení skladového úhlavního účtu skladového úhlavního účtu. Ve výchozím nastavení bude jedna veřejná ADRESA IP automaticky vytvořena ve stejné skupině prostředků jako cluster AKS, pokud není zadána žádná veřejná IP adresa, veřejná předpona IP nebo počet IP adres. Musíte také povolit veřejné adresy a vyhnout se vytváření zásad Azure, které zakazují vytváření IP adres.
* Při použití *standardní* skladové položky pro vyrovnávání zatížení je nutné použít kubernetes verze *1.13 nebo vyšší*.
* Definování skladové položky správce zatížení lze provést pouze při vytvoření clusteru AKS. Po vytvoření clusteru AKS nelze změnit skladovou položku správce zatížení.
* V jednom clusteru lze použít pouze jeden typ skladové položky pro vyrovnávání zatížení (základní nebo standardní).
* *Standardní* Vykladače zatížení soupoložky podporují pouze *standardní* adresy IP skladových položk.

## <a name="use-the-standard-sku-load-balancer"></a>Použití *standardního* účetního čítitele skladového úpisu

Při vytváření clusteru AKS se *standardpro* vyrovnávání zatížení sku používá při spuštění služeb v tomto clusteru. Například [rychlý start pomocí azure CLI][aks-quickstart-cli] nasadí ukázkovou aplikaci, která používá *standardní* vyrovnávání zatížení skladové položky.

> [!IMPORTANT]
> Veřejné IP adresy lze vyhnout přizpůsobením uživatelem definované trasy (UDR). Určení odchozího typu clusteru AKS jako UDR může přeskočit nastavení zřizování IP a back-endového fondu pro aks vytvořený azure balancer. Viz [nastavení clusteru `outboundType` na 'userDefinedRouting'](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Konfigurace systému vyrovnávání zatížení jako interního

Můžete také nakonfigurovat vyrovnávání zatížení jako interní a nevystavit veřejnou IP adresu. Chcete-li nakonfigurovat správce zatížení `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jako interní, přidejte jako poznámku ke službě *LoadBalancer.* Můžete vidět příklad yaml manifest, stejně jako další podrobnosti o vnitřní nástroj pro vyrovnávání zatížení [zde][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Škálování počtu spravovaných veřejných IP služeb

Při použití *standardního* vyvažovače skladových míst se spravovanými veřejnými IP adresami, které jsou vytvořeny ve výchozím nastavení, můžete škálovat počet spravovaných odchozích veřejných IP adres pomocí parametru *počet spravovaných ip adres vyvažovače zatížení.*

Chcete-li aktualizovat existující cluster, spusťte následující příkaz. Tento parametr lze také nastavit v době vytvoření clusteru, aby měl více spravovaných odchozích veřejných IP serverů.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Výše uvedený příklad nastaví počet spravovaných odchozích veřejných IP adresy na *2* pro cluster *myAKSCluster* v *myResourceGroup*. 

Pomocí parametru *load-balancer-managed-ip-count* můžete také nastavit počáteční počet spravovaných odchozích veřejných `--load-balancer-managed-outbound-ip-count` IP adres při vytváření clusteru připojením parametru a jeho nastavením na požadovanou hodnotu. Výchozí počet spravovaných odchozích veřejných IP služeb je 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Poskytněte vlastní veřejné IP adresy nebo předpony pro odchozí přenos dat

Při použití *standardního* účetního stavu SKU clusteru AKS automaticky vytvoří veřejnou IP adresu ve stejné skupině prostředků vytvořené pro cluster AKS a přiřadí veřejnou IP adresu *standardnímu* správci zatížení skladových míst. Případně můžete přiřadit vlastní veřejnou IP adresu v době vytvoření clusteru nebo můžete aktualizovat vlastnosti existujícího clusteru pro vyrovnávání zatížení.

Tím, že přináší více IP adres nebo předpony, můžete definovat více zálohování služby při definování IP adresu za jeden objekt vyrovnávání zatížení. Koncový bod odchozího přenosu dat konkrétních uzlů bude záviset na tom, k jaké službě jsou přidruženy.

> [!IMPORTANT]
> Je nutné použít veřejné IP adresy *sku standardní* sku pro odchozí přenos se *standardní* skladovou položkou vyrovnávání zatížení vyrovnávání zatížení. Skladovou položku veřejných IP adres můžete ověřit pomocí příkazu [az network public-ip show:][az-network-public-ip-show]
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Pomocí příkazu [az network public-ip show][az-network-public-ip-show] uveďte ID veřejných IP adres.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID veřejné IP adresy *myPublicIP* ve skupině prostředků *myResourceGroup.*

Pomocí příkazu *az aks update* s parametrem *load-balancer-outbound-ips* aktualizujte cluster pomocí veřejných IP adresy.

Následující příklad používá parametr *Load-balancer-outbound-ips* s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Můžete také použít veřejné IP předpony pro odchozí přenos se *standardním* vyrovnáváním zatížení skladové položky. Následující příklad používá příkaz [az network public-ip show show][az-network-public-ip-prefix-show] k zobrazení ID veřejných předpon IP:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID předpony veřejné IP adresy *myPublicIPPrefix* ve skupině prostředků *myResourceGroup.*

Následující příklad používá parametr load *balancer-outbound-ip-prefixes* s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Veřejné IP adresy a ip předpony musí být ve stejné oblasti a součástí stejného předplatného jako cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definování vlastní veřejné IP adresy nebo předpon v době vytváření clusteru

Možná budete chtít přenést vlastní IP adresy nebo IP předpony pro odchozí přenos v době vytváření clusteru pro podporu scénářů, jako je zjišťování povolených koncových bodů. Přidejte stejné parametry uvedené výše ke kroku vytvoření clusteru a definujte vlastní veřejné IP adresy a IP předpony na začátku životního cyklu clusteru.

Pomocí příkazu *az aks create* s parametrem *load-balancer-outbound-ips* vytvořte nový cluster s veřejnými IP adresy na začátku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Pomocí příkazu *az aks create* s parametrem *load-balancer-outbound-ip-prefixes* vytvořte nový cluster s veřejnými předponami IP na začátku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>Konfigurace odchozích portů a časového limitu nečinnosti

> [!WARNING]
> Následující část je určena pro pokročilé scénáře většího rozsahu sítě nebo pro řešení problémů vyčerpání SNAT s výchozí konfigurace. Před změnou *alokovaného výstupního portu* nebo *idletimeoutinminutes* z výchozí hodnoty musíte mít přesný soupis dostupné kvóty pro virtuální sítě a adresy IP, aby bylo možné udržovat clustery v pořádku.
> 
> Změna hodnoty *pro AlocatedOutboundPorts* a *IdleTimeoutInMinutes* může výrazně změnit chování odchozí pravidlo pro vyrovnávání zatížení. Před aktualizací těchto hodnot zkontrolujte [odchozí pravidla nástrojů pro vyrovnávání][azure-lb-outbound-rules-overview]zatížení , odchozí pravidla [vyrovnávání zatížení][azure-lb-outbound-rules]a odchozí připojení v [Azure,][azure-lb-outbound-connections] abyste tyto hodnoty plně porozuměli dopadu vašich změn.

Odchozí přidělené porty a jejich časové limity nečinnosti se používají pro [snat][azure-lb-outbound-connections]. Standard *SKU* balancer standard udává [automatické přiřazení pro počet odchozích portů na základě velikosti back-endového fondu][azure-lb-outbound-preallocatedports] a 30minutového časového limitu nečinnosti pro každý port. Chcete-li zobrazit tyto hodnoty, použijte [seznam odchozích pravidel az network lb][az-network-lb-outbound-rule-list] k zobrazení odchozího pravidla pro vykladač zatížení:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Předchozí příkazy zobrazí odchozí pravidlo pro vyrovnávání zatížení, například:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Ukázkový výstup zobrazuje výchozí hodnotu pro *AlocatedOutboundPorts* a *IdleTimeoutInMinutes*. Hodnota 0 pro *přidělené odchozí porty* nastaví počet odchozích portů pomocí automatického přiřazení pro počet odchozích portů na základě velikosti fondu back-end. Například pokud cluster má 50 nebo méně uzlů, jsou přiděleny 1024 porty pro každý uzel.

Zvažte změnu nastavení *přidělenéOutboundPorts* nebo *IdleTimeoutInMinutes,* pokud očekáváte, že čelit vyčerpání SNAT na základě výše uvedené výchozí konfigurace. Každá další IP adresa umožňuje 64 000 dalších portů pro přidělení, ale nástroj Pro vyrovnávání zatížení Azure Standard automaticky nezvýší porty na uzel při přidání dalších IP adres. Tyto hodnoty můžete změnit nastavením parametrů vyrovnávání zatížení a *výstupních portů* a *vyrovnávání zatížení-idle-timeout.* Například:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Před přizpůsobením *přidělených outboundports* je nutné [vypočítat požadovanou kvótu,][calculate-required-quota] abyste se vyhnuli problémům s připojením nebo škálováním. Hodnota, kterou zadáte pro *přidělenéoutporty* musí být také násobkem 8.

Při vytváření clusteru můžete také použít parametry *load-balancer-out-out-ips* a load *balancer-idle-timeout,* ale musíte také zadat buď *vyrovnávání zatížení spravované-odchozí-ip-count*, *vyrovnávání zatížení-odchozí-ips*nebo *vyrovnávání zatížení-odchozí-ip-prefixy.*  Například:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Při změně parametrů *vyrovnávání vyvažovače zatížení* a parametrů *časového limitu pro vyrovnávání zatížení* z výchozího nastavení ovlivňuje chování profilu vykladače zatížení, což má vliv na celý cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Požadovaná kvóta pro přizpůsobení přidělených odchozích portů
Musíte mít dostatek výstupní kapacity IP na základě počtu virtuálních počítačů uzlů a požadovaných přidělených odchozích portů. Chcete-li ověřit, že máte dostatek výstupní kapacity IP, použijte následující vzorec: 
 
*odchozí IP adresy* \* 64 000 \> uzlů *VM požadovánoAlokOutboundPorts* *nodeVMs* \* .
 
Například pokud máte 3 *nodeVMs*a 50 000 *desiredAllocatedOutboundPorts*, musíte mít alespoň 3 *odchozí IPs*. Doporučujeme začlenit další výstupní kapacitu IP nad rámec toho, co potřebujete. Kromě toho je nutné účet pro automatické škálování clusteru a možnost upgradu fondu uzlů při výpočtu odchozí kapacity IP. Pro automatické škálování clusteru zkontrolujte aktuální počet uzlů a maximální počet uzlů a použijte vyšší hodnotu. Pro upgrade účet pro další uzel virtuálního virtuálního účtu pro každý fond uzlů, který umožňuje upgrade.
 
Při nastavování *IdleTimeoutInMinutes* na jinou hodnotu, než je výchozí 30 minut, zvažte, jak dlouho vaše úlohy budou potřebovat odchozí připojení. Zvažte také výchozí hodnotu časového limitu pro *standardní* skladovou položku pro vyrovnávání zatížení používané mimo AKS je 4 minuty. Hodnota *IdleTimeoutInMinutes,* která přesněji odráží vaše konkrétní úlohy AKS může pomoci snížit vyčerpání SNAT způsobené vázání připojení již nepoužívá.

## <a name="restrict-access-to-specific-ip-ranges"></a>Omezení přístupu k určitým rozsahům IP adres

Skupina zabezpečení sítě (NSG) přidružená k virtuální síti pro nástroj pro vyrovnávání zatížení má ve výchozím nastavení pravidlo umožňující všechny příchozí externí přenosy. Toto pravidlo můžete aktualizovat tak, aby umožňovalo pouze určité rozsahy IP adres pro příchozí přenosy. Následující manifest používá *loadBalancerSourceRanges* k určení nového rozsahu IP pro příchozí externí přenosy:

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

Výše uvedený příklad aktualizuje pravidlo tak, aby umožňovalo pouze příchozí externí přenosy z *rozsahu MY_EXTERNAL_IP_RANGE.* Další informace o použití této metody k omezení přístupu ke službě nástroje pro vyrovnávání zatížení jsou k dispozici v [dokumentaci Kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Další kroky

Další informace o službách Kubernetes naleznete v [dokumentaci ke službám Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
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
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
