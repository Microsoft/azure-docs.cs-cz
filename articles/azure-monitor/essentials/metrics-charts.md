---
title: Rozšířené funkce Průzkumníka metrik Azure
description: Přečtěte si o rozšířených použitích Průzkumníka metrik Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.openlocfilehash: 444185dc41532bfa55c271e2f0027eb8b71de42f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959893"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Rozšířené funkce Průzkumníka metrik Azure

> [!NOTE]
> V tomto článku se předpokládá, že jste obeznámeni se základními funkcemi funkce Průzkumník metrik Azure v Azure Monitor. Pokud jste nový uživatel a chcete zjistit, jak vytvořit první graf metriky, přečtěte si téma [Začínáme s Průzkumníkem metrik](./metrics-getting-started.md).

V Azure Monitor [metriky](data-platform-metrics.md) představují řadu měřených hodnot a počty, které se shromažďují a ukládají v průběhu času. Metriky můžou být standardní (označované taky jako platforma) nebo vlastní. 

Standardní metriky poskytuje platforma Azure. Odrážejí údaje o stavu a využití vašich prostředků Azure. 

## <a name="resource-scope-picker"></a>Výběr oboru prostředků
Nástroj pro výběr oboru prostředků umožňuje zobrazit metriky napříč jednotlivými prostředky a více prostředky. V následujících částech se dozvíte, jak používat nástroj pro výběr oboru prostředků. 

### <a name="select-a-single-resource"></a>Vyberte jeden prostředek.
V nabídce **Azure monitor** vyberte **metriky** nebo v části **monitorování** v nabídce prostředku. Pak zvolte **Vybrat obor** a otevřete tak výběr oboru. 

Pomocí nástroje pro výběr oboru můžete vybrat prostředky, jejichž metriky chcete zobrazit. Pokud jste v nabídce prostředku otevřeli Průzkumníka metrik Azure, musí se rozsah naplnit. 

![Snímek obrazovky znázorňující otevření výběru oboru prostředků](./media/metrics-charts/scope-picker.png)

U některých prostředků můžete v jednu chvíli zobrazit jenom metriky prostředků. V nabídce **typy prostředků** jsou tyto prostředky v části **všechny typy prostředků** .

![Snímek obrazovky znázorňující jeden prostředek.](./media/metrics-charts/single-resource-scope.png)

Po výběru prostředku se zobrazí všechna předplatná a skupiny prostředků, které tento prostředek obsahují.

![Snímek obrazovky zobrazující dostupné prostředky](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Pokud chcete, aby možnost zobrazila metriky pro více prostředků najednou, nebo pokud chcete zobrazit metriky v rámci předplatného nebo skupiny prostředků **, vyberte možnost** odkaz.

Až budete s výběrem spokojeni, vyberte **použít**.

### <a name="view-metrics-across-multiple-resources"></a>Zobrazení metrik v různých prostředcích
Některé typy prostředků se mohou dotazovat na metriky v několika prostředcích. Prostředky musí být v rámci stejného předplatného a umístění. Tyto typy prostředků najdete v horní části nabídky **typy prostředků** . 

Další informace najdete v tématu [Výběr více prostředků](./metrics-dynamic-scope.md#select-multiple-resources).

![Snímek obrazovky znázorňující typy různých prostředků](./media/metrics-charts/multi-resource-scope.png)

Pro typy, které jsou kompatibilní s více prostředky, můžete zadávat dotazy na metriky v rámci předplatného nebo několika skupin prostředků. Další informace najdete v tématu [Výběr skupiny prostředků nebo předplatného](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Více čar a grafů metriky

V Průzkumníkovi metrik Azure můžete vytvářet grafy, které vykreslují více řádků metrik nebo zobrazují více grafů metrik současně. Tato funkce umožňuje:

- Korelujte související metriky ve stejném grafu, abyste viděli, jak se jedna hodnota vztahuje na jinou.
- Zobrazit metriky, které používají různé měrné jednotky v těsné blízkosti.
- Vizuálně agregovat a porovnejte metriky z více zdrojů.

Představte si například, že máte pět účtů úložiště a chcete zjistit, kolik místa spotřebovávají dohromady. Můžete vytvořit (skládaný) plošný graf, který zobrazuje jednotlivé hodnoty a součet všech hodnot v určitých bodech v čase.

### <a name="multiple-metrics-on-the-same-chart"></a>Víc metrik na stejném grafu

Chcete-li zobrazit více metrik ve stejném grafu, [vytvořte nejprve nový graf](./metrics-getting-started.md#create-your-first-metric-chart). Pak vyberte **Přidat metriku**. Opakováním tohoto kroku přidejte další metriky do stejného grafu.

> [!NOTE]
> Grafy by obvykle neměly kombinovat metriky, které používají různé měrné jednotky. Nepoužívejte třeba kombinování jedné metriky, která používá milisekundy s jinou, která používá kilobajty. Také se vyhněte smíchání metrik, jejichž měřítko se výrazně liší. 
>
> V těchto případech zvažte místo toho použití více grafů. V Průzkumníku metrik vyberte **přidat graf** pro vytvoření nového grafu.

### <a name="multiple-charts"></a>Více grafů

Pokud chcete vytvořit jiný graf, který používá jinou metriku, vyberte **přidat graf**.

Chcete-li změnit pořadí nebo odstranění více grafů, klikněte na tlačítko se třemi tečkami (**...**) a otevřete nabídku grafu. Pak zvolte **Přesunout nahoru**, **Přesunout dolů** nebo **Odstranit**.

## <a name="time-range-controls"></a>Ovládací prvky časového rozsahu

Kromě změny časového rozsahu pomocí [panelu pro výběr času](metrics-getting-started.md#select-a-time-range)můžete také posouvání a přiblížení pomocí ovládacích prvků v oblasti grafu.
### <a name="pan"></a>Posouvání

Chcete-li se posunout, klikněte na šipky vlevo a vpravo na okraji grafu.  Tím se vybraný časový rozsah posune zpátky a vpřed po jedné polovině časového rozsahu grafu.  Pokud například prohlížíte za posledních 24 hodin, kliknutí na šipku vlevo způsobí, že se časový rozsah posune do rozsahu určitého dne a před polovinu až 12 hodin.

Většina metrik podporuje 93 dnů uchovávání, ale umožňuje vám jenom zobrazit 30 dnů najednou.  Pomocí ovládacích prvků posunu se můžete podívat v posledních 30 dnech a potom snadno přejít na více než 15 dnů a zobrazit zbývající dobu uchování.

![Animovaný obrázek GIF znázorňující ovládací prvky posouvání vlevo a vpravo](./media/metrics-charts/metrics-pan-controls.gif)

### <a name="zoom"></a>Zoom

Kliknutím a přetažením grafu můžete zvětšit část grafu.  Přiblížením dojde k aktualizaci časového rozsahu grafu pro výběr a výběr menšího časového intervalu, pokud je časový interval nastaven na hodnotu automaticky.  Nový časový rozsah bude platit pro všechny grafy v metrikách.

![Animovaný obrázek GIF znázorňující funkci přiblížení metrik](./media/metrics-charts/metrics-zoom-control.gif)

## <a name="aggregation"></a>Agregace

Když přidáte metriku do grafu, Průzkumník metrik automaticky použije výchozí agregaci. Ve výchozím nastavení je to v základních scénářích smysl. K získání dalších přehledů o této metrikě ale můžete použít jinou agregaci. 

Před použitím různých agregací v grafu byste měli pochopit, jak je Průzkumník metrik zpracovává. Metriky představují řadu měření (neboli hodnoty metrik), které jsou zachyceny v časovém období. Při vykreslení grafu se hodnoty vybrané metriky samostatně agreguje v *časovém intervalu*. 

Velikost časového intervalu můžete vybrat pomocí [panelu pro výběr času](./metrics-getting-started.md#select-a-time-range)v Průzkumníkovi metrik. Pokud jste nevybrali časový interval explicitně, použije se ve výchozím nastavení aktuálně vybraný časový rozsah. Po určení časového intervalu se hodnoty metrik, které byly zachyceny během každé časové intervaly, sčítají v grafu, jeden datový bod za časový interval.

Předpokládejme například, že se v grafu zobrazuje metrika *doby odezvy serveru* . Používá *průměrnou* agregaci v časovém intervalu za *posledních 24 hodin*. V tomto příkladu:

- Pokud je časová členitost nastavená na 30 minut, graf se vykreslí z 48 agregovaných datových bodů. To znamená, že spojnicový graf spojí 48 teček v oblasti vykreslení grafu (24 hodin × 2 datové body za hodinu). Každý datový bod představuje *průměr* všech zaznamenaných dob odezvy pro žádosti serveru, ke kterým došlo během každé z příslušných 30 minut časových období.
- Pokud časové rozlišení přepnete na 15 minut, dostanete 96 agregovaných datových bodů.  To znamená, že získáte 24 hodin × 4 datových bodů za hodinu.

Průzkumník metrik má pět základních statistických typů agregace: Sum, Count, min, Max a Average. Agregace *Sum* se někdy označuje jako *Celková* agregace. V případě mnoha metrik Průzkumník metrik skryje agregace, které nejsou relevantní a nelze je použít.

* **Sum**: součet všech hodnot zachycených během intervalu agregace.

    ![Snímek obrazovky žádosti o součet](./media/metrics-charts/request-sum.png)

* **Count**(počet): počet měření zachycených během intervalu agregace. 
    
    Pokud je metrika vždy zachycena s hodnotou 1, agregace Count je rovna agregaci součtu. Tento scénář je běžný, pokud metrika sleduje počet jedinečných událostí a každé měření představuje jednu událost. Kód vygeneruje záznam metriky pokaždé, když přijde nový požadavek.

    ![Snímek obrazovky s požadavkem na počet.](./media/metrics-charts/request-count.png)

* **Average**: průměr hodnot metriky zachycených během intervalu agregace.

    ![Snímek obrazovky s průměrným požadavkem.](./media/metrics-charts/request-avg.png)

* **Min**: nejmenší hodnota zachycená během intervalu agregace.

    ![Snímek obrazovky s minimální žádostí](./media/metrics-charts/request-min.png)

* **Max**: největší hodnota zachycená během intervalu agregace.

    ![Snímek obrazovky s maximálním požadavkem](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filtry

Můžete použít filtry na grafy, jejichž metriky mají rozměry. Představte si například metriku "počet transakcí", která má dimenzi "typ odpovědi". Tato dimenze označuje, zda byla odpověď z transakcí úspěšná nebo neúspěšná. Pokud filtrujete tuto dimenzi, zobrazí se řádek grafu jenom úspěšných (nebo jenom neúspěšných) transakcí. 

### <a name="add-a-filter"></a>Přidání filtru

1. Nad grafem vyberte **Přidat filtr**.

2. Vyberte dimenzi (vlastnost), která se má filtrovat.

   ![Snímek obrazovky zobrazující dimenze (vlastnosti), které můžete filtrovat.](./media/metrics-charts/028.png)

3. Vyberte operátor, který chcete použít pro dimenzi (vlastnost). Výchozí operátor je = (je rovno).

   ![Snímek obrazovky, který zobrazuje operátor, který můžete použít spolu s filtrem.](./media/metrics-charts/filter-operator.png)

4. Vyberte, které hodnoty dimenze chcete použít pro filtr při vykreslování grafu (Tento příklad ukazuje odfiltrování úspěšných transakcí úložiště):

   ![Snímek obrazovky zobrazující úspěšné filtrované transakce úložiště](./media/metrics-charts/029.png)

5. Po výběru hodnot filtru klikněte na pryč v selektoru filtru a zavřete ho. Graf teď zobrazuje, kolik transakcí úložiště selhalo:

   ![Snímek obrazovky, který ukazuje, kolik transakcí úložiště selhalo.](./media/metrics-charts/030.png)

6. Opakováním kroků 1-5 můžete použít více filtrů pro stejné grafy.


## <a name="metric-splitting"></a>Rozdělení metriky

Metriku můžete rozdělit podle dimenzí, abyste vizualizují, jak různé segmenty metriky jsou srovnávány. Rozdělení vám také může přispět k identifikaci neležících segmentů dimenze.

### <a name="apply-splitting"></a>Použít rozdělení

1. Nad grafem vyberte **použít rozdělení**.
 
   > [!NOTE]
   > Grafy, které mají více metrik, nemůžou používat rozdělení funkcí. I když graf může mít více filtrů, může mít pouze jednu rozdělenou dimenzi.

2. Vyberte dimenzi, na které se má graf rozdělit:

   ![Snímek obrazovky zobrazující vybranou dimenzi, na které se má graf rozdělit](./media/metrics-charts/031.png)

   Graf nyní zobrazuje více řádků, jeden pro každý segment dimenze:

   ![Snímek obrazovky, který zobrazuje více řádků, jeden pro každý segment dimenze.](./media/metrics-charts/segment-dimension.png)
   
3. Vyberte limit počtu hodnot, který se má zobrazit po rozdělení podle vybrané dimenze. Výchozí limit je 10, jak je znázorněno v předchozím grafu. Rozsah limitu je 1-50.
   
   ![Snímek obrazovky zobrazující limit rozdělení, který omezuje počet hodnot po rozdělení.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Vyberte pořadí řazení pro segmenty: vzestupně nebo sestupně. Výchozí výběr je sestupně.
   
   ![Snímek obrazovky zobrazující pořadí řazení na rozdělených hodnotách](./media/metrics-charts/segment-dimension-sort.png)

5. Kliknutím na tlačítko mimo **Výběr seskupení** jej zavřete.
   

   > [!NOTE]
   > Chcete-li skrýt segmenty, které jsou nepodstatné pro váš scénář, a usnadnit čtení grafů, použijte filtrování i rozdělení na stejnou dimenzi.

## <a name="locking-the-range-of-the-y-axis"></a>Uzamykání rozsahu osy y

Uzamčení rozsahu osy hodnot (y) je důležité v grafech, které zobrazují malé kolísání velkých hodnot. 

Například pokles objemu úspěšných požadavků od 99,99% do 99,5% může představovat významné snížení kvality služby. Ale pokud použijete výchozí nastavení grafu, ale všímáte malými výkyvy numerických hodnot, je obtížné nebo ještě nemožné. V takovém případě můžete uzamknout nejnižší hranici grafu až 99 procent, aby bylo malé vyřazení přesnější. 

Dalším příkladem je kolísání dostupné paměti. V tomto scénáři nebude hodnota technicky nikdy přístupná 0. Oprava rozsahu na vyšší hodnotu by mohla být v dostupném případě přehlednější. 

Chcete-li řídit rozsah osy y, otevřete nabídku grafu (**...**). Pak vyberte **nastavení grafu** pro přístup k pokročilým nastavením grafu.

![Snímek obrazovky, který zvýrazní výběr nastavení grafu](./media/metrics-charts/033.png)

Upravte hodnoty v části **Rozsah osy Y** nebo vyberte **auto** a vraťte se k výchozím hodnotám.
 
 ![Snímek obrazovky, který zvýrazní část rozsahu osy Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Pokud potřebujete zamknout hranice osy y pro grafy, které sledují počty nebo součty v určitém časovém období (pomocí agregací COUNT, Sum, min nebo max), měli byste obvykle zadat členitou časovou osu. V takovém případě byste neměli spoléhat na automatické výchozí hodnoty. 
>
> Zvolíte pevnou časovou členitost, protože hodnoty grafu se změní, když se automaticky upraví časové rozlišení, jakmile uživatel změní velikost okna prohlížeče nebo změní rozlišení obrazovky. Výsledná změna v časové členitosti má vliv na vzhled grafu, který neověřuje aktuální výběr rozsahu osy y.

## <a name="line-colors"></a>Barvy čáry

Po nakonfigurování grafů jsou čáry grafu automaticky přiřazeny barevně z výchozí palety. Tyto barvy můžete změnit.

Chcete-li změnit barvu čáry grafu, vyberte v legendě barevný pruh, který odpovídá grafu. Otevře se dialogové okno pro výběr barvy. Pro konfiguraci barvy čáry použijte výběr barvy.

![Snímek obrazovky, který ukazuje, jak změnit barvu.](./media/metrics-charts/035.png)

Vaše přizpůsobené barvy se zachovají při připnutí grafu na řídicí panel. V následující části se dozvíte, jak připnout graf.

## <a name="pinning-to-dashboards"></a>Připnutí k řídicím panelům 

Po nakonfigurování grafu je můžete chtít přidat na řídicí panel. Když připnete graf na řídicí panel, můžete ho zpřístupnit vašemu týmu. Přehledy můžete získat také zobrazením v kontextu jiné telemetrie monitorování.

Pokud chcete napnout nakonfigurovaný graf na řídicí panel, vyberte v pravém horním rohu grafu možnost **Připnout na řídicí panel**.

![Snímek obrazovky znázorňující, jak připnout graf na řídicí panel](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Pravidla upozornění

Pomocí kritérií vizualizace můžete vytvořit pravidlo upozornění založené na metrikách. Nové pravidlo upozornění bude zahrnovat cílové prostředky, metriky, rozdělení a filtrování dimenzí vašeho grafu. Tato nastavení můžete změnit pomocí podokna pro vytvoření pravidla výstrahy.

Začněte tím, že vyberete **nové pravidlo výstrahy**.

![Snímek obrazovky zobrazující tlačítko nového pravidla výstrahy zvýrazněné červeně](./media/metrics-charts/042.png)

Otevře se podokno vytvoření pravidla výstrahy. V podokně vidíte rozměry metriky grafu. Pole v podokně jsou předem vyplněna, aby vám pomohly upravit pravidlo.

![Snímek obrazovky znázorňující podokno pro vytvoření pravidla](./media/metrics-charts/041.png)

Další informace najdete v tématu [Vytvoření, zobrazení a Správa upozornění na metriky](../alerts/alerts-metric.md).

## <a name="correlate-metrics-to-logs"></a>Korelace metrik s protokoly
Abychom zákazníkům pomohla diagnostikovat hlavní příčinu anomálií v grafu metriky, vytvořili jsme podrobné informace o protokolech. V protokolech umožňujících zákazníkům korelovat špičky v grafu metriky k protokolům a dotazům. 

Předtím, než jsme se podrobněi do prostředí, chceme nejprve zavést různé typy protokolů a dotazů. 

| Označení             | Definice  | 
|------------------|-------------|
| Protokoly aktivit    | Poskytuje přehled o operacích u každého prostředku Azure v předplatném mimo (rovinu správy) Kromě aktualizací Service Healthch událostí. Pomocí protokolu aktivit můžete určit, kdo a kdy se u prostředků ve vašem předplatném mají dělat operace zápisu (PUT, POST, DELETE). Pro každé předplatné Azure existuje jeden protokol aktivit.  |   
| Diagnostický protokol   | Poskytněte přehled o operacích, které byly provedeny v rámci prostředku Azure (rovina dat), například získání tajného kódu z Key Vault nebo vytvoření žádosti do databáze. Obsah protokolů prostředků se liší podle typu prostředku a služby Azure. **Poznámka:** Musí být poskytované službou a povolené zákazníkem.  | 
| Doporučený protokol | Dotazy založené na scénářích, které zákazník může využít k prozkoumání anomálií v Průzkumníku metrik.  |

V současné době jsou k dispozici podrobné protokoly pro výběr poskytovatelů prostředků. Poskytovatelé prostředků, kteří mají kompletní přehled o protokolování, jsou: 

* Application Insights 
* Automatické škálování 
* App Services  
* Storage  

Níže najdete ukázková prostředí pro Application Insights poskytovatele prostředků.

![Špička selhání v okně metriky App Insights](./media/metrics-charts/drill-into-log-ai.png)

Pokud chcete zjistit, špička v neúspěšných žádostech, klikněte na přejít k podrobnostem o protokolech.

![Snímek obrazovky s rozevíracím seznamem pro přechod k protokolům](./media/metrics-charts/drill-into-logs-dropdown.png)

Kliknutím na možnost selhání přejdete k vlastnímu oknu selhání, které vám poskytne operace nezdařené operace, hlavní typy výjimek a závislosti. 

![Snímek obrazovky s oknem selhání App Insights](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Běžné problémy s podrobnostmi v protokolech

* Protokolování a dotazy jsou zakázané – Chcete-li zobrazit doporučené protokoly a dotazy, je nutné směrovat diagnostické protokoly na Log Analytics. Přečtěte si [Tento dokument](./diagnostic-settings.md) , kde se dozvíte, jak to udělat. 
* Jsou k dispozici pouze protokoly aktivit – funkce přejít k protokolům je k dispozici pouze pro vybrané poskytovatele prostředků. Ve výchozím nastavení jsou k dispozici protokoly aktivit. 

 
## <a name="troubleshooting"></a>Řešení potíží

Pokud v grafu nevidíte žádná data, přečtěte si následující informace pro řešení potíží:

* Filtry se použijí na všechny grafy v podokně. Když se zaměříte na graf, ujistěte se, že jste nastavili filtr, který vylučuje všechna data v jiném grafu.

* Chcete-li nastavit různé filtry v různých grafech, vytvořte grafy v různých oknech. Pak grafy uložte jako samostatné oblíbené položky. Pokud chcete, můžete grafy připnout na řídicí panel, abyste je mohli vidět dohromady.

* Pokud graf nasegmentujte pomocí vlastnosti, kterou metrika nedefinuje, nezobrazí se v grafu žádný obsah. Zkuste vymazat segmentaci (rozdělení) nebo zvolit jinou vlastnost.

## <a name="next-steps"></a>Další kroky

Další informace o vytváření řídicích panelů s akcemi pomocí metrik najdete v tématu [vytváření vlastních řídicích panelů klíčových ukazatelů výkonu](../app/tutorial-app-dashboards.md).