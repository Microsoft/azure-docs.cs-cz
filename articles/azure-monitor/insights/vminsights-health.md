---
title: Pochopení stavu virtuálních počítačů Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete porozumět stavu virtuálních počítačů a službou Azure Monitor základní operační systémy pro virtuální počítače.
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
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340146"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Pochopení stavu virtuálních počítačů Azure

Azure obsahuje služby pro konkrétní role a úlohy v prostoru pro monitorování, ale neposkytuje podrobné stavu perspektivy operačních systémů (OSs) hostovaných na Azure virtual machines (VM). I když používáte Azure Monitor pro různé podmínky má nejsou určeny pro modelování a představují stavu základní součásti nebo celkový stav virtuálních počítačů.

Pomocí Azure monitoru stavu virtuálních počítačů můžete aktivně Monitoruje dostupnost a výkon Windows nebo Linuxem hostovaného operačního systému. Stav funkce používá model, který představuje klíčových komponent a jejich vztahy. najdete tu kritéria, která určuje, jak měřit stav součásti a odesílá upozornění, když zjistí podmínku není v pořádku.

Zobrazení celkový stav virtuálního počítače Azure a podkladovému operačnímu systému může být dodržen za dvou hledisek: přímo z virtuálního počítače, nebo ve všech virtuálních počítačích ve skupině prostředků ze služby Azure Monitor.

Tento článek ukazuje, jak rychle posoudit, prozkoumat a vyřešit problémy se stavem zjištěném službou Azure Monitor pro funkci stav virtuálních počítačů.

Informace o konfiguraci monitorování Azure pro virtuální počítače najdete v tématu [povolit monitorování Azure pro virtuální počítače](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Podrobnosti o konfiguraci monitorování

Tato část popisuje výchozí kritéria stavu ke sledování Windows Azure a virtuální počítače s Linuxem. Všechna kritéria stavu jsou nakonfigurovaná tak, aby Odeslat výstrahu, když zjistí podmínku není v pořádku.

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

### <a name="linux-vms"></a>Virtuální počítače s Linuxem

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

Chcete-li přihlásit, přejděte na [webu Azure portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Úvod do Azure monitoru stavu virtuálních počítačů

Před použitím funkce stavu pro jeden virtuální počítač nebo skupinu virtuálních počítačů, je důležité pochopit, jak je uvedené informace a co představují vizualizace.

### <a name="view-health-directly-from-a-vm"></a>Zobrazit stav přímo z virtuálního počítače

Chcete-li zobrazit stav virtuálního počítače Azure, vyberte **Insights (preview)** v levém podokně virtuálního počítače. Na stránce přehledu virtuálního počítače **stavu** karta je otevřený ve výchozím nastavení a zobrazuje stav virtuálního počítače.

![Azure Monitor pro přehled stavu virtuálních počítačů z vybraných virtuálních počítačů Azure](./media/vminsights-health/vminsights-directvm-health.png)

V **stavu** ve skupině **stavu virtuálního počítače hosta**, v tabulce jsou uvedeny stav virtuálního počítače a celkový počet upozornění na stav virtuálního počítače vyvolané komponentou není v pořádku.

Další informace najdete v tématu [výstrahy](#alerts).

Stavy definované pro virtuální počítač jsou popsány v následující tabulce:

|Ikona |Stav |Význam |
|-----|-------------|---------------|
| |V pořádku |Virtuální počítač je ve stavu definované podmínky. Tento stav indikuje existuje jsou zjištěny žádné problémy a virtuální počítač funguje normálně. Nadřazené souhrnné monitorování stavu shrnuje a odráží nejlepší a nejhorší stav podřízeného.|
| |Kritická |Stav není v podmínce definované stavu označující, že jeden nebo více problémů byly zjištěny. Pokud chcete obnovit normální funkce musí tyto problémy řešeny. Stav v pořádku s nadřazené souhrnné monitorování shrnuje stav z a odráží nejlepší a nejhorší stav podřízeného.|
| |Upozornění |Stav není mezi dvěma prahovými hodnotami podmínky definované stavu, kde jeden označuje stav varování a druhý označující kritický stav (tři prahové hodnoty stavu health můžete nakonfigurovat) nebo když nekritické problém může způsobit kritické problémy, pokud Nevyřešená. S nadřazené souhrnné monitorování jedna nebo víc podřízených je ve stavu varování, bude odrážet nadřazené varovném stavu. Jeden podřízený prvek je v kritickém stavu a jiné podřízené ve stavu upozornění, zobrazí nadřazené souhrnné jako kritický stav.|
| |Neznámé |Stav nelze vypočítat z několika důvodů. V následujících částech najdete další podrobnosti a možná řešení. |

Stav neznámý stav může být způsobeno následujícím problémům:

- Agent byl znovu konfigurován s a sestavy do pracovního prostoru už zadaný, když bylo povoleno monitorování Azure pro virtuální počítače. Konfigurace agenta na generování sestav do pracovního prostoru najdete [přidání nebo odebrání pracovního prostoru](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Virtuální počítač se odstranil.
- Pracovní prostor, přidružené k monitorování Azure pro virtuální počítače se odstranil. Pracovní prostor můžete obnovit, pokud budete mít výhody podpory Premier Support. Přejděte na [Premier](https://premier.microsoft.com/) a žádost o podporu.
- Řešení závislostí bylo odstraněno. Opětovné povolení ServiceMap a InfrastructureInsights řešení ve vašem pracovním prostoru Log Analytics, přeinstalujte pomocí těchto řešení [šablony Azure Resource Manageru](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Nebo použijte možnost konfigurace pracovního prostoru v kartě začít pracovat.
- Virtuální počítač byl vypnut.
- Služba Azure virtuální počítač není k dispozici, nebo právě probíhá údržba se.
- Pracovní prostor [limitu uchovávání dat nebo dat o denním](../platform/manage-cost-storage.md) byla splněna.

Vyberte **zobrazit stav diagnostiky** otevře se stránka zobrazující všechny součásti virtuálního počítače a související kritéria, změny stavu a dalších problémů zjištěných monitorování součásti související se virtuální počítač.

Další informace najdete v tématu [stav diagnostiky](#health-diagnostics).

V **stavu** části tabulka zobrazuje souhrn stavů výkonu komponent monitoruje stav kritéria. Mezi tyto komponenty patří **procesoru**, **paměti**, **disku**, a **sítě**. Výběrem komponenty se otevře stránka obsahující seznam všech monitorování kritéria a stav této součásti.

Při přístupu k stavu z virtuálního počítače Azure, na kterém běží Windows, stav pěti základních služeb Windows se zobrazí v části **Core services stavu**. Výběr některých služeb se otevře stránka obsahující seznam kritéria stavu monitorování pro danou součást spolu s jeho stav.

Název stavu kritéria vyberete, otevře se podokno vlastností. V tomto podokně můžete zkontrolovat podrobnosti o konfiguraci, včetně kritéria stavu máte shodná výstraha Azure Monitor.

Další informace najdete v tématu [stav diagnostiky a práci s kritéria](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Agregační perspektivy virtuálního počítače

Chcete-li zobrazit stav kolekce všech vašich virtuálních počítačů ve skupině prostředků, vyberte **Azure Monitor** ze seznamu navigace v portálu a pak vyberte **virtuálních počítačů (preview)** .

![Virtuální počítač statistiky, sledování zobrazení ze služby Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

V **předplatné** a **skupiny prostředků** rozevíracích seznamech vyberte odpovídající prostředek skupiny, která zahrnuje virtuální počítače související s skupiny, chcete-li zobrazit jejich ohlášené stavu. Výběr pouze se vztahuje na funkce stav a nepřenáší na **výkonu** nebo **mapy** karty.

**Stavu** karta obsahuje následující informace:

* Kolik virtuálních počítačů jsou ve stavu kritický nebo není v pořádku, a kolik jsou v pořádku nebo se odesílání dat (označované jako neznámém stavu).
* Které a kolik virtuálních počítačů podle operačního systému zasílání zpráv o stavu není v pořádku.
* Kolik virtuálních počítačů jsou kvůli problému zjištěnému s procesoru, disku, paměti nebo síťový adaptér, zařazených do kategorií podle stavu není v pořádku.
* Kolik virtuálních počítačů jsou kvůli problému zjištěnému službou jádra operačního systému, zařazených do kategorií podle stavu není v pořádku.

Na **stavu** kartu, můžete identifikovat kritické problémy zjištěné podle kritérií stavu monitorování virtuálního počítače a zkontrolujte podrobnosti výstrahy a články znalostní báze přidružené. Tyto články vám můžou pomoct v diagnostiky a problémy. Vyberte některou z závažnosti otevřete [všechny výstrahy](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) stránka se vyfiltruje podle tohoto závažnosti.

**Distribuce virtuálního počítače podle operačního systému** seznam virtuálních počítačů uvedených ve verzi Windows nebo distribuce Linuxu, spolu s jejich verze. V každé kategorii operačního systému virtuálních počítačů jsou rozdělené další na základě stavu virtuálního počítače.

![Přehled virtuálních počítačů virtuálního počítače distribučního perspektivy](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Vyberte libovolný sloupec včetně **počet virtuálních počítačů**, **kritický**, **upozornění**, **pořádku**, nebo **neznámý**. Zobrazení filtrované výsledky v seznamu **virtuálních počítačů** stránky splňujících vybraný sloupec.

Například pokud chcete zkontrolovat všechny virtuální počítače, na kterých běží Red Hat Enterprise Linux verze 7.5, vyberte **počet virtuálních počítačů** hodnotu tohoto operačního systému a zobrazí se seznam virtuálních počítačů, které odpovídají použitému filtru a jejich aktuálního stavu.

![Příklad souhrnu virtuálních počítačů Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

V **virtuálních počítačů** stránky, pokud vyberete název virtuálního počítače ve sloupci **název virtuálního počítače**, budete přesměrováni na **instance virtuálního počítače** stránky. Tato stránka obsahuje podrobnosti o upozornění a kritéria problémy se stavem, které mají vliv na vybraný virtuální počítač. Podrobnosti o stavu stavu filtrovat výběrem **stav** ikony v levém horním rohu stránky zobrazíte komponenty, které nejsou v pořádku. Můžete také zobrazit stav virtuálního počítače výstrahy vyvolané komponentou není v pořádku zařazený do kategorie službou závažnost výstrahy.

Z **seznamu virtuálních počítačů** zobrazit, vyberte název virtuálního počítače a otevřete **stavu** stránky pro tento virtuální počítač, podobně jako v případě, že jste vybrali **Insights (preview)** z virtuálního počítače přímo.

![Přehled virtuálních počítačů z vybraných virtuálních počítačů Azure](./media/vminsights-health/vminsights-directvm-health.png)

**Insights (preview)** stránka zobrazuje souhrn stav virtuálního počítače a výstrahy. Tento stav jsou rozdělené do kategorií podle závažnosti, která představuje virtuální počítač výstrahy týkající se stavu aktivovaná při změně stavu v pořádku na není v pořádku, na základě kritérií. Výběr **virtuálních počítačů do kritického stavu** otevře stránku se seznamem nejmíň jednoho virtuálního počítače v kritickém stavu.

Výběr stavu pro jeden z virtuálních počítačů ukazuje **stav diagnostiky** zobrazení virtuálního počítače. V tomto zobrazení můžete určit, která kritéria stavu se odráží problém – stav. Když **stav diagnostiky** stránka se otevře, zobrazuje všechny součásti virtuálního počítače a jeho přidruženého stavu kritéria a aktuální stav.

Další informace najdete v tématu [stav diagnostiky](#health-diagnostics).

Výběr **zobrazit všechna kritéria stavu** se otevře stránka zobrazuje seznam všech kritéria stavu k dispozici s touto funkcí. Informace můžete dále filtrovat podle následujících možností:

* **Typ**. Existují tři typy stavů kritérií vyhodnocení podmínky a souhrnné celkového stavu monitorovaných virtuálních počítačů:
    - **Jednotka**. Měří určitý aspekt virtuálního počítače. Tento typ kritérií stavu může kontrolovat čítač výkonu pro zjištění výkonu komponentě, spustit skript k provedení virtuální transakce nebo sledovat události indikující chybu. Ve výchozím nastavení k jednotce pro filtr.
    - **Závislost**. Poskytuje shrnutí stavů mezi různými entitami. Tato kritéria stavu umožňuje stavu entity závisí na stavu jiného typu entity, která se spoléhá na úspěšně fungovat.
    - **Agregační**. Poskytuje souhrnný stav podobných kritérií stavu. Kritéria stavu jednotek a závislostí se obvykle konfigurují v rámci agregovaný kritéria. Kromě lepší uspořádání obecné mnoho různých stavů kritérií zaměřený na entitu, nabízí agregovaný kritérium jedinečný stav pro různé kategorie entit.

* **Kategorie**. Typ kritéria použít k seskupení podobných kritérií pro účely vytváření sestav. Tyto kategorie jsou **dostupnosti** a **výkonu**.

Pokud chcete zobrazit, která instance nejsou v pořádku, vyberte hodnotu v rámci **není v pořádku, součást** sloupce. Na této stránce tabulka uvádí komponenty, které jsou v kritickém stavu.

## <a name="health-diagnostics"></a>Stav diagnostiky

**Stav diagnostiky** stránka umožňuje vizualizovat modelu stavu virtuálního počítače. Tato stránka obsahuje všechny součásti virtuálního počítače, přidruženého stavu kritéria, změny stavu a další závažné potíže identifikovaný sledované součásti související se virtuální počítač.

![Příklad stránky stav diagnostiky pro virtuální počítač](./media/vminsights-health/health-diagnostics-page-01.png)

Diagnostika stavu spuštění pomocí následujících metod:

* Podle souhrnu stavu pro všechny virtuální počítače z hlediska agregační virtuálních počítačů ve službě Azure Monitor:

    1. Na **stavu** stránky, vyberte ikonu **kritický**, **upozornění**, **pořádku**, nebo **neznámý** Stav v části **stavu virtuálního počítače hosta**.
    2. Přejděte na stránku, která obsahuje seznam všech virtuálních počítačů odpovídající dané filtrované kategorie.
    3. Vyberte hodnotu v **stav** sloupci otevřete diagnostické stavu omezená na tomto virtuálním počítači.

* Podle operačního systému z hlediska agregační virtuálních počítačů ve službě Azure Monitor. V části **distribuci virtuálních počítačů**, vyberete některou z hodnot sloupců se otevře **virtuálních počítačů** stránce a v tabulce odpovídající kategorii filtrovaný seznam. Výběrem hodnoty v rámci **stav** sloupec otevře diagnostiku stavu pro vybraný virtuální počítač.
 
* Z virtuálního počítače v Azure Monitor pro virtuální počítače hosta **stavu** kartu tak, že vyberete **zobrazit stav diagnostiky**.

Diagnostika stavu organizuje informace o stavu do dvou kategorií: dostupnost a výkon.
 
Bez filtrování na dvě kategorie lze zobrazit všechna kritéria stavu definované pro komponentu, jako je například logický disk, procesoru a tak dále. Tato zobrazení může být v zobrazení veškeré kritéria nebo prostřednictvím filtrování výsledků podle buď kategorie, když vyberete **dostupnosti** nebo **výkonu**.

Navíc kritéria kategorii uvidíte vedle **kritéria** sloupce. Pokud kritériím neodpovídají vybrané kategorie zprávu s oznámením **žádná kritéria stavu k dispozici pro vybranou kategorii** se zobrazí v **kritéria** sloupce.

Stav kritéria stavu je určené jedním ze čtyř typů: **Kritické**, **upozornění**, **v pořádku**, a **neznámý**. První tři se dají konfigurovat, což znamená, že můžete upravit přímo v monitorování prahových hodnot **kritéria** konfigurační podokno. To je také možné pomocí REST API služby Azure Monitor [monitorování operace aktualizace](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Neznámý** není Konfigurovatelný a je vyhrazený pro konkrétní scénáře.

**Stav diagnostiky** stránka má tři hlavní části:

* Komponenta modelu
* Kritéria stavu
* Změny stavu

![Oddíly stránku Stav diagnostiky](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Komponenta modelu

V levém sloupci **stav diagnostiky** stránka je **modelu**. Všechny součásti, které jsou spojené s virtuálním Počítačem, se zobrazí v tomto sloupci spolu s jejich aktuálního stavu.

V následujícím příkladu se zjištěné součásti **disku**, **logického disku**, **procesoru**, **paměti**, a  **Operační systém**. Více instancí tyto součásti jsou zjistí a zobrazí v tomto sloupci.

Například následující obrázek znázorňuje, že virtuální počítač se dvěma instancemi logických disků **C:** a **D:** , které jsou v pořádku:

![Příklad modelu v stav diagnostiky](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Kritéria stavu

V prostředním sloupci na stránce stavu diagnostiky se **kritéria**. Health model definované pro virtuální počítač se zobrazí v hierarchické stromové struktury. Health model pro virtuální počítač se skládá z jednotky a agregovaný kritéria.

![Příklad kritéria v stav diagnostiky](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Kritéria stavu měří stav monitorovaných instance, které by mohly být prahovou hodnotu, stav entity a tak dále. Kritéria stavu má dvě nebo tři konfigurovatelné zdravotní stav prahové hodnoty, jak je popsáno výše. V libovolném okamžiku může být kritérium stavu pouze do jednoho z jeho možných stavů.

Health model definuje kritéria určující stav celkové cíle a komponenty cíle. Hierarchie kritéria je zobrazena ve **kritéria** části na **stav diagnostiky** stránky.

Souhrn stavů zásady jsou součástí konfigurace agregovaný kritéria (výchozí hodnota je nastavena na **nejhorší of**). Výchozí sadu kritérií stavu spuštěna jako součást tuto funkci můžete najít [monitorování podrobnosti o konfiguraci](#monitoring-configuration-details) části tohoto článku.

Můžete použít také rozhraní REST API pro monitorování Azure [monitorování instance seznamu prostředkem](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) k získání seznamu všech kritérií stavu. Tato kritéria obsahuje podrobnosti o konfiguraci spouštění prostředku virtuálního počítače Azure.

**Jednotky** typ kritérií stavu může mít jeho konfiguraci upravit tak, že vyberete odkaz tlačítko se třemi tečkami na pravé straně. Vyberte **zobrazit podrobnosti** a otevřete tak podokno konfigurace.

![Příklad kritéria stavu konfigurace](./media/vminsights-health/health-diagnostics-vm-example-02.png)

V podokně Konfigurace pro vybraný stav kritéria, pokud používáte v příkladu **průměrné sekund za zápisu disku**, prahová hodnota se dá nakonfigurovat s jinou číselnou hodnotu. Monitorování dvou stavů, což znamená, ho můžete změnit pouze z je **pořádku** k **upozornění**.

Další kritéria stavu v některých případech pomocí tří stavů, ve kterém můžete nakonfigurovat hodnotu pro upozornění a kritickou mezní hodnoty stavu stavu. Můžete také upravit prahové hodnoty pomocí REST API služby Azure Monitor [konfigurace monitoru](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Provádění změn konfigurace kritérií stavu do jedné instance je platí pro všechny sledované instance. Pokud vyberete třeba **disku D:-1** a potom změňte **průměrné sekund za zápisu disku** prahovou hodnotu, změna se vztahuje na všechny instance zjišťování a monitorování ve virtuálním počítači.


![Konfigurace stavu kritéria příkladu monitorování jednotky](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Pokud chcete získat další informace o stavu kritéria, zahrnuli jsme články ve znalostní bázi, abyste mohli snadno identifikovat problémy, příčiny a řešení. Vyberte **zobrazení informací o** na stránce najdete v článku znalostní báze související.

Všechny články znalostní báze dodává v rámci Azure monitorování pro stav virtuálních počítačů najdete v tématu [dokumentace k Azure Monitor stavu znalostní báze](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Změny stavu

Sloupec nejvíce vpravo **stav diagnostiky** stránka je **změny stavu**. V tomto sloupci jsou uvedeny všechny změny stavu přidružené k kritéria stavu vybraná v **kritéria** části nebo změnit stav virtuálního počítače, pokud byl vybrán virtuální počítač **modelu** nebo  **Kritéria stavu** sloupci tabulky.

![Příklad změny stavu v stav diagnostiky](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Následující části se zobrazuje stav kritéria stavu a přidružené čas. Tyto informace zobrazují nejnovější stav v horní části sloupce.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Přidružení sloupců modelu, kritéria stavu a změny stavu

Tři sloupce jsou vzájemně propojena mezi sebou. Když vyberete instance v **modelu** sloupce, **kritéria** je sloupec filtrován zobrazení komponenty. Odpovídajícím způsobem **změny stavu** sloupec je aktualizován na základě kritérií vybraný stav.

![Příklad výběru sledované instance a výsledky](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Například, pokud vyberete *disku – 1 D:* ze seznamu **modelu**, **kritéria** filtrů *Disk - 1 D:* a  **Stav změny** ukazuje změnu stavu na základě dostupnosti z *disku – 1 D:* .

Pokud chcete zobrazit stav aktualizace stavu, můžete aktualizovat stránku diagnostiky stavu tak, že vyberete **aktualizovat** odkaz. Při aktualizaci stavu kritérium stav podle předem definovaného intervalu dotazování tento úkol vám umožní vyhnout čekání a odráží nejnovější stav. **Kritéria stavu** je filtr, který umožňuje určit obor výsledky na základě vybraného stavu: V pořádku, varování, kritické, neznámý a všechny. **Poslední aktualizace** čas v pravém horním rohu představuje čas posledního stavu diagnostiky stránka byla obnovena.

## <a name="alerts"></a>Výstrahy

Monitorování stavu virtuálních počítačů Azure se integruje s [Azure Alerts](../../azure-monitor/platform/alerts-overview.md). Vyvolá výstrahu při změně předdefinované kritéria, když zjistí, v dobrém stavu do stavu není v pořádku. Výstrahy jsou rozdělené podle závažnosti, z závažnost 0 až 4 závažnost s závažnost 0 jako nejvyšší úrovně.

Výstrahy nejsou přidruženy k skupinu akcí, které vás upozorní, pokud byla výstraha. Vlastník předplatného musíte nakonfigurovat oznámení podle postupu v [konfigurace výstrah](#configure-alerts) oddílu.

Celkový počet zařazených do kategorií podle závažnosti upozornění na stav virtuálního počítače je k dispozici na **stavu** řídicího panelu v části **výstrahy** oddílu. Když vyberete buď celkový počet výstrah nebo číslo odpovídající úroveň závažnosti, **výstrahy** se otevře a zobrazí seznam všech výstrah odpovídající výběru.

Například, pokud vyberete řádek odpovídající k **úroveň závažnost 1**, zobrazí se následující zobrazení:

![Příklad všech 1. úrovně závažnosti výstrah](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**Všechny výstrahy** stránky není v oboru zobrazovat pouze výstrahy odpovídající výběru. Také se filtrují podle **typ prostředku** zobrazíte pouze stav výstrahy vyvolané prostředku virtuálního počítače. Tento formát se projeví v seznamu výstrah pod sloupcem **cílový prostředek**, kde se zobrazí virtuální počítač Azure vyvolanou výstrahu. Pokud není v pořádku stav byl splněn.

Upozornění z jiných typů prostředků nebo služeb nejsou určeny mají být zahrnuty v tomto zobrazení. Tyto výstrahy obsahují upozornění protokolů, které jsou založeny na dotazy protokolů nebo upozornění metrik, které by normálně zobrazení z výchozího Azure Monitor [všechny výstrahy](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) stránky.

Toto zobrazení můžete filtrovat výběrem hodnoty v rozevíracích nabídkách v horní části stránky.

|Sloupec |Popis |
|-------|------------|
|Předplatné |Vyberte předplatné služby Azure. Pouze výstrahy ve vybraném předplatném se nastavují v zobrazení. |
|Skupina prostředků |Výběr jedné skupiny prostředků. V zobrazení jsou zahrnuty pouze výstrahy s cíli ve vybrané skupině prostředků. |
|Typ prostředku |Vyberte jeden nebo více typů prostředků. Ve výchozím nastavení pouze výstrahy cíle **virtuálních počítačů** nebude vybrána a zahrnuté v tomto zobrazení. Tento sloupec je k dispozici pouze po zadal skupinu prostředků. |
|Prostředek |Vyberte prostředek. V zobrazení jsou zahrnuty pouze výstrahy s tento prostředek jako cíl. Tento sloupec je k dispozici až poté, co byl zadán typ prostředku. |
|Severity |Vyberte výstrahy závažnosti nebo **všechny** zahrnout všechny závažnosti výstrahy. |
|Stav monitorování |Vyberte podmínku monitorování výstrah filtr, pokud byla aktivována nebo vyřešena systémem, pokud podmínka není už aktivní. Nebo vyberte **všechny** zahrnout všechny podmínky upozornění. |
|Stav upozornění |Vyberte stav výstrahy **nový**, **potvrzení**, **uzavřeno**, nebo **všechny** zahrnout výstrahy všechny stavy. |
|Monitorovat službu |Vyberte službu nebo vyberte **všechny** zahrnout všechny služby. K použití této funkce jsou podporovány pouze výstrahy z virtuálního počítače Insights.|
|Časové rozmezí| Pouze výstrahy vyvolané v rámci vybrané časové období se nastavují v zobrazení. Podporované hodnoty jsou uplynulou hodinu, posledních 24 hodin, posledních 7 dní a posledních 30 dní. |

Když vyberete výstrahu, **podrobností výstrahy** zobrazí se stránka. Tato stránka obsahuje podrobnosti o výstraze a umožňuje změnit její stav.

Další informace o správě výstrah naleznete v tématu [vytvoření, zobrazení a Správa výstrah pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Vytváření nových výstrah na základě kritérií stavu nebo změny stávajících stavu pravidla výstrah ve službě Azure Monitor z portálu není aktuálně podporováno.


![Panelu podrobností výstrahy pro vybrané výstrahy](./media/vminsights-health/alert-details-pane-01.png)

Upozornění stavu pro jedno nebo více upozornění můžete změnit výběrem a následným výběrem **změnit stav** z **všechny výstrahy** stránky v levém horním rohu. Vyberte jednu z stavů na **změnit stav upozornění** podokně Přidat popis změnu v hodnotě **komentář** pole a pak vyberte **Ok** potvrďte provedené změny. Při ověřování informací a změny se použijí, sledovat průběh **oznámení** v nabídce.

### <a name="configure-alerts"></a>Konfigurace upozornění
Nelze spravovat určitých úloh správy upozornění z portálu Azure portal. Tyto úlohy je nutné provádět pomocí [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Zejména:

- Povolení nebo zakázání výstrahu týkající se stavu kritéria
- Nastavení oznámení pro výstrahy týkající se stavu kritéria

Každý příklad používá [ARMClient](https://github.com/projectkudu/armclient) na svém počítači s Windows. Pokud nejste obeznámeni s touto metodou, přečtěte si téma [pomocí ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Povolení nebo zakázání pravidla upozornění

K povolení nebo zakázání výstrahy pro konkrétní kritéria, vlastnost **alertGeneration** musí být upraveny s hodnotou buď **zakázané** nebo **povoleno**.

K identifikaci *elementu monitorId* pro konkrétní kritéria, následující příklad ukazuje, jak se dotázat na tuto hodnotu pro kritéria **LogicalDisk\Avg disku sekund za přenos**:

1. V okně terminálu zadejte **armclient.exe přihlášení**. To vás vyzve k přihlášení do Azure.

2. Zadejte následující příkaz k načtení všech kritérium stavu aktivní na konkrétní virtuální počítač a určit hodnotu pro *elementu monitorId* vlastnost:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Následující příklad ukazuje výstup *armclient GET* příkazu. Poznamenejte si hodnotu *elementu MonitorId*. Tato hodnota je povinná na další krok, kde jsme musíte zadat ID kritéria stavu a upravit jeho vlastnosti, aby se vytvořila výstraha.

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

3. Zadejte následující příkaz k úpravě *alertGeneration* vlastnost:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Zadejte příkaz GET použili v kroku 2, chcete-li ověřit, že je hodnota vlastnosti nastavena **zakázané**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Kritéria stavu přidružit skupiny akcí

Monitorování stavu virtuálních počítačů Azure podporuje oznámení SMS a e-mailové výstrahy jsou generovány z kritérií stavu není v pořádku. Konfigurace oznámení, poznamenejte si název skupiny akcí nakonfigurované k odeslání serveru SMS nebo e-mailové oznámení.

>[!NOTE]
>Tuto akci musíte provést pro každý monitorovaných virtuálních počítačů, která chcete dostávat oznámení. Nevztahuje se na všechny virtuální počítače ve skupině prostředků.

1. V okně terminálu zadejte *armclient.exe přihlášení*. To vás vyzve k přihlášení do Azure.

2. Zadejte následující příkaz k přidružení skupiny akcí pravidel upozornění:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Pro ověření, že hodnota vlastnosti **actionGroupResourceIds** byl úspěšně aktualizován, zadejte následující příkaz:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Výstup by měl vypadat jako následující kritéria:
    
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

- K identifikaci omezení a celkový výkon virtuálního počítače, naleznete v tématu [virtuálního počítače Azure zobrazení výkonu](vminsights-performance.md).
- Další informace o závislosti zjištěných aplikací najdete v tématu [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).
