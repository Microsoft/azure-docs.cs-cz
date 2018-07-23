---
title: Monitorování stavu pomocí vlastní sondy nástroje pro vyrovnávání zatížení | Dokumentace Microsoftu
description: Zjistěte, jak použít vlastní sondy pro Azure Load Balancer k monitorování instancí za nástroj pro vyrovnávání zatížení
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
ms.date: 07/20/2018
ms.author: kumud
ms.openlocfilehash: 8d354e3f409a51bdbb03ad340c951c39cc6137e1
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186439"
---
# <a name="understand-load-balancer-probes"></a>Porozumění testům nástroje pro vyrovnávání zatížení

Nástroj Azure Load Balancer používá k určení, která instance back-endový fond obdrželi nové toky sondy stavu.   Sondy stavu můžete použít ke zjištění chyby aplikace na instanci back-endu.  Odpověď sondy stavu ze své aplikace můžete použít také na signál pro Load Balancer, jestli se má pokračovat v odesílání nových toků nebo zastavit odesílání nových toků do instance back-end pro správu zatížení nebo plánované výpadky.

Sondy stavu služby se řídí, zda jsou vytvořeny nové toky v dobrém stavu back-endových instancí. Při selhání sondy stavu nástroje pro vyrovnávání zatížení zastaví odesílání nových toků do příslušné instance není v pořádku.  Navázané připojení TCP pokračovat po selhání sondy stavu.  Stávající toky UDP se přesune z instance není v pořádku, k jiné instanci v pořádku v back-endový fond.

Pokud selžou i všechny testy pro back-endový fond, základní nástroje pro vyrovnávání zatížení se ukončí všechny existující Velkoobjemové toky na back-endového fondu, zatímco Load balancer úrovně Standard vám umožní zavedené Velkoobjemové toky pokračovat; žádné nové toky se odešlou do back-endový fond.  Všechny stávající toky UDP se ukončí u úrovní Basic a Standard nástroje pro vyrovnávání zatížení, pokud všechny testy pro back-end fondu selhání.  Je přenos UDP a neexistuje žádný stav toku sledovány pro protokol UDP.  Za předpokladu, algoritmu hash vytváří stejný výsledek, tok datagramy zůstane na konkrétní instanci.  Změnu sondu stavu v back-endový fond může nové datagramy přesunout do jiné instance v back-endový fond.

Role cloudové služby (role pracovního procesu a webové role) hostovaného agenta použít pro test monitorování. Při použití cloudových služeb s virtuálními počítači IaaS za nástrojem pro vyrovnávání zatížení musí být nakonfigurovaný protokol TCP nebo HTTP vlastních testů stavu paměti.

## <a name="understand-probe-count-and-timeout"></a>Počet testu a vypršení časového limitu

Chování sondy závisí na:

* Počet úspěšné testy, které umožňují instance označeny jako provoz.
* Počet neúspěšných testů, které způsobují instance popisky vypnuté.

Časový limit a četnost hodnoty nastavené v SuccessFailCount určit, zda je instance potvrzen spuštěný, nebo není spuštěna. Na webu Azure Portal je nastavit časový limit dvakrát na hodnotu četnosti.

Konfigurace testu všech instancí s vyrovnáváním zatížení pro koncový bod (tedy vyrovnáváním zatížení) musí být stejné. Nemůže mít konfiguraci různých sondy pro každou instanci role nebo virtuálního počítače ve stejné hostované služby pro konkrétní koncový bod kombinaci. Například každá instance musí mít stejné místní porty a vypršení časového limitu.

> [!IMPORTANT]
> Test nástroje pro vyrovnávání zatížení používá adresu IP adresy 168.63.129.16. Tato veřejná IP adresa umožňuje komunikaci k prostředkům interní platformy pro používání your vlastní – IP adresu scénáři Azure Virtual Network. Virtuální veřejné IP adresy 168.63.129.16 se používá ve všech oblastech a nezmění. Doporučujeme povolit tuto IP adresu v rámci zásad žádné místní brány firewall. To by neměly být zahrnuté bezpečnostní riziko protože pouze interní platformy Azure mají možnost zprávy z této adresy. Pokud tuto IP adresu není povoleno v zásady brány firewall, dojde k neočekávanému chování v různých scénářích. Chování zahrnuje konfiguraci stejného rozsahu IP adres z adresy 168.63.129.16 a duplikování IP adresy.

## <a name="learn-about-the-types-of-probes"></a>Další informace o typech testy paměti

### <a name="guest-agent-probe"></a>Test agenta hosta

Test agenta hosta je pouze k dispozici pro Azure Cloud Services. Nástroj pro vyrovnávání zatížení využívá agent hosta ve virtuálním počítači. Potom naslouchá a jako odpověď vrátí odpověď HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno. (Ostatní stavy jsou zaneprázdněn recyklaci nebo ukončení).

Další informace najdete v tématu [konfigurace definiční soubor služby (csdef) pro sondy stavu](https://msdn.microsoft.com/library/azure/ee758710.aspx) nebo [Začínáme tím, že vytvoříte nástroj pro vyrovnávání zatížení veřejnou pro cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Co vlastně test agenta hosta označit instanci jako není v pořádku?

Pokud agent hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení označí instance jako reagovat. Poté se zastaví, odesílání provozu do této instance. Nástroje pro vyrovnávání zatížení i nadále odešlete zprávu ping na instanci. Pokud agent hosta odpoví HTTP 200, nástroje pro vyrovnávání zatížení odesílá provoz do této instance znovu.

Pokud používáte webovou roli, kód webu obvykle běží v w3wp.exe, který není monitorován pomocí Azure fabric nebo hostovaného agenta. Agent hosta nejsou hlášeny chyby v w3wp.exe (například odpovědi protokolu HTTP 500). Nástroje pro vyrovnávání zatížení v důsledku toho nepřijímá tuto instanci ze smyčky.

### <a name="http-custom-probe"></a>Vlastní sondu protokolu HTTP

Vlastní sondu protokolu HTTP přepisuje výchozí kontroly agenta hosta. Můžete vytvořit vlastní logiky určit stav role instance. Nástroje pro vyrovnávání zatížení sondy vašeho koncového bodu každých 15 sekund, ve výchozím nastavení. Instance se považuje za v oběhu nástroje pro vyrovnávání zatížení Pokud odpoví HTTP 200 v časovém limitu. Časový limit je 31 sekund ve výchozím nastavení.

Vlastní sondy protokolu HTTP může být užitečné, pokud chcete implementovat vlastní logiku k odebrání instance oběhu nástroje pro vyrovnávání zatížení. Například můžete rozhodnout pro odebrání instance, pokud je vyšší než 90 % využití procesoru a vrátí stav bez 200. Pokud máte webové role, které používají w3wp.exe, budete mít také automatické monitorování vašeho webu. Chyby v kódu webu návratový stav než 200 pro test paměti nástroje pro vyrovnávání zatížení.

> [!NOTE]
> Vlastní sondu protokolu HTTP podporuje pouze protokoly HTTP a relativní cesty. HTTPS se nepodporuje.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Co je vlastní sondy protokolu HTTP označit instanci jako není v pořádku?

* HTTP aplikace vrátí kód odpovědi HTTP než 200 (například 403, 404 nebo 500). Toto potvrzení kladné vás upozorní, okamžitě se instance aplikace mimo provoz.
* HTTP server nereaguje vůbec po uplynutí časového limitu. V závislosti na tom, která je nastavena hodnota časového limitu žádosti více testu je možné dát nezodpovězené před sondy označeno jako neběží. (to znamená, že před SuccessFailCount testy jsou odesílány).
* Server ukončí připojení prostřednictvím protokolu TCP resetování.

### <a name="tcp-custom-probe"></a>Vlastní sondu protokolu TCP

Vlastní sondy protokolu TCP inicializovat připojení pomocí provádí třícestné s definovaný port.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Co je vlastní sondu protokolu TCP označit instanci jako není v pořádku?

* Po uplynutí časového limitu TCP serveru nereaguje vůbec. Test paměti, když je označena jako neběží, závisí na počet selhání testu požadavků, které byly nakonfigurovány přejít nezodpovězené před označením testu jako neběží.
* Sonda obdrží TCP resetovat z role instance.

Další informace o konfiguraci sondy stavu protokolu HTTP nebo sondu protokolu TCP najdete v tématu [Začínáme s vytvářením nástroj pro vyrovnávání zatížení veřejnou v Resource Manageru pomocí prostředí PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Přidání instancí v dobrém stavu zpět do oběhu nástroje pro vyrovnávání zatížení

Sondy protokolu TCP a HTTP jsou považovány za v pořádku a označit instanci role jako v pořádku v případě:

* Nástroje pro vyrovnávání zatížení získává kladné první virtuální počítač se spustí test.
* Číslo SuccessFailCount (popsanou výš) definuje hodnotu úspěšné testy, které jsou nutné k označení instance role jako v pořádku. Pokud byla odebrána role instance, počet testy úspěšné, po sobě jdoucích musí být roven nebo překročit hodnotu SuccessFailCount k označení instance role jako spuštění.

> [!NOTE]
> Pokud kolísá stavu role instance, nástroje pro vyrovnávání zatížení čeká už předtím, než se uloží role instance je zpět do stavu v pořádku. Tato doba čekání navíc chrání uživatele a infrastruktury a jsou záměr zásady.

## <a name="use-log-analytics-for-a-load-balancer"></a>Použití log analytics pro nástroj pro vyrovnávání zatížení

Můžete použít [protokolu analytics](load-balancer-monitor-log.md) zkontrolovat stav sondy stavu nástroje pro vyrovnávání zatížení a počet pro zjišťování. Protokolování je možné s Power BI nebo Azure Operational Insights k poskytování statistické údaje o stavu nástroje pro vyrovnávání zatížení.
