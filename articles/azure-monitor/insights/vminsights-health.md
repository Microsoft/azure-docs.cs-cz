---
title: Pochopení stavu virtuálních počítačů Azure | Microsoft Docs
description: Tento článek popisuje, jak pochopit stav virtuálních počítačů a podkladových operačních systémů s Azure Monitor pro virtuální počítače.
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
ms.date: 09/12/2019
ms.author: magoedte
ms.openlocfilehash: b9b4a33e5aee92a4e8caa7a1128538cb2f1a8a7e
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933125"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Pochopení stavu virtuálních počítačů Azure

Azure zahrnuje služby pro konkrétní role nebo úlohy v prostoru monitorování, ale neposkytuje podrobné perspektivy provozu operačních systémů (OSs) hostovaných na virtuálních počítačích Azure (VM). I když můžete použít Azure Monitor pro různé podmínky, není navrženo tak, aby modeloval a nepředstavovalo stav základních komponent nebo celkový stav virtuálních počítačů.

Pomocí Azure Monitor pro virtuální počítače stavu můžete aktivně monitorovat dostupnost a výkon hostovaného operačního systému Windows nebo Linux. Funkce Health používá model, který představuje klíčové komponenty a jejich vztahy, poskytuje kritéria, která určují, jak změřit stav součásti, a pošle výstrahu, když zjistí stav, který není v pořádku.

Zobrazení celkového stavu virtuálního počítače Azure a základního operačního systému se dá pozorovat ze dvou perspektiv: přímo z virtuálního počítače nebo napříč všemi virtuálními počítači ve skupině prostředků z Azure Monitor.

Tento článek ukazuje, jak rychle vyhodnotit, prozkoumat a vyřešit problémy se stavem, když jsou zjištěny funkcí Azure Monitor pro virtuální počítače Health.

Informace o konfiguraci Azure Monitor pro virtuální počítače najdete v tématu [povolení Azure monitor pro virtuální počítače](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Podrobnosti o konfiguraci monitorování

Tato část popisuje výchozí kritéria stavu pro monitorování virtuálních počítačů Azure s Windows a Linux. Všechna kritéria stavu jsou předem nakonfigurovaná tak, aby odesílala výstrahu, když zjistí stav není v pořádku.

| Název monitorování | Frekvence (min.) | Lookback trvání (min.) | Operator | Prahová hodnota | Výstraha ve stavu | severity | Kategorie úlohy | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Logický disk online | 5 | 15 | <> | 1 (pravda) | Kritická | Sev1 | Linux | 
| Volné místo na logickém disku | 5 | 15 | < | 200 MB (upozornění)<br> 100 MB (kritické) | Upozornění | Sev1<br> Sev2 | Linux | 
| Logický disk% Free uzlů inode | 5 | 15 | < | 5 % | Kritická | Sev1 | Linux | 
| % Volného místa logického disku | 5 | 15 | < | 5 % | Kritická | Sev1 | Linux | 
| Stav síťového adaptéru | 5 | 15 | <> | 1 (pravda) | Upozornění | Sev2 | Linux | 
| Dostupná paměť operačního systému v megabajtech | 5 | 10 | < | 2,5 MB | Kritická | Sev1 | Linux | 
| Průměrná doba disku Doba disku/čtení | 5 | 25 | > | 0,05 s | Kritická | Sev1 | Linux | 
| Průměrná doba disku Doba disku/přenos | 5 | 25 | > | 0,05 s | Kritická | Sev1 | Linux | 
| Průměrná doba disku Doby disku/zápis | 5 | 25 | > | 0,05 s | Kritická | Sev1 | Linux | 
| Stav disku | 5 | 25 | <> | 1 (pravda) | Kritická | Sev1 | Linux | 
| Celkové procento procesorového času operačního systému | 5 | 10 | >= | 95% | Kritická | Sev1 | Linux | 
| Celkové procento využití procesoru | 5 | 10 | >= | 95% | Kritická | Sev1 | Windows | 
| Chyba nebo poškození systému souborů | 60 | 60 | <> | 4 | Kritická | Sev1 | Windows | 
| Průměrný počet sekund na čtení na logický disk | 1 | 15 | > | 0.04 s | Upozornění | Sev2 | Windows | 
| Průměrný počet sekund na přenos za sekundu | 1 | 15 | > | 0.04 s | Upozornění | Sev2 | Windows | 
| Průměrný počet sekund na zápis na logický disk (logický disk) | 1 | 15 | > | 0.04 s | Upozornění | Sev2 | Windows | 
| Aktuální délka fronty disku (logický disk) | 5 | 60 | >= | 32 | Upozornění | Sev2 | Windows | 
| Volné místo na logickém disku (MB) | 15 | 60 | > | Upozornění 500 MB<br> 300 MB – kritické | Kritická | Sev1<br> Sev2 | Windows | 
| Volné místo na logickém disku (%) | 15 | 60 | > | upozornění 10%<br> 5% kritických | Kritická | Sev1<br> Sev2 | Windows |
| Procento času nečinnosti logického disku | 15 | 360 | <= | 20 % | Upozornění | Sev2 | Windows | 
| Procento využití využité šířky pásma | 5 | 60 | >= | 60% | Upozornění | Sev2 | Windows | 
| Procento využité šířky pásma celkem | 5 | 60 | >= | 75% | Upozornění | Sev2 | Windows | 
| Vypsání procenta využití šířky pásma | 5 | 60 | >= | 60% | Upozornění | Sev2 | Windows | 
| Service Health klienta DHCP | 5 | 12 | <> | 4 (spuštěno) | Kritická | Sev1 | Windows | 
| Service Health klienta DNS | 5 | 12 | <> | 4 (spuštěno) | Kritická | Sev1 | Windows | 
| Service Health protokolu událostí systému Windows | 5 | 12 | <> | 4 (spuštěno) | Kritická | Sev1 | Windows | 
| Service Health brány Windows Firewall | 5 | 12 | <> | 4 (spuštěno) | Kritická | Sev1 | Windows | 
| Service Health RPC | 5 | 12 | <> | 4 (spuštěno) | Kritická | Sev1 | Windows | 
| Service Health serveru | 5 | 12 | <> | 4 (spuštěno) | Kritická | Sev1 | Windows | 
| Vzdálená správa systému Windows Service Health | 5 | 12 | <> | 4 (spuštěno) | Kritická | Sev1 | Windows | 
| Dostupné megabajty paměti | 5 | 10 | < | 100 MB | Kritická | Sev1 | Windows | 
| Bezplatné položky stránkovací tabulky systému | 5 | 10 | <= | 5000 | Kritická | Sev1 | Windows | 
| Paměťové stránky za sekundu | 5 | 10 | >= | 5 000/s | Upozornění | Sev1 | Windows | 
| Procento používané potvrzené paměti | 5 | 10 | > | 80 % | Kritická | Sev1 | Windows | 
| Průměrná doba disku v sekundách pro přenos | 1 | 15 | > | 0.04 s | Upozornění | Sev2 | Windows | 
| Průměrný počet sekund na zápis na disku | 1 | 15 | > | 0.04 s | Upozornění | Sev2 | Windows | 
| Aktuální délka fronty disku | 5 | 60 | >= | 32 | Upozornění | Sev2 | Windows | 
| Doba nečinnosti disku v procentech | 5 | 60 | >= | 20 % | Upozornění | Sev2 | Windows | 

>[!NOTE]
>Lookback Duration představuje, jak často se v okně Hledat znovu vyhledávají hodnoty metriky, například za posledních pět minut.  

>[!NOTE]
>Frekvence představuje, jak často výstraha metrika kontroluje, jestli jsou splněné podmínky, například každou minutu.  Je to rychlost, s jakou je kritérium stavu spuštěno, a lookback je doba, po kterou je vyhodnoceno kritérium stavu. Například kritérium zdravotního stavu je vyhodnoceno, pokud je **využití CPU** větší než 95% s frekvencí 5 minut a zůstane větší než 95% po dobu 15 minut (3 po sobě jdoucí zkušební cykly), stav se aktualizuje na kritickou. závažnost, pokud se ještě nedokončila.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pokud se chcete přihlásit, otevřete [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Úvod do Azure Monitor pro virtuální počítače stavu

Než použijete funkci Health pro jeden virtuální počítač nebo skupinu virtuálních počítačů, je důležité pochopit, jak se zobrazují informace a jaké vizualizace představují.

### <a name="view-health-directly-from-a-vm"></a>Zobrazení stavu přímo z virtuálního počítače

Pokud chcete zobrazit stav virtuálního počítače Azure, v levém podokně virtuálního počítače vyberte **přehledy (Preview)** . Na stránce s přehledy virtuálních počítačů se ve výchozím nastavení otevře karta **stav** a zobrazí se zobrazení stavu virtuálního počítače.

![Přehled stavu Azure Monitor pro virtuální počítače vybraného virtuálního počítače Azure](./media/vminsights-health/vminsights-directvm-health-01.png)

V části **stav virtuálního počítače hosta** tabulka zobrazuje souhrn stavů komponent výkonu monitorovaných podle kritérií stavu pro virtuální počítač a celkový počet výstrah stavu virtuálního počítače vyvolaných součástmi, které nejsou v pořádku. Mezi tyto součásti patří **procesor**, **paměť**, **disk**a **síť**. Rozbalte dvojitou šipku vedle stavu virtuálního počítače hosta, abyste viděli stav svých součástí.

![Azure Monitor pro virtuální počítače stav součásti na vybraném virtuálním počítači Azure](./media/vminsights-health/vminsights-directvm-health-02.png)

Po výběru stavu vedle komponenty se otevře prostředí diagnostiky stavu v kontextu vybrané součásti. Zobrazuje složení stavu této součásti a popisuje, jaká kritéria stavu se používají k výpočtu svého stavu. Další informace najdete v tématech [Diagnostika stavu a práce s kritérii stavu](#health-diagnostics). Další informace o výstrahách najdete v tématu [výstrahy](#alerts).

Stav definovaný pro virtuální počítač je popsán v následující tabulce:

|Ikona |Stav |Význam |
|-----|-------------|---------------|
| |V pořádku |Virtuální počítač je v rámci definovaných stavových podmínek. Tento stav indikuje, že nebyly zjištěny žádné problémy a virtuální počítač pracuje normálně. Díky nadřazenému souhrnnému monitorování se zobrazí stav a odráží nejlepší nebo nejhorší stav podřízeného případu.|
| |Kritická |Stav není v rámci definované podmínky stavu, což značí, že byly zjištěny minimálně některé kritické problémy. Tyto problémy je potřeba řešit, aby se obnovily normální funkce. Díky nadřazenému souhrnnému monitorování se stav zobrazí a odráží nejlepší nebo nejhorší případový stav podřízeného objektu.|
| |Upozornění |Stav je mezi dvěma mezními hodnotami pro definovaný stav, kde jedna indikuje stav varování a druhá označuje kritický stav (tři prahové hodnoty stavu lze nakonfigurovat), nebo pokud Nekritická chyba může způsobit kritické problémy, pokud nevyřešené. Pokud je u jednoho nebo více podřízených objektů ve stavu varování nadřazený monitor, bude mít nadřazený stav upozornění. Pokud je jeden podřízený v kritickém stavu a další podřízenosti ve stavu varování, nadřazený souhrn zobrazí stav jako kritický.|
| |Neznámé |Stav nelze vypočítat z několika důvodů. V následující části najdete další podrobnosti a možná řešení. |

Příčinu neznámého stavu může být způsobeno následujícími problémy:

- Agent byl překonfigurován a již nebude hlásit do pracovního prostoru zadaného, když byla povolena Azure Monitor pro virtuální počítače. Chcete-li nakonfigurovat agenta tak, aby se do pracovního prostoru nahlásil, podívejte se na téma [Přidání nebo odebrání pracovního prostoru](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Virtuální počítač se odstranil.
- Pracovní prostor přidružený k Azure Monitor pro virtuální počítače byl odstraněn. Pokud máte výhody podpory Premier Support, můžete pracovní prostor obnovit. Navštivte [web Premier](https://premier.microsoft.com/) a otevřete žádost o podporu.
- Závislosti řešení se odstranily. Pokud chcete znovu povolit řešení ServiceMap a InfrastructureInsights v pracovním prostoru Log Analytics, přeinstalujte Tato řešení pomocí [šablony Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Nebo použijte možnost konfigurovat pracovní prostor, která se nachází na kartě Začínáme.
- Virtuální počítač se vypnul.
- Služba virtuálního počítače Azure není k dispozici nebo probíhá údržba.
- Dosáhlo se [denního limitu dat nebo doby uchování](../platform/manage-cost-storage.md) pracovního prostoru.

Výběrem možnosti **Zobrazit diagnostiku stavu** otevřete stránku zobrazující všechny součásti virtuálního počítače, přidružená kritéria stavu, změny stavu a další problémy zjištěné monitorovacími součástmi, které se vztahují k virtuálnímu počítači.

Další informace najdete v tématu [Diagnostika stavu](#health-diagnostics).

V části **stav** tabulka zobrazuje souhrn stavů komponent výkonu monitorovaných podle kritérií stavu. Mezi tyto součásti patří **procesor**, **paměť**, **disk**a **síť**. Výběrem komponenty se otevře stránka se seznamem všech kritérií monitorování a stavu této součásti.

Při přístupu ke stavu z virtuálního počítače Azure, na kterém běží Windows, se v části stav **základních služeb**zobrazí stav prvních pět základních služeb Windows. Když vyberete některou ze služeb, otevře se stránka, která obsahuje monitorování kritérií stavu pro danou součást spolu s jejím stavem.

Když vyberete název kritéria stavu, otevře se podokno vlastností. V tomto podokně si můžete prohlédnout podrobnosti o konfiguraci, včetně toho, jestli mají kritéria stavu odpovídající Azure Monitor výstrahy.

Další informace najdete v tématech [Diagnostika stavu a práce s kritérii stavu](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Agregovaná perspektiva virtuálních počítačů

Pokud chcete zobrazit shromažďování stavů pro všechny virtuální počítače ve skupině prostředků, vyberte v navigačním seznamu na portálu **Azure monitor** a pak vyberte **Virtual Machines (Preview)** .

![Zobrazení monitorování virtuálních počítačů z Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

V rozevíracích seznamech **odběr** a **Skupina prostředků** vyberte příslušnou skupinu prostředků, která obsahuje virtuální počítače související se skupinou, a zobrazte jejich nahlášený stav. Váš výběr se vztahuje pouze na funkci stavu a neprovádí se na kartách **výkon** a **Mapa** .

Karta **stav** poskytuje následující informace:

* Kolik virtuálních počítačů je v nesprávném stavu, v jakém jsou v pořádku nebo neodesílají data (označované jako neznámý stav).
* Který a kolik virtuálních počítačů podle operačního systému hlásí stav není v pořádku.
* Kolik virtuálních počítačů není v pořádku kvůli problému zjištěnému pomocí procesoru, disku, paměti nebo síťového adaptéru zařazených do kategorií podle stavu.
* Kolik virtuálních počítačů není v pořádku z důvodu problému zjištěného se základní službou operačního systému v členění podle stavu.

Na kartě **stav** můžete identifikovat kritické problémy zjištěné kritérii stavu monitorování virtuálního počítače a zkontrolovat podrobnosti výstrahy a související články znalostní báze. Tyto články můžou pomoct s diagnostikou a nápravou problémů. Výběrem libovolné závažnosti otevřete stránku [všechny výstrahy](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrované podle této závažnosti.

Seznam **distribuce virtuálních počítačů podle operačního systému** zobrazuje virtuální počítače, které jsou uvedené v rámci edice Windows nebo distribuce systému Linux, spolu s jejich verzí. V jednotlivých kategoriích operačních systémů jsou virtuální počítače dále rozdělené podle stavu virtuálního počítače.

![Perspektiva distribuce virtuálních počítačů pro virtuální počítač Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Vyberte libovolný sloupec, včetně **počtu virtuálních počítačů**, **kritických**, **varovných**, **zdravých**nebo **neznámých**. Zobrazí seznam filtrovaných výsledků na stránce **Virtual Machines** , která se shoduje s vybraným sloupcem.

Pokud chcete například zkontrolovat všechny virtuální počítače, které používají Red Hat Enterprise Linux verze 7,5, vyberte hodnotu **počet virtuálních počítačů** pro tento operační systém a zobrazí seznam virtuálních počítačů odpovídajících tomuto filtru a jejich aktuální stav.

![Příklad souhrnu virtuálních počítačů se systémem Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Klikněte na **Zobrazit stav** zaškrtávací políčko a v tabulce se vrátí stav pro filtrované výsledky.  

![Příklad stavu virtuálních počítačů se systémem Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Pro jednu z položek v seznamu můžete kliknout na odpovídající stav a spustit diagnostiku stavu, který ukazuje, jak se vyhodnocuje stav pro vybraný virtuální počítač. 

Pokud na stránce **Virtual Machines** vyberete název virtuálního počítače ve sloupci **název virtuálního počítače**, budete přesměrováni na stránku **instance virtuálního počítače** . Tato stránka poskytuje další podrobnosti o problémech s kritérii pro výstrahy a stav, které ovlivňují vybraný virtuální počítač. Filtrováním podrobností o stavu výběrem ikony **stavu** v levém horním rohu stránky zjistíte, které součásti nejsou v pořádku. Výstrahy týkající se stavu virtuálních počítačů vyvolané nestavovou komponentou můžete také zobrazit v kategoriích podle závažnosti výstrahy.

V zobrazení **seznamu virtuálních počítačů** vyberte název virtuálního počítače, pro který chcete otevřít stránku **stavu** pro tento virtuální počítač, podobně jako při přímém výběru **Insights (Preview)** z virtuálního počítače.

![Přehledy virtuálních počítačů vybraného virtuálního počítače Azure](./media/vminsights-health/vminsights-directvm-health.png)

Stránka **Virtual Machines (Preview) na stránce Azure monitor** zobrazuje souhrnný stav pro virtuální počítač a výstrahy. Tento stav je rozdělen do kategorií podle závažnosti, které představují výstrahy týkající se stavu virtuálních počítačů, které jsou vyvolány při změně stavu v pořádku na stav v závislosti na kritériích. Když vyberete **virtuální počítače v kritickém stavu** , otevře se stránka se seznamem jednoho nebo více virtuálních počítačů v kritickém stavu.

Když vyberete stav jednoho z virtuálních počítačů, zobrazí se zobrazení **diagnostiky stavu** virtuálního počítače. V tomto zobrazení můžete určit, která kritéria stavu odráží problém se stavem stavu. Po otevření stránky **Diagnostika stavu** se zobrazí všechny součásti virtuálního počítače a jejich přidružená kritéria stavu s aktuálním stavem.

Další informace najdete v tématu [Diagnostika stavu](#health-diagnostics).

Když vyberete **Zobrazit všechna kritéria stavu** , otevře se stránka se seznamem všech kritérií stavu, která jsou k dispozici s touto funkcí. Informace je možné dále filtrovat na základě následujících možností:

* **Zadejte**. Existují tři typy kritérií zdravotního stavu pro vyhodnocení podmínek a shrnutí celkového stavu monitorovaného virtuálního počítače:
    - **Jednotka**. Měří určitý aspekt virtuálního počítače. Tento typ kritérií stavu může kontrolovat čítač výkonu k určení výkonu komponenty, spuštění skriptu k provedení syntetické transakce nebo sledování události, která indikuje chybu. Filtr je ve výchozím nastavení nastaven na hodnotu jednotka.
    - **Závislost**. Poskytuje souhrn stavu mezi různými entitami. Tato kritéria stavu umožňují, aby stav entity byl závislý na stavu jiného typu entity, které spoléhá na úspěšnou operaci.
    - **Agregace**. Poskytuje souhrnný stav podobných kritérií stavu. Kritérium stavu jednotek a závislostí se obvykle konfiguruje v rámci souhrnného kritéria stavu. Kromě poskytování lepší obecné organizace mnoha různých kritérií stavu určených pro entitu poskytuje agregované kritérium stavu jedinečný stav pro různé kategorie entit.

* **Kategorie**. Typ kritérií stavu, který se používá k seskupení podobných kritérií pro účely generování sestav. Tyto kategorie jsou **dostupnost** a **výkon**.

Pokud chcete zjistit, které instance nejsou v pořádku, vyberte hodnotu ve sloupci **špatný stav součásti** . Na této stránce tabulka obsahuje seznam součástí, které jsou v kritickém stavu.

## <a name="health-diagnostics"></a>Diagnostika stavu

Stránka **Diagnostika stavu** umožňuje vizualizovat model stavu virtuálního počítače. Tato stránka obsahuje seznam všech součástí virtuálního počítače, přidružená kritéria stavu, změny stavu a další důležité problémy identifikované monitorovanými součástmi, které se vztahují k virtuálnímu počítači.

![Příklad stránky diagnostiky stavu pro virtuální počítač](./media/vminsights-health/health-diagnostics-page-01.png)

Spusťte diagnostiku stavu pomocí následujících metod:

* Kumulativní stav pro všechny virtuální počítače z hlediska agregované virtuální počítače v Azure Monitor:

    1. Na stránce **stav** vyberte ikonu **kritických**, **varovných**, **zdravých**nebo **neznámých** stavů v části **stav virtuálního počítače hosta**.
    2. Přejít na stránku se seznamem všech virtuálních počítačů, které odpovídají této filtrované kategorii.
    3. Výběrem hodnoty ve sloupci **stav** otevřete diagnostickou diagnostiku stavu daného virtuálního počítače.

* Podle operačního systému z perspektivy agregace virtuálních počítačů v Azure Monitor. V části **distribuce virtuálních počítačů**se při výběru libovolné z hodnot sloupců otevře stránka **Virtual Machines** a vrátí seznam v tabulce, která odpovídá filtrované kategorii. Výběrem hodnoty ve sloupci **stav** otevřete diagnostiku stavu pro vybraný virtuální počítač.
 
* Z virtuálního počítače hosta na kartě **stav** Azure monitor pro virtuální počítače výběrem možnosti **Zobrazit diagnostiku stavu**.

Diagnostika stavu organizuje informace o stavu do dvou kategorií: dostupnost a výkon.
 
Všechna kritéria stavu definovaná pro komponentu, jako je například logický disk, procesor atd., lze zobrazit bez filtrování na těchto dvou kategoriích. Tato zobrazení můžou být v libovolném zobrazení kritérií nebo prostřednictvím filtrování výsledků podle kategorie, když vyberete **dostupnost** nebo **výkon**.

Kategorie kritéria se taky může zobrazit vedle sloupce **kritéria stavu** . Pokud kritéria neodpovídají vybrané kategorii, zobrazí se ve sloupci **kritéria stavu** zpráva o tom, že **pro vybranou kategorii nejsou k dispozici žádná kritéria stavu** .

Stav kritérií stavu je definován jedním ze čtyř typů: **Kritická**, **varovná**, **v pořádku**a **neznámá**. První tři jsou konfigurovatelné, což znamená, že mezní hodnoty monitorů můžete upravit přímo v podokně Konfigurace **kritérií stavu** . To je možné taky pomocí operace Azure Monitor REST API [Update monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Neznámá** možnost není konfigurovatelná a je vyhrazena pro konkrétní scénáře.

Stránka **Diagnostika stavu** má tři hlavní části:

* Model komponent
* Kritéria stavu
* Změny stavu

![Sekce stránky diagnostiky stavu](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Model komponenty

Sloupec úplně vlevo na stránce **diagnostiky stavu** je **model komponent**. V tomto sloupci se zobrazí všechny součásti, které jsou přidružené k virtuálnímu počítači, spolu s jejich aktuálním stavem.

V následujícím příkladu jsou zjištěné součásti **disk**, **logický disk**, **procesor**, **paměť**a **operační systém**. V tomto sloupci jsou zjištěna a zobrazena více instancí těchto komponent.

Následující obrázek například ukazuje, že virtuální počítač má dvě instance logických disků, **C:** a **D:** , které jsou v dobrém stavu:

![Příklad modelu komponenty prezentovaného v diagnostice stavu](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Kritéria stavu

Prostřední sloupec na stránce Diagnostika stavu je **kritéria stavu**. Model stavu definovaný pro virtuální počítač se zobrazí v hierarchickém stromu. Model stavu virtuálního počítače se skládá z jednotek a agregovaných kritérií pro stav.

![Příklady kritérií stavu prezentovaných v diagnostice stavu](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Kritérium stavu měří stav monitorované instance, což může být prahová hodnota, stav entity a tak dále. Kritérium zdravotního stavu má buď dvě, nebo tři konfigurovatelné prahové hodnoty stavu, jak je popsáno výše. V každém okamžiku může být kritérium stavu pouze v jednom z jeho možných stavů.

Model stavu definuje kritéria, která určují stav celkového cíle a součástí cíle. Hierarchie kritérií je uvedena v části **kritéria stavu** na stránce **Diagnostika stavu** .

Zásada souhrnu stavu je součástí konfigurace agregovaných kritérií stavu (výchozí nastavení je nastaveno na **nejhorší-of**). Výchozí sadu kritérií stavu spuštěných jako součást této funkce najdete v části [Podrobnosti o konfiguraci monitorování](#monitoring-configuration-details) v tomto článku.

K získání seznamu všech kritérií stavu můžete použít také seznam Azure Monitor REST API [monitorovat instance podle prostředku](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) . Tato kritéria zahrnují podrobnosti konfigurace spuštěné na prostředku virtuálního počítače Azure.

Typ kritérií pro stav **jednotky** může mít upravenou konfiguraci tak, že vyberete odkaz tři tečky na pravou stranu. Vyberte **Zobrazit podrobnosti** a otevřete podokno konfigurace.

![Příklad konfigurace kritérií stavu](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Pokud v podokně Konfigurace pro vybraná kritéria stavu použijete příklad Průměrná doba disku v **sekundách pro zápis**, je možné nastavit prahovou hodnotu s jinou číselnou hodnotou. Je to monitor se dvěma stavy, což znamená, že se dá změnit jenom v **pořádku** na **varování**.

Další kritéria stavu někdy používají tři stavy, kde můžete nakonfigurovat hodnotu pro upozornění a kritické prahové hodnoty stavu. Prahovou hodnotu můžete také upravit pomocí Azure Monitor REST API [monitorování konfigurace](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Použití změn konfigurace kritérií stavu u jedné instance je aplikuje na všechny monitorované instance. Pokud například vyberete možnost **disk-1 D:** a pak upravíte **průměrnou dobu v sekundách pro zápis** , tato změna se bude vztahovat na všechny instance zjištěné a monitorované na virtuálním počítači.


![Konfigurace kritérií stavu jako příklad monitorování jednotky](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Pokud se chcete dozvědět víc o kritériích pro stav, zahrnuli jsme články znalostní báze, které vám pomůžou identifikovat problémy, příčiny a řešení. Kliknutím na **Zobrazit informace** na stránce zobrazíte související článek znalostní báze.

Pokud chcete zkontrolovat všechny články znalostní báze, které jsou součástí Azure Monitor pro virtuální počítače stavu, přečtěte si [dokumentaci ke službě Azure monitor Health Knowledge](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Změny stavu

Sloupec úplně vpravo stránky **diagnostiky stavu** je **Změna stavu**. V tomto sloupci se zobrazují všechny změny stavu přidružené k kritériím stavu vybraným v části **kritéria stavu** nebo změně stavu virtuálního počítače, pokud byl vybrán virtuální počítač ze sloupce **Component model** nebo **kritéria stavu** v tabulce.

![Příklady změn stavu prezentovaných v diagnostice stavu](./media/vminsights-health/health-diagnostics-page-statechanges.png)

V následující části jsou uvedeny stavy kritérií stavu a související čas. Tyto informace zobrazují nejnovější stav v horní části sloupce.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Přidružení ke sloupcům modelu komponenty, kritériím stavu a změnám stavu

Tři sloupce jsou vzájemně propojeny. Když vyberete instanci ve sloupci **model komponenty** , je sloupec **kritéria stavu** filtrován do tohoto zobrazení součásti. Odpovídajícím způsobem se sloupec **změny stavu** aktualizuje na základě vybraných kritérií stavu.

![Příklad výběru monitorované instance a výsledků](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Pokud například vyberete položku *disk-1 d:* ze seznamu v části **model komponenty**, **kritéria stavu** filtry na *disk-1d:* a **změny stavu** zobrazí změnu stavu na základě dostupnosti *disku-1 D:* .

Pokud se chcete podívat na aktualizovaný stav, můžete stránku diagnostiky stavu aktualizovat výběrem odkazu **aktualizovat** . Pokud dojde k aktualizaci stavu kritéria stavu na základě předem definovaného intervalu cyklického dotazování, tato úloha vám umožní vyhnout se čekání a projevit nejnovější stav. **Stav kritéria** stavu je filtr, který umožňuje určit rozsah výsledků na základě vybraného stavu: V pořádku, upozornění, kritická, neznámá a veškerá. Čas **Poslední aktualizace** v pravém horním rohu představuje čas poslední aktualizace stránky diagnostiky stavu.

## <a name="alerts"></a>Upozornění

Stav Azure Monitor pro virtuální počítače se integruje s [výstrahami Azure](../../azure-monitor/platform/alerts-overview.md). Vyvolá výstrahu v případě, že při zjištění předdefinovaných kritérií dojde ke změně stavu v pořádku na stav není v pořádku. Výstrahy jsou rozdělené podle závažnosti, od závažnost 0 do závažnost 4, s závažnost 0 jako nejvyšší úrovní.

Výstrahy nejsou přidruženy ke skupině akcí, které vám upozorní na aktivaci výstrahy. Uživatel s rolí vlastníka v oboru předplatného musí nakonfigurovat oznámení podle postupu v části [Konfigurace výstrah](#configure-alerts) .

Celkový počet výstrah stavu virtuálního počítače zařazených do kategorií podle závažnosti je k dispozici na řídicím panelu **stav** v části **výstrahy** . Když vyberete buď celkový počet výstrah, nebo číslo odpovídající úrovni závažnosti, otevře se stránka **výstrahy** a zobrazí se všechny výstrahy odpovídající vašemu výběru.

Pokud například vyberete řádek, který odpovídá **závažnost úrovně 1**, zobrazí se následující zobrazení:

![Příklad všech výstrah úrovně závažnosti 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Stránka **všechny výstrahy** není vymezená, aby zobrazovala pouze výstrahy vyhovující vašemu výběru. Filtruje se taky podle **typu prostředku** , aby se zobrazily jenom výstrahy stavu vyvolané prostředkem virtuálního počítače. Tento formát se projeví v seznamu výstrah v rámci **prostředku cíl**sloupce, kde se zobrazí virtuální počítač Azure, který vyvolal výstrahu, když byla splněna podmínka není v pořádku.

V tomto zobrazení nejsou určeny výstrahy z jiných typů prostředků nebo služeb. Tyto výstrahy zahrnují výstrahy protokolu, které jsou založené na dotazech protokolů nebo upozorněních metrik, které byste normálně viděli z výchozí Azure Monitor stránky [všechna upozornění](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) .

Toto zobrazení můžete filtrovat výběrem hodnot v rozevíracích nabídkách v horní části stránky.

|Sloupec |Popis |
|-------|------------|
|Subscription |Vyberte předplatné služby Azure. V zobrazení jsou zahrnuté jenom výstrahy ve vybraném předplatném. |
|Skupina prostředků |Vyberte jednu skupinu prostředků. V zobrazení jsou zahrnuté jenom výstrahy s cíli ve vybrané skupině prostředků. |
|Typ prostředku |Vyberte jeden nebo více typů prostředků. Ve výchozím nastavení jsou vybrané jenom výstrahy cílových **virtuálních počítačů** a zahrnují se v tomto zobrazení. Tento sloupec je k dispozici až po zadání skupiny prostředků. |
|Resource |Vyberte prostředek. V zobrazení jsou zahrnuty pouze výstrahy s tímto prostředkem jako cíl. Tento sloupec je k dispozici až po zadání typu prostředku. |
|severity |Vyberte Závažnost výstrahy nebo vyberte možnost **vše** , pokud chcete zahrnout výstrahy všech závažnosti. |
|Stav monitorování |Vyberte podmínku monitorování, pokud chcete filtrovat výstrahy, pokud byly vyvolány nebo vyřešeny systémem, pokud již podmínka není aktivní. Případně můžete vybrat možnost **vše** , pokud chcete zahrnout výstrahy všech podmínek. |
|Stav upozornění |Pokud chcete zahrnout výstrahy všech stavů, vyberte stav výstrahy, **Nový**, **Potvrdit**, **Uzavřeno**nebo **vše** . |
|Monitorovat službu |Vyberte službu nebo vyberte možnost **vše** , pokud chcete zahrnout všechny služby. Pro tuto funkci se podporují jenom výstrahy z virtuálního počítače Insights.|
|Časový rozsah| V zobrazení jsou zahrnuté jenom výstrahy aktivované v rámci vybraného časového období. Podporovány jsou hodnoty za poslední hodinu, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

Když vyberete výstrahu, zobrazí se stránka s **podrobnostmi výstrahy** . Tato stránka poskytuje podrobnosti o výstraze a umožňuje změnit její stav.

Další informace o správě výstrah najdete v tématu [Vytvoření, zobrazení a správa výstrah pomocí Azure monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Vytváření nových výstrah na základě kritérií stavu nebo změně stávajících pravidel upozornění na stav v Azure Monitor z portálu není aktuálně podporováno.


![Podokno podrobností výstrahy pro vybranou výstrahu](./media/vminsights-health/alert-details-pane-01.png)

Můžete změnit stav výstrahy pro jednu nebo více výstrah tak, že je vyberete a pak vyberete **změnit stav** ze stránky **všechna upozornění** v levém horním rohu. V podokně **změnit stav výstrahy** vyberte jeden ze stavů, do pole **Komentář** přidejte popis změny a potvrďte provedené změny kliknutím na **tlačítko OK** . Když jsou informace ověřeny a jsou aplikovány změny, Sledujte průběh v nabídce **oznámení** .

### <a name="configure-alerts"></a>Konfigurace upozornění
Z Azure Portal nemůžete spravovat určité úlohy správy výstrah. Tyto úlohy se musí provádět pomocí [REST API Azure monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Zejména:

- Povolení nebo zakázání výstrahy pro kritéria stavu
- Nastavení oznámení pro výstrahy kritérií stavu

Každý příklad používá [ARMClient](https://github.com/projectkudu/armclient) na počítači s Windows. Pokud tuto metodu neznáte, přečtěte si téma [použití ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Povolení nebo zakázání pravidla výstrahy

Chcete-li povolit nebo zakázat výstrahu pro určitá kritéria stavu, vlastnost **alertGeneration** musí být upravena hodnotou buď **disabled** , nebo **Enabled**.

Chcete-li identifikovat *monitorId* pro konkrétní kritéria stavu, následující příklad ukazuje, jak zadat dotaz na tuto hodnotu pro kritéria **logický disku za přenos**:

1. V okně terminálu zadejte **armclient. exe Login**. Zobrazí se výzva, abyste se přihlásili do Azure.

2. Zadáním následujícího příkazu načtete všechna aktivní kritéria stavu na konkrétním virtuálním počítači a určíte hodnotu vlastnosti *monitorId* :

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Následující příklad ukazuje výstup příkazu *ARMCLIENT Get* . Poznamenejte si hodnotu *MonitorId*. Tato hodnota je vyžadována pro další krok, kde je nutné zadat ID kritérií stavu a upravit její vlastnost k vytvoření výstrahy.

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

3. Zadejte následující příkaz pro úpravu vlastnosti *alertGeneration* :

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Zadejte příkaz GET použitý v kroku 2 a ověřte, zda je hodnota vlastnosti nastavena na **disabled (zakázáno**).

#### <a name="associate-an-action-group-with-health-criteria"></a>Přidružení skupiny akcí k kritériím stavu

Azure Monitor pro virtuální počítače Health podporuje SMS a e-mailová oznámení, když jsou výstrahy vygenerovány z poškozených kritérií stavu. Chcete-li konfigurovat oznámení, poznamenejte si název konfigurované skupiny akcí pro odesílání oznámení SMS nebo e-mailových oznámení.

>[!NOTE]
>Tuto akci je nutné provést u každého monitorovaného virtuálního počítače, pro který chcete dostávat oznámení. Nevztahuje se na všechny virtuální počítače ve skupině prostředků.

1. V okně terminálu zadejte *armclient. exe Login*. Zobrazí se výzva, abyste se přihlásili do Azure.

2. Zadejte následující příkaz, který přidruží skupinu akcí k pravidlům výstrah:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Chcete-li ověřit, zda byla hodnota vlastnosti **actionGroupResourceIds** úspěšně aktualizována, zadejte následující příkaz:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Výstup by měl vypadat jako v následujících kritériích:
    
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

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit omezení a celkový výkon virtuálních počítačů, přečtěte si téma [zobrazení výkonu virtuálního počítače Azure](vminsights-performance.md).
- Další informace o zjištěných závislostech aplikace najdete v tématu [zobrazení mapy Azure monitor pro virtuální počítače](vminsights-maps.md).
