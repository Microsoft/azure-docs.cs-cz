---
title: Funkce monitorování výkonu v řešení pro sledování výkonu sítě v Azure Log Analytics | Microsoft Docs
description: Funkce sledování výkonu v nástroji Sledování výkonu sítě umožňuje sledovat připojení k síti v rámci různých bodů ve vaší síti. Můžete monitorovat nasazení cloudu a místní umístění více datových centrech a firemní pobočky a kritické vícevrstvé aplikace nebo mikroslužeb.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: na
ms.openlocfilehash: 3d51399edbb9679d1cf7b62b075ba34aa5ede42f
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131334"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Síťová řešení pro sledování výkonu: sledování výkonu

Funkce sledování výkonu v [sledování výkonu sítě](log-analytics-network-performance-monitor.md) vám pomůže s monitorováním připojení k síti přes různé body ve vaší síti. Můžete monitorovat nasazení cloudu a místní umístění více datových centrech a firemní pobočky a kritické vícevrstvé aplikace nebo mikroslužeb. Pomocí tohoto nástroje můžete zjistit problémy se síťovým před stížnost vaši uživatelé. Klíčové výhody jsou, můžete: 

- Monitorování ztrátě a latence v různých podsítích a nastavit výstrahy.
- Monitorujte všechny cesty (včetně redundantní cesty) v síti.
- Řešení přechodných a v daném okamžiku síťových problémů, které je obtížné k replikaci.
- Určete konkrétní segmentu v síti, která je zodpovědná za snížení výkonu.
- Monitorování stavu sítě, bez nutnosti SNMP.


![Sledování výkonu sítě](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurace
Chcete-li spustit nástroj konfigurace pro sledování výkonu sítě, otevřete [řešení pro sledování výkonu sítě](log-analytics-network-performance-monitor.md)a vyberte **konfigurace**.

![Konfigurace programu Sledování výkonu sítě](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Vytvořit nové sítě

Síť v nástroji Sledování výkonu sítě je logický kontejner pro podsítě. Pomáhá uspořádat monitorování infrastruktury sítě podle svých potřeb. Můžete vytvořit síť s popisným názvem a do ní přidejte podsítě podle obchodní logiky. Můžete například vytvořit síť s názvem Londýnské a přidat všechny podsítě v Londýně datové centrum. Nebo můžete vytvořit síť s názvem *ContosoFrontEnd* a přidejte k této síti všechny podsítě s názvem Contoso, které slouží front-endu vaší aplikace. Řešení automaticky vytvoří výchozí síť, která obsahuje všechny podsítě zjištěných ve vašem prostředí. 

Vždy, když vytvoříte v síti, se do ní přidávat podsíť. Pak této podsíti se odebere z výchozí sítí. Pokud odstraníte síť, všechny jeho podsítě se automaticky vrátí k výchozí síti. Výchozí sítí slouží jako kontejner pro všechny podsítě, které nejsou obsaženy v žádné uživatelem definované síti. Nelze upravit nebo odstranit výchozí sítí. Vždy zůstává v systému. Můžete vytvořit tolik vlastní sítě podle potřeby. Ve většině případů podsítí ve vaší organizaci jsou uspořádány do více než jedna síť. Vytvořte jednu nebo více sítí, které chcete seskupit podsítě pro vaši obchodní logiku.

Chcete-li vytvořit nové sítě:


1. Vyberte **sítě** kartě.
1. Vyberte **sítě přidat**a pak zadejte název sítě a popis. 
2. Vyberte jednu nebo více podsítí a pak vyberte **přidat**. 
3. Vyberte **Uložit** konfiguraci uložíte. 


### <a name="create-monitoring-rules"></a>Vytvoření pravidla monitorování 

Sledování výkonu generuje události stavu, když je prahová hodnota výkonu síťové připojení mezi dvěma podsítěmi nebo mezi dvěma sítěmi porušena. Systém automaticky další tyto prahové hodnoty. Také můžete zadat vlastní prahové hodnoty. Systém automaticky vytvoří výchozí pravidlo, které generuje událost stavu vždy, když ke ztrátě nebo latenci mezi žádném páru síť nebo podsíť odkazy narušení prahovou hodnotu naučili systému. Tento proces pomáhá monitorovat infrastrukturu sítě, dokud jste nevytvořili žádné pravidla monitorování explicitně řešení. Pokud je povoleno výchozí pravidlo, všechny uzly syntetické transakce poslat všechny další uzly, které jste povolili pro monitorování. Výchozí pravidlo je užitečný v případě malé sítě. Příkladem je scénář, kde máte malý počet serverů se systémem mikroslužbu a chcete zajistit, že všechny servery mají připojení k sobě navzájem.

>[!NOTE]
> Doporučujeme zakázat výchozí pravidlo a vytvořit vlastní pravidla monitorování, zejména u rozsáhlých sítí, kde pro monitorování používáte velký počet uzlů. Vlastní pravidla sledování můžete omezit přenos vygenerovaných řešení a k uspořádání monitorování sítě.

Vytvoření pravidla monitorování podle obchodní logiky. Příkladem je, pokud chcete monitorovat výkon síťové připojení dvou lokalit office s ústředím. Skupiny jsou podsítě v office web1 v síti O1. Potom skupiny jsou podsítě v office site2 v síti O2. Nakonec skupiny jsou podsítě v ústředí v síti H. vytvořit dvě pravidla monitorování – jeden mezi O1 a H a dalších až O2 H. 

Chcete-li vytvořit vlastní pravidla monitorování:

1. Vyberte **přidat pravidlo** na **monitorování** kartě a zadejte název pravidla a popis.
2. Vyberte dvojice síť nebo podsíť odkazy na monitorování seznamů. 
3. Vyberte síť, která obsahuje podsítě, které chcete z rozevíracího seznamu síť. Pak vyberte podsítěmi v příslušném rozevíracím seznamu podsítí. Pokud chcete monitorovat všechny podsítě v síťové propojení, vyberte **všechny podsítě**. Podobně vyberte jiné podsítěmi, které chcete. Pokud chcete vyloučit, monitorování pro konkrétní podsítí odkazy z voleb, vyberte **přidat výjimka**. 
4. Volba mezi ICMP a TCP protokoly spuštění syntetické transakce. 
5. Pokud nechcete, aby k vytvoření události stavu pro položky jste vybrali, zrušte **povolit sledování stavu na odkazy předmětem toto pravidlo**. 
6. Vyberte sledování podmínek. Pokud chcete nastavit vlastní prahové hodnoty pro generování událost stavu, zadejte prahové hodnoty. Vždy, když je hodnota stavu překročí jeho vybraná prahová hodnota pro vybranou síť nebo dvojici podsíť, je generována událost stavu. 
7. Vyberte **Uložit** konfiguraci uložíte. 

Po uložení monitorování pravidlo, kterou můžete integrovat daného pravidla se správu výstrah výběrem **vytvoření výstrahy**. Pravidlo výstrahy se automaticky vytvoří s vyhledávací dotaz. Další požadované parametry se automaticky vyplní. Pravidlo výstrahy může přijímat výstrahy – na základě, kromě existující výstrahy v rámci programu Sledování výkonu sítě. Výstrahy můžete také aktivovat nápravné akce pomocí runbooků nebo se může integrovat stávajících řešení pro správu služby pomocí webhooků. Vyberte **Spravovat výstrahy** upravit nastavení výstrah. 

Nyní můžete vytvořit další pravidla sledování výkonu nebo přesunout na řídicí panel řešení funkce.

### <a name="choose-the-protocol"></a>Vyberte protokol

Sledování výkonu sítě používá k výpočtu metriky výkonu sítě jako paketu ztrátě a odkaz latence syntetické transakce. Chcete-li tento koncept lépe pochopit, zvažte agenta monitorování výkonu síťového připojení k jednom konci síťového propojení. Tento agent monitorování výkonu sítě odesílá pakety testu druhý Agent sledování výkonu sítě připojené k jiné konec sítě. Druhý agenta odpoví odpovědí s odpovědí na žádost. Tento proces opakuje několikrát. Měřením počet odpovědi a čas potřebný k přijetí každé odpovědi prvního agenta monitorování výkonu síťového vyhodnocuje odkaz latence a zahazování paketů. 

Formát, velikosti a pořadí tyto pakety je určen podle protokol, který zvolíte, při vytváření pravidla monitorování. Na základě protokolu paketů, zprostředkující síťová zařízení, jako například routery a přepínače, může zpracovat tyto pakety jinak. V důsledku toho zvoleného protokol ovlivní přesnost výsledků. Zvoleného protokol také určuje, zda je nutné provést jakékoli ruční kroky, po nasazení řešení pro sledování výkonu sítě. 

Sledování výkonu sítě nabízí volba mezi protokoly ICMP a TCP pro provádění syntetické transakce. Pokud si zvolíte ICMP, při vytváření pravidla syntetické transakce, agenti monitorování výkonu sítě používat zprávy ODEZVU protokolu ICMP k výpočtu latence sítě a ztráta paketů. ODEZVU protokolu ICMP používá stejnou zprávu, která je odeslána nástrojem konvenční ping. Použijete-li jako protokol TCP, agenti monitorování výkonu síťového poslat TCP SYN paketů přes síť. Tento krok je následován dokončení metody handshake TCP a připojení je odebrat pomocí RVNÍ paketů. 

Před vyberte protokol, zvažte následující informace: 

* **Zjišťování několik síťových tras.** TCP je přesnější při zjišťování víc tras a je menší počet agentů v jednotlivých podsítích. Jednoho nebo dva agenty, které používají TCP můžete například zjistit všechny redundantní cesty mezi podsítěmi. Budete potřebovat několik agenty, kteří dosáhnout podobné výsledky pomocí protokolu ICMP. Pomocí protokolu ICMP, pokud máte několik tras mezi dvěma podsítěmi, je potřeba více než 5N agentů ve zdrojové nebo cílové podsíti.

* **Přesné výsledky.** Směrovače a přepínače zpravidla přiřadit ODEZVU protokolu ICMP pakety ve srovnání s TCP pakety s nižší prioritou. V některých situacích při síťová zařízení jsou velkém zatížení, data získat TCP přesněji odráží ztrátě a latence, které aplikace. K tomu dochází, protože většina dat aplikace probíhá přes protokol TCP. V takových případech ICMP poskytuje ve srovnání s TCP méně přesné výsledky. 

* **Konfigurace brány firewall.** Protokolu TCP vyžaduje, aby TCP pakety odesílají na cílový port. Výchozí port je používán agenty monitorování výkonu sítě je 8084. Při konfiguraci agentů, můžete změnit port. Ujistěte se, že síťové brány firewall nebo skupiny (NSG) pravidla zabezpečení sítě (v Azure) umožňují přenosy na portu. Budete také muset Ujistěte se, že místní brány firewall na počítačích, kde jsou nainstalováni agenti konfigurace umožňuje provozu na tomto portu. Konfigurace pravidel brány firewall na počítačích s Windows můžete použít skripty prostředí PowerShell, ale budete muset ručně nakonfigurovat bránu firewall vaší sítě. Naproti tomu ICMP nepracuje pomocí portu. Ve většině scénářů organizace ICMP je povolen přenos přes bránu firewall umožňují použijte nástroj Diagnostika sítě jako nástroj ping. Pokud jeden počítač z jiné, může odeslat příkaz ping, můžete protokol ICMP bez nutnosti ruční konfigurace brány firewall.

>[!NOTE] 
> Některé brány firewall mohou blokovat ICMP, což může vést k opakování přenosu, jejímž výsledkem velké množství událostí v systému správy informace a událostí zabezpečení. Ujistěte se, že protokol, který zvolíte, není blokován nastavením brány firewall sítě nebo NSG. Sledování výkonu sítě, jinak nelze monitorovat segmentu sítě. Doporučujeme použít TCP pro sledování. Použití ICMP ve scénářích, kdy je nelze použít TCP, jako např. kdy: 
>
> - Uzly na základě klienta systému Windows můžete použít, protože nezpracovaná sockets TCP nejsou povoleny v klientech Windows.
> - Síťová brána firewall nebo NSG blokuje TCP.
> - Nevíte jak přepínat protokol.

Pokud jste se rozhodli použít ICMP během nasazení, můžete přepnout TCP kdykoli úpravou výchozí pravidlo monitorování.

1. Přejděte na **síťový výkon** > **monitorování** > **konfigurace**   >  **Monitorování**. Potom vyberte **výchozí pravidlo**. 
2. Přejděte k položce **protokol** a vyberte protokol, který chcete použít. 
3. Vyberte **Uložit** použití nastavení. 

I když výchozí pravidlo používá určitý protokol, můžete vytvořit nová pravidla s jiný protokol. Můžete například vytvořit směs pravidla, kde některá pravidla použít ICMP a ostatní použití protokolu TCP. 

## <a name="walkthrough"></a>Názorný postup 

Nyní se podívejte na jednoduchý vyšetřování příčiny stavu události.

Na řídicím panelu řešení událost stavu ukazuje, že není v pořádku síťové propojení. Chcete-li prozkoumat problém, vyberte **sítě odkazy monitorovaných** dlaždici.

Procházení stránka zobrazuje, které **DMZ2 DMZ1** síťové propojení není v pořádku. Vyberte **zobrazení podsítě odkazy** pro toto propojení sítě. 


Na stránce podrobností se zobrazí všechna propojení podsítí ve **DMZ2 DMZ1** síťového propojení. Pro obě propojení podsítí latence překročí prahovou hodnotu, což může síťové propojení není v pořádku. Taky uvidíte latence trendy obou propojení podsítí. Použijte výběr časového řízení v grafu a zaměřit se na požadované časové rozmezí. Zobrazí čas, po dosažení latence jeho ve špičce. Vyhledejte v protokolech později pro toto časové období můžete prozkoumat problém. Vyberte **zobrazení uzlu odkazy** k podrobnostem. 
 
 ![Stránka odkazy podsítí](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Podobně jako na předchozí stránku, na stránce procházení pro konkrétní podsítí propojení uvedeny jeho základní uzlu odkazy. Můžete provádět akce podobně jako zde, stejně jako v předchozím kroku. Vyberte **zobrazení topologie** zobrazíte topologie mezi dvěma uzly. 
 
 ![Stránka odkazy uzlu](media/log-analytics-network-performance-monitor/node-links.png) 

Všechny cesty mezi dva vybrané uzly jsou zobrazeny v mapě topologie. Můžete vizualizovat topologii směrování směrování trasy mezi dvěma uzly na mapě topologie. Poskytuje přehledné informace o tom, kolik tras mezi dva uzly a co cesty trvat datových paketů. Kritické body sítě se zobrazí červeně. Vyhledat vadný síťového připojení nebo chyba síťové zařízení, podívejte se na mapě topologie na red elementy. 

 ![Řídicí panel topologie se mapy topologie](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Můžete zkontrolovat ztrátu, latenci a počet skoků v každou z cest v **akce** podokně. Posuvník použijte k zobrazení podrobností není v pořádku cesty. Pomocí filtrů můžete vybrat cesty s směrování není v pořádku, aby se vykreslí topologie pro pouze vybrané cesty. Pokud chcete zvětšit nebo zmenšit topologickou mapu, použijte kolečko myši. 

Na následujícím obrázku v red cesty a segmentů směrování zobrazí hlavní příčinu problému oblasti, které mají určitou část sítě. Vyberte uzel v mapě topologie a odhalit vlastnosti uzlu, který obsahuje plně kvalifikovaný název domény a IP adresu. Výběr směrování zobrazuje IP adresu směrování. 
 
![Mapa topologie s vybrané vlastnosti uzlu](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Další postup
[V protokolech Hledat](log-analytics-log-searches.md) zobrazíte podrobné sítě záznamů dat výkonu.
