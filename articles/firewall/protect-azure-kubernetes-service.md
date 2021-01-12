---
title: Zajištění ochrany nasazení služby Azure Kubernetes Service (AKS) s využitím služby Azure Firewall
description: Naučte se používat Azure Firewall k ochraně nasazení AKS (Azure Kubernetes Service).
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: 51af9ff4972f5edef02426a6e81e8582123c9a7a
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107850"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Zajištění ochrany nasazení služby Azure Kubernetes Service (AKS) s využitím služby Azure Firewall

Služba Azure Kubernetes Service (AKS) nabízí spravovaný cluster Kubernetes v Azure. Snižuje složitost a provozní režii při správě Kubernetes tím, že přenese velkou část této zodpovědnosti do Azure. AKS zpracovává kritické úlohy, jako je monitorování stavu a údržba, a poskytuje cluster na podnikové úrovni a zabezpečený cluster s usnadněním správného řízení.

Kubernetes orchestruje clustery virtuálních počítačů a kontejnerů plánů ke spuštění na těchto virtuálních počítačích na základě jejich dostupných výpočetních prostředků a požadavků na prostředky každého kontejneru. Kontejnery se seskupují do lusků, základní provozní jednotka pro Kubernetes a ta v luskech se škálují do stavu, který chcete.

Pro účely správy a provozu musí uzly v clusteru AKS přistupovat k určitým portům a plně kvalifikovaným názvům domén (FQDN). Tyto akce můžou být pro komunikaci se serverem rozhraní API nebo ke stažení a instalaci základních součástí clusteru Kubernetes a aktualizací zabezpečení uzlů. Azure Firewall vám může pomáhat uzamknout prostředí a filtrovat odchozí provoz.

Postupujte podle pokynů v tomto článku a poskytněte vám dodatečnou ochranu pro cluster Azure Kubernetes pomocí Azure Firewall.

## <a name="prerequisites"></a>Požadavky

- Nasazený cluster Azure Kubernetes se spuštěnou aplikací.

   Další informace najdete v tématu [kurz: nasazení clusteru Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) a [kurzu: spouštění aplikací ve službě Azure KUBERNETES Service (AKS)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>Zabezpečení AKS

Azure Firewall poskytuje značku plně kvalifikovaného názvu domény AKS pro zjednodušení konfigurace. Pro povolení odchozího provozu platformy AKS použijte následující postup:

- Když použijete Azure Firewall k omezení odchozího provozu a vytvoření trasy definované uživatelem (UDR) pro směrování veškerého odchozího provozu, ujistěte se, že jste v bráně firewall vytvořili příslušné pravidlo DNAT pro správné povolení příchozího provozu. 

   Použití Azure Firewall s UDR ukončí příchozí instalaci z důvodu asymetrického směrování. K tomuto problému dochází, pokud má podsíť AKS výchozí trasu, která přechází na privátní IP adresu brány firewall, ale používáte veřejný Nástroj pro vyrovnávání zatížení. Například příchozí nebo Kubernetes služba typu *Vyrovnávání zatížení*.

   V tomto případě se příchozí provoz nástroje pro vyrovnávání zatížení přijímá prostřednictvím veřejné IP adresy, ale návratová cesta prochází přes privátní IP adresu brány firewall. Vzhledem k tomu, že brána firewall je stavová, dojde k vrácení vráceného paketu, protože brána firewall neví o zavedené relaci. Informace o tom, jak integrovat Azure Firewall s vaším nástrojem pro vyrovnávání zatížení pro příchozí přenosy nebo služby, najdete v tématu věnovaném [integraci Azure firewall s Azure Standard Load Balancer](integrate-lb.md).
- Vytvořte kolekci pravidel aplikace a přidejte pravidlo, které povolí značku *AzureKubernetesService* plně kvalifikovaného názvu domény. Zdrojový rozsah IP adres je virtuální síť fondu hostitelů, protokol je https a cíl je AzureKubernetesService.
- Pro cluster AKS se vyžadují následující Odchozí porty nebo Síťová pravidla:

   - Port 443 protokolu TCP
   - TCP [*IPAddrOfYourAPIServer*]: 443 se vyžaduje v případě, že máte aplikaci, která potřebuje komunikovat se serverem API. Tuto změnu lze nastavit po vytvoření clusteru.
   - Port TCP 9000 a port UDP 1194 pro front-endu tunelu pro komunikaci s koncovým tunelovým zakončením na serveru rozhraní API.

      Chcete-li být konkrétnější, přečtěte si adresy v následující tabulce:

   | Cílový koncový bod                                                             | Protokol | Port    | Použití  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Ani* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Ani* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Ani* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. |
   | **`*:9000`** <br/> *Ani* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Ani* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Ani* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. |

   - Port UDP 123 pro synchronizaci času NTP (Network Time Protocol) (uzly Linux).
   - Port UDP 53 pro DNS se vyžaduje i v případě, že máte k dispozici přímý přístup k serveru rozhraní API.

   Další informace najdete v tématu [řízení odchozího provozu pro uzly clusteru ve službě Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md).
- Nakonfigurujte AzureMonitor a značky služby úložiště. Azure Monitor přijímá data Log Analytics.

   Adresu URL pracovního prostoru můžete také nechat individuálně: `<worksapceguid>.ods.opinsights.azure.com` a `<worksapceguid>.oms.opinsights.azure.com` . Můžete to vyřešit jedním z následujících způsobů:

    - Povolte přístup HTTPS z podsítě fondu hostitelů do `*. ods.opinsights.azure.com` a `*.oms. opinsights.azure.com` . Tyto plně kvalifikované názvy domén zástupných znaků umožňují požadovaný přístup, ale jsou méně omezující.
    - Pomocí následujícího dotazu Log Analytics můžete zobrazit seznam přesně požadovaných plně kvalifikovaných názvů domén a pak je v pravidlech aplikací brány firewall výslovně zapnout:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Další kroky

- Další informace o službě Azure Kubernetes najdete v tématu [Kubernetes Core koncepty pro Azure Kubernetes Service (AKS)](../aks/concepts-clusters-workloads.md).
