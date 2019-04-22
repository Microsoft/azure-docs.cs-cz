---
title: Nejčastější dotazy k Azure Application Gateway
description: Najděte odpovědi na nejčastější dotazy týkající se Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: 3c8a2fe9f4486fe4d33754b58f4e7ebec1b3252d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682946"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Nejčastější dotazy k Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Obecné

### <a name="what-is-application-gateway"></a>Co je Application Gateway?

Azure Application Gateway poskytuje kontroler doručování aplikací (ADC) jako službu. Nabízí různé vrstvy 7 možnostmi Vyrovnávání zatížení pro vaše aplikace. Tato služba je vysoce dostupná, škálovatelná a plně spravovanou službou Azure.

### <a name="what-features-does-application-gateway-support"></a>Jaké funkce Application Gateway podporuje?

Služba Application Gateway podporuje automatické škálování, snižování zátěže protokolu SSL a -kompletního protokolu SSL, firewall webových aplikací (WAF), spřažení relace na základě souborů cookie, adresy URL směrování na základě cesty, hostování ve více lokalitách a další funkce. Úplný seznam podporovaných funkcí najdete v tématu [seznámení se službou Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Jak se liší Application Gateway a nástroje pro vyrovnávání zatížení Azure?

Služba Application Gateway je Vyrovnávání zatížení vrstvy 7, což znamená, že bude fungovat jenom s webový provoz (HTTP, HTTPS, pomocí protokolu WebSocket a HTTP/2). Podporuje možnosti, jako je například ukončení protokolu SSL, spřažení relace na základě souborů cookie a kruhové dotazování pro vyrovnávání zatížení provozu. Načtěte nástroj pro vyrovnávání zatížení – provozu na vrstvy 4 (TCP nebo UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jaké protokolů Application Gateway podporuje?

Služba Application Gateway podporuje HTTP, HTTPS, HTTP/2 a protokolu WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak Application Gateway podporuje HTTP/2?

Zobrazit [podpora HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Které prostředky jsou podporované jako součást back-endový fond?

Zobrazit [podporované back-endovým prostředkům](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>V jaké oblasti je Application Gateway k dispozici?

Služba Application Gateway je k dispozici ve všech oblastech Azure globální. Je také k dispozici v [Azure China 21Vianet](https://www.azure.cn/) a [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Toto nasazení je vyhrazený pro mé předplatné, nebo je sdílen mezi zákazníky?

Služba Application Gateway je vyhrazené nasazení ve virtuální síti.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Služba Application Gateway podporuje přesměrování HTTP na HTTPS?

Přesměrování se nepodporuje. Zobrazit [přehled přesměrování ve službě Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>V jakém pořadí se naslouchací procesy zpracovávají?

Zobrazit [pořadí zpracování naslouchací proces](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Kde najdu Application Gateway IP a DNS?

Pokud používáte jako koncový bod veřejné IP adresy, najdete informace o protokolu IP a DNS na prostředek veřejné IP adresy. Nebo ho najít na portálu, na stránce Přehled služby application gateway. Pokud používáte interní IP adresy, najdete informace na stránce Přehled.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Co jsou nastavení časového limitu Keep-Alive a časového limitu nečinnosti protokolu TCP?

 V SKU v1 Application Gateway Keep-Alive časový limit je 120 sekund. Keep-Alive časový limit pro v2 SKU je 75 sekund. Vypršení časového limitu nečinnosti protokolu TCP je výchozí 4minutové na front-endový virtuální IP adresa (VIP) služby Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Mění název IP nebo DNS během životního cyklu application gateway?

Virtuální IP adresu můžete změnit, je-li zastavit a spustit službu application gateway. Ale název DNS, který je přidružený k službě application gateway nemění během životního cyklu brány. Vzhledem k tomu, že nedojde ke změně názvu DNS, musí použít CNAME alias a přejděte na adresu DNS služby application gateway.

### <a name="does-application-gateway-support-static-ip"></a>Služba Application Gateway podporuje statickou IP adresu?

Ano, SKU v2 Application Gateway podporuje statické veřejné IP adresy. V1 SKU podporuje statické interní IP adresy.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Služba Application Gateway podporuje několik veřejných IP adres v bráně?

Službu application gateway podporuje pouze jednu veřejnou IP adresu.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak velké by měl udělám mou podsíť pro službu Application Gateway?

Zobrazit [aspekty velikost podsítě Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Je možné nasadit více než jeden prostředek aplikační brány pro jednu podsíť?

Ano. Kromě několika instancí dané nasazení Application Gateway můžete zřídit jiný jedinečný prostředek aplikační brány do existující podsítě, který obsahuje jiný prostředek aplikační brány.

Jednu podsíť nemůže podporovat Standard_v2 a standardní Application Gateway společně.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Služba Application Gateway podporuje hlavičky x předané pro?

Ano. Zobrazit [úpravy na žádost o](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Jak dlouho trvá nasazení služby application gateway? Moje služba application gateway bude fungovat se během aktualizace?

Nová nasazení SKU v1 Application Gateway může trvat až 20 minut, než se zřídit. Změny velikosti instance nebo počet nejsou rušivé a během této doby zůstává aktivní brány.

Nasazení, která používají v2 SKU může trvat až 6 minut, než se zřídit.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Můžete použít systému Exchange Server jako back-end pomocí služby Application Gateway?

Ne. Služba Application Gateway nepodporuje e-mailových protokolů, jako je například POP3, SMTP a IMAP. 

## <a name="performance"></a>Výkon

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak Application Gateway podporuje vysokou dostupnost a škálovatelnost?

SKU v1 Application Gateway podporuje scénáře vysoké dostupnosti, když nasadíte dva nebo víc instancí. Azure rozděluje tyto instance mezi aktualizace a domény selhání k zajištění, že instance není všechna selhání ve stejnou dobu. V1 SKU podporuje škálovatelnost přidáním více instancí stejné bráně sdílení zatížení.

V2 SKU automaticky zajistí, že nové instance jsou rozděleny mezi doménami selhání a aktualizačními doménami. Pokud se rozhodnete redundanci zón, nejnovější instance jsou také šíří napříč zónami dostupnosti nabízí oblastmi selhání odolnost proti chybám.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Jak dosáhnu scénář zotavení po Havárii napříč datovými centry s využitím Application Gateway?

Traffic Manager můžete používat k distribuování síťového provozu mezi více aplikačních bran v různých datových centrech.

### <a name="does-application-gateway-support-autoscaling"></a>Služba Application Gateway podporuje automatické škálování?

Ano, SKU v2 Application Gateway podporuje automatické škálování. Další informace najdete v tématu [automatické škálování a zónově redundantní služba Application Gateway (public preview)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Provede ruční škálování vertikálně zvýšit nebo snížit prostoje příčina?

Ne. Instance se distribuují napříč upgradovací doménami a doménami selhání.

### <a name="does-application-gateway-support-connection-draining"></a>Služba Application Gateway podporuje vyprázdnění připojení?

Ano. Můžete nastavit vyprázdnění Chcete-li změnit členy v rámci fondu back-end bez přerušení připojení. Toto nastavení umožní vám bude odesílat existující připojení do jejich předchozího cíle dokud buď toto připojení zavře nebo konfigurovatelný časový limit vyprší platnost. Vyprázdnění připojení čeká pouze aktuální vydávaných za pochodu připojení na dokončení. Služba Application Gateway není si vědom stavu relace aplikace.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Můžu změnit velikost instance ze střední a velké bez přerušení?

Ano. Azure rozděluje instance mezi aktualizace a domény selhání k zajištění, že instance není selžou všechny najednou. Služba Application Gateway podporuje škálování tak, že přidáte více instancí stejné bráně sdílení zatížení.

## <a name="configuration"></a>Konfigurace

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Služba Application Gateway vždy nasazení ve virtuální síti?

Ano. Služba Application Gateway je vždy nasazené v podsíti virtuální sítě. Tato podsíť může obsahovat jenom aplikační brány. Další informace najdete v tématu [požadavky na virtuální síť a podsíť](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Služba Application Gateway komunikovat instancí mimo jeho virtuální síť nebo mimo svého předplatného?

Za předpokladu, když máte připojení pomocí IP adresy, služba Application Gateway může komunikovat s instancí mimo virtuální síť, která je v. Služba Application Gateway může také komunikovat s instancí mimo předplatné, které je v. Pokud máte v plánu používat interní IP adresy jako členy fondu back-endu, použijte [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) nebo [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Je možné nasadit nic jiného v podsítě služby application gateway?

Ne. Ale můžete nasadit další služby application Gateway v podsíti.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Podporují se skupinami zabezpečení sítě na podsítě služby application gateway?

Zobrazit [skupiny zabezpečení sítě v podsítě Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Podsítě služby application gateway podporuje uživatelsky definovaných tras?

Zobrazit [trasy definované uživatelem, které jsou podporovány v podsítě Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jaká jsou omezení ve službě Application Gateway? Můžete tyto limity zvýšit?

Zobrazit [omezuje Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Můžu pro externí a interní provoz používat současně Application Gateway?

Ano. Služba Application Gateway podporuje jeden interní IP adresa a jedna externí IP adresy na aplikační bránu.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Podporuje aplikační bránu partnerský vztah virtuální sítě?

Ano. Partnerské vztahy virtuálních sítí pomáhá vyrovnávat zatížení provozu v jiných virtuálních sítí.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Mi může sdělit na místních serverech při připojení pomocí ExpressRoute nebo VPN tunely?

Ano, tak dlouho, dokud je povolený provoz.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Jeden back-endový fond může sloužit mnoho aplikací na jiném portu?

Architektura Mikroslužeb se podporuje. Testovat na jiném portu, musíte nakonfigurovat více nastavení HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Vlastní sondy podporují zástupných znaků nebo regulární výraz v odpovědi data?

Ne. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Jak se zpracovávají pravidla směrování ve službě Application Gateway?

Zobrazit [pořadí zpracování pravidla](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Pro vlastní sondy co pole hostitele místo?

Pole hostitel Určuje název, který chcete odeslat testu a pokud jste nakonfigurovali ve víc lokalitách ve službě Application Gateway. Jinak použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního počítače. Má formát \<protokol\>://\<hostitele\>:\<port\>\<cesta\>.

### <a name="can-i-whitelist-application-gateway-access-to-only-a-few-source-ips"></a>Je možné seznamu povolených IP adres službě Application Gateway přístup k jenom pár zdrojové IP adresy?

Ano. Zobrazit [omezení přístupu ke konkrétní zdrojové IP adresy](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Můžete použít stejný port pro naslouchací procesy veřejná i privátní přístupem?

Ne.

## <a name="configuration---ssl"></a>Konfigurace – SSL

### <a name="what-certificates-does-application-gateway-support"></a>Jaké certifikáty podporuje Application Gateway?

Služba Application Gateway podporuje certifikáty podepsané svým držitelem, certifikáty od certifikační autority (CA), rozšířené ověřování (rozšířené) certifikáty a certifikáty se zástupnými znaky.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Jaké šifrovací sady nemá Application Gateway podporovat?

Služba Application Gateway podporuje následující šifrovací sada. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Informace o tom, jak přizpůsobit možnosti SSL najdete v tématu [verze zásad konfigurace protokolu SSL a šifrovací sady ve službě Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Služba Application Gateway podporuje šifrování provozu do back-end?

Ano. Služba Application Gateway podporuje přesměrování zpracování SSL a -kompletního protokolu SSL, které šifrovaly provoz do back-endu.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Můžete nakonfigurovat zásady protokolu SSL pro ovládací prvek verze protokolu SSL?

Ano. Můžete nakonfigurovat Application Gateway pro protokol TLS 1.0, TLS1.1 a TLS1.2 odepření. Ve výchozím nastavení protokol SSL 2.0 a 3.0 jsou již zakázané a nejsou konfigurovatelné.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Můžete nakonfigurovat šifrovací sady a pořadí zásad?

Ano. Ve službě Application Gateway je možné [konfiguraci šifrovací sady](application-gateway-ssl-policy-overview.md). K definování vlastní zásady, povolte alespoň jeden z následujících sad šifer. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Služba Application Gateway používá SHA256 do back-end správy.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Jak velký počet certifikátů SSL Application Gateway podporuje?

Služba Application Gateway podporuje až 100 certifikáty SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Kolik ověřovacích certifikátů pro šifrování back-end služba Application Gateway podporuje?

Služba Application Gateway podporuje až 10 certifikáty pro ověřování. Výchozí hodnota je 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway nativní integrace se službou Azure Key Vault?

Ne.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Jak nakonfigurovat naslouchací procesy HTTPS pro weby .com a .net? 

Pro více založený na doméně (hostitel) směrování na základě, můžete vytvořit ve více lokalitách naslouchacích procesů, nastavit naslouchací procesy, které používá protokol HTTPS a přidružit naslouchací procesy pravidel směrování. Další informace najdete v tématu [hostování více webů s využitím Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfigurace – firewall webových aplikací (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Nabízí WAF SKU všechny funkce dostupné ve standardním SKU?

Ano. WAF podporuje všechny funkce ve standardním SKU.

### <a name="which-crs-versions-does-application-gateway-support"></a>Jaké verze CRS Application Gateway podporuje?

Služba Application Gateway podporuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Jak můžu monitorovat WAF?

Monitorování WAF prostřednictvím protokolování diagnostiky. Další informace najdete v tématu [protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Režim detekce blokovat provoz?

Ne. Režim detekce zaznamená pouze provoz, který se aktivuje pravidlo WAF.

### <a name="can-i-customize-waf-rules"></a>Můžete přizpůsobit pravidla firewallu webových aplikací?

Ano. Další informace najdete v tématu [skupin pravidel přizpůsobte WAF a pravidla](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Pravidla jsou aktuálně k dispozici pro WAF?

WAF v současné době podporuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Tato pravidla poskytují směrného plánu zabezpečení před většinou z prvních 10 ohrožení zabezpečení, které identifikuje Open Web Application zabezpečení projektu (OWASP): 

* Ochrana před útoky prostřednictvím injektáže SQL.
* Ochrana skriptování mezi weby
* Ochrana proti common webovými útoky, jako je například injektáž příkazů, pronášení, rozdělování odpovědí protokolu HTTP požadavku HTTP a útok vzdáleného souboru
* Ochrana před narušením protokolu HTTP.
* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.
* Ochrana před roboty, prohledávacími moduly a skenery.
* Detekce běžných chyb v konfiguraci aplikací (to znamená, Apache, IIS atd.)

Další informace najdete v tématu [OWASP top 10 ohrožení](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Podporuje WAF služby DDoS protection?

Ano. Můžete povolit ochranu před útoky DDoS na virtuální síť, ve kterém je nasazená Aplikační brána. Toto nastavení zajistí, že služba Azure DDoS Protection chrání také application gateway virtuální IP (VIP).

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Jaké typy protokolů Application Gateway poskytuje?

Application Gateway poskytuje tří protokolů: 

* **ApplicationGatewayAccessLog**: Přístup k protokolu obsahuje každý požadavek odeslána k application gateway front-endu. Data obsahují dovnitř a ven volajícího IP adresy URL vyžádané, latence odpovědi, návratový kód a bajtů. Přístup k protokolu shromažďovaných každých 300 sekund. Obsahuje jeden záznam na aplikační bránu.
* **ApplicationGatewayPerformanceLog**: V protokolu výkonu zaznamenává informace o výkonu pro každá služba application gateway. Informace zahrnují propustnost v bajtech, celkový počet požadavků obsluhovat, počet neúspěšných požadavků a počet instancí funkčních a nefunkčních back-endu.
* **ApplicationGatewayFirewallLog**: Pro brány application Gateway, konfigurují s WAF brány firewall protokolu obsahuje požadavky, které jsou zaznamenány pomocí detekce režim nebo režim ochrany před únikem informací.

Další informace najdete v tématu [back-endu stav, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Jak poznám, že pokud jsou v pořádku Moje členů fondu back-end?

Ověření stavu pomocí rutiny prostředí PowerShell `Get-AzApplicationGatewayBackendHealth` nebo na portálu. Další informace najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Co jsou zásady uchovávání informací pro diagnostické protokoly?

Diagnostické protokoly toku do účtu úložiště zákazníka. Zákazníci můžou nastavit zásady uchovávání informací podle jejich priority. Diagnostické protokoly také odesílat do centra událostí nebo protokoly Azure monitoru. Další informace najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Jak získám protokoly auditu pro službu Application Gateway?

Na portálu v okně nabídky služby application gateway, vyberte **protokolu aktivit** pro přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Můžete nastavit výstrahy se službou Application Gateway?

Ano. Ve službě Application Gateway se konfigurují na metriky. Další informace najdete v tématu [metrik Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) a [doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Jak analyzovat statistiku provozu pro službu Application Gateway?

Můžete zobrazit a analyzovat protokoly přístup několika způsoby. Použijte protokoly Azure monitoru, Excel, Power BI a tak dále.

Můžete také použít šablony Resource Manageru, který nainstaluje a spustí Oblíbené [GoAccess](https://goaccess.io/) protokolu analyzátoru pro Application Gateway přístup k protokolům. GoAccess poskytuje cenné statistiku provozu HTTP jako je například jedinečných návštěvníků, požadované soubory, hostitele, operačních systémů, prohlížečů a stavové kódy HTTP. Další informace najdete na webu GitHub, najdete v článku [souboru Readme do složky šablony Resource Manageru](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Co může způsobit, že stav back-endu vrátit Neznámý stav?

Neznámý stav obvykle, uvidíte po zablokování přístupu k back-end je skupina zabezpečení sítě (NSG), vlastní DNS nebo směrování definovaného uživatelem (UDR) na podsítě služby application gateway. Další informace najdete v tématu [back-endu stav, protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Další postup

Další informace o službě Application Gateway najdete v tématu [co je Azure Application Gateway?](overview.md).
