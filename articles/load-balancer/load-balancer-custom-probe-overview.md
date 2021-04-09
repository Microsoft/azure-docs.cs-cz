---
title: Zajištění vysoké dostupnosti a škálování služby s využitím sond stavu
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak pomocí sond stavu monitorovat instance za Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a008d7b26738b9552a7a43ab026391bd9afe0aa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780939"
---
# <a name="load-balancer-health-probes"></a>Sondy stavu Load Balanceru

Pokud používáte pravidla vyrovnávání zatížení s Azure Load Balancer, je nutné zadat sondy stavu, aby bylo možné Load Balancer detekovat stav koncového bodu back-endu.  Konfigurace sondy stavu a odezvy sondy určují, které instance fondu back-end budou dostávat nové toky. Sondy stavu můžete použít k detekci selhání aplikace na koncovém bodu back-endu. Můžete také vygenerovat vlastní odpověď na sondu stavu a použít sondu stavu pro řízení toku ke správě zátěže nebo plánovaného výpadku. Pokud selže test stavu, Load Balancer zastaví odesílání nových toků do příslušné instance, která není v pořádku. Odchozí připojení nemá vliv na jenom příchozí připojení.

Sondy stavu podporují více protokolů. Dostupnost konkrétního protokolu sondy stavu se liší podle Load Balancer SKU.  Kromě toho se chování služby liší podle Load Balancer SKU, jak je znázorněno v této tabulce:

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| **[Typy sond](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Chování testu](#probedown)** | Všechny sondy vyplynou, všechny toky TCP pokračuje. | Všechny sondy vyprší, všechny toky TCP vyprší. | 


>[!IMPORTANT]
>Pokud chcete vytvořit spolehlivou službu, Projděte si tento dokument v plném rozsahu, včetně důležitých [pokynů k návrhu](#design) .

>[!IMPORTANT]
>Load Balancer sondy stavu pocházejí z IP adresy 168.63.129.16 a nesmí být blokovaná pro sondy k označení vaší instance.  Podrobnosti najdete ve [zdrojové IP adrese sondy](#probesource) .

>[!IMPORTANT]
>Bez ohledu na nakonfigurovanou prahovou hodnotu časového limitu budou testy paměti Load Balancer protokolu HTTP (S) automaticky testovat instanci, pokud server vrátí stavový kód, který není HTTP 200 OK nebo pokud je připojení ukončeno pomocí resetování protokolu TCP.

## <a name="probe-configuration"></a><a name="probes"></a>Konfigurace testu paměti

Konfigurace sondy stavu se skládá z následujících prvků:

- Doba trvání intervalu mezi jednotlivými sondami
- Počet odezvy sondy, které musí být pozorovány před přechodem do jiného stavu testu.
- Protokol testu
- Port testu
- Cesta HTTP, která se má použít pro HTTP GET při použití sond HTTP (S)

>[!NOTE]
>Definice sondy není povinná ani při použití Azure PowerShell, Azure CLI, šablon nebo rozhraní API zaškrtnutá. Testy ověření sondy se provádějí jenom při použití webu Azure Portal.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Porozumění signalizaci aplikace, detekci signálu a reakci platformy

Počet odezvy sondy platí pro obě

- počet úspěšných testů, které umožňují označit instanci jako a
- Počet testů s časovým limitem, které způsobují, že instance bude označena jako nefunkční.

Zadané hodnoty časového limitu a intervalu určují, zda bude instance označena jako nahoru nebo dolů.  Doba trvání intervalu vynásobeného počtem testových odpovědí určuje dobu, během které musí být detekovány testy testů.  A služba bude po dosažení požadovaných sond reagovat.

Chování můžeme dále ilustrovat v příkladu. Pokud jste nastavili počet testů testu na hodnotu 2 a interval na 5 sekund, znamená to, že 2 selhání sondy musí být dodržena v intervalu 10 sekund.  Vzhledem k tomu, že čas odeslání sondy není synchronizován, když vaše aplikace může změnit stav, můžeme čas zjistit podle dvou scénářů:

1. Pokud vaše aplikace začne vytvářet odpověď sondy časového limitu těsně před tím, než se první test dorazí, detekce těchto událostí bude trvat 10 sekund (2 x 5 sekund) a doba trvání aplikace, která začíná signalizovat časový limit při první dochodu testu.  Můžete předpokládat, že tato detekce bude trochu víc než 10 sekund trvat.
2. Pokud vaše aplikace začne vytvářet odezvy sondy časového limitu hned po přijetí prvního testu, detekce těchto událostí nebude zahájena, dokud nedojde k dalšímu testu paměti (a vypršení časového limitu) a dalších 10 sekund (2 x 5 sekund intervalů).  Tuto detekci můžete předpokládat, pokud chcete trvat jen 15 sekund.

V tomto příkladu, jakmile k detekci dojde, bude platforma trvat malou dobu, než bude tato změna reagovat.  To znamená, že v závislosti na 

1. Když aplikace začne měnit stav a
2. Když se tato změna zjistí a splní požadovaná kritéria (počet sond odeslaných v zadaném intervalu) a
3. Když se zjišťování komunikuje napříč platformou 

můžete předpokládat reakci na odpověď sondy časového limitu, která bude trvat minimálně za 10 sekund a maximálně mírně více než 15 sekund, aby reagovala na změnu v signálu z aplikace.  Tento příklad je k dispozici k ilustraci toho, co se provádí, ale není možné předpověď přesně přes výše uvedené přibližné doprovodné materiály, které jsou znázorněny v tomto příkladu.

>[!NOTE]
>Sonda stavu vyhledá všechny spuštěné instance ve fondu back-endu. Pokud je instance zastavena, nebude zjišťována, dokud nebude znovu spuštěna.

## <a name="probe-types"></a><a name="types"></a>Typy sond

Protokol používaný sondou stavu lze nakonfigurovat na jednu z následujících možností:

- [Naslouchací procesy TCP](#tcpprobe)
- [Koncové body HTTP](#httpprobe)
- [Koncové body HTTPS](#httpsprobe)

Dostupné protokoly závisí na použité Load Balancer SKU:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **Standardní SKU** |    &#9989; |   &#9989; |   &#9989; |
| **Základní SKU** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> Test TCP

Sondy TCP iniciují připojení provedením třícestných Open TCP handshake s definovaným portem.  Sondy TCP ukončí připojení se čtyřnásobnou metodou handshake TCP.

Minimální interval testu je 5 sekund a minimální počet odpovědí, které nejsou v pořádku, je 2.  Celková doba trvání všech intervalů nesmí překročit 120 sekund.

Test TCP se nezdařil v těchto případech:
* Naslouchací proces TCP na instanci po uplynutí časového limitu nereaguje.  Sonda je označena na základě počtu požadavků zkušebního použití, které byly nakonfigurovány na nezodpovězené před označením sondy.
* Sonda přijímá z instance resetování TCP.

Následující příklad ukazuje, jak můžete vyjádřit tento druh konfigurace sondy v šabloně Správce prostředků:

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

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a>Test http/https

>[!NOTE]
>Test HTTPS je dostupný jenom pro [Standard Load Balancer](./load-balancer-overview.md).

Testy HTTP a HTTPS se sestavují v testu TCP a vystavují HTTP GET se zadanou cestou. Obě tyto sondy podporují relativní cesty pro HTTP GET. Testy protokolu HTTPS jsou stejné jako testy HTTP s přidáním obálky TLS (Transport Layer Security), dříve označované jako SSL (Transport Layer Security). Sonda stavu je označena v případě, že instance reaguje se stavem HTTP 200 v rámci časového limitu.  Sonda stavu se ve výchozím nastavení pokusí ověřit nakonfigurovaný port sondy stavu každých 15 sekund. Minimální interval testu je 5 sekund. Celková doba trvání všech intervalů nesmí překročit 120 sekund.

Sondy HTTP/HTTPS můžete také využít k implementaci vlastní logiky k odebrání instancí z rotace nástroje pro vyrovnávání zatížení, pokud je port testu také naslouchací proces pro samotnou službu. Například se můžete rozhodnout odebrat instanci, pokud je vyšší než 90% CPU a vrátit stav HTTP, který není 200. 

> [!NOTE] 
> Test HTTPS vyžaduje použití certifikátů založených na minimální hodnotě hash signatury SHA256 v celém řetězci.

Pokud používáte Cloud Services a máte webové role, které používají w3wp.exe, dosáhnete také automatického monitorování vašeho webu. Chyby ve vašem kódu webu vrátí do testu nástroje pro vyrovnávání zatížení stav, který není 200.

Test HTTP/HTTPS se nezdařil v těchto případech:
* Koncový bod testu vrátí jiný kód odpovědi HTTP než 200 (například 403, 404 nebo 500). Tím se okamžitě označí sonda stavu. 
* Koncový bod testu nereaguje vůbec na minimum intervalu sondy a 30 sekund časového limitu. Než bude sonda označena jako nespuštěná a dokud nebude dosaženo součtu všech časových intervalů, může dojít k nezodpovězení více požadavků sondy.
* Koncový bod testu ukončí připojení pomocí resetu protokolu TCP.

Následující příklad ukazuje, jak můžete vyjádřit tento druh konfigurace sondy v šabloně Správce prostředků:

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

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Test hostovaného agenta (jenom klasický)

Role cloudové služby (role pracovních procesů a webové role) používají pro monitorování sondy ve výchozím nastavení agenta hosta.  Test hostovaného agenta je poslední konfigurace.  Vždy používejte sondu stavu explicitně se sondou TCP nebo HTTP. Test agenta hosta není tak efektivní jako explicitně definované sondy pro většinu scénářů aplikací.

Sonda agenta hosta je kontrolou agenta hosta uvnitř virtuálního počítače. Pak naslouchá a reaguje s odpovědí HTTP 200 OK pouze v případě, že je instance ve stavu připraveno. (Ostatní stavy jsou zaneprázdněné, recyklované nebo zastavované.)

Další informace najdete v tématu [Konfigurace definičního souboru služby (csdef) pro sondy stavu](/previous-versions/azure/reference/ee758710(v=azure.100)) nebo [Začínáme vytvořením veřejného nástroje pro vyrovnávání zatížení pro cloudové služby](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Pokud Agent hosta přestane reagovat pomocí protokolu HTTP 200 OK, nástroj pro vyrovnávání zatížení označí instanci jako nereagující. Poté zastaví odesílání toků do této instance. Nástroj pro vyrovnávání zatížení pokračuje v kontrole instance. 

Pokud Agent hosta odpoví HTTP 200, nástroj pro vyrovnávání zatížení odešle nové toky do této instance znovu.

Při použití webové role se kód webu obvykle spouští v w3wp.exe, který není monitorován pomocí prostředků infrastruktury Azure nebo hostovaného agenta. Selhání v w3wp.exe (například odpovědi HTTP 500) nejsou hlášeny agentovi hosta. V důsledku toho nástroj pro vyrovnávání zatížení nepřevezme tuto instanci mimo rotaci.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Chování testu

Sondy stavu TCP, HTTP a HTTPS se považují za v pořádku a označí koncový bod back-end jako v pořádku, když:

* Sonda stavu je po spuštění virtuálního počítače úspěšná.
* Zadaný počet sond potřebných k označení koncového bodu back-endu, který byl dosažen v dobrém stavu.

Libovolný koncový bod back-end, který dosáhl stavu v pořádku, je způsobilý pro příjem nových toků.  

> [!NOTE]
> Pokud se sonda stavu dostanou, nástroj pro vyrovnávání zatížení počká déle, než převede koncový bod back-endu zpátky do stavu v pořádku. Tato dodatečná doba čekání chrání uživatele a infrastrukturu a je záměrné zásady.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Chování testu

### <a name="tcp-connections"></a>Připojení TCP

Nová připojení TCP budou podařit zbývajícímu koncovému bodu v pořádku.

Pokud se test stavu koncového bodu back-endu nepovede, navázalo se připojení TCP k tomuto koncovému bodu back-endu.

Pokud všechny testy pro všechny instance v back-end fondu selžou, neodesílají se do fondu back-end žádné nové toky. Standard Load Balancer umožní pokračovat v navázání toků TCP.  Základní Load Balancer ukončí všechny existující toky TCP do back-endu fondu.
 
Load Balancer je průchozí služba (neukončuje připojení TCP) a tok je vždycky mezi klientem a hostovaným operačním systémem a aplikací. Fond se všemi sondami vychází z front-endu nereagujících na otevřené pokusy o připojení TCP (SYN), protože není k dispozici žádný koncový bod back-endu pro příjem toku a reaguje na SYN – ACK.

### <a name="udp-datagrams"></a>Datagramy UDP

Datagramy UDP budou doručeny do zdravých koncových bodů back-endu.

UDP je bez připojení a neexistuje žádný sledovaný stav toků pro UDP. Pokud selže test stavu koncového bodu back-end, stávající toky UDP se přesunou do jiné funkční instance ve fondu back-endu.

Pokud všechny sondy pro všechny instance v back-end fondu selžou, stávající toky UDP budou ukončeny pro základní a standardní služby Vyrovnávání zatížení.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Zdrojová IP adresa testu

Load Balancer používá distribuovanou službu zjišťování pro svůj interní model stavu. Služba zjišťování se nachází na všech hostitelích, ve kterých se virtuální počítače můžou programovat na vyžádání a generovat sondy stavu podle konfigurace zákazníka. Provoz sondy stavu je přímo mezi službou probingu, která generuje sondu stavu a virtuální počítač zákazníka. Všechny sondy stavu Load Balancer pocházejí z IP adresy 168.63.129.16 jako svůj zdroj.  Můžete použít adresní prostor IP adres v rámci virtuální sítě, která není RFC1918 místo.  Při použití globálně rezervované IP adresy Microsoftu snižuje riziko konfliktu IP adres s adresním prostorem IP adres, který používáte ve virtuální síti.  Tato IP adresa je stejná ve všech oblastech a nemění se a nejedná se o bezpečnostní riziko, protože z této IP adresy může zdroj paketů nabývat jenom interní součástí platformy Azure. 

Značka služby AzureLoadBalancer identifikuje tuto zdrojovou IP adresu ve [skupinách zabezpečení sítě](../virtual-network/network-security-groups-overview.md) a ve výchozím nastavení povoluje provoz sondy stavu.

Kromě Load Balancer sond stavu tato [IP adresa používá následující operace](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Povolí agentovi virtuálního počítače komunikovat s platformou k signalizaci, že je ve stavu "připraveno".
- Umožňuje komunikaci s virtuálním serverem DNS poskytovat filtrované rozlišení názvů zákazníkům, kteří nedefinují vlastní servery DNS.  Toto filtrování zajišťuje, že zákazníci můžou přeložit jenom názvy hostitelů jejich nasazení.
- Umožňuje virtuálnímu počítači získat dynamickou IP adresu ze služby DHCP v Azure.

## <a name="design-guidance"></a><a name="design"></a> Doprovodné materiály k návrhu

Sondy stavu slouží k zajištění odolnosti služby a umožňují škálování. Nesprávná konfigurace nebo špatný vzor návrhu může mít vliv na dostupnost a škálovatelnost vaší služby. Projděte si celý dokument a zvažte, jaký dopad ve vašem scénáři je v případě, že je tato odpověď v testu označena příznakem nebo označená a jak má dopad na dostupnost vašeho scénáře vaší aplikace.

Při návrhu modelu stavu aplikace byste měli testovat port na koncovém bodu back-end, který odráží stav této instance __a__ aplikační služby, kterou poskytujete.  Port aplikace a port testu musí být stejné.  V některých scénářích může být žádoucí, aby se port testu lišil od portu, na kterém vaše aplikace poskytuje službu.  

Někdy může být užitečné, aby vaše aplikace vygenerovala odpověď stavové sondy jenom k detekci stavu vaší aplikace, ale také signalizaci přímo Load Balancer, jestli by vaše instance měla přijímat nebo nedostávat nové toky.  Můžete manipulovat s odezvou testu, aby vaše aplikace mohla vytvářet beztlakové zatížení a omezovat doručování nových toků do instance tím, že selže sondu stavu nebo se připraví na údržbu vaší aplikace a zahájit vyprazdňování vašeho scénáře.  Při použití Standard Load Balancer signál [zkušebního](#probedown) stavu vždy umožní, aby toky TCP pokračovaly až do vypršení časového limitu nečinnosti nebo ukončení připojení. 

Pro vyrovnávání zatížení UDP byste měli z koncového bodu back-end vygenerovat vlastní signál sondy stavu a použít test stavu TCP, HTTP nebo HTTPS, který cílí na odpovídající naslouchací proces, aby odrážel stav vaší aplikace UDP.

Pokud používáte [pravidla vyrovnávání zatížení s porty ha](load-balancer-ha-ports-overview.md) s [Standard Load Balancer](./load-balancer-overview.md), všechny porty jsou vyvážené vyrovnáváním zatížení a jedna odpověď na sondu stavu musí odrážet stav celé instance.

Neprovádějte překládání nebo proxy stav sondy prostřednictvím instance, která obdrží sondu stavu s jinou instancí ve vaší virtuální síti, protože tato konfigurace může způsobit kaskádové chyby ve vašem scénáři.  Vezměte v úvahu následující scénář: sada zařízení třetích stran je nasazená do back-endu Load Balancer prostředku, aby poskytovala škálování a redundanci pro zařízení, a sondu stavu je nakonfigurovaná tak, aby provedla test portu, který proxy zařízení třetí strany nebo překládá na jiné virtuální počítače za zařízením.  Pokud provedete test stejného portu, který používáte k překladu nebo proxy požadavků na jiné virtuální počítače za zařízením, označí jakákoli odezva z jednoho virtuálního počítače za zařízení jako nedoručitelné. Tato konfigurace může způsobit kaskádové selhání celého scénáře aplikace v důsledku jednoho koncového bodu back-endu za zařízením.  Triggerem může být přerušované selhání sondy, které způsobí, že Load Balancer označí původní cíl (instance zařízení), a pak může celý scénář aplikace vypnout. Místo toho se vyhledá stav samotného zařízení. Výběr sondy pro určení zdravotní signalizace je důležitým aspektem scénářů síťových virtuálních zařízení (síťové virtuální zařízení) a musíte požádat dodavatele aplikace o to, aby pro takové scénáře byl vhodný signál stavu.

Pokud nepovolíte [zdrojovou IP adresu](#probesource) testu v zásadách brány firewall, sonda stavu selže, protože se nebude schopen spojit s vaší instancí.  Pak Load Balancer označí vaši instanci z důvodu selhání sondy stavu.  Tato Chybná konfigurace může způsobit selhání scénáře vaší aplikace s vyrovnáváním zatížení.

Pokud chcete, aby se sonda stavu Load Balancera, aby se dala označit vaše instance, **musíte** tuto IP adresu nastavit v jakýchkoli [skupinách zabezpečení sítě](../virtual-network/network-security-groups-overview.md) Azure a místních zásadách brány firewall.  Ve výchozím nastavení každá skupina zabezpečení sítě zahrnuje [značku služby](../virtual-network/network-security-groups-overview.md#service-tags) AzureLoadBalancer, která povoluje provoz sondy stavu.

Pokud chcete otestovat selhání sondy stavu nebo označit jednotlivou instanci, můžete k explicitnímu blokování sondy stavu (cílového portu nebo [zdrojové IP adresy](#probesource)) použít [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md) a simulovat selhání testu.

Nekonfigurujte svou virtuální síť pomocí rozsahu IP adres vlastněných společností Microsoft, který obsahuje 168.63.129.16.  Takové konfigurace budou kolidovat s IP adresou sondy stavu a můžou způsobit selhání scénáře.

Pokud máte na svém VIRTUÁLNÍm počítači více rozhraní, musíte si být jistí, že odpovíte na test na rozhraní, na kterém jste ho dostali.  Možná budete muset Zdrojová síťová adresa přeložit tuto adresu ve virtuálním počítači na každé rozhraní.

Nepovolujte [Časová razítka TCP](https://tools.ietf.org/html/rfc1323).  Povolení časových razítek TCP může způsobit selhání sond stavu kvůli tomu, že pakety TCP jsou vyřazeny z hostovaného operačního systému hostovaného operačního systému virtuálního počítače. výsledkem Load Balancer označení příslušného koncového bodu.  Ve výchozím nastavení jsou časová razítka TCP zapnutá u imagí s posíleným zabezpečením virtuálních počítačů a musí být zakázaná.

## <a name="monitoring"></a>Monitorování

Veřejné i interní [Standard Load Balancer](./load-balancer-overview.md) zveřejňují stav testu stavu koncového bodu na koncovém bodu a koncovým bodem back-end jako multidimenzionální metriky prostřednictvím Azure monitor. Tyto metriky můžou využívat jiné služby Azure nebo partnerské aplikace. 

Základní veřejné Load Balancer zveřejňuje stav sondy stavu pro každý back-end fond prostřednictvím protokolů Azure Monitor.  Protokoly Azure Monitor nejsou k dispozici pro interní základní nástroje pro vyrovnávání zatížení.  Pomocí [protokolů Azure monitor](load-balancer-monitor-log.md) můžete kontrolovat stav testu veřejného nástroje pro vyrovnávání zatížení a počet testů. Protokolování se dá použít s Power BI nebo Azure Operational Insights k poskytnutí statistik o stavu nástroje pro vyrovnávání zatížení.

## <a name="limitations"></a>Omezení

- Testy HTTPS nepodporují vzájemné ověřování pomocí klientského certifikátu.
- Pokud jsou povolená časová razítka TCP, měli byste předpokládat, že testy stavu nebudou úspěšné.

## <a name="next-steps"></a>Další kroky

- Další informace o [Load Balanceru úrovně Standard](./load-balancer-overview.md)
- [Začínáme vytvářet veřejný Nástroj pro vyrovnávání zatížení v Správce prostředků pomocí prostředí PowerShell](quickstart-load-balancer-standard-public-powershell.md)
- [REST API pro sondy stavu](/rest/api/load-balancer/loadbalancerprobes/)
- Vyžádejte si nové možnosti sondy stavu s [Load Balancer UserVoice](https://aka.ms/lbuservoice)
