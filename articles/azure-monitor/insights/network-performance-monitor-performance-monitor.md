---
title: Sledování výkonu v monitoru výkonu sítě
description: Funkce Sledování výkonu v programu Sledování výkonu sítě umožňuje sledovat připojení k síti v různých bodech sítě. Můžete monitorovat cloudová nasazení a místní umístění, více datových center a poboček a kritické vícevrstvé aplikace nebo mikroslužby.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 126cca9d3606b378e59e4f4e1c5b52d985d19d94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055689"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Řešení sledování výkonu sítě: Sledování výkonu

Funkce Sledování výkonu v [programu Sledování výkonu sítě](network-performance-monitor.md) umožňuje sledovat připojení k síti v různých bodech sítě. Můžete monitorovat cloudová nasazení a místní umístění, více datových center a poboček a kritické vícevrstvé aplikace nebo mikroslužby. Pomocí funkce Sledování výkonu můžete zjistit problémy se sítí dříve, než si uživatelé stěžují. Hlavní výhodou je, že můžete: 

- Sledujte ztráty a latence v různých podsítích a nastavte výstrahy.
- Sledujte všechny cesty (včetně redundantních cest) v síti.
- Řešení potíží s přechodným problémem se sítí a problémy se sítí v okamžiku, které je obtížné replikovat.
- Určete konkrétní segment v síti, který je zodpovědný za snížený výkon.
- Sledujte stav sítě bez nutnosti protokolu SNMP.


![Sledování výkonu sítě](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurace
Chcete-li otevřít konfiguraci nástroje Sledování výkonu sítě, otevřete [řešení Sledování výkonu sítě](network-performance-monitor.md)a vyberte **konfigurovat**.

![Konfigurace Network Performance Monitoru](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Vytvoření nových sítí

Síť v programu Sledování výkonu sítě je logický kontejner pro podsítě. Pomáhá vám organizovat monitorování síťové infrastruktury podle vašich potřeb. Můžete vytvořit síť s popisným názvem a přidat do ní podsítě podle obchodní logiky. Můžete například vytvořit síť s názvem Londýn a přidat všechny podsítě v datovém centru v Londýně. Nebo můžete vytvořit síť s názvem *ContosoFrontEnd* a přidat do této sítě všechny podsítě s názvem Contoso, které slouží front-end vaší aplikace. Řešení automaticky vytvoří výchozí síť, která obsahuje všechny podsítě zjištěné ve vašem prostředí. 

Při každém vytvoření sítě do ní přidáte podsíť. Potom je tato podsíť odebrána z výchozí sítě. Pokud odstraníte síť, všechny její podsítě se automaticky vrátí do výchozí sítě. Výchozí síť funguje jako kontejner pro všechny podsítě, které nejsou obsaženy v žádné uživatelem definované síti. Výchozí síť nelze upravit ani odstranit. Vždy zůstává v systému. Můžete vytvořit libovolný počet vlastních sítí. Ve většině případů jsou podsítě ve vaší organizaci uspořádány do více než jedné sítě. Vytvořte jednu nebo více sítí a seskupte podsítě pro obchodní logiku.

Vytvoření nové sítě:


1. Vyberte kartu **Sítě.**
1. Vyberte **Přidat síť**a zadejte název a popis sítě. 
2. Vyberte jednu nebo více podsítí a pak vyberte **Přidat**. 
3. Chcete-li uložit konfiguraci, vyberte **uložit.** 


### <a name="create-monitoring-rules"></a>Vytvoření pravidel monitorování 

Sledování výkonu generuje události stavu při překročení prahové hodnoty výkonu síťových připojení mezi dvěma podsítěmi nebo mezi dvěma sítěmi. Systém může tyto prahové hodnoty naučit automaticky. Můžete také zadat vlastní prahové hodnoty. Systém automaticky vytvoří výchozí pravidlo, které generuje událost stavu, kdykoli ztráta nebo latence mezi libovolnou dvojicí síťových nebo podsíťových propojení překročí prahovou hodnotu nazdu systému. Tento proces pomáhá řešení sledovat vaši síťovou infrastrukturu, dokud jste explicitně nevytvořili žádná pravidla monitorování. Pokud je povoleno výchozí pravidlo, všechny uzly odesílají syntetické transakce do všech ostatních uzlů, které jste povolili pro monitorování. Výchozí pravidlo je užitečné u malých sítí. Příkladem je scénář, kde máte malý počet serverů, na kterých je spuštěna mikroslužba, a chcete se ujistit, že všechny servery mají připojení k sobě navzájem.

>[!NOTE]
> Doporučujeme zakázat výchozí pravidlo a vytvořit vlastní pravidla monitorování, zejména u velkých sítí, kde používáte velký počet uzlů pro monitorování. Vlastní pravidla monitorování mohou snížit provoz generovaný řešením a pomoci vám uspořádat monitorování sítě.

Vytvořte pravidla monitorování podle obchodní logiky. Příkladem je, pokud chcete sledovat výkon síťového připojení dvou kancelářských lokalit k ústředí. Seskupte všechny podsítě v kancelářské lokalitě1 v síti O1. Potom seskupte všechny podsítě v kancelářském webu2 do sítě O2. Nakonec seskupte všechny podsítě v centrále v síti H. Vytvořte dvě pravidla monitorování – jednu mezi O1 a H a druhou mezi O2 a H. 

Vytvoření vlastních pravidel monitorování:

1. Na kartě **Monitor** vyberte **Přidat pravidlo** a zadejte název a popis pravidla.
2. Ze seznamů vyberte dvojici propojení sítě nebo podsítě, která chcete sledovat. 
3. V rozevíracím seznamu sítě vyberte síť obsahující požadované podsítě. Potom vyberte podsítě z příslušného rozevíracího seznamu podsítě. Chcete-li sledovat všechny podsítě v síťovém propojení, vyberte **možnost Všechny podsítě**. Podobně vyberte další požadované podsítě. Chcete-li vyloučit monitorování konkrétních propojení podsítí z provedených výběrů, vyberte **možnost Přidat výjimku**. 
4. Vyberte si mezi protokoly ICMP a TCP pro provádění syntetických transakcí. 
5. Pokud nechcete vytvářet události stavu pro vybrané položky, zrušte zaškrtnutí **políčka Povolit sledování stavu u odkazů, na které se vztahuje toto pravidlo**. 
6. Zvolte podmínky monitorování. Chcete-li nastavit vlastní prahové hodnoty pro generování událostí stavu, zadejte prahové hodnoty. Vždy, když hodnota podmínky překročí vybranou prahovou hodnotu pro vybranou síť nebo dvojici podsítě, je vygenerována událost stavu. 
7. Chcete-li uložit konfiguraci, vyberte **uložit.** 

Po uložení pravidla monitorování můžete toto pravidlo integrovat se správou výstrah výběrem **možnosti Vytvořit výstrahu**. Pravidlo výstrahy je automaticky vytvořeno pomocí vyhledávacího dotazu. Další požadované parametry jsou automaticky vyplněny. Pomocí pravidla výstrahy můžete kromě existujících výstrah v rámci sledování výkonu sítě přijímat výstrahy založené na e-mailech. Výstrahy mohou také aktivovat nápravné akce se sadami Runbook nebo se mohou integrovat s existujícími řešeními správy služeb pomocí webhooků. Chcete-li upravit nastavení výstrah, vyberte **Spravovat výstrahy.** 

Nyní můžete vytvořit další pravidla sledování výkonu nebo přejít na řídicí panel řešení a použít tuto funkci.

### <a name="choose-the-protocol"></a>Zvolte protokol

Sledování výkonu sítě používá syntetické transakce k výpočtu metrik výkonu sítě, jako je ztráta paketů a latence propojení. Chcete-li tento koncept lépe pochopit, zvažte agenta sledování výkonu sítě připojeného k jednomu konci síťového propojení. Tento agent sledování výkonu sítě odesílá pakety sondy druhému agentovi sledování výkonu sítě připojenému k jinému konci sítě. Druhý agent odpoví pakety odpovědi. Tento proces se opakuje několikrát. Měřením počtu odpovědí a doby, která byla přijata každá odpověď, první agent sledování výkonu sítě vyhodnotí latenci propojení a poklesy paketů. 

Formát, velikost a posloupnost těchto paketů je určena protokolem, který zvolíte při vytváření pravidel monitorování. Na základě protokolu paketů mohou zprostředkující síťová zařízení, například směrovače a přepínače, zpracovávat tyto pakety odlišně. V důsledku toho vaše volba protokolu ovlivňuje přesnost výsledků. Volba protokolu také určuje, zda je nutné provést jakékoli ruční kroky po nasazení řešení sledování výkonu sítě. 

Nástroj Sledování výkonu sítě nabízí možnost volby mezi protokoly ICMP a TCP pro provádění syntetických transakcí. Pokud při vytváření pravidla syntetické transakce zvolíte ICMP, agenti sledování výkonu sítě použijí zprávy ICMP ECHO k výpočtu latence sítě a ztráty paketů. ICMP ECHO používá stejnou zprávu, která je odeslána konvenční ping utility. Použijete-li jako protokol protokol protokol TCP, agenti sledování výkonu sítě odesílají pakety TCP SYN po síti. Po tomto kroku následuje dokončení protokolu Handshake protokolu TCP a připojení je odebráno pomocí paketů RST. 

Než zvolíte protokol, zvažte následující informace: 

* **Zjišťování více síťových tras.** Protokol TCP je přesnější při zjišťování více tras a potřebuje méně agentů v každé podsíti. Například jeden nebo dva agenti, kteří používají protokol TCP, mohou zjistit všechny redundantní cesty mezi podsítěmi. K dosažení podobných výsledků potřebujete několik agentů, kteří používají ICMP. Pokud používáte icmp, pokud máte několik tras mezi dvěma podsítěmi, potřebujete více než 5N agentů ve zdrojové nebo cílové podsíti.

* **Přesnost výsledků.** Směrovače a přepínače mají tendenci přiřazovat méně priorit paketům ICMP ECHO ve srovnání s pakety TCP. V určitých situacích, kdy jsou síťová zařízení silně načtena, data získaná protokolem TCP lépe odrážejí ztrátu a latenci, ke které dochází u aplikací. K tomu dochází, protože většina přenosů aplikací toky přes TCP. V takových případech icmp poskytuje méně přesné výsledky ve srovnání s TCP. 

* **Konfigurace brány firewall.** Protokol TCP vyžaduje, aby byly pakety TCP odesílány do cílového portu. Výchozí port používaný agenty sledování výkonu sítě je 8084. Port můžete změnit při konfiguraci agentů. Ujistěte se, že vaše síťové brány firewall nebo pravidla skupiny zabezpečení sítě (NSG) (v Azure) umožňují provoz na portu. Musíte se také ujistit, že místní brána firewall v počítačích, ve kterých jsou agenti nainstalováni, je nakonfigurována tak, aby umožňovala přenosy na tomto portu. Pomocí skriptů prostředí PowerShell můžete konfigurovat pravidla brány firewall v počítačích se systémem Windows, ale je třeba nakonfigurovat síťovou bránu firewall ručně. Naproti tomu ICMP nefunguje pomocí portu. Ve většině podnikových scénářů je přenos icmp povolen prostřednictvím bran firewall, které umožňují používat nástroje pro diagnostiku sítě, jako je nástroj ping. Pokud můžete ping jeden počítač z jiného, můžete použít protokol ICMP bez nutnosti konfigurovat brány firewall ručně.

>[!NOTE] 
> Některé brány firewall mohou blokovat protokol ICMP, což může vést k opakovanému přenosu, který má za následek velký počet událostí v systému správy informací o zabezpečení a událostí. Ujistěte se, že protokol, který zvolíte, není blokován síťovým firewallem nebo sítí zabezpečení sítě. V opačném případě program Sledování výkonu sítě nemůže sledovat segment sítě. Doporučujeme použít TCP pro monitorování. IcMP použijte ve scénářích, kde nelze použít protokol TCP, například když: 
>
> - Používáte uzly systému Windows založené na klientech, protože nezpracované sokety TCP nejsou v klientech systému Windows povoleny.
> - Síťová brána firewall nebo nsg blokuje protokol TCP.
> - Nevíte, jak změnit protokol.

Pokud jste se rozhodli používat protokol ICMP během nasazení, můžete kdykoli přepnout na protokol TCP úpravou výchozího pravidla monitorování.

1. Přejděte na **program Sledování** > **konfigurace programu Sledování výkonu** > **Monitor** > **sítě**. Pak vyberte **Výchozí pravidlo**. 
2. Přejděte do části **Protokol** a vyberte protokol, který chcete použít. 
3. Vyberte **Uložit,** chcete-li použít nastavení. 

I v případě, že výchozí pravidlo používá určitý protokol, můžete vytvořit nová pravidla s jiným protokolem. Můžete dokonce vytvořit kombinaci pravidel, kde některá pravidla používají ICMP a jiné používají TCP. 

## <a name="walkthrough"></a>Názorný postup 

Nyní se podívejte na jednoduché vyšetřování příčiny zdravotní události.

Na řídicím panelu řešení událost stavu ukazuje, že síťové propojení není v pořádku. Chcete-li problém prozkoumat, vyberte dlaždici **Síťová propojení, která je monitorována.**

Přechodka na stránku ukazuje, že propojení sítě **DMZ2-DMZ1** není v pořádku. Vyberte **Zobrazit propojení podsítí** pro toto síťové propojení. 


Na stránce přechodu k podrobnostem jsou zobrazeny všechny odkazy na podsíť v síťovém spojení **DMZ2-DMZ1.** U obou propojení podsítě překročila latence prahovou hodnotu, což způsobí, že síťové propojení není v pořádku. Můžete také zobrazit trendy latence obou propojení podsítě. Pomocí ovládacího prvku pro výběr času v grafu se můžete zaměřit na požadovaný časový rozsah. Můžete vidět denní dobu, kdy latence dosáhla svého vrcholu. Prohledejte protokoly později pro toto časové období a prozkoumejte problém. Chcete-li přejít k podrobnostem, vyberte **možnost Zobrazit odkazy uzlů.** 
 
 ![Stránka Odkazy podsíťových sítí](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Podobně jako předchozí stránka obsahuje stránka podrobností pro konkrétní propojení podsítě odkazy na jednotlivé jednotlivé uzla. Zde můžete provádět podobné akce jako v předchozím kroku. Výběrem **možnosti Zobrazit topologii** zobrazíte topologii mezi dvěma uzly. 
 
 ![Stránka Odkazy uzlů](media/network-performance-monitor-performance-monitor/node-links.png) 

Všechny cesty mezi dvěma vybranými uzly jsou vykresleny v mapě topologie. Můžete vizualizovat topologii směrování mezi dvěma uzly na mapě topologie. Poskytuje jasný přehled o tom, kolik tras existuje mezi dvěma uzly a jaké cesty datové pakety trvat. Kritická místa výkonu sítě jsou zobrazena červeně. Chcete-li vyhledat vadné síťové připojení nebo vadné síťové zařízení, podívejte se na červené prvky na mapě topologie. 

 ![Řídicí panel topologie s mapou topologie](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Můžete zkontrolovat ztrátu, latenci a počet směrování v každé cestě v podokně **akcí.** Pomocí posuvníku zobrazíte podrobnosti o cestách není v pořádku. Pomocí filtrů vyberte cesty s nefunkčním směrováním tak, aby byla vykreslována topologie pouze pro vybrané cesty. Chcete-li mapu topologie přiblížit nebo oddálit, použijte kolečko myši. 

Na následujícím obrázku se v červených cestách a směrování zobrazí hlavní příčina problémových oblastí v určité části sítě. Vyberte uzel v mapě topologie, chcete-li odhalit vlastnosti uzlu, který zahrnuje hlavní kvit ový kvit a IP adresu. Výběrem směrování se zobrazí IP adresa směrování. 
 
![Mapa topologie s vybranými vlastnostmi uzlu](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Další kroky
[Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) pro zobrazení podrobných záznamů dat o výkonu sítě.
