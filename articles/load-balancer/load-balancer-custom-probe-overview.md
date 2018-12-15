---
title: Použijte nástroj pro vyrovnávání zatížení Azure testy škálování a zajištění vysoké dostupnosti služby stavu
titlesuffix: Azure Load Balancer
description: Informace o používání sond stavu k monitorování instancí za nástroj pro vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2018
ms.author: kumud
ms.openlocfilehash: f9ada3518c4354b112e9e288da89ee9659629b1c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435119"
---
# <a name="load-balancer-health-probes"></a>Sondy stavu nástroje pro vyrovnávání zatížení

Nástroj Azure Load Balancer poskytuje sondy stavu pro použití s pravidla Vyrovnávání zatížení.  Konfigurace a kontroly odpovědím sondy stavu určit, která instance back-endový fond obdrží nové toky. Sondy stavu můžete použít ke zjištění chyby aplikace na instanci back-endu. Můžete také vytvořit vlastní odpověď na sondu stavu a použití sondu stavu pro řízení toku pro správu zatížení nebo plánovaný výpadek. Při selhání sondy stavu nástroje pro vyrovnávání zatížení zastaví odesílání nových toků do příslušné instance není v pořádku.

Sondy stavu služby podporují víc protokolů. Dostupnost určitého typu sondu stavu pro určitý protokol podporu se liší podle SKU nástroje pro vyrovnávání zatížení.  Kromě toho chování služby se liší podle SKU nástroje pro vyrovnávání zatížení.

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| [Typy testu](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Dolů chování pro zjišťování](#probedown) | Všechny testy, všechny toky TCP pokračovat. | Všechny testy dolů ukončit všechny toky TCP. | 

> [!IMPORTANT]
> Load Balancer pocházejí z IP adresy 168.63.129.16 sondy stavu a nesmí být blokovány pro testy označit navyšte kapacitu instance.  Kontrola [Zdrojová IP adresa pro zjišťování](#probesource) podrobnosti.

## <a name="types"></a>Typy testu

Sonda stavu lze nakonfigurovat pro naslouchací procesy pomocí těchto tří protokolů:

- [Moduly pro naslouchání TCP](#tcpprobe)
- [Koncové body HTTP](#httpprobe)
- [Koncové body HTTPS](#httpsprobe)

Dostupné typy sondy stavu služby se liší v závislosti na vybrané SKU nástroje pro vyrovnávání zatížení:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardní SKU |    &#9989; |   &#9989; |   &#9989; |
| Základní SKU |   &#9989; |   &#9989; | &#10060; |

Bez ohledu na to, který zvolíte typ prohledávání sondy stavu můžete sledovat všechny port v back-end instance, včetně port, na němž je poskytována aktuální služby.

### <a name="tcpprobe"></a> Test protokolu TCP

Sondy protokolu TCP inicializovat připojení pomocí provádí trojcestných otevřít ověření TCP metodou handshake s definovaný port.  Připojení se čtyř směrů zavřít ověření TCP metodou handshake ukončit sondy protokolu TCP.

Interval minimální testu je 5 sekund a minimální počet odpovědí na není v pořádku, je 2.  Celková doba trvání všechny intervalů o délce maximálně 120 sekund.

Sondu protokolu TCP není úspěšné při:
* Naslouchací proces TCP na instanci během časového limitu nereaguje vůbec.  Test je označena jako na základě počtu selhání sondy požadavky, které byly nakonfigurovány přejít nezodpovězené před označením dolů sondy.
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

Sondy protokolu HTTP a HTTPS stavět na sondu protokolu TCP a se zadanou cestou vydat příkaz HTTP GET. Obě tyto sondy HTTP GET podporu relativní cesty. HTTPS testy jsou stejné jako sondy protokolu HTTP a uveďte (TLS, dřív označované jako SSL) Transport Layer Security obálky. Sonda stavu je označen, pokud odpoví instance se stavem HTTP 200 v časovém limitu.  Sonda stavu se pokouší zkontrolovat portu sondy stavu nakonfigurované ve výchozím nastavení každých 15 sekund. Interval minimální testu je 5 sekund. Celková doba trvání všechny intervalů o délce maximálně 120 sekund.

HTTP / HTTPS sondy může být také užitečné, pokud chcete vyjádřit sondu stavu.  implementace vlastní logiky k odebrání instance z oběhu nástroje pro vyrovnávání zatížení, pokud je port testu je také naslouchacího procesu pro službu samotnou. Například můžete rozhodnout pro odebrání instance, pokud je vyšší než 90 % využití procesoru a vrátit stav 200 HTTP. 

Pokud používáte cloudové služby a webovými rolemi, které používají w3wp.exe, můžete také dosáhnout automatické monitorování vašeho webu. Chyby v kódu webu návratový stav než 200 pro test paměti nástroje pro vyrovnávání zatížení.

HTTP / HTTPS testu není úspěšné při:
* Koncový bod vrátí kód odpovědi HTTP než 200 (například 403, 404 nebo 500). Tato akce označí dolů sondu stavu ihned. 
* Koncový bod nereaguje vůbec během 31 sekundách časový limit. Test vícenásobných může přejít nezodpovězené před sondy označeno jako neběží a dokud nedosáhne součet všech časových limitů.
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

Role cloudové služby (role pracovního procesu a webové role) hostovaného agenta použít pro test monitorování ve výchozím nastavení.  Test agenta hosta je poslední možnost konfigurace.  Vždy použijte sondu stavu explicitně s TCP nebo sondu protokolu HTTP. Test agenta hosta není co nejúčinnější explicitně definované sondy pro většinu scénářů aplikace.

Test agenta hosta je kontrolu agent hosta ve virtuálním počítači. Potom naslouchá a jako odpověď vrátí odpověď HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno. (Ostatní stavy jsou zaneprázdněn recyklaci nebo ukončení).

Další informace najdete v tématu [konfigurace definiční soubor služby (csdef) pro sondy stavu](https://msdn.microsoft.com/library/azure/ee758710.aspx) nebo [Začínáme tím, že vytvoříte nástroj pro vyrovnávání zatížení veřejnou pro cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Pokud agent hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení označí instance jako reagovat. Poté se zastaví, odesílání toky do této instance. Nástroje pro vyrovnávání zatížení pokračuje v kontrole instance. 

Pokud agent hosta, který odpovídá zprávou HTTP 200, nástroje pro vyrovnávání zatížení odešle nové toky do této instance znovu.

Pokud používáte webovou roli, kód webu obvykle běží v w3wp.exe, který není monitorován pomocí Azure fabric nebo hostovaného agenta. Agent hosta nejsou hlášeny chyby v w3wp.exe (například odpovědi protokolu HTTP 500). Nástroje pro vyrovnávání zatížení v důsledku toho nepřijímá tuto instanci ze smyčky.

<a name="health"></a>
## <a name="probehealth"></a>Sondy stavu

Sondy stavu protokolu TCP, HTTP a HTTPS se považují za v pořádku a označit instanci role jako v pořádku v případě:

* Sonda stavu je jednou úspěšný po spuštění virtuálního počítače.
* Bylo dosaženo zadaný počet sond vyžaduje k označení instance role jako v pořádku.

> [!NOTE]
> Pokud kolísá sondu stavu nástroje pro vyrovnávání zatížení čeká už předtím, než se uloží role instance je zpět do stavu v pořádku. Tato doba čekání navíc chrání uživatele a infrastruktury a jsou záměr zásady.

## <a name="probe-count-and-timeout"></a>Počet a časový limit pro zjišťování

Chování sondy závisí na:

* Počet úspěšné testy, které umožňují instance být označený jako zapnutý.
* Počet selhání sondy, které způsobují instance označit vypnuté.

Hodnoty časového limitu a interval zadaný určit, zda instance je označen jako nahoru nebo dolů.

## <a name="probedown"></a>Dolů chování pro zjišťování

### <a name="tcp-connections"></a>Připojení TCP

Nové připojení TCP se úspěšně zbývající instance back-end v pořádku.

Pokud sonda stavu back-end instance selže, pokračovat navázané připojení TCP do této instance back-endu.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, žádné nové toky se odešlou do back-endový fond. Load balancer úrovně Standard vám umožní zavedené Velkoobjemové toky, abyste mohli pokračovat.  Load balancer úrovně Basic se ukončí všechny stávající toky TCP na back-endový fond.
 
Předávací služba (neukončí připojení protokolu TCP) je nástroj pro vyrovnávání zatížení a řízení toku je vždy mezi klientem a hostovaného operačního systému a aplikací Virtuálního počítače. Způsobí, že fond se všechny testy dolů front-endu nereaguje na připojení TCP pokusů (SYN) jako neexistuje žádná instance v dobrém stavu back-endu pro příjem toku a odpoví SYN – potvrzení

### <a name="udp-datagrams"></a>Datagramů UDP

Datagramy UDP bude doručen do instance back-end v pořádku.

Je přenos UDP a neexistuje žádný stav toku sledovány pro protokol UDP. Pokud sonda stavu všechny instance back-endu selže, může stávající toky UDP přesunout do jiné instance v pořádku v back-endový fond.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, stávající toky UDP se ukončí u úrovní Basic a Standard nástroje pro vyrovnávání zatížení.

<a name="source"></a>
## <a name="probesource"></a>Zdrojová IP adresa pro zjišťování

Nástroj pro vyrovnávání zatížení používá distribuované zjišťování služby pro jeho vnitřní stavů modelu. Zjišťování služby se nachází na každém hostiteli ve kterém virtuální počítače a může být naprogramovaných na vyžádání ke generování sondy stavu služby podle konfigurace zákazníka. Provoz sondy stavu je přímo mezi zjišťování služby, který generuje sondu stavu a zákazníkem virtuálních počítačů. Z dané IP adresy 168.63.129.16 jako jejich zdroje mají původ sondy stavu všechny nástroje pro vyrovnávání zatížení.  Můžete použít adresní prostor IP adres ve virtuální síti, která není definice RFC1918 místa.  Používáte globálně rezervovaná, Microsoft, který vlastní IP adresu snižuje pravděpodobnost konflikt IP adres s adresní prostor IP adres, které můžete použít uvnitř virtuální sítě.  Tato IP adresa je stejná ve všech oblastech a nezmění a není bezpečnostní riziko, protože pouze součást vnitřní platformy Azure mají možnost paketů z této IP adresy. 

Služby značka AzureLoadBalancer označuje tuto IP adresu zdroje v vaše [skupiny zabezpečení sítě](../virtual-network/security-overview.md) a povolí provoz sondy stavu ve výchozím nastavení.

Tuto IP adresu kromě sondy stavu nástroje pro vyrovnávání zatížení, použijte následující operace:

- Povolí agenta virtuálního počítače pro komunikaci s platformou, který signalizuje, že je ve stavu "Připraveno"
- Umožňuje komunikaci s virtuálním serverem DNS k překladu názvů filtrovaná pro zákazníky, kteří nemá definován vlastní servery DNS.  Toto filtrování se zajistí, že zákazníci můžou jenom překládat názvy hostitelů jejich nasazení.
- Umožňuje virtuálnímu počítači můžete získat dynamickou IP adresu ze služby DHCP v Azure.

## <a name="design"></a> Pokyny k návrhu

Sondy stavu se používají k Ujistěte se, vaši službu odolné a povolit její škálování. Chybné konfigurace nebo vzor chybný návrhu může mít vliv dostupnost a škálovatelnost služby. Zkontrolujte tato celý dokument a vezměte v úvahu co dopad na váš scénář je, když je tato odpověď testu označen nebo označený a jak to má vliv na dostupnost scénář aplikace.

Při návrhu modelu stavu pro vaši aplikaci, by měl probe port v back-end instance, který odráží stav dané instance __a__ aplikační služby, které poskytujete.  Port aplikace a portu sondy nemusí být stejné.  V některých případech může být žádoucí, aby je jiná než port, který vaše aplikace poskytuje službu na portu sondy.  

V některých případech může být užitečné pro vaši aplikaci k vygenerování odpověď sondy stavu nejen zjištění stavu vaší aplikace, ale také signál přímo do nástroje pro vyrovnávání zatížení, zda by měla přijímat nebo neinicializují, nepřijímaly nových toků vaší instance.  Můžete pracovat s odpověď testu, aby vaše aplikace můžete vytvořit protitlak a omezení doručování nových toků do instance služeb při selhání sondy stavu nebo připravit údržba vaší aplikace a spuštění vyprázdnění váš scénář.  Při použití Load balanceru úrovně Standard, [probe dolů](#probedown) signál vždycky povolí Velkoobjemové toky pokračovat až do nečinnosti časový limit připojení nebo uzavření. 

UDP Vyrovnávání zatížení, by měl generovat vlastní stavu testu signál z instance back-endu a používat cílení na odpovídající naslouchací proces TCP, HTTP nebo HTTPS sondu stavu tak, aby odrážely stavu aplikace UDP.

Při použití [pravidla Vyrovnávání zatížení porty s vysokou DOSTUPNOSTÍ](load-balancer-ha-ports-overview.md) s [Load balanceru úrovně Standard](load-balancer-standard-overview.md), jsou všechny porty s vyrovnáváním zatížení a odpověď sondy stavu jednoho musí odráží stav celou instanci.

Nepřekládat nebo sběru dat stavu proxy prostřednictvím instance, která přijímá sondu stavu do jiné instance ve vaší virtuální síti, protože tato konfigurace může vést ke kaskádovým selháním ve vašem scénáři.  Vezměte v úvahu následující scénář: sady zařízení třetích stran je nasazena v back-endový fond prostředků nástroje pro vyrovnávání zatížení a poskytuje škálovatelnost a redundanci pro zařízení a sondu stavu je nakonfigurován na portu sondy, které proxy zařízení třetích stran nebo přeloží do jiných virtuálních počítačů za zařízení.  Pokud jste pro zjišťování stejný port, který používáte pro převod nebo požadavky na proxy serveru k jiným virtuálním počítačům za zařízení, každá odpověď testu z jednoho virtuálního počítače za zařízení označí zařízení samotné neaktivní. Tato konfigurace může vést k selhání kaskádové scénář celá aplikace v důsledku jediného back-endu instance za zařízení.  Aktivační událost může být selhání přerušované test, který způsobí, že nástroj pro vyrovnávání zatížení k označení dolů původní cíl (instance zařízení) a pak můžete zakázat váš scénář celá aplikace. Místo toho testovat stav zařízení samotného. Výběr testu a určení stavu signálu je důležitým aspektem pro scénáře síťových virtuálních zařízení (NVA) a jste musí získáte od dodavatele aplikace pro odpovídající stavu signálu je pro takové scénáře.

Pokud není povoleno [Zdrojová IP adresa](#probesource) testu v zásady brány firewall, sondy stavu selže, protože se nám kontaktovat vaši instanci.  Nástroj pro vyrovnávání zatížení pak označí dolů instanci z důvodu selhání sondy stavu.  Tato chybná konfigurace může způsobit scénář aplikace s vyrovnáváním zatížení selhání.

Pro sondy stavu služby Vyrovnávání zatížení k označení navyšte kapacitu instance můžete **musí** povolit tuto IP adresu v Azure [skupiny zabezpečení sítě](../virtual-network/security-overview.md) a zásady brány firewall na místní.  Ve výchozím nastavení, zahrnuje každá skupina zabezpečení sítě [značka služby](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer tak, aby povolovala přenosy sonda stavu.

Pokud chcete otestovat selhání sondy stavu nebo označte dolů jednotlivé instance, můžete použít [skupiny zabezpečení sítě](../virtual-network/security-overview.md) na explicitní blokovat sondu stavu (cílový port nebo [Zdrojová IP adresa](#probesource)) a proveďte simulaci selhání testu.

Neprovádějte konfiguraci virtuální sítě s Microsoftem vlastní rozsah IP adres, který obsahuje adresy 168.63.129.16.  Tato konfigurace bude v konfliktu s IP adresou sondy stavu a může způsobit, že váš scénář selhání.

Pokud máte více rozhraní na virtuálním počítači, musíte zajistit, že vám odpoví na test, který jste dostali v rozhraní.  Možná budete muset zdrojovou síť přeložit tuto adresu ve virtuálním počítači, na základě za rozhraní.

Nepovolujte [časová razítka TCP](https://tools.ietf.org/html/rfc1323).  Povolení protokolu TCP časová razítka způsobí, že sond stavu selhání kvůli TCP paketů probíhá vyřazování podle Virtuálního počítače hosta zásobník protokolu TCP operačním systémem, jehož výsledkem je nástroj pro vyrovnávání zatížení označování dolů na příslušný koncový bod.  Ve výchozím nastavení zabezpečení jsou pravidelně povolená časová razítka TCP posílené Image virtuálního počítače a musí se zakázat.

## <a name="monitoring"></a>Monitorování

Veřejné a vnitřní [Load balanceru úrovně Standard](load-balancer-standard-overview.md) vystavit na koncový bod a back-end instance sondy stavu jako vícedimenzionálních metrik přes Azure Monitor. Tyto metriky můžou využívat další služby Azure nebo třetí arty aplikací. 

Veřejný Load balancer úrovně Basic poskytuje sondy stavu shrnuté na fond back-end pomocí Log Analytics.  Log Analytics nejsou k dispozici pro interní základní nástroje pro vyrovnávání zatížení.  Můžete použít [protokolu analytics](load-balancer-monitor-log.md) a zkontrolovat stav sondy stavu nástroje pro vyrovnávání veřejný nástroj pro zjišťování počtu. Protokolování je možné s Power BI nebo Azure Operational Insights k poskytování statistické údaje o stavu nástroje pro vyrovnávání zatížení.

## <a name="limitations"></a>Omezení

- Sondy HTTPS nepodporují vzájemného ověřování pomocí certifikátu klienta.
- Sondy stavu se nezdaří, pokud jsou povolené TCP časová razítka.

## <a name="next-steps"></a>Další postup

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- [Začínáme s vytvářením nástroj pro vyrovnávání zatížení veřejnou v Resource Manageru pomocí prostředí PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [Rozhraní REST API pro sondy stavu](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Žádost o nové možnosti sondu stavu s [Uservoice pro vyrovnávání zatížení](https://aka.ms/lbuservoice)
