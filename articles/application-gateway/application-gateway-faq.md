---
title: Nejčastější dotazy k Azure Application Gateway
description: Přečtěte si odpovědi na nejčastější dotazy k Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: c93198848058bad8c9af6903cc68253e71e2d668
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996645"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Nejčastější dotazy týkající se Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následují Nejčastější dotazy týkající se Azure Application Gateway.

## <a name="general"></a>Obecné

### <a name="what-is-application-gateway"></a>Co je Application Gateway?

Azure Application Gateway poskytuje jako službu řadič pro doručování aplikací (ADC). Nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Tato služba je vysoce dostupná, škálovatelná a plně spravovaná pomocí Azure.

### <a name="what-features-does-application-gateway-support"></a>Jaké funkce Application Gateway podporují?

Application Gateway podporuje automatické škálování, snižování zátěže SSL a kompletní protokol SSL, Firewall webových aplikací (WAF), spřažení relací na základě souborů cookie, směrování na základě cesty URL, hostování ve více lokalitách a další funkce. Úplný seznam podporovaných funkcí najdete v tématu [Úvod do Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Jak se liší Application Gateway a Azure Load Balancer?

Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7, což znamená, že funguje jenom s webovým provozem (HTTP, HTTPS, WebSocket a HTTP/2). Podporuje funkce, jako je ukončení protokolu SSL, spřažení relace na základě souborů cookie a kruhové dotazování pro provoz vyrovnávání zatížení. Load Balancer zatížení vyrovnává zatížení ve vrstvě 4 (TCP nebo UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jaké protokoly Application Gateway podporují?

Application Gateway podporuje HTTP, HTTPS, HTTP/2 a WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak Application Gateway podporuje HTTP/2?

Viz [Podpora protokolu HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Jaké prostředky se podporují jako součást fondu back-endu?

Viz [podporované prostředky back-endu](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>V jakých oblastech je Application Gateway k dispozici?

Application Gateway je k dispozici ve všech oblastech globální služby Azure. Je také k dispozici v [Azure Čína 21Vianet](https://www.azure.cn/) a [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Je toto nasazení vyhrazeno pro moje předplatné nebo je sdíleno mezi zákazníky?

Application Gateway je ve vaší virtuální síti vyhrazené nasazení.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Podporuje Application Gateway přesměrování od protokolu HTTP do HTTPS?

Přesměrování je podporováno. Přečtěte si téma [Přehled přesměrování Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>V jakém pořadí jsou naslouchací procesy zpracovávány?

Podívejte se na [pořadí zpracování naslouchacího procesu](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Kde najdu Application Gateway IP a DNS?

Pokud jako koncový bod používáte veřejnou IP adresu, najdete informace o IP adrese a DNS na prostředku veřejné IP adresy. Můžete ji také najít na portálu na stránce Přehled pro aplikační bránu. Pokud používáte interní IP adresy, vyhledejte informace na stránce Přehled.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Jaká jsou nastavení časového limitu pro zachování a časový limit nečinnosti protokolu TCP?

 V SKU Application Gateway v1 má časový limit zachování 120 sekund. Časový limit zachování pro SKU v2 je 75 sekund. Časový limit nečinnosti protokolu TCP je výchozí 4 minuty na front-endové virtuální IP adrese (VIP) Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Mění se IP adresa nebo název DNS po celou dobu životnosti služby Application Gateway?

V případě SKU Application Gateway V1 se virtuální IP adresa může změnit, pokud zastavíte a spustíte Aplikační bránu. Název DNS přidružený k aplikační bráně se ale po dobu života brány nezmění. Vzhledem k tomu, že se název DNS nemění, měli byste použít alias CNAME a nasměrovat ho na adresu DNS služby Application Gateway. V případě SKU Application Gateway v2 můžete IP adresu nastavit jako statickou, takže se IP adresa a název DNS se po celou dobu životnosti služby Application Gateway nezmění. 

### <a name="does-application-gateway-support-static-ip"></a>Podporuje Application Gateway statickou IP adresu?

Ano, SKU Application Gateway v2 podporuje statické veřejné IP adresy. SKU v1 podporuje statické interní IP adresy.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Podporuje Application Gateway více veřejných IP adres v bráně?

Aplikační brána podporuje jenom jednu veřejnou IP adresu.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak velký má být podsíť pro Application Gateway?

Informace najdete v tématu [Application Gateway hlediska velikosti podsítě](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Můžu nasadit více než jeden prostředek Application Gateway do jedné podsítě?

Ano. Kromě více instancí daného nasazení Application Gateway můžete zřídit jiný jedinečný Application Gateway prostředek pro existující podsíť, která obsahuje jiný prostředek Application Gateway.

Jedna podsíť nemůže podporovat současně Standard_v2 i standardní Application Gateway.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Podporuje Application Gateway hlaviček předávaných přes x?

Ano. Viz [Úpravy žádosti](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Jak dlouho trvá nasazení aplikační brány? Bude moje Aplikační brána fungovat, i když se aktualizuje?

Zřizování nových SKU Application Gateway v1 může trvat až 20 minut. Změny velikosti nebo počtu instancí nejsou rušivé a brána zůstane během této doby aktivní.

Zřizování většiny nasazení, která používají SKU v2, trvá přibližně 6 minut. Může ale trvat delší dobu v závislosti na typu nasazení. Například nasazení v různých Zóny dostupnosti s mnoha instancemi může trvat více než 6 minut. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Můžu použít Exchange Server jako back-end s Application Gateway?

Ne. Application Gateway nepodporuje e-mailové protokoly, jako jsou SMTP, IMAP a POP3. 

## <a name="performance"></a>Výkon

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak Application Gateway podporuje vysokou dostupnost a škálovatelnost?

SKU Application Gateway v1 podporuje scénáře s vysokou dostupností, pokud jste nasadili dvě nebo víc instancí. Azure distribuuje tyto instance napříč doménami aktualizace a selhání, aby se zajistilo, že se instance ve stejnou dobu nedaří. SKU v1 podporuje škálovatelnost přidáním více instancí stejné brány pro sdílení zatížení.

SKU v2 automaticky zajišťuje, že se nové instance rozprostře mezi doménami selhání a aktualizačními doménami. Pokud zvolíte redundanci zóny, nejnovější instance se také rozšíří napříč zónami dostupnosti, aby nabízely odolnost v oblasti selhání.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Návody dosáhnout scénáře zotavení po havárii napříč datacentry pomocí Application Gateway?

Použijte Traffic Manager k distribuci provozu mezi několik aplikačních bran v různých datových centrech.

### <a name="does-application-gateway-support-autoscaling"></a>Podporuje Application Gateway automatické škálování?

Ano, skladová položka Application Gateway v2 podporuje automatické škálování. Další informace najdete v tématu Automatické [škálování a redundantní Application Gateway v zóně](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Způsobuje ruční horizontální navýšení nebo snížení kapacity při výpadku?

Ne. Instance se distribuují napříč doménami upgradu a doménami selhání.

### <a name="does-application-gateway-support-connection-draining"></a>Podporuje Application Gateway vyprazdňování připojení?

Ano. Můžete nastavit vyprazdňování připojení pro změnu členů ve fondu back-end bez přerušení. Další informace najdete v [části vyprazdňování připojení Application Gateway](overview.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Můžu změnit velikost instance z středně na velká bez přerušení?

Ano. Azure distribuuje instance napříč doménami aktualizace a selhání, aby se zajistilo, že instance selžou ve stejnou dobu. Application Gateway podporuje škálování přidáním více instancí stejné brány pro sdílení zatížení.

## <a name="configuration"></a>Konfigurace

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Je Application Gateway vždycky nasazené ve virtuální síti?

Ano. Application Gateway je vždy nasazena v podsíti virtuální sítě. Tato podsíť může obsahovat jenom aplikační brány. Další informace najdete v tématu [požadavky na virtuální síť a podsíť](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Může Application Gateway komunikovat s instancemi mimo svou virtuální síť nebo mimo její předplatné?

Pokud máte připojení IP, Application Gateway můžou komunikovat s instancemi mimo virtuální síť, ve které je. Application Gateway může také komunikovat s instancemi mimo předplatné, ve kterém je. Pokud máte v úmyslu používat jako členy fondu back-end interní IP adresy, použijte [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) nebo [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Můžu v podsíti aplikační brány nasazovat cokoli jiného?

Ne. V podsíti ale můžete nasadit i jiné aplikační brány.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Jsou skupiny zabezpečení sítě podporované v podsíti služby Application Gateway?

Viz téma [skupiny zabezpečení sítě v podsíti Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Podporuje podsíť služby Application Gateway trasy definované uživatelem?

Viz [trasy definované uživatelem podporované v Application Gateway podsíti](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jaká jsou omezení pro Application Gateway? Můžu tato omezení zvýšit?

Viz [omezení Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Můžu současně Application Gateway použít pro externí i interní provoz?

Ano. Application Gateway podporuje jednu interní IP adresu a jednu externí IP adresu pro bránu aplikace.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Podporuje Application Gateway partnerský vztah virtuálních sítí?

Ano. Partnerské vztahy virtuálních sítí pomáhají vyrovnávat zatížení v jiných virtuálních sítích.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Můžu komunikovat s místními servery, když jsou připojeni pomocí tunelů ExpressRoute nebo VPN?

Ano, pokud je povolený provoz.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Může jeden back-end fond poskytovat mnoho aplikací na různých portech?

Architektura mikroslužeb je podporovaná. Chcete-li provést test na různých portech, je nutné nakonfigurovat více nastavení protokolu HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Podporují vlastní testy zástupné znaky nebo regulární výrazy v datech odpovědí?

Ne. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Jak se zpracovávají pravidla směrování v Application Gateway?

Viz [pořadí pravidel zpracování](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Pro vlastní testy, co pole hostitel označuje?

Pole hostitel Určuje název, do kterého se má odeslat sonda při konfiguraci nasazení ve více lokalitách na Application Gateway. Jinak použijte 127.0.0.1. Tato hodnota se liší od názvu hostitele virtuálního počítače. Formát je \<protokolu\>://\<hostitel\>:\<port\>\<cesta\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Můžu Application Gateway přístup k jenom několika zdrojovým IP adresám?

Ano. Viz [omezení přístupu na konkrétní zdrojové IP adresy](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Můžu použít stejný port pro veřejné i privátní naslouchací procesy?

Ne.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Je k dispozici návod k migraci z SKU V1 na SKU 2?

Ano. Podrobnosti najdete v tématu [migrace služby Azure Application Gateway a firewall webových aplikací z verze V1 na verzi v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Konfigurace-SSL

### <a name="what-certificates-does-application-gateway-support"></a>Jaké certifikáty Application Gateway podporují?

Application Gateway podporuje certifikáty podepsané svým držitelem, certifikáty certifikační autority (CA), rozšířené ověřování (EV) certifikátů a certifikáty se zástupnými znaky.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Jaké šifrovací sady podporuje Application Gateway?

Application Gateway podporuje následující šifrovací sady. 

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

Informace o tom, jak přizpůsobit možnosti SSL, najdete v tématu [Konfigurace verzí zásad protokolu SSL a šifrovacích sad na Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Podporuje Application Gateway šifrování provozu do back-endu?

Ano. Application Gateway podporuje snižování zátěže SSL a komplexní SSL, které přešifrují provoz na back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Můžu nakonfigurovat zásady protokolu SSL pro řízení verzí protokolu SSL?

Ano. Application Gateway můžete nakonfigurovat tak, aby odepřely protokol TLS 1.0, TLS 1.1 a TLS 1.2. Ve výchozím nastavení jsou SSL 2,0 a 3,0 už zakázané a nedají se konfigurovat.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Můžu nakonfigurovat šifrovací sady a pořadí zásad?

Ano. V Application Gateway můžete [nakonfigurovat šifrovací sady](application-gateway-ssl-policy-overview.md). Pokud chcete definovat vlastní zásadu, povolte aspoň jednu z následujících šifrovacích sad. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway používá SHA256 ke správě back-endu.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Kolik certifikátů SSL Application Gateway podporuje?

Application Gateway podporuje až 100 certifikátů SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Kolik ověřovacích certifikátů pro opakované šifrování back-endu Application Gateway podporu?

Application Gateway podporuje až 100 ověřovacích certifikátů.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Je Application Gateway nativně integrována s Azure Key Vault?

Ano, skladová položka Application Gateway v2 podporuje Key Vault. Další informace najdete v tématu [ukončení SSL pomocí certifikátů Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Návody nakonfigurovat naslouchací procesy protokolu HTTPS pro weby. com a .NET? 

U více směrování založených na doméně (hostitele) můžete vytvořit více než více lokalit, nastavit naslouchací procesy, které používají protokol HTTPS jako protokol, a přidružit naslouchací procesy k pravidlům směrování. Další informace najdete v tématu [hostování více lokalit pomocí Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Můžu v heslu souboru. pfx použít speciální znaky?

Ne, v heslech souboru. pfx použijte pouze alfanumerické znaky.

## <a name="configuration---web-application-firewall-waf"></a>Konfigurace – Firewall webových aplikací (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Nabízí SKU WAF všechny funkce, které jsou k dispozici ve standardní SKU?

Ano. WAF podporuje všechny funkce v SKU Standard.

### <a name="how-do-i-monitor-waf"></a>Návody monitorování WAF?

Monitorujte WAF prostřednictvím diagnostického protokolování. Další informace najdete v tématu [protokolování diagnostiky a metriky pro Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Blokuje režim detekce přenos dat?

Ne. V režimu detekce se protokoluje pouze provoz, který aktivuje pravidlo WAF.

### <a name="can-i-customize-waf-rules"></a>Můžu přizpůsobit pravidla WAF?

Ano. Další informace najdete v tématu [přizpůsobení skupin pravidel a pravidel pro WAF](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Jaká pravidla jsou aktuálně k dispozici pro WAF?

WAF v současné době podporuje počítačový [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)a [3,1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Tato pravidla poskytují základní zabezpečení proti většině nejčastějších chyb zabezpečení, které otevřou projekt zabezpečení webových aplikací (OWASP): 

* Ochrana před útoky prostřednictvím injektáže SQL.
* Ochrana skriptování mezi weby
* Ochrana před běžnými webovými útoky, jako je vkládání příkazů, podvržení požadavků HTTP, rozdělování odpovědí HTTP a útok na vzdálené zahrnutí souborů
* Ochrana před narušením protokolu HTTP.
* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.
* Ochrana před roboty, prohledávacími moduly a skenery.
* Detekce běžných neobvyklých konfigurací aplikací (tj. Apache, IIS atd.)

Další informace najdete v tématu [OWASP chyby zabezpečení nejvyšší úrovně 10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Podporuje WAF ochranu DDoS?

Ano. Službu DDoS Protection můžete povolit ve virtuální síti, ve které je nasazená aplikační brána. Toto nastavení zajistí, aby služba Azure DDoS Protection chránila také virtuální IP adresu aplikační brány.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Je k dispozici návod k migraci z SKU V1 na SKU 2?

Ano. Podrobnosti najdete v tématu [migrace služby Azure Application Gateway a firewall webových aplikací z verze V1 na verzi v2](migrate-v1-v2.md).

## <a name="configuration---ingress-controller-for-aks"></a>Konfigurace – řadič pro příchozí přenosy pro AKS

### <a name="what-is-an-ingress-controller"></a>Co je kontroler příchozího přenosu dat?

Kubernetes umožňuje vytvoření `deployment` a `service` prostředků k internímu vystavení skupiny lusků v clusteru. Pokud chcete stejnou službu vystavit externě, je definován [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) prostředek, který poskytuje vyrovnávání zatížení, ukončení protokolu SSL a virtuální hostování na základě názvu.
Aby bylo možné splnit tento prostředek `Ingress`, vyžaduje se kontroler příchozího přenosu dat, který naslouchá jakýmkoli změnám `Ingress` prostředků a konfiguruje zásady nástroje pro vyrovnávání zatížení.

Kontroler Application Gateway příchozího přenosu dat umožňuje použití [azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) jako příchozí pro [službu Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) , která se označuje také jako cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Může jediná instance kontroleru příchozího přenosu dat spravovat víc aplikačních bran?

V současné době může být jedna instance řadiče pro příchozí spojení přidružená jenom k jednomu Application Gateway.

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Jaké typy protokolů Application Gateway poskytnout?

Application Gateway poskytuje tři protokoly: 

* **ApplicationGatewayAccessLog**: protokol Access obsahuje všechny požadavky odeslané do front-endu služby Application Gateway. Mezi tato data patří IP adresa volajícího, požadovaná adresa URL, latence odezvy, návratový kód a bajtů. Protokol přístupu se shromáždí každých 300 sekund. Obsahuje jeden záznam na aplikační bránu.
* **ApplicationGatewayPerformanceLog**: protokol výkonu zachycuje informace o výkonu pro každou bránu aplikace. Informace zahrnují propustnost v bajtech, celkový počet zpracovaných požadavků, počet neúspěšných požadavků a stav back-endu, který není v pořádku.
* **ApplicationGatewayFirewallLog**: pro brány aplikací, které nakonfigurujete pomocí WAF, obsahuje protokol brány firewall požadavky, které se protokolují buď v režimu detekce, nebo v režimu prevence.

Další informace najdete v tématu [stav back-endu, diagnostické protokoly a metriky pro Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Návody zjistit, jestli jsou členové fondu back-endu v pořádku?

Ověřte stav pomocí rutiny prostředí PowerShell `Get-AzApplicationGatewayBackendHealth` nebo portálu. Další informace najdete v tématu [diagnostika Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Jaké jsou zásady uchovávání informací pro diagnostické protokoly?

Diagnostické protokoly se zaznamenávají do účtu úložiště zákazníka. Zákazníci můžou zásady uchovávání informací nastavit na základě jejich preference. Diagnostické protokoly je také možné odeslat do centra událostí nebo do protokolů Azure Monitor. Další informace najdete v tématu [diagnostika Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Návody získat protokoly auditu pro Application Gateway?

Na portálu v okně nabídky aplikační brány vyberte **Protokol aktivit** pro přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Můžu nastavit upozornění pomocí Application Gateway?

Ano. V Application Gateway jsou výstrahy nakonfigurované na metrikách. Další informace najdete v tématu [Application Gateway metriky](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) a [přijímání oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Návody analyzovat Statistiky provozu pro Application Gateway?

Protokoly přístupu můžete zobrazit a analyzovat několika způsoby. Použijte protokoly Azure Monitor, Excel, Power BI a tak dále.

Můžete také použít šablonu Správce prostředků, která nainstaluje a spustí oblíbený analyzátor protokolů [GoAccess](https://goaccess.io/) pro Application Gateway protokoly přístupu. GoAccess poskytuje cennou statistiku přenosů HTTP, například jedinečné návštěvníky, požadované soubory, hostitele, operační systémy, prohlížeče a stavové kódy HTTP. Další informace najdete na webu GitHub v [souboru Readme ve složce šablony Správce prostředků](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Co by mohlo způsobit neznámý stav back-endu?

Obvykle se zobrazuje neznámý stav, pokud je přístup k back-endu zablokovaný skupinou zabezpečení sítě (NSG), vlastním DNS nebo uživatelem definovaným směrováním (UDR) v podsíti aplikační brány. Další informace najdete v tématu [stav back-endu, protokolování diagnostiky a metriky pro Application Gateway](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Existují nějaké případy, kdy protokoly toku NSG neukazují povolený provoz?

Ano. Pokud vaše konfigurace odpovídá následujícímu scénáři, neuvidíte v protokolech toku NSG povolený provoz:
- Nasadili jste Application Gateway v2
- Máte NSGu v podsíti služby Application Gateway.
- Povolili jste protokoly toku NSG na těchto NSG.

## <a name="next-steps"></a>Další kroky

Další informace o Application Gateway najdete v tématu [co je Azure Application Gateway?](overview.md).
