---
title: Funkce monitorování výkonu v řešení Network Performance Monitor ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Funkce monitorování výkonu v nástroji Sledování výkonu sítě umožňuje monitorování síťového propojení mezi různými body ve vaší síti. Můžete monitorovat nasazení cloudu a místními umístěními, více datových center a firemní pobočky a nejdůležitější vícevrstvé aplikace nebo mikroslužeb.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: de9e6d5c8c6b4dc9652ae64c9a4fd0e38d7a7b87
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184759"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Network Performance Monitor řešení: Sledování výkonu

Funkce monitorování výkonu v [Network Performance Monitor](network-performance-monitor.md) vám pomůže s monitorováním připojení k síti přes různé body ve vaší síti. Můžete monitorovat nasazení cloudu a místními umístěními, více datových center a firemní pobočky a nejdůležitější vícevrstvé aplikace nebo mikroslužeb. Pomocí sledování výkonu dokáže detekovat problémy se sítí, předtím, než si vaši zákazníci začnou stěžovat. Klíčové výhody jsou, ke kterým můžete: 

- Monitorování ztrát a latence napříč různými podsítěmi a nastavení výstrah.
- Monitorování všech cest (včetně redundantní cesty) v síti.
- Řešení potíží s problémy se sítí přechodné a bodu v čase, které se těžko replikují.
- Určení konkrétního segmentu sítě, což je důvodem sníženého výkonu.
- Monitorování stavu sítě bez nutnosti využití SNMP.


![Sledování výkonu sítě](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurace
Chcete-li spustit nástroj konfigurace pro sledování výkonu sítě, otevřete [řešení Network Performance Monitor](network-performance-monitor.md)a vyberte **konfigurovat**.

![Konfigurace Network Performance Monitor](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Vytváření nových sítí

Sítě v nástroji Sledování výkonu sítě je logický kontejner pro podsítě. Pomůže vám uspořádání, sledování infrastruktury sítě podle vašich potřeb. Můžete vytvořit síť s popisným názvem a přidejte do ní podsítě podle obchodní logiku. Můžete například vytvořit síť s názvem Londýnské a přidat všechny podsítě v Londýně datovém centru. Nebo můžete vytvořit síť s názvem *ContosoFrontEnd* a přidejte k této síti všechny podsítě s názvem Contoso, které slouží front-endu vaší aplikace. Řešení automaticky vytváří výchozí síť, která obsahuje všechny podsítě prohledány ve vašem prostředí. 

Pokaždé, když vytvoříte síť, přidáte do ní podsíť. Poté této podsíti se odebere z výchozí síť. Při odstranění síť, všechny její podsítě se automaticky vrátí k síti výchozí. Výchozí síť funguje jako kontejner pro všechny podsítě, které nejsou součástí žádné uživatelem definované síti. Se nedají upravit ani odstranit výchozí síť. Vždy zůstane v systému. Můžete vytvořit tolik vlastní sítě podle potřeby. Ve většině případů jsou podsítě ve vaší organizaci uspořádány do více než jedna síť. Vytvořte jednu nebo více sítí, které chcete seskupit podsítě pro vaši obchodní logiku.

Chcete-li vytvořit novou síť:


1. Vyberte **sítě** kartu.
1. Vyberte **sítě přidat**a potom zadejte síťový název a popis. 
2. Vyberte jednu nebo více podsítí a potom vyberte **přidat**. 
3. Vyberte **Uložit** uložte konfiguraci. 


### <a name="create-monitoring-rules"></a>Vytvoření pravidla monitorování 

Sledování výkonu generuje události týkající se stavu, když prahové hodnoty výkonu síťová připojení mezi dvě podsítě, nebo mezi dvěma sítěmi dojde k porušení zabezpečení. Systém automaticky další tyto prahové hodnoty. Také můžete zadat vlastní prahové hodnoty. Systém automaticky vytváří výchozí pravidlo, které generuje událost stavu vždy, když ztráta nebo latence mezi nějaká dvojice sítě nebo podsítě propojí porušení prahové hodnoty se naučili systému. Tento proces pomáhá řešení monitorovat infrastrukturu sítě, dokud explicitně jste ještě nevytvořili žádná pravidla monitorování. Pokud je povolené výchozí pravidlo, všechny uzly odeslat do všech ostatních uzlů, které jste povolili monitorování syntetické transakce. Výchozí pravidlo je užitečný v případě malých sítích. Příkladem je scénář, kde máte malý počet serverů se systémem mikroslužby a chcete, aby se zajistilo, že všechny servery mají připojení k sobě navzájem.

>[!NOTE]
> Doporučujeme zakázat pravidlo výchozí a vytvářet vlastní pravidla monitorování, hlavně u rozsáhlých sítí, kde používáte velký počet uzlů pro monitorování. Vlastní pravidla monitorování můžete omezit přenos generovaných řešení a usnadňují uspořádání monitorování vaší sítě.

Vytvoření pravidla monitorování podle obchodní logiku. Příkladem je, pokud chcete sledovat výkon síťového připojení dvou lokalit office s ústředím. Skupina všech podsítí v office web1 v síti O1. Potom proveďte seskupení všech podsítí v office site2 v síti O2. Nakonec skupině všech podsítí v ústředí v síti H. vytvořit dvě pravidla monitorování – mezi O1 a H a druhá mezi O2 a H. 

Chcete-li vytvořit vlastní pravidla monitorování:

1. Vyberte **přidat pravidlo** na **monitorování** kartu a zadejte název pravidla a popis.
2. Vyberte dvojice sítě nebo podsítě. odkazy na monitorování ze seznamu. 
3. Vyberte síť, která obsahuje podsítě, ke které chcete z rozevíracího seznamu síť. Ze seznamu odpovídající podsítě rozevíracího seznamu vyberte podsítím. Pokud chcete monitorovat všechny podsítě v síti odkaz, vyberte **všechny podsítě**. Podobným způsobem vyberte další podsítě, které chcete. Chcete-li vyloučit monitorování pro odkazy na konkrétní podsítě z vybrané možnosti, které jste provedli, vyberte **přidat výjimky**. 
4. Zvolte mezi protokoly ICMP a TCP protokoly provádět syntetické transakce. 
5. Pokud nechcete vytvořit stavu události pro položky jste vybrali, zrušte **povolit monitorování stavu na odkazů spadajících pod toto pravidlo**. 
6. Vyberte sledování podmínek. Pokud chcete nastavit vlastní prahové hodnoty pro generování událost stavu, zadejte prahové hodnoty. Pokaždé, když se hodnota podmínky překračuje jeho zvolená prahová hodnota pro vybranou síť nebo pár podsítě, vygeneruje událost stavu. 
7. Vyberte **Uložit** uložte konfiguraci. 

Po uložení pravidlo monitorování můžete integrovat tímto pravidlem se správou výstrah tak, že vyberete **vytvoření výstrahy**. Pravidlo upozornění se automaticky vytvoří pomocí vyhledávacího dotazu. Další požadované parametry jsou automaticky vyplněna. Pravidlo upozornění můžete dostávat e-mailových oznámení, kromě existující výstrahy v rámci Network Performance monitoru. Upozornění můžou také aktivovat nápravné akce pomocí runbooků nebo můžete integrovat s stávajících řešení pro správu služby pomocí webhooků. Vyberte **spravovat upozornění** úprava nastavení oznámení. 

Teď můžete vytvořit další pravidla sledování výkonu nebo přejděte na řídicí panel řešení použít funkci.

### <a name="choose-the-protocol"></a>Vyberte protokol

Network Performance Monitor používá k výpočtu metriky výkonu sítě, jako jsou ztráty a odkaz latenci paketů syntetické transakce. Chcete-li lépe porozumět tento koncept, zvažte Network Performance Monitor agent, který je připojený k jednom konci síťového propojení. Tento agent Network Performance Monitor odešle pakety testu do druhého Network Performance Monitor agenta připojena k jiné koncové sítě. Druhý agenta odpoví odpovědí na žádost. Tento proces se opakuje několikrát. Na základě měření počtu odpovědí a čas potřebný k přijetí každé odpovědi, první Network Performance Monitor agent vyhodnocuje latence připojení a zahazování paketů. 

Formát, velikosti a pořadí tyto pakety se určuje podle protokolu, který vyberete při vytváření pravidla monitorování. Na základě protokolu paketů, zprostředkující síťová zařízení, jako například routery a přepínače, může zpracovat tyto pakety odlišně. V důsledku toho podle vašeho výběru protokolu ovlivní přesnost výsledků. Podle vašeho výběru protokol také určuje, zda, je nutné provést jakékoli ruční kroky po nasazení řešení Network Performance Monitor. 

Network Performance Monitor vám nabízí možností volby mezi protokoly ICMP a TCP pro syntetické transakce provádění. Pokud vyberete možnost protokolu ICMP, když vytvoříte pravidlo syntetické transakce, agenti Network Performance Monitor využívat zprávy ODEZVU ICMP pro výpočet latence sítě a ztráta paketů. ODEZVU ICMP používá stejnou zprávu, která nástroj ping konvenční odesílají. Použijete-li jako protokol TCP, bude Network Performance Monitor agenty v síti odesílat TCP SYN pakety. Tento krok je následována dokončení ověření typu handshake protokolu TCP a odebrání připojení RVNÍ pakety. 

Než se rozhodnete protokol vezměte v úvahu následující informace: 

* **Zjišťování několik síťových tras.** TCP je přesnější při zjištění několik tras a vyžaduje menší počet agentů v každé podsíti. Například jeden nebo dva agenty, které používají protokol TCP najdou všechny redundantní cesty mezi podsítěmi. Budete potřebovat několik agentů, které podobného výsledku dosáhnout pomocí protokolu ICMP. Pomocí protokolu ICMP, pokud máte několik tras mezi dvěma podsítěmi, budete potřebovat vyšší než u agentů 5N ve zdrojové nebo cílové podsíti.

* **Přesnost výsledků.** Síťové směrovače a přepínače mají nižší prioritou přiřadit ODEZVU ICMP pakety ve srovnání s pakety protokolu TCP. V některých situacích Pokud síťová zařízení jsou silně zatížen, dat získaná TCP lépe odráží ztrát a latence mají aplikace. K tomu dochází, protože většina provozu aplikace toky přes protokol TCP. V takových případech ICMP poskytuje méně přesné výsledky ve srovnání s TCP. 

* **Konfigurace brány firewall.** Protokolu TCP vyžaduje, aby TCP pakety odesílají na cílový port. Výchozí port je používán agenty Network Performance Monitor je 8084. Při konfiguraci agentů můžete port změnit. Ujistěte se, že síťové brány firewall nebo pravidlech skupiny (NSG) zabezpečení sítě (v Azure) umožňují přenosy na portu. Budete také muset Ujistěte se, že je místní brány firewall na počítačích, kde jsou nainstalovaní agenti nakonfigurovat pro povolení provozu na tomto portu. Konfigurace pravidel brány firewall na počítačích s Windows můžete použít skripty prostředí PowerShell, ale budete muset ručně konfigurovat bránu firewall vaší sítě. Naproti tomu ICMP nepracuje pomocí portu. Ve většině scénářů organizace je povolený provoz protokolu ICMP přes bránu firewall k tomu, abyste při použití nástroje pro diagnostiku sítě jako nástroj ping. Pokud odešlete zprávu ping z jiného jeden počítač, můžete použít protokol ICMP bez nutnosti ruční konfigurace brány firewall.

>[!NOTE] 
> Některé brány firewall může blokovat ICMP, což může vést k opakování, jehož výsledkem velký počet událostí v zabezpečení informací a událostí systému pro správu. Ujistěte se, že není síťová brána firewall nebo NSG blokují protokol, kterou zvolíte. V opačném případě Network Performance Monitor nelze sledovat segmentu sítě. Doporučujeme použít TCP pro monitorování. Použijte ICMP ve scénářích, kdy nejde používat TCP, třeba při: 
>
> - Použijete uzly na základě klienta Windows, protože nezpracovaná sockety TCP nejsou povoleny v klientech Windows.
> - Síťová brána firewall nebo NSG blokuje TCP.
> - Nevíte, jak přepnout na protokol.

Pokud jste se rozhodli použít protokol ICMP během nasazení, můžete přepnout na TCP kdykoli tak, že upravíte výchozí pravidlo monitorování.

1. Přejděte na **výkon sítě** > **monitorování** > **nakonfigurovat**   >  **Monitorování**. Potom vyberte **výchozí pravidlo**. 
2. Přejděte **protokol** části a vyberte protokol, který chcete použít. 
3. Vyberte **Uložit** použijte nastavení. 

I když výchozí pravidlo používá určitý protokol, můžete vytvořit nová pravidla s jiným protokolem. Můžete dokonce vytvořit kombinaci pravidla, kde některá pravidla používat protokol ICMP a ostatní použití protokolu TCP. 

## <a name="walkthrough"></a>Názorný postup 

Nyní se podívejte na jednoduché vyšetřování původní příčinu stavu události.

Na řídicím panelu řešení událost stavu ukazuje, že síťové propojení není v pořádku. K prošetření problému, vyberte **sítě propojuje, monitoruje** dlaždici.

Na stránce procházení ukazuje, že **DMZ2 DMZ1** síťového propojení není v pořádku. Vyberte **zobrazit odkazy na podsítě** pro toto síťové propojení. 


Na stránce procházení podrobností se zobrazí všechna propojení podsítí ve **DMZ2 DMZ1** síťové propojení. Latence pro oba odkazy na podsítě, překročí prahovou hodnotu, takže síťového propojení není v pořádku. Také můžete zobrazit trendy latence z obou odkazy na podsítě. Použití v grafu a zaměřte se na požadovaný časový rozsah ovládacím prvku Výběr času. Zobrazí se čas při dosažení latence jeho ve špičce. Hledání protokolů později pro toto časové období k prošetření problému. Vyberte **zobrazit odkazy na uzly** k podrobnostem. 
 
 ![Stránka odkazy podsítě.](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Podobně jako na předchozí stránku, na stránce procházení pro odkaz na konkrétní podsíť uvedeny odkazy na jeho základní uzel. Můžete provádět akce podobné tady, jako jste to udělali v předchozím kroku. Vyberte **zobrazení topologie** zobrazíte topologie mezi dvěma uzly. 
 
 ![Stránka uzel propojení](media/network-performance-monitor-performance-monitor/node-links.png) 

Všechny cesty mezi dvěma vybrané uzly jsou zobrazeny na mapě topologie. Můžete vizualizovat hop-by-hop topologii tras mezi dvěma uzly na mapě topologie. Poskytuje přehledné informace o tom, kolik tras mezi dva uzly a jaká cesty trvat datových paketů. Kritické body výkonu sítě se zobrazí červeně. K vyhledání připojení k vadné síti nebo chybného síťového zařízení, podívejte se na červené prvků na mapě topologie. 

 ![Řídicí panel topologie pomocí mapy topologie](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Můžete zkontrolovat ztrátu, latenci a počet segmentů směrování v každé cesty v **akce** podokně. Chcete-li zobrazit podrobnosti cesty není v pořádku pomocí posuvníku. Pomocí filtrů vybrat cesty s není v pořádku segment směrování tak, aby se vykreslí topologie pro pouze vybrané cesty. Chcete-li zvětšit nebo zmenšit mapu, použijte kolečko myši. 

Na následujícím obrázku hlavní příčinu problémových oblastí pro konkrétní části sítě zobrazí v red cesty a segmentů směrování. Vyberte uzel na mapě topologie zobrazíte vlastnosti uzlu, který obsahuje plně kvalifikovaný název domény a IP adresu. Výběr hop zobrazuje IP adresu směrování. 
 
![Mapu s vybrané vlastnosti uzlu](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Další postup
[Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) zobrazíte podrobné sítě výkonu datových záznamů.
