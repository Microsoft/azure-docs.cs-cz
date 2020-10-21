---
title: Nejčastější dotazy ke službě Azure na přední dveře
description: Na této stránce najdete odpovědi na nejčastější dotazy týkající se služby Azure front-dveří.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2020
ms.author: duau
ms.openlocfilehash: c7a5a02a063f90953982d42fe9c7d2c6dc199b2a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282287"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Nejčastější dotazy pro přední dveře Azure

Tento článek obsahuje odpovědi na běžné dotazy k funkcím a funkcím služby Azure front-dveří. Pokud nevidíte odpověď na svoji otázku, můžete nás kontaktovat prostřednictvím následujících kanálů (v pořadí eskalace):

1. Část s poznámkami tohoto článku.
2. [Přední dveře Azure ve službě UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Podpora Microsoftu:** Chcete-li vytvořit novou žádost o podporu, v Azure Portal na kartě **help** klikněte na tlačítko **pomoc a podpora** a pak vyberte **Nová žádost o podporu**.

## <a name="general"></a>Obecné

### <a name="what-is-azure-front-door"></a>Co je Azure Front Door?

Přední dvířka Azure je Application Delivery Network (a) jako služba, která nabízí různé možnosti vyrovnávání zatížení vrstvy 7 pro vaše aplikace. Poskytuje akceleraci dynamického webu (DSA) spolu s globálním vyrovnáváním zatížení s převzetím služeb při selhání v téměř reálném čase. Jedná se o vysoce dostupnou a škálovatelnou službu, kterou plně spravuje Azure.

### <a name="what-features-does-azure-front-door-support"></a>Jaké funkce nabízí podpora front Azure?

Přední dvířka Azure podporují snižování zátěže dynamického webového serveru (DSA), snižování zátěže TLS/SSL a koncového šifrování TLS, Firewall webových aplikací, spřažení relací na základě souborů cookie, směrování na základě cest URL, bezplatné certifikáty a správu více domén a další. Úplný seznam podporovaných funkcí najdete v tématu [Přehled front-dveří Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Jaký je rozdíl mezi předními dveřmi Azure a Application Gateway Azure?

I když jsou přední dveře i Application Gateway nástroje pro vyrovnávání zatížení vrstvy 7 (HTTP/HTTPS), hlavním rozdílem je to, že přední dveře představují globální službu, zatímco Application Gateway je místní služba. Zatímco přední dveře můžou vyrovnávat zatížení mezi různými jednotkami škálování/clustery/jednotkami razítek v různých oblastech, Application Gateway vám umožní vyrovnávat zatížení mezi vašimi virtuálními počítači nebo kontejnery atd. to je v rámci jednotky škálování.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Kdy byste měli nasadit Application Gateway za předními dveřmi?

Klíčové scénáře, proč použít Application Gateway za předními dveřmi:

- Přední dveře můžou vyrovnávání zatížení na základě cesty provádět jenom na globální úrovni, ale pokud jeden chce vyrovnávat zatížení provozu ještě dál v rámci své virtuální sítě (VNET), měl by použít Application Gateway.
- Vzhledem k tomu, že přední dvířka nefungují na úrovni virtuálního počítače nebo kontejneru, nemůže připojení vyprázdnit. Application Gateway však umožňuje vyprazdňování připojení. 
- U Application Gateway za předními dveřmi může jedna z nich dosáhnout 100% snižování zátěže TLS/SSL a směrovat pouze požadavky HTTP v rámci své virtuální sítě (VNET).
- Přední dvířka a Application Gateway obojí podporují spřažení relace. Zatímco přední dveře můžou směrovat následné přenosy z uživatelské relace do stejného clusteru nebo do back-endu v dané oblasti, Application Gateway můžou spřažení provoz směrovat na stejný server v rámci clusteru.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Můžeme nasazovat Azure Load Balancer za předními dveřmi?

Přední dveře Azure vyžadují veřejnou virtuální IP adresu nebo veřejně dostupný název DNS pro směrování provozu do. Nasazení Azure Load Balancer za předními dveřmi je běžný případ použití.

### <a name="what-protocols-does-azure-front-door-support"></a>Jaké protokoly podporuje přední dveře Azure?

Přední dvířka Azure podporují HTTP, HTTPS a HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Jak Azure front-dveře podporuje HTTP/2?

Podpora protokolu HTTP/2 je dostupná jenom pro klienty, kteří se připojují k Azure na přední dveře. Komunikace s back-endy ve fondu back-end je přes protokol HTTP/1.1. Podpora HTTP/2 je ve výchozím nastavení povolená.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jaké prostředky už dnes podporujeme jako součást fondu back-endu?

Back-endové fondy se dají skládat z úložiště, webové aplikace, instancí Kubernetes nebo jakéhokoli jiného vlastního názvu hostitele, který má veřejné připojení. Přední vrátka Azure vyžaduje, aby back-endy byly definovány buď prostřednictvím veřejné IP adresy, nebo veřejně přeložitelných názvů hostitelů DNS. Členové back-end fondu můžou být v různých zónách, oblastech nebo dokonce mimo Azure, pokud mají veřejné připojení.

### <a name="what-regions-is-the-service-available-in"></a>Ve kterých oblastech je služba dostupná?

Přední vrátka Azure je globální služba a není vázaná na žádnou konkrétní oblast Azure. Jediné umístění, které je potřeba zadat při vytváření front-dveří, je umístění skupiny prostředků, které v podstatě určuje, kde se budou ukládat metadata pro skupinu prostředků. Samotný prostředek přední dveře je vytvořen jako globální prostředek a konfigurace je globálně nasazena na všechny body POP (bod přítomnosti). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Jaká jsou umístění POP pro přední dveře Azure?

Přední dveře Azure mají stejný seznam umístění POP (v bodech) jako Azure CDN od Microsoftu. Úplný seznam našich bodů POP najdete v tématu [Azure CDN umístění pop od Microsoftu](../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Je přední z bran Azure vyhrazené nasazení pro moji aplikaci nebo je sdíleno mezi zákazníky?

Přední dvířka Azure je globálně distribuovaná služba pro více tenantů. Proto je infrastruktura pro přední dveře sdílená mezi všemi svými zákazníky. Vytvoříte-li profil front-dveří, definujete specifickou konfiguraci potřebnou pro vaši aplikaci a žádné změny provedené u front-dveří nebudou mít vliv na jiné konfigurace front-dveří.

### <a name="is-http-https-redirection-supported"></a>Je podporováno přesměrování HTTP->HTTPS?

Ano. Ve skutečnosti podporuje přední dveře Azure hostitele, cestu a přesměrování řetězce dotazu a také součást přesměrování adresy URL. Přečtěte si další informace o [přesměrování adresy URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>V jakém pořadí jsou pravidla směrování zpracovaná?

Trasy pro vaše přední dveře nejsou seřazené a na základě nejlepší shody se vybere konkrétní trasa. Přečtěte si další informace o [tom, jak přední dvířka odpovídají požadavkům na pravidlo směrování](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Návody uzamknout přístup k back-endu jenom na přední vrátka Azure?

Pokud chcete aplikaci uzamknout, aby přijímala provoz jenom z vašich konkrétních front, budete muset pro svůj back-end nastavit seznamy řízení přístupu (ACL) IP adres a potom omezit provoz back-endu na konkrétní hodnotu hlavičky X-Azure-FDID, kterou odesílají přední dveře. Tyto kroky jsou podrobně popsané níže:

- Nakonfigurujte IP funkce acling pro vaše back-endy pro příjem provozu z adresního prostoru IP adres back-endu na front-endu a Azure služby infrastruktury. Přečtěte si následující podrobnosti o IP adrese pro funkce acling back-end:
 
    - Přečtěte si část *AzureFrontDoor. back-end* v tématu [rozsahy IP adres Azure a značky služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro rozsah IP adres back-endu IPv4 front-endu nebo můžete ve svých [skupinách zabezpečení sítě](../virtual-network/network-security-groups-overview.md#security-rules)použít také značku služby *AzureFrontDoor. back-end* .
    - IP adresa back-endu **IPv6** front-endu, která je popsaná ve značce služby, není uvedená v souboru JSON rozsahy IP adres Azure. Pokud hledáte explicitní rozsah adres IPv6, je aktuálně omezen na `2a01:111:2050::/44`
    - [Základní služby infrastruktury](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) Azure prostřednictvím virtualizované IP adresy hostitele: `168.63.129.16` a`169.254.169.254`

    > [!WARNING]
    > Back-endové IP místo pro front-endu se může později změnit, ale zajistíme, že budeme integrovat s [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519). Doporučujeme, abyste se přihlásili k odběru [rozsahů IP adres Azure a značek služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro jakékoli změny nebo aktualizace.

-    Proveďte operaci GET na front-dveřích s verzí rozhraní API `2020-01-01` nebo vyšší. V volání rozhraní API vyhledejte `frontdoorID` pole. Vyfiltrujte příchozí hlavičku**X-Azure-FDID**, kterou odeslala přední dvířka do back-endu, s hodnotou, která je v poli `frontdoorID` . Hodnotu můžete najít také `Front Door ID` v části Přehled na stránce portálu front dveří. 

- Na webovém serveru back-end použijte filtrování pravidel pro omezení provozu na základě výsledné hodnoty hlavičky X-Azure-FDID.

  Tady je příklad pro [Microsoft Internetová informační služba (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Může se IP adresa libovolného vysílání změnit během životnosti mých front-dveří?

IP adresa front-endu pro vaše přední dveře by se obvykle neměla měnit a může zůstat statická po dobu života front-dveří. Neexistují však **žádné záruky** za stejné. Neprovádějte žádné přímé závislosti na IP adrese.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Podporuje přední dveře Azure statické nebo vyhrazené IP adresy?

Ne, přední dveře Azure aktuálně nepodporují statické nebo vyhrazené IP adresy front-endu. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Podporuje přední dveře Azure pro hlavičky, které jsou předány?

Ano, přední dvířka Azure podporují hlavičky x-for, X předávaných hostitelů a X-předávaných za běhu. V případě předávaného X-za Pokud hlavička již existovala, připojí přední dveře k ní IP adresu klientského soketu. V opačném případě přidá hlavičku s IP adresou soketu klienta jako hodnotu. U X předávaných-hostitelů a X předávaných hodnot se hodnota přepíše.

Přečtěte si další informace o [podporovaných hlavičkách protokolu HTTP pro front-dveří](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Jak dlouho trvá nasazení front-dveří Azure? Pořád při aktualizaci funguje moje dveře?

Pro globální nasazení trvá nové vytvoření nebo jakékoli aktualizace služby na začátku každé z nich o 3 až 5 minut. To znamená přibližně 3 až 5 minut. konfigurace front-dveří se pak globálně nasadí napříč všemi našimi body POP.

Poznámka: vlastní aktualizace certifikátu TLS/SSL trvá přibližně 30 minut, než se nasadí globálně.

Jakékoli aktualizace pro trasy nebo fondy back-endu atd. jsou bezproblémové a způsobí nulové výpadky (Pokud je nová konfigurace správná). Aktualizace certifikátů se také považují za atomické a nezpůsobují žádný výpadek, pokud se nepřepne z ' AFD Managed ' na ' použít svůj vlastní certifikát ' nebo naopak.


## <a name="configuration"></a>Konfigurace

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Může služba Azure Load Balancing nebo směrování provozu v rámci virtuální sítě?

Přední dvířka Azure (AFD) vyžadují pro směrování provozu veřejnou IP adresu nebo název DNS, který se veřejně přeložitelný. Proto odpověď není AFD přímo v rámci virtuální sítě, ale při použití Application Gateway nebo Azure Load Balancer v nástroji se tento scénář vyřeší.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Jaké jsou různé časové limity a omezení pro přední dveře Azure?

Přečtěte si o všech dokumentovaných [časových limitech a omezeních pro přední dveře Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Jak dlouho trvá, než se pravidlo projeví po přidání do modulu pravidel pro přední dveře?

Dokončení aktualizace v konfiguraci modulu pravidel trvá přibližně 10 až 15 minut. Můžete očekávat, že se pravidlo projeví až po dokončení aktualizace. 

## <a name="performance"></a>Výkon

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Jak Azure front-dveře podporuje vysokou dostupnost a škálovatelnost?

Přední dvířka Azure jsou globálně distribuovaná víceklientská platforma s obrovskými objemy kapacity pro potřeby škálovatelnosti vaší aplikace. Přední dveře dodávané z hranice globální sítě Microsoftu poskytují funkce pro vyrovnávání zatížení, které umožňují převzít služby při selhání celé aplikace nebo dokonce i jednotlivé mikroslužby napříč oblastmi nebo různými cloudy.

## <a name="tls-configuration"></a>Konfigurace TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Jaké verze TLS podporuje i přední dvířka Azure?

Všechny profily front-dveří vytvořené po září 2019 jako výchozí minimum používají TLS 1,2.

Přední dvířka podporují protokol TLS verze 1,0, 1,1 a 1,2. TLS 1,3 není zatím podporován.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Jaké certifikáty jsou podporované na frontách Azure na předních dveřích?

Pokud chcete protokol HTTPS povolit pro bezpečné doručování obsahu do vlastní domény na předních dveřích, můžete použít certifikát, který je spravovaný přes službu Azure front-dveří, nebo použít vlastní certifikát.
Možnost spravovaná přes dvířka zřídí standardní certifikát TLS/SSL prostřednictvím DigiCert a uložený v Key Vaultu na frontě. Pokud se rozhodnete použít vlastní certifikát, můžete připojit certifikát od podporované certifikační autority a může to být standardní TLS, rozšířený ověřovací certifikát nebo certifikát se zástupnými znaky. Certifikáty podepsané svým držitelem nejsou podporovány. Naučte [se, jak povolit protokol HTTPS pro vlastní doménu](./front-door-custom-domain-https.md).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Podporuje přední dveře automatické otočení certifikátů?

Pro možnost spravovaného certifikátu front-dveří se certifikáty připravují pomocí front-dveří. Pokud používáte certifikát spravovaný přes dvířka a zjistíte, že je datum vypršení platnosti certifikátu kratší než 60 dní, zajistěte si soubor a lístek podpory.
</br>Pro vlastní certifikát TLS/SSL se nepodporuje automatické otočení. Podobně jako při prvním nastavení pro danou vlastní doménu bude nutné, abyste nastavili přední dveře na správnou verzi certifikátu v Key Vault a zajistili, že instanční objekt pro front-dveře má stále přístup k Key Vault. Tato aktualizovaná operace zavedení certifikátu na předních dveřích je atomická a nezpůsobuje žádný dopad na produkční prostředí. název subjektu nebo síť SAN pro certifikát se nezmění.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Jaké jsou aktuální šifrovací sady, které jsou podporovány předními dvířky Azure?

Pro protokol TLS 1.2 jsou podporovány následující šifrovací sady: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Při použití vlastních domén s povoleným protokolem TLS 1.0/1.1 jsou podporovány následující šifrovací sady:

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

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Můžu nakonfigurovat zásady TLS pro řízení verzí protokolu TLS?

Minimální verzi protokolu TLS v frontách Azure můžete nakonfigurovat v nastaveních HTTPS vlastní domény pomocí Azure Portal nebo [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). V současné době si můžete vybrat mezi 1,0 a 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Můžu nakonfigurovat přední dveře tak, aby podporovaly pouze konkrétní šifrovací sady?

Ne, konfigurace front-dveří pro konkrétní šifrovací sady není podporována. Vlastní certifikát TLS/SSL ale můžete získat od certifikační autority (například VeriSign, svěřit nebo DigiCert) a mít konkrétní šifrovací sady označené certifikátem, když ho vygenerujete. 

### <a name="does-front-door-support-ocsp-stapling"></a>Podporuje se pro připojení k protokolu OCSP i přední dvířka?

Ano, ve výchozím nastavení je připojení protokolu OCSP podporované předními dveřmi a nevyžaduje se žádná konfigurace.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Podporuje i přední dveře Azure opětovné šifrování provozu do back-endu?

Ano, přední dvířka Azure podporují snižování zátěže TLS/SSL a koncového šifrování TLS, které znovu zašifruje provoz do back-endu. Vzhledem k tomu, že připojení k back-endu nastávají přes veřejnou IP adresu, doporučujeme nakonfigurovat přední dveře tak, aby jako protokol předávání používaly protokol HTTPS.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Podporují přední dveře certifikáty podepsané svým držitelem v back-endu pro připojení HTTPS?

Ne, certifikáty podepsané svým držitelem nejsou podporovány na front-dveřích a omezení platí pro:

1. **Back-endy**: certifikáty podepsané svým držitelem nemůžete použít při předávání provozu jako sondy stavu HTTPS nebo HTTPS nebo při vyplňování mezipaměti pro zdroj od počátku pro pravidla směrování s povoleným ukládáním do mezipaměti.
2. **Front-end**: certifikáty podepsané svým držitelem nemůžete použít při použití vlastního certifikátu TLS/SSL pro povolení protokolu HTTPS ve vaší vlastní doméně.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Proč selhává přenos HTTPS na můj back-end?

Pro úspěšné připojení HTTPS k back-endu, ať už pro sondy stavu nebo pro předávání požadavků, můžou existovat dva důvody, proč může selhání provozu HTTPS:

1. **Neshoda názvů subjektu certifikátu**: u připojení HTTPS očekává přední dveře, že váš server back-end prezentuje certifikát od platné certifikační autority s názvy subjektů odpovídajícími názvu hostitele back-endu. Pokud je například váš název hostitele back-endu nastavený na `myapp-centralus.contosonews.net` a certifikát, který váš back-end prezentuje během metody handshake `myapp-centralus.contosonews.net` TLS `*myapp-centralus*.contosonews.net` , ani v názvu subjektu není, zamítne Tato dvířka připojení a výsledkem bude chyba. 
    1. **Řešení**: i když se z hlediska dodržování předpisů nedoporučuje, můžete tuto chybu obejít tím, že pro vaše přední dveře zakážete kontrolu názvu subjektu certifikátu. Tato možnost je k dispozici v části nastavení v Azure Portal a v části BackendPoolsSettings v rozhraní API.
2. **Certifikát hostování back-endu z neplatného certifikační autority**: pro back-end s předními dveřmi lze použít pouze certifikáty z [platných certifikačních autorit](./front-door-troubleshoot-allowed-ca.md) . Certifikáty z interních certifikačních autorit nebo certifikátů podepsaných svým držitelem nejsou povoleny.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Můžu použít klienta a vzájemné ověřování s využitím front-dveří Azure?

Ne. I když přední dvířka Azure podporují protokol TLS 1,2, který zavedl klient a vzájemné ověřování v [RFC 5246](https://tools.ietf.org/html/rfc5246), v současné době se u front-in Azure nepodporuje klient a vzájemné ověřování.

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Jaké typy metrik a protokolů jsou k dispozici v rámci služby Azure front-dveří?

Informace o protokolech a dalších diagnostických možnostech najdete v tématu [monitorování metrik a protokolů pro přední dveře](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Jaké jsou zásady uchovávání informací v diagnostických protokolech?

Diagnostické protokoly se zaznamenávají do účtu úložiště zákazníků a zákazníci můžou nastavit zásady uchovávání informací na základě jejich preference. Diagnostické protokoly je také možné odeslat do centra událostí nebo do protokolů Azure Monitor. Další informace najdete v tématu [Diagnostika front-dveří pro Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Návody získat protokoly auditu pro přední dveře Azure?

Protokoly auditu jsou k dispozici pro přední dveře Azure. Na portálu klikněte v okně nabídky vaší přední dveře na **Protokol aktivit** , abyste měli přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Můžu nastavovat upozornění pomocí front-dveří Azure?

Ano, přední dveře Azure podporují výstrahy. Výstrahy jsou nakonfigurovány na metrikách. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).