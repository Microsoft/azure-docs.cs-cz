---
title: Sondy stavu nástroje pro vyrovnávání zatížení používat k ochraně vaší služby | Dokumentace Microsoftu
description: Informace o používání sond stavu k monitorování instancí za nástroj pro vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2018
ms.author: kumud
ms.openlocfilehash: 58bc0c0669992b8b3884e24c39862f47412b9110
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584651"
---
# <a name="load-balancer-health-probes"></a>Sondy stavu nástroje pro vyrovnávání zatížení

Nástroj Azure Load Balancer používá k určení, která instance back-endový fond obdrží nové toky sondy stavu. Sondy stavu můžete použít ke zjištění chyby aplikace na instanci back-endu. Můžete také generovat vlastní odpověď na sondu stavu a použijte sondu stavu pro řízení toku a signál pro nástroj pro vyrovnávání zatížení, jestli se má pokračovat v odesílání nových toků nebo zastavit odesílání nových toků do instance back-endu. To je možné spravovat zatížení nebo plánované výpadky. Při selhání sondy stavu nástroje pro vyrovnávání zatížení zastaví odesílání nových toků do příslušné instance není v pořádku.

Typy sond stavu, které jsou k dispozici a způsob, jak se chovají sondy stavu závisí na které SKU nástroje pro vyrovnávání zatížení používá. Například chování nové i stávající toky závisí na tom, zda tok TCP nebo UDP jako i požadovanou SKU nástroje pro vyrovnávání zatížení, který používáte.

| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| [Typy testu](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Dolů chování pro zjišťování](#probedown) | Všechny testy, všechny toky TCP pokračovat. | Všechny testy dolů ukončit všechny toky TCP. | 

> [!IMPORTANT]
> Load Balancer pocházejí z IP adresy 168.63.129.16 sondy stavu a nesmí být blokovány pro testy k vyznačení vaší instance.  Kontrola [Zdrojová IP adresa pro zjišťování](#probesource) podrobnosti.

## <a name="types"></a>Typy testu

Sondy stavu můžete sledovat všechny port v back-end instance, včetně port, na němž je poskytována aktuální služby. Protokol testu stavu lze nakonfigurovat pro tři různé typy sond stavu:

- [Moduly pro naslouchání TCP](#tcpprobe)
- [Koncové body HTTP](#httpprobe)
- [Koncové body HTTPS](#httpsprobe)

Dostupné typy sondy stavu služby se liší v závislosti na vybrané SKU nástroje pro vyrovnávání zatížení:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardní SKU |    &#9989; |   &#9989; |   &#9989; |
| Základní SKU |   &#9989; |   &#9989; | &#10060; |

Pro vyrovnávání zatížení UDP, byste měli generovat vlastní stavu testu signál pro instance back-end pomocí TCP, HTTP, nebo sondu stavu protokolu HTTPS.

Při použití [pravidla Vyrovnávání zatížení pro porty s vysokou DOSTUPNOSTÍ](load-balancer-ha-ports-overview.md) s [Load balanceru úrovně Standard](load-balancer-standard-overview.md), jsou všechny porty s vyrovnáváním zatížení a odpověď sondy stavu jednoho musí odráží stav celou instanci.  

Měli není překladu adres nebo proxy server sondy prostřednictvím instance, která přijímá sondu stavu do jiné instance ve vaší virtuální síti, protože to může vést ke kaskádovým selháním ve vašem scénáři.

Pokud chcete otestovat selhání sondy stavu nebo označte dolů jednotlivé instance, můžete použít skupinu zabezpečení pro explicitní bloku sondu stavu (určení nebo [zdroj](#probesource)).

### <a name="tcpprobe"></a> Test protokolu TCP

Sondy protokolu TCP inicializovat připojení pomocí provádí trojcestných otevřít ověření TCP metodou handshake s definovaný port.  To je následována čtyř směrů zavřít ověření TCP metodou handshake.

Interval minimální testu je 5 sekund a minimální počet odpovědí na není v pořádku, je 2.  Celková doba trvání nesmí překročit 120 sekund.

Sondu protokolu TCP není úspěšné při:
* Naslouchací proces TCP na instanci během časového limitu nereaguje vůbec.  Test je označena jako na základě počtu selhání sondy požadavky, které byly nakonfigurovány přejít nezodpovězené před označením test mimo provoz.
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

Sondy protokolu HTTP a HTTPS navázání připojení TCP a se zadanou cestou vydat příkaz HTTP GET. Obě tyto sondy HTTP GET podporu relativní cesty. HTTPS testy jsou stejné jako sondy protokolu HTTP a uveďte (TLS, dřív označované jako SSL) Transport Layer Security obálky. Sonda stavu je označen, pokud odpoví instance se stavem HTTP 200 v časovém limitu.  Tyto stav sond pokusí zkontrolovat portu sondy stavu nakonfigurované ve výchozím nastavení každých 15 sekund. Interval minimální testu je 5 sekund. Celková doba trvání nesmí překročit 120 sekund. 

HTTP / HTTPS sondy může být také užitečné, pokud chcete implementovat vlastní logiku k odebrání instance oběhu nástroje pro vyrovnávání zatížení. Například můžete rozhodnout pro odebrání instance, pokud je vyšší než 90 % využití procesoru a vrátit stav 200 HTTP. 

Pokud používáte cloudové služby a webovými rolemi, které používají w3wp.exe, můžete také dosáhnout automatické monitorování vašeho webu. Chyby v kódu webu návratový stav než 200 pro test paměti nástroje pro vyrovnávání zatížení.  Sondu protokolu HTTP přepisuje výchozí kontroly agenta hosta. 

HTTP / HTTPS testu není úspěšné při:
* Koncový bod vrátí kód odpovědi HTTP než 200 (například 403, 404 nebo 500). Sonda stavu tato akce označí okamžitě. 
* Koncový bod během časového limitu 31 druhé nereaguje vůbec. V závislosti na tom, která je nastavena hodnota časového limitu žádosti více testu je možné dát nezodpovězené před sondy označeno jako neběží. (to znamená, že před SuccessFailCount testy jsou odesílány).
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

Role cloudové služby (role pracovního procesu a webové role) hostovaného agenta použít pro test monitorování ve výchozím nastavení.   Měli byste zvážit to možnost poslední možnost.  Vždy byste měli definovat sondy stavu explicitně s TCP nebo sondu protokolu HTTP. Test agenta hosta není co nejúčinnější explicitně definované sondy pro většinu scénářů aplikace.  

Test agenta hosta je kontrolu agent hosta ve virtuálním počítači. Potom naslouchá a jako odpověď vrátí odpověď HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno. (Ostatní stavy jsou zaneprázdněn recyklaci nebo ukončení).

Další informace najdete v tématu [konfigurace definiční soubor služby (csdef) pro sondy stavu](https://msdn.microsoft.com/library/azure/ee758710.aspx) nebo [Začínáme tím, že vytvoříte nástroj pro vyrovnávání zatížení veřejnou pro cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Pokud agent hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení označí instance jako reagovat. Poté se zastaví, odesílání toky do této instance. Nástroje pro vyrovnávání zatížení pokračuje v kontrole instance. 

Pokud agent hosta, který odpovídá zprávou HTTP 200, nástroje pro vyrovnávání zatížení odešle nové toky do této instance znovu.

Pokud používáte webovou roli, kód webu obvykle běží v w3wp.exe, který není monitorován pomocí Azure fabric nebo hostovaného agenta. Agent hosta nejsou hlášeny chyby v w3wp.exe (například odpovědi protokolu HTTP 500). Nástroje pro vyrovnávání zatížení v důsledku toho nepřijímá tuto instanci ze smyčky.

## <a name="probehealth"></a>Sondy stavu

Sondy stavu protokolu TCP, HTTP a HTTPS se považují za v pořádku a označit instanci role jako v pořádku v případě:

* Sonda stavu je úspěšný při prvním spuštění virtuálního počítače.
* Číslo SuccessFailCount (popsanou výš) definuje hodnotu úspěšné testy, které jsou nutné k označení instance role jako v pořádku. Pokud byla odebrána role instance, počet testy úspěšné, po sobě jdoucích musí být roven nebo překročit hodnotu SuccessFailCount k označení instance role jako spuštění.

> [!NOTE]
> Pokud kolísá stavu role instance, nástroje pro vyrovnávání zatížení čeká už předtím, než se uloží role instance je zpět do stavu v pořádku. Tato doba čekání navíc chrání uživatele a infrastruktury a jsou záměr zásady.

## <a name="probe-count-and-timeout"></a>Počet a časový limit pro zjišťování

Chování sondy závisí na:

* Počet úspěšné testy, které umožňují instance být označený jako zapnutý.
* Počet selhání sondy, které způsobují instance označit vypnuté.

Časový limit a četnost hodnoty nastavené v SuccessFailCount určit, zda je instance potvrzen spuštěný, nebo není spuštěna. Na webu Azure Portal je nastavit časový limit dvakrát na hodnotu četnosti.

Pravidla Vyrovnávání zatížení definoval sondu stavu jednoho příslušných back-endový fond.

## <a name="probedown"></a>Dolů chování pro zjišťování

### <a name="tcp-connections"></a>Připojení TCP

Nové připojení TCP se úspěšně back-end instance, která je v pořádku a má hostovaný operační systém a aplikace nemůže přijmout nový tok.

Pokud sonda stavu back-end instance selže, pokračovat navázané připojení TCP do této instance back-endu.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, žádné nové toky se odešlou do back-endový fond. Load balancer úrovně Standard vám umožní zavedené Velkoobjemové toky, abyste mohli pokračovat.  Load balancer úrovně Basic se ukončí všechny stávající toky TCP na back-endový fond.
 
Vzhledem k tomu, že tok je vždy mezi klientem a operačního systému hosta Virtuálního počítače, způsobí, že fond se všechny testy dolů front-endu, protože neexistuje žádná instance v dobrém stavu back-endu pro příjem toku nereaguje na otevřené pokusy o připojení TCP.

### <a name="udp-datagrams"></a>Datagramů UDP

Datagramy UDP bude doručen do instance back-end v pořádku.

Je přenos UDP a neexistuje žádný stav toku sledovány pro protokol UDP. Pokud sonda stavu všechny instance back-endu selže, může stávající toky UDP přesunout do jiné instance v pořádku v back-endový fond.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, stávající toky UDP se ukončí u úrovní Basic a Standard nástroje pro vyrovnávání zatížení.

## <a name="probesource"></a>Zdrojová IP adresa pro zjišťování

Nástroj pro vyrovnávání zatížení používá distribuované zjišťování služby pro jeho vnitřní stavů modelu. Každého hostitele, kde jsou umístěné virtuální počítače mohou být naprogramovány na generování sondy stavu služby podle konfigurace zákazníka. Provoz sondy stavu je přímo mezi součást infrastruktury, která generuje sondu stavu a zákazníkem virtuálních počítačů. Z dané IP adresy 168.63.129.16 jako jejich zdroje mají původ sondy stavu všechny nástroje pro vyrovnávání zatížení.  Když použijete vlastní IP adresy do virtuální sítě Azure, tuto IP adresu zdroje sondy stavu je musí být jedinečný, protože je globálně vyhrazené pro Microsoft.  Tato adresa je stejná ve všech oblastech a nemění. To by neměly být zahrnuté bezpečnostní riziko protože pouze interní platformy Azure mají možnost paketů z této IP adresy. 

Tuto IP adresu kromě sondy stavu nástroje pro vyrovnávání zatížení, použijte následující operace:

- Povolí agenta virtuálního počítače pro komunikaci s platformou, který signalizuje, že je ve stavu "Připraveno"
- Umožňuje komunikaci s virtuálním serverem DNS k překladu názvů filtrovaná pro zákazníky, kteří nemá definován vlastní servery DNS.  Toto filtrování se zajistí, že zákazníci můžou jenom překládat názvy hostitelů jejich nasazení.

Pro sondy stavu služby Vyrovnávání zatížení k označení instance, můžete **musí** povolit tuto IP adresu v Azure [skupiny zabezpečení](../virtual-network/security-overview.md) a zásady brány firewall na místní.

Pokud tuto IP adresu není povoleno v zásady brány firewall, sondy stavu se nezdaří, protože se nám kontaktovat vaši instanci.  Nástroj pro vyrovnávání zatížení pak označí dolů instanci z důvodu selhání sondy stavu.  To může způsobit selhání vaší služby s vyrovnáváním zatížení. 

Také byste neměli konfigurovat vaše virtuální síť s Microsoftem vlastní rozsah IP adres, který obsahuje adresy 168.63.129.16.  To bude kolidují s IP adresou sondy stavu.

Pokud máte více rozhraní na virtuálním počítači, musíte zajistit, že vám odpoví na test, který jste dostali v rozhraní.  To může vyžadovat jednoznačně zdroj NAT'ing tuto adresu ve virtuálním počítači, na základě za rozhraní.

## <a name="monitoring"></a>Monitorování

Veřejné a vnitřní [Load balanceru úrovně Standard](load-balancer-standard-overview.md) vystavit na koncový bod a back-end instance sondy stavu jako vícedimenzionálních metrik přes Azure Monitor. To může být potom používán jinými službami Azure nebo aplikace 3. stran. 

Veřejný Load balancer úrovně Basic poskytuje sondy stavu shrnuté na fond back-end pomocí Log Analytics.  Toto není k dispozici pro interní základní nástroje pro vyrovnávání zatížení.  Můžete použít [protokolu analytics](load-balancer-monitor-log.md) a zkontrolovat stav sondy stavu nástroje pro vyrovnávání veřejný nástroj pro zjišťování počtu. Protokolování je možné s Power BI nebo Azure Operational Insights k poskytování statistické údaje o stavu nástroje pro vyrovnávání zatížení.

## <a name="limitations"></a>Omezení

-  Sondy HTTPS nepodporují vzájemného ověřování pomocí certifikátu klienta.

## <a name="next-steps"></a>Další postup

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- [Začínáme s vytvářením nástroj pro vyrovnávání zatížení veřejnou v Resource Manageru pomocí prostředí PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [Rozhraní REST API pro sondy stavu](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Žádost o nové možnosti sondu stavu s [Uservoice pro vyrovnávání zatížení](https://aka.ms/lbuservoice)
