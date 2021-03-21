---
title: Přizpůsobení uživatelsky definovaných tras (UDR) ve službě Azure Kubernetes Service (AKS)
description: Naučte se definovat vlastní výstupní trasu ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: 72ba90510afb00ee001c97612e88f452039f53a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182124"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Přizpůsobení výstupů clusteru pomocí User-Defined trasy

Odchozí přenos dat z clusteru AKS se dá přizpůsobit tak, aby vyhovoval specifickým scénářům. Ve výchozím nastavení AKS zřídí standardní SKU Load Balancer, který se má nastavit a použít k odchozímu přenosu dat. Výchozí nastavení ale nemusí splňovat požadavky všech scénářů, pokud jsou veřejné IP adresy zakázané nebo jsou pro odchozí přenosy nutné další segmenty směrování.

Tento článek vás seznámí s postupem přizpůsobení odchozí trasy clusteru pro podporu vlastních síťových scénářů, jako jsou například ty, které nepovolují veřejné IP adresy a vyžadují, aby se cluster zacházel za virtuálním síťovým zařízením (síťové virtuální zařízení).

## <a name="prerequisites"></a>Předpoklady
* Azure CLI verze 2.0.81 nebo vyšší
* Verze rozhraní API `2020-01-01` nebo vyšší


## <a name="limitations"></a>Omezení
* OutboundType se dá definovat jenom v době vytváření clusteru a nedá se aktualizovat později.
* Nastavení `outboundType` vyžaduje clustery AKS s `vm-set-type` `VirtualMachineScaleSets` a `load-balancer-sku` z `Standard` .
* Nastavení `outboundType` na hodnotu `UDR` vyžaduje trasu definovanou uživatelem s platným odchozím připojením pro cluster.
* Nastavení `outboundType` na hodnotu `UDR` znamená, že IP adresa příchozího přenosu dat směrovaného do nástroje pro vyrovnávání zatížení nemusí **odpovídat** cílové adrese odchozího výstupu clusteru.

## <a name="overview-of-outbound-types-in-aks"></a>Přehled odchozích typů v AKS

Cluster AKS se dá přizpůsobit jedinečným `outboundType` typem `loadBalancer` nebo `userDefinedRouting` .

> [!IMPORTANT]
> Typ odchozího přenosu ovlivňuje jenom výstupní přenos vašeho clusteru. Další informace najdete v tématu [nastavení řadičů](ingress-basic.md)příchozího přenosu dat.

> [!NOTE]
> Můžete použít vlastní [směrovací tabulku][byo-route-table] s udr a kubenet sítí. Ujistěte se, že identita clusteru (instanční objekt nebo spravovaná identita) má oprávnění přispěvatele k vlastní tabulce směrování.

### <a name="outbound-type-of-loadbalancer"></a>Odchozí typ vyrovnávání zatížení

Pokud `loadBalancer` je nastavená, AKS dokončí následující konfiguraci automaticky. Nástroj pro vyrovnávání zatížení se používá k odchozímu přenosu prostřednictvím AKS s přiřazenou veřejnou IP adresou. Odchozí typ `loadBalancer` podporuje služby Kubernetes typu `loadBalancer` , které očekávají výstup z nástroje pro vyrovnávání zatížení vytvořeného poskytovatelem prostředků AKS.

Následující konfigurace se provádí pomocí AKS.
   * Veřejná IP adresa se zřídí pro odchozí výstup clusteru.
   * Veřejné IP adresy se přiřadí prostředku nástroje pro vyrovnávání zatížení.
   * Back-endové fondy pro nástroj pro vyrovnávání zatížení jsou nastaveny pro uzly agentů v clusteru.

Níže je síťová topologie nasazená v clusterech AKS ve výchozím nastavení, která `outboundType` používají `loadBalancer` .

![Diagram zobrazuje vstupní I P a výstup I P, kde příchozí I P směřuje provoz na nástroj pro vyrovnávání zatížení, který směruje provoz do a z interního clusteru a další provoz na výstup I P, který směruje provoz na Internet, C-R, požadované služby Azure a řídicí rovinu A K S.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Odchozí typ userDefinedRouting

> [!NOTE]
> Použití odchozího typu je pokročilý scénář sítě a vyžaduje správnou konfiguraci sítě.

Pokud `userDefinedRouting` je nastavená, AKS nebude automaticky konfigurovat výstupní cesty. Nastavení výstupů se musí udělat sami.

Cluster AKS musí být nasazený do existující virtuální sítě s dříve nakonfigurovanou podsítí, protože pokud nepoužívá architekturu služby Load Balancer úrovně Standard (SLB), musíte nastavit explicitní výstup. Tato architektura proto vyžaduje explicitní odeslání odchozího provozu do zařízení, jako je brána firewall, brána, proxy server, nebo povolení překladu adres (NAT) veřejnou IP adresou přiřazenou ke službě Load Balancer úrovně Standard nebo zařízením.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Vytvoření nástroje pro vyrovnávání zatížení s userDefinedRouting

Clustery AKS s odchozím typem UDR obdrží standardní nástroj pro vyrovnávání zatížení (SLB) pouze v případě, že je nasazená první Kubernetes služba typu "vyrovnávání zatížení". Nástroj pro vyrovnávání zatížení je nakonfigurovaný s veřejnou IP adresou pro *příchozí* požadavky a back-end fond pro *příchozí* požadavky. Příchozí pravidla jsou nakonfigurovaná poskytovatelem cloudu Azure, ale **žádná odchozí veřejná IP adresa ani odchozí pravidla** se nekonfigurují jako výsledek pro odchozí typ udr. Váš UDR bude stále jediným zdrojem provozu pro výstup.

Nástroje pro vyrovnávání zatížení Azure neúčtují [poplatky, dokud se neuloží pravidlo](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Nasazení clusteru s odchozím typem UDR a Azure Firewall

K ilustraci aplikace clusteru s odchozím typem pomocí uživatelsky definované trasy můžete cluster nakonfigurovat ve virtuální síti s Azure Firewall ve vlastní podsíti. Podívejte se na tento příklad na [příkladu omezení odchozího provozu pomocí brány Azure firewall](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> Výstupní typ UDR vyžaduje trasu pro 0.0.0.0/0 a cíl dalšího segmentu směrování síťové virtuální zařízení (síťové virtuální zařízení) v tabulce směrování.
> Tabulka směrování už má výchozí hodnotu 0.0.0.0/0 pro Internet, bez veřejné IP adresy do SNAT jenom přidání této trasy vám neposkytne žádný výstup. AKS ověří, že nevytvoříte trasu 0.0.0.0/0 ukazující na Internet, ale místo síťové virtuální zařízení nebo brány atd. Při použití odchozího typu UDR není vytvořena veřejná IP adresa nástroje pro vyrovnávání zatížení pro **příchozí požadavky** , pokud není nakonfigurované služba typu *Vyrovnávání* zatížení. Veřejná IP adresa pro **odchozí požadavky** není nikdy vytvořena službou AKS, pokud je nastaven odchozí typ udr.

## <a name="next-steps"></a>Další kroky

Viz [Přehled služby Azure Networking udr](../virtual-network/virtual-networks-udr-overview.md).

Přečtěte si téma [jak vytvořit, změnit nebo odstranit směrovací tabulku](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
