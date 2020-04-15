---
title: Azure Front Door – nejčastější dotazy
description: Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se azure front door
services: frontdoor
documentationcenter: ''
author: sohamnchatterjee
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2020
ms.author: sohamnc
ms.openlocfilehash: e2785baab27f5bfc996b57607816062195a19b2b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313758"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Nejčastější dotazy týkající se dveří Azure Front Door

Tento článek odpovídá na běžné otázky týkající se funkcí a funkcí Azure Front Door. Pokud odpověď na vaši otázku nevidíte, můžete nás kontaktovat prostřednictvím následujících kanálů (v eskalujícím pořadí):

1. Komentáře v tomto článku.
2. [Azure Front Door UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Podpora společnosti Microsoft:** Pokud chcete vytvořit novou žádost o podporu, na webu Azure portal na kartě **Nápověda** vyberte tlačítko **Nápověda + podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="general"></a>Obecné

### <a name="what-is-azure-front-door"></a>Co je Azure Front Door?

Azure Front Door je síť pro doručování aplikací (ADN) jako služba, která nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Poskytuje dynamickou akceleraci webu (DSA) spolu s globálním vyrovnáváním zatížení s převzetím služeb při selhání téměř v reálném čase. Jedná se o vysoce dostupnou a škálovatelnou službu, kterou plně spravuje Azure.

### <a name="what-features-does-azure-front-door-support"></a>Jaké funkce azure front door podporuje?

Azure Front Door podporuje dynamickou akceleraci webu (DSA), snižování zátěže TLS/SSL a od konce tls, bránou firewall webové aplikace, spřažení relací na základě souborů cookie, směrování na základě cesty url, bezplatné certifikáty a správu více domén a další. Úplný seznam podporovaných funkcí najdete v [tématu Přehled předních dveří Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Jaký je rozdíl mezi Azure Front Door a Azure Application Gateway?

Zatímco front door a aplikační brána jsou nástroj pro vyrovnávání zatížení vrstvy 7 (HTTP/HTTPS), primární rozdíl je, že přední dveře jsou globální službou, zatímco aplikační brána je regionální služba. Zatímco přední dveře mohou vyvážit zatížení mezi různými jednotkami stupnice / clustery / razítkem napříč oblastmi, aplikační brána umožňuje vyrovnávání zatížení mezi virtuálními počítači/kontejnery atd., které je v rámci jednotky škálování.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Kdy bychom měli nasadit aplikační bránu za předními dveřmi?

Klíčové scénáře, proč by měl používat aplikační bránu za předními dveřmi, jsou:

- Front Door můžete provádět vyrovnávání zatížení založené na cestě pouze na globální úrovni, ale pokud jeden chce, aby zatížení provozu ještě dále v rámci své virtuální sítě (VNET), pak by měli používat aplikační bránu.
- Vzhledem k tomu, že přední dveře nefunguje na úrovni virtuálního připojení/kontejneru, takže nemůže provést vyprázdnění připojení. Aplikační brána však umožňuje provést vyprázdnění připojení. 
- S aplikační bránou za AFD lze dosáhnout 100% snížení zátěže TLS/SSL a směrovat pouze požadavky HTTP v rámci své virtuální sítě (VNET).
- Front Door a Application Gateway podporují spřažení relací. Zatímco front door může směrovat následný provoz z relace uživatele do stejného clusteru nebo back-endu v dané oblasti, aplikační brána může směrovat spřažení přenosů na stejný server v rámci clusteru.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Můžeme nasadit Azure Load Balancer za přední dveře?

Azure Front Door potřebuje veřejnou virtuální IP adresu nebo veřejně dostupný název DNS, na který bude provoz směrovat. Nasazení Azure Balancer za přední dveře je běžné použití případu.

### <a name="what-protocols-does-azure-front-door-support"></a>Jaké protokoly azure front door podporuje?

Azure Front Door podporuje HTTP, HTTPS a HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Jak Azure Front Door podporuje HTTP/2?

Podpora protokolu HTTP/2 je k dispozici klientům, kteří se připojují jenom k Azure Front Door. Komunikace back-endů v back-endu je přes HTTP/1.1. Podpora protokolu HTTP/2 je ve výchozím nastavení povolena.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jaké prostředky jsou dnes podporovány jako součást back-endového fondu?

Back-endové fondy se mohou skládat z instancí Storage, Web App, Kubernetes nebo jiného vlastního názvu hostitele, který má veřejné připojení. Azure Front Door vyžaduje, aby back-endy byly definovány buď prostřednictvím veřejné IP adresy nebo veřejně řešitelného názvu hostitele DNS. Členové back-endových fondů můžou být napříč zónami, oblastmi nebo dokonce mimo Azure, pokud mají veřejné připojení.

### <a name="what-regions-is-the-service-available-in"></a>V jakých oblastech je služba dostupná?

Azure Front Door je globální služba a není vázaná na žádnou konkrétní oblast Azure. Jediné umístění, které je třeba zadat při vytváření předních dveří, je umístění skupiny prostředků, které v podstatě určuje, kde budou uložena metadata pro skupinu prostředků. Prostředek front door sám je vytvořen jako globální prostředek a konfigurace je nasazena globálně na všechny POP (Bod přítomnosti). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Jaká jsou umístění bodů POP pro azure front door?

Azure Front Door má stejný seznam míst POP (Point of Presence) jako Azure CDN od Microsoftu. Úplný seznam našich bodů POP naleznete v [seznamu umístění Azure CDN POP od Microsoftu](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Je Azure Front Door vyhrazené nasazení pro mou aplikaci nebo je sdíleno mezi zákazníky?

Azure Front Door je globálně distribuovaná víceklientská služba. Takže infrastruktura pro Front Door je sdílena mezi všemi svými zákazníky. Vytvořením profilu předních dveří však definujete konkrétní konfiguraci požadovanou pro vaši aplikaci a žádné změny provedené v předních dveřích neovlivní ostatní konfigurace předních dveří.

### <a name="is-http-https-redirection-supported"></a>Je přesměrování HTTP->HTTPS podporováno?

Ano. Azure Front Door ve skutečnosti podporuje přesměrování řetězce hostitele, cesty a dotazu a také součást přesměrování adres URL. Další informace o [přesměrování adres URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>V jakém pořadí jsou zpracována pravidla směrování?

Trasy pro vaše přední dveře nejsou objednány a konkrétní trasa je vybrána na základě nejlepší shody. Další informace o [tom, jak front door odpovídá požadavkům na pravidlo směrování](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Jak uzamknu přístup k back-endu jenom k hlavním dveřím Azure?

Chcete-li uzamknout aplikaci přijímat provoz pouze z konkrétních předních dveří, budete muset nastavit IP ACLs pro back-end a pak omezit provoz na back-endu na konkrétní hodnotu záhlaví 'X-Azure-FDID' odeslané Front Door. Tyto kroky jsou podrobně popsány takto:

- Nakonfigurujte ip acling pro vaše back-endy tak, aby přijímali provoz z back-endového ip adresového prostoru Azure Front Door a pouze z infrastrukturních služeb Azure. Podívejte se na IP podrobnosti níže pro ACLing váš backend:
 
    - Odkazovat *AzureFrontDoor.Backend* části v [Rozsahu IP Azure a značky služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro frontdoor je IPv4 rozsah ip adres IP nebo můžete také použít značku služby *AzureFrontDoor.Backend* ve [skupinách zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) nebo s Azure [Firewall](https://docs.microsoft.com/azure/firewall/service-tags).
    - Back-endIP prostor **IPv6** společnosti Front Door, který je zakrytý servisní majem, není uveden v souboru JSON rozsahů Azure IP. Pokud hledáte explicitní rozsah adres IPv6, je v současné době omezen na`2a01:111:2050::/44`
    - [Základní infrastrukturní služby](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Azure prostřednictvím virtualizovaných IP adres hostitele: `168.63.129.16` a`169.254.169.254`

    > [!WARNING]
    > Back-endIP prostor předních dveří se může později změnit, ale zajistíme, že dříve než k tomu dojde, že bychom se integrovali s [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519). Doporučujeme, abyste se přihlásili k odběru [rozsahů IP adres Azure a značek služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro všechny změny nebo aktualizace.

-    Proveďte operaci GET na předních `2020-01-01` dveřích s verzí rozhraní API nebo vyšší. Ve volání rozhraní API `frontdoorID` vyhledejte pole. Filtrujte na příchozí záhlaví**X-Azure-FDID**odeslané frontdoorem do back-endu `frontdoorID`s hodnotou jako hodnota pole . 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Může se ip adresa libovolného vysílání během životnosti mých předních dveří změnit?

Front-end anycast IP pro vaše přední dveře by se obvykle nemělměnit a může zůstat statický po celou dobu životnosti předních dveří. Neexistují však **žádné záruky** pro stejné. Laskavě neužívejte žádné přímé závislosti na IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Podporuje Azure Front Door statické nebo vyhrazené IP adresy?

Ne, Azure Front Door aktuálně nepodporuje statické nebo vyhrazené front-end žádné ip adresy vysílání. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Podporuje Azure Front Door záhlaví x-forwarded-for?

Ano, Azure Front Door podporuje záhlaví X-Forwarded-For, X-Forwarded-Host a X-Forwarded-Proto. Pro X-Forwarded-For, pokud záhlaví již bylo k dispozici, pak front door připojí ip adresy klienta. Jinak přidá hlavičku s IP klientem soketu jako hodnotu. Pro X-Forwarded-Host a X-Forwarded-Proto je hodnota přepsána.

Další informace o [hlavičkách HTTP podporovaných předními dveřmi](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Jak dlouho trvá nasazení předních dveří Azure? Funguje moje přední dveře i při aktualizaci?

Vytvoření nového předního dveří nebo jakékoli aktualizace existujících předních dveří trvá přibližně 3 až 5 minut pro globální nasazení. To znamená, že přibližně za 3 až 5 minut bude konfigurace předních dveří nasazena na všechny naše POP po celém světě.

Poznámka – Aktualizace vlastních certifikátů TLS/SSL se nasazují globálně přibližně 30 minut.

Všechny aktualizace tras nebo back-endových fondů atd. Aktualizace certifikátu jsou také atomické a nezpůsobí žádný výpadek, pokud přechod z "AFD Spravované" na "Použít vlastní certifikát" nebo naopak.


## <a name="configuration"></a>Konfigurace

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Může azure front door vyrovnávání zatížení nebo směrování provozu v rámci virtuální sítě?

Azure Front Door (AFD) vyžaduje veřejnou IP adresu nebo veřejně řešitelný název DNS pro směrování provozu. Takže odpověď není AFD přímo nelze směrovat v rámci virtuální sítě, ale pomocí aplikační brány nebo Nástroje pro vyrovnávání zatížení Azure mezi vyřeší tento scénář.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Jaké jsou různé časové limity a limity pro Azure Front Door?

Seznamte se se všemi zdokumentovanámi [časovými limity a limity pro Azure Front Door](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Výkon

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Jak Azure Front Door podporuje vysokou dostupnost a škálovatelnost?

Azure Front Door je globálně distribuovaná platforma pro více klientů s obrovskýmobjemem kapacity, která uspokojí škálovatelnost vaší aplikace. Front Door, který je dodáván z okraje globální sítě Microsoftu, poskytuje globální možnost vyrovnávání zatížení, které vám umožní převzetí služeb při selhání celé aplikace nebo dokonce jednotlivé mikroslužby napříč oblastmi nebo různými cloudy.

## <a name="tls-configuration"></a>Konfigurace TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Jaké verze TLS jsou podporovány Azure Front Door?

Všechny profily předních dveří vytvořené po září 2019 používají tls 1.2 jako výchozí minimum.

Přední dveře podporují TLS verze 1.0, 1.1 a 1.2. TLS 1.3 ještě není podporován.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Jaké certifikáty jsou podporované na Azure Front Door?

Chcete-li povolit protokol HTTPS pro bezpečné doručování obsahu na vlastní doméně Front Door, můžete použít certifikát, který je spravovaný Azure Front Door nebo použít vlastní certifikát.
Možnost spravovaná předními dveřmi zřizována je standardní certifikát TLS/SSL prostřednictvím digicertu a uložená v trezoru klíčů předních dveří. Pokud se rozhodnete použít vlastní certifikát, můžete certifikát zavést od podporované certifikační autority a může se z nich povést standardní certifikát TLS, certifikát rozšířeného ověření nebo dokonce certifikát se zástupnými kódy. Certifikáty podepsané svým držitelem nejsou podporovány. Přečtěte [si, jak povolit protokol HTTPS pro vlastní doménu](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Podporuje přední dveře automatické střídání certifikátů?

Pro možnost spravovaného certifikátu Přední dveře jsou certifikáty automaticky otočeny předními dveřmi. Pokud používáte certifikát se spravovaným předními dveřmi a zjistíte, že datum vypršení platnosti certifikátu je kratší než 60 dní, nasuzte lístek podpory.
</br>Pro vlastní certifikát TLS/SSL není automatické otáčení podporováno. Podobně jako to, jak byla nastavena poprvé pro danou vlastní doménu, budete muset v trezoru klíčů nasměrovat přední dveře na správnou verzi certifikátu a zajistit, aby byl instanční objekt pro frontdoor stále přístup k trezoru klíčů. Tato aktualizovaná operace zavádění certifikátu předními dveřmi je atomická a nezpůsobuje žádný dopad výroby za předpokladu, že se nezmění název subjektu nebo san pro certifikát.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Jaké jsou aktuální šifrovací sady podporované Azure Front Door?

Pro TLS1.2 jsou podporovány následující šifrovací sady

TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 TLS_DHE_RSA_WITH_AES_256_GCM_SHA384 TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Při použití vlastních domén s povolenou technologií TLS1.0/1.1 jsou podporovány následující šifrovací sady:

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
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Mohu nakonfigurovat zásady TLS pro řízení verzí protokolu TLS?

Minimální verzi TLS můžete nakonfigurovat ve službě Azure Front Door ve vlastním nastavení HTTPS domény prostřednictvím portálu Azure nebo [rozhraní AZURE REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). V současné době si můžete vybrat mezi 1.0 a 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Mohu nakonfigurovat přední dveře tak, aby podporovaly pouze určité šifrovací sady?

Ne, konfigurace předních dveří pro konkrétní šifrovací sady není podporována. Můžete však získat vlastní certifikát TLS/SSL od certifikační autority (řekněme Verisign, Entrust nebo Digicert) a mít na certifikátu vyznačené specifické šifrovací sady, když jste jej vygenerovali. 

### <a name="does-front-door-support-ocsp-stapling"></a>Podporuje přední dveře sešívání OCSP?

Ano, sešívání OCSP je ve výchozím nastavení podporováno předními dveřmi a není vyžadována žádná konfigurace.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Podporuje azure front door také opětovné šifrování provozu do back-endu?

Ano, Azure Front Door podporuje snižování zátěže TLS/SSL a koncové mezikoncovými protokoly TLS, což znovu zašifruje provoz do back-endu. Ve skutečnosti, protože připojení k back-endu dojít přes jeho veřejné IP, je doporučeno nakonfigurovat přední dveře pro použití protokolu HTTPS jako protokol předávání.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Podporuje front door certifikáty podepsané svým držitelem v back-endu pro připojení HTTPS?

Ne, certifikáty podepsané svým držitelem nejsou u předních dveří podporovány a omezení se vztahuje na obojí:

1. **Back-endy**: Certifikáty podepsané svým držitelem nelze použít při předávání přenosů jako sond stavu HTTPS nebo HTTPS nebo vyplňování mezipaměti od počátku pro pravidla směrování s povoleným ukládáním do mezipaměti.
2. **Front-end**: Při použití vlastního certifikátu TLS/SSL pro povolení protokolu HTTPS ve vlastní doméně nelze použít certifikáty podepsané svým držitelem.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Proč se lhající přenos protokolu HTTPS do mého back-endu nedaří?

Pro úspěšné připojení HTTPS k back-endu, ať už pro sondy stavu nebo pro předávání požadavků, může být dva důvody, proč může dojít k selhání přenosu HTTPS:

1. **Neshoda názvů předmětů certifikátu**: U připojení HTTPS front door očekává, že back-end zobrazí certifikát z platné certifikační autority s názvem subjektu odpovídajícím názvu hostitele back-endu. Jako příklad pokud je název hostitele back-end nastaven `myapp-centralus.contosonews.net` a certifikát, který back-end představuje `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` během tls handshake, nemá ani v názvu předmětu, front door odmítne připojení a výsledkem je chyba. 
    1. **Řešení:** I když se to z hlediska dodržování předpisů nedoporučuje, můžete tuto chybu vyřešit zakázáním kontroly názvu předmětu certifikátu pro přední dveře. To se nachází v části Nastavení na webu Azure portal a v části BackendPoolsSettings v rozhraní API.
2. **Back-endový hostingový certifikát od neplatné certifikační autority**: V back-endu s frontdoorem lze použít pouze certifikáty od [platných certifikačních autorit.](/azure/frontdoor/front-door-troubleshoot-allowed-ca) Certifikáty interních certifikačních autorit nebo certifikátů podepsaných svým držitelem nejsou povoleny.

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Jaké typy metrik a protokolů jsou dostupné u Azure Front Door?

Informace o protokolech a dalších diagnostických funkcích naleznete [v tématu Monitorování metrik a protokolů pro frontdoor](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Jaké jsou zásady uchovávání informací v protokolech diagnostiky?

Diagnostické protokoly toku do účtu úložiště zákazníků a zákazníci mohou nastavit zásady uchovávání informací na základě jejich preference. Diagnostické protokoly lze také odeslat do centra událostí nebo azure monitor protokoly. Další informace naleznete [v tématu Diagnostika předních dveří Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Jak získám protokoly auditu pro Azure Front Door?

Protokoly auditu jsou k dispozici pro Azure Front Door. Na portálu klikněte na **protokol aktivit** v okně nabídky přednídveře pro přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Můžu nastavit upozornění pomocí Azure Front Door?

Ano, Azure Front Door podporuje výstrahy. Výstrahy jsou konfigurovány na metriky. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
