---
title: 'Přední dveře Azure: nejčastější dotazy'
description: Na této stránce najdete odpovědi na nejčastější dotazy týkající se služby Azure front-end Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a42601b696f292e9d2a9da90070fea3662acae87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099112"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Nejčastější dotazy týkající se služby Azure front bran Standard/Premium (Preview)

Tento článek obsahuje odpovědi na běžné dotazy k funkcím a funkcím služby Azure front-dveří.

## <a name="general"></a>Obecné

### <a name="what-is-azure-front-door"></a>Co je Azure Front Door?

Přední dvířka Azure jsou rychlá, spolehlivá a zabezpečená moderní cloudová síť CDN s inteligentní ochranou hrozeb. Poskytuje statickou a dynamickou akceleraci obsahu, globální vyrovnávání zatížení a rozšířené zabezpečení vašich globálních aplikací, rozhraní API, webů a cloudových služeb Hyper-Scale s inteligentní ochranou hrozeb.

### <a name="what-features-does-azure-front-door-support"></a>Jaké funkce nabízí podpora front Azure?

Přední vrátka Azure podporuje:

* Statický obsah i dynamická akcelerace aplikace
* Snižování zátěže TLS/SSL a koncového šifrování TLS
* Firewall webových aplikací
* Spřažení relace na základě souborů cookie
* Směrování na základě cesty URL
* Bezplatné certifikáty a Správa více domén

Úplný seznam podporovaných funkcí najdete v tématu [Přehled front-dveří Azure](overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Jaký je rozdíl mezi předními dveřmi Azure a Application Gateway Azure?

I když jsou přední dveře i Application Gateway nástroje pro vyrovnávání zatížení vrstvy 7 (HTTP/HTTPS), hlavním rozdílem je to, že přední dveře představují globální službu. Application Gateway je místní služba. Zatímco přední dveře můžou vyrovnávat zatížení mezi různými jednotkami škálování/clustery/jednotkami razítek v různých oblastech, Application Gateway vám umožní vyrovnávat zatížení mezi virtuálními počítači nebo kontejnery, které jsou v jednotce škálování.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Kdy byste měli nasadit Application Gateway za předními dveřmi?

Klíčové scénáře, proč použít Application Gateway za předními dveřmi:

* Přední dveře můžou provádět vyrovnávání zatížení na základě cesty jenom na globální úrovni, ale pokud jeden chce vyrovnávat zatížení provozu ještě dál v rámci své virtuální sítě (VNET), měl by použít Application Gateway.
* Vzhledem k tomu, že přední dvířka nefungují na úrovni virtuálního počítače nebo kontejneru, nemůže připojení vyprázdnit. Application Gateway však umožňuje vyprazdňování připojení. 
* U Application Gateway za předními dveřmi může jedna z nich dosáhnout 100% snižování zátěže TLS/SSL a směrovat pouze požadavky HTTP v rámci své virtuální sítě (VNET).
* Přední dvířka a Application Gateway obojí podporují spřažení relace. Přední dveře můžou směrovat provoz z uživatelské relace do stejného clusteru nebo do back-endu v dané oblasti. Application Gateway můžou spřažení provoz na stejný server v clusteru.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Můžeme nasazovat Azure Load Balancer za předními dveřmi?

Přední dveře Azure vyžadují veřejnou virtuální IP adresu nebo veřejně dostupný název DNS pro směrování provozu do. Nasazení Azure Load Balancer za předními dveřmi je běžný případ použití.

### <a name="what-protocols-does-azure-front-door-support"></a>Jaké protokoly podporuje přední dveře Azure?

Přední dvířka Azure podporují HTTP, HTTPS a HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Jak Azure front-dveře podporuje HTTP/2?

Podpora protokolu HTTP/2 je dostupná jenom pro klienty, kteří se připojují k Azure na přední dveře. Komunikace s back-endy ve fondu back-end je přes protokol HTTP/1.1. Podpora HTTP/2 je ve výchozím nastavení povolená.

### <a name="what-resources-are-supported-today-as-part-of-origin-group"></a>Jaké prostředky se v současnosti podporují jako součást skupiny původ?

Skupina původu se může skládat z úložiště, webové aplikace, instancí Kubernetes nebo jakéhokoli jiného vlastního názvu hostitele, který má veřejné připojení. Přední vrátka Azure vyžaduje, aby byly zdroje definovány buď prostřednictvím veřejné IP adresy, nebo veřejně přeložitelných názvů hostitelů DNS. Členové skupiny původu můžou být v různých zónách, oblastech nebo dokonce mimo Azure, pokud mají veřejné připojení.

### <a name="what-regions-is-the-service-available-in"></a>Ve kterých oblastech je služba dostupná?

Přední vrátka Azure je globální služba a není vázaná na žádnou konkrétní oblast Azure. Jediné umístění, které je třeba zadat při vytváření front-dveří, je pro skupinu prostředků. Toto umístění v podstatě určuje, kde se budou ukládat metadata pro skupinu prostředků. Samotný prostředek přední dveře je vytvořen jako globální prostředek a konfigurace je globálně nasazena na všechny body POP (bod přítomnosti). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Jaká jsou umístění POP pro přední dveře Azure?

Přední dveře Azure mají stejný seznam umístění POP (v bodech) jako Azure CDN od Microsoftu. Úplný seznam našich bodů POP najdete v tématu [Azure CDN umístění pop od Microsoftu](../../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Je přední z bran Azure vyhrazené nasazení pro moji aplikaci nebo je sdíleno mezi zákazníky?

Přední dvířka Azure je globálně distribuovaná služba pro více tenantů. Infrastruktura pro přední dveře je sdílená mezi všemi svými zákazníky. Vytvořením profilu front-dveří definujete konkrétní konfiguraci potřebnou pro vaši aplikaci. Změny provedené na vašich frontách nebudou mít vliv na jiné konfigurace front-dveří.

### <a name="is-http-https-redirection-supported"></a>Je podporováno přesměrování HTTP->HTTPS?

Ano. Ve skutečnosti podporuje přední dveře Azure hostitele, cestu, přesměrování řetězce dotazu a část přesměrování adresy URL. Přečtěte si další informace o [přesměrování adresy URL](concept-rule-set-url-redirect-and-rewrite.md). 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Návody uzamknout přístup k back-endu jenom na přední vrátka Azure?

Pokud chcete aplikaci uzamknout, aby přijímala jenom přenosy z vašich konkrétních front, musíte pro svůj back-end nastavit seznamy ACL pro IP adresu. Pak můžete omezit provoz back-endu na konkrétní hodnotu hlavičky X-Azure-FDID, kterou odesílají přední dveře. Tyto kroky jsou podrobně popsané níže:

* Nakonfigurujte IP funkce acling pro vaše back-endy pro příjem provozu z adresního prostoru IP adres back-endu na front-endu a Azure služby infrastruktury. Další informace o funkce acling back-endu najdete v podrobnostech o IP adrese:
 
    * Přečtěte si část *AzureFrontDoor. back-end* v tématu [rozsahy IP adres Azure a značky služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro rozsah IP adres back-endu IPv4 pro front-endu. Ve [skupinách zabezpečení sítě](../../virtual-network/network-security-groups-overview.md#security-rules)můžete také použít značku služby *AzureFrontDoor. back-end* .
    * [Základní služby infrastruktury](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) Azure prostřednictvím virtualizované IP adresy hostitele: `168.63.129.16` a `169.254.169.254` .

    > [!WARNING]
    > Back-endové IP místo pro front-endu se může později změnit, ale zajistíme, že budeme integrovat s [rozsahy IP adres Azure a značkami služeb](https://www.microsoft.com/download/details.aspx?id=56519). Doporučujeme, abyste se přihlásili k odběru [rozsahů IP adres Azure a značek služeb](https://www.microsoft.com/download/details.aspx?id=56519) pro jakékoli změny nebo aktualizace.

* Proveďte operaci GET na front-dveřích s verzí rozhraní API `2020-01-01` nebo vyšší. V volání rozhraní API vyhledejte `frontdoorID` pole. Vyfiltrujte příchozí hlavičku **X-Azure-FDID**, kterou odeslala přední dvířka do back-endu s hodnotou pole `frontdoorID` . Hodnotu můžete najít také `Front Door ID` v části Přehled na stránce portálu front dveří. 

* Na webovém serveru back-end použijte filtrování pravidel pro omezení provozu na základě výsledné hodnoty hlavičky X-Azure-FDID.

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

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Jak dlouho trvá nasazení front-dveří Azure? Pořád při aktualizaci funguje moje dveře?

Pro globální nasazení trvá nové vytvoření nebo jakékoli aktualizace služby na začátku každé z nich o 3 až 5 minut. To znamená přibližně 3 až 5 minut. konfigurace front-dveří se pak globálně nasadí napříč všemi našimi body POP.

Poznámka: vlastní aktualizace certifikátu TLS/SSL trvá přibližně 30 minut, než se nasadí globálně.

Jakékoli aktualizace pro trasy nebo fondy back-end jsou bezproblémové a způsobí nulové výpadky (Pokud je nová konfigurace správná). Aktualizace certifikátu nezpůsobí žádný výpadek, pokud nepřecházíte ze služby Azure front-dveří spravované na použití vlastního certifikátu nebo jiným způsobem.


## <a name="configuration"></a>Konfigurace

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Může služba Azure Load Balancing nebo směrování provozu v rámci virtuální sítě?

Přední dvířka Azure (AFD) vyžadují pro směrování provozu veřejnou IP adresu nebo veřejně přeložitelný název DNS. Přední dveře Azure nemůžou směrovat přímo do prostředků ve virtuální síti. K vyřešení tohoto problému můžete použít Application Gateway nebo Azure Load Balancer s veřejnou IP adresou.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Jaké jsou různé časové limity a omezení pro přední dveře Azure?

Přečtěte si o všech dokumentovaných [časových limitech a omezeních pro přední dveře Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Jak dlouho trvá, než se pravidlo projeví po přidání do modulu pravidel pro přední dveře?

Dokončení aktualizace v konfiguraci modulu pravidel trvá přibližně 10 až 15 minut. Můžete očekávat, že se pravidlo projeví až po dokončení aktualizace. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Můžu nakonfigurovat Azure CDN za profilem front-dveří nebo předními dveřmi za můj Azure CDN?

Přední dvířka Azure a Azure CDN není možné konfigurovat společně, protože obě služby používají stejné lokality Azure Edge při reagování na požadavky. 

## <a name="performance"></a>Výkon

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Jak Azure front-dveře podporuje vysokou dostupnost a škálovatelnost?

Přední dvířka Azure jsou globálně distribuovaná víceklientská platforma s velkým množstvím kapacit pro potřeby škálovatelnosti vaší aplikace. Přední dveře dodávané z hranice globální sítě Microsoftu poskytují funkce pro vyrovnávání zatížení, které umožňují převzít služby při selhání celé aplikace nebo dokonce i jednotlivé mikroslužby napříč oblastmi nebo různými cloudy.

## <a name="tls-configuration"></a>Konfigurace TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Jaké verze TLS podporuje i přední dvířka Azure?

Všechny profily front-dveří vytvořené po září 2019 jako výchozí minimum používají TLS 1,2.

Přední dvířka podporují protokol TLS verze 1,0, 1,1 a 1,2. TLS 1,3 není zatím podporován.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Jaké certifikáty jsou podporované na frontách Azure na předních dveřích?

Pokud chcete protokol HTTPS povolit na vlastní doméně front-dveří, můžete si vybrat certifikát, který se spravuje přes službu Azure front-dveří, nebo použít vlastní certifikát.
Možnost spravovaná přes dvířka zřídí standardní certifikát TLS/SSL prostřednictvím DigiCert a uložený v Key Vaultu na frontě. Pokud se rozhodnete použít vlastní certifikát, můžete připojit certifikát od podporované certifikační autority a může to být standardní TLS, rozšířený ověřovací certifikát nebo certifikát se zástupnými znaky. Certifikáty podepsané svým držitelem nejsou podporovány.

### <a name="does-front-door-support-autorotation-of-certificates"></a>Podporuje přední dveře automatické otočení certifikátů?

Pro možnost spravovaného certifikátu front-dveří se certifikáty připravují pomocí front-dveří. Pokud používáte certifikát spravovaný přes dvířka a zjistíte, že je datum vypršení platnosti certifikátu kratší než 60 dní, zajistěte si soubor a lístek podpory.

Pro vlastní certifikát TLS/SSL se nepodporuje automatické otočení. Podobně jako v případě, že jste si nastavili poprvé pro danou vlastní doménu, budete muset na svém Key Vault nasměrovat do správné verze certifikátu pracovní dveře. Ujistěte se, že instanční objekt pro front-dveře má stále přístup k Key Vault. Tato aktualizovaná operace zavedení certifikátu na front-dveřích nezpůsobí, že se název subjektu nebo síť SAN pro daný certifikát nemění.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Jaké jsou aktuální šifrovací sady, které jsou podporovány předními dvířky Azure?

Pro protokol TLS 1.2 jsou podporovány následující šifrovací sady: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

U vlastních domén s povoleným protokolem TLS 1.0/1.1 jsou podporovány následující šifrovací sady:

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

Minimální verzi protokolu TLS v frontách Azure můžete nakonfigurovat v nastaveních HTTPS vlastní domény pomocí Azure Portal nebo [REST API Azure](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). V současné době si můžete vybrat mezi 1,0 a 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Můžu nakonfigurovat přední dveře tak, aby podporovaly pouze konkrétní šifrovací sady?

Ne, konfigurace front-dveří pro konkrétní šifrovací sady není podporována. Vlastní certifikát TLS/SSL můžete získat od certifikační autority (například VeriSign, svěření nebo DigiCert). Po vygenerování se pak budou v certifikátu označovat konkrétní šifrovací sady. 

### <a name="does-front-door-support-ocsp-stapling"></a>Podporuje se pro připojení k protokolu OCSP i přední dvířka?

Ano, ve výchozím nastavení je připojení protokolu OCSP podporované předními dveřmi a nevyžaduje se žádná konfigurace.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Podporuje i přední dveře Azure opětovné šifrování provozu do back-endu?

Ano, přední dvířka Azure podporují snižování zátěže TLS/SSL a koncového šifrování TLS, které znovu zašifruje provoz do back-endu. Vzhledem k tomu, že připojení k back-endu dojde přes veřejnou IP adresu, doporučujeme nakonfigurovat přední dvířka tak, aby jako předávací protokol používala HTTPS.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Podporují přední dveře certifikáty podepsané svým držitelem v back-endu pro připojení HTTPS?

Ne, certifikáty podepsané svým držitelem nejsou podporovány na front-dveřích a omezení platí pro:

* **Back-endy**: certifikáty podepsané svým držitelem nemůžete použít při předávání provozu jako sondy stavu HTTPS nebo HTTPS nebo při vyplňování mezipaměti pro zdroj pro pravidla směrování s povoleným ukládáním do mezipaměti.
* **Front-end**: certifikáty podepsané svým držitelem nemůžete použít při použití vlastního certifikátu TLS/SSL pro povolení protokolu HTTPS ve vaší vlastní doméně.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Proč selhává přenos HTTPS na můj back-end?

Pro úspěšné připojení HTTPS k back-endu, ať už pro sondy stavu nebo pro předávání požadavků, můžou existovat dva důvody, proč může selhání provozu HTTPS:

* **Neshoda názvů subjektu certifikátu**: u připojení HTTPS očekává přední dveře, že váš server back-end prezentuje certifikát od platné certifikační autority s názvy subjektů odpovídajícími názvu hostitele back-endu. Příklad: Pokud je váš název hostitele back-end nastavený na `myapp-centralus.contosonews.net` a certifikát, který vaše back-end prezentuje během ověřování TLS, nemá `myapp-centralus.contosonews.net` nebo `*myapp-centralus*.contosonews.net` v názvu subjektu. Pak přední dveře odmítnou připojení a výsledkem bude chyba. 
    * **Řešení**: nedoporučuje se z hlediska dodržování předpisů, ale tuto chybu můžete obejít tím, že zakážete kontrolu názvu subjektu certifikátu pro vaše přední dveře. Tuto možnost najdete v části nastavení v Azure Portal a v části BackendPoolsSettings v rozhraní API.
* **Certifikát hostování back-endu z neplatné certifikační autority**: pro back-end s předními dveřmi lze použít pouze certifikáty z [platných certifikačních autorit](troubleshoot-allowed-certificate-authority.md) . Certifikáty z interních certifikačních autorit nebo certifikátů podepsaných svým držitelem nejsou povolené.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Můžu použít klienta a vzájemné ověřování s využitím front-dveří Azure?

No. I když přední dvířka Azure podporují protokol TLS 1,2, který zavedl klient a vzájemné ověřování v [RFC 5246](https://tools.ietf.org/html/rfc5246), v současné době se u front-in Azure nepodporuje klient a vzájemné ověřování.

## <a name="diagnostics-and-logging"></a>Diagnostika a protokolování

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Jaké typy metrik a protokolů jsou k dispozici v rámci služby Azure front-dveří?

Informace o protokolech a dalších diagnostických možnostech najdete v tématu monitorování metrik a protokolů pro přední dveře.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Jaké jsou zásady uchovávání informací v diagnostických protokolech?

Diagnostické protokoly se zaznamenávají do účtu úložiště zákazníků a zákazníci můžou nastavit zásady uchovávání informací na základě jejich preference. Diagnostické protokoly je také možné odeslat do centra událostí nebo do protokolů Azure Monitor. Další informace najdete v tématu [protokolování front-dveří Azure](how-to-logs.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Návody získat protokoly auditu pro přední dveře Azure?

Protokoly auditu jsou k dispozici pro přední dveře Azure. Na portálu vyberte **Protokol aktivit** na stránce nabídky vaší přední dveře, abyste měli přístup k protokolu auditu. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Můžu nastavovat upozornění pomocí front-dveří Azure?

Ano, přední dveře Azure podporují výstrahy. Výstrahy jsou nakonfigurovány na metrikách. 

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md).
