---
title: Co je brána Azure Firewall?
description: Přečtěte si více o vlastnostech brány Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 11/28/2018
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: b01e16f85eaccee4d765b4985769df670d4d35ca
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413026"
---
# <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jde o plně stavovou bránu firewall poskytovanou jako služba s integrovanou vysokou dostupností a neomezenou cloudovou škálovatelností. 

![Přehled brány firewall](media/overview/firewall-overview.png)

Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě.  Služba je plně integrovaná se službou Azure Monitor zajišťující protokolování a analýzy.

## <a name="features"></a>Funkce

Brána Azure Firewall nabízí následující funkce:

### <a name="built-in-high-availability"></a>Integrovaná vysoká dostupnost
Vysoká dostupnost je přímo součástí návrhu, takže není nutné žádné dodatečné vyrovnávání zatížení ani konfigurace.

### <a name="unrestricted-cloud-scalability"></a>Neomezená cloudová škálovatelnost 
Bránu Azure Firewall můžete vertikálně škálovat tak, jak to vyžadují změny v síťovém provozu, takže nemusíte platit za dimenzování podle špiček v datovém toku.

### <a name="application-fqdn-filtering-rules"></a>Pravidla filtrování plně kvalifikovaných názvů domén aplikací

Odchozí přenosy HTTP/S můžete omezit na zadaný seznam plně kvalifikovaných názvů domén (FQDN) včetně zástupných znaků. Tato funkce nevyžaduje ukončení protokolu SSL.

### <a name="network-traffic-filtering-rules"></a>Pravidla filtrování síťového provozu

Můžete centrálně vytvořit pravidla pro *povolení* nebo *blokování* podle zdrojové a cílové IP adresy, portu a protokolu. Brána Azure Firewall je plně stavová, takže dokáže odlišit legitimní pakety pro různé typy spojení. Pravidla jsou vynucována a protokolována napříč různými předplatnými a virtuálními sítěmi.

### <a name="fqdn-tags"></a>Značky plně kvalifikovaných názvů domén

Značky plně kvalifikovaných názvů domén usnadňují povolení přenosů z dobře známé služby Azure prostřednictvím brány firewall. Řekněme například, že chcete povolit síťové přenosy z webu Windows Update přes bránu firewall. Můžete vytvořit pravidlo aplikace a zahrnout značku webu Windows Update. Teď je možný síťový přenos z webu Windows Update přes vaši bránu firewall.

### <a name="outbound-snat-support"></a>Podpora pro odchozí SNAT

Veškeré IP adresy pro odchozí provoz z virtuálních sítí se překládají na veřejnou IP adresu brány Azure Firewall na základě zdroje (SNAT). Můžete identifikovat a povolit provoz pocházející z vaší virtuální sítě do vzdálených internetových cílů.

### <a name="inbound-dnat-support"></a>Podpora DNAT u příchozích přenosů

Příchozí síťový provoz na veřejnou IP adresu vaší brány firewall se překládá (překlad cílových adres) a filtruje na privátní IP adresy ve vašich virtuálních sítích. 

### <a name="azure-monitor-logging"></a>Protokolování Azure Monitor

Všechny události jsou zaznamenávány službou Azure Monitor a díky tomu je možné archivovat protokoly do účtu úložiště, streamovat události do centra událostí nebo je odesílat do služby Log Analytics.

## <a name="known-issues"></a>Známé problémy

Brána Azure Firewall má následující známé problémy:


|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
|Konflikt s funkcí Just-in-Time (JIT) služby Azure Security Center (ASC)|Pokud se k virtuálnímu počítači přistupuje metodou JIT a je v podsíti s uživatelem definovanou trasou, která odkazuje na Azure Firewall jako na výchozí bránu, nebude ASC JIT fungovat. To je důsledkem asymetrického směrování – paket přichází přes veřejnou IP adresu virtuálního počítače (JIT otevřel přístup), ale návratový paket odchází přes bránu firewall, která ho zahodí, protože v bráně firewall nebyla otevřena žádná relace.|Tento problém odstraníte tak, že umístíte virtuální počítače s JIT do samostatné podsítě, která nemá uživatelem definovanou trasu do firewallu.|
|Hvězdicová architektura s globálním peeringem se nepodporuje|Používáte hvězdicovou architekturu, kdy jsou rozbočovač a brána firewall nasazené v jedné oblasti Azure a koncové body v jiné oblasti Azure. Připojení k rozbočovači prostřednictvím globálního VNET peeringu se nepodporují.|Toto chování je úmyslné. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md#azure-firewall-limits).|
Pravidla síťového filtrování pro jiné protokoly než TCP/UDP (třeba ICMP) nebudou fungovat pro provoz do internetu.|Pravidla síťového filtrování pro jiné protokoly než TCP/UDP nefungují s překladem SNAT na veřejnou IP adresu. Jiné protokoly než TCP/UDP jsou ale podporované mezi koncovými podsítěmi a virtuálními sítěmi.|Azure Firewall používá vyvažování zatížení úrovně Standard, [které v současnosti nepodporuje SNAT pro protokol IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Zkoumáme možnosti, jak podporu tohoto scénáře zahrnout do budoucích verzí.|
|Chybějící podpora PowerShellu a rozhraní příkazového řádku pro protokol ICMP|Azure PowerShell a rozhraní příkazového řádku nepodporují ICMP jako platný protokol v pravidlech sítě.|ICMP můžete pořád používat jako protokol prostřednictvím portálu a rozhraní REST API. Pracujeme na brzkém přidání protokolu ICMP do PowerShellu a rozhraní příkazového řádku.|
|Značky plně kvalifikovaného názvu domény vyžadují, aby byl nastavený protokol: port|Pravidla aplikace se značkami plně kvalifikovaného názvu domény vyžadují definici port:protokol.|Jako hodnotu port: protokol můžete použít **https**. Pracujeme na tom, aby toto pole bylo při použití značek plně kvalifikovaného názvu domény nepovinné.|
|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného není podporované.|Podpora této funkce se na náš podrobný popis. Pokud chcete bránu firewall přesunout do jiné skupiny prostředků nebo předplatného, musíte odstranit aktuální instanci a znovu ji vytvořit v nové skupině prostředků nebo předplatném.|
|Rozsah portů v pravidlech sítě a aplikace|Porty s vysokou jsou vyhrazené pro správu a stavu jsou omezená na 64 000 portů sondy. |Pracujeme na toto omezení zmírnit.|
|

## <a name="next-steps"></a>Další postup

- [Kurz: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md)
- [Nasazení brány Azure Firewall pomocí šablony](deploy-template.md)
- [Vytvoření testovacího prostředí brány Azure Firewall](scripts/sample-create-firewall-test.md)

