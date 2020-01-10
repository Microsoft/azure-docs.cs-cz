---
title: Co je brána Azure Firewall?
description: Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 01/08/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 271a4f116b8b929e17c3a0379bef91d6b80e1490
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754450"
---
# <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jde o plně stavovou bránu firewall poskytovanou jako služba s integrovanou vysokou dostupností a neomezenou cloudovou škálovatelností.

![Přehled brány firewall](media/overview/firewall-threat.png)

Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě.  Služba je plně integrovaná se službou Azure Monitor zajišťující protokolování a analýzy.

Brána Azure Firewall nabízí následující funkce:

## <a name="built-in-high-availability"></a>Integrovaná vysoká dostupnost

Máte vestavěnou vysokou dostupnost, takže se nevyžadují žádné další nástroje pro vyrovnávání zatížení a nemusíte nic konfigurovat.

## <a name="availability-zones"></a>Zóny dostupnosti

Azure Firewall se dá nakonfigurovat během nasazení, aby se zvýšila dostupnost více Zóny dostupnosti. Díky Zóny dostupnosti se dostupnost zvyšuje až 99,99% doby provozu. Další informace najdete v tématu Azure Firewall [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Smlouva SLA o 99,99% provozu se nabízí, když jsou vybrané dvě nebo víc Zóny dostupnosti.

K určité zóně taky můžete přidružit Azure Firewall jenom z důvodů blízkosti, a to pomocí smlouvy SLA pro službu Standard 99,95%.

Pro bránu firewall, která je nasazená v zóně dostupnosti, se neúčtují žádné další náklady. U příchozích a odchozích přenosů dat spojených s Zóny dostupnosti však existují další náklady. Další informace najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall Zóny dostupnosti jsou k dispozici v oblastech, které podporují Zóny dostupnosti. Další informace najdete v tématu [co je zóny dostupnosti v Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Zóny dostupnosti lze nakonfigurovat pouze během nasazování. Existující bránu firewall nemůžete nakonfigurovat tak, aby zahrnovala Zóny dostupnosti.

Další informace o Zóny dostupnosti najdete v tématu [co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Neomezená cloudová škálovatelnost

Bránu Azure Firewall můžete vertikálně škálovat tak, jak to vyžadují změny v síťovém provozu, takže nemusíte platit za dimenzování podle špiček v datovém toku.

## <a name="application-fqdn-filtering-rules"></a>Pravidla filtrování plně kvalifikovaných názvů domén aplikací

Můžete omezit odchozí provoz HTTP/S nebo Azure SQL provoz (Preview) na zadaný seznam plně kvalifikovaných názvů domény (FQDN), včetně zástupných karet. Tato funkce nevyžaduje ukončení protokolu SSL.

## <a name="network-traffic-filtering-rules"></a>Pravidla filtrování síťového provozu

Můžete centrálně vytvořit pravidla pro *povolení* nebo *blokování* podle zdrojové a cílové IP adresy, portu a protokolu. Brána Azure Firewall je plně stavová, takže dokáže odlišit legitimní pakety pro různé typy spojení. Pravidla jsou vynucována a protokolována napříč různými předplatnými a virtuálními sítěmi.

## <a name="fqdn-tags"></a>Značky plně kvalifikovaných názvů domén

Značky plně kvalifikovaných názvů domén usnadňují povolení přenosů z dobře známé služby Azure prostřednictvím brány firewall. Řekněme například, že chcete povolit síťové přenosy z webu Windows Update přes bránu firewall. Můžete vytvořit pravidlo aplikace a zahrnout značku webu Windows Update. Teď je možný síťový přenos z webu Windows Update přes vaši bránu firewall.

## <a name="service-tags"></a>Značky služeb

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou zahrnuty v rámci značky. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

## <a name="threat-intelligence"></a>Inteligentní analýza hrozeb

Pro bránu firewall můžete povolit filtrování na základě analýzy hrozeb, které bude upozorňovat na provoz směřující z nebo do známých škodlivých IP adres nebo domén a odepírat takový provoz. Tyto IP adresy a domény se přebírají z informačního kanálu analýzy hrozeb Microsoftu.

## <a name="outbound-snat-support"></a>Podpora pro odchozí SNAT

Veškeré IP adresy pro odchozí provoz z virtuálních sítí se překládají na veřejnou IP adresu brány Azure Firewall na základě zdroje (SNAT). Můžete identifikovat a povolit provoz pocházející z vaší virtuální sítě do vzdálených internetových cílů. Azure Firewall nesnat, pokud je cílová IP adresa privátního rozsahu IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall na jednu z privátních IP adres firewallu v AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Podpora DNAT u příchozích přenosů

Příchozí síťový provoz na veřejnou IP adresu vaší brány firewall se překládá (překlad cílových adres) a filtruje na privátní IP adresy ve vašich virtuálních sítích.

## <a name="multiple-public-ip-addresses"></a>Několik veřejných IP adres

Pomocí brány firewall můžete přidružit několik veřejných IP adres (až 100).

To umožňuje následující scénáře:

- **DNAT** – můžete přeložit několik standardních instancí portů na servery back-end. Pokud máte například dvě veřejné IP adresy, můžete přeložit TCP port 3389 (RDP) na obě IP adresy.
- **SNAT** – pro odchozí připojení SNAT jsou k dispozici další porty, což snižuje potenciál vyčerpání portů SNAT. V tuto chvíli Azure Firewall náhodně vybere zdrojovou veřejnou IP adresu, která se má použít pro připojení. Pokud máte v síti filtrování pro příjem dat, je potřeba, abyste povolili všechny veřejné IP adresy přidružené k bráně firewall.

## <a name="azure-monitor-logging"></a>Protokolování Azure Monitor

Všechny události jsou integrované s Azure Monitor, což umožňuje archivaci protokolů do účtu úložiště, streamování událostí do centra událostí nebo jejich odeslání do protokolů Azure Monitor.

## <a name="pci-soc-and-iso-compliant"></a>PCI, SOC a ISO – kompatibilní

Azure Firewall je obor platebních karet (PCI), ovládací prvky SOC (Service Organization Controls) a kompatibilní s ISO (ISO). V současné době podporuje SOC 1 typ 2, SOC 2 Type 2, SOC 3, PCI DSS a ISO 27001, 27018, 20000-1, 22301, 9001, 27017.

Další informace najdete v tématu [Průvodce dodržováním předpisů Microsoftu](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide).

## <a name="known-issues"></a>Známé problémy

Brána Azure Firewall má následující známé problémy:

|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
Pravidla síťového filtrování pro jiné protokoly než TCP/UDP (třeba ICMP) nebudou fungovat pro provoz do internetu.|Pravidla síťového filtrování pro jiné protokoly než TCP/UDP nefungují s překladem SNAT na veřejnou IP adresu. Jiné protokoly než TCP/UDP jsou ale podporované mezi koncovými podsítěmi a virtuálními sítěmi.|Azure Firewall používá vyvažování zatížení úrovně Standard, [které v současnosti nepodporuje SNAT pro protokol IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Zkoumáme možnosti podpory tohoto scénáře v budoucí verzi.|
|Chybějící podpora PowerShellu a rozhraní příkazového řádku pro protokol ICMP|Azure PowerShell a rozhraní příkazového řádku nepodporují ICMP jako platný protokol v pravidlech sítě.|Protokol ICMP je stále možné používat prostřednictvím portálu a REST API. Pracujeme na přidání protokolu ICMP v PowerShellu a rozhraní příkazového řádku brzy.|
|Značky plně kvalifikovaného názvu domény vyžadují, aby byl nastavený protokol: port|Pravidla aplikací s značkami plně kvalifikovaného názvu domény vyžadují port: definice protokolu.|Jako hodnotu port: protokol můžete použít **https**. Pracujeme na tom, aby toto pole bylo volitelné při použití značek FQDN.|
|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje.|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje.|Podpora této funkce je naše mapa cest. Pokud chcete bránu firewall přesunout do jiné skupiny prostředků nebo předplatného, musíte odstranit aktuální instanci a znovu ji vytvořit v nové skupině prostředků nebo předplatném.|
|Výstrahy analýzy hrozeb se můžou maskovat.|Pravidla sítě s cílem 80/443 pro filtry odchozího filtrování upozorňující výstrahy, když jsou nakonfigurovaná jenom na režim výstrahy.|Vytvořte filtrování odchozího připojení pro 80/443 pomocí pravidel aplikací. Nebo změňte režim analýzy hrozeb na **Alert a Deny**.|
|Azure Firewall používá Azure DNS jenom pro překlad názvů.|Azure Firewall řeší plně kvalifikované názvy domény pouze pomocí Azure DNS. Vlastní server DNS není podporovaný. V ostatních podsítích není nijak ovlivněn překlad DNS.|Pracujeme na zmírnit toto omezení.|
|Azure Firewall SNAT/DNAT nefunguje pro cíle privátních IP adres|Azure Firewall podpora SNAT/DNAT je omezená na internetovou komunikaci a příchozí přenos dat. SNAT/DNAT aktuálně nefunguje pro cíle privátních IP adres. Například paprskový na paprskový.|Toto je aktuální omezení.|
|První konfiguraci veřejné IP adresy nejde odebrat.|Každá Azure Firewall veřejná IP adresa je přiřazena ke *konfiguraci protokolu IP*.  Při nasazení brány firewall se přiřadí první konfigurace IP adresy a obvykle obsahuje odkaz na podsíť brány firewall (Pokud není explicitně nakonfigurovaný přes nasazení šablony). Tuto konfiguraci protokolu IP nemůžete odstranit, protože by to vedlo ke zrušení přidělení brány firewall. Pokud má brána firewall k dispozici alespoň jednu jinou veřejnou IP adresu, můžete i nadále změnit nebo odebrat veřejnou IP adresu přidruženou k této konfiguraci protokolu IP.|Toto chování je úmyslné.|
|Zóny dostupnosti se dají konfigurovat jenom během nasazování.|Zóny dostupnosti se dají konfigurovat jenom během nasazování. Po nasazení brány firewall nemůžete Zóny dostupnosti nakonfigurovat.|Toto chování je úmyslné.|
|SNAT při příchozích připojeních|Kromě DNAT jsou připojení přes veřejnou IP adresu (příchozí) brány firewall před jejich vstupem na jednu z privátních IP adres brány firewall. Tento požadavek dnes (také pro aktivní/aktivní síťová virtuální zařízení) zajistíte tak, aby se zajistilo symetrické směrování.|Pokud chcete zachovat původní zdroj pro HTTP/S, zvažte použití hlaviček [xff](https://en.wikipedia.org/wiki/X-Forwarded-For) . Například před bránou firewall použijte službu, jako je například [přední vrátka Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) . WAF můžete také přidat jako součást služby Azure front-dveří a řetězit k bráně firewall.
|Podpora filtrování plně kvalifikovaného názvu domény SQL pouze v režimu proxy (port 1433)|Pro Azure SQL Database, Azure SQL Data Warehouse a Azure SQL Managed instance:<br><br>V průběhu verze Preview se filtrování plně kvalifikovaného názvu domény SQL podporuje jenom v režimu proxy serveru (port 1433).<br><br>Pro Azure SQL IaaS:<br><br>Pokud používáte nestandardní porty, můžete tyto porty zadat v pravidlech aplikací.|V případě SQL v režimu přesměrování, který je ve výchozím nastavení při připojování z Azure, můžete místo toho použít filtr přístupu pomocí značky služby SQL jako součást Azure Firewallch síťových pravidel.
|Odchozí provoz na portu TCP 25 není povolený.| Odchozí připojení SMTP, která používají port TCP 25, jsou blokovaná. Port 25 se primárně používá pro neověřené doručování e-mailů. Toto je výchozí chování platformy pro virtuální počítače. Další informace najdete v tématu řešení potíží s [odchozím připojením SMTP v Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Na rozdíl od virtuálních počítačů ale tuto funkci v tuto chvíli nemůžete povolit na Azure Firewall.|Použijte doporučený postup k odeslání e-mailu, jak je uvedeno v článku věnovaném odstraňování potíží SMTP. Případně vylučte virtuální počítač, který potřebuje odchozí přístup SMTP z výchozí trasy k bráně firewall, a místo toho nakonfigurujte odchozí přístup přímo na Internet.

## <a name="next-steps"></a>Další kroky

- [Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal](tutorial-firewall-deploy-portal.md)
- [Nasazení brány Azure Firewall pomocí šablony](deploy-template.md)
- [Vytvoření testovacího prostředí brány Azure Firewall](scripts/sample-create-firewall-test.md)
