---
title: Použijte nástroj pro vyrovnávání zatížení vlastní testy paměti pro sledování stavu | Microsoft Docs
description: Naučte se používat vlastní testy paměti pro vyrovnávání zatížení Azure k monitorování instancí za službou Vyrovnávání zatížení
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
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30176784"
---
# <a name="understand-load-balancer-probes"></a>Pochopení sondy nástroj pro vyrovnávání zatížení

Azure nástroj pro vyrovnávání zatížení používá k určení, kterou instanci fond back-end obdrželi nové toky sondy stavu. Pokud selže test stavu, nástroj pro vyrovnávání zatížení zastaví odesílání nové toky k příslušné instanci není v pořádku a existující toků v dané instanci jsou poškozena.  Pokud všechny instance back-end fondu probe dolů, budou všechny existující toky vypršení časového limitu na všech instancích ve fondu back-end.

Cloudové služby rolí (role pracovního procesu a webové role) pomocí agenta hosta pro test monitorování. Při použití virtuálních počítačů za službou Vyrovnávání zatížení musí být nakonfigurované vlastní stavu sondy TCP nebo HTTP.

## <a name="understand-probe-count-and-timeout"></a>Pochopení počet test a vypršení časového limitu

Postup kontroly závisí na:

* Počet úspěšně sondy, které umožňují instance popisky jako zapnutý.
* Počet neúspěšných sondy, které způsobí instance popisky jako dolů.

Časový limit a četnost hodnot zadaných v SuccessFailCount určit, zda je instance potvrzen spuštěna nebo není spuštěna. Na portálu Azure časový limit nastavena na dvojnásobek hodnota frekvence.

Test konfigurace všech instancí Vyrovnávání zatížení sítě pro koncový bod (to znamená, Vyrovnávání zatížení sítě sady) musí být stejné. Nemůže mít jiný test konfigurace pro každou instanci role nebo virtuálních počítačů v rámci stejné hostované služby pro konkrétní koncový bod kombinaci. Například každá instance musí mít identické místní porty a vypršení časových limitů.

> [!IMPORTANT]
> Sondu nástroje pro vyrovnávání zatížení používá IP adresu 168.63.129.16. Tato veřejná IP adresa usnadňuje komunikaci k prostředkům interní platformu pro bring vaše – vlastní-IP adresu Azure virtuální síť. Virtuální veřejnou IP adresu 168.63.129.16 se používá ve všech oblastech a nemění. Doporučujeme povolit tuto IP adresu na všechny zásady místní brány firewall. Nemělo by se používat bezpečnostní riziko protože zdrojem může být pouze vnitřní platformy Azure zprávy z této adresy. Pokud nepovolíte tuto IP adresu ve vaší zásady brány firewall, dojde k neočekávanému chování v různých scénářů. Chování zahrnuje konfiguraci stejného rozsahu IP adres z 168.63.129.16 a duplikování IP adres.

## <a name="learn-about-the-types-of-probes"></a>Další informace o typech sondy

### <a name="guest-agent-probe"></a>Test agenta hosta

Jenom je k dispozici pro Azure Cloud Services sondu agenta hosta. Nástroj pro vyrovnávání zatížení využívá agent hosta ve virtuálním počítači. Pak naslouchá a odpoví odpověď HTTP 200 OK jenom v případě, že instance je ve stavu Připraveno. (Další stavy jsou zaneprázdněn recyklace nebo ukončení).

Další informace najdete v tématu [nakonfigurovat soubor definice služby (csdef) pro sondy stavu](https://msdn.microsoft.com/library/azure/ee758710.aspx) nebo [začít s vytvářením Vyrovnávání zatížení veřejnou pro cloudové služby](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Díky sondu agenta hosta označit instance jako není v pořádku?

Pokud agenta hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení označí instance jako reagovat. Poté se zastaví, odesílání provozu do této instance. Nástroje pro vyrovnávání zatížení bude nadále příkazem ping otestovat instance. Pokud agenta hosta odpoví HTTP 200, nástroje pro vyrovnávání zatížení odešle provoz do této instance znovu.

Pokud používáte webovou roli, kód webu obvykle běží v w3wp.exe, který není monitorován pomocí Azure prostředků infrastruktury nebo hostovaného agenta. Selhání v w3wp.exe (například odpovědi HTTP 500) nejsou hlášeny agenta hosta. Nástroje pro vyrovnávání zatížení v důsledku toho neberou v dané instanci mimo otočení.

### <a name="http-custom-probe"></a>Vlastní test paměti HTTP

Vlastní test paměti HTTP přepíše výchozí kontroly agenta hosta. Můžete vytvořit vlastní logiky k určení stavu role instance. Nástroje pro vyrovnávání zatížení sondy váš koncový bod každých 15 sekund, ve výchozím nastavení. Instance považován za v otočení nástroje pro vyrovnávání zatížení, pokud se během časového limitu odpoví HTTP 200. Časový limit je 31 sekund ve výchozím nastavení.

HTTP vlastní test paměti může být užitečné, pokud chcete implementovat vlastní logiky k odebrání instance otočení nástroje pro vyrovnávání zatížení. Například můžete rozhodnout k odebrání instance, pokud je vyšší než 90 % využití procesoru a vrátí stav bez 200. Pokud máte webové role, které používají w3wp.exe, můžete také získat automatické monitorování vašeho webu. Selhání v kódu webu vrátit stav bez 200 sondu nástroje pro vyrovnávání zatížení.

> [!NOTE]
> Vlastní test paměti HTTP podporuje pouze protokoly HTTP a relativní cesty. Není podporován protokol HTTPS.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Díky HTTP vlastní test paměti označit instance jako není v pořádku?

* HTTP aplikace vrátí kód odpovědi HTTP než 200 (například 403, 404 nebo 500). Tato kladné potvrzení upozorňuje hned trvat instanci aplikace mimo provoz.
* HTTP server vůbec neodpoví po uplynutí časového limitu. V závislosti na hodnotu časového limitu, který je nastavený, může přejít nezodpovězená víc požadavků na test před sondy získá označen jako neběží (který je před SuccessFailCount sondy odesílají).
* Server uzavře připojení přes TCP resetování.

### <a name="tcp-custom-probe"></a>Vlastní test paměti TCP

Vlastní testy paměti TCP iniciovat připojení k provádění třícestné s definovaný port.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Díky vlastní test paměti TCP označit instance jako není v pořádku?

* TCP server vůbec neodpoví po uplynutí časového limitu. Tato kontrola, když je označena jako neběží závisí na počtu selhání kontroly požadavků, které jste nakonfigurovali přejděte nezodpovězená před označením testu jako neběží.
* Tato kontrola obdrží TCP, obnovit z role instance.

Další informace o tom, jak nakonfigurovat test stavu protokolu HTTP nebo TCP testu najdete v tématu [začínáte s vytvářením Vyrovnávání zatížení veřejnou ve službě Správce prostředků pomocí prostředí PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Přidání pořádku instancí zpět do rotace nástroje pro vyrovnávání zatížení

Sondy TCP a protokolu HTTP jsou považovány za v pořádku a označit instanci role jako v pořádku, pokud:

* Nástroje pro vyrovnávání zatížení získá sondu kladné poprvé spustí virtuální počítač.
* Číslo pro SuccessFailCount (popsanou výš) definuje hodnotu úspěšné sondy, které jsou nutné k označení instance role jako v pořádku. Pokud role instance byla odebrána, počet úspěšný, následných sondy musí rovnat nebo překročit hodnotu SuccessFailCount označit jako spuštěná instance role.

> [!NOTE]
> Pokud stav role instance kolísá, nástroje pro vyrovnávání zatížení čeká už předtím, než odešle role instance zpět do stavu v pořádku. Tato doba čekání navíc chrání uživatele a infrastruktury a je záměrné zásady.

## <a name="use-log-analytics-for-a-load-balancer"></a>Pomocí analýzy protokolů pro vyrovnávání zatížení

Můžete použít [protokolu analýzy](load-balancer-monitor-log.md) testu počet a zkontrolovat stav test stavu nástroje pro vyrovnávání zatížení. Protokolování můžete použít s Power BI nebo statistice provozu Azure poskytnout statistiky o stav okno nástroje pro vyrovnávání zatížení.
