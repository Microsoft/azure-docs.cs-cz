---
title: Nejčastější dotazy pro Azure Application Gateway
description: Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se Azure Application Gateway
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/19/2018
ms.author: victorh
ms.openlocfilehash: a2f664525235fa97b0694362d7dc82086e31f71d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229331"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Nejčastější dotazy pro službu Application Gateway

## <a name="general"></a>Obecné

**Q. Co je aplikační brána?**

Služba Azure Application Gateway je řadiče doručení aplikace (ADC) jako služba nabízí různé vrstvy 7 možnosti vyrovnávání zatížení pro vaše aplikace. Nabízí vysoce dostupné a škálovatelné služby, která je plně spravovaná službou Azure.

**Q. Jaké funkce podporuje Application Gateway?**

Aplikační brána podporuje snižování zátěže protokolu SSL a koncová SSL, brány Firewall webových aplikací, spřažení na základě souboru cookie relace, adresa url na základě cesty směrování, hostování více lokality a ostatní. Úplný seznam podporovaných funkcích najdete na adrese [Úvod aplikační brány](application-gateway-introduction.md)

**Q. Jaký je rozdíl mezi aplikační bránu a nástroj pro vyrovnávání zatížení Azure?**

Application Gateway je Vyrovnávání zatížení vrstvy 7, což znamená, že pracuje pouze webový provoz (HTTP nebo HTTPS/WebSocket). Podporuje možnosti, například ukončení protokolu SSL, spřažení relace na základě souborů cookie a kruhové dotazování pro provoz služby Vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení a načtěte provozu zůstatky na vrstvě 4 (TCP/UDP).

**Q. Jaké protokoly podporuje Application Gateway?**

Aplikační brána podporuje protokoly HTTP, HTTPS, HTTP nebo 2 a protokolu WebSocket.

**Q. Jak Aplikační brána podporuje protokol HTTP/2?**

Podpora protokolu HTTP/2 je k dispozici pro připojení klientů k jenom naslouchací procesy pro aplikační bránu. Komunikace na back-end serveru fondy je přes protokol HTTP/1.1. 

Podpora protokolu HTTP/2 je ve výchozím nastavení zakázaná. Následující příklad fragmentu kódu prostředí Azure PowerShell ukazuje, jak můžete ji povolit:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**Q. Jaké prostředky jsou dnes podporovány v rámci fondu back-end?**

Back-endové fondy může skládat ze síťových adaptérů, sady škálování virtuálního počítače, veřejné IP adresy, názvy interní IP adres, plně kvalifikované domény (FQDN) a víceklientské back EndY jako Azure Web Apps. Členy fondu back-end brány aplikace nejsou vázaný na sadu dostupnosti. Členy fondu back-end může být napříč clustery, datových center, nebo mimo Azure, dokud mají připojení pomocí protokolu IP.

**Q. Jaké oblasti je služba k dispozici v?**

Application Gateway je k dispozici ve všech oblastech globální Azure. Je také dostupná v [Azure China](https://www.azure.cn/) a [Azure Government.](https://azure.microsoft.com/overview/clouds/government/)

**Q. To je vyhrazený pro Moje předplatné nasazení nebo je sdílen na zákazníky?**

Application Gateway je vyhrazené nasazení ve virtuální síti.

**Q. Je HTTP -> HTTPS přesměrování podporovány?**

Přesměrování je podporována. Navštivte [přehled přesměrování Application Gateway](application-gateway-redirect-overview.md) Další informace.

**Q. Pořadí, v jakém jsou naslouchací procesy zpracování?**

Moduly pro naslouchání jsou zpracovány v pořadí, ve kterém jsou uvedené. Z tohoto důvodu Pokud základní naslouchací proces odpovídá příchozí požadavek zpracuje jej nejprve.  Moduly pro naslouchání více lokalit by měl být nakonfigurovaný před základní naslouchací proces pro zajištění, že provoz se směruje na správný back-end.

**Q. Kde najít IP a DNS Application Gateway?**

Pokud používáte veřejnou IP adresu jako koncový bod, tyto informace naleznete na prostředek veřejné IP adresy nebo na stránce Přehled pro službu Application Gateway na portálu. Pro interní IP adresy najdete na stránce Přehled.

**Q. IP adresa nebo DNS mění za dobu existence služby Application Gateway?**

Virtuální IP adresu můžete změnit, pokud brána po zastavení a spuštění zákazník. DNS přidružené Application Gateway přes životního cyklu brány nezmění. Z tohoto důvodu doporučujeme použít CNAME alias a přejděte na adresu DNS aplikační brány.

**Q. Podporuje Application Gateway statickou IP adresu?**

Ne, aplikační brána nepodporuje statické veřejné IP adresy, ale podporuje statické interní IP adresy.

**Q. Podporuje Application Gateway na bráně víc veřejných IP adres?**

Na aplikační brány je podporovaná pouze jednu veřejnou IP adresu.

**Q. Jak velké by měl vytvořit mou podsíť pro aplikační bránu?**

Aplikační brána spotřebuje jednu privátní IP adresu na jednu instanci, plus další privátní IP adresu, pokud je nakonfigurovaný na privátní front-endovou konfiguraci protokolu IP. Kromě toho Azure si vyhrazuje první čtyři a poslední IP adresa v každé podsíti pro interní použití.
Například, pokud Application Gateway je nastavena na tři instance a žádný privátní front-endovou IP, pak/29 je potřeba podsíť velké nebo větší. V takovém případě Application Gateway používá tři IP adresy. Pokud máte tři instance a IP adresu pro privátní front-endovou konfiguraci protokolu IP, pak o velikosti/28 podsíť velké nebo větší je potřeba, protože jsou požadovány čtyři IP adresy.

**Q. Podporuje Application Gateway hlavičky x předávaných pro?**

Ano, Application Gateway vloží záhlaví x předávaných pro, x předávaných proto a x předávaných portu do žádosti předávaných do back-end. Formát hlavičky x předávaných pro je čárkami oddělený seznam IP: port. Platné hodnoty pro x předávaných proto jsou protokolu http nebo https. X předávaných port Určuje port, kdy požadavek dosáhla aplikační brány.

**Q. Jak dlouho trvá nasazení služby Application Gateway? Moje aplikace brány stále funguje při aktualizaci?**

Nová nasazení aplikací brány může trvat až 20 minut zřídit. Změny velikosti nebo počet instancí nejsou rušivý a během této doby je nadále aktivní brány.

## <a name="configuration"></a>Konfigurace

**Q. Je aplikační brána vždy nasazený ve virtuální síti?**

Ano, aplikační brány je vždy nasazena v podsíti virtuální sítě. Tato podsíť může obsahovat pouze Application Gateway.

**Q. Můžete aplikační brány komunikují s instancí mimo jeho virtuální síť?**

Aplikační brána může komunikovat instancí mimo virtuální síť, která je v, dokud není IP připojení. Pokud máte v úmyslu používat interní IP adresy jako členy fondu back-end, pak vyžaduje [VNET Peering](../virtual-network/virtual-network-peering-overview.md) nebo [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**Q. Lze nasadit nic jiného v podsíť brány aplikace?**

Ne, ale můžete nasadit další application Gateway v podsíti.

**Q. Skupiny zabezpečení sítě podporuje na podsíť brány aplikace?**

Skupiny zabezpečení sítě jsou podporovány v podsíti Application Gateway s následujícími omezeními:

* Výjimky musíte umístit pro příchozí komunikaci na portech 65503 65534 pro back-end stavu fungovala správně.

* Odchozí připojení k Internetu, nejde blokovat.

* Provoz z značka AzureLoadBalancer musí být povoleno.

**Q. Jaké jsou omezení pro aplikační bránu? Může tyto limity zvýšit?**

Navštivte [omezení brány aplikací](../azure-subscription-service-limits.md#application-gateway-limits) zobrazíte omezení.

**Q. Lze použít aplikační brány pro externí i interní provoz současně?**

Ano, podporuje aplikační brány s jeden interní IP adresy a jeden externí IP adresu na aplikační brány.

**Q. VNet peering je podporovaný?**

Ano, partnerský vztah virtuální sítě je podporovaný a je výhodné pro provoz v jiných virtuálních sítí Vyrovnávání zatížení.

**Q. Mi může sdělit místních serverů, když jsou připojeni pomocí ExpressRoute nebo VPN tunely?**

Ano, tak dlouho, dokud provoz je povolený.

**Q. Může mít jeden fond back-end obsluhující mnoho aplikací na jiné porty?**

Architektura malých služby je podporována. Potřebovali byste více nastavení http, které jsou nakonfigurované tak, aby sběru dat na jiné porty.

**Q. Podporují vlastní testy paměti na data odpovědi zástupné znaky/regex?**

Vlastní testy paměti nepodporují zástupných znaků nebo regex na data odpovědi. 

**Q. Jak se zpracovávají pravidla?**

Pravidla se zpracovávají v pořadí, ve kterém jsou nakonfigurované. Doporučuje se, zda pravidla více lokalit jsou nakonfigurovány před basic pravidla může snížit pravděpodobnost, že provoz se směruje na nevhodných back-end jako základní pravidlo odpovídá provozu na port před vyhodnocovaný pravidlo více lokalit.

**Q. Co označují pole hostitel pro vlastní testy paměti?**

Pole hostitel Určuje název odeslat sondy k. Platí jenom v případě více lokalit je nakonfigurovaná na aplikační bránu, v opačném případě použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálních počítačů a je ve formátu \<protokol\>://\<hostitele\>:\<port\>\<cestu\>.

**Q. Můžete vytvořit bílou Application Gateway přístup k několika zdrojové IP adresy?**

Tento scénář lze provést pomocí skupin Nsg na podsítě brány aplikace. Následující omezení měly být umístěny v podsíti v uvedených pořadí podle priority:

* Povolí příchozí provoz ze zdrojových rozsah IP/IP.

* Povolit příchozí požadavky ze všech zdrojů na porty 65503 65534 [komunikace stavu back-end](application-gateway-diagnostics.md).

* Povolit příchozí nástroj pro vyrovnávání zatížení Azure sondy (značka AzureLoadBalancer) a příchozí přenosy virtuální sítě (virtuální síť značky) na [NSG](../virtual-network/security-overview.md).

* Blokovat všechna ostatní příchozí přenosy s odepření všechna pravidla.

* Povolte odchozí přenosy na Internet pro všechna místa určení.

**Q. Můžete použít stejný port pro veřejné a privátní přístupných naslouchací procesy?**

Ne, na tuto situaci se podpora nevztahuje.

## <a name="performance"></a>Výkon

**Q. Jak Application Gateway podporuje vysokou dostupnost a škálovatelnost?**

Aplikační brána podporuje scénáře s vysokou dostupností, až budete mít dva nebo více instancí nasazení. Azure distribuuje tyto instance napříč doménami aktualizace a odolnost zajistit, aby všechny instance nedošlo k selhání ve stejnou dobu. Aplikační brána podporuje škálovatelnost přidáním více instancí stejné bráně sdílení zatížení.

**Q. Jak dosáhnout scénář zotavení po Havárii napříč datovými centry s Application Gateway?**

Zákazníci mohou Traffic Manager slouží k distribuci provoz napříč více bran aplikace v různých datových centrech.

**Q. Automatické škálování je podporovaný?**

Ne, ale Application Gateway poskytuje metriky propustnosti, který slouží k upozornění, když je dosaženo prahové hodnoty. Ruční přidání instance nebo změna velikosti nerestartuje brány a nemá negativní vliv na stávající přenosy dat.

**Q. Podporuje ruční škálování nahoru/dolů příčina výpadku?**

Neexistuje žádné výpadky, instancí jsou rozmístěny v upgradu domén a domén selhání.

**Q. Provádí vyprazdňování připojení podpory brány aplikace?**

Ano. Můžete nakonfigurovat vyprazdňování Chcete-li změnit členy v rámci fondu back-end bez přerušení připojení. To vám umožní existující připojení pokračujte k odeslání do jejich předchozího cílové dokud toto připojení je ukončeno nebo konfigurovat vypršení časového limitu vyprší. Poznámka: Toto připojení vyprazdňování pouze počká pro aktuální připojení během letu na dokončení. Aplikační brána nemá žádné informace o stavu relace aplikace.

**Q. Jaké jsou velikosti brány aplikace?**

Služba Application Gateway je v současné době nabízena ve třech velikostech: **Small** (krátkodobé používání), **Medium** (střednědobé používání) a **Large** (dlouhodobé používání). Instance krátkodobého používání jsou určené pro scénáře vývoje a testování.

V rámci jednoho předplatného můžete vytvořit až 50 služeb Application Gateway a každá z nich může mít až 10 instancí. Každá služba Application Gateway se může skládat z 20 naslouchacích procesů HTTP. Úplný seznam omezení služby Application Gateway najdete na stránce [Omezení služby Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Následující tabulka ukazuje průměrnou propustnost výkonu pro jednotlivé instance služby Application Gateway s povoleným přesměrováním zpracování SSL:

| Průměrná velikost zadní stránky odpovědi | Krátkodobé používání | Střednědobé používání | Dlouhodobé používání |
| --- | --- | --- | --- |
| 6KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Tyto hodnoty jsou přibližné hodnoty propustnosti služby Application Gateway. Skutečná propustnost závisí na různých podrobnostech o prostředí, jako jsou například průměrná velikost stránky, umístění back-endových instancí a doba zpracování potřebná k doručení stránky. Přesné údaje o výkonu získáte, když spustíte vlastní testy. Tyto hodnoty slouží jenom jako vodítko při plánování kapacity.

**Q. Můžete změnit velikost instance ze střední velké bez přerušení?**

Ano, Azure distribuuje instancí napříč doménami aktualizace a odolnost zajistit, aby všechny instance nedošlo k selhání ve stejnou dobu. Aplikační brána podporuje škálování přidáním více instancí stejné bráně sdílení zatížení.

## <a name="ssl-configuration"></a>Konfigurace SSL

**Q. Jaké certifikáty jsou podporovány ve Application Gateway?**

Vlastní podepsané certifikáty, certifikátů certifikační Autority a certifikátů zástupnému znaku. jsou podporovány. Rozšířené ověření certifikátů nejsou podporovány.

**Q. Jaké jsou aktuální šifrovací sada podporovaná Application Gateway?**

Níže jsou uvedeny aktuální šifrovací sada podporovaná aplikační brány. Navštivte: [konfigurace SSL verze zásad a šifrovací sady ve Application Gateway](application-gateway-configure-ssl-policy-powershell.md) se dozvíte, jak přizpůsobit možnosti protokolu SSL.

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

**Q. Application Gateway také podporuje znova šifrovat provoz na back-end?**

Ano, aplikační brána podporuje přesměrování zpracování SSL a koncová SSL, který znovu zašifruje provoz na back-end.

**Q. Můžete nakonfigurovat zásady protokolu SSL pro řízení verzí protokolu SSL?**

Ano, můžete nakonfigurovat tak, aby odepřel TLS1.0, TLS1.1 a TLS1.2 Application Gateway. Protokol SSL 2.0 a 3.0 jsou už ve výchozím nastavení zakázána a se nedají konfigurovat.

**Q. Můžete nakonfigurovat šifrovací sady a pořadí zásad?**

Ano, [konfigurace šifrovací sady](application-gateway-ssl-policy-overview.md) je podporována. Při definování vlastní zásady, musí být povolena alespoň jeden z následujících sad šifer. Aplikační brána používá SHA256 k pro správu back-end.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**Q. Jak velký počet certifikátů SSL jsou podporovány?**

Až 20 SSL certifikáty jsou podporovány.

**Q. Kolik ověřovací certifikáty pro back-end znova šifrovat jsou podporovány?**

Až 10 ověřování certifikáty jsou podporovány výchozí hodnota je 5.

**Q. Umožňuje Application Gateway integraci s Azure Key Vault nativně?**

Ne, není integrované s Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfigurace brány Firewall (firewall webových aplikací) webové aplikace

**Q. SKU firewall webových aplikací nabízí všechny funkce, které jsou k dispozici standardní SKU systém?**

Ano, firewall webových aplikací podporuje všechny funkce v standardní SKU.

**Q. Co je verze řádku Application Gateway podporuje?**

Aplikační brána podporuje řádku [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a řádku [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**Q. Jak se monitorování firewall webových aplikací?**

Firewall webových aplikací je monitorována prostřednictvím protokolování diagnostiky, další informace o protokolování diagnostiky naleznete na [protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md)

**Q. Detekce režimu blokování provozu?**

Ne, detekce režimu jenom protokoly přenosy, která spustí pravidlo firewall webových aplikací.

**Q. Jak lze přizpůsobit pravidla firewall webových aplikací?**

Ano, pravidla firewall webových aplikací jsou přizpůsobitelné, další informace o tom, jak přizpůsobit návštěvu [skupiny pravidel přizpůsobit firewall webových aplikací a pravidla](application-gateway-customize-waf-rules-portal.md)

**Q. Jaká pravidla jsou nyní k dispozici?**

Firewall webových aplikací v současné době podporuje řádku [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) a [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), které poskytují základní zabezpečení proti většinu prvních 10 ohrožení zabezpečení, identifikovat pomocí otevřít webové aplikace zabezpečení projektu (OWASP) nachází tady [ Prvních 10 OWASP ohrožení zabezpečení](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Ochrana před útoky prostřednictvím injektáže SQL.

* Ochrana před skriptováním mezi weby.

* Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

* Ochrana před narušením protokolu HTTP.

* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

* Ochrana před roboty, prohledávacími moduly a skenery.

 * Detekce časté nesprávné konfigurace aplikace (tedy Apache, IIS, atd.)

**Q. Firewall webových aplikací taky podporuje DDoS prevence?**

Ne, firewall webových aplikací neposkytuje DDoS prevence.

## <a name="diagnostics-and-logging"></a>Protokolování a diagnostiky

**Q. Jaké typy protokolů jsou k dispozici s Application Gateway?**

Nejsou k dispozici pro službu Application Gateway tři protokoly. Další informace o těchto protokolů a jiné diagnostické funkce, najdete v článku [back-end stavu, protokolů diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** – protokol přístupu obsahuje každý odeslaný požadavek na front-endu Application Gateway. Data obsahují volajícího IP adresy, požadovaná, adresa URL odpovědi latence, návratový kód, bajtů a odhlášení. Protokol přístupu se shromažďují každých 300 sekund. Tento protokol obsahuje jeden záznam za instance aplikační brány.
- **ApplicationGatewayPerformanceLog** -výkonu protokolu zaznamená informace o výkonu na základě za instance včetně celkový požadavek zpracovat, propustnost v bajtech, celkový počet požadavků zpracovaných, počet neúspěšných požadavků v pořádku a není v pořádku back-end počet instancí.
- **ApplicationGatewayFirewallLog** -protokolu brány firewall obsahuje požadavky, které se protokolují prostřednictvím zjišťování nebo zabránění režim služby application gateway, která je konfigurovaná pomocí brány firewall webových aplikací.

**Q. Jak poznám, pokud jsou moje členy fondu back-end v pořádku?**

Můžete použít rutiny prostředí PowerShell `Get-AzureRmApplicationGatewayBackendHealth` nebo ověřte stav prostřednictvím portálu, navštivte stránky [diagnostiku brány aplikace](application-gateway-diagnostics.md)

**Q. Co je zásady uchovávání informací na protokolů diagnostiky?**

Diagnostické protokoly toku k účtu úložiště zákazníků a zákazníků můžete nastavit zásady uchovávání informací podle jejich předvoleb. Diagnostické protokoly můžete odeslat také do centra událostí nebo analýzy protokolů. Navštivte [Application Diagnostics brány](application-gateway-diagnostics.md) další podrobnosti.

**Q. Získání protokolů auditu pro službu Application Gateway**

Protokoly auditu jsou k dispozici pro službu Application Gateway. Na portálu, klikněte na tlačítko **protokol aktivit** v okně nabídky aplikační brány pro přístup k protokolu auditu. 

**Q. Můžete nastavit výstrahy s Application Gateway?**

Ano, aplikační brána podporuje výstrahy, se konfigurují vypnout metriky. Aplikační brána v současnosti má metriky propustnosti"", kterého lze nakonfigurovat výstrahy. Další informace o výstrahách naleznete [dostávat oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**Q. Back-end stavu vrátí Neznámý stav, co by mohlo být příčinou tento stav?**

Nejčastější příčinou je skupina NSG nebo vlastní DNS je blokován přístup k back-end. Navštivte [back-end stavu, protokolování diagnostiky a metriky pro službu Application Gateway](application-gateway-diagnostics.md) Další informace.

## <a name="next-steps"></a>Další kroky

Další informace o návštěvě Application Gateway [co je Azure Application Gateway?](overview.md)
