---
title: Nejčastější dotazy k Azure Application Gateway
description: Tato stránka nabízí odpovědi na nejčastější dotazy týkající se Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 8c2856f41b4623519c7bcdd97b5018129f1f23cf
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092589"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Nejčastější dotazy k Application Gateway

## <a name="general"></a>Obecné

### <a name="what-is-application-gateway"></a>Co je Application Gateway?

Azure Application Gateway je Application Delivery Controller (ADC) jako službu, nabízí různé vrstvy 7 možnostmi Vyrovnávání zatížení pro vaše aplikace. Nabízí vysoce dostupné a škálovatelné služby, která je plně spravuje Azure.

### <a name="what-features-does-application-gateway-support"></a>Jaké funkce Application Gateway podporuje?

Služba Application Gateway podporuje automatické škálování, snižování zátěže protokolu SSL a kompletního protokolu SSL, Firewall webových aplikací, spřažení relace na základě souborů cookie, adresa url na základě cest směrování, hostování několika webů a ostatní. Úplný seznam podporovaných funkcí najdete v tématu [seznámení se službou Application Gateway](application-gateway-introduction.md).

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Jaký je rozdíl mezi Application Gateway a nástroje pro vyrovnávání zatížení Azure?

Služba Application Gateway je Vyrovnávání zatížení vrstvy 7, což znamená, že pracuje s pouze webový provoz (HTTP/HTTPS/WebSocket). Podporuje možnosti, jako jsou ukončení protokolu SSL, spřažení relace na základě souborů cookie a kruhové dotazování pro provoz služby Vyrovnávání zatížení. Načtěte provozu nástroje pro vyrovnávání zatížení vrstvy 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jaké protokolů Application Gateway podporuje?

Služba Application Gateway podporuje HTTP, HTTPS, HTTP/2 a protokolu WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak Application Gateway podporuje HTTP/2?

Podpora protokolu HTTP/2 je k dispozici pro připojení klientů k application gateway pouze posluchače. Komunikace do fondů back-end serveru je přes HTTP/1.1. 

Ve výchozím nastavení je zakázána podpora HTTP/2. Následující příklad fragmentu kódu prostředí Azure PowerShell ukazuje, jak můžete zajistit:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Které prostředky jsou dnes podporované jako součást back-endový fond?

Back-endové fondy se může skládat ze síťových rozhraní škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy nebo plně kvalifikované názvy (plně kvalifikovaný název domény) a více tenantů back EndY, jako je Azure Web Apps. Členy fondu back-end brány aplikace nejsou vázané na skupinu dostupnosti. Členů fondu back-end může být napříč clustery, datová centra, Azure nebo mimo něj, tak dlouho, dokud mají připojení pomocí IP adresy.

### <a name="what-regions-is-the-service-available-in"></a>Jaké oblasti je k dispozici ve službě?

Služba Application Gateway je k dispozici ve všech oblastech Azure globální. Je také k dispozici v [Azure China](https://www.azure.cn/) a [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Je to vyhrazená nasazení pro mé předplatné nebo je sdílen mezi zákazníky?

Služba Application Gateway je vyhrazené nasazení ve virtuální síti.

### <a name="is-http-https-redirection-supported"></a>Je HTTP -> přesměrování protokolu HTTPS, které jsou podporovány?

Přesměrování se nepodporuje. Zobrazit [přehled přesměrování ve službě Application Gateway](application-gateway-redirect-overview.md) Další informace.

### <a name="in-what-order-are-listeners-processed"></a>V jakém pořadí se naslouchací procesy zpracovávají?

Naslouchací procesy jsou zpracovávány v pořadí, ve kterém jsou zobrazeny. Z tohoto důvodu Pokud základní naslouchací proces odpovídá příchozí žádosti zpracovávat jej nejprve.  Naslouchací procesy Multi-Site by měl být nakonfigurovaný před základní naslouchací proces chcete zajistit, aby provoz se směruje do správného back endu.

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Kde najdu IP a DNS Application Gateway?

Při použití veřejné IP adresy jako koncový bod, tyto informace můžete najít na prostředek veřejné IP adresy nebo na stránce Přehled pro službu application gateway na portálu. Pro interní IP adresy to najdete na stránce Přehled.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Mění název IP nebo DNS během životního cyklu Application Gateway?

Virtuální IP adresu můžete změnit, pokud application gateway je zastavit a spustit. Název DNS, který je přidružený k službě application gateway nemění přes životní cyklus brány. Z tohoto důvodu se doporučuje použít CNAME alias a přejděte na adresu DNS služby application gateway.

### <a name="does-application-gateway-support-static-ip"></a>Služba Application Gateway podporuje statickou IP adresu?

Ano, SKU v2 Application Gateway podporuje statické veřejné IP adresy. V1 SKU podporuje statické interní IP adresy.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Služba Application Gateway podporuje několik veřejných IP adres v bráně?

Ve službě application gateway se podporuje jenom jednu veřejnou IP adresu.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak velké by měl udělám mou podsíť pro službu Application Gateway?

Application Gateway využívá jednu privátní IP adresu na jednu instanci a jiné privátní IP adresu, pokud je nakonfigurovaný privátní front-endovou konfiguraci IP. Azure si vyhrazuje první čtyři a poslední IP adresu v každé podsíti pro interní použití.
Například, pokud služby application gateway je nastavena na tři instance a žádné privátní front-endovou IP, pak je/29 nebo větší velikost podsítě je potřeba. V tomto případě application gateway používá tři IP adresy. Pokud máte tři instance a IP adresu pro privátní front-endovou konfiguraci protokolu IP, pak o velikosti/28 podsíť, velikost nebo vyšší je potřeba, protože čtyři IP adresy jsou povinné.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Otázka: Můžete nasadit více než jeden prostředek aplikační brány do jedné podsíti? **

Ano, kromě má více instancí dané nasazení Application Gateway, můžete zřídit jiný jedinečný prostředek aplikační brány do existující podsítě, který obsahuje jiný prostředek aplikační brány.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Služba Application Gateway podporuje hlavičky x předané pro?

Ano, služba Application Gateway vloží záhlaví x předané u, x-forwarded-proto a x předané port do žádosti předané back-endu. Formát pro x předané – pro hlavičky je čárkou oddělený seznam IP: port. Platné hodnoty pro x-forwarded-proto jsou http nebo https. X předané port Určuje port, ve kterém bylo dosaženo žádost ve službě application gateway.

Služba Application Gateway také vloží hlavičku X-původní-hostitele, která obsahuje hlavičku původního hostitele, pomocí kterého Přišla žádost. Tato hlavička se užitečné v situacích, jako jsou integrace webu Azure, ve kterém se upraví hlavičku hostitele příchozího před provoz směruje do back-endu.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Jak dlouho trvá nasazení služby Application Gateway? My Application Gateway stále funguje při aktualizaci?

Nová nasazení SKU v1 Application Gateway může trvat až 20 minut, než se zřídit. Změny velikosti a počtu instancí nejsou rušivé a během této doby zůstává aktivní brány.

V2 SKU nasazení může trvat přibližně pět až šest minut zřizování.

## <a name="configuration"></a>Konfigurace

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Služba Application Gateway vždy nasazení ve virtuální síti?

Ano, služba Application Gateway je vždy nasazeny v podsíti virtuální sítě. Tato podsíť může obsahovat jenom aplikační brány.

### <a name="can-application-gateway-communicate-with-instances-outside-its-virtual-network"></a>Služba Application Gateway komunikovat instance mimo příslušné virtuální síti?

Služba Application Gateway může komunikovat s instancí mimo virtuální síť, která probíhá, jako je připojení pomocí IP adresy. Pokud plánujete používat interní IP adresy jako členy fondu back-end, pak vyžaduje [VNET Peering](../virtual-network/virtual-network-peering-overview.md) nebo [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Je možné nasadit nic jiného v podsítě služby application gateway?

Ne, ale můžete nasadit další služby application Gateway v podsíti.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Podporují se skupinami zabezpečení sítě na podsítě služby application gateway?

Skupiny zabezpečení sítě (Nsg) podporují na podsítě služby application gateway s následujícími omezeními:

* Výjimky musí být umístěno v pro příchozí provoz na portech 65503 65534 pro službu Application Gateway v1 SKU a porty 65200 – 65 535 pro v2 SKU. Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity, včetně zákazníků těchto bran, nebude možné zahájit žádné změny na tyto koncové body.

* Odchozí připojení k Internetu, nejde blokovat.

* Musí se povolit provoz z značka AzureLoadBalancer.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Podporuje uživatelem definované trasy na podsítě služby application gateway?

Trasy definované uživatelem (udr) jsou podporované na podsítě služby application gateway, za předpokladu, nijak nemění komunikace začátku do konce žádostí a odpovědí.

Například trasu UDR v podsítě služby application gateway můžete nastavit tak, aby odkazoval na zařízení brány firewall pro kontrolu paketů, ale musíte zajistit, aby paketu přístup jeho příspěvku kontrole požadovaného cíle. K tomu může dojít nesprávný stav testu nebo provoz směrování chování. To zahrnuje zjištěné trasy nebo výchozí trasy 0.0.0.0/0 šířeny přes ExpressRoute nebo bran VPN ve virtuální síti.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jaká jsou omezení ve službě Application Gateway? Můžete tyto limity zvýšit?

Zobrazit [omezení služby Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits) zobrazíte omezení.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Můžu používat Application Gateway pro externí a interní provozu současně?

Ano, služba Application Gateway podporuje jeden interní IP adresa a jedna externí IP adresy na aplikační bránu.

### <a name="is-vnet-peering-supported"></a>VNet peering je podporováno?

Ano, VNet peering je podporován a je vhodné pro provoz v jiných virtuálních sítí pro vyrovnávání zatížení.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Mi může sdělit na místních serverech při připojení pomocí ExpressRoute nebo VPN tunely?

Ano, tak dlouho, dokud je povolený provoz.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Může mít jeden back-endový fond obsluhují mnoho aplikací na jiném portu?

Architektura Micro služby se podporuje. Potřebujete různá nastavení protokol http nakonfigurovaná testovat na jiném portu.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>Vlastní sondy podporují zástupných znaků nebo regulární výraz v odpovědi data?

Vlastní sondy nepodporují zástupných znaků nebo regulární výraz na data odpovědi.

### <a name="how-are-rules-processed"></a>Jak se zpracovávají pravidla?

Pravidla se zpracovávají v pořadí, ve kterém jsou nakonfigurované. Doporučuje se, že jsou před základních pravidel, snížíte pravděpodobnost, že provoz se směruje na nevhodný back-end jako základní pravidlo by odpovídala provoz na portu před Multi-Site pravidlo se vyhodnotí na základě nakonfigurovaná pravidla více lokalit.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>Co místo pole hostitele pro vlastní sondy?

Pole hostitel Určuje název, který chcete odeslat testu a. Použít pouze v případě více webů je nakonfigurovaná ve službě Application Gateway, v opačném případě použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního počítače a je ve formátu \<protokol\>://\<hostitele\>:\<port\>\<cesta\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Je možné seznamu povolených IP adres službě Application Gateway přístup k několika zdrojové IP adresy?

Tento scénář lze provést pomocí skupin Nsg na podsítě služby application gateway. Následující omezení by mělo být uvedeno na podsítě v uvedené pořadí podle priority:

* Umožňuje příchozí provoz ze zdrojového rozsahu IP/IP.

* Povolit příchozí žádosti ze všech zdrojů k portům 65503 65534 pro [komunikace stavu back-endu](application-gateway-diagnostics.md). Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity, včetně zákazníků těchto bran, nebude možné zahájit žádné změny na tyto koncové body.

* Povolit příchozí sondy pro Azure Load Balancer (značka AzureLoadBalancer) a příchozí přenosy virtuální sítě (značka VirtualNetwork) na [NSG](../virtual-network/security-overview.md).

* Blokovat všechna ostatní příchozí provoz s odepřít všechna pravidla.

* Povolí odchozí provoz do Internetu pro všechny cíle.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>Lze použít stejný port pro veřejné a soukromé protilehlé naslouchacích procesů?

Ne, na tuto situaci se podpora nevztahuje.

## <a name="performance"></a>Výkon

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak Application Gateway podporuje vysokou dostupnost a škálovatelnost?

SKU v1 Application Gateway podporuje scénáře vysoké dostupnosti, pokud mají nasazené nejmíň dvě instance. Azure rozděluje tyto instance mezi aktualizace a domény selhání k zajištění, že všechny instance neselže ve stejnou dobu. V1 SKU podporuje škálovatelnost přidáním více instancí stejné bráně sdílení zatížení.

V2 SKU automaticky zajistí, že nové instance jsou rozděleny mezi doménami selhání a aktualizačními doménami. Pokud je zvolená redundanci zón nejnovější instance jsou také šíří napříč zónami dostupnosti nabízí oblastmi selhání odolnost proti chybám.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Jak dosáhnu scénář zotavení po Havárii napříč datovými centry s bránou Application Gateway?

Zákazníci mohou pomocí Traffic Manageru k distribuování síťového provozu mezi více aplikačních bran v různých datových centrech.

### <a name="is-autoscaling-supported"></a>Je podporováno automatické škálování?

Ano, SKU v2 Application Gateway podporuje automatické škálování. Další informace najdete v tématu [automatické škálování a zónově redundantní služba Application Gateway (Public Preview)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Je ruční škálování nahoru/dolů příčina výpadku?

Neexistuje žádný výpadek, instance se distribuují napříč upgradovací doménami a doménami selhání.

### <a name="does-application-gateway-support-connection-draining"></a>Služba Application Gateway podporuje vyprázdnění připojení?

Ano. Můžete nakonfigurovat vyprázdnění Chcete-li změnit členy v rámci fondu back-end bez přerušení připojení. To vám umožní existující připojení a pokračujte v odešlou do jejich předchozího cíle, dokud nebude tento připojení se ukončilo nebo konfigurovatelný časový limit vyprší. Vyprázdnění připojení pouze čeká vydávaných za pochodu aktuální počet připojení k dokončení. Služba Application Gateway nemá žádné informace o stavu relace aplikace.

### <a name="what-are-application-gateway-sizes"></a>Co jsou aplikace velikosti brány?

Služba Application Gateway je v současné době nabízena ve třech velikostech: **Small** (krátkodobé používání), **Medium** (střednědobé používání) a **Large** (dlouhodobé používání). Instance krátkodobého používání jsou určené pro scénáře vývoje a testování.

V rámci jednoho předplatného můžete vytvořit až 50 služeb Application Gateway a každá z nich může mít až 10 instancí. Každá služba Application Gateway se může skládat z 20 naslouchacích procesů HTTP. Úplný seznam omezení služby Application Gateway najdete na stránce [Omezení služby Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Následující tabulka ukazuje průměrnou propustnost výkonu pro jednotlivé instance služby Application Gateway s povoleným přesměrováním zpracování SSL:

| Průměrná velikost odpovědi back endové stránky | Krátkodobé používání | Střednědobé používání | Dlouhodobé používání |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Tyto hodnoty jsou přibližné hodnoty propustnosti služby Application Gateway. Skutečná propustnost závisí na různých podrobnostech o prostředí, jako jsou například průměrná velikost stránky, umístění back-endových instancí a doba zpracování potřebná k doručení stránky. Přesné údaje o výkonu získáte, když spustíte vlastní testy. Tyto hodnoty slouží jenom jako vodítko při plánování kapacity.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Můžu změnit velikost instance ze střední a velké bez přerušení?

Ano, Azure distribuuje instancí napříč aktualizace a domény selhání k zajištění, že všechny instance neselže ve stejnou dobu. Služba Application Gateway podporuje škálování tak, že přidáte více instancí stejné bráně sdílení zatížení.

## <a name="ssl-configuration"></a>Konfigurace SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Jaké certifikáty jsou podporovány ve službě Application Gateway?

Certifikáty podepsané svým držitelem, certifikáty CA certs a zástupný znak – certifikáty jsou podporovány. Rozšířené certifikátů nejsou podporovány.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Jaké jsou aktuální šifrovací sada podporovaná službou Application Gateway?

Toto jsou aktuální šifrovací sada podporovaná službou Application Gateway. Zobrazit [verze zásad konfigurace protokolu SSL a šifrovací sady ve službě Application Gateway](application-gateway-configure-ssl-policy-powershell.md) se naučíte přizpůsobit možnosti SSL.

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Služba Application Gateway také podporuje znova šifrovat provoz do back-end?

Ano, služba Application Gateway podporuje přesměrování zpracování SSL a kompletního protokolu SSL, který znovu zašifruje provoz do back-endu.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Můžete nakonfigurovat zásady protokolu SSL pro řízení verze protokolu SSL?

Ano, můžete nakonfigurovat Application Gateway pro protokol TLS 1.0, TLS1.1 a TLS1.2 odepření. Protokol SSL 2.0 a 3.0 jsou již ve výchozím nastavení zakázána a se nedají konfigurovat.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Můžete nakonfigurovat šifrovací sady a pořadí zásad?

Ano, [konfiguraci šifrovací sady](application-gateway-ssl-policy-overview.md) je podporována. Při definování vlastní zásady, musí povolená aspoň jedna z následujících sad šifer. Služba Application gateway používá SHA256 pro správu back-endu.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Jak velký počet certifikátů SSL jsou podporovány?

Certifikáty jsou podporovány až 20 SSL.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Kolik ověřovacích certifikátů pro back-endu znovu šifrovat jsou podporovány?

Až 10 ověřování certifikáty jsou podporovány výchozí hodnota je 5.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Služba Application Gateway integrovat s Azure Key Vault nativně?

Ne, není integrovaná se službou Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfigurace brány Firewall (WAF) webové aplikace

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>Nabízí WAF SKU všechny funkce dostupné v standardní SKU?

Ano, WAF podporuje všechny funkce ve standardním SKU.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Jaká je verze CRS Application Gateway podporuje?

Služba Application Gateway podporuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Jak můžu monitorovat WAF?

WAF se monitoruje prostřednictvím protokolování diagnostiky, další informace o protokolování diagnostiky najdete v [protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Režim detekce blokovat provoz?

Režim detekce Ne, zaznamená jenom provoz, která aktivuje pravidlo WAF.

### <a name="how-do-i-customize-waf-rules"></a>Jak mohu přizpůsobit pravidla firewallu webových aplikací?

Ano, jsou přizpůsobitelné, další informace o tom, jak si je přizpůsobit, viz pravidla firewallu webových aplikací [WAF přizpůsobení skupin pravidel a pravidla](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>Pravidla jsou aktuálně k dispozici

WAF v současné době podporuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), které poskytují směrného plánu zabezpečení před většinou z 10 nejčastějších ohrožení zabezpečení, identifikovat podle otevřené webové aplikace zabezpečení projektu (OWASP) najdete tady [ OWASP top 10 ohrožení zabezpečení](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Ochrana před útoky prostřednictvím injektáže SQL.

* Ochrana před skriptováním mezi weby.

* Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

* Ochrana před narušením protokolu HTTP.

* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

* Ochrana před roboty, prohledávacími moduly a skenery.

* Detekce běžných chyb v konfiguraci aplikací (tj. Apache, IIS atd.)

### <a name="does-waf-also-support-ddos-prevention"></a>WAF také podporuje před útoky DDoS ochrany před únikem informací?

Ano. Můžete povolit ochranu před útoky DDos na virtuální síť, ve kterém je nasazená Aplikační brána. To zajistí, že služba application gateway virtuálních IP adres je taky chránit pomocí služby Azure DDos Protection.

## <a name="diagnostics-and-logging"></a>Protokolování a Diagnostika

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Jaké typy protokolů jsou dostupné pomocí služby Application Gateway?

K dispozici tři protokoly pro službu Application Gateway. Další informace na tyto protokoly a další diagnostické možnosti najdete v tématu [back-endu stav, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog** – protokol přístupu obsahuje každý požadavek odeslána k application gateway front-endu. Data obsahují volajícího IP adresy URL vyžádané, latence odpovědi, návratový kód, bajtů a oddálení. Přístup k protokolu shromažďovaných každých 300 sekund. Tento protokol obsahuje jeden záznam za instance služby application gateway.
* **ApplicationGatewayPerformanceLog** -protokolu výkonu zaznamenává informace o výkonu na na základě instancí včetně celkový požadavek obsluhuje, propustnost v bajtech, celkový počet požadavků obsluhovat, počet neúspěšných požadavků funkčních a nefunkčních back-end počet instancí.
* **ApplicationGatewayFirewallLog** – brány firewall protokolu obsahuje požadavky, které jsou zaznamenány pomocí detekce a ochrany před únikem informací režim služby application gateway, která je nakonfigurovaná s firewallem webových aplikací.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Jak poznám, že pokud jsou v pořádku Moje členů fondu back-end?

Můžete použít rutinu Powershellu `Get-AzureRmApplicationGatewayBackendHealth` nebo ověřit stav prostřednictvím portálu návštěvou [diagnostiku brány aplikací](application-gateway-diagnostics.md)

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Co jsou zásady uchovávání informací na protokoly diagnostiky?

Diagnostické protokoly toku do účtu úložiště zákazníkům a zákazníci můžou nastavit zásady uchovávání informací podle jejich priority. Diagnostické protokoly můžete odeslat také do Log Analytics nebo centra událostí. Zobrazit [diagnostiku brány aplikace](application-gateway-diagnostics.md) další podrobnosti.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Jak získám protokoly auditu pro službu Application Gateway?

Protokoly auditu jsou k dispozici pro službu Application Gateway. Na portálu klikněte na tlačítko **protokolu aktivit** v okně nabídky služby application gateway přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Můžete nastavit výstrahy se službou Application Gateway?

Ano, služba Application Gateway podporuje výstrahy. Konfigurují se upozornění na metriky. Zobrazit [metriky Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) získat další informace o službě Application Gateway metriky. Další informace o výstrahách najdete v tématu [doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Jak analyzovat statistiku provozu pro službu Application Gateway?

Můžete zobrazit a analyzovat protokoly přístupu přes celou řadou mechanismy, jako je Azure Log Analytics, Excel, Power BI atd.

Také jsme publikovali šablony Resource Manageru, který nainstaluje a spustí Oblíbené [GoAccess](https://goaccess.io/) protokolu analyzátoru protokolů Application Gateway přístup. GoAccess poskytuje cenné statistiku provozu HTTP jako je například jedinečných návštěvníků, vyžádané soubory, hostitele, operační systémy, prohlížeče, stavové kódy HTTP a další. Další podrobnosti najdete [souboru Readme do složky šablony Resource Manageru v Githubu](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>Stav back-endu vrátí stav neznámý, co by mohlo být příčinou tohoto stavu?

Nejběžnějším důvodem je, blokuje přístup k back-endové skupiny zabezpečení sítě nebo vlastní DNS. Zobrazit [back-endu stav, protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md) Další informace.

## <a name="next-steps"></a>Další kroky

Další informace o službě Application Gateway najdete v tématu [co je Azure Application Gateway?](overview.md)
