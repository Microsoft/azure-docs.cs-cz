---
title: Co je brána Azure Firewall?
description: Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 04/07/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 951396afc95a215a6ff9f4885f83fcdf6efdeb72
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810329"
---
# <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

![Certifikace ICSA](media/overview/icsa-cert-firewall-small.png)

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu.

![Přehled brány firewall](media/overview/firewall-threat.png)

Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě.  Služba je plně integrovaná se službou Azure Monitor zajišťující protokolování a analýzy.

Brána Azure Firewall nabízí následující funkce:

## <a name="built-in-high-availability"></a>Integrovaná vysoká dostupnost

Je zabudována vysoká dostupnost, takže nejsou vyžadovány žádné další vykladače zatížení a není třeba nic konfigurovat.

## <a name="availability-zones"></a>Zóny dostupnosti

Azure Firewall lze během nasazení nakonfigurovat tak, aby pro zvýšení dostupnosti přebývalo více zón dostupnosti. Díky zónám dostupnosti se vaše dostupnost zvyšuje na 99,99% dostupnost. Další informace naleznete v [tématu Azure Firewall Smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99% dostupnost sla je nabízena, když jsou vybrány dvě nebo více zón dostupnosti.

Azure Firewall můžete také přidružit ke konkrétní zóně jen z důvodu blízkosti pomocí standardní 99,95% sla služby.

Za bránu firewall nasazenou v zóně dostupnosti se nezapříplatují žádné další náklady. Existují však další náklady na příchozí a odchozí datové přenosy spojené s zónami dostupnosti. Další informace naleznete v [tématu Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Zóny dostupnosti azure firewall jsou k dispozici v oblastech, které podporují zóny dostupnosti. Další informace najdete v tématu [Co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Zóny dostupnosti lze konfigurovat pouze během nasazení. Existující bránu firewall nelze nakonfigurovat tak, aby zahrnovala zóny dostupnosti.

Další informace o zónách dostupnosti najdete v tématu [Co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Neomezená cloudová škálovatelnost

Bránu Azure Firewall můžete vertikálně škálovat tak, jak to vyžadují změny v síťovém provozu, takže nemusíte platit za dimenzování podle špiček v datovém toku.

## <a name="application-fqdn-filtering-rules"></a>Pravidla filtrování plně kvalifikovaných názvů domén aplikací

Odchozí provoz HTTP/S nebo provoz Azure SQL (preview) můžete omezit na zadaný seznam plně kvalifikovaných názvů domén (FQDN) včetně zástupných znaků. Tato funkce nevyžaduje ukončení SSL.

## <a name="network-traffic-filtering-rules"></a>Pravidla filtrování síťového provozu

Můžete centrálně vytvořit pravidla pro *povolení* nebo *blokování* podle zdrojové a cílové IP adresy, portu a protokolu. Brána Azure Firewall je plně stavová, takže dokáže odlišit legitimní pakety pro různé typy spojení. Pravidla jsou vynucována a protokolována napříč různými předplatnými a virtuálními sítěmi.

## <a name="fqdn-tags"></a>Značky plně kvalifikovaných názvů domén

Značky FQDN usnadňují povolení známého síťového provozu služeb Azure prostřednictvím brány firewall. Řekněme například, že chcete povolit síťové přenosy z webu Windows Update přes bránu firewall. Můžete vytvořit pravidlo aplikace a zahrnout značku webu Windows Update. Teď je možný síťový přenos z webu Windows Update přes vaši bránu firewall.

## <a name="service-tags"></a>Značky služeb

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nelze vytvořit vlastní výrobní značku ani určit, které IP adresy jsou do značky zahrnuty. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

## <a name="threat-intelligence"></a>Analýza hrozeb

Filtrování založené na technologii threat intelligence může být povoleno, aby brána firewall upozorňovala a odepírala provoz ze známých škodlivých IP adres a domén. IP adresy a domény jsou získávány z informačního kanálu Microsoft Threat Intelligence.

## <a name="outbound-snat-support"></a>Podpora pro odchozí SNAT

Veškeré IP adresy pro odchozí provoz z virtuálních sítí se překládají na veřejnou IP adresu brány Azure Firewall na základě zdroje (SNAT). Můžete identifikovat a povolit provoz pocházející z vaší virtuální sítě do vzdálených internetových cílů. Azure Firewall není SNAT, pokud cílová IP adresa je privátní rozsah IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Pokud vaše organizace používá rozsah veřejných IP adres pro privátní sítě, Azure Firewall spoří provoz na jednu z privátních IP adres brány firewall v síti AzureFirewallSubnet. Bránu Azure Firewall **not** můžete nakonfigurovat tak, aby nespořila rozsah veřejných IP adres. Další informace naleznete v tématu [Rozsahprijových IP adres Azure Firewall SNAT](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Podpora DNAT u příchozích přenosů

Příchozí internetový síťový provoz na veřejnou IP adresu brány firewall je přeložen (Překlad cílové síťové adresy) a filtrován na privátní IP adresy ve virtuálních sítích.

## <a name="multiple-public-ip-addresses"></a>Více veřejných IP adres

K bráně firewall můžete přidružit více veřejných IP adres (až 100).

To umožňuje následující scénáře:

- **DNAT** - Můžete přeložit více standardních instancí portů na back-endové servery. Máte-li například dvě veřejné adresy IP, můžete přeložit port TCP 3389 (RDP) pro obě adresy IP.
- **SNAT** - Další porty jsou k dispozici pro odchozí připojení SNAT, což snižuje potenciál vyčerpání portu SNAT. V tuto chvíli Azure Firewall náhodně vybere zdrojovou veřejnou IP adresu, která se má použít pro připojení. Pokud máte v síti nějaké následné filtrování, je třeba povolit všechny veřejné IP adresy přidružené k bráně firewall.

## <a name="azure-monitor-logging"></a>Protokolování Azure Monitor

Všechny události jsou integrované s Azure Monitor, což vám umožní archivovat protokoly na účet úložiště, streamovat události do centra událostí nebo je odeslat do protokolů Azure Monitoru.

## <a name="certifications"></a>Certifikace

Azure Firewall je odvětví platebních karet (PCI), ovládací prvky organizace služeb (SOC), mezinárodní organizace pro normalizaci (ISO) a laboratoře ICSA kompatibilní. Další informace najdete v tématu [Certifikace dodržování předpisů azure firewall .](compliance-certifications.md)


## <a name="known-issues"></a>Známé problémy

Brána Azure Firewall má následující známé problémy:

|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
Pravidla síťového filtrování pro jiné protokoly než TCP/UDP (třeba ICMP) nebudou fungovat pro provoz do internetu.|Pravidla filtrování sítě pro protokoly, které nejsou protokoly TCP/UDP, nefungují s protokolem SNAT na vaši veřejnou IP adresu. Jiné protokoly než TCP/UDP jsou ale podporované mezi koncovými podsítěmi a virtuálními sítěmi.|Azure Firewall používá vyvažování zatížení úrovně Standard, [které v současnosti nepodporuje SNAT pro protokol IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Zkoumáme možnosti podpory tohoto scénáře v budoucí verzi.|
|Chybějící podpora PowerShellu a rozhraní příkazového řádku pro protokol ICMP|Azure PowerShell a CLI nepodporují ICMP jako platný protokol v síťových pravidlech.|IcMP je stále možné použít jako protokol prostřednictvím portálu a rozhraní REST API. Pracujeme na přidání ICMP v PowerShell a CLI brzy.|
|Značky plně kvalifikovaného názvu domény vyžadují, aby byl nastavený protokol: port|Pravidla aplikace se značkami FQDN vyžadují port: definice protokolu.|Jako hodnotu port: protokol můžete použít **https**. Pracujeme na tom, aby toto pole bylo volitelné při použití značek FQDN.|
|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného není podporováno.|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného není podporováno.|Podpora této funkce je na naší cestovní mapě. Pokud chcete bránu firewall přesunout do jiné skupiny prostředků nebo předplatného, musíte odstranit aktuální instanci a znovu ji vytvořit v nové skupině prostředků nebo předplatném.|
|Výstrahy analýzy hrozeb mohou být maskovány|Síťová pravidla s cílem 80/443 pro odchozí filtrování masky výstrahy threat intelligence při konfiguraci pouze upozornění režimu.|Vytvořte odchozí filtrování pro 80/443 pomocí pravidel aplikace. Nebo změňte režim analýzy hrozeb na **Výstraha a Odepřít**.|
|Azure Firewall používá Azure DNS jenom pro překlad názvů|Azure Firewall řeší názvy domény pomocí jenom Azure DNS. Vlastní dns server není podporován. Neexistuje žádný vliv na rozlišení DNS v jiných podsítích.|Pracujeme na uvolnění tohoto omezení.|
|Azure Firewall SNAT/DNAT nefunguje pro privátní IP cíle|Podpora Azure Firewall SNAT/DNAT je omezená na odchozí a příchozí přenos internetu. SNAT/DNAT v současné době nefunguje pro soukromé ip destinace. Například, mluvil mluvil.|Toto je aktuální omezení.|
|Nelze odebrat první veřejnou konfiguraci IP|Každá veřejná IP adresa Azure Firewall je přiřazena *ke konfiguraci IP*adresy .  První konfigurace protokolu IP je přiřazena během nasazení brány firewall a obvykle také obsahuje odkaz na podsíť brány firewall (pokud není explicitně nakonfigurována jinak prostřednictvím nasazení šablony). Tuto konfiguraci PROTOKOLU IP nelze odstranit, protože by byla brána firewall zrušena. Stále můžete změnit nebo odebrat veřejnou IP adresu přidruženou k této konfiguraci IP, pokud má brána firewall k dispozici alespoň jednu další veřejnou IP adresu.|Toto chování je úmyslné.|
|Zóny dostupnosti lze konfigurovat pouze během nasazení.|Zóny dostupnosti lze konfigurovat pouze během nasazení. Zóny dostupnosti nelze konfigurovat po nasazení brány firewall.|Toto chování je úmyslné.|
|SNAT na příchozí připojení|Kromě DNAT jsou připojení přes veřejnou IP adresu firewallu (příchozí) nahlašována do jednoho z privátních IP adres brány firewall. Tento požadavek dnes (také pro aktivní/aktivní neva) pro zajištění symetrického směrování.|Chcete-li zachovat původní zdroj pro protokol HTTP/S, zvažte použití záhlaví [XFF.](https://en.wikipedia.org/wiki/X-Forwarded-For) Můžete například použít službu, jako je [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) nebo Azure Application [Gateway](../application-gateway/rewrite-http-headers.md) před bránou firewall. Waf můžete také přidat jako součást Azure Front Door a řetězu do brány firewall.
|Podpora filtrování SQL FQDN pouze v režimu proxy (port 1433)|Pro Azure SQL Database, Azure SQL Data Warehouse a Azure SQL Managed Instance:<br><br>Během náhledu sql fqdn filtrování je podporována pouze v režimu proxy (port 1433).<br><br>Pro Azure SQL IaaS:<br><br>Pokud používáte nestandardní porty, můžete tyto porty zadat v pravidlech aplikace.|Pro SQL v režimu přesměrování, což je výchozí, pokud se připojujete z Azure, můžete místo toho filtrovat přístup pomocí značky služby SQL jako součást pravidel sítě Azure Firewall.
|Odchozí provoz na portu TCP 25 není povolen.| Odchozí připojení SMTP, která používají port TCP 25, jsou blokována. Port 25 se používá především pro neověřené doručování e-mailů. Toto je výchozí chování platformy pro virtuální počítače. Další informace najdete [v tématu Řešení potíží s odchozím připojením SMTP v Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Na rozdíl od virtuálních počítačů však není v současné době možné povolit tuto funkci v bráně Azure Firewall.|Podle doporučené metody odeslat e-mail, jak je popsáno v článku řešení potíží SMTP. Nebo vylučte virtuální počítač, který potřebuje odchozí přístup SMTP z výchozí trasy do brány firewall, a místo toho nakonfigurujte odchozí přístup přímo do Internetu.
|Aktivní ftp není podporován.|Aktivní ftp je zakázáno na Azure Firewall k ochraně proti útokům FTP bounce pomocí příkazu FTP PORT.|Místo toho můžete použít pasivní FTP. Stále je nutné explicitně otevřít porty TCP 20 a 21 v bráně firewall.
|Metrika využití portu SNAT zobrazuje 0 %|Metrika využití portu Azure Firewall SNAT může zobrazovat 0 % využití i při použití portů SNAT. V tomto případě pomocí metriky jako součást metriky stavu brány firewall poskytuje nesprávný výsledek.|Tento problém byl vyřešen a zavedení do výroby je určeno na květen 2020. V některých případech přeřazení brány firewall problém vyřeší, ale není konzistentní. Jako zprostředkující řešení použijte stav brány firewall pouze k vyhledání *stavu=degradováno*, nikoli *stavu=není v pořádku*. Vyčerpání portů se projeví jako *zhoršené*. *Není v pořádku* je vyhrazena pro budoucí použití, když jsou další metriky ovlivnit stav brány firewall.
|Funkce DNAT není podporována s povoleným vynuceným tunelovým propojením.|Brány firewall nasazené s povoleným vynuceným tunelovým propojením nemohou z důvodu asymetrického směrování podporovat příchozí přístup z Internetu.|Toto je záměrné z důvodu asymetrického směrování. Zpáteční cesta pro příchozí připojení vede přes místní bránu firewall, ve které se připojení nenapojovalo.
|Odchozí pasivní ftp nefunguje pro brány firewall s více veřejnými IP adresami.|Pasivní FTP vytváří různá připojení pro řídicí a datové kanály. Pokud brána firewall s více veřejnými IP adresami odešle odchozí data, náhodně vybere jednu ze svých veřejných IP adres pro zdrojovou IP adresu. Protokol FTP selže, pokud datové a řídicí kanály používají různé zdrojové adresy IP.|Je plánována explicitní konfigurace SNAT. Mezitím zvažte použití jedné IP adresy v této situaci.|

## <a name="next-steps"></a>Další kroky

- [Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal](tutorial-firewall-deploy-portal.md)
- [Nasazení brány Azure Firewall pomocí šablony](deploy-template.md)
- [Vytvoření testovacího prostředí brány Azure Firewall](scripts/sample-create-firewall-test.md)
