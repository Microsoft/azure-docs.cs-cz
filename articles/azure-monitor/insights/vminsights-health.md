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
ms.date: 01/30/2019
ms.author: magoedte
ms.openlocfilehash: 58da86140b97c5292d390b6f91502b7f0622986a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476838"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms-preview"></a>Vysvětlení stavu virtuálních počítačů Azure pomocí Azure monitoru pro virtuální počítače (preview)
Azure obsahuje několik služeb, které jednotlivě provádět konkrétní role nebo úkolu v prostoru pro monitorování, ale poskytuje perspektivy podrobný stav operačního systému hostované na Azure virtual machines nebyl k dispozici.  V průběhu monitorování může pro různé podmínky použití Log Analytics nebo Azure Monitor, nejsou určeny pro modelování a představují stavu základní součásti nebo celkového stavu virtuálního počítače.  Prostřednictvím služby Azure Monitor pro funkci stav virtuálních počítačů aktivně Monitoruje dostupnost a výkon Windows nebo Linuxem hostovaného operačního systému s modelem, které představují klíčových komponent a jejich vztahů kritéria, která určuje, jak měřit kvalitu ty komponenty, a upozorní vás při zjištění není v pořádku podmínky.  

Zobrazení celkového stavu virtuálního počítače Azure a základního operačního systému může být dodržen za dvou hledisek službou Azure Monitor stavu virtuálních počítačů, přímo z virtuálního počítače nebo ve všech virtuálních počítačích ve skupině prostředků ze služby Azure Monitor.

Tento článek vám pomůže pochopit, jak rychle posoudit, prozkoumat a vyřešit problémy se stavem zjištěna.

Informace o konfiguraci monitorování Azure pro virtuální počítače najdete v tématu [povolit monitorování Azure pro virtuální počítače](vminsights-onboard.md).

>[!NOTE]
>Od 15. února 2019 začneme můžete migrovat z aktuální stav modelu ve službě Azure Monitor pro funkci stav virtuální počítače, který se zobrazí, když jste v prostředí diagnostiku stavu v současné době na novou verzi modelu stavu. Tato aktualizace zlepšuje výkon při zpracování stavu kumulativní a obsahuje model zpracovaných stavů zobrazí v zobrazení stavu diagnostiky. 
>
>Na novém modelu stavu kumulativní podřízené kritéria kritéria úrovně stavu nadřazená/entita bude rychleji a proto stav aktualizace nadřazené do požadovaného nebo cílovém stavu s nižší latencí. Stále můžete filtrovat kritéria stavu v rámci **výkonu** a **dostupnosti** kategorie na rozdíl od předchozích metodu založenou na kartu k výběru obou kategorie v zobrazení.
>
>Podrobné informace o nové možnosti diagnostiky stavu najdete stav diagnostiky [části](#health-diagnostics) v tomto článku. 
>
>Tato aktualizace zlepší následující: 
>
>- Souhrn stavu zpracování s nižší latencí  
>- Rychleji upozorňuje na ně změn stavů 
>- Rychlejší aktualizace stavu v zobrazení pro agregované virtuálního počítače pro všechny virtuální počítače 
>
>Neexistuje žádná regrese funkcí doručit ještě dnes s funkcí stavu služby Azure Monitor pro virtuální počítače.

>V důsledku této změny bude některých přerušení pro krátké době se služby a historie stavu. Dvě prostředí ve stavu diagnostiky se to týká – obnoví historii změn stavu a předchozí změny stavu pro kritéria nebudou k dispozici ke kontrole ve sloupci stav změní stav stránky diagnostiky. Pokud vás zajímají historických datech kteréhokoli stěžejní kritické virtuální počítač, pak můžete pořídit snímek kritéria data o stavu a odpovídající změny stavu pro vaši informaci. 

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
- Procento doby nečinnosti fyzického disku
- Stav služby Klient DHCP
- DNS Client Service Health
- Stav služby vzdáleného volání Procedur
- Stav služby serveru
- Procento využití celkový čas procesoru
- Stav služby protokolu událostí Windows
- Stav služby Windows Firewall
- Stav služby Vzdálená správa Windows

### <a name="linux-vms"></a>Virtuální počítače s Linuxem
- Disk – monitorování střední Doba disku/přenos 
- Disk – monitorování střední Doba disku/čtení 
- Disk – monitorování střední Doby disku/zápis 
- Stav disku
- Volné místo logického disku
- % Volného místa logického disku
- Logický Disk % volných uzlů Inode
- Stav síťového adaptéru
- Procento času DPC času procesoru
- Procesorový čas procesoru v %
- Celkové procento času procesoru
- Celkové procento času DPC
- Operační systém dostupný počet megabajtů paměti

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Úvod do prostředí stavu
Než se podíváme do aplikace pomocí funkcí stavu pro jeden virtuální počítač nebo skupinu virtuálních počítačů, je důležité, že poskytujeme stručný úvod, takže víte, jak je uvedené informace a co představují vizualizace.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Zobrazit stav přímo z virtuálního počítače 
Chcete-li zobrazit stav virtuálního počítače Azure, vyberte **Insights (preview)** v levém podokně virtuální počítač. Na stránce přehledu virtuálního počítače **stavu** je ve výchozím nastavení otevřít a zobrazuje stav virtuálního počítače.  

![Azure Monitor pro přehled stavu virtuálních počítačů z vybraných virtuálních počítačů Azure](./media/vminsights-health/vminsights-directvm-health.png)

Na **stavu** karta, v části **stavu virtuálního počítače hosta**, tato tabulka ukazuje aktuální stav virtuálního počítače a celkový počet upozornění na stav virtuálního počítače vyvolané komponentu není v pořádku. Odkazovat na [upozorňování a oznámení správy](#alerting-and-alert-management) další podrobnosti.  

Stavy definované pro virtuální počítače jsou: 

* **V pořádku** – virtuální počítač se nedetekovaly žádné problémy a funguje podle potřeby.  
* **Kritické** – jeden nebo více problémů se zjistí, které je třeba řešit, aby bylo možné obnovit normální funkce podle očekávání. 
* **Upozornění** -zjištění jeden nebo více problémů, které je třeba řešit nebo podmínku stavu může být důležité.  
* **Neznámý** – Pokud službu nebylo možné navázat připojení s virtuálním Počítačem, stav se změní na neznámém stavu.  

Výběr **zobrazit stav diagnostiky** se otevře stránka zobrazuje všechny součásti virtuálního počítače, přidruženého stavu kritéria, změny stavů a další závažné potíže, se kterými monitorování součásti související se virtuální počítač. Další informace najdete v tématu [stav diagnostiky](#health-diagnostics). 

V části **stav součásti** části, v tabulce jsou uvedeny souhrnné stav primární výkonu kategorií monitoruje stav kritéria pro tyto oblasti, konkrétně **procesoru**,  **Paměť**, **disku**, a **sítě**.  Výběrem některého z komponenty se otevře stránka obsahující všechny aspekty dané komponenty a stav příslušných stavu každého monitorování kritéria jednotlivých stavů.  

Při přístupu ke stavu z virtuálního počítače Azure s operačním systémem Windows, stav top 5 základních služeb Windows jsou uvedeny v části **Core services stavu**.  Výběrem některého ze služeb otevře stránku s výpisem stavu kritéria sledování této komponentě a jeho stav.  Kliknutím na název stavu kritéria otevře se podokno vlastností a odtud můžete zkontrolovat podrobnosti o konfiguraci, včetně Pokud kritéria stavu má odpovídající Azure Monitor alert definované. Další informace najdete v tématu [stav diagnostiky a práci s kritéria](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Perspektiva agregační virtuálního počítače
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

Stavy definované pro virtuální počítače jsou: 

* **V pořádku** – virtuální počítač se nedetekovaly žádné problémy a funguje podle potřeby.  
* **Kritické** – jeden nebo více problémů se zjistí, které je třeba řešit, aby bylo možné obnovit normální funkce podle očekávání. 
* **Upozornění** -zjištění jeden nebo více problémů, které je třeba řešit nebo podmínku stavu může být důležité.  
* **Neznámý** – Pokud službu nebylo možné navázat připojení s virtuálním Počítačem, stav se změní na neznámém stavu.  

Kliknutím na libovolnou položku sloupec - **počet virtuálních počítačů**, **kritický**, **upozornění**, **stavu v pořádku** nebo **neznámý** do Přejít k **virtuálních počítačů** stránky zobrazení filtrované výsledky odpovídající vybraný sloupec. Například, pokud chcete zkontrolovat všechny virtuální počítače spuštěné **Red Hat Enterprise Linux verze 7.5**, klikněte na **počet virtuálních počítačů** hodnota tohoto operačního systému a otevře se na následující stránce výpis virtuálních počítačů odpovídající Tento filtr a jejich stav aktuálně známé stavu.  

![Příklad souhrnu virtuálních počítačů Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Na **virtuálních počítačů** stránky, pokud vyberete název virtuálního počítače ve sloupci **název virtuálního počítače**, budete přesměrováni na stránku instance virtuálního počítače s dalšími podrobnostmi o upozornění a identifikovat problémy se stavem kritéria, která jsou vybraný virtuální počítač vliv.  Z tohoto místa můžete filtrovat kliknutím na podrobnosti o stavu stavu **stav** ikony v levém horním rohu stránky zobrazíte komponenty, které nejsou v pořádku nebo je můžete zobrazit stav virtuálního počítače výstrahy vyvolané komponentou není v pořádku zařazený do kategorie službou závažnost výstrahy.    

Ze zobrazení seznamu virtuálních počítačů, otevře se kliknutím na název virtuálního počítače **stavu** stránka, která vybraný virtuální počítač, podobně jako v případě, že jste vybrali **Insights (preview)** z virtuálního počítače přímo.

![Přehled virtuálních počítačů z vybraných virtuálních počítačů Azure](./media/vminsights-health/vminsights-directvm-health.png)

Zde zobrazuje souhrn **stav** pro virtuální počítač a **výstrahy**zařazené do kategorií podle závažnosti, které představují vyvolá, když se stav změní ze stavu na není v pořádku pro upozornění na stav virtuálního počítače kritéria stavu.  Výběr **virtuálních počítačů do kritického stavu** otevře se stránka seznam jednoho nebo více virtuálních počítačů, které jsou v kritickém stavu.  Kliknutím na stav pro jeden z virtuálních počítačů v seznamu se zobrazí **stav diagnostiky** zobrazení virtuálního počítače.  Tady můžete zjistit která kritéria stavu se odráží stav problému se stavem. Když **stav diagnostiky** stránka se otevře, zobrazuje všechny součásti virtuálního počítače a jeho přidruženého stavu kritéria s aktuálním stavem.  Odkazovat [stav diagnostiky](#health-diagnostics) části Další podrobnosti.  

Výběr **zobrazit všechna kritéria stavu** se otevře stránka zobrazuje seznam všech kritéria stavu k dispozici s touto funkcí.  Informace můžete dále filtrovat podle následujících možností:

* **Typ** – existují tři druhy stavu kritéria typy vyhodnocování podmínek a souhrnné celkového stavu monitorovaných virtuálních počítačů.  
    a. **Jednotka** – měří určitý aspekt virtuálního počítače. Tento typ kritérií stavu může kontrolovat čítač výkonu pro zjištění výkonu komponenty, spustit skript k provedení virtuální transakce nebo sledovat události indikující chybu.  Ve výchozím nastavení je nastaven filtr k jednotce.  
    b. **Závislost** – poskytuje shrnutí stavů mezi různými entitami. Tato kritéria stavu umožňuje stavu entity závisí na stavu jiného typu entity, která se spoléhá na úspěšně fungovat.  
    c. **Agregační** – poskytuje souhrnný stav podobných kritérií stavu. Kritéria stavu jednotek a závislostí se obvykle konfigurují v rámci agregovaný kritéria. Kromě lepší uspořádání obecné mnoho různých stavů kritérií zaměřený na entitu, nabízí agregovaný kritérium jedinečný stav pro různé kategorie entit.

* **Kategorie** -zadejte kritéria stavu se použijí pro skupinu kritéria podobného typu pro účely vytváření sestav.  Buď jsou **dostupnosti** nebo **výkonu**.

Můžete přejít k další dolů zobrazíte, která instance nejsou v pořádku po kliknutí na hodnoty v rámci **není v pořádku, komponenta** sloupce.  Na stránce tabulka uvádí komponenty, které jsou v kritickém stavu.    

## <a name="health-diagnostics"></a>Stav diagnostiky
Thge **stav diagnostiky** stránka umožňuje vizualizovat modelu stavu virtuálního počítače, výpis všech součástí virtuálního počítače, související kritéria, změny stavu a další závažné potíže identifikovaný monitorované součásti související k virtuálnímu počítači.

![Příklad stránky stav diagnostiky pro virtuální počítač](./media/vminsights-health/health-diagnostics-page-01.png)

Diagnostika stavu můžete spustit následujícími způsoby.

* Podle souhrnu stavu pro všechny virtuální počítače z hlediska agregační virtuálních počítačů ve službě Azure Monitor.  Na **stavu** stránky, klikněte na ikonu pro **kritický**, **upozornění**, **pořádku**, nebo **neznámý** Stav v části **stavu virtuálního počítače hosta** a přejít na stránku, která obsahuje seznam všech virtuálních počítačů odpovídající dané filtrované kategorie.  Kliknutím na hodnotu v **stav** sloupce se otevře stav diagnostiky oboru tohoto konkrétního virtuálního počítače.      

* Podle operačního systému z hlediska agregační virtuálních počítačů ve službě Azure Monitor. V části **distribuci virtuálních počítačů**, vyberete některou z hodnot sloupců se otevře **virtuálních počítačů** stránce a v tabulce odpovídající kategorii filtrovaný seznam.  Kliknutím na hodnoty v rámci **stav** sloupec otevře diagnostiku stavu pro vybraný virtuální počítač.    
 
* Z virtuálního počítače v Azure Monitor pro virtuální počítače hosta **stavu** kartu tak, že vyberete **zobrazit stav diagnostiky** 

Diagnostika stavu organizuje informace o stavu do následujících kategorií: 

* Dostupnost
* Výkon
 
Všechna kritéria stavu definovaný pro konkrétní součást, jako je například logického disku, využití procesoru, atd. Kromě toho můžete zobrazit kategorie monitorování vedle **kritéria** sloupce.  

Stav kritérií stavu je definován pomocí jedné ze čtyř stavů – *kritický*, *upozornění*, *pořádku*, a *neznámý*. První tři se dají konfigurovat, což znamená, můžete upravit prahové hodnoty monitorování pomocí [úlohy monitorování API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). *Neznámý* není Konfigurovatelný a vyhrazené pro konkrétní scénáře, jak je popsáno v následující tabulce.  

Následující tabulka obsahuje podrobnosti o stavů ve stavu diagnostiky.

|Ikona |Stav |Význam |
|-----|-------------|------------|
| |V pořádku |Stav je v pořádku, pokud je ve stavu definované podmínky, o žádných problémech zjistila pro virtuální počítač tak, že funguje podle potřeby. V případě nadřazené souhrnné monitorování stavu zobrazí nahoru a odráží nejlepší a nejhorší stav podřízené.|
| |Kritická |Stav je velmi důležité, pokud není v podmínce definované stavu označující, že byly zjištěny jeden nebo více problémů, které je třeba řešit, aby bylo možné obnovit normální funkce. V případě nadřazené souhrnné monitorování stavu zobrazí nahoru a odráží nejlepší a nejhorší stav podřízené.|
| |Upozornění |Stav je upozornění. Pokud je mezi dvěma prahovými hodnotami podmínky definované stavu, kde jeden udává *upozornění* stavu a druhý je uvedeno *kritický* stavu (tři stavy pod kontrolou uživatele jsou je to možné), nebo při, nekritické problém je zjištěna, což může způsobit kritické problémy, pokud nebyl vyřešen. V případě nadřazené souhrnné monitorování, pokud jeden nebo více podřízených je ve stavu upozornění, pak bude odrážet nadřazené *upozornění* stavu. Pokud je podřízený, který je v *kritický* a jiné podřízené v *upozornění* stavu, nadřazené souhrn se zobrazí stav *kritický*.|
| |Neznámé |Stav je v *neznámý* stav, když stav nelze vypočítat z několika důvodů, například není možné shromažďovat data, služba neinicializované atd. Toto není stav pod kontrolou uživatele.| 

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

Celkový stav cíl je určen podle stavu všech jeho stav kritéria definovaná v modelu stavu. Bude jím kombinace kritéria stavu zaměřených přímo na cíl, stav kritéria zaměřený na komponenty zahrnované do cílové prostřednictvím agregovaný kritérium. Tato hierarchie je znázorněna v **kritéria** část stránky diagnostiky stavu. Zásady souhrnu stavů je součástí konfigurace agregovaný kritéria (výchozí nastavení je *nejhorší of*). Můžete najít seznam výchozí sadu kritérií stavu spuštěných v rámci této funkce v části [monitorování podrobnosti o konfiguraci](#monitoring-configuration-details).  

**Jednotka** typ kritérií stavu může mít svou konfiguraci upravit kliknutím na odkaz elipsa daleko vpravo a výběrem možnosti **zobrazit podrobnosti** a otevřete tak podokno konfigurace. 

![Příklad kritéria stavu konfigurace](./media/vminsights-health/health-diagnostics-vm-example-02.png)

V podokně Konfigurace pro vybraný stav kritéria, použijeme příklad **průměrné sekund za zápisu disku**, jeho prahová hodnota se dá nakonfigurovat s jinou číselnou hodnotu. Monitorování dvou stavů, což znamená pouze změny provedené od v dobrém stavu na varování je. Další kritéria stavu může být tři stavu, ve kterém můžete nakonfigurovat hodnotu pro upozornění a kritickou stavu mezní hodnotu stavu.  

>[!NOTE]
>Provádění změn konfigurace kritérií stavu do jedné instance se použijí pro všechny sledované instance.  Pokud vyberete třeba **fyzický Disk -1 D:** a upravovat **průměrné sekund za zápisu disku** prahovou hodnotu, se nevztahuje na pouze tuto instanci, ale všechny ostatní instance disku zjišťování a monitorování na virtuální počítač.
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

V příkladu výše, když vyberete **fyzického disku – 1 D:**, je filtrovaná stromu kritéria **fyzický Disk - 1 D:**. **Změny stavu** sloupci se zobrazuje změny stavu založené na dostupnosti **fyzického disku – 1 D:**. 

Najdete v článku aktualizovaný stav, můžete aktualizovat stránku diagnostiky stavu kliknutím **aktualizovat** odkaz.  Při aktualizaci stavu kritérium stav podle předem definovaného intervalu dotazování tento úkol vám umožní vyhnout čekání a odráží nejnovější stav.  **Kritéria stavu** je filtr umožňuje určit obor výsledky na základě vybraného stavu - *pořádku*, *upozornění*, *kritický*, *Neznámý*, a *všechny*.  **Poslední aktualizace** čas v pravém horním rohu představuje poslední čas při poslední stránku diagnostiky stavu aktualizace.  

## <a name="alerts"></a>Výstrahy
Pro funkci stav virtuálních počítačů Azure Monitor integruje s [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) a vygeneruje výstrahu, pokud kritéria předdefinované stavu změnit dobrý do stavu není v pořádku, při zjištění stavu. Výstrahy jsou rozdělené podle závažnosti - závažnost 0 až 4, s závažnost 0 představuje nejvyšší úroveň závažnosti.  

Celkový počet zařazených do kategorií podle závažnosti upozornění na stav virtuálního počítače je k dispozici na **stavu** řídicího panelu v části **výstrahy**. Když vyberete buď celkový počet výstrah nebo číslo odpovídající úroveň závažnosti, **výstrahy** se otevře a zobrazí seznam všech výstrah odpovídající výběru.  Například, pokud jste vybrali na řádek odpovídající **úroveň závažnost 1**, pak se zobrazí následující zobrazení:

![Příklad všech 1. úrovně závažnosti výstrah](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Na **výstrahy** stránky, není pouze v oboru zobrazení výstrahy odpovídající váš výběr, ale také se filtrují podle **typ prostředku** pouze zobrazíte stav výstrahy vyvolané prostředku virtuálního počítače.  Toto je zohledněno v seznamu výstrah, ve sloupci **cílový prostředek**, kde se zobrazuje při byla splněna podmínka stavu konkrétní kritéria není v pořádku, byla výstraha vyvolána pro virtuální počítač Azure.  

Upozornění z jiných typů prostředků nebo služeb nejsou určeny mají být zahrnuty v tomto zobrazení, například podle upozornění protokolů v Log Analytics dotazů nebo metriky upozornění, která by obvykle zobrazení z výchozího Azure Monitor [všechny výstrahy](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) stránky. 

Toto zobrazení můžete filtrovat výběrem hodnoty v rozevíracích nabídek v horní části stránky.

|Sloupec |Popis | 
|-------|------------| 
|Předplatné |Vyberte předplatné služby Azure. Pouze výstrahy ve vybraném předplatném se nastavují v zobrazení. | 
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

## <a name="next-steps"></a>Další postup
Pokud chcete identifikovat problémová místa a celkové využití výkonu vašich virtuálních počítačů, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). 
