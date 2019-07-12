---
title: Co je brána Azure Firewall?
description: Přečtěte si více o vlastnostech brány Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 7/10/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: da82f6c93045b38aed887860c6d5c45c93b2260b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703955"
---
# <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Je plně stavová brána firewall jako služba s integrovanou vysokou dostupnost a škálovatelnost cloudu neomezený.

![Přehled brány firewall](media/overview/firewall-threat.png)

Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě.  Služba je plně integrovaná se službou Azure Monitor zajišťující protokolování a analýzy.

Brána Azure Firewall nabízí následující funkce:

## <a name="built-in-high-availability"></a>Integrovaná vysoká dostupnost

Vysoká dostupnost je součástí, takže žádné služby Vyrovnávání zatížení další požadované a není nic, které je potřeba nakonfigurovat.

## <a name="availability-zones"></a>Zóny dostupnosti

Během nasazování rozložit několika zónami dostupnosti pro zajištění vyšší dostupnosti je možné nakonfigurovat brány Firewall na Azure. Díky zónám dostupnosti vaší dostupnost zvýší na 99,99 % dostupnost. Další informace najdete v tématu Brána Firewall Azure [smlouva o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99 % dostupnosti smlouvy SLA se nabízí, když vyberete dva nebo více zónách dostupnosti.

Brána Firewall služby Azure můžete také přiřadit ke konkrétní zóně pouze z důvodů blízkosti pomocí služeb standard 99,95 % SLA.

Se neúčtují žádné další poplatky pro bránu firewall nasazeného v zóně dostupnosti. Existují však další náklady pro příchozí a odchozí datové přenosy spojené se zónami dostupnosti. Další informace najdete v tématu [podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Zóny dostupnosti Azure brány Firewall jsou dostupné v oblastech, které podporují zóny dostupnosti. Další informace najdete v tématu [co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Zóny dostupnosti se dá nakonfigurovat jenom během nasazení. Nemůžete nakonfigurovat stávající brány firewall zahrnout zóny dostupnosti.

Další informace o zónách dostupnosti najdete v tématu [co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Neomezená cloudová škálovatelnost

Bránu Azure Firewall můžete vertikálně škálovat tak, jak to vyžadují změny v síťovém provozu, takže nemusíte platit za dimenzování podle špiček v datovém toku.

## <a name="application-fqdn-filtering-rules"></a>Pravidla filtrování plně kvalifikovaných názvů domén aplikací

Můžete omezit odchozí přenosy HTTP/S nebo provoz Azure SQL (preview) na zadaného seznamu plně kvalifikované názvy domény (FQDN) včetně zástupné znaky. Tato funkce nevyžaduje ukončení protokolu SSL.

## <a name="network-traffic-filtering-rules"></a>Pravidla filtrování síťového provozu

Můžete centrálně vytvořit pravidla pro *povolení* nebo *blokování* podle zdrojové a cílové IP adresy, portu a protokolu. Brána Azure Firewall je plně stavová, takže dokáže odlišit legitimní pakety pro různé typy spojení. Pravidla jsou vynucována a protokolována napříč různými předplatnými a virtuálními sítěmi.

## <a name="fqdn-tags"></a>Značky plně kvalifikovaných názvů domén

Značky plně kvalifikovaných názvů domén usnadňují povolení přenosů z dobře známé služby Azure prostřednictvím brány firewall. Řekněme například, že chcete povolit síťové přenosy z webu Windows Update přes bránu firewall. Můžete vytvořit pravidlo aplikace a zahrnout značku webu Windows Update. Teď je možný síťový přenos z webu Windows Update přes vaši bránu firewall.

## <a name="service-tags"></a>Značky služeb

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nelze vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

## <a name="threat-intelligence"></a>Analýza hrozeb

Pro bránu firewall můžete povolit filtrování na základě analýzy hrozeb, které bude upozorňovat na provoz směřující z nebo do známých škodlivých IP adres nebo domén a odepírat takový provoz. Tyto IP adresy a domény se přebírají z informačního kanálu analýzy hrozeb Microsoftu.

## <a name="outbound-snat-support"></a>Podpora pro odchozí SNAT

Veškeré IP adresy pro odchozí provoz z virtuálních sítí se překládají na veřejnou IP adresu brány Azure Firewall na základě zdroje (SNAT). Můžete identifikovat a povolit provoz pocházející z vaší virtuální sítě do vzdálených internetových cílů. Brány Firewall na Azure není SNAT, pokud cílová IP adresa je rozsah privátních IP za [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Pokud vaše organizace používá veřejnou rozsah IP adres pro privátní sítě, brána Firewall služby Azure se provoz do jedné z brány firewall na privátní IP adresy v AzureFirewallSubnet SNAT.

## <a name="inbound-dnat-support"></a>Podpora DNAT u příchozích přenosů

Příchozí síťový provoz na veřejnou IP adresu vaší brány firewall se překládá (překlad cílových adres) a filtruje na privátní IP adresy ve vašich virtuálních sítích.

## <a name="multiple-public-ip-addresses"></a>Několik veřejných IP adres

> [!IMPORTANT]
> Brány Azure s víc veřejných IP adres je k dispozici prostřednictvím Azure Powershellu, rozhraní příkazového řádku Azure, rozhraní REST a šablony. Portálu uživatelského rozhraní je přidáte do oblastí postupně a budou k dispozici ve všech oblastech po dokončení tohoto uvedení.


Můžete přiřadit víc veřejných IP adres (až 100) s bránou firewall.

To umožňuje používat následující scénáře:

- **DNAT** -více instancí standardní port lze přeložit do back-end serverů. Například pokud máte dvě veřejné IP adresy, může překládat TCP port 3389 (RDP) pro obě IP adresy.
- **SNAT** – další porty jsou k dispozici pro odchozí připojení SNAT, snižuje riziko vyčerpání portů SNAT. Brána Firewall služby Azure v tuto chvíli náhodně vybere zdroj veřejnou IP adresu pro připojení. Pokud máte jakékoli podřízené filtrování ve vaší síti, budete muset povolit všechny veřejné IP adresy přidružené k vaší brány firewall.

## <a name="azure-monitor-logging"></a>Protokolování Azure Monitor

Všechny události jsou integrované s Azure Monitor, abyste mohli archivovat do účtu úložiště, datový proud událostí do centra událostí, protokoly nebo odeslat protokoly Azure monitoru.

## <a name="known-issues"></a>Známé problémy

Brána Azure Firewall má následující známé problémy:

|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
|Konflikt s funkcí Just-in-Time (JIT) služby Azure Security Center (ASC)|Pokud se k virtuálnímu počítači přistupuje metodou JIT a je v podsíti s uživatelem definovanou trasou, která odkazuje na Azure Firewall jako na výchozí bránu, nebude ASC JIT fungovat. To je výsledkem asymetrického směrování – paket, který je k dispozici ve přes veřejnou IP adresu virtuálního počítače (JIT otevřen přístup), ale návratový cesty je přes bránu firewall, která zruší paket, protože neexistuje žádný navázanou relaci v bráně firewall.|Tento problém odstraníte tak, že umístíte virtuální počítače s JIT do samostatné podsítě, která nemá uživatelem definovanou trasu do firewallu.|
Pravidla síťového filtrování pro jiné protokoly než TCP/UDP (třeba ICMP) nebudou fungovat pro provoz do internetu.|Pravidla síťového filtrování pro jiné protokoly než TCP/UDP nefungují s překladem SNAT na veřejnou IP adresu. Jiné protokoly než TCP/UDP jsou ale podporované mezi koncovými podsítěmi a virtuálními sítěmi.|Azure Firewall používá vyvažování zatížení úrovně Standard, [které v současnosti nepodporuje SNAT pro protokol IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Zkoumání jsme možnosti pro podporu tohoto scénáře v budoucí verzi.|
|Chybějící podpora PowerShellu a rozhraní příkazového řádku pro protokol ICMP|Azure PowerShell a rozhraní příkazového řádku nepodporují ICMP jako platný protokol v pravidlech sítě.|Je stále možné používat protokol ICMP jako protokol prostřednictvím portálu a rozhraní REST API. Pracujeme na přidání ICMP brzy v prostředí PowerShell a rozhraní příkazového řádku.|
|Značky plně kvalifikovaného názvu domény vyžadují, aby byl nastavený protokol: port|Pravidla aplikace se značkami plně kvalifikovaný název domény, vyžadují port: protokol definice.|Jako hodnotu port: protokol můžete použít **https**. Pracujeme na nastavit toto pole jako volitelný zadáním plně kvalifikovaného názvu domény značky.|
|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje.|Podpora této funkce se na náš podrobný popis. Pokud chcete bránu firewall přesunout do jiné skupiny prostředků nebo předplatného, musíte odstranit aktuální instanci a znovu ji vytvořit v nové skupině prostředků nebo předplatném.|
|Rozsah portů v pravidlech sítě a aplikace|Porty s vysokou jsou vyhrazené pro správu a stavu jsou omezená na 64 000 portů sondy. |Pracujeme na toto omezení zmírnit.|
|Upozornění na hrozby intelligence může získat zakryté hvězdičkami|Pravidla sítě s cílem 80 a 443 pro odchozí filtrování masky hrozeb intelligence výstrahy, když se nakonfigurovaný tak, aby režimu jen pro výstrahy.|Vytvoření výstupní filtrování 80/443 pomocí pravidel pro aplikace. Nebo změňte režim threat intelligence **výstrahy a Odepřít**.|
|Azure Brána Firewall používá pouze pro překlad názvů Azure DNS|Brány Firewall Azure řeší plně kvalifikované názvy domény pouze pomocí služby Azure DNS. Vlastní server DNS se nepodporuje. Neexistuje žádný vliv na překlad názvů DNS v jiných podsítích.|Pracujeme na toto omezení zmírnit.|
|Azure bránu Firewall SNAT/DNAT nefunguje pro privátní IP cíle|Podpora brány Firewall SNAT/DNAT Azure je omezený na příchozí a výchozí přenos dat sítě Internet. SNAT/DNAT aktuálně nefunguje pro privátní IP cíle. Například paprsků do paprsku.|Jedná se o aktuální omezení.|
|První konfigurace veřejné IP adresy nejde odebrat.|Každý veřejnou IP adresu brány Firewall Azure je přiřazená *konfigurace IP adresy*.  První konfigurace IP adresy se přiřadí při nasazení brány firewall a obvykle také obsahuje odkaz na podsíť brány firewall (Pokud explicitně nakonfigurovat jinak než pomocí šablony nasazení). Tato konfigurace protokolu IP nejde odstranit, protože by zrušení přidělení bránu firewall. Stále můžete změnit nebo odebrat veřejnou IP adresu, pokud brána firewall nemá alespoň jeden další veřejnou IP adresu k dispozici pro použití spojené s touto konfigurací IP.|Jedná se o účel.|
|Zóny dostupnosti se dá nakonfigurovat jenom během nasazení.|Zóny dostupnosti se dá nakonfigurovat jenom během nasazení. Zóny dostupnosti nelze nakonfigurovat po nasazení brány firewall.|Jedná se o účel.|
|SNAT u příchozích připojení|Kromě DNAT, připojení přes veřejnou IP adresu brány firewall (příchozí) jsou přeložené pomocí překladu SNAT na jednu bránu firewall privátní IP adresy. Tento požadavek dnes (také pro síťová virtuální zařízení aktivní/aktivní) k zajištění symetrické směrování.|Pokud chcete zachovat původní zdroj pro HTTP/S, zvažte použití [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) záhlaví. Například použít službu [Azure branou](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) před branou firewall. WAF jako součást Azure branou a řetězce můžete také přidat do brány firewall.
|Podpora pouze v režimu proxy (port 1433) filtrování SQL v plně kvalifikovaný název domény|Pro službu Azure SQL Database spravované Instance Azure SQL Data Warehouse a Azure SQL:<br><br>Ve verzi preview se podporuje filtrování SQL v plně kvalifikovaný název domény v režimu serveru proxy jenom (port 1433).<br><br>Pro Azure SQL IaaS:<br><br>Pokud používáte nestandardních portech, můžete zadat tyto porty v pravidlech aplikace.|Pro server SQL v režimu přesměrování, což je výchozí, pokud se připojujete z Azure, můžete místo toho filtrovat přístupu pomocí pravidel sítě Brána Firewall služby Azure v rámci značka služby SQL.

## <a name="next-steps"></a>Další postup

- [Kurz: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md)
- [Nasazení brány Azure Firewall pomocí šablony](deploy-template.md)
- [Vytvoření testovacího prostředí brány Azure Firewall](scripts/sample-create-firewall-test.md)
