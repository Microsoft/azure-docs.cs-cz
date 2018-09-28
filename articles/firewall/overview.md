---
title: Co je brána Azure Firewall?
description: Přečtěte si více o vlastnostech brány Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 2961f6cc8607ba7ec670b297a1858bf433c3ec89
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960783"
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
|Hvězdicová architektura s globálním peeringem nefunguje|Hvězdicová architektura, kdy jsou rozbočovač a brána firewall nasazené v jedné oblasti Azure a koncové body připojené k rozbočovači prostřednictvím globálního peeringu VNet jsou v jiné oblasti, není podporovaná.|Další informace najdete v tématu [Vytvoření, změna nebo odstranění peeringu virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints).|
Pravidla síťového filtrování pro jiné protokoly než TCP/UDP (třeba ICMP) nebudou fungovat pro provoz do internetu.|Pravidla síťového filtrování pro jiné protokoly než TCP/UDP nefungují s překladem SNAT na veřejnou IP adresu. Jiné protokoly než TCP/UDP jsou ale podporované mezi koncovými podsítěmi a virtuálními sítěmi.|Azure Firewall používá vyvažování zatížení úrovně Standard, [které v současnosti nepodporuje SNAT pro protokol IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Zkoumáme možnosti, jak podporu tohoto scénáře zahrnout do budoucích verzí.|
|Určení NAT (DNAT) nefunguje pro port 80 a 22.|Pole Cílový port v kolekci pravidel NAT nemůže obsahovat port 80 nebo 22.|Pracujeme na tom, abychom to v blízké budoucnosti opravili. Do té doby použijte jako cílový port v pravidlech NAT jakýkoliv jiný port. Port 80 nebo 22 může i nadále sloužit jako překládaný port (například můžete namapovat veřejnou adresu ip:81 na privátní ip:80).|
|

## <a name="next-steps"></a>Další kroky

- [Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal](tutorial-firewall-deploy-portal.md)
- [Nasazení brány Azure Firewall pomocí šablony](deploy-template.md)
- [Vytvoření testovacího prostředí brány Azure Firewall](scripts/sample-create-firewall-test.md)

