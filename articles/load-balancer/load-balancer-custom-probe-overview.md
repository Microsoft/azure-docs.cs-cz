---
title: Použití sond stavu Azure Load Balancer k škálování a zajištění vysoké dostupnosti pro vaši službu
titlesuffix: Azure Load Balancer
description: Informace o používání sond stavu k monitorování instancí za nástroj pro vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: allensu
ms.openlocfilehash: 75009530940a0cce7adb8469ead5f55f509a1faa
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275339"
---
# <a name="load-balancer-health-probes"></a>Sondy stavu nástroje pro vyrovnávání zatížení

Azure Load Balancer poskytuje sondy stavu pro použití s pravidly vyrovnávání zatížení.  Konfigurace sond stavu a odezvy testu určují, které instance fondu back-end budou dostávat nové toky. Sondy stavu můžete použít ke zjištění chyby aplikace na instanci back-endu. Můžete také vygenerovat vlastní odpověď na sondu stavu a použít sondu stavu pro řízení toku ke správě zátěže nebo plánovaného výpadku. Při selhání sondy stavu nástroje pro vyrovnávání zatížení zastaví odesílání nových toků do příslušné instance není v pořádku.

Sondy stavu podporují více protokolů. Dostupnost konkrétního typu sondy stavu pro podporu konkrétního protokolu se liší podle Load Balancer SKU.  Kromě toho se chování služby liší podle Load Balancer SKU.

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| [Typy testu](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Dolů chování pro zjišťování](#probedown) | Všechny testy, všechny toky TCP pokračovat. | Všechny sondy vyprší, všechny toky TCP vyprší. | 

> [!IMPORTANT]
> Load Balancer sondy stavu pocházejí z IP adresy 168.63.129.16 a nesmí být blokovaná pro sondy k označení vaší instance.  Kontrola [Zdrojová IP adresa pro zjišťování](#probesource) podrobnosti.

## <a name="types"></a>Typy testu

Sondu stavu lze nakonfigurovat pro naslouchací procesy pomocí následujících tří protokolů:

- [Moduly pro naslouchání TCP](#tcpprobe)
- [Koncové body HTTP](#httpprobe)
- [Koncové body HTTPS](#httpsprobe)

Dostupné typy sondy stavu služby se liší v závislosti na vybrané SKU nástroje pro vyrovnávání zatížení:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardní SKU |    &#9989; |   &#9989; |   &#9989; |
| Základní SKU |   &#9989; |   &#9989; | &#10060; |

Bez ohledu na to, jaký typ testu jste zvolili, sondy stavu můžou sledovat jakýkoli port back-endu instance, včetně portu, na kterém je zadaná skutečná služba.

### <a name="tcpprobe"></a> Test protokolu TCP

Sondy protokolu TCP inicializovat připojení pomocí provádí trojcestných otevřít ověření TCP metodou handshake s definovaný port.  Sondy TCP ukončí připojení se čtyřnásobnou metodou handshake TCP.

Interval minimální testu je 5 sekund a minimální počet odpovědí na není v pořádku, je 2.  Celková doba trvání všech intervalů nesmí překročit 120 sekund.

Sondu protokolu TCP není úspěšné při:
* Naslouchací proces TCP na instanci během časového limitu nereaguje vůbec.  Sonda je označena na základě počtu neúspěšných žádostí o test, které byly nakonfigurovány pro přechod na nezodpovězené před označením sondy.
* Sonda obdrží TCP obnovit z instance.

#### <a name="resource-manager-template"></a>Šablona Resource Manageru

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS pro zjišťování

> [!NOTE]
> Test HTTPS je dostupná jenom pro [Load balanceru úrovně Standard](load-balancer-standard-overview.md).

Testy HTTP a HTTPS se sestavují v testu TCP a vystavují HTTP GET se zadanou cestou. Obě tyto sondy HTTP GET podporu relativní cesty. HTTPS testy jsou stejné jako sondy protokolu HTTP a uveďte (TLS, dřív označované jako SSL) Transport Layer Security obálky. Sonda stavu je označen, pokud odpoví instance se stavem HTTP 200 v časovém limitu.  Sonda stavu se ve výchozím nastavení pokusí ověřit nakonfigurovaný port sondy stavu každých 15 sekund. Interval minimální testu je 5 sekund. Celková doba trvání všech intervalů nesmí překročit 120 sekund.

Sondy HTTP/HTTPS můžou být užitečné také v případě, že chcete vyjádřit stav sondy.  implementací vlastní logiky odeberte instance z rotace nástroje pro vyrovnávání zatížení, pokud je port testu také naslouchací proces pro samotnou službu. Například můžete rozhodnout pro odebrání instance, pokud je vyšší než 90 % využití procesoru a vrátit stav 200 HTTP. 

Pokud používáte cloudové služby a webovými rolemi, které používají w3wp.exe, můžete také dosáhnout automatické monitorování vašeho webu. Chyby v kódu webu návratový stav než 200 pro test paměti nástroje pro vyrovnávání zatížení.

HTTP / HTTPS testu není úspěšné při:
* Koncový bod vrátí kód odpovědi HTTP než 200 (například 403, 404 nebo 500). Tím se okamžitě označí sonda stavu. 
* Koncový bod testu nereaguje vůbec na uplynutí 31 sekund časového limitu. Než bude sonda označena jako nespuštěná a dokud nebude dosaženo součtu všech časových intervalů, může dojít k nezodpovězení více požadavků sondy.
* Koncový bod uzavře připojení prostřednictvím protokolu TCP resetování.

#### <a name="resource-manager-templates"></a>Šablony Resource Manageru

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>Test agenta hosta (pouze Klasický model)

Role cloudové služby (role pracovního procesu a webové role) hostovaného agenta použít pro test monitorování ve výchozím nastavení.  Test hostovaného agenta je poslední konfigurace.  Vždy používejte sondu stavu explicitně se sondou TCP nebo HTTP. Test agenta hosta není co nejúčinnější explicitně definované sondy pro většinu scénářů aplikace.

Test agenta hosta je kontrolu agent hosta ve virtuálním počítači. Potom naslouchá a jako odpověď vrátí odpověď HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno. (Ostatní stavy jsou zaneprázdněn recyklaci nebo ukončení).

Další informace najdete v tématu [konfigurace definiční soubor služby (csdef) pro sondy stavu](https://msdn.microsoft.com/library/azure/ee758710.aspx) nebo [Začínáme tím, že vytvoříte nástroj pro vyrovnávání zatížení veřejnou pro cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Pokud agent hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení označí instance jako reagovat. Poté se zastaví, odesílání toky do této instance. Nástroje pro vyrovnávání zatížení pokračuje v kontrole instance. 

Pokud agent hosta, který odpovídá zprávou HTTP 200, nástroje pro vyrovnávání zatížení odešle nové toky do této instance znovu.

Pokud používáte webovou roli, kód webu obvykle běží v w3wp.exe, který není monitorován pomocí Azure fabric nebo hostovaného agenta. Agent hosta nejsou hlášeny chyby v w3wp.exe (například odpovědi protokolu HTTP 500). Nástroje pro vyrovnávání zatížení v důsledku toho nepřijímá tuto instanci ze smyčky.

<a name="health"></a>
## <a name="probehealth"></a>Sondy stavu

Sondy stavu protokolu TCP, HTTP a HTTPS se považují za v pořádku a označit instanci role jako v pořádku v případě:

* Sonda stavu je po spuštění virtuálního počítače úspěšná.
* Zadaný počet sond potřebných k označení instance role jako v dobrém stavu.

> [!NOTE]
> Pokud se sonda stavu dostanou, nástroj pro vyrovnávání zatížení počká déle, než převede instanci role zpátky do stavu v pořádku. Tato doba čekání navíc chrání uživatele a infrastruktury a jsou záměr zásady.

## <a name="probe-count-and-timeout"></a>Počet a časový limit pro zjišťování

Chování sondy závisí na:

* Počet úspěšné testy, které umožňují instance být označený jako zapnutý.
* Počet selhání sondy, které způsobují instance označit vypnuté.

Zadané hodnoty časového limitu a intervalu určují, jestli je instance označená jako nahoru nebo dolů.

## <a name="probedown"></a>Dolů chování pro zjišťování

### <a name="tcp-connections"></a>Připojení TCP

Nové připojení TCP budou podařit zbývající instance back-endu v pořádku.

Pokud sonda stavu back-end instance selže, pokračovat navázané připojení TCP do této instance back-endu.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, žádné nové toky se odešlou do back-endový fond. Load balancer úrovně Standard vám umožní zavedené Velkoobjemové toky, abyste mohli pokračovat.  Load balancer úrovně Basic se ukončí všechny stávající toky TCP na back-endový fond.
 
Load Balancer je průchozí služba (neukončuje připojení TCP) a tok je vždycky mezi klientem a hostovaným operačním systémem a aplikací. Fond se všemi sondami vychází z front-endu nereagujících na otevřené pokusy o připojení TCP (SYN), protože není k dispozici bezproblémová instance back-endu, která by přijímala tok a reaguje na SYN-ACK.

### <a name="udp-datagrams"></a>Datagramů UDP

Datagramy UDP bude doručen do instance back-end v pořádku.

Je přenos UDP a neexistuje žádný stav toku sledovány pro protokol UDP. Pokud sonda stavu všechny instance back-endu selže, může stávající toky UDP přesunout do jiné instance v pořádku v back-endový fond.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, stávající toky UDP se ukončí u úrovní Basic a Standard nástroje pro vyrovnávání zatížení.

<a name="source"></a>
## <a name="probesource"></a>Zdrojová IP adresa pro zjišťování

Nástroj pro vyrovnávání zatížení používá distribuované zjišťování služby pro jeho vnitřní stavů modelu. Služba zjišťování se nachází na všech hostitelích, ve kterých se virtuální počítače můžou programovat na vyžádání a generovat sondy stavu podle konfigurace zákazníka. Provoz sondy stavu je přímo mezi službou probingu, která generuje sondu stavu a virtuální počítač zákazníka. Z dané IP adresy 168.63.129.16 jako jejich zdroje mají původ sondy stavu všechny nástroje pro vyrovnávání zatížení.  Můžete použít adresní prostor IP adres v rámci virtuální sítě, která není RFC1918 místo.  Při použití globálně rezervované IP adresy Microsoftu snižuje riziko konfliktu IP adres s adresním prostorem IP adres, který používáte ve virtuální síti.  Tato IP adresa je stejná ve všech oblastech a nemění se a nejedná se o bezpečnostní riziko, protože z této IP adresy může zdroj paketů nabývat jenom interní součástí platformy Azure. 

Značka služby AzureLoadBalancer identifikuje tuto zdrojovou IP adresu ve [skupinách zabezpečení sítě](../virtual-network/security-overview.md) a ve výchozím nastavení povoluje provoz sondy stavu.

Kromě Load Balancer sond stavu tato [IP adresa používá následující operace](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Povolí agenta virtuálního počítače pro komunikaci s platformou, který signalizuje, že je ve stavu "Připraveno"
- Umožňuje komunikaci s virtuálním serverem DNS k překladu názvů filtrovaná pro zákazníky, kteří nemá definován vlastní servery DNS.  Toto filtrování se zajistí, že zákazníci můžou jenom překládat názvy hostitelů jejich nasazení.
- Umožňuje virtuálnímu počítači získat dynamickou IP adresu ze služby DHCP v Azure.

## <a name="design"></a>Doprovodné materiály k návrhu

Sondy stavu slouží k zajištění odolnosti služby a umožňují škálování. Nesprávná konfigurace nebo špatný vzor návrhu může mít vliv na dostupnost a škálovatelnost vaší služby. Projděte si celý dokument a zvažte, jaký dopad ve vašem scénáři je v případě, že je tato odpověď v testu označena příznakem nebo označená a jak má dopad na dostupnost vašeho scénáře vaší aplikace.

Při návrhu modelu stavu aplikace byste měli testovat port na back-end instanci, která odráží stav této instance __a__ aplikační služby, kterou poskytujete.  Port aplikace a port testu musí být stejné.  V některých scénářích může být žádoucí, aby se port testu lišil od portu, na kterém vaše aplikace poskytuje službu.  

Někdy může být užitečné, aby vaše aplikace vygenerovala odpověď stavové sondy jenom k detekci stavu vaší aplikace, ale také signalizaci přímo Load Balancer, jestli by vaše instance měla přijímat nebo nedostávat nové toky.  Můžete manipulovat s odezvou testu, aby vaše aplikace mohla vytvářet beztlakové zatížení a omezovat doručování nových toků do instance tím, že selže sondu stavu nebo se připraví na údržbu vaší aplikace a zahájit vyprazdňování vašeho scénáře.  Při použití Standard Load Balancer signál [zkušebního](#probedown) stavu vždy umožní, aby toky TCP pokračovaly až do vypršení časového limitu nečinnosti nebo ukončení připojení. 

Pro vyrovnávání zatížení UDP byste měli vygenerovat vlastní signál sondy stavu z back-endu instance a použít test stavu TCP, HTTP nebo HTTPS, který cílí na odpovídající naslouchací proces, aby odrážel stav vaší aplikace UDP.

Pokud používáte [pravidla vyrovnávání zatížení s porty ha](load-balancer-ha-ports-overview.md) s [Standard Load Balancer](load-balancer-standard-overview.md), všechny porty jsou vyvážené vyrovnáváním zatížení a jedna odpověď na sondu stavu musí odrážet stav celé instance.

Neprovádějte překládání nebo proxy stav sondy prostřednictvím instance, která obdrží sondu stavu s jinou instancí ve vaší virtuální síti, protože tato konfigurace může způsobit kaskádové chyby ve vašem scénáři.  Vezměte v úvahu následující scénář: sada zařízení třetích stran je nasazená do back-endu Load Balancer prostředku, aby poskytovala škálování a redundanci pro zařízení a sondu stavu je nakonfigurované tak, aby provedla test portu, který proxy zařízení třetí strany nebo překládá se na jiné virtuální počítače za zařízením.  Pokud provedete test stejného portu, který používáte k překladu nebo proxy požadavků na jiné virtuální počítače za zařízením, označí jakákoli odezva z jednoho virtuálního počítače za zařízení jako nedoručitelné. Tato konfigurace může způsobit kaskádové selhání celého scénáře aplikace v důsledku jediné instance back-endu za zařízením.  Triggerem může být přerušované selhání sondy, které způsobí, že Load Balancer označí původní cíl (instance zařízení), a pak může celý scénář aplikace vypnout. Místo toho se vyhledá stav samotného zařízení. Výběr sondy pro určení zdravotní signalizace je důležitým aspektem scénářů síťových virtuálních zařízení (síťové virtuální zařízení) a musíte požádat dodavatele aplikace o to, aby pro takové scénáře byl vhodný signál stavu.

Pokud nepovolíte [zdrojovou IP adresu](#probesource) testu v zásadách brány firewall, sonda stavu selže, protože se nebude schopen spojit s vaší instancí.  Nástroj pro vyrovnávání zatížení pak označí dolů instanci z důvodu selhání sondy stavu.  Tato Chybná konfigurace může způsobit selhání scénáře vaší aplikace s vyrovnáváním zatížení.

Pokud chcete, aby se sonda stavu Load Balancera, aby se dala označit vaše instance, **musíte** tuto IP adresu nastavit v jakýchkoli [skupinách zabezpečení sítě](../virtual-network/security-overview.md) Azure a místních zásadách brány firewall.  Ve výchozím nastavení každá skupina zabezpečení sítě zahrnuje [značku služby](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer, která povoluje provoz sondy stavu.

Pokud chcete otestovat selhání sondy stavu nebo označit jednotlivou instanci, můžete k explicitnímu blokování sondy stavu (cílového portu nebo [zdrojové IP adresy](#probesource)) použít [skupiny zabezpečení sítě](../virtual-network/security-overview.md) a simulovat selhání testu.

Nekonfigurujte svou virtuální síť pomocí rozsahu IP adres vlastněných společností Microsoft, který obsahuje 168.63.129.16.  Takové konfigurace budou kolidovat s IP adresou sondy stavu a můžou způsobit selhání scénáře.

Pokud máte více rozhraní na virtuálním počítači, musíte zajistit, že vám odpoví na test, který jste dostali v rozhraní.  Možná budete muset Zdrojová síťová adresa přeložit tuto adresu ve virtuálním počítači na každé rozhraní.

Nepovolujte [Časová razítka TCP](https://tools.ietf.org/html/rfc1323).  Povolením časových razítek TCP dojde k selhání sond stavu kvůli tomu, že pakety TCP jsou vyřazeny ze zásobníku TCP hostovaného operačního systému virtuálního počítače. výsledkem Load Balancer označení příslušného koncového bodu.  Ve výchozím nastavení jsou časová razítka TCP zapnutá u imagí s posíleným zabezpečením virtuálních počítačů a musí být zakázaná.

## <a name="monitoring"></a>Monitorování

Veřejné a vnitřní [Load balanceru úrovně Standard](load-balancer-standard-overview.md) vystavit na koncový bod a back-end instance sondy stavu jako vícedimenzionálních metrik přes Azure Monitor. Tyto metriky můžou využívat jiné služby Azure nebo partnerské aplikace. 

Základní veřejné Load Balancer zveřejňuje stav sondy stavu pro každý back-end fond prostřednictvím protokolů Azure Monitor.  Protokoly Azure Monitor nejsou k dispozici pro interní základní nástroje pro vyrovnávání zatížení.  Pomocí [protokolů Azure monitor](load-balancer-monitor-log.md) můžete kontrolovat stav testu veřejného nástroje pro vyrovnávání zatížení a počet testů. Protokolování je možné s Power BI nebo Azure Operational Insights k poskytování statistické údaje o stavu nástroje pro vyrovnávání zatížení.

## <a name="limitations"></a>Omezení

- Sondy HTTPS nepodporují vzájemného ověřování pomocí certifikátu klienta.
- Sondy stavu selžou, pokud jsou povolená časová razítka TCP.

## <a name="next-steps"></a>Další kroky

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- [Začínáme s vytvářením nástroj pro vyrovnávání zatížení veřejnou v Resource Manageru pomocí prostředí PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [Rozhraní REST API pro sondy stavu](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Žádost o nové možnosti sondu stavu s [Uservoice pro vyrovnávání zatížení](https://aka.ms/lbuservoice)
