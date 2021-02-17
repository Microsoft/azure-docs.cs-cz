---
title: Sledování výkonu v Network Performance Monitor
description: Funkce sledování výkonu v Network Performance Monitor pomáhá monitorovat síťové připojení mezi různými místy ve vaší síti. Můžete monitorovat cloudová nasazení a místní umístění, několik datových center a poboček a klíčové aplikace nebo mikroslužby s více vrstvami.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 5335882c11ee6a1a7352ef3dc9822cfa88eda196
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573514"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Řešení Network Performance Monitor: sledování výkonu

> [!IMPORTANT]
> Od 1. července 2021 nebudete moci přidat nové testy do existujícího pracovního prostoru nebo povolit nový pracovní prostor v Network Performance Monitor. Můžete pokračovat v používání testů vytvořených před 1. července 2021. Pokud chcete minimalizovat přerušení služby na vaše aktuální úlohy, [migrujte testy z Network Performance Monitor na nové monitorování připojení](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) v Azure Network Watcher před 29. února 2024.

Funkce sledování výkonu v [Network Performance Monitor](network-performance-monitor.md) pomáhá monitorovat síťové připojení mezi různými místy ve vaší síti. Můžete monitorovat cloudová nasazení a místní umístění, několik datových center a poboček a klíčové aplikace nebo mikroslužby s více vrstvami. Pomocí nástroje sledování výkonu můžete detekovat problémy se sítí ještě předtím, než uživatelé podali stížnost. Mezi klíčové výhody patří: 

- Sledujte ztráty a latenci napříč různými podsítěmi a nastavte výstrahy.
- Monitorujte všechny cesty (včetně redundantních cest) v síti.
- Řešení přechodných a časově probíhajících síťových problémů, které je obtížné replikovat.
- Určení konkrétního segmentu sítě, který je zodpovědný za snížený výkon.
- Monitoruje stav sítě bez nutnosti SNMP.


![Sledování výkonu sítě](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurace
Chcete-li otevřít konfiguraci pro Network Performance Monitor, otevřete [řešení Network Performance Monitor](network-performance-monitor.md)a vyberte možnost **Konfigurovat**.

![Konfigurace Network Performance Monitoru](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Vytváření nových sítí

Síť v Network Performance Monitor je logický kontejner pro podsítě. Pomáhá organizovat monitorování síťové infrastruktury podle vašich potřeb. Můžete vytvořit síť s popisným názvem a přidat do ní podsítě podle vaší obchodní logiky. Můžete například vytvořit síť s názvem Londýn a přidat všechny podsítě v datovém centru Londýn. Nebo můžete vytvořit síť s názvem *ContosoFrontEnd* a přidat k této síti všechny podsítě s názvem contoso, které budou sloužit na front-end vaší aplikace. Řešení automaticky vytvoří výchozí síť, která obsahuje všechny podsítě zjištěné ve vašem prostředí. 

Pokaždé, když vytvoříte síť, přidáte do ní podsíť. Pak se podsíť odebere z výchozí sítě. Pokud síť odstraníte, všechny její podsítě se automaticky vrátí do výchozí sítě. Výchozí síť slouží jako kontejner pro všechny podsítě, které nejsou obsaženy v žádné uživatelsky definované síti. Výchozí síť nelze upravit ani odstranit. Vždy zůstává v systému. V případě potřeby můžete vytvořit libovolný počet vlastních sítí. Ve většině případů jsou podsítě ve vaší organizaci uspořádány do více než jedné sítě. Vytvořte jednu nebo více sítí pro seskupení podsítí pro obchodní logiku.

Vytvoření nové sítě:


1. Vyberte kartu **sítě** .
1. Vyberte **Přidat síť** a pak zadejte název a popis sítě. 
2. Vyberte jednu nebo více podsítí a pak vyberte **Přidat**. 
3. Kliknutím na **Uložit** uložte konfiguraci. 


### <a name="create-monitoring-rules"></a>Vytvořit pravidla monitorování 

Nástroj Sledování výkonu generuje události stavu, když dojde k porušení prahové hodnoty výkonu síťových připojení mezi dvěma podsítěmi nebo mezi dvěma sítěmi. Systém může tyto prahové hodnoty automaticky zjistit. Můžete také zadat vlastní prahové hodnoty. Systém automaticky vytvoří výchozí pravidlo, které generuje událost stavu vždy, když dojde ke ztrátě nebo latenci mezi libovolným párem sítě nebo podsítí v případě porušení prahové hodnoty zjištěné systémem. Tento proces pomůže řešení sledovat vaši síťovou infrastrukturu, dokud nevytvoříte žádná pravidla monitorování explicitně. Pokud je výchozí pravidlo povolené, všechny uzly odesílají syntetické transakce do všech ostatních uzlů, které jste povolili pro monitorování. Výchozí pravidlo je užitečné v malých sítích. Příkladem je situace, kdy máte malý počet serverů, na kterých běží mikroslužba, a chcete zajistit, aby všechny servery byly vzájemně připojené.

>[!NOTE]
> Doporučujeme zakázat výchozí pravidlo a vytvořit vlastní pravidla monitorování, zejména ve velkých sítích, ve kterých používáte velký počet uzlů pro monitorování. Vlastní pravidla monitorování můžou snížit provoz vygenerovaný řešením a pomáhat vám při uspořádávání monitorování vaší sítě.

Vytvořte pravidla monitorování podle vaší obchodní logiky. Příkladem je, že chcete monitorovat výkon síťového připojení dvou webů Office k ústředí. Seskupí všechny podsítě v sadě Office site1 v síti O1. Pak seskupte všechny podsítě v Office site2 v síti O2. Nakonec seskupte všechny podsítě v ústředí v síti H. Vytvořte dvě pravidla monitorování – jeden mezi O1 a H a druhý mezi O2 a H. 

Postup při vytváření vlastních pravidel monitorování:

1. Na kartě **monitorování** vyberte **Přidat pravidlo** a zadejte název pravidla a popis.
2. Vyberte dvojici sítí nebo podsítí, které chcete monitorovat ze seznamů. 
3. V rozevíracím seznamu síť vyberte síť, která obsahuje požadované podsítě. Pak vyberte podsítě z rozevíracího seznamu odpovídající podsítě. Pokud chcete monitorovat všechny podsítě v síťovém propojení, vyberte **všechny podsítě**. Podobně vyberte další podsítě, které chcete. Pokud chcete z vybraných výběrů vyloučit monitorování pro konkrétní odkazy na podsítě, vyberte **Přidat výjimku**. 
4. Pro provádění syntetických transakcí si můžete vybrat mezi protokoly ICMP a TCP. 
5. Pokud nechcete vytvářet události stavu pro vybrané položky, zrušte zaškrtnutí políčka **Povolit monitorování stavu u odkazů, které jsou předmětem tohoto pravidla**. 
6. Vyberte možnost podmínky monitorování. Chcete-li nastavit vlastní prahové hodnoty pro generování událostí stavu, zadejte prahové hodnoty. Pokaždé, když hodnota podmínky překročí zvolenou prahovou hodnotu pro vybraný síťový pár nebo dvojici podsítí, dojde k vygenerování události stavu. 
7. Kliknutím na **Uložit** uložte konfiguraci. 

Po uložení pravidla monitorování můžete toto pravidlo integrovat s Alert Management tím, že vyberete **vytvořit výstrahu**. Pomocí vyhledávacího dotazu se automaticky vytvoří pravidlo upozornění. Další požadované parametry jsou automaticky vyplněny. Pomocí pravidla výstrahy můžete přijímat i e-mailové výstrahy, a to i s existujícími výstrahami v rámci Network Performance Monitor. Výstrahy také mohou aktivovat nápravné akce pomocí sad Runbook nebo je mohou integrovat s existujícími řešeními pro správu služeb pomocí webhooků. Vyberte **Spravovat upozornění** a upravte nastavení výstrah. 

Teď můžete vytvořit další pravidla monitorování výkonu nebo přesunout na řídicí panel řešení a využít tak tuto možnost.

### <a name="choose-the-protocol"></a>Zvolit protokol

Network Performance Monitor používá syntetické transakce pro výpočet metrik výkonu sítě, jako jsou ztráty paketů a latence propojení. Pokud chcete tento koncept lépe pochopit, zvažte Network Performance Monitor agenta připojeného k jednomu konci síťového propojení. Tento agent Network Performance Monitor odesílá pakety sondy druhému agentovi Network Performance Monitor připojenému k jinému konci sítě. Druhý agent odpoví pomocí paketů odpovědi. Tento proces se opakuje několikrát. Při měření počtu odpovědí a doby trvání přijetí každé odpovědi první agent Network Performance Monitor vyhodnocuje latenci propojení a pokles paketů. 

Formát, velikost a posloupnost těchto paketů jsou určeny protokolem, který zvolíte při vytváření pravidel monitorování. Na základě protokolu paketů můžou zprostředkující síťová zařízení, jako jsou směrovače a přepínače, zpracovávat tyto pakety různě. V důsledku toho volba protokolu ovlivňuje přesnost výsledků. Volba protokolu také určuje, zda je nutné po nasazení řešení Network Performance Monitor provést jakékoli ruční kroky. 

Network Performance Monitor nabízí možnost výběru mezi protokoly ICMP a TCP pro provádění syntetických transakcí. Pokud při vytváření pravidla syntetické transakce zvolíte protokol ICMP, agenti Network Performance Monitor použijí zprávy ICMP ECHO k výpočtu latence sítě a ztráty paketů. ECHO protokol ICMP používá stejnou zprávu, kterou odesílá Nástroj pro konvenční test příkazů. Pokud jako protokol používáte protokol TCP, Network Performance Monitor agenti odesílají pakety TCP SYN přes síť. Tento krok následuje po dokončení TCP handshake a připojení se odebere pomocí paketů RST. 

Před zvolením protokolu zvažte následující informace: 

* **Zjišťování více síťových tras.** Protokol TCP je přesnější při zjišťování více tras a potřebuje méně agentů v každé podsíti. Například jeden nebo dva agenti, kteří používají protokol TCP, mohou zjišťovat všechny redundantní cesty mezi podsítěmi. K dosažení podobných výsledků potřebujete několik agentů používajících protokol ICMP. Pokud používáte několik tras mezi dvěma podsítěmi, budete potřebovat více než 5N agentů v buď zdrojové, nebo cílové podsíti.

* **Přesnost výsledků.** Směrovače a přepínače obvykle přiřazují k paketům s protokolem ICMP nižší prioritu v porovnání s pakety TCP. V některých situacích, kdy jsou síťová zařízení hodně načítána, data získaná protokolem TCP porážejí i ztrátu a latenci, které aplikace zaznamenaly. K tomu dochází, protože většina přenosů aplikací přes protokol TCP. V takových případech poskytuje ICMP méně přesné výsledky v porovnání s protokolem TCP. 

* **Konfigurace brány firewall.** Protokol TCP vyžaduje, aby byly pakety TCP odesílány do cílového portu. Výchozí port používaný Network Performance Monitor agenty je 8084. Port můžete změnit při konfiguraci agentů. Ujistěte se, že brány firewall sítě nebo pravidla skupiny zabezpečení sítě (NSG) (v Azure) povolují provoz na portu. Musíte také zajistit, aby byla místní brána firewall na počítačích, kde jsou agenti nainstalováni, nakonfigurována tak, aby povolovala přenosy na tomto portu. Ke konfiguraci pravidel brány firewall na počítačích s Windows můžete použít skripty prostředí PowerShell, ale musíte ručně nakonfigurovat bránu firewall sítě. Naproti tomu protokol ICMP nefunguje pomocí portu. Ve většině podnikových scénářů je provoz ICMP povolený přes brány firewall, aby bylo možné používat nástroje pro diagnostiku sítě, jako je například nástroj příkazového testu. Pokud můžete provést příkaz k otestování jednoho počítače z jiného, můžete použít protokol ICMP bez nutnosti ruční konfigurace bran firewall.

>[!NOTE] 
> Některé brány firewall můžou blokovat protokol ICMP, což by mohlo vést k opětovnému přenosu, což vede k velkému počtu událostí v systému pro správu informací a událostí. Ujistěte se, že protokol, který zvolíte, není blokovaný síťovou bránou firewall nebo NSG. V opačném případě Network Performance Monitor nemůže sledovat segment sítě. Pro monitorování doporučujeme používat protokol TCP. Protokol ICMP se používá v situacích, kdy nemůžete používat protokol TCP, například když: 
>
> - Používáte klientské uzly systému Windows, protože nezpracované sokety TCP nejsou povoleny v klientech systému Windows.
> - Vaše síťová brána firewall nebo NSG blokují protokol TCP.
> - Nevíte, jak protokol přepínat.

Pokud se rozhodnete používat protokol ICMP během nasazení, můžete kdykoli přejít na protokol TCP úpravou výchozího pravidla monitorování.

1. Přejít na **sledování výkonu sítě**   >  ****   >  **Konfigurace**   >  **monitorování**. Pak vyberte **výchozí pravidlo**. 
2. Přejděte do části **protokol** a vyberte protokol, který chcete použít. 
3. Vyberte **Uložit** a použijte nastavení. 

I v případě, že výchozí pravidlo používá konkrétní protokol, můžete vytvořit nová pravidla s jiným protokolem. Můžete dokonce vytvořit kombinaci pravidel, kde některá pravidla používají protokol ICMP a jiné používají protokol TCP. 

## <a name="walkthrough"></a>Názorný postup 

Nyní se můžete podívat na jednoduché šetření hlavní příčiny události stavu.

Na řídicím panelu řešení událost stavu ukazuje, že síťové propojení není v pořádku. Pokud chcete problém prozkoumat, vyberte dlaždici **monitorovaná síťová propojení** .

Na stránce s podrobnostmi o přecházení se zobrazuje, že síťové propojení **DMZ2-DMZ1** není v pořádku. Vyberte **Zobrazit odkazy podsítě** pro toto síťové propojení. 


Stránka s podrobnostmi o prostupnosti zobrazuje všechna propojení podsítí v síťovém odkazu **DMZ2-DMZ1** . U odkazů na obě podsítě tato latence překročila prahovou hodnotu, což způsobí, že síťové propojení není v pořádku. Můžete také zobrazit trendy latence obou odkazů na podsítě. Pomocí ovládacího prvku Výběr času v grafu se zaměřte na požadovaný časový rozsah. Můžete zobrazit denní dobu, kdy latence dosáhla své špičky. Prozkoumejte protokoly později v tomto časovém období, abyste mohli problém prozkoumat. Vyberte **Zobrazit odkazy na uzly** a procházejte podrobněji. 
 
 ![Stránka odkazy na podsítě](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Podobně jako na předchozí stránce je na stránce podrobností pro konkrétní síť uvedena jeho odkazy na uzel. Podobné akce můžete provádět jako v předchozím kroku. Vyberte **Zobrazit topologii** pro zobrazení topologie mezi dvěma uzly. 
 
 ![Stránka odkazy na uzel](media/network-performance-monitor-performance-monitor/node-links.png) 

Všechny cesty mezi dvěma vybranými uzly jsou vykresleny v mapě topologie. Topologii směrování mezi segmenty hop můžete vizualizovat mezi dvěma uzly na mapě topologie. Poskytuje jasný přehled o tom, kolik tras existuje mezi dvěma uzly a jaké cesty tyto datové pakety přebírají. Kritická místa výkonu sítě se zobrazují červeně. Pokud chcete najít vadné síťové připojení nebo vadné síťové zařízení, Prohlédněte si červené prvky na mapě topologie. 

 ![Řídicí panel topologie s mapou topologie](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Můžete zkontrolovat ztrátu, latenci a počet segmentů směrování v každé cestě v podokně **Akce** . K zobrazení podrobností o nestavových cestách použijte posuvník. Pomocí filtrů vyberte cesty s nedobrým segmentem směrování, aby se vykreslila topologie jenom pro vybrané cesty. Chcete-li zvětšit nebo zmenšit mapu topologie, použijte kolečko myši. 

Na následujícím obrázku se v červené cestě a segmentech směrování zobrazí hlavní příčina problematických oblastí do konkrétní části sítě. Vyberte uzel na mapě topologie, abyste odhalili vlastnosti uzlu, který zahrnuje plně kvalifikovaný název domény a IP adresu. Výběrem směrování se zobrazí IP adresa směrování. 
 
![Mapa topologie s vybranými vlastnostmi uzlu](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Další kroky
V [protokolech hledání](../logs/log-query-overview.md) zobrazíte podrobné záznamy dat o výkonu sítě.

