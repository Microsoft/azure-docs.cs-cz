---
title: Nejčastější dotazy k Azure Application Gateway
description: Přečtěte si odpovědi na nejčastější dotazy k Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 83779dcd319614ae15de6b7e3e4e3abfd9599089
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619150"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Nejčastější dotazy týkající se Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následují Nejčastější dotazy týkající se Azure Application Gateway.

## <a name="general"></a>Obecné

### <a name="what-is-application-gateway"></a>Co je Application Gateway?

Azure Application Gateway poskytuje jako službu řadič pro doručování aplikací (ADC). Nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Tato služba je vysoce dostupná, škálovatelná a plně spravovaná pomocí Azure.

### <a name="what-features-does-application-gateway-support"></a>Jaké funkce Application Gateway podporují?

Application Gateway podporuje automatické škálování, snižování zátěže TLS a komplexní TLS, Firewall webových aplikací (WAF), spřažení relací na základě souborů cookie, směrování na základě cesty URL, hostování ve více lokalitách a další funkce. Úplný seznam podporovaných funkcí najdete v tématu [Úvod do Application Gateway](./overview.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Jak se liší Application Gateway a Azure Load Balancer?

Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7, což znamená, že funguje jenom s webovým provozem (HTTP, HTTPS, WebSocket a HTTP/2). Podporuje funkce, jako je ukončení protokolu TLS, spřažení relace na základě souborů cookie a kruhové dotazování pro provoz vyrovnávání zatížení. Load Balancer zatížení vyrovnává zatížení ve vrstvě 4 (TCP nebo UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jaké protokoly Application Gateway podporují?

Application Gateway podporuje HTTP, HTTPS, HTTP/2 a WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak Application Gateway podporuje HTTP/2?

Viz [Podpora protokolu HTTP/2](./configuration-listeners.md#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Jaké prostředky se podporují jako součást fondu back-endu?

Viz [podporované prostředky back-endu](./application-gateway-components.md#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>V jakých oblastech je Application Gateway k dispozici?

Application Gateway V1 (Standard a WAF) je k dispozici ve všech oblastech globální služby Azure. Je také k dispozici v [Azure Čína 21Vianet](https://www.azure.cn/) a [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

Dostupnost Application Gateway v2 (Standard_v2 a WAF_v2) najdete v části [podporované oblasti pro Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md#supported-regions) .

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Je toto nasazení vyhrazeno pro moje předplatné nebo je sdíleno mezi zákazníky?

Application Gateway je ve vaší virtuální síti vyhrazené nasazení.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Podporuje Application Gateway přesměrování od protokolu HTTP do HTTPS?

Přesměrování je podporováno. Přečtěte si téma [Přehled přesměrování Application Gateway](./redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>V jakém pořadí jsou naslouchací procesy zpracovávány?

Podívejte se na [pořadí zpracování naslouchacího procesu](./configuration-listeners.md#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Kde najdu Application Gateway IP a DNS?

Pokud jako koncový bod používáte veřejnou IP adresu, najdete informace o IP adrese a DNS na prostředku veřejné IP adresy. Můžete ji také najít na portálu na stránce Přehled pro aplikační bránu. Pokud používáte interní IP adresy, vyhledejte informace na stránce Přehled.

V případě SKU verze V2 otevřete prostředek veřejné IP adresy a vyberte **Konfigurace**. K dispozici je pole **popisek názvu DNS (volitelné)** pro konfiguraci názvu DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Jaká jsou nastavení časového limitu Keep-Alive a vypršení časového limitu nečinnosti protokolu TCP?

*Časový limit zachování* určuje, jak dlouho Application Gateway čekat, než klient pošle další požadavek HTTP na trvalé připojení, než ho znovu použije nebo zavře. *Časový limit nečinnosti protokolu TCP* určuje, jak dlouho zůstane připojení TCP otevřené v případě žádné aktivity. 

*Časový limit udržování* připojení v SKU Application Gateway v1 je 120 sekund a v SKU v2 je 75 sekund. *Časový limit nečinnosti TCP* je výchozí 4 minuty na front-endové virtuální IP adrese (VIP) verze V1 i v2 Application Gateway. Můžete nakonfigurovat hodnotu časového limitu nečinnosti TCP v bránách V1 a v2, aby byly kdekoli mezi 4 minutami a 30 minutami. Pro aplikační brány v1 i v2 budete muset přejít na veřejnou IP adresu Application Gateway a změnit časový limit nečinnosti TCP v okně Konfigurace veřejné IP adresy na portálu. Můžete nastavit hodnotu časového limitu nečinnosti protokolu TCP veřejné IP adresy prostřednictvím PowerShellu spuštěním následujících příkazů: 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Mění se IP adresa nebo název DNS po celou dobu životnosti služby Application Gateway?

V případě SKU Application Gateway V1 se virtuální IP adresa může změnit, pokud zastavíte a spustíte Aplikační bránu. Název DNS přidružený k aplikační bráně se ale po dobu života brány nezmění. Vzhledem k tomu, že se název DNS nemění, měli byste použít alias CNAME a nasměrovat ho na adresu DNS služby Application Gateway. V případě SKU Application Gateway v2 můžete IP adresu nastavit jako statickou, takže se IP adresa a název DNS se po celou dobu životnosti služby Application Gateway nezmění. 

### <a name="does-application-gateway-support-static-ip"></a>Podporuje Application Gateway statickou IP adresu?

Ano, SKU Application Gateway v2 podporuje statické veřejné IP adresy. SKU v1 podporuje statické interní IP adresy.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Podporuje Application Gateway více veřejných IP adres v bráně?

Aplikační brána podporuje jenom jednu veřejnou IP adresu.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak velký má být podsíť pro Application Gateway?

Informace najdete v tématu [Application Gateway hlediska velikosti podsítě](./configuration-infrastructure.md#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Můžu nasadit více než jeden prostředek Application Gateway do jedné podsítě?

Ano. Kromě více instancí daného nasazení Application Gateway můžete zřídit jiný jedinečný Application Gateway prostředek pro existující podsíť, která obsahuje jiný prostředek Application Gateway.

Jedna podsíť nepodporuje Application Gateway SKU v2 i v1.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Podporuje Application Gateway v2 trasy definované uživatelem (UDR)?

Ano, ale pouze konkrétní scénáře. Další informace najdete v tématu [Konfigurace infrastruktury Application Gateway](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Podporuje Application Gateway hlaviček předávaných přes x?

Ano. Viz [Úpravy žádosti](./how-application-gateway-works.md#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Jak dlouho trvá nasazení aplikační brány? Bude moje Aplikační brána fungovat, i když se aktualizuje?

Zřizování nových SKU Application Gateway v1 může trvat až 20 minut. Změny velikosti nebo počtu instancí nejsou rušivé a brána zůstane během této doby aktivní.

Zřizování většiny nasazení, která používají SKU v2, trvá přibližně 6 minut. Může ale trvat delší dobu v závislosti na typu nasazení. Například nasazení v různých Zóny dostupnosti s mnoha instancemi může trvat více než 6 minut. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Můžu použít Exchange Server jako back-end s Application Gateway?

No. Application Gateway nepodporuje e-mailové protokoly, jako jsou SMTP, IMAP a POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Je k dispozici návod k migraci z SKU V1 na SKU 2?

Ano. Podrobnosti najdete v tématu [migrace služby Azure Application Gateway a firewall webových aplikací z verze V1 na verzi v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Bude nadále podporována SKU Application Gateway v1?

Ano. SKU Application Gateway v1 bude nadále podporována. Důrazně však doporučujeme přejít na verzi v2, abyste mohli využít aktualizace funkcí v této SKU. Další informace najdete v tématu Automatické [škálování a redundantní Application Gateway v2 pro zóny](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Podporuje Application Gateway v2 požadavky na proxy s ověřováním NTLM?

No. Application Gateway v2 nepodporuje proxy požadavky s ověřováním NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Podporuje soubor cookie spřažení Application Gateway atribut SameSite?
Ano, [v80](https://chromiumdash.appspot.com/schedule) v [prohlížeči chrom](https://www.chromium.org/Home) představila v souborech cookie protokolu HTTP pověření bez SameSite atributu, aby bylo považováno za SameSite = Lax. To znamená, že prohlížeč nebude odesílat soubory cookie spřažení Application Gateway v kontextu třetí strany. 

Pro podporu tohoto scénáře Application Gateway vloží další soubor cookie s názvem *ApplicationGatewayAffinityCORS* spolu s existujícím souborem cookie *ApplicationGatewayAffinity* .  Tyto soubory cookie jsou podobné, ale soubor cookie *ApplicationGatewayAffinityCORS* má přidané dva další atributy: *SameSite = None; Zabezpečení*. Tyto atributy udržují rychlé relace i pro žádosti o více zdrojů. Další informace najdete v [části spřažení na základě souborů cookie](configuration-http-settings.md#cookie-based-affinity) .

## <a name="performance"></a>Výkon

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak Application Gateway podporuje vysokou dostupnost a škálovatelnost?

SKU Application Gateway v1 podporuje scénáře s vysokou dostupností, pokud jste nasadili dvě nebo víc instancí. Azure distribuuje tyto instance napříč doménami aktualizace a selhání, aby se zajistilo, že se instance ve stejnou dobu nedaří. SKU v1 podporuje škálovatelnost přidáním více instancí stejné brány pro sdílení zatížení.

SKU v2 automaticky zajišťuje, že se nové instance rozprostře mezi doménami selhání a aktualizačními doménami. Pokud zvolíte redundanci zóny, nejnovější instance se také rozšíří napříč zónami dostupnosti, aby nabízely odolnost v oblasti selhání.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Návody dosáhnout scénáře zotavení po havárii napříč datacentry pomocí Application Gateway?

Použijte Traffic Manager k distribuci provozu mezi několik aplikačních bran v různých datových centrech.

### <a name="does-application-gateway-support-autoscaling"></a>Podporuje Application Gateway automatické škálování?

Ano, skladová položka Application Gateway v2 podporuje automatické škálování. Další informace najdete v tématu Automatické [škálování a redundantní Application Gateway v zóně](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Způsobuje ruční nebo automatické horizontální navýšení nebo snížení kapacity při výpadku?

No. Instance se distribuují napříč doménami upgradu a doménami selhání.

### <a name="does-application-gateway-support-connection-draining"></a>Podporuje Application Gateway vyprazdňování připojení?

Ano. Můžete nastavit vyprazdňování připojení pro změnu členů ve fondu back-end bez přerušení. Další informace najdete v [části vyprazdňování připojení Application Gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Můžu změnit velikost instance z středně na velká bez přerušení?

Ano.

## <a name="configuration"></a>Konfigurace

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Je Application Gateway vždycky nasazené ve virtuální síti?

Ano. Application Gateway je vždy nasazena v podsíti virtuální sítě. Tato podsíť může obsahovat jenom aplikační brány. Další informace najdete v tématu [požadavky na virtuální síť a podsíť](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Může Application Gateway komunikovat s instancemi mimo svou virtuální síť nebo mimo její předplatné?

Pokud máte připojení IP, Application Gateway můžou komunikovat s instancemi mimo virtuální síť, ve které je. Application Gateway může také komunikovat s instancemi mimo předplatné, ve kterém je. Pokud máte v úmyslu používat jako členy fondu back-end interní IP adresy, použijte [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) nebo [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Můžu v podsíti aplikační brány nasazovat cokoli jiného?

No. V podsíti ale můžete nasadit i jiné aplikační brány.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Jsou skupiny zabezpečení sítě podporované v podsíti služby Application Gateway?

Viz téma [skupiny zabezpečení sítě v podsíti Application Gateway](./configuration-infrastructure.md#network-security-groups).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Podporuje podsíť služby Application Gateway trasy definované uživatelem?

Viz [trasy definované uživatelem podporované v Application Gateway podsíti](./configuration-infrastructure.md#supported-user-defined-routes).

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>Podporují se v Application Gateway podsíti zásady koncového bodu služby?

No. [Zásady koncového bodu služby](../virtual-network/virtual-network-service-endpoint-policies-overview.md) pro účty úložiště nejsou podporované v Application Gateway podsíti a nakonfigurují se tak, že budou blokovat přenosy infrastruktury Azure.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jaká jsou omezení pro Application Gateway? Můžu tato omezení zvýšit?

Viz [omezení Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Můžu současně Application Gateway použít pro externí i interní provoz?

Ano. Application Gateway podporuje jednu interní IP adresu a jednu externí IP adresu pro bránu aplikace.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Podporuje Application Gateway partnerský vztah virtuálních sítí?

Ano. Partnerské vztahy virtuálních sítí pomáhají vyrovnávat zatížení v jiných virtuálních sítích.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Můžu komunikovat s místními servery, když jsou připojeni pomocí tunelů ExpressRoute nebo VPN?

Ano, pokud je povolený provoz.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Může jeden back-end fond poskytovat mnoho aplikací na různých portech?

Architektura mikroslužeb je podporovaná. Chcete-li provést test na různých portech, je nutné nakonfigurovat více nastavení protokolu HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Podporují vlastní testy zástupné znaky nebo regulární výrazy v datech odpovědí?

No. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Jak se zpracovávají pravidla směrování v Application Gateway?

Viz [pořadí pravidel zpracování](./configuration-request-routing-rules.md#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Pro vlastní testy, co pole hostitel označuje?

Pole hostitel Určuje název, do kterého se má odeslat sonda při konfiguraci nasazení ve více lokalitách na Application Gateway. Jinak použijte 127.0.0.1. Tato hodnota se liší od názvu hostitele virtuálního počítače. Formát je \<protocol\> :// \<host\> : \<port\> \<path\> .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Můžu Application Gateway přístup k jenom několika zdrojovým IP adresám?

Ano. Viz [omezení přístupu na konkrétní zdrojové IP adresy](./configuration-infrastructure.md#allow-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Můžu použít stejný port pro veřejné i privátní naslouchací procesy?

No.

### <a name="does-application-gateway-support-ipv6"></a>Podporuje Application Gateway IPv6?

Application Gateway V2 v současné době nepodporuje protokol IPv6. Může pracovat ve virtuální síti s duálním zásobníkem jenom pomocí protokolu IPv4, ale podsíť brány musí být jenom IPv4. Application Gateway v1 nepodporuje duální virtuální sítě zásobníku. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Návody používat Application Gateway v2 s pouze soukromou front-end IP adresou?

Application Gateway V2 v současné době nepodporuje pouze režim privátních IP adres. Podporuje následující kombinace
* Privátní IP adresa a veřejná IP adresa
* Jenom veřejná IP adresa

Pokud ale chcete použít Application Gateway v2 jenom s privátní IP adresou, můžete postupovat podle následujícího postupu:
1. Vytvoření Application Gateway s IP adresou veřejného i privátního front-endu
2. Nevytvářejte žádné naslouchací procesy pro veřejnou IP adresu front-endu. Application Gateway nebude naslouchat jakémukoli provozu na veřejné IP adrese, pokud pro něj nebudou vytvořeny žádné naslouchací procesy.
3. Vytvořte a připojte [skupinu zabezpečení sítě](../virtual-network/network-security-groups-overview.md) pro Application Gateway podsíť s následující konfigurací v pořadí podle priority:
    
    a. Povolte provoz ze zdroje jako značky služby **GatewayManager** a cíle jako **libovolný** a cílový port jako **65200-65535**. Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure. Tyto porty jsou chráněné (jsou uzamčené) pomocí ověření certifikátu. Externí entity, včetně správců uživatelů brány, nemůžou iniciovat změny těchto koncových bodů bez příslušných certifikátů.
    
    b. Povolení provozu ze zdroje jako značky služby **AzureLoadBalancer** a cílového a cílového portu jako **libovolného**
    
    c. Zakažte veškerý příchozí provoz ze zdroje jako značky **internetové** služby a cílového a cílového portu **.** Dát tomuto pravidlu *nejnižší prioritu* pro příchozí pravidla
    
    d. Ponechte výchozí pravidla, jako je například povolování příchozího VirtualNetwork, aby přístup na privátní IP adrese není blokovaný.
    
    e. Odchozí připojení k Internetu nejde zablokovat. V opačném případě dojde k problémům s protokolováním, metrikami atd.

Ukázková konfigurace NSG jenom pro soukromý IP přístup: ![ konfigurace Application Gateway v2 NSG jenom pro privátní IP adresy](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Konfigurace-TLS

### <a name="what-certificates-does-application-gateway-support"></a>Jaké certifikáty Application Gateway podporují?

Application Gateway podporuje certifikáty podepsané svým držitelem, certifikáty certifikační autority (CA), rozšířené ověřování (EV), certifikáty pro více domén (SAN) a certifikáty se zástupnými znaky.

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

Informace o tom, jak přizpůsobit možnosti TLS, najdete v tématu [Konfigurace verzí zásad TLS a šifrovacích sad na Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Podporuje Application Gateway šifrování provozu do back-endu?

Ano. Application Gateway podporuje snižování zátěže TLS a komplexní TLS, které přešifrují provoz na back-end.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Můžu nakonfigurovat zásady TLS pro řízení verzí protokolu TLS?

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

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Kolik certifikátů TLS/SSL Application Gateway podporu?

Application Gateway podporuje až 100 certifikátů TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Kolik ověřovacích certifikátů pro opakované šifrování back-endu Application Gateway podporu?

Application Gateway podporuje až 100 ověřovacích certifikátů.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Je Application Gateway nativně integrována s Azure Key Vault?

Ano, skladová položka Application Gateway v2 podporuje Key Vault. Další informace najdete v tématu [Ukončení šifrování TLS s využitím certifikátů služby Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Návody nakonfigurovat naslouchací procesy protokolu HTTPS pro weby. com a .NET? 

U více směrování založených na doméně (hostitele) můžete vytvořit více než více lokalit, nastavit naslouchací procesy, které používají protokol HTTPS jako protokol, a přidružit naslouchací procesy k pravidlům směrování. Další informace najdete v tématu [hostování více lokalit pomocí Application Gateway](./multiple-site-overview.md).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Můžu v heslu souboru. pfx použít speciální znaky?

Ne, v heslech souboru. pfx použijte pouze alfanumerické znaky.

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>Můj certifikát pro EV vystavil DigiCert a můj zprostředkující certifikát byl odvolán. Návody prodloužit platnost certifikátu v Application Gateway?

Prohlížeč certifikační autority (CA) Členové prohlížeče nedávno publikovali sestavy s podrobnostmi o více certifikátech vydaných dodavateli certifikačních autorit, kteří používají naši zákazníci, společnost Microsoft a vyšší technologická komunita, která byla nekompatibilní s oborovým standardem pro veřejně důvěryhodné certifikační autority.Sestavy týkající se certifikačních autorit, které nedodržují předpisy, najdete tady:  

* [Chyba 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Chyba 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

Podle požadavků na dodržování předpisů v oboru můžou dodavatelé CA odvolat certifikační autority, které nedodržují předpisy, a vydávat odpovídající certifikační autority, které vyžadují, aby se jejich certifikáty znovu vystavily pro zákazníky.Společnost Microsoft úzce spolupracuje s těmito dodavateli, aby minimalizovala potenciální dopad na služby Azure, **ale vaše vlastní certifikáty nebo certifikáty, které se používají ve scénářích "Přineste si vlastní certifikát" (BYOC), jsou stále ohroženy tím, že se neočekávaně odvolají**.

Chcete-li zjistit, zda certifikáty využívané vaší aplikací byly odvolány z oznámení referenčního [DigiCert](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) a [sledování odvolaných certifikátů](https://misissued.com/#revoked). Pokud vaše certifikáty byly odvolány nebo budou odvolány, budete muset požádat dodavatele CA o nové certifikáty využívané ve svých aplikacích. Aby nedošlo k přerušení dostupnosti vaší aplikace z důvodu neočekávaného odvolání certifikátů, nebo pokud chcete aktualizovat certifikát, který byl odvolán, přečtěte si následující příspěvek v našich aktualizacích Azure, kde najdete odkazy na nápravy různých služeb Azure, které podporují BYOC: https://azure.microsoft.com/updates/certificateauthorityrevocation/

Konkrétní informace o Application Gateway najdete v části níže.

Pokud používáte certifikát vydaný jedním z odvolaných ICAs, může být dostupnost vaší aplikace přerušená a v závislosti na vaší aplikaci může docházet k nejrůznějším chybovým zprávám, včetně, ale ne omezení: 

1.  Neplatný certifikát nebo odvolaný certifikát
2.  Vypršel časový limit připojení
3.  HTTP 502

Abyste se vyhnuli jakémukoli přerušení vaší aplikace z důvodu tohoto problému nebo pokud chcete znovu vydat certifikační autoritu, která byla odvolána, je nutné provést následující akce: 

1.  Obraťte se na svého poskytovatele certifikátů a zjistěte, jak znovu vystavit certifikáty.
2.  Po vyřešení aktualizace certifikátů v Azure Application Gateway/WAF s úplným [řetězcem důvěryhodnosti](/windows/win32/seccrypto/certificate-chains) (list, zprostředkující, kořenový certifikát). V závislosti na tom, kde certifikát používáte, buď na naslouchací proces, nebo na nastavení HTTP Application Gateway, postupujte podle následujících kroků, abyste aktualizovali certifikáty, a další informace najdete v odkazech na dokumentaci uvedených v dokumentaci.
3.  Aktualizujte aplikační servery back-end tak, aby používaly znovu vydaný certifikát. V závislosti na back-end serveru, který používáte, se může změnit váš postup aktualizace certifikátu. Podívejte se prosím na dokumentaci od dodavatele.

Aktualizace certifikátu v naslouchací službě:

1.  V [Azure Portal](https://portal.azure.com/)otevřete prostředek Application Gateway
2.  Otevřete Nastavení naslouchacího procesu, které je přidružené k vašemu certifikátu.
3.  Klikněte na tlačítko obnovit nebo upravit vybraný certifikát.
4.  Nahrajte nový certifikát PFX s heslem a klikněte na Uložit.
5.  Přihlaste se k webu a ověřte, jestli lokalita funguje podle [očekávání. Další informace najdete v](./renew-certificates.md)dokumentaci.

Pokud odkazujete na certifikáty z trezoru klíčů Azure v rámci naslouchacího procesu Application Gateway, doporučujeme, abyste provedli následující kroky pro rychlou změnu –

1.  V [Azure Portal](https://portal.azure.com/)přejděte do nastavení trezoru klíčů Azure, které bylo přidruženo k Application Gateway
2.  Přidejte nebo importujte znovu vydaný certifikát ve vašem úložišti. Další informace o tom, jak postupovat, najdete [v dokumentaci.](../key-vault/certificates/quick-create-portal.md)
3.  Po importu certifikátu přejděte do Nastavení naslouchacího procesu Application Gateway a v části zvolit certifikát z Key Vault klikněte na rozevírací seznam certifikát a vyberte naposledy přidaný certifikát.
4.  Kliknutím na Uložit zobrazíte další informace o ukončení protokolu TLS u Application Gateway pomocí certifikátů Key Vault, Projděte [si dokumentaci sem](./key-vault-certs.md).


Aktualizace certifikátu v nastavení HTTP:

Pokud používáte SKU verze V1 služby Application Gateway/WAF, museli byste nový certifikát nahrát jako certifikát pro ověřování back-endu.
1.  V [Azure Portal](https://portal.azure.com/)otevřete prostředek Application Gateway
2.  Otevřete nastavení HTTP, které je přidružené k vašemu certifikátu.
3.  Klikněte na Přidat certifikát a nahrajte znovu vydaný certifikát a klikněte na Uložit.
4.  Starý certifikát můžete odebrat později kliknutím na "..." tlačítko Možnosti vedle starého certifikátu a vyberte Odstranit a klikněte na Uložit.
Další informace najdete v dokumentaci [sem](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers).

Pokud používáte SKU verze V2 služby Application Gateway/WAF, nemusíte v nastavení HTTP nahrávat nový certifikát, protože SKU v2 používá "důvěryhodné kořenové certifikáty" a není nutné provádět žádnou akci.

## <a name="configuration---ingress-controller-for-aks"></a>Konfigurace – řadič pro příchozí přenosy pro AKS

### <a name="what-is-an-ingress-controller"></a>Co je kontroler příchozího přenosu dat?

Kubernetes umožňuje vytvoření `deployment` a `service` prostředek k internímu vystavení skupiny lusků v clusteru. Pokud chcete stejnou službu vystavit externě, [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) je definován prostředek, který poskytuje vyrovnávání zatížení, ukončení protokolu TLS a virtuální hostování na základě názvu.
Aby byl tento `Ingress` prostředek splněn, vyžaduje se kontroler příchozího přenosu dat, který naslouchá jakýmkoli změnám v `Ingress` prostředcích a konfiguruje zásady nástroje pro vyrovnávání zatížení.

Řadič Application Gateway příchozího přenosu dat (AGIC) umožňuje použití [Application Gateway Azure](https://azure.microsoft.com/services/application-gateway/) jako příchozí pro [službu Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) , která se také označuje jako cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Může jediná instance kontroleru příchozího přenosu dat spravovat víc aplikačních bran?

V současné době může být jedna instance řadiče pro příchozí spojení přidružená jenom k jednomu Application Gateway.

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>Proč můj cluster AKS s kubenet nepracuje s AGIC?

AGIC se pokusí automaticky přidružit prostředek směrovací tabulky k podsíti Application Gateway, ale může to udělat, protože z AGIC chybí oprávnění. Pokud AGIC není schopen přidružit směrovací tabulku k podsíti Application Gateway, dojde k chybě v protokolech AGIC. v takovém případě budete muset ručně přidružit směrovací tabulku vytvořenou clusterem AKS k podsíti Application Gateway. Další informace najdete v tématu [podporované uživatelsky definované trasy](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>Můžu svůj cluster AKS a Application Gateway připojit k samostatným virtuálním sítím? 

Ano, pokud jsou virtuální sítě v partnerském vztahu a nemají překrývající se adresní prostory. Pokud používáte AKS s kubenet, nezapomeňte přidružit směrovací tabulku vygenerovanou AKS do podsítě Application Gateway. 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>Jaké funkce nejsou podporovány na doplňku AGIC? 

Podívejte se prosím na rozdíly mezi AGIC nasazenými prostřednictvím Helm a nasazenými jako doplněk pro [](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) AKS.

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>Kdy mám použít doplněk oproti Helm nasazení? 

Podívejte se na rozdíly mezi AGIC nasazenými prostřednictvím Helm a nasazenými jako doplněk pro AKS [, zejména](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)v tabulkách dokumentace, které scénáře jsou podporovány AGIC nasazenými prostřednictvím Helm, a to na rozdíl od doplňku AKS. Obecně platí, že nasazení prostřednictvím Helm vám umožní otestovat verze beta verzí a kandidáty na vydání před oficiální verzí. 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>Můžu určit, která verze AGIC se bude s doplňkem nasazovat?

Ne, doplněk AGIC je spravovaná služba, která znamená, že Microsoft bude automaticky aktualizovat doplněk na nejnovější stabilní verzi. 

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Jaké typy protokolů Application Gateway poskytnout?

Application Gateway poskytuje tři protokoly: 

* **ApplicationGatewayAccessLog**: protokol Access obsahuje všechny požadavky odeslané do front-endu služby Application Gateway. Mezi tato data patří IP adresa volajícího, požadovaná adresa URL, latence odezvy, návratový kód a bajtů. Obsahuje jeden záznam na aplikační bránu.
* **ApplicationGatewayPerformanceLog**: protokol výkonu zachycuje informace o výkonu pro každou bránu aplikace. Informace zahrnují propustnost v bajtech, celkový počet zpracovaných požadavků, počet neúspěšných požadavků a stav back-endu, který není v pořádku.
* **ApplicationGatewayFirewallLog**: pro brány aplikací, které nakonfigurujete pomocí WAF, obsahuje protokol brány firewall požadavky, které se protokolují buď v režimu detekce, nebo v režimu prevence.

Všechny protokoly se shromažďují každých 60 sekund. Další informace najdete v tématu [stav back-endu, diagnostické protokoly a metriky pro Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Návody zjistit, jestli jsou členové fondu back-endu v pořádku?

Ověřte stav pomocí rutiny prostředí PowerShell `Get-AzApplicationGatewayBackendHealth` nebo portálu. Další informace najdete v tématu [diagnostika Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Jaké jsou zásady uchovávání informací pro diagnostické protokoly?

Diagnostické protokoly se zaznamenávají do účtu úložiště zákazníka. Zákazníci můžou zásady uchovávání informací nastavit na základě jejich preference. Diagnostické protokoly je také možné odeslat do centra událostí nebo do protokolů Azure Monitor. Další informace najdete v tématu [diagnostika Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Návody získat protokoly auditu pro Application Gateway?

Na portálu v okně nabídky aplikační brány vyberte **Protokol aktivit** pro přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Můžu nastavit upozornění pomocí Application Gateway?

Ano. V Application Gateway jsou výstrahy nakonfigurované na metrikách. Další informace najdete v tématu [Application Gateway metriky](./application-gateway-metrics.md) a [přijímání oznámení o výstrahách](../azure-monitor/alerts/alerts-overview.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Návody analyzovat Statistiky provozu pro Application Gateway?

Protokoly přístupu můžete zobrazit a analyzovat několika způsoby. Použijte protokoly Azure Monitor, Excel, Power BI a tak dále.

Můžete také použít šablonu Správce prostředků, která nainstaluje a spustí oblíbený analyzátor protokolů [GoAccess](https://goaccess.io/) pro Application Gateway protokoly přístupu. GoAccess poskytuje cennou statistiku přenosů HTTP, například jedinečné návštěvníky, požadované soubory, hostitele, operační systémy, prohlížeče a stavové kódy HTTP. Další informace najdete na webu GitHub v [souboru Readme ve složce šablony Správce prostředků](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Co by mohlo způsobit neznámý stav back-endu?

Obvykle se zobrazuje neznámý stav, pokud je přístup k back-endu zablokovaný skupinou zabezpečení sítě (NSG), vlastním DNS nebo uživatelem definovaným směrováním (UDR) v podsíti aplikační brány. Další informace najdete v tématu [stav back-endu, protokolování diagnostiky a metriky pro Application Gateway](application-gateway-diagnostics.md).

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>Podporují se protokoly toku NSG v skupin zabezpečení sítě přidružené k podsíti Application Gateway v2?

V důsledku současných omezení platformy, pokud máte NSG v podsíti Application Gateway v2 (Standard_v2, WAF_v2) a pokud jste povolili NSG flow, uvidíte nedeterministické chování a tento scénář se momentálně nepodporuje.

### <a name="where-does-application-gateway-store-customer-data"></a>Kam se Application Gateway ukládají zákaznická data?

Application Gateway nepřesouvá ani neukládají zákaznická data mimo oblast, ve které je nasazená.

## <a name="next-steps"></a>Další kroky

Další informace o Application Gateway najdete v tématu [co je Azure Application Gateway?](overview.md).
