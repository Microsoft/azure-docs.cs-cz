---
title: Služba front-dveří Azure – Nejčastější dotazy
description: Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se služby Azure front-dveří.
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
ms.openlocfilehash: c8f95a1de85fd2eb00b0525fca8e62ade87dd57f
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184644"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Nejčastější dotazy ke službě Azure front-dveří

Tento článek obsahuje odpovědi na běžné dotazy k funkcím a funkcím služby Azure front dveří. Pokud nevidíte odpověď na svoji otázku, můžete nás kontaktovat prostřednictvím následujících kanálů (v pořadí eskalace):

1. Část s poznámkami tohoto článku.
2. [Služba Azure pro front-dveří – UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)
3. **Podpora Microsoftu:** Chcete-li vytvořit novou žádost o podporu, v Azure Portal na kartě **help** klikněte na tlačítko **pomoc a podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="general"></a>Obecné

### <a name="what-is-azure-front-door-service"></a>Co je Azure Front Door Service?

Služba front-dveří Azure je Application Delivery Network (a) jako služba, která nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Poskytuje akceleraci dynamického webu (DSA) spolu s globálním vyrovnáváním zatížení téměř v reálném čase. Je to vysoce dostupná a škálovatelná služba, která je plně spravovaná pomocí Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Jaké funkce služba Azure front dveří podporuje?

Služba front-endu Azure podporuje akceleraci dynamického webu (DSA), snižování zátěže SSL a koncové šifrování protokolu SSL, bránu firewall webových aplikací, spřažení relací na základě souborů cookie, směrování na základě cest URL, bezplatné certifikáty a správu více domén a další. Úplný seznam podporovaných funkcí najdete v tématu [Přehled služby Azure front-dveří](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Jaký je rozdíl mezi službou Azure front-dveří a Application Gateway Azure?

I když jsou přední dveře i Application Gateway nástroje pro vyrovnávání zatížení vrstvy 7 (HTTP/HTTPS), hlavním rozdílem je to, že přední dveře představují globální službu, zatímco Application Gateway je místní služba. Zatímco přední dveře můžou vyrovnávat zatížení mezi různými jednotkami škálování/clustery/jednotkami razítek v různých oblastech, Application Gateway vám umožní vyrovnávat zatížení mezi vašimi virtuálními počítači nebo kontejnery atd. to je v rámci jednotky škálování.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Kdy byste měli nasadit Application Gateway za předními dveřmi?

Klíčové scénáře, proč použít Application Gateway za předními dveřmi:

- Přední dveře můžou vyrovnávání zatížení na základě cesty provádět jenom na globální úrovni, ale pokud jeden chce vyrovnávat zatížení provozu ještě dál v rámci své virtuální sítě (VNET), měl by použít Application Gateway.
- Vzhledem k tomu, že přední dvířka nefungují na úrovni virtuálního počítače nebo kontejneru, nemůže připojení vyprázdnit. Application Gateway však umožňuje vyprazdňování připojení. 
- U Application Gateway za AFD může jeden dosáhnout 100% snižování zátěže SSL a směrovat pouze požadavky HTTP v rámci své virtuální sítě (VNET).
- Přední dvířka a Application Gateway obojí podporují spřažení relace. Zatímco přední dveře můžou směrovat následné přenosy z uživatelské relace do stejného clusteru nebo do back-endu v dané oblasti, Application Gateway můžou spřažení provoz směrovat na stejný server v rámci clusteru.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Můžeme nasazovat Azure Load Balancer za předními dveřmi?

Služba front-dveří Azure potřebuje veřejnou virtuální IP adresu nebo veřejně dostupný název DNS pro směrování provozu do. Nasazení Azure Load Balancer za předními dveřmi je běžný případ použití.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Jaké protokoly služba Azure front dveří podporuje?

Služba front-dveří Azure podporuje protokoly HTTP, HTTPS a HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Jak služba Azure front dveří podporuje HTTP/2?

Podpora protokolu HTTP/2 je dostupná jenom pro klienty, kteří se připojují ke službě Azure front-dveří. Komunikace s back-endy ve fondu back-end je přes protokol HTTP/1.1. Podpora HTTP/2 je ve výchozím nastavení povolená.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jaké prostředky už dnes podporujeme jako součást fondu back-endu?

Back-endové fondy se dají skládat z úložiště, webové aplikace, instancí Kubernetes nebo jakéhokoli jiného vlastního názvu hostitele, který má veřejné připojení. Služba front-endu Azure vyžaduje, aby back-endy byly definovány buď prostřednictvím veřejné IP adresy, nebo veřejně přeložitelných názvů hostitelů DNS. Členové back-end fondu můžou být v různých zónách, oblastech nebo dokonce mimo Azure, pokud mají veřejné připojení.

### <a name="what-regions-is-the-service-available-in"></a>Ve kterých oblastech je služba dostupná?

Služba front-dveří Azure je globální služba a není vázaná na žádnou konkrétní oblast Azure. Jediné umístění, které je potřeba zadat při vytváření front-dveří, je umístění skupiny prostředků, které v podstatě určuje, kde se budou ukládat metadata pro skupinu prostředků. Samotný prostředek přední dveře je vytvořen jako globální prostředek a konfigurace je globálně nasazena na všechny body POP (bod přítomnosti). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Jaká jsou umístění POP pro službu front-dveří Azure?

Služba front-dveří Azure má stejný seznam umístění POP (v bodech) jako Azure CDN od Microsoftu. Úplný seznam našich bodů POP najdete v tématu [Azure CDN umístění pop od Microsoftu](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Je služba front-dveří pro Azure vyhrazené nasazení pro moji aplikaci nebo je sdíleno mezi zákazníky?

Služba front-dveří Azure je globálně distribuovaná služba pro více tenantů. Proto je infrastruktura pro přední dveře sdílená mezi všemi svými zákazníky. Vytvoříte-li profil front-dveří, definujete specifickou konfiguraci potřebnou pro vaši aplikaci a žádné změny provedené u front-dveří nebudou mít vliv na jiné konfigurace front-dveří.

### <a name="is-http-https-redirection-supported"></a>Je podporováno přesměrování HTTP-> HTTPS?

Ano. Služba front-in Azure navíc podporuje přesměrování hostitele, cesty a řetězce dotazu a také součást přesměrování adresy URL. Přečtěte si další informace o [přesměrování adresy URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>V jakém pořadí jsou pravidla směrování zpracovaná?

Trasy pro vaše přední dveře nejsou seřazené a na základě nejlepší shody se vybere konkrétní trasa. Přečtěte si další informace o [tom, jak přední dvířka odpovídají požadavkům na pravidlo směrování](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Návody uzamknout přístup k back-endu jenom na přední vrátka Azure?

Pokud chcete aplikaci uzamknout, aby přijímala provoz jenom z vašich konkrétních front, budete muset pro svůj back-end nastavit seznamy ACL pro IP adresu a pak omezit sadu povolených hodnot pro hlavičku X předávaného hostitele, kterou odesílají přední dveře Azure. Tyto kroky jsou podrobně popsané níže:

- Nakonfigurujte IP funkce acling pro vaše back-endy pro příjem provozu z adresního prostoru IP adres back-endu na front-endu a Azure služby infrastruktury. Pracujeme na integraci s [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519) , ale teď můžete na tyto ROZSAHy IP adres odkazovat následujícím způsobem:
 
    - IP místo pro back-end **IPv4** pro front-endu: `147.243.0.0/16`
    - IP adresa back-endu **IPv6** na předních dveřích: `2a01:111:2050::/44`
    - [Základní služby infrastruktury](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Azure prostřednictvím virtualizované IP adresy hostitele: `168.63.129.16` a `169.254.169.254`

    > [!WARNING]
    > Back-endové IP místo pro front-endu se může později změnit, ale zajistíme, že budeme integrovat s [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519). Doporučujeme, abyste se přihlásili k odběru [rozsahů IP adres Azure a značek služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro jakékoli změny nebo aktualizace.

-   Vyfiltruje hodnoty pro příchozí hlavičku**X předaného hostitele**odesílané předními dvířky. Jediné povolené hodnoty pro hlavičku by měly být všechny hostitele front-endu, jak jsou definovány ve vaší frontě konfiguraci před dvířky. Ve skutečnosti přesněji řečeno jenom názvy hostitelů, ze kterých chcete přijímat provoz, v tomto konkrétním back-endu.
    - Příklad – řekněme, že konfigurace front-endu má následující front-endové hostitele _`contoso.azurefd.net`_ (A), _`www.contoso.com`_ (B), _ (C) a _`notifications.contoso.com`_ (D). Pojďme předpokládat, že máte dvě back-endy X a Y. 
    - Back-end X by měl přijímat jenom přenosy z názvů hostitelů a a B. back-end Y může přijímat přenosy z A, C a D.
    - V případě back-endu X byste proto měli přijmout jenom provoz s hlavičkou "**X předávaných hostitelů**" nastavenou na buď _`contoso.azurefd.net`_ , nebo _`www.contoso.com`_ . Pro všechno ostatní by back-end X měl zamítnout přenos.
    - Podobně platí, že na back-endu Y byste měli přijmout jenom provoz s hlavičkou "**X předávaných hostitelů**" nastavenou na _`contoso.azurefd.net`_ , _`api.contoso.com`_ nebo _`notifications.contoso.com`_ . Pro všechno ostatní back-end Y by měl přenos zamítnout.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Může se IP adresa libovolného vysílání změnit během životnosti mých front-dveří?

IP adresa front-endu pro vaše přední dveře by se obvykle neměla měnit a může zůstat statická po dobu života front-dveří. Neexistují však **žádné záruky** za stejné. Neprovádějte žádné přímé závislosti na IP adrese.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Podporuje služba front-dveří pro Azure statické nebo vyhrazené IP adresy?

Ne, služba front-endu Azure momentálně nepodporuje statické nebo vyhrazené IP adresy front-endu. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Podporuje služba front-end v Azure pro hlavičky?

Ano, služba front-end pro Azure podporuje hlavičky x-for, X předávaných hostitelů a X-předávaných. V případě předávaného X-za Pokud hlavička již existovala, připojí přední dveře k ní IP adresu klientského soketu. V opačném případě přidá hlavičku s IP adresou soketu klienta jako hodnotu. U X předávaných-hostitelů a X předávaných hodnot se hodnota přepíše.

Přečtěte si další informace o [podporovaných hlavičkách protokolu HTTP pro front-dveří](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Jak dlouho trvá nasazení služby front-dveří Azure? Pořád při aktualizaci funguje moje dveře?

Pro globální nasazení trvá nové vytvoření nebo jakékoli aktualizace služby na začátku každé z nich o 3 až 5 minut. To znamená přibližně 3 až 5 minut. konfigurace front-dveří se pak globálně nasadí napříč všemi našimi body POP.

Poznámka: globální aktualizace certifikátů SSL trvá přibližně 30 minut, než se nasadí globálně.

## <a name="configuration"></a>Konfigurace

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Může služba Azure Load Balancing nebo směrování provozu v rámci virtuální sítě?

Přední dvířka Azure (AFD) vyžadují pro směrování provozu veřejnou IP adresu nebo název DNS, který se veřejně přeložitelný. Proto odpověď není AFD přímo v rámci virtuální sítě, ale při použití Application Gateway nebo Azure Load Balancer v nástroji se tento scénář vyřeší.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Jaké jsou různé časové limity a omezení pro službu Azure front-dveří?

Přečtěte si o všech dokumentovaných [časových limitech a omezeních pro službu Azure front-dveří](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Výkon

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Jak služba Azure front dveří podporuje vysokou dostupnost a škálovatelnost?

Služba front-dveří Azure je globálně distribuovaná víceklientská platforma s obrovskými objemy kapacity pro potřeby škálovatelnosti vaší aplikace. Přední dveře dodávané z hranice globální sítě Microsoftu poskytují funkce pro vyrovnávání zatížení, které umožňují převzít služby při selhání celé aplikace nebo dokonce i jednotlivé mikroslužby napříč oblastmi nebo různými cloudy.

## <a name="ssl-configuration"></a>Konfigurace SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Jaké verze TLS podporuje služba front-dveří Azure?

Všechny profily front-dveří vytvořené po září 2019 jako výchozí minimum používají TLS 1,2.

Přední dvířka podporují protokol TLS verze 1,0, 1,1 a 1,2. TLS 1,3 není zatím podporován.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Jaké certifikáty jsou podporovány ve službě Azure front-dveří?

Pokud chcete protokol HTTPS povolit pro bezpečné doručování obsahu do vlastní domény na předních dveřích, můžete použít certifikát, který je spravovaný službou Azure front-dveří, nebo použít vlastní certifikát.
Možnost spravovaná přes dvířka zřídí standardní certifikát SSL prostřednictvím DigiCert a uložený v Key Vaultu předních dveří. Pokud se rozhodnete použít vlastní certifikát, můžete připojit certifikát od podporované certifikační autority a může to být standardní protokol SSL, certifikát rozšířeného ověření nebo certifikát se zástupnými znaky. Certifikáty podepsané svým držitelem nejsou podporovány. Naučte [se, jak povolit protokol HTTPS pro vlastní doménu](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Podporuje přední dveře automatické otočení certifikátů?

Pro možnost spravovaného certifikátu front-dveří se certifikáty připravují pomocí front-dveří. Pokud používáte certifikát spravovaný přes dvířka a zjistíte, že je datum vypršení platnosti certifikátu kratší než 60 dní, zajistěte si soubor a lístek podpory.
</br>Pro vlastní certifikát SSL se nepodporuje automatické otočení. Podobně jako při prvním nastavení pro danou vlastní doménu bude nutné, abyste nastavili přední dveře na správnou verzi certifikátu v Key Vault a zajistili, že instanční objekt pro front-dveře má stále přístup k Key Vault. Tato aktualizovaná operace zavedení certifikátu na předních dveřích je atomická a nezpůsobuje žádný dopad na produkční prostředí. název subjektu nebo síť SAN pro certifikát se nezmění.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Jaké jsou aktuální šifrovací sady, které podporuje služba front-dveří Azure?

Níže jsou uvedené aktuální šifrovací sady, které podporuje služba front-dveří Azure:

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Podporuje služba Azure front dveří i opětovné šifrování provozu do back-endu?

Ano, služba front-endu Azure podporuje snižování zátěže SSL a koncové šifrování protokolu SSL, které znovu zašifruje provoz do back-endu. Vzhledem k tomu, že připojení k back-endu nastávají přes veřejnou IP adresu, doporučujeme nakonfigurovat přední dveře tak, aby jako protokol předávání používaly protokol HTTPS.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Můžu nakonfigurovat zásady protokolu SSL pro řízení verzí protokolu SSL?

Minimální verzi protokolu TLS v rámci služby Azure front-dveří můžete nakonfigurovat prostřednictvím [REST API Azure](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). V současné době si můžete vybrat mezi 1,0 a 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Můžu nakonfigurovat přední dveře tak, aby podporovaly pouze konkrétní šifrovací sady?

Ne, konfigurace front-dveří pro konkrétní šifrovací sady není podporována. 

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Jaké typy metrik a protokolů jsou k dispozici ve službě Azure front-dveří?

Informace o protokolech a dalších diagnostických možnostech najdete v tématu [monitorování metrik a protokolů pro přední dveře](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Jaké jsou zásady uchovávání informací v diagnostických protokolech?

Diagnostické protokoly se zaznamenávají do účtu úložiště zákazníků a zákazníci můžou nastavit zásady uchovávání informací na základě jejich preference. Diagnostické protokoly je také možné odeslat do centra událostí nebo do protokolů Azure Monitor. Další informace najdete v tématu [Diagnostika služby front-dveří pro Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Návody získat protokoly auditu pro službu Azure front-dveří?

Protokoly auditu jsou k dispozici pro službu Azure front-dveří. Na portálu klikněte v okně nabídky vaší přední dveře na **Protokol aktivit** , abyste měli přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Můžu nastavit upozornění pomocí služby Azure front-dveří?

Ano, služba Azure front-dveří podporuje výstrahy. Výstrahy jsou nakonfigurovány na metrikách. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
