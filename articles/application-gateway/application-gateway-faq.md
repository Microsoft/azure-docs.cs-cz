---
title: Nejčastější dotazy týkající se brány aplikací Azure
description: Najděte odpovědi na nejčastější dotazy týkající se Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: d9691a6fd5c320242b9677776cbd08be4f800921
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544510"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Nejčastější dotazy týkající se brány aplikace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Níže jsou uvedeny běžné otázky týkající se Azure Application Gateway.

## <a name="general"></a>Obecné

### <a name="what-is-application-gateway"></a>Co je Application Gateway?

Azure Application Gateway poskytuje řadič pro doručování aplikací (ADC) jako službu. Nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Tato služba je vysoce dostupná, škálovatelná a plně spravovaná Azure.

### <a name="what-features-does-application-gateway-support"></a>Jaké funkce aplikace gateway podporuje?

Aplikační brána podporuje automatické škálování, snižování zátěže SSL a end-to-end SSL, bránu firewall webové aplikace (WAF), spřažení relací založenou na souborech cookie, směrování na základě cesty URL, hostování ve více sítích a další funkce. Úplný seznam podporovaných funkcí naleznete [v tématu Úvod do aplikační brány](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Jak se liší aplikační brána a Azure Load Balancer?

Aplikační brána je nástroj pro vyrovnávání zatížení vrstvy 7, což znamená, že funguje pouze s webovým provozem (HTTP, HTTPS, WebSocket a HTTP/2). Podporuje funkce, jako je například ukončení SSL, spřažení relací na základě souborů cookie a kruhové dotazování pro přenos vyrovnávání zatížení. Balancer zatížení vyrovnává provoz na úrovni 4 (TCP nebo UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jaké protokoly aplikace gateway podporuje?

Aplikační brána podporuje http, https, http/2 a websocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak aplikační brána podporuje protokol HTTP/2?

Viz [podpora HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Jaké prostředky jsou podporovány jako součást back-endového fondu?

Viz [podporované back-endové prostředky](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>V jakých oblastech je aplikační brána k dispozici?

Aplikační brána je dostupná ve všech oblastech globálního Azure. Je taky dostupná v [Azure China 21Vianet](https://www.azure.cn/) a [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Je toto nasazení vyhrazené pro mé předplatné, nebo je sdíleno mezi zákazníky?

Aplikační brána je vyhrazené nasazení ve vaší virtuální síti.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Podporuje aplikační brána přesměrování HTTP-HTTPS?

Přesměrování je podporováno. Viz [Přehled přesměrování brány aplikace](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>V jakém pořadí jsou posluchači zpracovány?

Viz [pořadí zpracování posluchače](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Kde najdu IP a DNS aplikační brány?

Pokud jako koncový bod používáte veřejnou IP adresu, najdete informace o IP a DNS v prostředku veřejné IP adresy. Nebo ji najděte na portálu na stránce s přehledem pro aplikační bránu. Pokud používáte interní IP adresy, najděte informace na stránce s přehledem.

Pro skladovou položku v2 otevřete veřejný prostředek IP a vyberte **možnost Konfigurace**. Pole **Popisek názvu DNS (volitelné)** je k dispozici pro konfiguraci názvu DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Jaké jsou nastavení pro keep-alive časový limit a TCP časový limit nečinnosti?

*Časový limit keep-alive* určuje, jak dlouho bude aplikační brána čekat, až klient odešle další požadavek HTTP na trvalé připojení, než jej znovu použije nebo zavře. *Časový limit nečinnosti protokolu TCP* určuje, jak dlouho je připojení TCP v případě žádné aktivity otevřeno. 

*Časový limit Keep-Alive* v aplikační bráně v1 SKU je 120 sekund a ve sku v2 je to 75 sekund. *Časový limit nečinnosti protokolu TCP* je výchozí 4 minutna front-endové virtuální IP adresy (VIP) v1 a v2 skladové položky aplikační brány. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Mění se název IP nebo DNS po dobu životnosti aplikační brány?

V sku aplikace V1 virtuální ip může změnit, pokud zastavíte a spustíte bránu aplikace. Ale název DNS přidružený k bráně aplikace se během životnosti brány nezmění. Vzhledem k tomu, že se název DNS nemění, měli byste použít alias CNAME a nasměrovat ho na adresu DNS aplikační brány. Ve skladové jednotce SKU aplikace V2 můžete nastavit adresu IP jako statickou, takže název IP a DNS se během doby životnosti aplikační brány nezmění. 

### <a name="does-application-gateway-support-static-ip"></a>Podporuje aplikační brána statickou IP adresu?

Ano, aplikační brána v2 skladová položka podporuje statické veřejné IP adresy. Skladová položka v1 podporuje statické interní IP adresy.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Podporuje aplikační brána v bráně více veřejných IP služeb?

Aplikační brána podporuje pouze jednu veřejnou IP adresu.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak velký mám vytvořit svou podsíť pro aplikační bránu?

Viz [Aspekty velikosti podsítě Aplikační brána](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Můžu do jedné podsítě nasadit více prostředků aplikační brány?

Ano. Kromě více instancí daného nasazení aplikační brány můžete zřídit jiný jedinečný prostředek aplikační brány do existující podsítě, která obsahuje jiný prostředek aplikační brány.

Jedna podsíť nemůže podporovat Standard_v2 i standardní aplikační bránu společně.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Podporuje aplikační brána v2 uživatelem definované trasy (UDR)?

Ano, ale pouze konkrétní scénáře. Další informace naleznete v tématu [Přehled konfigurace aplikační brány](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Podporuje aplikační brána záhlaví x-forwarded-for?

Ano. Viz [Změny požadavku](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Jak dlouho trvá nasazení aplikační brány? Bude moje aplikační brána fungovat, když se aktualizuje?

Nové nasazení sku aplikace v1 může trvat až 20 minut. Změny velikosti nebo počtu instancí nejsou rušivé a brána zůstane aktivní během této doby.

Většina nasazení, které používají v2 Skladové položky trvat přibližně 6 minut na zřízení. V závislosti na typu nasazení však může trvat déle. Například nasazení ve více zónách dostupnosti s mnoha instancemi může trvat déle než 6 minut. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Můžu použít Exchange Server jako back-end s aplikační bránou?

Ne. Aplikační brána nepodporuje e-mailové protokoly, jako je SMTP, IMAP a POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Je k dispozici pokyny pro migraci z skladové položky v1 do sku v2?

Ano. Podrobnosti najdete v [tématu Migrace brány aplikací Azure a brány firewall webových aplikací z v1 na 2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Bude aplikační brána v1 skladová položka nadále podporována?

Ano. Skladová položka brány aplikace v1 bude nadále podporována. Důrazně se však doporučuje přejít na v2, abyste využili výhod aktualizací funkcí v této skladové jednotce. Další informace naleznete [v tématu Automatické škálování a zónově redundantní aplikační brána v2](application-gateway-autoscaling-zone-redundant.md).

## <a name="performance"></a>Výkon

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak aplikační brána podporuje vysokou dostupnost a škálovatelnost?

SKU aplikační brány v1 podporuje scénáře vysoké dostupnosti, když jste nasadili dvě nebo více instancí. Azure distribuuje tyto instance mezi aktualizační a zlomové domény, aby zajistil, že instance nevšechny selžou ve stejnou dobu. Skladová položka v1 podporuje škálovatelnost přidáním více instancí stejné brány pro sdílení zatížení.

Skladová položka v2 automaticky zajišťuje, že nové instance jsou rozloženy mezi doménami selhání a aktualizačními doménami. Pokud zvolíte redundanci zóny, nejnovější instance jsou také rozloženy mezi zóny dostupnosti a nabízejí odolnost proti selhání zón.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Jak lze dosáhnout scénáře zotavení po havárii napříč datovými centry pomocí aplikační brány?

Traffic Manager slouží k distribuci provozu mezi více aplikačních bran v různých datových centrech.

### <a name="does-application-gateway-support-autoscaling"></a>Podporuje aplikační brána automatické škálování?

Ano, aplikační brána v2 skladová položka podporuje automatické škálování. Další informace naleznete [v tématu Automatické škálování a zónově redundantní aplikační brána](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Způsobuje ruční nebo automatické škálování na výši nahoru nebo zmenšení prostoje?

Ne. Instance jsou distribuovány mezi upgradovacími doménami a doménami selhání.

### <a name="does-application-gateway-support-connection-draining"></a>Podporuje aplikační brána vyprázdnění připojení?

Ano. Můžete nastavit vyprázdnění připojení změnit členy v rámci back-endového fondu bez přerušení. Další informace naleznete v [části vypouštění připojení v application gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Je možné změnit velikost instance ze střední na velkou bez přerušení?

Ano.

## <a name="configuration"></a>Konfigurace

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Je aplikační brána vždy nasazená ve virtuální síti?

Ano. Aplikační brána se vždy nasadí v podsíti virtuální sítě. Tato podsíť může obsahovat pouze aplikační brány. Další informace naleznete v tématu [požadavky na virtuální síť a podsíť](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Může aplikační brána komunikovat s instancemi mimo svou virtuální síť nebo mimo její předplatné?

Dokud máte připojení IP, aplikační brána může komunikovat s instancemi mimo virtuální síť, ve které se nachází. Aplikační brána může také komunikovat s instancemi mimo předplatné, ve které se nachází. Pokud plánujete používat interní IP adresy jako členy back-endového fondu, použijte [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) nebo [bránu Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Můžu v podsíti aplikační brány nasadit něco jiného?

Ne. Ale můžete nasadit další aplikační brány v podsíti.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Jsou v podsíti aplikační brány podporovány skupiny zabezpečení sítě?

Viz [Skupiny zabezpečení sítě v podsíti Aplikační brána](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Podporuje podsíť aplikační brány uživatelem definované trasy?

Viz [Uživatelem definované trasy podporované v podsíti Aplikační brána](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jaké jsou limity pro aplikační bránu? Mohu tyto limity zvýšit?

Viz [Omezení aplikační brány](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Mohu současně používat aplikační bránu pro externí i interní provoz?

Ano. Aplikační brána podporuje jednu interní IP adresu a jednu externí IP adresu na aplikační bránu.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Podporuje aplikační brána partnerský vztah virtuální sítě?

Ano. Partnerský vztah virtuální sítě pomáhá vyvažovat zatížení v jiných virtuálních sítích.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Mohu mluvit s místními servery, když jsou připojeny tunely ExpressRoute nebo VPN?

Ano, pokud je povolen provoz.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Může jeden back-endový fond obsluhovat mnoho aplikací na různých portech?

Architektura mikroslužeb je podporována. Chcete-li sondovat na různých portech, je třeba nakonfigurovat více nastavení protokolu HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Podporují vlastní sondy zástupné znaky nebo regulární výraz u dat odpovědí?

Ne. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Jak se zpracovávají pravidla směrování v bráně aplikace?

Viz [Pořadí pravidel zpracování](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Co znamená pole Host pro vlastní sondy?

Pole Host určuje název, na který má být sonda odeslána, když jste nakonfigurovali více pracovištosti v aplikační bráně. V opačném případě použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního počítače. Jeho formát \<\>je\<\>protokol\<\>\<:\>hostitel : cesta k portu .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Mohu povolit přístup aplikační brány pouze k několika zdrojovým ADRESÁm IP?

Ano. Viz [omezení přístupu k určitým zdrojovým IP adresy](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Mohu použít stejný port pro veřejné i soukromé posluchače?

Ne.

### <a name="does-application-gateway-support-ipv6"></a>Podporuje aplikační brána iPv6?

Aplikační brána v2 aktuálně nepodporuje IPv6. Může pracovat ve virtuální síti s dvěma zásobníky pouze pomocí IPv4, ale podsíť brány musí být jenom s IPv4. Aplikační brána v1 nepodporuje virtuální sítě s duálním zásobníkem. 

## <a name="configuration---ssl"></a>Konfigurace - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Jaké certifikáty aplikace gateway podporuje?

Aplikační brána podporuje certifikáty podepsané svým držitelem, certifikáty certifikační autority ,, certifikáty rozšířeného ověření (EV) a certifikáty se zástupnými kódy.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Jaké šifrovací sady podporuje aplikační bránu?

Aplikační brána podporuje následující šifrovací sady. 

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

Informace o přizpůsobení možností protokolu SSL naleznete v [tématu Konfigurace verzí zásad SSL a šifrovacích sad v aplikaci Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Podporuje aplikační brána opětovné šifrování provozu do back-endu?

Ano. Aplikační brána podporuje snižování zátěže SSL a end-to-end SSL, které znovu šifrují provoz do back-endu.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Lze nakonfigurovat zásady SSL pro řízení verzí protokolu SSL?

Ano. Můžete nakonfigurovat aplikaci Gateway odepřít TLS1.0, TLS1.1 a TLS1.2. Ve výchozím nastavení jsou ssl 2.0 a 3.0 již zakázány a nelze je konfigurovat.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Mohu nakonfigurovat šifrovací sady a pořadí zásad?

Ano. V application gateway můžete [konfigurovat šifrovací sady](application-gateway-ssl-policy-overview.md). Chcete-li definovat vlastní zásady, povolte alespoň jednu z následujících šifrovacích sad. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Aplikační brána používá SHA256 pro správu back-endu.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Kolik certifikátů SSL aplikace podporuje?

Aplikační brána podporuje až 100 SSL certifikátů.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Kolik ověřovacích certifikátů pro šifrování back-endu podporuje aplikační brána?

Aplikační brána podporuje až 100 ověřovacích certifikátů.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Integruje se aplikační brána nativně s Azure Key Vault?

Ano, aplikační brána v2 sku podporuje trezor klíčů. Další informace naleznete v [tématu SSL ukončení s certifikáty trezoru klíčů](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Jak nakonfiguruji naslouchací procesy HTTPS pro weby .com a .net? 

Pro více směrování založených na doméně (založené na hostiteli) můžete vytvořit naslouchací procesy s více pracemi, nastavit naslouchací procesy, které používají protokol HTTPS jako protokol, a přidružit posluchače k pravidlům směrování. Další informace naleznete v [tématu Hostování více webů pomocí aplikace gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Mohu v hesle souboru .pfx použít speciální znaky?

Ne, v hesle souboru .pfx používejte pouze alfanumerické znaky.

## <a name="configuration---web-application-firewall-waf"></a>Konfigurace - firewall webových aplikací (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Nabízí skladová položka WAF všechny funkce dostupné ve standardní skladové jednotce?

Ano. WAF podporuje všechny funkce standardní sku.

### <a name="how-do-i-monitor-waf"></a>Jak mohu sledovat WAF?

Monitorujte WAF pomocí diagnostického protokolování. Další informace naleznete [v tématu Diagnostické protokolování a metriky pro aplikační bránu](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Blokuje režim zjišťování provoz?

Ne. Režim zjišťování zaznamenává pouze přenosy, které aktivují pravidlo WAF.

### <a name="can-i-customize-waf-rules"></a>Mohu přizpůsobit pravidla WAF?

Ano. Další informace naleznete v [tématu Customize WAF rule groups and rules](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Jaká pravidla jsou v současné době k dispozici pro WAF?

WAF v současné době podporuje CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)a [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Tato pravidla poskytují základní zabezpečení proti většině 10 nejvyšších chyb zabezpečení, které identifikuje Open Web Application Security Project (OWASP): 

* Ochrana před útoky prostřednictvím injektáže SQL.
* Ochrana skriptování mezi weby
* Ochrana proti běžným webovým útokům, jako je injektáž příkazů, pašování požadavků HTTP, rozdělení odpovědi HTTP a útok vzdáleného zahrnutí souborů
* Ochrana před narušením protokolu HTTP.
* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.
* Ochrana před roboty, prohledávacími moduly a skenery.
* Detekce běžných konfigurací aplikace (tj. Apache, IIS a tak dále)

Další informace naleznete v tématu [OWASP top-10 chyb zabezpečení](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Podporuje WAF ochranu DDoS?

Ano. Můžete povolit ochranu DDoS ve virtuální síti, kde se nasadí aplikační brána. Toto nastavení zajišťuje, že služba Azure DDoS Protection také chrání virtuální IP (VIP) brány aplikace.

## <a name="configuration---ingress-controller-for-aks"></a>Konfigurace - řadič příchozího přenosu dat pro AKS

### <a name="what-is-an-ingress-controller"></a>Co je řadič příchozího přenosu dat?

Kubernetes umožňuje `deployment` vytváření `service` a prostředek vystavit skupinu podů interně v clusteru. Chcete-li vystavit stejnou službu [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) externě, je definován prostředek, který poskytuje vyrovnávání zatížení, ukončení SSL a virtuální hosting založený na názvu.
K uspokojení `Ingress` tohoto prostředku je vyžadován řadič příchozího přenosu `Ingress` dat, který naslouchá všem změnám prostředků a konfiguruje zásady vyrovnávání zatížení.

Řadič příchozího přenosu dat aplikační brány umožňuje Azure [Application Gateway,](https://azure.microsoft.com/services/application-gateway/) který se používá jako příchozí přenos dat pro [službu Azure Kubernetes,](https://azure.microsoft.com/services/kubernetes-service/) která se také označuje jako cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Může jedna instanci řadiče příchozího přenosu dat spravovat více aplikačních bran?

V současné době jedna instance ingress řadič lze přidružit pouze k jedné bráně aplikace.

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Jaké typy protokolů poskytuje aplikační brána?

Aplikační brána poskytuje tři protokoly: 

* **ApplicationGatewayAccessLog**: Protokol přístupu obsahuje každý požadavek odeslaný do front-endu aplikační brány. Data zahrnují IP adresu volajícího, požadovanou adresu URL, latenci odezvy, návratový kód a bajty dovnitř a ven. Obsahuje jeden záznam na aplikační bránu.
* **ApplicationGatewayPerformanceLog**: Protokol výkonu zachycuje informace o výkonu pro každou aplikační bránu. Informace zahrnují propustnost v bajtů, celkový počet doručených požadavků, počet neúspěšných požadavků a počet back-endových instancí v pořádku a není v pořádku.
* **ApplicationGatewayFirewallLog**: Pro aplikační brány, které nakonfigurujete pomocí WAF, obsahuje protokol brány firewall požadavky, které jsou protokolovány buď prostřednictvím režimu zjišťování, nebo režimu prevence.

Všechny protokoly jsou shromažďovány každých 60 sekund. Další informace naleznete [v tématu Back-endstavu, protokoly diagnostiky a metriky pro aplikační bránu](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Jak poznám, že jsou členové back-endu v pořádku?

Ověřte stav pomocí rutiny `Get-AzApplicationGatewayBackendHealth` prostředí PowerShell nebo portálu. Další informace naleznete v tématu [Diagnostika aplikační brány](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Jaké jsou zásady uchovávání informací pro diagnostické protokoly?

Diagnostické protokoly toku do účtu úložiště zákazníka. Zákazníci mohou nastavit zásady uchovávání informací na základě jejich předvoleb. Diagnostické protokoly lze také odeslat do centra událostí nebo protokoly Azure Monitor. Další informace naleznete v tématu [Diagnostika aplikační brány](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Jak získám protokoly auditu pro aplikační bránu?

Na portálu vyberte v okně nabídky aplikační brány **protokol aktivit,** abyste měli přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Můžu nastavit upozornění pomocí brány aplikace?

Ano. V application gateway jsou výstrahy konfigurovány na metriky. Další informace najdete v [tématu metriky aplikační brány](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) a [přijímat upozornění](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Jak lze analyzovat statistiky provozu pro aplikační bránu?

Protokoly přístupu můžete zobrazit a analyzovat několika způsoby. Používejte protokoly Azure Monitoru, Excel, Power BI a tak dále.

Můžete také použít šablonu Správce prostředků, která nainstaluje a spustí populární analyzátor protokolů [GoAccess](https://goaccess.io/) pro protokoly přístupu k aplikační bráně. GoAccess poskytuje cenné statistiky provozu HTTP, jako jsou jedinečné návštěvníky, požadované soubory, hostitelé, operační systémy, prohlížeče a stavové kódy HTTP. Další informace naleznete v GitHubu v [souboru Readme ve složce šablony Správce prostředků](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Co by mohlo způsobit, že stav back-endu vrátí neznámý stav?

Obvykle se zobrazí neznámý stav, když je přístup k back-endu blokován skupinou zabezpečení sítě (NSG), vlastním serverem DNS nebo uživatelem definovaným směrováním (UDR) v podsíti aplikační brány. Další informace naleznete [v tématu Back-endstavu, protokolování diagnostiky a metriky pro aplikační bránu](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Existuje nějaký případ, kdy protokoly toku nsg nezobrazí povolený provoz?

Ano. Pokud se konfigurace shoduje v následujícím scénáři, neuvidíte povolený provoz v protokolech toku nsg:
- Nasadili jste aplikační bránu v2
- V podsíti aplikační brány máte skupinu síťových sítí.
- Povolili jste protokoly toku nsg na tomto souboru nsg

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Jak se používá aplikační brána V2 pouze s privátní ip adresou?

Aplikační brána V2 v současné době nepodporuje pouze soukromý režim IP. Podporuje následující kombinace
* Privátní IP adresa a veřejná IP adresa
* Pouze veřejná IP adresa

Ale pokud chcete používat Aplikační bránu V2 pouze s privátní IP, můžete postupovat podle níže uvedeného procesu:
1. Vytvoření aplikační brány s veřejnou i privátní ip adresou front-endu
2. Nevytvářejte žádné naslouchací procesy pro veřejnou front-endovou IP adresu. Aplikační brána nebude poslouchat žádný provoz na veřejné IP adrese, pokud pro ni nejsou vytvořeny žádné naslouchací procesy.
3. Vytvořte a připojte [skupinu zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview) pro podsíť Aplikační brána s následující konfigurací v pořadí podle priority:
    
    a. Povolit provoz ze zdroje jako značku **služby GatewayManager** a cíl jako **libovolný** a cílový port jako **65200-65535**. Tento rozsah portů je vyžadován pro komunikaci infrastruktury Azure. Tyto porty jsou chráněny (uzamčeny) ověřováním certifikátů. Externí entity, včetně správců uživatelů brány, nemohou iniciovat změny v těchto koncových bodech bez příslušných certifikátů.
    
    b. Povolit provoz ze zdroje jako značku **služby AzureLoadBalancer** a cílový port jako **libovolný**
    
    c. Odepřít veškerý příchozí provoz ze zdroje jako značku **služby Internet** a cílový port jako **libovolný**. Přiřazuji tomuto pravidlu *nejnižší prioritu* v příchozích pravidlech.
    
    d. Zachovat výchozí pravidla, jako je povolení příchozí virtuální sítě tak, aby nebyl blokován přístup k privátní IP adrese
    
    e. Odchozí připojení k internetu nelze blokovat. V opačném případě budete čelit problémům s protokolováním, metrikami a tak dále.

Ukázková konfigurace skupiny zabezpečení sítě ![pro přístup pouze k privátní IP adrese: Konfigurace nsg aplikační brány V2 pouze pro soukromý přístup k IP adresám](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Podporuje soubor cookie spřažení aplikační brány atribut SameSite?
Ano, aktualizace [prohlížeče Chromium](https://www.chromium.org/Home) [v80](https://chromiumdash.appspot.com/schedule) zavedla mandát pro http cookies bez atributu SameSite, který má být považován za SameSite =Lax. To znamená, že soubor cookie spřažení aplikační brány nebude odeslán prohlížečem v kontextu třetí strany. Pro podporu tohoto scénáře, Application Gateway vloží další soubor cookie s názvem *ApplicationGatewayAffinityCORS* kromě existující soubor cookie *ApplicationGatewayAffinity.*  Tyto soubory cookie jsou podobné, ale soubor cookie *ApplicationGatewayAffinityCORS* má další dva atributy: *SameSite=None; Zabezpečte*. Tyto atributy udržovat rychlé relace i pro požadavky napříč původem. Další informace naleznete v [části spřažení založené na souborech cookie.](configuration-overview.md#cookie-based-affinity)

## <a name="next-steps"></a>Další kroky

Další informace o aplikační bráně najdete v tématu [Co je Azure Application Gateway?](overview.md).
