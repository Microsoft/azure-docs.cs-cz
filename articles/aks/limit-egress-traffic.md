---
title: Omezení odchozího provozu ve službě Azure Kubernetes (AKS)
description: Informace o tom, jaké porty a adresy se vyžadují k řízení odchozího provozu ve službě Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 9476290669606f6eb6c56b51497f3026b9613698
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034949"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Preview – omezení odchozího provozu pro uzly clusteru a řízení přístupu k požadovaným portům a službám ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení mají clustery AKS neomezený odchozí (výstupní) přístup k Internetu. Tato úroveň přístupu k síti umožňuje uzlům a službám, které spouštíte pro přístup k externím prostředkům, podle potřeby. Pokud chcete omezit výstupní přenos dat, musí být k dispozici omezený počet portů a adres, aby bylo možné udržovat v pořádku úlohy údržby clusteru. Cluster se pak nakonfiguruje tak, aby používal image kontejnerů základního systému z Microsoft Container Registry (MCR) nebo Azure Container Registry (ACR), ne z externích veřejných úložišť. Je nutné nakonfigurovat upřednostňovanou bránu firewall a pravidla zabezpečení, aby bylo možné tyto požadované porty a adresy.

Tento článek podrobně popisuje, které síťové porty a plně kvalifikované názvy domény (FQDN) jsou povinné a volitelné, pokud omezíte odchozí přenosy v clusteru AKS.  Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Před zahájením

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.66 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

Pokud chcete vytvořit cluster AKS, který může omezit odchozí přenos, nejdřív u vašeho předplatného povolte příznak funkce. Tato registrace funkce nakonfiguruje všechny AKS clustery, které vytvoříte pro použití imagí kontejnerů základního systému z MCR nebo ACR. Chcete-li zaregistrovat příznak funkce *AKSLockingDownEgressPreview* , použijte příkaz [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí verze Preview se můžou použít výchozí hodnoty pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Zobrazení stavu v *registraci*trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Přehled odchozího provozu

Pro účely správy a provozu musí uzly v clusteru AKS přistupovat k určitým portům a plně kvalifikovaným názvům domén (FQDN). Tyto akce můžou být pro komunikaci se serverem rozhraní API nebo ke stažení a instalaci základních součástí clusteru Kubernetes a aktualizací zabezpečení uzlů. Ve výchozím nastavení není odchozí (odchozí) internetový provoz omezený pro uzly v clusteru AKS. Cluster může z externích úložišť získat základní image kontejneru systému.

Chcete-li zvýšit zabezpečení clusteru AKS, můžete chtít omezit odchozí přenosy. Cluster je nakonfigurovaný tak, aby vyčetl základní image kontejneru systému z MCR nebo ACR. Pokud tímto způsobem zamknete výstupní přenos, je nutné definovat konkrétní porty a plně kvalifikované názvy domény, aby uzly AKS správně komunikovaly s požadovanými externími službami. Bez těchto autorizovaných portů a plně kvalifikovaných názvů domény nemůžou uzly AKS komunikovat se serverem rozhraní API nebo instalovat součásti jádra.

K zabezpečení odchozího provozu a definování potřebných portů a adres můžete použít [Azure firewall][azure-firewall] nebo zařízení brány firewall jiného výrobce. AKS automaticky nevytváří tato pravidla. Při vytváření vhodných pravidel v bráně firewall sítě jsou k disadrese následující porty a adresy.

> [!IMPORTANT]
> Když použijete Azure Firewall k omezení odchozího provozu a vytvoření trasy definované uživatelem (UDR) k vynucení veškerého odchozího provozu, ujistěte se, že jste v bráně firewall vytvořili příslušné pravidlo DNAT pro správné povolení provozu příchozího přenosu dat. Použití Azure Firewall s UDR v důsledku asymetrického směrování narušuje nastavení příchozího přenosu dat. (K tomuto problému dochází, protože podsíť AKS má výchozí trasu, která přechází na privátní IP adresu brány firewall, ale používáte veřejný Nástroj pro vyrovnávání zatížení – příchozí nebo Kubernetes služba typu: Nástroj pro vyrovnávání zatížení). V tomto případě se příchozí provoz nástroje pro vyrovnávání zatížení přijímá prostřednictvím veřejné IP adresy, ale návratová cesta prochází přes privátní IP adresu brány firewall. Vzhledem k tomu, že brána firewall je stavová, dojde k vrácení vráceného paketu, protože brána firewall neví o zavedené relaci. Informace o tom, jak integrovat Azure Firewall s vaším nástrojem pro vyrovnávání zatížení pro příchozí přenosy nebo služby, najdete v tématu věnovaném [integraci Azure firewall s Azure Standard Load Balancer](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb).
>

V AKS existují dvě sady portů a adres:

* [Požadované porty a adresy pro clustery AKS podrobně vyžadují](#required-ports-and-addresses-for-aks-clusters) minimální požadavky pro ověřený odchozí přenos.
* [Volitelné Doporučené adresy a porty pro clustery AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) se nevyžadují pro všechny scénáře, ale integrace s jinými službami, jako je Azure monitor, nebudou správně fungovat. Zkontrolujte tento seznam volitelných portů a plně kvalifikovaných názvů domény a autorizujte všechny služby a součásti používané v clusteru AKS.

> [!NOTE]
> Omezení odchozího provozu funguje jenom na nových clusterech AKS vytvořených po povolení registrace příznaků funkcí. U existujících clusterů [proveďte operaci upgradu clusteru][aks-upgrade] pomocí `az aks upgrade` příkazu před omezením odchozího provozu.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Požadované porty a adresy pro clustery AKS

Pro cluster AKS se vyžadují následující Odchozí porty nebo Síťová pravidla:

* Port TCP *443*
* Port TCP *9000* a TCP port *22* pro front-endu tunelu pro komunikaci s koncovým tunelem na serveru rozhraní API.
    * Pokud chcete získat konkrétnější informace, podívejte se na * *.\< HCP. Location\>. azmk8s.IO* a * *. tun.\< Location\>. azmk8s.IO* adresy v následující tabulce.

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

| PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. Umístění nahraďte *\<oblastí, ve které je nasazený cluster AKS.\>* |
| *.tun. \<Location\>. azmk8s.IO | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. Umístění nahraďte *\<oblastí, ve které je nasazený cluster AKS.\>* |
| aksrepos.azurecr.io        | HTTPS:443 | Tato adresa je vyžadována pro přístup k obrázkům v Azure Container Registry (ACR). Tento registr obsahuje image a grafy třetích stran (například server metrik, základní DNS atd.), které se vyžadují pro fungování clusteru během upgradu a škálování clusteru.|
| *.blob.core.windows.net    | HTTPS:443 | Tato adresa je back-end úložiště imagí uložených v ACR. |
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je nutná pro přístup k obrázkům v Microsoft Container Registry (MCR). Tento registr obsahuje soubory a grafy první strany (například Moby atd.), které se vyžadují pro fungování clusteru během upgradu a škálování clusteru. |
| *.cdn.mscr.io              | HTTPS:443 | Tato adresa je vyžadována pro MCR úložiště zajištěné službou Azure Content Delivery Network (CDN). |
| management.azure.com       | HTTPS:443 | Tato adresa je vyžadována pro Kubernetes operace GET/PUT. |
| login.microsoftonline.com  | HTTPS:443 | Tato adresa je vyžadována pro Azure Active Directory ověřování. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa se vyžaduje pro synchronizaci času NTP na uzlech se systémem Linux. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Volitelné Doporučené adresy a porty pro clustery AKS

* Port UDP *53* pro DNS

Pro správné fungování clusterů AKS se doporučuje následující plně kvalifikovaný název domény nebo pravidla použití:

| PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com                           | HTTP:80   | Tato adresa umožní uzlům clusteru se systémem Linux stáhnout požadované opravy a aktualizace zabezpečení. |
| packages.microsoft.com                  | HTTPS:443 | Tato adresa je úložiště balíčků Microsoftu používané pro operace *apt-get* uložené v mezipaměti. |
| dc.services.visualstudio.com            | HTTPS:443 | Doporučuje se pro správné metriky a monitorování pomocí Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Doporučuje se pro správné metriky a monitorování pomocí Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Doporučuje se pro správné metriky a monitorování pomocí Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Tato adresa se používá pro správnou operaci Azure Policy (aktuálně ve verzi Preview v AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |
| nvidia.github.io                        | HTTPS:443 | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jaké porty a adresy se mají povolit, pokud omezíte odchozí přenosy clusteru. Můžete také definovat, jak můžou částice a jaká omezení v clusteru komunikovat. Další informace najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě v AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
