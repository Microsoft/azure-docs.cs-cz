---
title: Azure Monitor pro virtuální počítače (preview) – nejčastější dotazy | Dokumentace Microsoftu
description: Azure Monitor pro virtuální počítače je řešení v Azure, která kombinuje stavu a výkonu monitorování operačního systému virtuálního počítače Azure, stejně jako automatické zjišťování mezi součástmi aplikace a závislosti s jiným prostředkům a mapuje komunikace mezi je. Tento článek obsahuje odpovědi na běžné dotazy.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: 32f2833b4c1ba77564d5388bc080a7cb32d90201
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243769"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Azure Monitor pro virtuální počítače (preview) – nejčastější dotazy
Tato FAQ Microsoft je uveden seznam nejčastější dotazy týkající se Azure Monitor pro virtuální počítače. Pokud máte nějaké další dotazy ohledně řešení, přejděte [diskusní fórum](https://feedback.azure.com/forums/34192--general-feedback) a zveřejněte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Můžu připojit k existujícímu pracovnímu prostoru?
Pokud vaše virtuální počítače jsou již připojeni k pracovnímu prostoru Log Analytics, můžete dál používat tento pracovní prostor při připojení ke službě Azure Monitor pro virtuální počítače, za předpokladu, že je v jednom z podporovaných oblastech uvedených [tady](vminsights-onboard.md#prerequisites).

Při připojování, nakonfigurujeme čítače výkonu pro pracovní prostor, který způsobí, že všechny virtuální počítače hlásí data do pracovního prostoru zahajte shromažďování těchto informací pro zobrazení a analýza ve službě Azure Monitor pro virtuální počítače.  V důsledku toho se zobrazí data o výkonu z všechny virtuální počítače připojené k vybranému pracovnímu prostoru.  Funkce stav a mapování jsou povolené jenom pro virtuální počítače, které jste zadali, a připojit.

Další informace o výkonu, které jsou povoleny čítače, najdete v našich [připojování](vminsights-onboard.md) článku.

## <a name="can-i-onboard-to-a-new-workspace"></a>Můžu připojit k nový pracovní prostor? 
Pokud vaše virtuální počítače momentálně nejste připojení k existující pracovní prostor Log Analytics, musíte vytvořit nový pracovní prostor pro ukládání vašich dat.  Vytváří se nový výchozího pracovního prostoru se provádí automaticky při konfiguraci jednoho virtuálního počítače Azure pro monitorování Azure pro virtuální počítače na webu Azure portal.

Pokud se rozhodnete používat metodu založenou na skript, tyto kroky jsou popsané v [připojování](vminsights-onboard.md) článku. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co mám dělat, když je můj virtuální počítač už hlásí do existujícího pracovního prostoru?
Pokud jsou již shromažďování dat z vašich virtuálních počítačů, možná jste již nakonfigurovali ho na data sestavy do existujícího pracovního prostoru Log Analytics.  Za předpokladu, tento pracovní prostor je v jednom z našich podporovaných oblastí, můžete povolit monitorování Azure pro virtuální počítače do tohoto pracovního prostoru už existující.  Pokud pracovní prostor, který již používáte, není v jednom z našich podporovaných oblastí, nebudete moct připojit k Azure Monitor pro virtuální počítače v tuto chvíli.  Aktivně pracujeme na podporu dalších oblastech.

>[!NOTE]
>Nakonfigurujeme čítače výkonu pro pracovní prostor, který ovlivňuje všechny virtuální počítače hlásit do pracovního prostoru, jestli se rozhodli jste se připojit je na platformě Azure Monitor pro virtuální počítače. Podrobné informace o konfiguraci čítače výkonu pracovního prostoru najdete na naší [dokumentaci](../../azure-monitor/platform/data-sources-performance-counters.md). Informace o čítačích nakonfigurovaná pro monitorování Azure pro virtuální počítače, naleznete v našem [najdete dokumentaci k onboardingu](vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Proč můj virtuální počítač nezdařilo se připojení?
Při připojování virtuálního počítače Azure na webu Azure Portal, jsou provedeny následující kroky:

* Výchozí pracovní prostor Log Analytics se vytvoří, pokud, který jste vybrali možnost.
* Čítače výkonu jsou nakonfigurované pro vybraný pracovní prostor. Pokud tento krok nezdaří, si všimnete, že některé výkonu grafů a tabulek nejsou zobrazena data pro virtuální počítač jste připojili. Problém můžete vyřešit spuštěním skriptu prostředí PowerShell, které jsou popsány [tady](vminsights-onboard.md#enable-with-powershell).
* Agenta Log Analytics je nainstalován na virtuálních počítačích Azure pomocí rozšíření virtuálního počítače, je-li určit, že je povinný.  
* Monitorování Azure pro virtuální počítače mapu závislostí agenta je nainstalován na virtuálních počítačích Azure s využitím rozšíření, je-li určit, že je vyžadován.  
* Azure Monitor komponenty podporující funkci stavu jsou nakonfigurované v případě potřeby a virtuální počítač je nakonfigurovaný k datům o stavu sestavy.

Během procesu připojení zkontrolujeme stav v každém z výše uvedených vrátí stav oznámení na portálu. Konfigurace pracovního prostoru a instalace agenta obvykle trvá 5 až 10 minut. Zobrazení dat monitorování a stavu na portálu trvat dalších 5 až 10 minut.  

Pokud inicializovali připojování a zobrazit zprávy oznamující, že virtuální počítač musí být připojili, povolit po dobu až 30 minut pro daný virtuální počítač k dokončení procesu. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Můžu Povolit jenom monitorování Azure pro virtuální počítače, proč se zobrazují všechny moje monitoruje stav funkce virtuálních počítačů?
Stav funkce je povolená pro všechny virtuální počítače, které jsou připojené k pracovnímu prostoru Log Analytics i v případě, že tato akce je zahájeno jednoho virtuálního počítače.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Můžete upravit plán, kdy kritéria vyhodnocuje podmínku?
Ne, časové období a četnost kritéria stavu nelze změnit v této vydané verzi. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Můžete zakázat kritéria pro podmínky, které není potřeba monitorovat?
Kritéria nelze zakázat v této verzi.

## <a name="are-the-health-alert-severities-configurable"></a>Se stav výstrahy závažnosti konfigurovatelné?  
Závažnost výstrahy stavu nelze změnit, můžete pouze měly být povoleno nebo zakázáno. Kromě toho některé výstraha závažnosti aktualizace na základě stavu kritéria. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Pokud mám překonfigurujte nastavení stavu konkrétní kritéria, se dají vymezit na konkrétní instanci?  
Při úpravě jakékoli nastavení instance kritérium stavu, jsou změněny všechny instance kritéria stavu stejného typu na virtuálním počítači Azure. Například pokud se změní prahovou hodnotu instance kritérium stavu volného místa disku, který odpovídá logický disk C:, tato prahová hodnota se vztahuje na všechny ostatní logické disky, které zjišťování a monitorování pro stejný virtuální počítač.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Monitorování stavu funkce logických procesorů a jader?
Ne, jednotlivé procesoru a kritéria úrovně stavu logický procesor nejsou pro Windows, ve výchozím nastavení se efektivně vyhodnotit zatížení procesoru na základě celkového počtu logických procesorů, které jsou k dispozici pro virtuální počítač Azure se monitoruje jenom celkové využití procesoru. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Všechny stavy jsou konfigurovatelné prahové hodnoty kritérií?  
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

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Jak upravím výstrahy, které jsou součástí stavu?
Pravidla výstrah, které jsou definovány pro každé kritérium stavu se nezobrazují na portálu Azure portal. Můžete povolit nebo zakázat upozornění na stav pouze v pravidlo [úlohy monitorování rozhraní API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Také nelze přiřadit [skupiny akcí Azure Monitor](../../azure-monitor/platform/action-groups.md) pro výstrahy týkající se stavu na webu Azure Portal. Rozhraní API nastavení oznámení můžete použít pouze ke konfiguraci skupiny akcí až se spustí pokaždé, když se aktivuje upozornění na stav. V současné době můžete přiřadit skupiny akcí na virtuálním počítači tak, aby všechny *výstrahy týkající se stavu* aktivuje proti aktivační událost virtuální počítač stejné skupiny akcí. Na rozdíl od tradičních upozornění v Azure neexistuje koncept skupiny samostatnou akci pro každé pravidlo výstrahy stavu. Kromě toho pouze skupiny akcí, které jsou nakonfigurované na poskytování e-mailu nebo oznámení SMS jsou podporovány, když se aktivuje upozornění na stav. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Se mi nezobrazují některé nebo všech dat v grafech výkonu pro virtuální počítač
Pokud se nezobrazí údaje o výkonu v tabulce disku nebo v některé z grafy výkonu. čítače výkonu nemusí být nakonfigurovaná v pracovním prostoru. Pokud chcete vyřešit, spusťte následující příkaz [skript prostředí PowerShell](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Jak se Azure Monitor pro funkci Mapa virtuální počítače liší od řešení Service Map?
Azure Monitor pro virtuální počítače mapování funkce je založená na mapě služby, ale má následující rozdíly:

* Zobrazení mapy je přístupný z okna virtuálního počítače a z Azure monitoru pro virtuální počítače v části Azure Monitor.
* Připojení v mapě jsou nyní kliknout, čímž a zobrazení dat metrik připojení v postranní panel pro vybrané připojení.
* Není k dispozici nové rozhraní API, která se používá k vytvoření mapy pro lepší podporu složitější mapy.
* Monitorovaných virtuálních počítačů jsou teď součástí uzel skupiny klienta a prstencový graf znázorňuje podíl sledované vs nemonitorované virtuální počítače ve skupině.  To lze použít také pro filtrování seznamu počítačů, když je skupina rozbalena.
* Monitorovaných virtuálních počítačů jsou teď součástí na uzlech skupiny portů serveru a prstencový graf znázorňuje podíl sledované vs nemonitorovaných počítačů ve skupině.  To lze použít také pro filtrování seznamu počítačů, když je skupina rozbalena.
* Styl mapa byla aktualizována byly konzistentnější s Mapa aplikace ze služby Application insights.
* Boční panely byly aktualizovány, ale ještě není kompletní integrace společnosti, které se podporovaly v Service Map - Update Management, Change Tracking, zabezpečení a oddělení služeb. 
* Možnost pro výběr skupin a počítačů k mapování byl aktualizován a nyní podporuje předplatná, skupiny prostředků, škálovací sady virtuálních počítačů Azure a cloudových služeb.
* Nelze vytvořit nové skupiny počítačů řešení Service Map ve službě Azure Monitor pro funkci mapování virtuálních počítačů.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Proč se Moje grafy výkonu zobrazit tečkované čáry
Tato situace může nastat z několika důvodů.  V případech, ve kterých je mezera v shromažďování dat jsme znázornění řádky jako tečkovaná.  Pokud změníte data vzorkovací frekvence pro čítače výkonu povoleno (výchozí nastavení je ke shromažďování dat každých 60 sekund), uvidíte tečkované čáry v grafu, pokud zvolíte úzký časový rozsah pro graf a vzorkovací frekvence je menší než velikost kbelíku používaných v grafu (například vzorkovací frekvence je každých 10 minut a každý blok v grafu je 5 minut).  Výběr širší časový rozsah pro zobrazení by se měl grafu řádky v tomto případě se zobrazí jako čar spíše než tečky.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Skupiny podporovaných službou Azure Monitor pro virtuální počítače?
Ano, po instalaci agenta závislostí můžeme shromažďovat informace z virtuálních počítačů k zobrazení skupin na základě předplatného, skupiny prostředků, virtuálního počítače škálovací sady a cloudové služby.  Pokud jste dosud používali řešení Service Map a vytvořili skupiny počítačů, se zobrazí také.  Skupiny počítačů se také zobrazí ve filtru skupiny, pokud jste vytvořili pro pracovní prostor, který si prohlížíte. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak můžu zobrazit podrobnosti pro co ovlivňuje 95. percentil řádek v agregované grafy výkonu?
Ve výchozím nastavení je seznam seřazen k zobrazení virtuálních počítačů, které mají nejvyšší hodnotu 95. percentil pro vybranou metriku, s výjimkou grafu dostupné paměti, která zobrazuje počítače s nejnižší hodnotu 5. percentil.  Otevře se kliknutím na graf **N nejlepších** zobrazení s odpovídající vybranou metrikou.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkce mapy zpracovává duplicitní IP adresy v různých virtuálních sítích a podsítích?
Pokud jsou duplikování rozsahy IP adres pomocí virtuálních počítačů nebo virtuální počítač Azure škálovacích sad napříč podsítěmi a virtuálními sítěmi, může to způsobit monitorování Azure pro virtuální počítače mapu zobrazit nesprávné informace. Jedná se o známý problém a zkoumá možnosti na zdokonalování tohoto prostředí.

## <a name="does-map-feature-support-ipv6"></a>Mapování funkce podpory protokolu IPv6?
Mapování funkce aktuálně podporuje jenom IPv4 a zkoumá podpory pro IPv6. Podporují se i IPv4, který je tunelovým propojením uvnitř IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Načíst mapu pro skupinu prostředků nebo další velké skupiny mapy při ztěžovalo prohlížení
Zatímco provedli jsme vylepšení Map pro zpracování velkých a složitých konfigurací, Uvědomujeme si, že mapa může mít mnoho uzlů, připojení a uzel fungovat jako cluster.  Naší prioritou je pokračováním vylepšit podporu o zvýšení škálovatelnosti.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Proč grafu sítě na kartě Výkon vypadat jinak než sítě graf na stránce Přehled virtuálních počítačů Azure?

Stránka s přehledem pro virtuální počítač Azure zobrazuje grafy založené na hostitele měření aktivity ve virtuálním počítači hosta.  Sítě grafu na Přehled virtuálních počítačů Azure se zobrazí pouze síťový provoz, který se bude účtovat.  To nezahrnuje provoz mezi virtuálními sítěmi vnet.  Data a grafy, které jsou uvedené pro monitorování Azure pro virtuální počítače je na základě dat z hosta virtuálního počítače a sítě grafu se zobrazuje veškerý provoz protokolu TCP/IP, který je příchozí a odchozí tomuto virtuálnímu počítači, včetně mezi sítěmi vnet.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Pokud jsem v Log Analytics zdarma cenového plánu jsou jejich omezení?
Pokud jste nakonfigurovali Azure Monitor se pomocí pracovního prostoru Log Analytics *Free* cenovou úroveň, Azure Monitor pro funkci Mapa virtuální počítače se podporují pouze pět připojené počítače připojený k pracovnímu prostoru. Pokud máte pět virtuální počítače připojené k bezplatný pracovní prostor, odpojit jeden z virtuálních počítačů a pak připojit nový virtuální počítač, nový virtuální počítač není monitorovat a uvedených na stránce mapy.  

Za těchto podmínek, zobrazí se výzva s **vyzkoušet** při otevření virtuálního počítače a vyberte možnost **Insights (preview)** v levém podokně, i po jeho instalaci už na virtuálním počítači.  Však můžete nezobrazí výzva s možnostmi jako obvykle ke kterým by tento virtuální počítač nebylo zapojený do služby Azure Monitor pro virtuální počítače. 

## <a name="next-steps"></a>Další postup
Kontrola [připojení Azure Monitor pro virtuální počítače](vminsights-onboard.md) vám pomohou pochopit požadavky a metody, pokud chcete povolit monitorování virtuálních počítačů.
