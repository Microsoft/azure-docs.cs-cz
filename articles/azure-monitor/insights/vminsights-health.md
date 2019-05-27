---
title: Monitorování stavu virtuálních počítačů pomocí Azure monitoru pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Tento článek popisuje, jak porozumět stavu virtuálního počítače a službou Azure Monitor základního operačního systému pro virtuální počítače.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 9fa76c9637a6dcdca48bf45e8ee2aa9305a4f64f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66130459"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Pochopení stavu virtuálních počítačů Azure

Azure obsahuje několik služeb, které jednotlivě provádění konkrétních rolí a úloh v prostoru pro monitorování, ale nebyly k dispozici perspektivy podrobný stav operačního systému hostované na Azure virtual machines. V průběhu monitorování může pro různé podmínky použití Azure monitoru, nebyl navržen pro modelování a představují stavu základní součásti nebo celkového stavu virtuálního počítače. Prostřednictvím služby Azure Monitor pro funkci stav virtuálních počítačů aktivně Monitoruje dostupnost a výkon Windows nebo Linuxem hostovaného operačního systému s modelem, které představují klíčových komponent a jejich vztahů kritéria, která určuje, jak měřit kvalitu ty komponenty, a upozorní vás při zjištění není v pořádku podmínky.  

Zobrazení celkového stavu virtuálního počítače Azure a základního operačního systému může být dodržen za dvou hledisek službou Azure Monitor stavu virtuálních počítačů, přímo z virtuálního počítače nebo ve všech virtuálních počítačích ve skupině prostředků ze služby Azure Monitor.

Tento článek vám pomůže pochopit, jak rychle posoudit, prozkoumat a vyřešit problémy se stavem zjištěna.

Informace o konfiguraci monitorování Azure pro virtuální počítače najdete v tématu [povolit monitorování Azure pro virtuální počítače](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Podrobnosti o konfiguraci monitorování

Tato část popisuje výchozí stav kritéria definovat za účelem monitorování Windows Azure a virtuální počítače s Linuxem. Všechna kritéria stavu jsou nakonfigurovaná tak, aby upozornění, když je splněna podmínka není v pořádku. 

### <a name="windows-vms"></a>Virtuální počítače s Windows

- Dostupný počet megabajtů paměti 
- Průměrná sekund na zápis (logický Disk)
- Průměrná sekund na zápis (Disk)
- Průměrná logického disku sekund na čtení
- Průměrná logického disku sekund na přenos
- Sekund průměrné čtení
- Průměrná sekund na přenos
- Aktuální délka fronty disku (logický Disk)
- Aktuální délka fronty disku (Disk)
- Procento doby nečinnosti disku
- Chyba systému souborů nebo poškození
- Volné místo logického disku (%) Nízká
- Nízká (MB) volného místa logického disku
- Procento doby nečinnosti logického disku
- Paměťové stránky za sekundu
- Procento šířky pásma použít pro čtení
- Procento šířky pásma použít celkem
- Procento šířky pásma použít zápisu
- Procento používané potvrzené paměti
- Procento doby nečinnosti disku
- Stav služby Klient DHCP
- DNS Client Service Health
- Stav služby vzdáleného volání Procedur
- Stav služby serveru
- Procento využití celkový čas procesoru
- Stav služby protokolu událostí Windows
- Stav služby Windows Firewall
- Stav služby Vzdálená správa Windows

### <a name="linux-vms"></a>Linuxové virtuální počítače
- Disk – monitorování střední Doba disku/přenos 
- Disk – monitorování střední Doba disku/čtení 
- Disk – monitorování střední Doby disku/zápis 
- Stav disku
- Volné místo logického disku
- % Volného místa logického disku
- Logický Disk % volných uzlů Inode
- Stav síťového adaptéru
- Celkové procento času procesoru
- Operační systém dostupný počet megabajtů paměti

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Úvod do prostředí stavu

Než se podíváme do aplikace pomocí funkcí stavu pro jeden virtuální počítač nebo skupinu virtuálních počítačů, je důležité, že poskytujeme stručný úvod, takže víte, jak je uvedené informace a co představují vizualizace.  

### <a name="view-health-directly-from-a-virtual-machine"></a>Zobrazit stav přímo z virtuálního počítače 

Chcete-li zobrazit stav virtuálního počítače Azure, vyberte **Insights (preview)** v levém podokně virtuální počítač. Na stránce přehledu virtuálního počítače **stavu** je ve výchozím nastavení otevřít a zobrazuje stav virtuálního počítače.  

![Azure Monitor pro přehled stavu virtuálních počítačů z vybraných virtuálních počítačů Azure](./media/vminsights-health/vminsights-directvm-health.png)

Na **stavu** karta, v části **stavu virtuálního počítače hosta**, tato tabulka ukazuje aktuální stav virtuálního počítače a celkový počet upozornění na stav virtuálního počítače vyvolané komponentu není v pořádku. Další informace najdete v tématu [výstrahy](#alerts) další podrobnosti o upozornění prostředí.  

Stavy definované pro virtuální počítač jsou popsány v následující tabulce: 

|Ikona |Stav |Význam |
|-----|-------------|---------------|
| |V pořádku |Stav je v pořádku, pokud je v rámci podmínky definované stavu, o žádných problémech zjistila pro virtuální počítač a funguje podle potřeby. S nadřazené souhrnné monitorování stavu zobrazí souhrn po up a odráží nejlepší a nejhorší stav podřízených.|
| |Kritická |Stav je velmi důležité, pokud není v podmínce definované stavu označující, že byly zjištěny jeden nebo více problémů, které je třeba řešit, aby bylo možné obnovit normální funkce. S nadřazené souhrnné monitorování stavu zobrazí souhrn po up a odráží nejlepší a nejhorší stav podřízených.|
| |Upozornění |Stav je upozornění. Pokud je mezi dvěma prahovými hodnotami podmínky definované stavu, kde jeden udává *upozornění* stavu a druhý je uvedeno *kritický* stavu (tři prahové hodnoty stavu health můžete nakonfigurovat), nebo při, nekritické problém je zjištěna, což může způsobit kritické problémy, pokud nebyl vyřešen. S nadřazené souhrnné monitorování, pokud jeden nebo více podřízených je ve stavu upozornění, pak bude odrážet nadřazené *upozornění* stavu. Pokud je podřízený, který je v *kritický* a jiné podřízené v *upozornění* stavu, nadřazené souhrn se zobrazí stav *kritický*.|
| |Neznámé |Stav je *neznámý* při nelze vypočítat z několika důvodů. Viz následující poznámka pod čarou <sup>1</sup> pro další podrobnosti a možná řešení je vyřešit. |

<sup>1</sup> the Neznámý stav je způsobeno tím, k následujícím problémům:

- Byla překonfigurována agenta a sestavy do pracovního prostoru už zadaný, když bylo povoleno monitorování Azure pro virtuální počítače. Konfigurace agenta na generování sestav do pracovního prostoru najdete [přidání nebo odebrání pracovního prostoru](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Virtuální počítač se odstranil.
- Pracovní prostor přidružený k monitorování Azure pro virtuální počítače se odstraní. K obnovení pracovního prostoru, pokud máte smlouvu Premier support výhody, můžete otevřít žádost o podporu s [Premier](https://premier.microsoft.com/).
- Řešení závislostí se odstranily. Chcete-li znovu povolit ServiceMap a InfrastructureInsights řešení ve vašem pracovním prostoru Log Analytics, můžete přeinstalovat pomocí [šablony Azure Resource Manageru](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions) , který má k dispozici nebo pomocí možnosti konfigurace pracovního prostoru na Získejte kartu spuštěno.
- Virtuální počítač byl vypnut.
- Služba Azure virtuální počítač nedostupný nebo právě probíhá údržba se.
- Pracovní prostor [limitu uchovávání dat nebo dat o denním](../platform/manage-cost-storage.md) je splněna.

Výběr **zobrazit stav diagnostiky** se otevře stránka zobrazuje všechny součásti virtuálního počítače, přidruženého stavu kritéria, změny stavů a další závažné potíže, se kterými monitorování součásti související se virtuální počítač. Další informace najdete v tématu [stav diagnostiky](#health-diagnostics). 

V části **stav součásti** části, v tabulce jsou uvedeny souhrnné stav primární výkonu kategorií monitoruje stav kritéria pro tyto oblasti, konkrétně **procesoru**,  **Paměť**, **disku**, a **sítě**.  Výběrem některého z komponenty se otevře stránka obsahující všechny aspekty dané komponenty a stav příslušných stavu každého monitorování kritéria jednotlivých stavů.  

Při přístupu ke stavu z virtuálního počítače Azure s operačním systémem Windows, stav horní, pět základních Windows služby jsou uvedeny v části **Core services stavu**.  Výběrem některého ze služeb otevře stránku s výpisem stavu kritéria sledování této komponentě a jeho stav.  Kliknutím na název stavu kritéria otevře se podokno vlastností a odtud můžete zkontrolovat podrobnosti o konfiguraci, včetně Pokud kritéria stavu má odpovídající Azure Monitor alert definované. Další informace najdete v tématu [stav diagnostiky a práci s kritéria](#health-diagnostics).  

### <a name="aggregate-virtual-machine-perspective"></a>Perspektiva agregační virtuálního počítače

Chcete-li zobrazit shromažďování stavu pro všechny virtuální počítače ve skupině prostředků, ze seznamu navigace na portálu vyberte **Azure Monitor** a pak vyberte **virtuálních počítačů (preview)**.  

![Virtuální počítač statistiky, sledování zobrazení ze služby Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Z **předplatné** a **skupiny prostředků** rozevíracích seznamech vyberte odpovídající prostředek skupiny, která zahrnuje virtuální počítače vztahuje ke skupině, chcete-li zobrazit stav jejich ohlášené.  Výběr pouze se vztahuje na funkce stavu a není přenesou na výkon nebo mapy.

Na **stavu** kartu, je možné získat následující:

* Kolik virtuálních počítačů jsou ve stavu kritický nebo není v pořádku, a kolik jsou v pořádku, či ne odesílání dat (označované jako neznámém stavu)?
* Které virtuální počítače podle operačního systému (OS) zasílání zpráv o stavu není v pořádku a kolik?
* Kolik virtuálních počítačů jsou kvůli problému zjištěnému s procesoru, disku, paměti nebo síťový adaptér, zařazených do kategorií podle stavu není v pořádku?  
* Kolik virtuálních počítačů jsou kvůli problému zjištěnému službou jádra operačního systému, zařazených do kategorií podle stavu není v pořádku?

Tady můžete rychle identifikovat hlavní kritické problémy zjištěné podle kritérií stavu Proaktivní monitorování virtuálního počítače a zkontrolujte podrobnosti výstrahy stavu virtuálních počítačů a článek znalostní báze přidružené určené pro diagnostiku a opravy problému.  Vyberte některou z závažnosti otevřete [všechny výstrahy](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) stránka se vyfiltruje podle tohoto závažnosti.

**Distribuce virtuálního počítače podle operačního systému** seznam virtuálních počítačů uvedených ve verzi Windows nebo distribuce Linuxu, spolu s jejich verze. V každé kategorii operačního systému virtuálních počítačů jsou rozdělené další na základě stavu virtuálního počítače. 

![Přehled virtuálních počítačů virtuálního počítače distribučního perspektivy](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Kliknutím na libovolnou položku sloupec - **počet virtuálních počítačů**, **kritický**, **upozornění**, **stavu v pořádku** nebo **neznámý** do Přejít k **virtuálních počítačů** stránky zobrazení filtrované výsledky odpovídající vybraný sloupec. Například, pokud chcete zkontrolovat všechny virtuální počítače spuštěné **Red Hat Enterprise Linux verze 7.5**, klikněte na **počet virtuálních počítačů** hodnota tohoto operačního systému a otevře se na následující stránce výpis virtuálních počítačů odpovídající Tento filtr a jejich stav aktuálně známé stavu.  

![Příklad souhrnu virtuálních počítačů Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Na **virtuálních počítačů** stránky, pokud vyberete název virtuálního počítače ve sloupci **název virtuálního počítače**, budete přesměrováni na stránku instance virtuálního počítače s dalšími podrobnostmi o upozornění a identifikovat problémy se stavem kritéria, která jsou vybraný virtuální počítač vliv.  Z tohoto místa můžete filtrovat kliknutím na podrobnosti o stavu stavu **stav** ikony v levém horním rohu stránky zobrazíte komponenty, které nejsou v pořádku nebo je můžete zobrazit stav virtuálního počítače výstrahy vyvolané komponentou není v pořádku zařazený do kategorie službou závažnost výstrahy.    

Ze zobrazení seznamu virtuálních počítačů, otevře se kliknutím na název virtuálního počítače **stavu** stránka, která vybraný virtuální počítač, podobně jako v případě, že jste vybrali **Insights (preview)** z virtuálního počítače přímo.

![Přehled virtuálních počítačů z vybraných virtuálních počítačů Azure](./media/vminsights-health/vminsights-directvm-health.png)

Zde zobrazuje souhrn **stav** pro virtuální počítač a **výstrahy**zařazené do kategorií podle závažnosti, které představují vyvolá, když se stav změní ze stavu na není v pořádku pro upozornění na stav virtuálního počítače kritéria stavu.  Výběr **virtuálních počítačů do kritického stavu** otevře se stránka seznam jednoho nebo více virtuálních počítačů, které jsou v kritickém stavu.  Kliknutím na stav pro jeden z virtuálních počítačů v seznamu se zobrazí **stav diagnostiky** zobrazení virtuálního počítače.  Tady můžete zjistit která kritéria stavu se odráží stav problému se stavem. Když **stav diagnostiky** stránka se otevře, zobrazuje všechny součásti virtuálního počítače a jeho přidruženého stavu kritéria s aktuálním stavem. Další informace najdete v tématu [stav diagnostiky](#health-diagnostics).  

Výběr **zobrazit všechna kritéria stavu** se otevře stránka zobrazuje seznam všech kritéria stavu k dispozici s touto funkcí.  Informace můžete dále filtrovat podle následujících možností:

* **Typ** – existují tři druhy stavu kritéria typy vyhodnocování podmínek a souhrnné celkového stavu monitorovaných virtuálních počítačů.  
    a. **Jednotka** – měří určitý aspekt virtuálního počítače. Tento typ kritérií stavu může kontrolovat čítač výkonu pro zjištění výkonu komponenty, spustit skript k provedení virtuální transakce nebo sledovat události indikující chybu.  Ve výchozím nastavení je nastaven filtr k jednotce.  
    b. **Závislost** – poskytuje shrnutí stavů mezi různými entitami. Tato kritéria stavu umožňuje stavu entity závisí na stavu jiného typu entity, která se spoléhá na úspěšně fungovat.  
    c. **Agregační** – poskytuje souhrnný stav podobných kritérií stavu. Kritéria stavu jednotek a závislostí se obvykle konfigurují v rámci agregovaný kritéria. Kromě lepší uspořádání obecné mnoho různých stavů kritérií zaměřený na entitu, nabízí agregovaný kritérium jedinečný stav pro různé kategorie entit.

* **Kategorie** -zadejte kritéria stavu se použijí pro skupinu kritéria podobného typu pro účely vytváření sestav.  Buď jsou **dostupnosti** nebo **výkonu**.

Můžete přejít k další dolů zobrazíte, která instance nejsou v pořádku po kliknutí na hodnoty v rámci **není v pořádku, komponenta** sloupce.  Na stránce tabulka uvádí komponenty, které jsou v kritickém stavu.    

## <a name="health-diagnostics"></a>Stav diagnostiky

**Stav diagnostiky** stránka umožňuje vizualizovat modelu stavu virtuálního počítače, výpis všech součástí virtuálního počítače, související kritéria, změny stavu a další závažné potíže identifikovaný monitorované součásti související k virtuálnímu počítači.

![Příklad stránky stav diagnostiky pro virtuální počítač](./media/vminsights-health/health-diagnostics-page-01.png)

Diagnostika stavu můžete spustit následujícími způsoby.

* Podle souhrnu stavu pro všechny virtuální počítače z hlediska agregační virtuálních počítačů ve službě Azure Monitor.  Na **stavu** stránky, klikněte na ikonu pro **kritický**, **upozornění**, **pořádku**, nebo **neznámý** Stav v části **stavu virtuálního počítače hosta** a přejít na stránku, která obsahuje seznam všech virtuálních počítačů odpovídající dané filtrované kategorie.  Kliknutím na hodnotu v **stav** sloupce se otevře stav diagnostiky oboru tohoto konkrétního virtuálního počítače.      

* Podle operačního systému z hlediska agregační virtuálních počítačů ve službě Azure Monitor. V části **distribuci virtuálních počítačů**, vyberete některou z hodnot sloupců se otevře **virtuálních počítačů** stránce a v tabulce odpovídající kategorii filtrovaný seznam.  Kliknutím na hodnoty v rámci **stav** sloupec otevře diagnostiku stavu pro vybraný virtuální počítač.    
 
* Z virtuálního počítače v Azure Monitor pro virtuální počítače hosta **stavu** kartu tak, že vyberete **zobrazit stav diagnostiky** 

Diagnostika stavu organizuje informace o stavu do následujících kategorií: 

* Dostupnost
* Výkon
 
Všechna kritéria stavu definovaný pro konkrétní součást, jako je například logického disku, využití procesoru, atd. lze zobrazit bez filtrování na dvě kategorie (které je veškeré zobrazení všechna kritéria) nebo filtrovat výsledky podle buď kategorie při výběru **dostupnosti**  nebo **výkonu** možnosti na stránce. Kromě toho můžete zobrazit kategorie kritéria vedle **kritéria** sloupce. Pokud kritériím neodpovídá vybranou kategorii, zobrazí se zpráva **žádná kritéria stavu k dispozici pro vybranou kategorii** v **kritéria** sloupce.  

Stav kritérií stavu je definován pomocí jedné ze čtyř stavů – *kritický*, *upozornění*, *pořádku*, a *neznámý*. První tři se dají konfigurovat, což znamená, můžete upravit prahové hodnoty monitorování přímo z podokna kritérií stavu konfigurace nebo pomocí rozhraní REST API služby Azure Monitor [monitorování operace aktualizace](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). *Neznámý* není Konfigurovatelný a vyhrazené pro konkrétní scénáře.  

Stav diagnostiky stránka má tři hlavní části:

* Model komponent 
* Kritéria stavu
* Změny stavu 

![Oddíly stránku Stav diagnostiky](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Komponenta modelu

Sloupec úplně vlevo na stránce Diagnostika stavu je součástí modelu. Všechny součásti, které jsou spojené s virtuálním Počítačem, se zobrazí v tomto sloupci spolu s jejich aktuálního stavu. 

V následujícím příkladu jsou součásti zjištěné disku, logický disk, procesoru, paměti a operační systém. Více instancí tyto součásti jsou zjistí a zobrazí v tomto sloupci. Například následující obrázek ukazuje, že virtuální počítač má dvěma instancemi logických disků – C: a D:, které jsou v dobrém stavu.  

![Příklad modelu v stav diagnostiky](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Kritéria stavu

V prostředním sloupci na stránce stavu diagnostiky se **kritéria** sloupce. Health model definované pro virtuální počítač se zobrazí v hierarchické stromové struktury. Health model pro virtuální počítač se skládá z jednotky a agregovaný kritéria.  

![Příklad kritéria v stav diagnostiky](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Kritéria stavu měří stav monitorovaných instance s kritérií, které by mohly být prahovou hodnotu, stav entity atd. Kritéria stavu má dvě nebo tři stavu prahové hodnoty konfigurovatelné stavu, jak bylo popsáno dříve. V libovolném časovém okamžiku může být kritérium stavu pouze do jednoho z jeho možných stavů. 

Celkový stav cíl je určen podle stavu všech jeho stav kritéria definovaná v modelu stavu. Jedná se o kombinaci kritéria stavu zaměřených přímo na cíl, stav kritéria zaměřený na komponenty zahrnované do cílové prostřednictvím agregovaný kritérium. Tato hierarchie je znázorněna v **kritéria** část stránky diagnostiky stavu. Zásady souhrnu stavů je součástí konfigurace agregovaný kritéria (výchozí nastavení je *nejhorší of*). Můžete najít seznam výchozí sadu kritérií stavu spuštěných v rámci této funkce v části [monitorování podrobnosti o konfiguraci](#monitoring-configuration-details), a REST API služby Azure Monitor můžete použít [monitorovat instance – seznam podle prostředku operace](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) k získání seznamu všech kritérií stavu a jeho podrobnou konfiguraci spouštění prostředku virtuálního počítače Azure.  

**Jednotka** typ kritérií stavu může mít svou konfiguraci upravit kliknutím na odkaz elipsa daleko vpravo a výběrem možnosti **zobrazit podrobnosti** a otevřete tak podokno konfigurace. 

![Příklad kritéria stavu konfigurace](./media/vminsights-health/health-diagnostics-vm-example-02.png)

V podokně Konfigurace pro vybraný stav kritéria, použijeme příklad **průměrné sekund za zápisu disku**, jeho prahová hodnota se dá nakonfigurovat s jinou číselnou hodnotu. Monitorování dvou stavů, což znamená pouze změny provedené od v dobrém stavu na varování je. Další kritéria stavu může být tři stavy, ve kterém můžete nakonfigurovat hodnotu pro upozornění a kritickou stavu mezní hodnotu stavu. Můžete také změnit prahovou hodnotu pomocí REST API služby Azure Monitor [monitorování operace aktualizace](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Provádění změn konfigurace kritérií stavu do jedné instance se použijí pro všechny sledované instance.  Pokud vyberete třeba **disku D:-1** a upravovat **průměrné sekund za zápisu disku** prahovou hodnotu, se nevztahuje na pouze tuto instanci, ale všechny ostatní instance disku zjišťování a monitorování ve virtuálním počítači.
>

![Konfigurace stavu kritéria příkladu monitorování jednotky](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Pokud chcete získat další informace o indikátor stavu, článků znalostní báze jsou zahrnuty, aby mohli snadno identifikovat problémy, příčiny a řešení. Klikněte na **zobrazení informací o** na stránce na odkaz a otevře na nové kartě v prohlížeči zobrazující konkrétní znalostní báze. Kdykoli můžete zkontrolovat všechny články znalostní báze kritérium stavu dodává v rámci Azure Monitor pro virtuální počítače stavu funkce [tady](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Změny stavu

Sloupec úplně vpravo na stránce stavu diagnostiky se **změny stavu**. Zobrazí všechny změny stavu přidružené k kritéria stavu, které je vybrané v **kritéria** části nebo změnit stav virtuálního počítače, pokud byl vybrán virtuální počítač **modelu** nebo **Kritéria** sloupci tabulky. 

![Příklad změny stavu v stav diagnostiky](./media/vminsights-health/health-diagnostics-page-statechanges.png)

V této části se skládá z kritérií stavu a doby přidružené seřazené podle poslední stav v horní části.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Změnit přidružení modelu, kritéria stavu a stavu sloupce 

Tři sloupce jsou vzájemně propojena mezi sebou. Když vyberete zjištěné instance v **modelu** části **kritéria** části je filtrovaná zobrazení komponenty a odpovídajícím způsobem **změnu stavu**část je aktualizována na základě kritérií vybraný stav. 

![Příklad výběru sledované instance a výsledky](./media/vminsights-health/health-diagnostics-vm-example-01.png)

V příkladu výše, když vyberete **disku – 1 D:**, je filtrovaná stromu kritéria **Disk - 1 D:**. **Změny stavu** sloupci se zobrazuje změny stavu založené na dostupnosti **disku – 1 D:**. 

Najdete v článku aktualizovaný stav, můžete aktualizovat stránku diagnostiky stavu kliknutím **aktualizovat** odkaz.  Při aktualizaci stavu kritérium stav podle předem definovaného intervalu dotazování tento úkol vám umožní vyhnout čekání a odráží nejnovější stav.  **Kritéria stavu** je filtr umožňuje určit obor výsledky na základě vybraného stavu - *pořádku*, *upozornění*, *kritický*, *Neznámý*, a *všechny*.  **Poslední aktualizace** čas v pravém horním rohu představuje poslední čas při poslední stránku diagnostiky stavu aktualizace.  

## <a name="alerts"></a>Výstrahy

Pro funkci stav virtuálních počítačů Azure Monitor integruje s [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) a vygeneruje výstrahu, pokud kritéria předdefinované stavu změnit dobrý do stavu není v pořádku, při zjištění stavu. Výstrahy jsou rozdělené podle závažnosti - závažnost 0 až 4, s závažnost 0 představuje nejvyšší úroveň závažnosti. 

Výstrahy nejsou přidružené skupinu akcí, které vás upozorní, pokud byla výstraha. Vlastník předplatného je potřeba nakonfigurovat oznámení kroků [dál v této části](#configure-alerts).   

Celkový počet zařazených do kategorií podle závažnosti upozornění na stav virtuálního počítače je k dispozici na **stavu** řídicího panelu v části **výstrahy**. Když vyberete buď celkový počet výstrah nebo číslo odpovídající úroveň závažnosti, **výstrahy** se otevře a zobrazí seznam všech výstrah odpovídající výběru.  Například, pokud jste vybrali na řádek odpovídající **úroveň závažnost 1**, pak se zobrazí následující zobrazení:

![Příklad všech 1. úrovně závažnosti výstrah](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Na **výstrahy** stránky, není pouze v oboru zobrazení výstrahy odpovídající váš výběr, ale také se filtrují podle **typ prostředku** pouze zobrazíte stav výstrahy vyvolané prostředku virtuálního počítače.  To se projeví v seznamu výstrah, ve sloupci **cílový prostředek**, kde se zobrazuje při byla splněna podmínka stavu konkrétní kritéria není v pořádku, byla výstraha vyvolána pro virtuální počítač Azure.  

Upozornění z jiných typů prostředků nebo služeb nejsou určeny mají být zahrnuty v tomto zobrazení, například upozornění protokolů založené na dotazech protokolu nebo metriky upozornění, která by obvykle zobrazení z výchozího Azure Monitor [všechny výstrahy](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) stránky. 

Toto zobrazení můžete filtrovat výběrem hodnoty v rozevíracích nabídek v horní části stránky.

|Sloupec |Popis | 
|-------|------------| 
|Předplatné |Vyberte předplatné Azure. Pouze výstrahy ve vybraném předplatném se nastavují v zobrazení. | 
|Skupina prostředků |Výběr jedné skupiny prostředků. V zobrazení jsou zahrnuty pouze výstrahy s cíli ve vybrané skupině prostředků. | 
|Typ prostředku |Vyberte jeden nebo více typů prostředků. Ve výchozím nastavení pouze výstrahy cíle **virtuálních počítačů** nebude vybrána a zahrnuté v tomto zobrazení. Tento sloupec je k dispozici pouze po zadal skupinu prostředků. | 
|Prostředek |Vyberte prostředek. V zobrazení jsou zahrnuty pouze výstrahy s tento prostředek jako cíl. Tento sloupec je k dispozici pouze po byl zadán typ prostředku. | 
|Severity |rozhodnout závažnost výstrahy, nebo vyberte *všechny* zahrnout všechny závažnosti výstrahy. | 
|Stav monitorování |Vybrat podmínku monitorování pro filtrování upozorní, pokud byly *Fired* systém nebo *Vyřešeno* systém, pokud podmínka není už aktivní. Nebo vyberte *všechny* zahrnout všechny podmínky upozornění. | 
|Stav upozornění |Vyberte stav výstrahy *nový*, *potvrzení*, *uzavřeno*, nebo vyberte *všechny* zahrnout výstrahy všechny stavy. | 
|Monitorovat službu |Vyberte službu, nebo vyberte *všechny* zahrnout všechny služby. Pouze výstrahy z *VM Insights* jsou podporovány pro tuto funkci.| 
|Časové rozmezí| Pouze výstrahy vyvolané v rámci vybrané časové období se nastavují v zobrazení. Podporované hodnoty jsou uplynulou hodinu, posledních 24 hodin, posledních 7 dní a posledních 30 dní. | 

**Podrobností výstrahy** stránky se zobrazí, když vyberete výstrahu, kterým poskytuje podrobnosti výstrahy a díky tomu umožňuje změnit její stav. Další informace o správě výstrah naleznete v tématu [vytvoření, zobrazení a Správa výstrah pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>V tomto čas, vytváření nových výstrah na základě kritérií stavu nebo upravit existující stavu pravidla výstrah ve službě Azure Monitor z portálu nepodporuje.  
>

![Panelu podrobností výstrahy pro vybrané výstrahy](./media/vminsights-health/alert-details-pane-01.png)

Upozornění stavu lze také změnit pro jedno nebo více upozornění tak, že je vyberete a pak vyberete **změnit stav** z **všechny výstrahy** na horním levém rohu stránky. Na **změnit stav upozornění** podokně vyberte jednu z státy, přidejte popis změnu v hodnotě **komentář** pole a potom klikněte na tlačítko **Ok** potvrďte provedené změny. Během ověřování informací a změny se použijí, můžete sledovat jeho průběh **oznámení** z nabídky.  

### <a name="configure-alerts"></a>Konfigurace upozornění
Některé úlohy není možné spravovat z portálu Azure portal a třeba provádět pomocí upozornění správy [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Zejména:

- Povolení nebo zakázání výstrahu týkající se stavu kritéria 
- Nastavení oznámení pro výstrahy týkající se stavu kritéria 

Používá metodu použitou v obou příkladech [ARMClient](https://github.com/projectkudu/armclient) na svém počítači s Windows. Pokud nejste obeznámeni s touto metodou, přečtěte si téma [pomocí ARMClient](../platform/rest-api-walkthrough.md#use-armclient).  

#### <a name="enable-or-disable-alert-rule"></a>Povolení nebo zakázání pravidla upozornění

K povolení nebo zakázání výstrahy pro konkrétní stav kritéria, vlastnost kritérií stavu *alertGeneration* musí být upravena s hodnotou buď **zakázané** nebo **povoleno**. K identifikaci *elementu monitorId* kritérií určitého stavu, bude následující příklad ukazuje, jak provádět dotazy pro tuto hodnotu pro kritéria **LogicalDisk\Avg disku sekund za přenos**.

1. V okně terminálu zadejte **armclient.exe přihlášení**. To vás vyzve k přihlášení do Azure.

2. Zadejte následující příkaz k načtení všech kritérium stavu aktivní na konkrétní virtuální počítač a určit hodnotu pro *elementu monitorId* vlastnost. 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Následující příklad ukazuje výstup tohoto příkazu. Poznamenejte si hodnotu *elementu MonitorId*. Tato hodnota je povinná na další krok potřebujeme k určení ID kritéria stavu a upravit jeho vlastnosti, aby se vytvořila výstraha.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Zadejte následující příkaz k úpravě *alertGeneration* vlastnost.

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Zadejte příkaz GET použili v kroku 2, chcete-li ověřit, hodnota vlastnosti je nastavena na **zakázané**.  

#### <a name="associate-action-group-with-health-criteria"></a>Kritéria stavu přidružit skupiny akcí

Azure Monitor stavu virtuálních počítačů podporuje SMS a e-mailová oznámení při výstrahy jsou generovány, když nebude kritéria stavu v pořádku. Konfigurace oznámení, budete muset poznamenejte si název skupiny akcí, který je nakonfigurován k odeslání serveru SMS nebo e-mailové oznámení. 

>[!NOTE]
>Tato akce je třeba provést pro každý virtuální počítač monitorovaný, že chcete dostávat oznámení, se nevztahují na všechny virtuální počítače ve skupině prostředků.  

1. V okně terminálu zadejte **armclient.exe přihlášení**. To vás vyzve k přihlášení do Azure.

2. Zadejte následující příkaz k přidružení skupiny akcí pravidel upozornění.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Chcete-li ověřit hodnotu vlastnosti **actionGroupResourceIds** byl úspěšně aktualizován, zadejte následující příkaz.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Výstup by měl vypadat takto:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Další postup

Pokud chcete identifikovat problémová místa a celkové využití výkonu vašich virtuálních počítačů, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). 
