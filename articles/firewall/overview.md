---
title: Co je brána Azure Firewall?
description: Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 02/16/2021
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 56d04abe73020cef09383d4f79a58f037c266a93
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547992"
---
# <a name="what-is-azure-firewall"></a>Co je brána Azure Firewall?

![Certifikace ICSA](media/overview/icsa-cert-firewall-small.png)

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu.

![Přehled brány firewall](media/overview/firewall-threat.png)

Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi. Brána Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě a díky tomu umožňuje venkovním bránám firewall identifikovat provoz pocházející z vaší virtuální sítě.  Služba je plně integrovaná se službou Azure Monitor zajišťující protokolování a analýzy.

Další informace o funkcích Azure Firewall najdete v tématu [Azure firewall funkce](features.md).

## <a name="azure-firewall-premium-preview"></a>Azure Firewall Premium Preview

Azure Firewall Premium Preview je brána firewall nové generace s funkcemi, které jsou potřeba pro vysoce citlivá a regulovaná prostředí. Mezi tyto možnosti patří kontrola protokolu TLS, zprostředkovatelů identity, filtrování adres URL a webové kategorie.

Další informace o funkcích Azure Firewall Premium ve verzi Preview najdete v tématu [Azure firewall Premium Preview](premium-features.md).


Pokud chcete zjistit, jak je brána firewall Premium Preview nakonfigurovaná v Azure Portal, přečtěte si téma [Azure firewall Premium Preview v Azure Portal](premium-portal.md).


## <a name="pricing-and-sla"></a>Ceny a smlouvy SLA

Informace o cenách Azure Firewall najdete v tématu [Azure firewall ceny](https://azure.microsoft.com/pricing/details/azure-firewall/).

Informace o Azure Firewall smlouvě SLA najdete v tématu [Azure firewall SLA](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Novinky

Další informace o tom, co je nového v Azure Firewall, najdete v tématu [Aktualizace Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Známé problémy

Brána Azure Firewall má následující známé problémy:

|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
Pravidla síťového filtrování pro jiné protokoly než TCP/UDP (třeba ICMP) nebudou fungovat pro provoz do internetu.|Pravidla filtrování sítě pro protokoly jiné než TCP/UDP nefungují s SNAT na veřejnou IP adresu. Jiné protokoly než TCP/UDP jsou ale podporované mezi koncovými podsítěmi a virtuálními sítěmi.|Azure Firewall používá vyvažování zatížení úrovně Standard, [které v současnosti nepodporuje SNAT pro protokol IP](../load-balancer/load-balancer-overview.md). Zkoumáme možnosti podpory tohoto scénáře v budoucí verzi.|
|Chybějící podpora PowerShellu a rozhraní příkazového řádku pro protokol ICMP|Azure PowerShell a CLI v síťových pravidlech nepodporují protokol ICMP jako platný protokol.|Protokol ICMP je stále možné používat prostřednictvím portálu a REST API. Pracujeme na přidání protokolu ICMP v PowerShellu a rozhraní příkazového řádku brzy.|
|Značky plně kvalifikovaného názvu domény vyžadují, aby byl nastavený protokol: port|Pravidla aplikací s značkami plně kvalifikovaného názvu domény vyžadují port: definice protokolu.|Jako hodnotu port: protokol můžete použít **https**. Pracujeme na tom, aby toto pole bylo volitelné při použití značek FQDN.|
|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje.|Přesunutí brány firewall do jiné skupiny prostředků nebo předplatného se nepodporuje.|Podpora této funkce je naše mapa cest. Pokud chcete bránu firewall přesunout do jiné skupiny prostředků nebo předplatného, musíte odstranit aktuální instanci a znovu ji vytvořit v nové skupině prostředků nebo předplatném.|
|Výstrahy analýzy hrozeb se můžou maskovat.|Pravidla sítě s cílem 80/443 pro filtry odchozího filtrování upozorňující výstrahy, když jsou nakonfigurovaná jenom na režim výstrahy.|Vytvořte filtrování odchozího připojení pro 80/443 pomocí pravidel aplikací. Nebo změňte režim analýzy hrozeb na **Alert a Deny**.|
|Azure Firewall DNAT nefunguje pro cíle privátních IP adres|Podpora Azure Firewall DNAT je omezená na odchozí přenosy z Internetu a příchozí přenos dat. DNAT v současné době nefunguje pro cíle privátních IP adres. Například paprskový na paprskový.|Toto je aktuální omezení.|
|První konfiguraci veřejné IP adresy nejde odebrat.|Každá Azure Firewall veřejná IP adresa je přiřazena ke *konfiguraci protokolu IP*.  Při nasazení brány firewall se přiřadí první konfigurace IP adresy a obvykle obsahuje odkaz na podsíť brány firewall (Pokud není explicitně nakonfigurovaný přes nasazení šablony). Tuto konfiguraci protokolu IP nemůžete odstranit, protože by to vedlo ke zrušení přidělení brány firewall. Pokud má brána firewall k dispozici alespoň jednu jinou veřejnou IP adresu, můžete i nadále změnit nebo odebrat veřejnou IP adresu přidruženou k této konfiguraci protokolu IP.|Toto chování je úmyslné.|
|Zóny dostupnosti se dají konfigurovat jenom během nasazování.|Zóny dostupnosti se dají konfigurovat jenom během nasazování. Po nasazení brány firewall nemůžete Zóny dostupnosti nakonfigurovat.|Toto chování je úmyslné.|
|SNAT při příchozích připojeních|Kromě DNAT jsou připojení přes veřejnou IP adresu (příchozí) brány firewall před jejich vstupem na jednu z privátních IP adres brány firewall. Tento požadavek dnes (také pro aktivní/aktivní síťová virtuální zařízení) zajistíte tak, aby se zajistilo symetrické směrování.|Pokud chcete zachovat původní zdroj pro HTTP/S, zvažte použití hlaviček [xff](https://en.wikipedia.org/wiki/X-Forwarded-For) . Můžete například použít službu, jako je například [přední vrátka Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) nebo [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) před bránou firewall. WAF můžete také přidat jako součást služby Azure front-dveří a řetězit k bráně firewall.
|Podpora filtrování plně kvalifikovaného názvu domény SQL pouze v režimu proxy (port 1433)|Pro Azure SQL Database, Azure synapse Analytics a Azure SQL Managed instance:<br><br>Filtrování plně kvalifikovaného názvu domény SQL je podporováno pouze v režimu proxy serveru (port 1433).<br><br>Pro Azure SQL IaaS:<br><br>Pokud používáte nestandardní porty, můžete tyto porty zadat v pravidlech aplikací.|V případě SQL v režimu přesměrování (výchozí při připojování z Azure) můžete místo toho filtrovat přístup pomocí značky služby SQL jako součást Azure Firewallch síťových pravidel.
|Odchozí provoz na portu TCP 25 není povolený.| Odchozí připojení SMTP, která používají port TCP 25, jsou blokovaná. Port 25 se primárně používá pro neověřené doručování e-mailů. Toto je výchozí chování platformy pro virtuální počítače. Další informace najdete v tématu řešení potíží s [odchozím připojením SMTP v Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Na rozdíl od virtuálních počítačů ale tuto funkci v tuto chvíli nemůžete povolit na Azure Firewall. Poznámka: Pokud chcete umožnit ověřený protokol SMTP (port 587) nebo SMTP přes jiný port než 25, ujistěte se, že jste nakonfigurovali síťové pravidlo a nejedná se o pravidlo aplikace, protože kontrola SMTP není v tuto chvíli podporovaná.|Použijte doporučenou metodu k odeslání e-mailu, jak je popsáno v článku věnovaném odstraňování potíží SMTP. Nebo vylučte virtuální počítač, který potřebuje odchozí přístup SMTP z výchozí trasy k bráně firewall. Místo toho nakonfigurujte odchozí přístup přímo na Internet.
|Metrika využití portu SNAT zobrazuje 0%|Metrika využití portů Azure Firewall SNAT může zobrazovat 0% využití i v případě, že se používají porty SNAT. V takovém případě poskytuje použití metriky jako součást metriky stavu brány firewall nesprávný výsledek.|Tento problém byl opraven a zavedení do produkčního prostředí je cílené na květen 2020. V některých případech se problém vyřeší opětovným nasazením brány firewall, ale není konzistentní. V rámci dočasného řešení je třeba stav brány firewall použít jenom k vyhledání *stavu = snížený*, ne pro *stav = v pořádku*. Vyčerpání portů se zobrazí jako *degradované*. *Není v pořádku* pro budoucí použití, pokud jsou více metrik, aby ovlivnily stav brány firewall.
|DNAT se nepodporuje s povoleným vynuceným tunelovým propojením.|Brány firewall nasazené s povoleným vynuceným tunelovým propojením nemůžou podporovat příchozí přístup z Internetu kvůli asymetrickému směrování.|Jedná se o návrh z důvodu asymetrického směrování. Návratová cesta pro příchozí připojení prochází přes místní bránu firewall, která neviděla navázání připojení.
|Odchozí pasivní FTP nemusí fungovat pro brány firewall s více veřejnými IP adresami v závislosti na konfiguraci serveru FTP.|Pasivní FTP vytvoří různá připojení pro řídicí a datové kanály. Když brána firewall s více veřejnými IP adresami odesílá odchozí data, náhodně vybere jednu z jejích veřejných IP adres pro zdrojovou IP adresu. Protokol FTP může selhat, pokud datové a řídicí kanály používají jiné zdrojové IP adresy v závislosti na konfiguraci serveru FTP.|Naplánovala se explicitní konfigurace SNAT. Mezitím můžete nakonfigurovat server FTP tak, aby přijímal data a řídicí kanály z různých zdrojových IP adres (viz [Příklad služby IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Případně zvažte použití jediné IP adresy v této situaci.|
|Příchozí pasivní FTP nemusí fungovat v závislosti na konfiguraci serveru FTP. |Pasivní FTP vytvoří různá připojení pro řídicí a datové kanály. Příchozí připojení v Azure Firewall před jejich vstupem na jednu z privátních IP adres brány firewall, aby se zajistilo symetrické směrování. Protokol FTP může selhat, pokud datové a řídicí kanály používají jiné zdrojové IP adresy v závislosti na konfiguraci serveru FTP.|Probíhá zkoumání původní zdrojové IP adresy. Mezitím můžete nakonfigurovat server FTP tak, aby přijímal data a řídicí kanály z různých zdrojových IP adres.|
|V NetworkRuleHitu metriky chybí dimenze protokolu.|Metrika ApplicationRuleHit umožňuje protokol založený na filtrování, ale tato funkce chybí v odpovídající NetworkRuleHitové metrikě.|Probíhá šetření opravy.|
|Pravidla překladu adres (NAT) s porty mezi 64000 a 65535 nejsou podporovaná.|Azure Firewall povoluje jakýkoli port v rozsahu 1-65535 v pravidlech sítě a aplikace, ale pravidla NAT podporují jenom porty v rozsahu 1-63999.|Toto je aktuální omezení.
|Aktualizace konfigurace můžou v průměru trvat pět minut.|Aktualizace konfigurace Azure Firewall může v průměru trvat tři až pět minut a paralelní aktualizace nejsou podporované.|Probíhá šetření opravy.|
|Azure Firewall používá k filtrování provozu HTTPS a MSSQL hlavičky SNI TLS.|Pokud prohlížeč nebo serverový software nepodporuje rozšíření SNI (název serveru), nemůžete se připojit prostřednictvím Azure Firewall.|Pokud prohlížeč nebo serverový software nepodporuje SNI, může být možné řídit připojení pomocí síťového pravidla namísto pravidla aplikace. Software, který podporuje SNI, najdete v tématu [indikace názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication) .|
|Vlastní DNS nefunguje s vynuceným tunelovým propojením|Pokud je povolené vynucené tunelování, vlastní DNS nefunguje.|Probíhá šetření opravy.|
|Funkce Spustit/zastavit nefunguje s bránou firewall nakonfigurovanou v režimu vynuceného tunelového propojení.|Spuštění/zastavení nefunguje s bránou Azure firewall nakonfigurovanou v režimu vynuceného tunelového propojení. Při pokusu o spuštění Azure Firewall s nakonfigurovaným vynuceným tunelovým propojením dojde k následující chybě:<br><br>*Set-AzFirewall: AzureFirewall nelze přidat konfiguraci IP adresy pro správu FW-XX do existující brány firewall. Pokud chcete používat vynucené tunelové propojení, proveďte znovu nasazení s konfigurací IP adresy pro správu. <br> StatusCode: 400 <br> ReasonPhrase: Chybný požadavek*|V rámci šetření.<br><br>Jako alternativní řešení můžete odstranit existující bránu firewall a vytvořit novou se stejnými parametry.|
|Nejde přidat značky zásad brány firewall pomocí portálu.|Zásady Azure Firewall mají omezení podpory oprav, které vám brání v přidávání značky pomocí Azure Portal. Vygenerovala se následující chyba: *značky pro prostředek se nepovedlo Uložit*.|Probíhá šetření opravy. Nebo můžete použít rutinu Azure PowerShell `Set-AzFirewallPolicy` k aktualizaci značek.|
|Protokol IPv6 ještě není podporovaný.|Pokud přidáte adresu IPv6 k pravidlu, brána firewall se nezdařila.|Používejte jenom IPv4 adresy. V rámci šetření je podporována podpora protokolu IPv6.|


## <a name="next-steps"></a>Další kroky

- [Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal](tutorial-firewall-deploy-portal.md)
- [Nasazení brány Azure Firewall pomocí šablony](deploy-template.md)
- [Vytvoření testovacího prostředí brány Azure Firewall](scripts/sample-create-firewall-test.md)