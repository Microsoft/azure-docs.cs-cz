---
title: Nejčastější dotazy k Azure Monitor pro virtuální počítače (Preview) | Microsoft Docs
description: Odpovědi na časté otázky týkající se Azure Monitor pro virtuální počítače, které monitorují stav a výkon virtuálních počítačů Azure Kromě automatického zjišťování a mapování součástí aplikace a jejich závislostí.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/09/2018
ms.openlocfilehash: 579538996e934c7068c397a284d819f5ddb92f08
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305461"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Nejčastější dotazy k Azure Monitor pro virtuální počítače (Preview)
Toto je seznam nejčastějších dotazů k Azure Monitor pro virtuální počítače. Pokud máte další dotazy týkající se řešení, navštivte [diskuzní fórum](https://feedback.azure.com/forums/34192--general-feedback) a publikujte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Můžu se připojit k existujícímu pracovnímu prostoru?
Pokud jsou vaše virtuální počítače už připojené k pracovnímu prostoru Log Analytics, můžete tento pracovní prostor i nadále používat při připojování k Azure Monitor pro virtuální počítače za předpokladu, že je v některé z podporovaných oblastí, které jsou [tady](vminsights-enable-overview.md#prerequisites)uvedené.

Při připojování konfigurujeme čítače výkonu pro pracovní prostor, které způsobí, že všechny virtuální počítače nahlásí data do pracovního prostoru a začnou shromažďovat tyto informace pro zobrazení a analýzu v Azure Monitor pro virtuální počítače.  Výsledkem je, že se zobrazí data o výkonu ze všech virtuálních počítačů připojených k vybranému pracovnímu prostoru.  Funkce stavu a mapy jsou povolené jenom pro virtuální počítače, které jste zavedli k zprovoznění.

Další informace o tom, které čítače výkonu jsou povoleny, najdete v článku o [Povolení přehledu](vminsights-enable-overview.md#performance-counters-enabled) .

## <a name="can-i-onboard-to-a-new-workspace"></a>Můžu se připojit k novému pracovnímu prostoru? 
Pokud vaše virtuální počítače nejsou aktuálně připojené k existujícímu pracovnímu prostoru Log Analytics, je potřeba vytvořit nový pracovní prostor pro ukládání vašich dat. Vytvoření nového výchozího pracovního prostoru se provádí automaticky, pokud nakonfigurujete jeden virtuální počítač Azure pro Azure Monitor pro virtuální počítače prostřednictvím Azure Portal.

Pokud se rozhodnete použít metodu založenou na skriptu, tyto kroky jsou pokryté v článku [povolení Azure monitor pro virtuální počítače (Preview) pomocí Azure PowerShell nebo šablony Správce prostředků](vminsights-enable-at-scale-powershell.md) . 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Jak mám dělat, když už můj virtuální počítač hlásí stávající pracovní prostor?
Pokud už data z virtuálních počítačů shromažďujete, je možné, že jste ji už nakonfigurovali k nahlášení dat do existujícího pracovního prostoru Log Analytics.  Pokud je tento pracovní prostor v některé z našich podporovaných oblastí, můžete Azure Monitor pro virtuální počítače do tohoto existujícího pracovního prostoru povolit.  Pokud pracovní prostor, který už používáte, není v některé z našich podporovaných oblastí, nebudete moct Azure Monitor pro virtuální počítače v tuto chvíli zaregistrovat.  Aktivně pracujeme na podporu dalších oblastech.

>[!NOTE]
>Pro pracovní prostor konfigurujeme čítače výkonu, které mají vliv na všechny virtuální počítače, které jsou v pracovním prostoru nahlášené, bez ohledu na to, jestli jste se rozhodli Azure Monitor pro virtuální počítače. Další informace o tom, jak jsou pro pracovní prostor nakonfigurované čítače výkonu, najdete v naší [dokumentaci](../../azure-monitor/platform/data-sources-performance-counters.md). Informace o čítačích konfigurovaných pro Azure Monitor pro virtuální počítače najdete v našem článku o [povolení Azure monitor pro virtuální počítače](vminsights-enable-overview.md#performance-counters-enabled) .  

## <a name="why-did-my-vm-fail-to-onboard"></a>Proč se můj virtuální počítač nepovedlo zařadit do provozu?
Při připojování virtuálního počítače Azure z Azure Portal dojde k následujícím krokům:

* Výchozí pracovní prostor Log Analytics se vytvoří, pokud, který jste vybrali možnost.
* Čítače výkonu jsou nakonfigurovány pro vybraný pracovní prostor. Pokud tento krok neproběhne úspěšně, Všimněte si, že některé grafy a tabulky výkonu nezobrazuje data pro virtuální počítač, který jste připojili. To můžete vyřešit spuštěním skriptu prostředí PowerShell, který je [zde](vminsights-enable-at-scale-powershell.md#enable-performance-counters)popsán.
* Agent Log Analytics je nainstalovaný na virtuálních počítačích Azure pomocí rozšíření virtuálního počítače, pokud je to potřeba.  
* Agent závislostí Azure Monitor pro virtuální počítače se nainstaluje na virtuální počítače Azure s použitím rozšíření, pokud je stanovený jako povinný.  
* V případě potřeby jsou nakonfigurovány komponenty Azure Monitor podporující funkci stavu a virtuální počítač je nakonfigurován tak, aby hlásil údaje o stavu.

Během procesu připojení zkontrolujeme stav každé z výše uvedených a vrátíte na portálu stav oznámení. Konfigurace pracovního prostoru a instalace agenta obvykle trvá 5 až 10 minut. Zobrazení dat monitorování a stavu na portálu trvá dalších 5 až 10 minut.  

Pokud jste zahájili registraci a viděli zprávy indikující, že virtuální počítač musí být zaregistrované, počkejte až 30 minut, než virtuální počítač dokončí proces. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Můžu povolit jenom Azure Monitor pro virtuální počítače, proč se zobrazí všechny virtuální počítače monitorované funkcí stavu?
Funkce Health je povolená pro všechny virtuální počítače, které jsou připojené k pracovnímu prostoru Log Analytics, a to i v případě, že je akce inicializovaná pro jeden virtuální počítač.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Můžu upravit plán, kdy si kritéria pro stav vyhodnotí podmínku?
Ne, časové období a četnost kritérií pro stav nelze upravovat v této verzi. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Můžu zakázat kritéria stavu pro podmínku, že nepotřebujete monitorovat?
Kritéria stavu nejde v této verzi zakázat.

## <a name="are-the-health-alert-severities-configurable"></a>Jsou závažnost upozornění na stav konfigurovatelné?  
Závažnost upozornění na stav nelze změnit, lze ji povolit nebo zakázat pouze. V závislosti na stavu kritérií stavu se navíc některá Závažnost výstrahy aktualizují. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Pokud překonfigurujem nastavení konkrétního kritéria stavu, může být vymezeno na určitou instanci?  
Pokud upravíte jakékoli nastavení instance kritéria stavu, upraví se všechny instance všech kritérií stavu stejného typu na virtuálním počítači Azure. Například pokud se změní prahovou hodnotu instance kritérium stavu volného místa disku, který odpovídá logický disk C:, tato prahová hodnota se vztahuje na všechny ostatní logické disky, které zjišťování a monitorování pro stejný virtuální počítač.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Monitoruje funkce stavu logické procesory a jádra?
Žádná kritéria stavu na úrovni jednotlivých procesorů a logických procesorů nejsou pro Windows zahrnutá, ve výchozím nastavení se monitoruje jenom celkové využití procesoru, aby se účinně vyhodnotilo zatížení procesoru na základě celkového počtu logických procesorů dostupných pro virtuální počítač Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Konfigurovatelné jsou všechny prahové hodnoty kritérií stavu?  
Prahové hodnoty pro kritéria, které se zaměřují virtuálního počítače s Windows nejsou upravitelné, protože jejich stavů jsou nastaveny na *systémem* nebo *dostupné*. Při dotazování stav z [úlohy monitorování API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), zobrazí *comparisonOperator* hodnotu **LessThan** nebo **GreaterThan** s *prahová hodnota* hodnotu **4** služby nebo entity pokud:
   - Stav služby Klient DNS – služba není spuštěná. 
   - Stav služby Klient DHCP – služba není spuštěná. 
   - Stav služby vzdáleného volání Procedur – služba není spuštěná. 
   - Stav služby Windows firewall – služba není spuštěná.
   - Stav služby protokolu událostí Windows – služba není spuštěná. 
   - Stav služby serveru – služba není spuštěná. 
   - Stav služby vzdálené správy Windows – služba není spuštěná. 
   - Chyba systému souborů nebo poškození – je logický Disk není k dispozici.

Prahové hodnoty pro následující stav kritéria Linux nejsou upravitelné, protože jeho stav je již nastavena na *true*. Stav se zobrazí *comparisonOperator* s hodnotou **LessThan** a *prahová hodnota* hodnotu **1** při posílat dotaz z Úloha monitorování rozhraní API pro entitu, v závislosti na jeho kontextu:
   - Stav logického disku – logický disk není online / k dispozici
   - Stav disku – Disk není online / k dispozici
   - Stav síťového adaptéru – síťový adaptér je zakázána.

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Návody upravit výstrahy, které jsou součástí funkce Health?
Pravidla výstrah, které jsou definovány pro každé kritérium stavu se nezobrazují na portálu Azure portal. Můžete povolit nebo zakázat upozornění na stav pouze v pravidlo [úlohy monitorování rozhraní API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Nemůžete také přiřadit [Azure monitor skupinu akcí](../../azure-monitor/platform/action-groups.md) pro výstrahy týkající se stavu v Azure Portal. Pomocí rozhraní API pro nastavení oznámení můžete nakonfigurovat skupinu akcí, která se aktivuje při každém vyvolání výstrahy na stav. V současné době můžete přiřadit skupiny akcí na virtuálním počítači tak, aby všechny *výstrahy týkající se stavu* aktivuje proti aktivační událost virtuální počítač stejné skupiny akcí. Na rozdíl od tradičních upozornění v Azure neexistuje koncept skupiny samostatnou akci pro každé pravidlo výstrahy stavu. Kromě toho pouze skupiny akcí, které jsou nakonfigurované na poskytování e-mailu nebo oznámení SMS jsou podporovány, když se aktivuje upozornění na stav. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Se mi nezobrazují některé nebo všech dat v grafech výkonu pro virtuální počítač
Pokud nevidíte údaje o výkonu v tabulce disků nebo v některém z grafů výkonu, nemusí být v pracovním prostoru nakonfigurovány čítače výkonu. Pokud ho chcete vyřešit, spusťte následující [skript PowerShellu](vminsights-enable-at-scale-powershell.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Jak se Azure Monitor pro virtuální počítače funkce map liší od Service Map?
Funkce mapy Azure Monitor pro virtuální počítače je založena na Service Map, ale má následující rozdíly:

* Zobrazení mapy je dostupné z okna virtuálního počítače a z Azure Monitor pro virtuální počítače v části Azure Monitor.
* Připojení na mapě teď lze kliknout a zobrazit zobrazení dat metriky připojení na bočním panelu pro vybrané připojení.
* K dispozici je nové rozhraní API, které slouží k vytváření map pro lepší podporu složitějších map.
* Monitorované virtuální počítače jsou teď součástí uzlu skupiny klientů a prstencový graf zobrazuje poměr monitorovaných a nemonitorovaných virtuálních počítačů ve skupině.  Můžete ji také použít k filtrování seznamu počítačů při rozbalení skupiny.
* Monitorované virtuální počítače jsou teď zahrnuté do uzlů skupiny portů serveru a prstencový graf zobrazuje poměr monitorovaných a nemonitorovaných počítačů ve skupině.  Můžete ji také použít k filtrování seznamu počítačů při rozbalení skupiny.
* Styl mapy byl aktualizován tak, aby byl s mapou aplikace z Application Insights lépe konzistentní.
* Postranní panely byly aktualizovány a nemají kompletní sadu integrace, které byly podporovány v Service Map-Update Management, Change Tracking, zabezpečení a oddělení služeb. 
* Možnost výběru skupin a počítačů na mapě byla aktualizována a teď podporuje předplatná, skupiny prostředků, službu Azure Virtual Machine Scale Sets a cloudové služby.
* Ve funkci mapování Azure Monitor pro virtuální počítače nelze vytvořit nové Service Map skupiny počítačů.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Proč se Moje grafy výkonu zobrazit tečkované čáry
K tomu může dojít z několika důvodů.  V případech, kdy existuje mezera v kolekci dat, můžeme řádky považovat za tečky.  Pokud jste upravili četnost vzorkování dat pro povolené čítače výkonu (výchozí nastavení je shromažďování dat každých 60 sekund), můžete zobrazit tečkované řádky v grafu, pokud vyberete úzký časový rozsah grafu a frekvence vzorkování je menší než velikost intervalu použitá v grafu (například frekvence vzorkování je každých 10 minut a každá sada v grafu je 5 minut).  Když zvolíte širší časový rozsah, který se má zobrazit, musí se čáry grafu zobrazovat jako plné čáry místo teček v tomto případě.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Skupiny podporovaných službou Azure Monitor pro virtuální počítače?
Ano, Jakmile nainstalujete agenta závislostí, shromáždíme informace z virtuálních počítačů pro zobrazení skupin na základě předplatného, skupiny prostředků, sady škálování virtuálních počítačů a cloudových služeb.  Pokud jste používali Service Map a vytvořili jste skupiny počítačů, zobrazí se také.  Skupiny počítačů se zobrazí také ve filtru skupiny, pokud jste je vytvořili pro pracovní prostor, který si prohlížíte. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Návody se zobrazí podrobnosti o tom, co řídí řádek 95. percentilu v agregovaných grafech výkonu?
Ve výchozím nastavení je seznam seřazený tak, aby se zobrazily virtuální počítače s nejvyšší hodnotou 95. percentilu pro vybranou metriku, s výjimkou dostupného grafu paměti, který zobrazuje počítače s nejnižší hodnotou 5. percentilu.  Když kliknete na graf, otevře se zobrazení **horních seznamů** s odpovídající vybranou metrikou.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkce map zpracovává duplicitní IP adresy napříč různými virtuální sítě a podsítěmi?
Pokud duplikujete rozsahy IP adres buď pomocí virtuálních počítačů, nebo virtuálních počítačů Azure pro škálování v podsítích a virtuální sítě, může to způsobit, že Azure Monitor pro virtuální počítače mapa zobrazí nesprávné informace. Jedná se o známý problém a chystáme se prozkoumat možnosti pro zlepšení tohoto prostředí.

## <a name="does-map-feature-support-ipv6"></a>Podporuje funkce mapy protokol IPv6?
Funkce map aktuálně podporuje jenom protokol IPv4 a prozkoumáme podporu pro protokol IPv6. Podporujeme také protokol IPv4, který je tunelem v rámci protokolu IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Když nahrajem mapu pro skupinu prostředků nebo jinou velkou skupinu, může se mapa obtížně zobrazit.
I když jsme vylepšili mapování na zpracování rozsáhlých a složitých konfigurací, uvědomujeme si, že mapa může mít spoustu uzlů, připojení a uzel funguje jako cluster.  Naší prioritou je pokračováním vylepšit podporu o zvýšení škálovatelnosti.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Proč se síťový graf na kartě výkon liší od síťového grafu na stránce Přehled virtuálního počítače Azure?

Stránka s přehledem pro virtuální počítač Azure zobrazuje grafy založené na měření aktivity hostitele v hostovaném virtuálním počítači.  V článku Přehled síťového grafu na virtuálním počítači Azure se zobrazí jenom síťový provoz, který se bude fakturovat.  To nezahrnuje provoz mezi virtuálními sítěmi.  Data a grafy zobrazené pro Azure Monitor pro virtuální počítače jsou založené na datech z virtuálního počítače hosta a v síťovém grafu se zobrazují všechny přenosy protokolu TCP/IP, které jsou příchozí a odchozí pro tento virtuální počítač, včetně mezi virtuálními sítěmi.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Jak je měřena doba odezvy pro data uložená v VMConnection a zobrazená na panelu připojení a sešitech?

Doba odezvy je aproximace. Vzhledem k tomu, že kód aplikace neznáte, nevíte ve skutečnosti, kdy je zahájena žádost a kdy odpověď dorazí. Místo toho sledujeme data odesílaná na připojení a potom se data znovu přidávají do tohoto připojení. Náš agent udržuje přehled o těchto odesláních a pokusůch o jejich párování: sekvence odeslání, následovaná posloupností přijetí, je interpretována jako pár požadavků a odpovědí. Časování mezi těmito operacemi je doba odezvy. Bude zahrnovat latenci sítě a dobu zpracování serveru.

Tato aproximace funguje dobře u protokolů, které jsou založené na požadavcích a odpovědích: jedna žádost se na připojení doprovází a přijde jedna odpověď. Jedná se o případ HTTP (S) (bez použití kanálů), ale nevyhovuje pro jiné protokoly.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Jsou jejich omezení, pokud mám Log Analytics cenového plánu zdarma?
Pokud jste nakonfigurovali Azure Monitor s pracovním prostorem Log Analytics pomocí *bezplatné* cenové úrovně, funkce Azure monitor pro virtuální počítače map bude podporovat jenom pět připojených počítačů připojených k pracovnímu prostoru. Pokud máte pět virtuálních počítačů připojených k bezplatnému pracovnímu prostoru, odpojíte jeden z virtuálních počítačů a později se připojíte k novému VIRTUÁLNÍmu počítači, nový virtuální počítač se nemonitoruje a neprojeví se na stránce Mapa.  

V rámci této podmínky budete po otevření virtuálního počítače vyzváni v možnosti **vyzkoušet nyní** a v levém podokně vyberte **přehledy (Preview)** , i když už je ve virtuálním počítači nainstalovaná.  Nebudete ale mít k možnost, která by normálně probíhala, pokud se tento virtuální počítač nepřipojil k Azure monitor pro virtuální počítače. 

## <a name="next-steps"></a>Další kroky
Přečtěte si téma [povolení Azure monitor pro virtuální počítače](vminsights-enable-overview.md) pro pochopení požadavků a metod, které umožňují monitorování virtuálních počítačů.
