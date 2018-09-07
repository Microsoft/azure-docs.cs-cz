---
title: Nejčastější dotazy k Azure Application Gateway
description: Tato stránka nabízí odpovědi na nejčastější dotazy týkající se Azure Application Gateway
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 9/6/2018
ms.author: victorh
ms.openlocfilehash: e180f0912bdb9145f3d36492fb9bcdcb551037f0
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022912"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Nejčastější dotazy k Application Gateway

## <a name="general"></a>Obecné

**Q. Co je Application Gateway?**

Azure Application Gateway je Application Delivery Controller (ADC) jako službu, nabízí různé možnosti pro vaše aplikace Vyrovnávání zatížení vrstvy 7. Nabízí vysoce dostupné a škálovatelné služby, která je plně spravuje Azure.

**Q. Jaké funkce Application Gateway podporuje?**

Služba Application Gateway podporuje snižování zátěže protokolu SSL a kompletního protokolu SSL, Firewall webových aplikací, spřažení relace na základě souborů cookie, adresu url na základě cest směrování, hostování několika webů a dalších. Úplný seznam podporovaných funkcí najdete [seznámení se službou Application Gateway](application-gateway-introduction.md)

**Q. Jaký je rozdíl mezi Application Gateway a nástroje pro vyrovnávání zatížení Azure?**

Služba Application Gateway je Vyrovnávání zatížení vrstvy 7, což znamená, že pracuje s pouze webový provoz (HTTP/HTTPS/WebSocket). Podporuje možnosti, jako jsou ukončení protokolu SSL, spřažení relace na základě souborů cookie a kruhové dotazování pro provoz služby Vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení, zatížení provozu na vrstvy 4 (TCP/UDP).

**Q. Jaké protokolů Application Gateway podporuje?**

Služba Application Gateway podporuje HTTP, HTTPS, HTTP/2 a protokolu WebSocket.

**Q. Jak Application Gateway podporuje HTTP/2?**

Podpora protokolu HTTP/2 je k dispozici klienti připojení k službě Application Gateway jenom naslouchacích procesů. Komunikace do fondů back-end serveru je přes HTTP/1.1. 

Ve výchozím nastavení je zakázána podpora HTTP/2. Následující příklad fragmentu kódu prostředí Azure PowerShell ukazuje, jak můžete zajistit:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**Q. Které prostředky jsou dnes podporované jako součást back-endový fond?**

Back-endové fondy se může skládat ze síťových rozhraní škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy nebo plně kvalifikované názvy (plně kvalifikovaný název domény) a více tenantů back EndY, jako je Azure Web Apps. Členy fondu back-end brány aplikace nejsou vázané na skupinu dostupnosti. Členů fondu back-end může být napříč clustery, datová centra, Azure nebo mimo něj, tak dlouho, dokud mají připojení pomocí IP adresy.

**Q. Jaké oblasti je k dispozici ve službě?**

Služba Application Gateway je k dispozici ve všech oblastech Azure globální. Je také k dispozici v [Azure China](https://www.azure.cn/) a [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

**Q. Je to vyhrazená nasazení pro mé předplatné nebo je sdílen mezi zákazníky?**

Služba Application Gateway je vyhrazené nasazení ve virtuální síti.

**Q. Je HTTP -> přesměrování protokolu HTTPS, které jsou podporovány?**

Přesměrování se nepodporuje. Navštivte [přehled přesměrování ve službě Application Gateway](application-gateway-redirect-overview.md) Další informace.

**Q. V jakém pořadí se naslouchací procesy zpracovávají?**

Naslouchací procesy jsou zpracovávány v pořadí, ve kterém jsou zobrazeny. Z tohoto důvodu Pokud základní naslouchací proces odpovídá příchozí žádosti zpracovávat jej nejprve.  Naslouchací procesy Multi-Site by měl být nakonfigurovaný před základní naslouchací proces chcete zajistit, aby provoz se směruje do správného back endu.

**Q. Kde najdu IP a DNS Application Gateway?**

Při použití veřejné IP adresy jako koncový bod, tyto informace najdete na prostředek veřejné IP adresy nebo na stránce Přehled služby Application Gateway na portálu. Pro interní IP adresy to najdete na stránce Přehled.

**Q. IP adresy nebo DNS mění během životního cyklu Application Gateway?**

Virtuální IP adresu můžete změnit, pokud brána je zastaven a tím, že zákazník. DNS přidružené k Application Gateway nemění přes životní cyklus brány. Z tohoto důvodu se doporučuje použít CNAME alias a přejděte na adresu DNS služby Application Gateway.

**Q. Služba Application Gateway podporuje statickou IP adresu?**

Ne, služba Application Gateway nepodporuje statické veřejné IP adresy, ale nepodporuje statické interní IP adresy.

**Q. Služba Application Gateway podporuje několik veřejných IP adres v bráně?**

Ve službě Application Gateway se podporuje jenom jednu veřejnou IP adresu.

**Q. Jak velké by měl udělám mou podsíť pro službu Application Gateway?**

Application Gateway využívá jednu privátní IP adresu na jednu instanci a jiné privátní IP adresu, pokud je nakonfigurovaný privátní front-endovou konfiguraci IP. Azure si vyhrazuje první čtyři a poslední IP adresu v každé podsíti pro interní použití.
Například, pokud služba Application Gateway je nastavena na tři instance a žádné privátní front-endovou IP, pak je/29 nebo větší velikost podsítě je potřeba. V tomto případě Application Gateway používá tři IP adresy. Pokud máte tři instance a IP adresu pro privátní front-endovou konfiguraci protokolu IP, pak o velikosti/28 podsíť, velikost nebo vyšší je potřeba, protože čtyři IP adresy jsou povinné.

**Q. Služba Application Gateway podporuje hlavičky x předané pro?**

Ano, služba Application Gateway vloží záhlaví x předané u, x-forwarded-proto a x předané port do žádosti předané back-endu. Formát pro x předané – pro hlavičky je čárkou oddělený seznam IP: port. Platné hodnoty pro x-forwarded-proto jsou http nebo https. X předané port Určuje port, ve kterém bylo dosaženo žádost ve službě Application Gateway.

Služba Application Gateway také vloží hlavičku X-původní-hostitele, která obsahuje hlavičku původního hostitele, pomocí kterého Přišla žádost. Tato hlavička se užitečné v situacích, jako jsou integrace webu Azure, ve kterém se upraví hlavičku hostitele příchozího před provoz směruje do back-endu.

**Q. Jak dlouho trvá nasazení služby Application Gateway? My Application Gateway stále funguje při aktualizaci?**

Nová nasazení Application Gateway může trvat až 20 minut, než se zřídit. Změny velikosti a počtu instancí nejsou rušivé a během této doby zůstává aktivní brány.

## <a name="configuration"></a>Konfigurace

**Q. Služba Application Gateway vždy nasazení ve virtuální síti?**

Ano, služba Application Gateway je vždy nasazeny v podsíti virtuální sítě. Tato podsíť může obsahovat jenom aplikační brány.

**Q. Může služba Application Gateway sdělit instance mimo příslušné virtuální síti?**

Služba Application Gateway může komunikovat s instancí mimo virtuální síť, která bude v, jako je připojení pomocí IP adresy. Pokud plánujete používat interní IP adresy jako členy fondu back-end, pak vyžaduje [VNET Peering](../virtual-network/virtual-network-peering-overview.md) nebo [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**Q. Je možné nasadit nic jiného v podsítě Application Gateway?**

Ne, ale můžete nasadit další služby application Gateway v podsíti.

**Q. Podporují se skupiny zabezpečení sítě na podsíť Application Gateway?**

Skupiny zabezpečení sítě jsou podporovány na podsítě Application Gateway s následujícími omezeními:

* Výjimky musí být umístěno v pro příchozí provoz na portech 65503 65534. Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity, včetně zákazníků těchto bran, nebude možné zahájit žádné změny na tyto koncové body.

* Odchozí připojení k Internetu, nejde blokovat.

* Musí se povolit provoz z značka AzureLoadBalancer.

**Q. Podporuje uživatelem definované trasy na podsítě služby application gateway?**

Trasy definované uživatelem (udr) jsou podporované na podsítě služby application gateway, za předpokladu, nijak nemění komunikace začátku do konce žádostí a odpovědí.

Například trasu UDR v podsítě služby application gateway můžete nastavit tak, aby odkazoval na zařízení brány firewall pro kontrolu paketů, ale musíte zajistit, aby paketu přístup jeho příspěvku kontrole požadovaného cíle. K tomu může dojít nesprávný stav testu nebo provoz směrování chování. To zahrnuje zjištěné trasy nebo výchozí trasy 0.0.0.0/0 šířeny přes ExpressRoute nebo bran VPN ve virtuální síti.

**Q. Jaká jsou omezení ve službě Application Gateway? Můžete tyto limity zvýšit?**

Navštivte [omezení služby Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits) zobrazíte omezení.

**Q. Můžu používat Application Gateway pro externí a interní provozu současně?**

Ano, služba Application Gateway podporuje jeden interní IP adresa a jedna externí IP adresy na aplikační bránu.

**Q. VNet peering je podporováno?**

Ano, VNet peering je podporován a je vhodné pro provoz v jiných virtuálních sítí pro vyrovnávání zatížení.

**Q. Mi může sdělit na místních serverech při připojení pomocí ExpressRoute nebo VPN tunely?**

Ano, tak dlouho, dokud je povolený provoz.

**Q. Může mít jeden back-endový fond obsluhují mnoho aplikací na jiném portu?**

Architektura Micro služby se podporuje. Potřebujete různá nastavení protokol http nakonfigurovaná testovat na jiném portu.

**Q. Vlastní sondy podporují zástupných znaků nebo regulární výraz v odpovědi data?**

Vlastní sondy nepodporují zástupných znaků nebo regulární výraz na data odpovědi. 

**Q. Jak se zpracovávají pravidla?**

Pravidla se zpracovávají v pořadí, ve kterém jsou nakonfigurované. Doporučuje se, že jsou před základních pravidel, snížíte pravděpodobnost, že provoz se směruje na nevhodný back-end jako základní pravidlo by odpovídala provoz na portu před Multi-Site pravidlo se vyhodnotí na základě nakonfigurovaná pravidla více lokalit.

**Q. Co místo pole hostitele pro vlastní sondy?**

Pole hostitel Určuje název, který chcete odeslat testu a. Použít pouze v případě více webů je nakonfigurovaná ve službě Application Gateway, v opačném případě použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního počítače a je ve formátu \<protokol\>://\<hostitele\>:\<port\>\<cesta\>.

**Q. Je možné seznamu povolených IP adres službě Application Gateway přístup k několika zdrojové IP adresy?**

Tento scénář lze provést pomocí skupin Nsg na podsítě Application Gateway. Následující omezení by mělo být uvedeno na podsítě v uvedené pořadí podle priority:

* Umožňuje příchozí provoz ze zdrojového rozsahu IP/IP.

* Povolit příchozí žádosti ze všech zdrojů k portům 65503 65534 pro [komunikace stavu back-endu](application-gateway-diagnostics.md). Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity, včetně zákazníků těchto bran, nebude možné zahájit žádné změny na tyto koncové body.

* Povolit příchozí sondy pro Azure Load Balancer (značka AzureLoadBalancer) a příchozí přenosy virtuální sítě (značka VirtualNetwork) na [NSG](../virtual-network/security-overview.md).

* Blokovat všechna ostatní příchozí provoz s odepřít všechna pravidla.

* Povolí odchozí provoz do Internetu pro všechny cíle.

**Q. Lze použít stejný port pro veřejné a soukromé protilehlé naslouchacích procesů?**

Ne, na tuto situaci se podpora nevztahuje.

## <a name="performance"></a>Výkon

**Q. Jak Application Gateway podporuje vysokou dostupnost a škálovatelnost?**

Služba Application Gateway podporuje scénáře vysoké dostupnosti, pokud mají nasazené nejmíň dvě instance. Azure rozděluje tyto instance mezi aktualizace a domény selhání k zajištění, že všechny instance neselže ve stejnou dobu. Služba Application Gateway podporuje škálovatelnost přidáním více instancí stejné bráně sdílení zatížení.

**Q. Jak dosáhnu scénář zotavení po Havárii napříč datovými centry s bránou Application Gateway?**

Zákazníci mohou pomocí Traffic Manageru k distribuování síťového provozu mezi více aplikačních bran v různých datových centrech.

**Q. Automatické škálování je podporováno?**

Ne, ale služba Application Gateway má metriky propustnosti, který slouží k upozornění, když je dosaženo prahové hodnoty. Ruční přidání instance nebo změna velikosti nerestartuje brány a nemá vliv na existující provoz.

**Q. Je ruční škálování nahoru/dolů příčina výpadku?**

Neexistuje žádný výpadek, instance se distribuují napříč upgradovací doménami a doménami selhání.

**Q. Podporuje vyprázdnění application gateway podporu připojení?**

Ano. Můžete nakonfigurovat vyprázdnění Chcete-li změnit členy v rámci fondu back-end bez přerušení připojení. To vám umožní existující připojení a pokračujte v odešlou do jejich předchozího cíle, dokud nebude tento připojení se ukončilo nebo konfigurovatelný časový limit vyprší. Poznámka: Toto připojení vyprazdňování pouze čeká na dokončení aktuálních vydávaných za pochodu připojení. Služba Application Gateway nemá žádné informace o stavu relace aplikace.

**Q. Co jsou aplikace velikosti brány?**

Služba Application Gateway je v současné době nabízena ve třech velikostech: **Small** (krátkodobé používání), **Medium** (střednědobé používání) a **Large** (dlouhodobé používání). Instance krátkodobého používání jsou určené pro scénáře vývoje a testování.

V rámci jednoho předplatného můžete vytvořit až 50 služeb Application Gateway a každá z nich může mít až 10 instancí. Každá služba Application Gateway se může skládat z 20 naslouchacích procesů HTTP. Úplný seznam omezení služby Application Gateway najdete na stránce [Omezení služby Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Následující tabulka ukazuje průměrnou propustnost výkonu pro jednotlivé instance služby Application Gateway s povoleným přesměrováním zpracování SSL:

| Průměrná velikost odpovědi back endové stránky | Krátkodobé používání | Střednědobé používání | Dlouhodobé používání |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Tyto hodnoty jsou přibližné hodnoty propustnosti služby Application Gateway. Skutečná propustnost závisí na různých podrobnostech o prostředí, jako jsou například průměrná velikost stránky, umístění back-endových instancí a doba zpracování potřebná k doručení stránky. Přesné údaje o výkonu získáte, když spustíte vlastní testy. Tyto hodnoty slouží jenom jako vodítko při plánování kapacity.

**Q. Můžu změnit velikost instance ze střední a velké bez přerušení?**

Ano, Azure distribuuje instancí napříč aktualizace a domény selhání k zajištění, že všechny instance neselže ve stejnou dobu. Služba Application Gateway podporuje škálování tak, že přidáte více instancí stejné bráně sdílení zatížení.

## <a name="ssl-configuration"></a>Konfigurace SSL

**Q. Jaké certifikáty jsou podporovány ve službě Application Gateway?**

Vlastní podepsané certifikáty CA certs, a zástupný znak – certifikáty jsou podporovány. Rozšířené certifikátů nejsou podporovány.

**Q. Jaké jsou aktuální šifrovací sada podporovaná službou Application Gateway?**

Toto jsou aktuální šifrovací sada podporovaná službou application gateway. Navštivte: [konfigurace protokolu SSL verze zásad a šifrovací sady ve službě Application Gateway](application-gateway-configure-ssl-policy-powershell.md) se naučíte přizpůsobit možnosti SSL.

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

**Q. Služba Application Gateway také podporuje znova šifrovat provoz do back-end?**

Ano, služba Application Gateway podporuje přesměrování zpracování SSL a kompletního protokolu SSL, který znovu zašifruje provoz do back-endu.

**Q. Můžete nakonfigurovat zásady protokolu SSL pro řízení verze protokolu SSL?**

Ano, můžete nakonfigurovat Application Gateway pro protokol TLS 1.0, TLS1.1 a TLS1.2 odepření. Protokol SSL 2.0 a 3.0 jsou již ve výchozím nastavení zakázána a se nedají konfigurovat.

**Q. Můžete nakonfigurovat šifrovací sady a pořadí zásad?**

Ano, [konfiguraci šifrovací sady](application-gateway-ssl-policy-overview.md) je podporována. Při definování vlastní zásady, musí povolená aspoň jedna z následujících sad šifer. Služba Application gateway používá SHA256 pro správu back-endu.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**Q. Jak velký počet certifikátů SSL jsou podporovány?**

Certifikáty jsou podporovány až 20 SSL.

**Q. Kolik ověřovacích certifikátů pro back-endu znovu šifrovat jsou podporovány?**

Až 10 ověřování certifikáty jsou podporovány výchozí hodnota je 5.

**Q. Služba Application Gateway integrovat s Azure Key Vault nativně?**

Ne, není integrovaná se službou Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfigurace brány Firewall (WAF) webové aplikace

**Q. Nabízí WAF SKU všechny funkce dostupné v standardní SKU?**

Ano, WAF podporuje všechny funkce ve standardním SKU.

**Q. Jaká je verze CRS Application Gateway podporuje?**

Služba Application Gateway podporuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**Q. Jak můžu monitorovat WAF?**

WAF se monitoruje prostřednictvím protokolování diagnostiky, další informace o protokolování diagnostiky najdete v [protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md)

**Q. Režim detekce blokovat provoz?**

Režim detekce Ne, zaznamená jenom provoz, která aktivuje pravidlo WAF.

**Q. Jak mohu přizpůsobit pravidla firewallu webových aplikací?**

Ano, jsou přizpůsobitelné, další informace o tom, jak si je přizpůsobit, navštivte pravidla firewallu webových aplikací [WAF přizpůsobení skupin pravidel a pravidla](application-gateway-customize-waf-rules-portal.md)

**Q. Pravidla jsou aktuálně k dispozici?**

WAF v současné době podporuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), které poskytují směrného plánu zabezpečení před většinou z 10 nejčastějších ohrožení zabezpečení, identifikovat podle otevřené webové aplikace zabezpečení projektu (OWASP) najdete tady [ OWASP top 10 ohrožení zabezpečení](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Ochrana před útoky prostřednictvím injektáže SQL.

* Ochrana před skriptováním mezi weby.

* Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

* Ochrana před narušením protokolu HTTP.

* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

* Ochrana před roboty, prohledávacími moduly a skenery.

 * Detekce běžných chyb v konfiguraci aplikací (tj. Apache, IIS atd.)

**Q. WAF také podporuje před útoky DDoS ochrany před únikem informací?**

Ano. Můžete povolit ochranu před útoky DDos na virtuální síť, ve kterém je nasazená Aplikační brána. To zajistí, že služba application gateway virtuálních IP adres je taky chránit pomocí služby Azure DDos Protection.

## <a name="diagnostics-and-logging"></a>Protokolování a Diagnostika

**Q. Jaké typy protokolů jsou dostupné pomocí služby Application Gateway?**

K dispozici tři protokoly pro službu Application Gateway. Další informace na tyto protokoly a další diagnostické možnosti [back-endu stav, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** – protokol přístupu obsahuje každou odeslal se požadavek na front-endová služba Application Gateway. Data obsahují volajícího IP adresy URL vyžádané, latence odpovědi, návratový kód, bajtů a oddálení. Přístup k protokolu shromažďovaných každých 300 sekund. Tento protokol obsahuje jeden záznam za instance služby Application Gateway.
- **ApplicationGatewayPerformanceLog** -protokolu výkonu zaznamenává informace o výkonu na na základě instancí včetně celkový požadavek obsluhuje, propustnost v bajtech, celkový počet požadavků obsluhovat, počet neúspěšných požadavků funkčních a nefunkčních back-end počet instancí.
- **ApplicationGatewayFirewallLog** – brány firewall protokolu obsahuje požadavky, které jsou zaznamenány pomocí detekce a ochrany před únikem informací režim služby application gateway, která je nakonfigurovaná s firewallem webových aplikací.

**Q. Jak poznám, že pokud jsou v pořádku Moje členů fondu back-end?**

Můžete použít rutinu Powershellu `Get-AzureRmApplicationGatewayBackendHealth` nebo ověřit stav prostřednictvím portálu návštěvou [diagnostiku brány aplikací](application-gateway-diagnostics.md)

**Q. Co jsou zásady uchovávání informací na protokoly diagnostiky?**

Diagnostické protokoly toku do účtu úložiště zákazníkům a zákazníci můžou nastavit zásady uchovávání informací podle jejich priority. Diagnostické protokoly můžete odeslat také do Log Analytics nebo centra událostí. Navštivte [diagnostiku brány aplikace](application-gateway-diagnostics.md) další podrobnosti.

**Q. Jak získám protokoly auditu pro službu Application Gateway?**

Protokoly auditu jsou k dispozici pro službu Application Gateway. Na portálu klikněte na tlačítko **protokolu aktivit** v okně nabídky služby Application Gateway přístup k protokolu auditu. 

**Q. Můžete nastavit výstrahy se službou Application Gateway?**

Ano, služba Application Gateway podporuje výstrahy, se konfigurují mimo metriky. Application Gateway aktuálně poskytuje metriky "propustnosti", kterého lze nakonfigurovat na upozornění. Další informace o výstrahách, najdete v tématu [doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**Q. Stav back-endu vrátí stav neznámý, co by mohlo být příčinou tohoto stavu?**

Nejběžnějším důvodem je, blokuje přístup k back-endové skupiny zabezpečení sítě nebo vlastní DNS. Navštivte [back-endu stav, protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md) Další informace.

## <a name="next-steps"></a>Další kroky

Další informace o službě Application Gateway najdete [co je Azure Application Gateway?](overview.md)
