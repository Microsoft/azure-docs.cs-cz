---
title: Co je brána Azure Firewall?
description: Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 05/19/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: b54e8efc4f5f22a89526bb5d529805b33371529f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655120"
---
# <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

![Certifikace ICSA](media/overview/icsa-cert-firewall-small.png)

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu.

![Přehled brány firewall](media/overview/firewall-threat.png)

Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě.  Služba je plně integrovaná se službou Azure Monitor zajišťující protokolování a analýzy.

Brána Azure Firewall nabízí následující funkce:

## <a name="built-in-high-availability"></a>Integrovaná vysoká dostupnost

Máte vestavěnou vysokou dostupnost, takže se nevyžadují žádné další nástroje pro vyrovnávání zatížení a nemusíte nic konfigurovat.

## <a name="availability-zones"></a>Zóny dostupnosti

Azure Firewall se dá nakonfigurovat během nasazení, aby se zvýšila dostupnost více Zóny dostupnosti. Díky Zóny dostupnosti se dostupnost zvyšuje až 99,99% doby provozu. Další informace najdete v tématu Azure Firewall [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Smlouva SLA o 99,99% provozu se nabízí, když jsou vybrané dvě nebo víc Zóny dostupnosti.

K určité zóně taky můžete přidružit Azure Firewall jenom z důvodů blízkosti, a to pomocí smlouvy SLA pro službu Standard 99,95%.

Pro bránu firewall, která je nasazená v zóně dostupnosti, se neúčtují žádné další náklady. U příchozích a odchozích přenosů dat spojených s Zóny dostupnosti však existují další náklady. Další informace najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall Zóny dostupnosti jsou k dispozici v oblastech, které podporují Zóny dostupnosti. Další informace najdete v tématu [oblasti, které podporují zóny dostupnosti v Azure](../availability-zones/az-region.md) .

> [!NOTE]
> Zóny dostupnosti lze nakonfigurovat pouze během nasazování. Existující bránu firewall nemůžete nakonfigurovat tak, aby zahrnovala Zóny dostupnosti.

Další informace o Zóny dostupnosti najdete v tématu [oblasti a zóny dostupnosti v Azure](../availability-zones/az-overview.md) .

## <a name="unrestricted-cloud-scalability"></a>Neomezená cloudová škálovatelnost

Bránu Azure Firewall můžete vertikálně škálovat tak, jak to vyžadují změny v síťovém provozu, takže nemusíte platit za dimenzování podle špiček v datovém toku.

## <a name="application-fqdn-filtering-rules"></a>Pravidla filtrování plně kvalifikovaných názvů domén aplikací

Můžete omezit odchozí provoz HTTP/S nebo Azure SQL provoz (Preview) na zadaný seznam plně kvalifikovaných názvů domény (FQDN), včetně zástupných karet. Tato funkce nevyžaduje ukončení protokolu TLS.

## <a name="network-traffic-filtering-rules"></a>Pravidla filtrování síťového provozu

Můžete centrálně vytvořit pravidla pro *povolení* nebo *blokování* podle zdrojové a cílové IP adresy, portu a protokolu. Brána Azure Firewall je plně stavová, takže dokáže odlišit legitimní pakety pro různé typy spojení. Pravidla jsou vynucována a protokolována napříč různými předplatnými a virtuálními sítěmi.

## <a name="fqdn-tags"></a>Značky plně kvalifikovaných názvů domén

Plně kvalifikovaný název domény usnadňuje povolení známého síťového provozu služby Azure přes bránu firewall. Řekněme například, že chcete povolit síťové přenosy z webu Windows Update přes bránu firewall. Můžete vytvořit pravidlo aplikace a zahrnout značku webu Windows Update. Teď je možný síťový přenos z webu Windows Update přes vaši bránu firewall.

## <a name="service-tags"></a>Značky služeb

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou zahrnuty v rámci značky. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

## <a name="threat-intelligence"></a>Analýza hrozeb

Filtrování na základě logiky hrozeb lze povolit pro bránu firewall pro upozornění a zamítnutí provozu z/do známých škodlivých IP adres a domén. IP adresy a domény se naúčtují z informačního kanálu Microsoft Threat Intelligence.

## <a name="outbound-snat-support"></a>Podpora pro odchozí SNAT

Veškeré IP adresy pro odchozí provoz z virtuálních sítí se překládají na veřejnou IP adresu brány Azure Firewall na základě zdroje (SNAT). Můžete identifikovat a povolit provoz pocházející z vaší virtuální sítě do vzdálených internetových cílů. Azure Firewall nesnat, pokud je cílová IP adresa privátního rozsahu IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall na jednu z privátních IP adres firewallu v AzureFirewallSubnet. Azure Firewall **můžete nakonfigurovat tak,** aby nesnat na svůj rozsah veřejných IP adres. Další informace najdete v tématu [Azure firewall rozsahy privátních IP adres SNAT](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Podpora DNAT u příchozích přenosů

Příchozí internetový síťový provoz na veřejnou IP adresu brány firewall se přeloží (překlad cílové síťové adresy) a filtruje na privátní IP adresy ve virtuálních sítích.

## <a name="multiple-public-ip-addresses"></a>Několik veřejných IP adres

Pomocí brány firewall můžete přidružit několik veřejných IP adres (až 250).

To umožňuje následující scénáře:

- **DNAT** – můžete přeložit několik standardních instancí portů na servery back-end. Pokud máte například dvě veřejné IP adresy, můžete přeložit TCP port 3389 (RDP) na obě IP adresy.
- **SNAT** – pro odchozí připojení SNAT jsou k dispozici další porty, což snižuje potenciál vyčerpání portů SNAT. V tuto chvíli Azure Firewall náhodně vybere zdrojovou veřejnou IP adresu, která se má použít pro připojení. Pokud máte v síti filtrování pro příjem dat, je potřeba, abyste povolili všechny veřejné IP adresy přidružené k bráně firewall. Pro zjednodušení této konfigurace zvažte použití [předpony veřejných IP adres](../virtual-network/public-ip-address-prefix.md) .

## <a name="azure-monitor-logging"></a>Protokolování Azure Monitor

Všechny události jsou integrované s Azure Monitor, což umožňuje archivaci protokolů do účtu úložiště, streamování událostí do centra událostí nebo jejich odeslání do protokolů Azure Monitor.

## <a name="forced-tunneling"></a>Vynucené tunelování

Azure Firewall můžete nakonfigurovat tak, aby směroval veškerý provoz vázaný na Internet na určený další segment směrování a nemuseli jít přímo na Internet. Můžete mít například místní hraniční bránu firewall nebo jiné síťové virtuální zařízení (síťové virtuální zařízení) pro zpracování síťového provozu před předáním na Internet. Další informace najdete v tématu [Azure firewall vynucené tunelování](forced-tunneling.md).

## <a name="certifications"></a>Certifikace

Azure Firewall je obor platebních karet (PCI), ovládací prvky SOC (Service Organization Controls), ISO (ISO) a ICSA Labs splňující předpisy. Další informace najdete v tématu [Azure firewall certifikace dodržování předpisů](compliance-certifications.md).


## <a name="known-issues"></a>Známé problémy

Brána Azure Firewall má následující známé problémy:

|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
Pravidla síťového filtrování pro jiné protokoly než TCP/UDP (třeba ICMP) nebudou fungovat pro provoz do internetu.|Pravidla filtrování sítě pro protokoly jiné než TCP/UDP nefungují s SNAT na veřejnou IP adresu. Jiné protokoly než TCP/UDP jsou ale podporované mezi koncovými podsítěmi a virtuálními sítěmi.|Azure Firewall používá vyvažování zatížení úrovně Standard, [které v současnosti nepodporuje SNAT pro protokol IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Zkoumáme možnosti podpory tohoto scénáře v budoucí verzi.|
|Chybějící podpora PowerShellu a rozhraní příkazového řádku pro protokol ICMP|Azure PowerShell a CLI v síťových pravidlech nepodporují protokol ICMP jako platný protokol.|Protokol ICMP je stále možné používat prostřednictvím portálu a REST API. Pracujeme na přidání protokolu ICMP v PowerShellu a rozhraní příkazového řádku brzy.|
|Značky plně kvalifikovaného názvu domény vyžadují, aby byl nastavený protokol: port|Pravidla aplikací s značkami plně kvalifikovaného názvu domény vyžadují port: definice protokolu.|Jako hodnotu port: protokol můžete použít **https**. Pracujeme na tom, aby toto pole bylo volitelné při použití značek FQDN.|
|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje.|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje.|Podpora této funkce je naše mapa cest. Pokud chcete bránu firewall přesunout do jiné skupiny prostředků nebo předplatného, musíte odstranit aktuální instanci a znovu ji vytvořit v nové skupině prostředků nebo předplatném.|
|Výstrahy analýzy hrozeb se můžou maskovat.|Pravidla sítě s cílem 80/443 pro filtry odchozího filtrování upozorňující výstrahy, když jsou nakonfigurovaná jenom na režim výstrahy.|Vytvořte filtrování odchozího připojení pro 80/443 pomocí pravidel aplikací. Nebo změňte režim analýzy hrozeb na **Alert a Deny**.|
|Azure Firewall používá Azure DNS jenom pro překlad názvů.|Azure Firewall řeší plně kvalifikované názvy domény pouze pomocí Azure DNS. Vlastní server DNS není podporovaný. V ostatních podsítích není nijak ovlivněn překlad DNS.|Pracujeme na zmírnit toto omezení.|
|Azure Firewall DNAT nefunguje pro cíle privátních IP adres|Podpora Azure Firewall DNAT je omezená na odchozí přenosy z Internetu a příchozí přenos dat. DNAT v současné době nefunguje pro cíle privátních IP adres. Například paprskový na paprskový.|Toto je aktuální omezení.|
|První konfiguraci veřejné IP adresy nejde odebrat.|Každá Azure Firewall veřejná IP adresa je přiřazena ke *konfiguraci protokolu IP*.  Při nasazení brány firewall se přiřadí první konfigurace IP adresy a obvykle obsahuje odkaz na podsíť brány firewall (Pokud není explicitně nakonfigurovaný přes nasazení šablony). Tuto konfiguraci protokolu IP nemůžete odstranit, protože by to vedlo ke zrušení přidělení brány firewall. Pokud má brána firewall k dispozici alespoň jednu jinou veřejnou IP adresu, můžete i nadále změnit nebo odebrat veřejnou IP adresu přidruženou k této konfiguraci protokolu IP.|Toto chování je úmyslné.|
|Zóny dostupnosti se dají konfigurovat jenom během nasazování.|Zóny dostupnosti se dají konfigurovat jenom během nasazování. Po nasazení brány firewall nemůžete Zóny dostupnosti nakonfigurovat.|Toto chování je úmyslné.|
|SNAT při příchozích připojeních|Kromě DNAT jsou připojení přes veřejnou IP adresu (příchozí) brány firewall před jejich vstupem na jednu z privátních IP adres brány firewall. Tento požadavek dnes (také pro aktivní/aktivní síťová virtuální zařízení) zajistíte tak, aby se zajistilo symetrické směrování.|Pokud chcete zachovat původní zdroj pro HTTP/S, zvažte použití hlaviček [xff](https://en.wikipedia.org/wiki/X-Forwarded-For) . Můžete například použít službu, jako je například [přední vrátka Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) nebo [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) před bránou firewall. WAF můžete také přidat jako součást služby Azure front-dveří a řetězit k bráně firewall.
|Podpora filtrování plně kvalifikovaného názvu domény SQL pouze v režimu proxy (port 1433)|Pro Azure SQL Database, Azure SQL Data Warehouse a Azure SQL Managed instance:<br><br>V průběhu verze Preview se filtrování plně kvalifikovaného názvu domény SQL podporuje jenom v režimu proxy serveru (port 1433).<br><br>Pro Azure SQL IaaS:<br><br>Pokud používáte nestandardní porty, můžete tyto porty zadat v pravidlech aplikací.|V případě SQL v režimu přesměrování (výchozí při připojování z Azure) můžete místo toho filtrovat přístup pomocí značky služby SQL jako součást Azure Firewallch síťových pravidel.
|Odchozí provoz na portu TCP 25 není povolený.| Odchozí připojení SMTP, která používají port TCP 25, jsou blokovaná. Port 25 se primárně používá pro neověřené doručování e-mailů. Toto je výchozí chování platformy pro virtuální počítače. Další informace najdete v tématu řešení potíží s [odchozím připojením SMTP v Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Na rozdíl od virtuálních počítačů ale tuto funkci v tuto chvíli nemůžete povolit na Azure Firewall. Poznámka: Pokud chcete umožnit ověřenou službu SMTP (port 587) nebo SMTP přes jiný port než 25, ujistěte se prosím, že jste nakonfigurovali síťové pravidlo a ne pravidlo aplikace, protože kontrola SMTP se v tuto chvíli nepodporuje.|Použijte doporučenou metodu k odeslání e-mailu, jak je popsáno v článku věnovaném odstraňování potíží SMTP. Nebo vylučte virtuální počítač, který potřebuje odchozí přístup SMTP z výchozí trasy k bráně firewall. Místo toho nakonfigurujte odchozí přístup přímo na Internet.
|Aktivní FTP se nepodporuje.|Aktivní FTP je v Azure Firewall zakázané, aby se chránily proti útokům na vrácení FTP pomocí příkazu FTP PORT.|Místo toho můžete použít pasivní FTP. V bráně firewall je stále nutné explicitně otevřít porty TCP 20 a 21.
|Metrika využití portu SNAT zobrazuje 0%|Metrika využití portů Azure Firewall SNAT může zobrazovat 0% využití i v případě, že se používají porty SNAT. V takovém případě poskytuje použití metriky jako součást metriky stavu brány firewall nesprávný výsledek.|Tento problém byl opraven a zavedení do produkčního prostředí je cílené na květen 2020. V některých případech se problém vyřeší opětovným nasazením brány firewall, ale není konzistentní. V rámci dočasného řešení je třeba stav brány firewall použít jenom k vyhledání *stavu = snížený*, ne pro *stav = v pořádku*. Vyčerpání portů se zobrazí jako *degradované*. *Není v pořádku* , pokud jde o budoucí použití v případě, že jsou další metriky ovlivněny stavem brány firewall.
|DNAT se nepodporuje s povoleným vynuceným tunelovým propojením.|Brány firewall nasazené s povoleným vynuceným tunelovým propojením nemůžou podporovat příchozí přístup z Internetu kvůli asymetrickému směrování.|Jedná se o návrh z důvodu asymetrického směrování. Návratová cesta pro příchozí připojení prochází přes místní bránu firewall, která neviděla navázání připojení.
|Odchozí pasivní FTP nefunguje pro brány firewall s více veřejnými IP adresami.|Pasivní FTP vytvoří různá připojení pro řídicí a datové kanály. Když brána firewall s více veřejnými IP adresami odesílá odchozí data, náhodně vybere jednu z jejích veřejných IP adres pro zdrojovou IP adresu. FTP se nezdařila, pokud datové a řídicí kanály používají jiné zdrojové IP adresy.|Naplánovala se explicitní konfigurace SNAT. V tuto situaci zvažte použití jediné IP adresy.|
|V NetworkRuleHitu metriky chybí dimenze protokolu.|Metrika ApplicationRuleHit umožňuje protokol založený na filtrování, ale tato funkce chybí v odpovídající NetworkRuleHitové metrikě.|Probíhá šetření opravy.|
|Pravidla překladu adres (NAT) s porty mezi 64000 a 65535 nejsou podporovaná.|Azure Firewall povoluje jakýkoli port v rozsahu 1-65535 v pravidlech sítě a aplikace, ale pravidla NAT podporují jenom porty v rozsahu 1-63999.|Toto je aktuální omezení.
|Aktualizace konfigurace můžou v průměru trvat pět minut.|Aktualizace konfigurace Azure Firewall může v průměru trvat tři až pět minut a paralelní aktualizace nejsou podporované.|Probíhá šetření opravy.|
|Azure Firewall používá k filtrování provozu HTTPS a MSSQL hlavičky SNI TLS.|Pokud prohlížeč nebo serverový software nepodporuje rozšíření SNI (název serveru), nebudete se moct připojit prostřednictvím Azure Firewall.|Pokud prohlížeč nebo serverový software nepodporuje SNI, může být možné řídit připojení pomocí síťového pravidla namísto pravidla aplikace. Software, který podporuje SNI, najdete v tématu [indikace názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication) .

## <a name="next-steps"></a>Další kroky

- [Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal](tutorial-firewall-deploy-portal.md)
- [Nasazení brány Azure Firewall pomocí šablony](deploy-template.md)
- [Vytvoření testovacího prostředí brány Azure Firewall](scripts/sample-create-firewall-test.md)
