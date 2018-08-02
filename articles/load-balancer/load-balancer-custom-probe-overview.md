---
title: Sondy stavu nástroje pro vyrovnávání zatížení používat k ochraně vaší služby | Dokumentace Microsoftu
description: Informace o používání sond stavu k monitorování instancí za nástroj pro vyrovnávání zatížení
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2018
ms.author: kumud
ms.openlocfilehash: 7366273e30132daf7dc5ea15072c574180d1bc8b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397276"
---
# <a name="load-balancer-health-probes"></a>Sondy stavu nástroje pro vyrovnávání zatížení

Nástroj Azure Load Balancer používá k určení, která instance back-endový fond obdrží nové toky sondy stavu. Sondy stavu můžete použít ke zjištění chyby aplikace na instanci back-endu. Můžete také generovat vlastní odpověď na sondu stavu a použijte sondu stavu pro řízení toku a signál pro nástroj pro vyrovnávání zatížení, jestli se má pokračovat v odesílání nových toků nebo zastavit odesílání nových toků do instance back-endu. To je možné spravovat zatížení nebo plánované výpadky.

Při selhání sondy stavu nástroje pro vyrovnávání zatížení zastaví odesílání nových toků do příslušné instance není v pořádku. Chování nové i stávající toky závisí na tom, zda tok TCP nebo UDP jako i požadovanou SKU nástroje pro vyrovnávání zatížení, který používáte.  Kontrola [sondy dolů chování podrobnosti](#probedown).

> [!IMPORTANT]
> Load Balancer pocházejí z IP adresy 168.63.129.16 sondy stavu a nesmí být blokovány pro testy k vyznačení vaší instance.  Kontrola [Zdrojová IP adresa pro zjišťování](#probesource) podrobnosti.

## <a name="health-probe-types"></a>Typy sondy stavu

Sondy stavu můžete sledovat všechny port v back-end instance, včetně port, na němž je poskytována aktuální služby. Sonda stavu podporuje TCP naslouchacích procesů nebo koncové body HTTP. 

Pro vyrovnávání zatížení UDP, byste měli generovat signál test vlastní stavu pro instance back-end pomocí TCP nebo HTTP sondu stavu.

Při použití [pravidla Vyrovnávání zatížení pro porty s vysokou DOSTUPNOSTÍ](load-balancer-ha-ports-overview.md) s [Load balanceru úrovně Standard](load-balancer-standard-overview.md), jsou všechny porty s vyrovnáváním zatížení a odpověď sondy stavu jednoho by měly odrážet stav celou instanci.  

Měli není překladu adres nebo proxy server sondy prostřednictvím instance, která přijímá sondu stavu do jiné instance ve vaší virtuální síti, protože to může vést ke kaskádovým selháním ve vašem scénáři.

Pokud chcete otestovat selhání sondy stavu nebo označte dolů jednotlivé instance, můžete použít skupinu zabezpečení pro explicitní bloku sondu stavu (určení nebo [zdroj](#probesource)).

### <a name="tcp-probe"></a>Test protokolu TCP

Sondy protokolu TCP inicializovat připojení pomocí provádí trojcestných otevřít ověření TCP metodou handshake s definovaný port.  To je následována čtyř směrů zavřít ověření TCP metodou handshake.

Interval minimální testu je 5 sekund a minimální počet odpovědí na není v pořádku, je 2.  Celková doba trvání nesmí překročit 120 sekund.

Sondu protokolu TCP není úspěšné při:
* Naslouchací proces TCP na instanci během časového limitu nereaguje vůbec.  Test je označena jako na základě počtu selhání sondy požadavky, které byly nakonfigurovány přejít nezodpovězené před označením test mimo provoz.
* Sonda obdrží TCP obnovit z instance.

### <a name="http-probe"></a>Test protokolu HTTP

Sondy protokolu HTTP navázání připojení TCP a se zadanou cestou vydat příkaz HTTP GET. Sondy protokolu HTTP podporují relativní cesty pro HTTP GET. Sonda stavu je označen, pokud odpoví instance se stavem HTTP 200 v časovém limitu.  HTTP pokus o stav sond ke kontrole portu sondy stavu nakonfigurované ve výchozím nastavení každých 15 sekund. Interval minimální testu je 5 sekund. Celková doba trvání nesmí překročit 120 sekund. 


Sondy protokolu HTTP může být také užitečné, pokud chcete implementovat vlastní logiku k odebrání instance oběhu nástroje pro vyrovnávání zatížení. Například můžete rozhodnout pro odebrání instance, pokud je vyšší než 90 % využití procesoru a vrátit stav 200 HTTP. 

Pokud používáte cloudové služby a webovými rolemi, které používají w3wp.exe, můžete také dosáhnout automatické monitorování vašeho webu. Chyby v kódu webu návratový stav než 200 pro test paměti nástroje pro vyrovnávání zatížení.  Sondu protokolu HTTP přepisuje výchozí kontroly agenta hosta. 

Sondy protokolu HTTP není úspěšné při:
* Koncový bod HTTP vrátí kód odpovědi HTTP než 200 (například 403, 404 nebo 500). Sonda stavu tato akce označí okamžitě. 
* Koncový bod HTTP nereaguje vůbec během do 31. druhý časový limit. V závislosti na tom, která je nastavena hodnota časového limitu žádosti více testu je možné dát nezodpovězené před sondy označeno jako neběží. (to znamená, že před SuccessFailCount testy jsou odesílány).
* Koncový bod HTTP uzavře připojení prostřednictvím protokolu TCP resetování.

### <a name="guest-agent-probe-classic-only"></a>Test agenta hosta (pouze Klasický model)

Role cloudové služby (role pracovního procesu a webové role) hostovaného agenta použít pro test monitorování ve výchozím nastavení.   Měli byste zvážit to možnost poslední možnost.  Vždy byste měli definovat sondy stavu explicitně s TCP nebo aplikace sondu protokolu HTTP. Test agenta hosta není co nejúčinnější explicitně definované sondy pro většinu scénářů aplikace.  

Test agenta hosta je kontrolu agent hosta ve virtuálním počítači. Potom naslouchá a jako odpověď vrátí odpověď HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno. (Ostatní stavy jsou zaneprázdněn recyklaci nebo ukončení).

Další informace najdete v tématu [konfigurace definiční soubor služby (csdef) pro sondy stavu](https://msdn.microsoft.com/library/azure/ee758710.aspx) nebo [Začínáme tím, že vytvoříte nástroj pro vyrovnávání zatížení veřejnou pro cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Pokud agent hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení označí instance jako reagovat. Poté se zastaví, odesílání toky do této instance. Nástroje pro vyrovnávání zatížení pokračuje v kontrole instance. 

Pokud agent hosta, který odpovídá zprávou HTTP 200, nástroje pro vyrovnávání zatížení odešle nové toky do této instance znovu.

Pokud používáte webovou roli, kód webu obvykle běží v w3wp.exe, který není monitorován pomocí Azure fabric nebo hostovaného agenta. Agent hosta nejsou hlášeny chyby v w3wp.exe (například odpovědi protokolu HTTP 500). Nástroje pro vyrovnávání zatížení v důsledku toho nepřijímá tuto instanci ze smyčky.

## <a name="probe-health"></a>Sondy stavu

Sondy stavu protokolu TCP nebo HTTP jsou považovány za v pořádku a označit instanci role jako v pořádku v případě:

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

Nové připojení TCP se úspěšně instance back-end, který je v pořádku a má hostovaný operační systém a aplikace nemůže přijmout nový tok.

Pokud sonda stavu back-end instance selže, pokračovat navázané připojení TCP do této instance back-endu.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, žádné nové toky se odešlou do back-endový fond. Load balancer úrovně Standard vám umožní zavedené Velkoobjemové toky, abyste mohli pokračovat.  Load balancer úrovně Basic se ukončí všechny existující Velkoobjemové toky na back-endový fond.
 
Vzhledem k tomu, že tok je vždy mezi klientem a operačního systému hosta Virtuálního počítače, způsobí, že fond se všechny testy dolů front-endu, protože neexistuje žádná instance v dobrém stavu back-endu pro příjem toku nereaguje na otevřené pokusy o připojení TCP.

### <a name="udp-datagrams"></a>Datagramů UDP

Datagramy UDP bude doručen do instance back-end v pořádku.

Je přenos UDP a neexistuje žádný stav toku sledovány pro protokol UDP. Pokud sonda stavu všechny instance back-endu selže, může stávající toky UDP přesunout do jiné instance v pořádku v back-endový fond.

Pokud selžou i všechny testy v rámci všech instancí ve fondu back-endu, stávající toky UDP se ukončí u úrovní Basic a Standard nástroje pro vyrovnávání zatížení.


## <a name="probesource"></a>Zdrojová IP adresa pro zjišťování

Z dané IP adresy 168.63.129.16 jako jejich zdroje mají původ sondy stavu všechny nástroje pro vyrovnávání zatížení.  Když použijete vlastní IP adresy do virtuální sítě Azure, tuto IP adresu zdroje sondy stavu je musí být jedinečný, protože je globálně vyhrazené pro Microsoft.  Tato adresa je stejná ve všech oblastech a nemění. To by neměly být zahrnuté bezpečnostní riziko protože pouze interní platformy Azure mají možnost paketů z této IP adresy. 

Pro sondy stavu služby Vyrovnávání zatížení k označení instance, můžete **musí** povolit tuto IP adresu v Azure [skupiny zabezpečení](../virtual-network/security-overview.md) a zásady brány firewall na místní.

Pokud tuto IP adresu není povoleno v zásady brány firewall, sondy stavu se nezdaří, protože se nám kontaktovat vaši instanci.  Nástroj pro vyrovnávání zatížení pak označí dolů instanci z důvodu selhání sondy stavu.  To může způsobit selhání vaší služby s vyrovnáváním zatížení. 

Také byste neměli konfigurovat vaše virtuální síť s Microsoftem vlastní rozsah IP adres, který obsahuje adresy 168.63.129.16.  To bude kolidují s IP adresou sondy stavu.

Pokud máte více rozhraní na virtuálním počítači, musíte zajistit, že vám odpoví na test, který jste dostali v rozhraní.  To může vyžadovat jednoznačně zdroj NAT'ing tuto adresu ve virtuálním počítači, na základě za rozhraní.

## <a name="monitoring"></a>Monitorování

Všechny [Load balanceru úrovně Standard](load-balancer-standard-overview.md) jako s multidimenzionálním metriky na instanci prostřednictvím služby Azure Monitor zpřístupňuje sonda stavu.

Load balancer úrovně Basic poskytuje sondy stavu na fond back-end pomocí Log Analytics.  Toto je pouze pro veřejné základní nástroje pro vyrovnávání zatížení k dispozici a není k dispozici pro interní základní nástroje pro vyrovnávání zatížení.  Můžete použít [protokolu analytics](load-balancer-monitor-log.md) a zkontrolovat stav sondy stavu nástroje pro vyrovnávání veřejný nástroj pro zjišťování počtu. Protokolování je možné s Power BI nebo Azure Operational Insights k poskytování statistické údaje o stavu nástroje pro vyrovnávání zatížení.


## <a name="limitations"></a>Omezení

-  Sondu stavu protokolu HTTP nepodporuje protokol TLS (HTTPS).  Místo toho použijte sondu protokolu TCP na portu 443.

## <a name="next-steps"></a>Další postup

- [Začínáme s vytvářením nástroj pro vyrovnávání zatížení veřejnou v Resource Manageru pomocí prostředí PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [Rozhraní REST API pro sondy stavu](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

