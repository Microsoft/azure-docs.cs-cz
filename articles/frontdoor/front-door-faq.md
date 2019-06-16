---
title: Služba Azure branou – nejčastější dotazy k branou | Dokumentace Microsoftu
description: Tato stránka nabízí odpovědi na nejčastější dotazy týkající se Azure branou služby
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736662"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Nejčastější dotazy pro Azure branou služby

Tento článek obsahuje odpovědi na běžné dotazy týkající se funkcí branou služby Azure. Pokud nevidíte odpověď na svoji otázku, kontaktujte nás prostřednictvím následujících kanálů (v neustále rostoucích pořadí):

1. V sekci komentáře v tomto článku.
2. [Azure branou služby UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Podpora společnosti Microsoft:** Chcete-li vytvořit novou žádost o podporu, na webu Azure Portal, na **pomáhají** kartu, vyberte možnost **Nápověda a podpora** tlačítko a pak vyberte **nová žádost o podporu**.

## <a name="general"></a>Obecné

### <a name="what-is-azure-front-door-service"></a>Co je Azure Front Door Service?

Služba Azure branou je síť doručování aplikací (ADN) jako službu, nabízí různé vrstvy 7 možnostmi Vyrovnávání zatížení pro vaše aplikace. Poskytuje akcelerace dynamického webu (DSA) spolu s globální služby Vyrovnávání zatížení s téměř v reálném čase převzetí služeb při selhání. Jedná se o vysoce dostupnou a škálovatelnou službu, plně spravovanou službou Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Jaké funkce podporuje branou služby Azure?

Služba Azure branou podporuje akcelerace dynamického webu (DSA), snižování zátěže protokolu SSL a kompletního protokolu SSL, Firewall webových aplikací, spřažení relace na základě souborů cookie, směrování na základě cesty url, bezplatné certifikáty a Správa více domén a ostatní. Úplný seznam podporovaných funkcí najdete v tématu [přehled Azure branou služby](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Jaký je rozdíl mezi službou branou Azure a Azure Application Gateway?

Zatímco přední dveře i služba Application Gateway jsou vrstvy 7 (HTTP/HTTPS) pro vyrovnávání zatížení, hlavní rozdíl je, že branou se globální službu, zatímco služba Application Gateway je místní služba. Zatímco branou můžete vyrovnávat zatížení mezi jednotky/clustery/razítko jednotek škálování různých oblastech, služba Application Gateway umožňuje vyrovnávat zatížení mezi vaše virtuální počítače a kontejnery atd., které je v jednotce škálování.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Když jsme nasadit službu Application Gateway za branou?

Hlavní scénáře, proč jeden by měl používat Application Gateway za branou jsou:

- Přední dveře můžete provádět pouze na globální úrovni, ale pokud jeden chce vyrovnávat zatížení provozu ještě více v rámci své virtuální síti (VNET) a musí používat Application Gateway Vyrovnávání zatížení na základě cest.
- Vzhledem k tomu, že vstupní brána nebude fungovat na úrovni virtuálního počítače nebo kontejneru, proto ji nelze provést vyprázdnění připojení. Ale služba Application Gateway umožňuje provádět vyprázdnění připojení. 
- Pomocí služby Application Gateway za AFD jeden dosažení 100 % snižování zátěže protokolu SSL a směrovat pouze požadavky HTTP v rámci své virtuální síti (VNET).
- Přední dveře a služba Application Gateway podporuje spřažení relace. Zatímco branou může směrovat následný provoz z uživatelské relace na stejném clusteru nebo back-end v dané oblasti, služba Application Gateway můžete přímo spřažení provoz na stejný server v rámci clusteru.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Doporučujeme nasadit Azure Load Balancer za branou?

Služba Azure branou musí veřejných virtuálních IP adres nebo veřejně k dispozici název DNS ke směrování provozu do. Nasazení služby Azure Load Balancer za branou je běžný případ použití.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Jaké protokoly branou služby Azure podporují?

Služba Azure branou podporuje HTTP, HTTPS a HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Jak podporuje Azure branou služby HTTP/2?

Podpora protokolu HTTP/2 je k dispozici připojování ke službě Azure branou jenom klientům. Komunikace s back-endů v back-endový fond je přes HTTP/1.1. Podpora HTTP/2 je standardně povolená.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Které prostředky jsou dnes podporované jako součást back-endový fond?

Může být tvořen back-endové fondy úložiště, webové aplikace, instance Kubernetes nebo jakékoli jiné vlastní název hostitele, který má připojení pomocí veřejné. Služba Azure branou vyžaduje, aby definovaných back-EndY přes veřejnou IP adresu nebo veřejně přeložitelného název hostitele DNS. Členy fondu back-end může být napříč zónami, oblasti, nebo dokonce i mimo Azure tak dlouho, dokud mají veřejné připojení.

### <a name="what-regions-is-the-service-available-in"></a>Jaké oblasti je k dispozici ve službě?

Služba Azure branou se globální službu a není vázána k žádné konkrétní oblasti Azure. Je jediným umístěním, které je třeba zadat při vytváření přední dveře umístění skupiny prostředků, který je v podstatě určení, kam se má uložit metadata pro skupinu prostředků. Samotného prostředku branou, je vytvořena jako globální prostředek a globálně nasazuje konfigurace do všech bodů POP (Point of Presence). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Co jde u umístění POP branou služby Azure?

Služba Azure branou má stejný seznam umístění POP (Point of Presence) jako Azure CDN od společnosti Microsoft. Kompletní seznam našich bodů POP, kdybyste najdete [místa POP sítě CDN Azure od Microsoftu](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Je služba Azure branou vyhrazené nasazení pro aplikaci nebo je sdílen mezi zákazníky?

Azure Service branou je globálně Distribuovaná služba více tenantů. Ano infrastrukturu pro branou je sdílen mezi svým zákazníkům. Ale tak, že vytvoříte přední dveře, můžete definovat konkrétní konfigurace požadované pro vaše aplikace a 

### <a name="is-http-https-redirection-supported"></a>Je HTTP -> přesměrování protokolu HTTPS, které jsou podporovány?

Přední dveře v současné době nepodporuje adresy URL přesměrování.

### <a name="in-what-order-are-routing-rules-processed"></a>V jakém pořadí se pravidla směrování zpracovávají?

Trasy pro vaše branou nejsou seřazené a konkrétní trasa se vybere na základě nejlepší shody. Další informace o [branou jak odpovídá požadavků na pravidlo směrování](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Jak uzamknout přístup do své back-endu do pouze branou služby Azure?

Můžete nakonfigurovat IP ACLing pro váš back-end tak, aby přijímal pouze provoz z branou služby Azure. Můžete omezit aplikace přijímat příchozí připojení pouze z back-endu adresní prostor IP branou služby Azure. Pracujeme na integraci se službou [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519) ale teď se můžete vrátit rozsahy IP adres, jak je uvedeno níže:
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> Naše adresní prostor IP back-end může později změnit, ale jsme zajistí, aby předtím, než k tomu dojde, můžeme by jste integrovali s [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519). Doporučujeme vám, že se přihlásíte k odběru [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro změny nebo aktualizace. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>IP adresa všesměrového vysílání změnit během životního cyklu Moje branou?

IP adresa všesměrového vysílání front-endu pro vaše branou obvykle neměli měnit a může zůstat statický po dobu životnosti branou. Existují však **žádné záruky** pro stejné. Kdybyste nepřebírají žádné přímé závislosti na IP adresu.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Podporuje služba Azure branou statické nebo vyhrazené IP adresy?

Ne, branou služby Azure v současné době nepodporuje anycast statické nebo vyhrazené front-endové IP adresy. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Podporuje Azure branou služby x předané – pro hlavičky?

Ano, podporuje služba Azure branou hlavičky X-předané-pro X předané hostiteli a X-Forwarded-Proto. Pokud X-předané – pro hlavičky už je k dispozici a branou připojí k němu socket IP adresu klienta. Jinak přidá hlavičku socket IP adresu klienta jako hodnotu. X předané hostiteli a X-Forwarded-Proto hodnotu přepsat.

Další informace o [branou nepodporuje hlavičky protokolu HTTP](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Jak dlouho trvá nasazení branou služby Azure? Moje branou stále funguje při aktualizaci?

Nové vytvoření branou nebo žádné aktualizace existující branou trvá přibližně 3 až 5 minut, než se globální nasazení. To znamená během 3 až 5 minut, konfiguraci branou se nasadí ve všech našich bodů POP globálně.

Poznámka: vlastní aktualizace certifikátu SSL trvat asi 30 minut nasadí globálně.

## <a name="configuration"></a>Konfigurace

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Můžete nástroj pro vyrovnávání zatížení Azure branou nebo směrování provozu ve virtuální síti?

Azure branou (AFD) vyžaduje veřejnou IP adresu nebo veřejně přeložitelného název DNS pro směrování provozu. Ano, odpověď se žádné AFD přímo nemůžete provádět směrování v rámci virtuální sítě, ale mezi pomocí Application Gateway nebo nástroje pro vyrovnávání zatížení Azure vyřeší tento scénář.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Jaké jsou různé časové limity a omezení branou služby Azure?

Další informace o všech dokument [časové limity a omezení služby Azure branou](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Výkon

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Jak Azure branou Service podporuje vysokou dostupnost a škálovatelnost?

Služba Azure branou je globálně distribuovanou platformu více tenantů s obrovské objemy kapacitu pro potřeby vaší aplikace škálovatelnost. Branou doručit od levého okraje globální síti Microsoftu, poskytuje globální funkce pro vyrovnávání zatížení, která umožňuje převzetí služeb při selhání celé aplikace nebo dokonce jednotlivé mikroslužby napříč oblastmi nebo jiné cloudy.

## <a name="ssl-configuration"></a>Konfigurace SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Jaké verze TLS jsou podporovány službou Azure branou?

Přední dveře podporuje TLS verze 1.0, 1.1 a 1.2. TLS 1.3 se ještě nepodporuje.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Jaké certifikáty jsou podporovány ve službě Azure branou Service?

Pokud chcete povolit protokol HTTPS pro bezpečné doručování obsahu pro vlastní doménu branou, můžete použít certifikát, který je spravovaný službou Azure branou nebo použít svůj vlastní certifikát.
Branou spravované ustanovení možnost Standardní certifikát SSL prostřednictvím Digicert a uložená v popředí dveří Key Vault. Pokud budete chtít použít svůj vlastní certifikát, pak můžete připojit certifikát od certifikační Autority podporované a může být standardní protokol SSL, rozšíření ověřování certifikátu nebo dokonce certifikát se zástupným znakem. Certifikáty podepsané svým držitelem nejsou podporovány. Přečtěte si [jak povolit HTTPS pro vlastní doménu](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Jaké jsou aktuální šifrovací sada podporovaná službou Azure branou?

Toto jsou aktuální šifrovací sada podporovaná službou Azure branou:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Služba Azure branou také podporuje znova šifrovat provoz do back-end?

Ano, služba branou Azure podporuje přesměrování zpracování SSL a kompletního protokolu SSL, který znovu zašifruje provoz do back-endu. Ve skutečnosti protože připojení k back-endu dojít v je veřejná IP adresa, se doporučuje, abyste nakonfigurovali přední dveře k používání protokolu HTTPS jako protokol pro předávání.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Můžete nakonfigurovat zásady protokolu SSL pro řízení verze protokolu SSL?

Ne, přední dveře v současné době nepodporuje odepřít určité verze TLS ani nastavení minimální verze protokolu TLS. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Můžete nakonfigurovat branou pouze na podporu konkrétních šifrovací sady?

Ne, se nepodporuje konfigurace branou pro konkrétní šifrovací sady. 

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Jaké typy protokoly a metriky jsou k dispozici branou službou Azure?

Informace o další možnosti diagnostiky a protokolování, najdete v části [monitorování metrik a protokolů branou](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Co jsou zásady uchovávání informací na protokoly diagnostiky?

Diagnostické protokoly toku do účtu úložiště zákazníkům a zákazníci můžou nastavit zásady uchovávání informací podle jejich priority. Diagnostické protokoly můžete také odešlou do Event Hub nebo Azure Monitor protokoly. Další informace najdete v tématu [diagnostiky služby Azure branou](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Jak získám protokoly auditu branou služby Azure?

Protokoly auditu jsou k dispozici pro službu Azure branou. Na portálu klikněte na tlačítko **protokolu aktivit** v okně nabídky přední dveře pro přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Můžete nastavit výstrahy s branou služby Azure?

Ano, podporuje Azure branou služby Výstrahy. Konfigurují se upozornění na metriky. 

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).