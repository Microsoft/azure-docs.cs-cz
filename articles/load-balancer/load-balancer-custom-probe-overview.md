---
title: Zdravotní sondy pro škálování a poskytování HA pro vaše služby
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak pomocí sond stavu sledovat instance za Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: ec1507e09a183f8d466a456b70151861f5f0e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159434"
---
# <a name="load-balancer-health-probes"></a>Sondy stavu Load Balanceru

Při použití pravidel vyrovnávání zatížení s Azure Load Balancer, musíte zadat sondy stavu, aby nástroj pro vyrovnávání zatížení pro detekci stavu koncového bodu back-endu.  Konfigurace sondy stavu a odpovědi sondy určují, které instance back-endového fondu obdrží nové toky. Sondy stavu můžete použít ke zjištění selhání aplikace v koncovém bodě back-endu. Můžete také generovat vlastní odpověď na sondu stavu a použít sondu stavu pro řízení toku pro správu zatížení nebo plánované prostoje. Pokud se nezdaří sonda stavu, vyrovnávání zatížení přestane odesílat nové toky do příslušné instance není v pořádku. Odchozí připojení není ovlivněno, je ovlivněno pouze příchozí připojení.

Sondy stavu podporují více protokolů. Dostupnost protokolu konkrétní sondy stavu se liší podle skladové položky vykladače zatížení.  Chování služby se navíc liší podle skladové položky vykladače zatížení, jak je znázorněno v této tabulce:

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| [Typy sond](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Chování sondy dolů](#probedown) | Všechny sondy dolů, všechny toky TCP pokračovat. | Všechny sondy dolů, všechny toky TCP vyprší. | 


>[!IMPORTANT]
>Zkontrolujte tento dokument v plném rozsahu, včetně důležitých [pokynů k návrhu](#design) níže vytvořit spolehlivou službu.

>[!IMPORTANT]
>Sondy stavu vykladače zatížení pocházejí z ADRESY IP 168.63.129.16 a nesmí být blokovány pro sondy k označení instance.  Podrobnosti naleznete v části Podrobnosti o [zdrojové adrese IP](#probesource) sondy.

>[!IMPORTANT]
>Bez ohledu na nakonfigurovanou prahovou hodnotu časového limitu budou sondy stavu nástroj ové bilance zatížení HTTP(S) automaticky sondovat instanci, pokud server vrátí stavový kód, který není HTTP 200 OK nebo pokud je připojení ukončeno pomocí resetování protokolu TCP.

## <a name="probe-configuration"></a><a name="probes"></a>Konfigurace sondy

Konfigurace sondy stavu se skládá z následujících prvků:

- Doba trvání intervalu mezi jednotlivými sondami
- Počet odpovědí sondy, které musí být pozorovány před přechodem sondy do jiného stavu
- Protokol sondy
- Přístav sondy
- Cesta HTTP pro HTTP GET při použití sond HTTP(S)

>[!NOTE]
>Definice sondy není povinná nebo zkontrolováná při použití Azure PowerShellu, Azure CLI, Šablon nebo ROZHRANÍ API. Testy ověření testu se provádějí jenom při použití portálu Azure Portal.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Pochopení aplikačního signálu, detekce signálu a reakce platformy

Počet odpovědí na sondu se vztahuje jak na

- počet úspěšných sond, které umožňují označit instanci jako nahoru, a
- počet časově vypočitatých sond, které způsobí, že instance bude označena jako dolů.

Zadané hodnoty časového času a intervalu určují, zda bude instance označena jako nahoru nebo dolů.  Doba trvání intervalu vynásobená počtem odpovědí na sondu určuje dobu, po kterou musí být odezvy sondy detekovány.  A služba bude reagovat po dosažení požadovaných sond.

Můžeme ilustrovat chování dále s příkladem. Pokud jste nastavili počet odpovědí sondy na 2 a interval na 5 sekund, znamená to, že 2 poruchy časového intervalu sondy musí být dodrženy v intervalu 10 sekund.  Vzhledem k tomu, že čas, kdy je odeslána sonda není synchronizována, když aplikace může změnit stav, můžeme vázaný čas pro detekci dvěma scénáři:

1. Pokud vaše aplikace začne produkovat odezvu časového intervalu sondy těsně před příchodem první sondy, detekce těchto událostí bude trvat 10 sekund (intervaly 2 x 5 sekund) plus doba trvání aplikace, která začíná signalizovat časový interval, kdy první sonda dorazila.  Můžete předpokládat, že tato detekce trvat mírně přes 10 sekund.
2. Pokud vaše aplikace začne produkovat odpověď časového intervalu sondy těsně po příchodu první sondy, detekce těchto událostí nezačne, dokud nedorazí další sonda (a časový interval) plus dalších 10 sekund (intervaly 2 x 5 sekund).  Můžete předpokládat, že tato detekce trvá necelých 15 sekund.

V tomto příkladu po detekci bude platforma trvat malé množství času reagovat na tuto změnu.  To znamená, že v závislosti na 

1. když aplikace začne měnit stav a
2. pokud je tato změna zjištěna a splněna požadovaná kritéria (počet sond odeslaných v určeném intervalu) a
3. pokud byla detekce sdělena napříč platformou 

můžete předpokládat, že reakce na odezvu časové sondy bude trvat minimálně něco málo přes 10 sekund a maximálně mírně přes 15 sekund, aby reagovala na změnu signálu z aplikace.  Tento příklad je uveden pro ilustraci toho, co se děje, ale není možné předpovědět přesnou dobu trvání nad rámec výše uvedených hrubých pokynů uvedených v tomto příkladu.
 
## <a name="probe-types"></a><a name="types"></a>Typy sond

Protokol používaný sondou stavu lze nakonfigurovat na jednu z následujících možností:

- [Naslouchací procesy Protokolu TCP](#tcpprobe)
- [Koncové body HTTP](#httpprobe)
- [Koncové body HTTPS](#httpsprobe)

Dostupné protokoly závisí na použité skladové jednotce pro vyrovnávání zatížení:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardní SKU |    &#9989; |   &#9989; |   &#9989; |
| Základní SKU |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>Sonda TCP

Sondy TCP inicializovat připojení provedením třícestný otevřený tcp handshake s definovaným portem.  Sondy TCP ukončit připojení s čtyři-cesta zavřít TCP handshake.

Minimální interval sondy je 5 sekund a minimální počet nefunkčních odpovědí je 2.  Celková doba trvání všech intervalů nesmí přesáhnout 120 sekund.

Sonda TCP se nezdaří, pokud:
* Naslouchací proces TCP na instanci nereaguje vůbec během časového omezení.  Sonda je označena na základě počtu časovaných požadavků na sondu, které byly nakonfigurovány tak, aby zůstaly nezodpovězeny před označením sondy.
* Sonda obdrží tcp reset z instance.

Následující text ukazuje, jak můžete vyjádřit tento druh konfigurace sondy v šabloně Správce prostředků:

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

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a> HTTP / HTTPS sonda

>[!NOTE]
>Sonda HTTPS je k dispozici pouze pro [standardní vytápěč zatížení](load-balancer-standard-overview.md).

Http a HTTPS sondy sestavení na sondě TCP a vydat HTTP GET se zadanou cestou. Obě tyto sondy podporují relativní cesty pro HTTP GET. Https sondy jsou stejné jako http sondy s přidáním zabezpečení transportní vrstvy (TLS, dříve známý jako SSL) obálky. Sonda stavu je označena, když instance odpoví stavem HTTP 200 v časovém období.  Sonda stavu se pokusí zkontrolovat nakonfigurovaný port sondy stavu každých 15 sekund ve výchozím nastavení. Minimální interval sondy je 5 sekund. Celková doba trvání všech intervalů nesmí přesáhnout 120 sekund.

HTTP / HTTPS sondy může být také užitečné k implementaci vlastní logiku odebrat instance z nástroje pro vyrovnávání zatížení otočení, pokud je port sondy je také naslouchací proces pro samotnou službu. Můžete se například rozhodnout odebrat instanci, pokud je vyšší než 90 % procesoru a vrátit stav HTTP bez 200. 

> [!NOTE] 
> Sonda HTTPS vyžaduje použití certifikátů založených na tom, které mají minimální hodnotu hash podpisu SHA256 v celém řetězci.

Pokud používáte cloudové služby a máte webové role, které používají w3wp.exe, dosáhnete také automatického sledování vašeho webu. Chyby v kódu webu vrátí stav bez 200 sondě nástrojpro vyrovnávání zatížení.

Sonda HTTP / HTTPS se nezdaří, pokud:
* Koncový bod probe vrátí jiný kód odpovědi HTTP než 200 (například 403, 404 nebo 500). To bude označit dolů zdravotní sondu okamžitě. 
* Koncový bod sondy nereaguje vůbec během minimálního intervalu sondy a časového intervalu 30 sekund. Více požadavků na sondu může přejít nezodpovězené před sonda získá označenjako není spuštěna a dokud není dosaženo součtu všech intervalů časového času.
* Koncový bod sondy ukončí připojení pomocí resetování protokolu TCP.

Následující text ukazuje, jak můžete vyjádřit tento druh konfigurace sondy v šabloně Správce prostředků:

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

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Sonda agenta hosta (pouze klasické)

Role cloudové služby (role pracovních procesů a webové role) ve výchozím nastavení používají agenta hosta pro monitorování sondy.  Sonda agenta hosta je konfigurace poslední instance.  Vždy používejte sondu stavu explicitně se sondou TCP nebo HTTP. Sonda agenta hosta není tak účinná jako explicitně definované sondy pro většinu scénářů aplikace.

Sonda agenta hosta je kontrola agenta hosta uvnitř virtuálního soudu. Potom naslouchá a reaguje s odpovědí HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno. (Ostatní stavy jsou zaneprázdněné, recyklační nebo zastavující.)

Další informace naleznete [v tématu Konfigurace souboru definice služby (csdef) pro sondy stavu](https://msdn.microsoft.com/library/azure/ee758710.aspx) nebo [Začínáme vytvořením veřejného zařízení pro vyrovnávání zatížení pro cloudové služby](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Pokud agent hosta neodpoví pomocí protokolu HTTP 200 OK, nástroj pro vyrovnávání zatížení označí instanci jako nereagující. Potom zastaví odesílání toků do této instance. Vyrovnávání zatížení pokračuje v kontrole instance. 

Pokud agent hosta odpoví pomocí protokolu HTTP 200, nástroj pro vyrovnávání zatížení znovu odešle nové toky do této instance.

Při použití webové role, kód webu obvykle běží v w3wp.exe, který není sledován prostředků azure fabric nebo host agent. Chyby v w3wp.exe (například odpovědi HTTP 500) nejsou hlášeny agentovi hosta. V důsledku toho vykladač zatížení nebere tuto instanci z rotace.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Sonda chování

Sondy stavu TCP, HTTP a HTTPS jsou považovány za v pořádku a označí koncový bod back-endu jako v pořádku, když:

* Sonda stavu je úspěšná jednou po spuštění virtuálního počítače.
* Byl dosažen zadaný počet sond, které jsou nutné k označení koncového bodu back-endu jako v pořádku.

Jakýkoli koncový bod back-endu, který dosáhl stavu v pořádku, je způsobilý pro příjem nových toků.  

> [!NOTE]
> Pokud sonda stavu kolísá, vyrovnávání zatížení čeká déle, než vrátí koncový bod back-endu zpět do stavu v pořádku. Tato extra čekací doba chrání uživatele a infrastrukturu a je záměrné zásady.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Chování sondy dolů

### <a name="tcp-connections"></a>Připojení TCP

Nová připojení TCP se podaří zbývající v pořádku koncový bod back-endu.

Pokud se nezdaří sonda stavu koncového bodu back-endu, navázání připojení TCP k tomuto koncovému bodu back-endu pokračovat.

Pokud všechny sondy pro všechny instance v back-endovém fondu nezdaří, žádné nové toky budou odeslány do back-endového fondu. Standardní vyrovnávání zatížení umožní, aby zavedené toky TCP pokračovaly.  Základní vyrovnávání zatížení ukončí všechny existující toky TCP do back-endového fondu.
 
Vyrovnávání zatížení je projíždějící služba (neukončuje připojení TCP) a tok je vždy mezi klientem a hostovaného operačního serveru virtuálního zařízení a aplikací. Fond se všemi sondami dolů způsobí front-end nereaguje na pokusy o otevření připojení TCP (SYN), protože neexistuje žádný koncový bod back-endu v pořádku přijímat tok a reagovat syn-ack.

### <a name="udp-datagrams"></a>Datagramy UDP

Datagramy UDP budou doručeny do koncových bodů back-endu v pořádku.

UDP je bez připojení a neexistuje žádný stav toku sledovánpro UDP. Pokud se nezdaří sonda stavu koncového bodu back-endu, existující toky UDP se přesunou do jiné instance v pořádku v back-endovém fondu.

Pokud všechny sondy pro všechny instance v back-endovém fondu nezdaří, existující toky UDP bude ukončena pro základní a standardní vyrovnávání zatížení.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Zdrojová IP adresa sondy

Vykladač zatížení používá distribuované sondování služby pro svůj model vnitřního stavu. Služba zjišťování je umístěna na každém hostiteli, kde jsou virtuální počítače a lze je naprogramovat na vyžádání tak, aby generovaly sondy stavu podle konfigurace zákazníka. Provoz sondy stavu je přímo mezi sondovací služby, která generuje sondu stavu a virtuálního provozu zákazníka. Všechny sondy stavu vyrovnávání zatížení pocházejí z IP adresy 168.63.129.16 jako jejich zdroj.  Adresní prostor IP můžete použít uvnitř virtuální sítě, která není rfc1918 prostor.  Pomocí globálně vyhrazené ip adresy vlastněné společností Microsoft se snižuje pravděpodobnost konfliktu ip adresy s adresním prostorem IP, který používáte ve virtuální síti.  Tato adresa IP je stejná ve všech oblastech a nemění se a není bezpečnostním rizikem, protože pouze interní součást platformy Azure může získat paket z této ip adresy. 

Značka služby AzureLoadBalancer identifikuje tuto zdrojovou IP adresu ve [skupinách zabezpečení sítě](../virtual-network/security-overview.md) a ve výchozím nastavení povoluje provoz sondy stavu.

Kromě sond stavu nástroje pro vyrovnávání zatížení [používají tuto adresu IP následující operace](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Umožňuje agentovi virtuálního zařízení komunikovat s platformou, aby signalizoval, že je ve stavu "Připraveno".
- Umožňuje komunikaci s virtuálním serverem DNS poskytovat filtrované překlad názvů zákazníkům, kteří nedefinují vlastní servery DNS.  Toto filtrování zajišťuje, že zákazníci mohou vyřešit pouze názvy hostitelů jejich nasazení.
- Umožňuje virtuálnímu počítači získat dynamickou IP adresu ze služby DHCP v Azure.

## <a name="design-guidance"></a><a name="design"></a>Pokyny k návrhu

Sondy stavu se používají k tomu, aby vaše služba byla odolná a umožnila škálování. Nesprávnou konfigurací nebo chybný návrhový vzor může mít vliv na dostupnost a škálovatelnost vaší služby. Zkontrolujte celý tento dokument a zvažte, jaký dopad má váš scénář, když je tato odpověď sondy označena dolů nebo označena a jak ovlivňuje dostupnost scénáře aplikace.

Při návrhu modelu stavu pro vaši aplikaci, měli byste sondovat port na koncový bod back-endu, který odráží stav této instance __a__ aplikační služby, které poskytujete.  Port aplikace a port sondy nemusí být stejné.  V některých scénářích může být žádoucí, aby se port sondy lišil od portu, na který aplikace poskytuje službu.  

Někdy může být užitečné pro vaši aplikaci generovat odpověď sondy stavu nejen zjistit stav aplikace, ale také signál přímo na vyrovnávání zatížení, zda vaše instance by měla přijímat nebo ne přijímat nové toky.  Můžete manipulovat odezvu sondy povolit vaší aplikaci vytvořit protitlak a omezení doručování nových toků do instance selháním sondou stavu nebo připravit na údržbu aplikace a zahájit vyprázdnění vašeho scénáře.  Při použití standardního vytápěče zatížení signál [uzávěrky sondy](#probedown) vždy umožní, aby toky Protokolu TCP pokračovaly až do časového limitu nečinnosti nebo ukončení připojení. 

Pro vyrovnávání zatížení UDP byste měli vygenerovat vlastní signál sondy stavu z koncového bodu back-endu a použít sondu stavu TCP, HTTP nebo HTTPS, která cílí na odpovídající naslouchací proces, aby odrážela stav aplikace UDP.

Při použití [pravidel vyrovnávání zatížení portů HA](load-balancer-ha-ports-overview.md) se [standardním nástrojem pro vyrovnávání zatížení](load-balancer-standard-overview.md)jsou všechny porty vyrovnány zatížením a odpověď jedné sondy stavu musí odrážet stav celé instance.

Nepřekládejte nebo proxy sondu stavu prostřednictvím instance, která přijímá sondu stavu do jiné instance ve vaší virtuální síti, protože tato konfigurace může vést k kaskádové selhání ve vašem scénáři.  Zvažte následující scénář: sada zařízení jiných výrobců je nasazena v back-endovém fondu prostředku nástroje pro vyrovnávání zatížení, aby poskytovala škálování a redundanci pro zařízení a sonda stavu je nakonfigurována tak, aby zkoumala port, který proxy zařízení jiného výrobce nebo překládá do jiných virtuálních počítačů za zařízením.  Pokud sondujete stejný port, který používáte k překladu nebo proxy požadavků na jiné virtuální počítače za zařízením, jakákoli odpověď sondy z jednoho virtuálního počítače za zařízením označí samotné zařízení jako mrtvé. Tato konfigurace může vést k kaskádové selhání celého scénáře aplikace v důsledku jednoho koncového bodu back-endu za zařízením.  Aktivační událost může být občasné selhání sondy, která způsobí, že balancer zatížení označit původní cíl (instance zařízení) a zase můžete zakázat celý scénář aplikace. Místo toho sondujte stav samotného přístroje. Výběr sondy k určení signálu stavu je důležitým aspektem pro scénáře síťových virtuálních zařízení (NVA) a je nutné konzultovat dodavatele aplikace, jaký je vhodný signál stavu pro takové scénáře.

Pokud nepovolíte [zdrojovou IP adresu](#probesource) sondy v zásadách brány firewall, sonda stavu se nezdaří, protože není schopna dosáhnout vaší instance.  Na druhé straně balancer označí vaši instanci z důvodu selhání sondy stavu.  Tato chybná konfigurace může způsobit selhání scénáře aplikace s vyrovnáváním zatížení.

Pro sondu stavu vyrovnávání zatížení k označení instance, **musíte** povolit tuto IP adresu ve všech [skupinách zabezpečení sítě](../virtual-network/security-overview.md) Azure a místní firewall zásady.  Ve výchozím nastavení každá skupina zabezpečení sítě zahrnuje [značku služby](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer povolit provoz sondy stavu.

Pokud chcete otestovat selhání sondy stavu nebo označit jednotlivé instance, můžete použít [skupiny zabezpečení sítě](../virtual-network/security-overview.md) explicitně blokovat sondu stavu (cílový port nebo [zdrojIP)](#probesource)a simulovat selhání sondy.

Nekonfigurujte virtuální síť s rozsahem IP adres vlastněným společností Microsoft, který obsahuje 168.63.129.16.  Tyto konfigurace se srazí s IP adresou sondy stavu a může způsobit selhání vašeho scénáře.

Pokud máte více rozhraní na vašem virtuálním počítači, musíte se ujistit, že reagovat na sondu na rozhraní, které jste obdrželi na.  Možná budete muset zdroj síťové adresy přeložit tuto adresu ve virtuálním počítači na základě rozhraní.

Nepovolujte [časová razítka Protokolu TCP](https://tools.ietf.org/html/rfc1323).  Povolení časových razítek TCP může způsobit selhání sond splatnosti sond y TCP z důvodu vynechání paketů TCP v zásobníku TCP hostovaného operačního systému, což vede k označení nástroje pro vyrovnávání zatížení příslušného koncového bodu.  Časová razítka TCP jsou běžně povolena ve výchozím nastavení na obrázcích virtuálních počítačích v rámci zabezpečení a musí být zakázána.

## <a name="monitoring"></a>Monitorování

Veřejný i interní [standardní nástroj pro vyrovnávání zatížení](load-balancer-standard-overview.md) zveřejňují stav sondy stavu koncového a koncového bodu na koncový bod jako vícerozměrné metriky prostřednictvím Azure Monitoru. Tyto metriky můžou spotřebovat jiné služby Azure nebo partnerské aplikace. 

Základní veřejný nástroj pro vyrovnávání zatížení zveřejňuje stav sondy stavu shrnuté na back-endového fondu prostřednictvím protokolů Azure Monitor.  Protokoly Azure Monitor nejsou k dispozici pro interní základní vyrovnávání zatížení.  Protokoly [Azure Monitor umíte](load-balancer-monitor-log.md) ke kontrole stavu a počtu sond y pro vyrovnávání veřejného zatížení. Protokolování se dá použít s Power BI nebo Azure Operational Insights k poskytování statistik o stavu nástrojpro vyrovnávání zatížení.

## <a name="limitations"></a>Omezení

- Https sondy nepodporují vzájemné ověřování s klientským certifikátem.
- Měli byste předpokládat, že sondy stavu se nezdaří, pokud jsou povolena časová razítka TCP.

## <a name="next-steps"></a>Další kroky

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- [Začínáme vytvářet veřejný nástroje pro vyrovnávání zatížení ve Správci prostředků pomocí PowerShellu](quickstart-create-standard-load-balancer-powershell.md)
- [ROZHRANÍ REST API pro sondy stavu](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Vyžádejte si nové schopnosti zdravotní sondy pomocí [uservoice nástrojového vykladače zatížení](https://aka.ms/lbuservoice)
