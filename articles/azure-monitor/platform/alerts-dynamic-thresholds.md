---
title: Vytváření výstrah s dynamickými prahovými hodnotami v Azure Monitor
description: Vytváření výstrah s dynamickými Prahy založenými na strojovém učení
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 92fafc9357ca08cd31c9ba7badbf7f6433f44f86
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862584"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Výstrahy metrik s dynamickými prahovými hodnotami v Azure Monitor

Výstraha metriky s detekcí dynamických prahových hodnot využívá pokročilé funkce Machine Learning (ML) k získání historických chování metriky, identifikaci vzorů a anomálií, které indikují možné problémy se službami. Poskytuje podporu jednoduchého uživatelského rozhraní i operací ve velkém měřítku tím, že umožňuje uživatelům konfigurovat pravidla upozornění prostřednictvím rozhraní API pro Azure Resource Manager, a to plně automatizovaným způsobem.

Po vytvoření pravidla výstrahy se aktivuje jenom v případě, že se monitorovaná metrika nechová podle očekávání, a to na základě jejich upravených prahových hodnot.

Rádi bychom slyšeli vaši zpětnou vazbu, podíváme se na <azurealertsfeedback@microsoft.com> .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Proč a kdy se doporučuje použít typ dynamické podmínky?

1. **Škálovatelné upozorňování** – pravidla dynamické výstrahy prahové hodnoty můžou vytvořit přizpůsobené prahové hodnoty pro stovky řady metrik najednou a zároveň zajistit stejné snadné definování pravidla výstrahy pro jednu metriku. Poskytují méně výstrahy pro vytváření a správu. K jejich vytvoření můžete použít buď Azure Portal, nebo rozhraní API Azure Resource Manager. Škálovatelný přístup je obzvlášť užitečný při práci s dimenzemi metriky nebo při použití na více prostředků, jako jsou například všechny prostředky předplatného.  [Přečtěte si další informace o tom, jak nakonfigurovat výstrahy metrik s dynamickými prahovými hodnotami pomocí šablon](alerts-metric-create-templates.md).

1. **Rozpoznávání vzorců inteligentní metriky** – s využitím naší technologie v ml jsme schopni automaticky detekovat modely metrik a přizpůsobovat metriky v průběhu času, což může často zahrnovat sezónnost (hourly/denně/týdně). Přizpůsobování chování metriky v průběhu času a upozorňování na základě odchylek od jejího vzoru zbavuje zatížení, které se u každé metriky navzájemují jako "pravé" prahové hodnoty. Algoritmus ML použitý v dynamických mezních hodnotách je navržený tak, aby nedocházelo k mezním hodnotám s vysokou přesností (nízká přesnost) nebo šířce (nízké), které nemají očekávaný vzor.

1. **Intuitivní konfigurace** – dynamické prahové hodnoty umožňují nastavit výstrahy metrik pomocí konceptů vysoké úrovně a zmírnit tak nutnost rozsáhlých znalostí v doméně o této metrikě.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Jak nakonfigurovat pravidla výstrah s dynamickými prahovými hodnotami?

Výstrahy s dynamickými mezními hodnotami lze nakonfigurovat prostřednictvím výstrah metrik v Azure Monitor. [Přečtěte si další informace o tom, jak nakonfigurovat upozornění na metriky](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Jak se počítají prahové hodnoty?

Dynamické prahové hodnoty průběžně učí data řady metrik a snaží se je modelovat pomocí sady algoritmů a metod. Detekuje vzory v datech, jako je například sezónnost (za hodinu/den/týden), a je schopna zpracovávat metriky odolné proti chybám (například procesor počítače nebo paměť) a metriky s nízkou mírou rozptýlení (například dostupnost a míra chyb).

Mezní hodnoty jsou vybrány takovým způsobem, že odchylka od těchto prahových hodnot označuje anomálii v chování metriky.

> [!NOTE]
> Dynamické prahové hodnoty mohou detekovat sezónnost pro hodinové, denní nebo týdenní vzory. Jiné vzorce, jako je například obousměrné nebo týdenní sezónnost, nemusí být zjištěny. K detekci týdenních sezónnost se vyžadují aspoň tři týdny historických dat. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Co znamená nastavení citlivosti v případě dynamických prahových hodnot?

Citlivost na prahové hodnoty výstrahy je koncept vysoké úrovně, který řídí míru odchylky od chování metriky potřebné k aktivaci výstrahy.
Tato možnost nevyžaduje znalosti v doméně týkající se metriky, jako je statická prahová hodnota. Dostupné jsou následující možnosti:

- Vysoká – prahové hodnoty budou pevně a blízko vzorce řady metrik. Pravidlo výstrahy se aktivuje při nejmenší odchylce. Výsledkem je více výstrah.
- Střední – méně těsné a více vyrovnaných prahových hodnot, méně výstrah než s vysokou citlivostí (výchozí).
- Nízká – prahové hodnoty budou volné s větší vzdáleností od vzorce řady metrik. Pravidlo výstrahy se aktivuje jenom u velkých odchylek, což vede k menšímu počtu výstrah.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Jaké jsou možnosti nastavení "operator" v dynamických prahových hodnotách?

Pravidlo upozornění na dynamické prahové hodnoty může vytvořit přizpůsobené prahové hodnoty na základě chování metriky u horních i dolních mezí pomocí stejného pravidla výstrahy.
Můžete zvolit výstrahu, která se má aktivovat, na jednu z následujících tří podmínek:

- Větší než horní prahová hodnota nebo nižší než dolní prahová hodnota (výchozí)
- Větší než horní prahová hodnota
- Nižší než dolní prahová hodnota.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Co znamená rozšířené nastavení v případě dynamických prahových hodnot?

**Neúspěšné tečky** – dynamické prahové hodnoty také umožňují nakonfigurovat "počet porušení pro aktivaci výstrahy", minimální počet odchylek vyžadovaných v určitém časovém období pro systém k vyvolání výstrahy (výchozí časový interval je čtyři odchylky za 20 minut). Uživatel může nakonfigurovat neúspěšné intervaly a zvolit, na co se má upozornit, změnou období selhání a časového okna. Tato možnost omezuje hluk výstrah vygenerovaný přechodnými špičkami. Například:

Chcete-li aktivovat upozornění, když je problém v průběhu 20 minut v intervalu po dobu 4 po sobě jdoucích časů v daném období seskupení 5 minut, použijte následující nastavení:

![Neúspěšná nastavení dob pro nepřetržitý problém po dobu 20 minut, 4 po sobě jdoucí časy v daném období seskupení 5 minut](media/alerts-dynamic-thresholds/0008.png)

Chcete-li aktivovat výstrahu v případě, že došlo k narušení za dynamické prahové hodnoty za 20 minut z posledních 30 minut a období 5 minut, použijte následující nastavení:

![Neúspěšná nastavení dob pro problém po dobu 20 minut z posledních 30 minut a seskupování období 5 minut](media/alerts-dynamic-thresholds/0009.png)

**Ignorovat data před** – uživatelé můžou volitelně definovat datum zahájení, ze kterého by měl systém počítat prahové hodnoty. Typický případ použití může nastat, pokud byl prostředek spuštěn v testovacím režimu a je nyní povýšen na provozní zatížení, a proto by mělo být chování jakékoli metriky v průběhu testovací fáze ignorováno.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Jak zjistím, proč se aktivovala výstraha dynamické prahové hodnoty?

Kliknutím na odkaz v e-mailu nebo textové zprávě nebo v prohlížeči zobrazíte zobrazení výstrah v Azure Portal, můžete prozkoumat spuštěné instance výstrah v zobrazení výstrah. [Přečtěte si další informace o zobrazení výstrah](alerts-overview.md#alerts-experience).

Zobrazení výstrah zobrazuje:

- Všechny podrobnosti metriky v okamžiku, kdy se vyvolala výstraha dynamické prahové hodnoty.
- Graf období, ve kterém byla výstraha aktivována, což zahrnuje dynamické prahové hodnoty použité v daném časovém okamžiku.
- Možnost poskytovat zpětnou vazbu na upozornění na dynamické prahové hodnoty a zobrazení výstrah, která by mohla zlepšit budoucí zjišťování.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Dojde k zpomalení změn chování v metrikě triggeru výstrahy?

Pravděpodobně ne. Dynamické prahové hodnoty jsou vhodné pro zjištění významných odchylek místo pomalého vyvíjejících se problémů.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Kolik dat se používá pro náhled a výpočet prahových hodnot?

Při prvním vytvoření pravidla výstrahy se prahové hodnoty uvedené v grafu vypočítávají na základě množství historických dat za účelem výpočtu hodinových nebo denních sezónních vzorů (10 dní). Po vytvoření pravidla výstrahy budou dynamické prahové hodnoty používat všechna potřebná historická data, která jsou k dispozici a budou se průběžně učit a přizpůsobovat na základě nových dat, aby byly prahové hodnoty přesnější. To znamená, že po tomto výpočtu se v grafu zobrazí také týdenní vzory.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Kolik dat je potřeba k aktivaci výstrahy?

Pokud máte nový prostředek nebo chybí data metriky, nebudou dynamické prahové hodnoty aktivovat výstrahy před třemi dny a k dispozici jsou alespoň 30 vzorků dat metriky, aby bylo zajištěno přesné prahové hodnoty.
U existujících prostředků s dostatečnými daty metriky můžou dynamické prahové hodnoty aktivovat výstrahy okamžitě.

## <a name="dynamic-thresholds-best-practices"></a>Osvědčené postupy pro dynamické prahové hodnoty

Dynamické prahové hodnoty lze použít pro většinu platforem a vlastní metriky v nástroji Azure Monitor a byly také vyladěny pro běžné metriky aplikací a infrastruktury.
Následující položky jsou osvědčené postupy pro konfiguraci výstrah na některých těchto metrikách pomocí dynamických prahových hodnot.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamické prahové hodnoty pro procentuální metriky procesoru virtuálního počítače

1. V [Azure Portal](https://portal.azure.com)klikněte na **monitorování**. Zobrazení monitorování slučuje všechna nastavení monitorování a data v jednom zobrazení.

2. Klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.

    > [!TIP]
    > Většina oken prostředků má také **výstrahy** v nabídce prostředků v části **monitorování**, můžete také vytvořit výstrahy.

3. Klikněte na **vybrat cíl** a v kontextovém podokně, které se načte, vyberte cílový prostředek, na kterém chcete upozornit. K vyhledání prostředku, který chcete monitorovat, použijte rozevírací seznam pro **předplatné** a **typ prostředku Virtual Machines** . K vyhledání prostředku můžete použít také panel hledání.

4. Po výběru cílového prostředku klikněte na **Přidat podmínku**.

5. Vyberte **procento využití procesoru**.

6. Volitelně můžete metriku Upřesnit úpravou **period** a **agregace**. Pro tento typ metriky se nedoporučuje používat typ agregace "maximum", protože je méně typický pro chování. Pro možnost maximální prahová hodnota statického typu agregace možná bude vhodnější.

7. Zobrazí se graf metriky za posledních 6 hodin. Zadejte parametry výstrahy:
    1. **Typ podmínky** – vyberte možnost ' Dynamic '.
    1. **Citlivost** – pro snížení šumu výstrah vyberte možnost střední/nízká citlivost.
    1. – Pokud chování nepředstavuje použití aplikace, vyberte **operátor** větší než.
    1. **Frekvence** – zvažte snížení úrovně na základě obchodního dopadu výstrahy.
    1. **Neúspěšná období** (Pokročilá možnost) – okno Hledat zpátky by mělo mít aspoň 15 minut. Pokud je například období nastaveno na pět minut, pak by neúspěšné období měla být alespoň tři nebo více.

8. V grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat.

9. Klikněte na **Hotovo**.

10. Vyplňte **Podrobnosti výstrahy** , jako **je název pravidla výstrahy**, **Popis** a **závažnost**.

11. Přidejte skupinu akcí k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.

12. Kliknutím na **Hotovo** uložte pravidlo upozornění na metriku.

> [!NOTE]
> Pravidla upozornění na metriky vytvořená prostřednictvím portálu se vytvoří ve stejné skupině prostředků jako cílový prostředek.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamické prahové hodnoty pro Application Insights doba provádění požadavku HTTP

1. V [Azure Portal](https://portal.azure.com)klikněte na **monitorování**. Zobrazení monitorování slučuje všechna nastavení monitorování a data v jednom zobrazení.

2. Klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.

    > [!TIP]
    > Většina oken prostředků má také **výstrahy** v nabídce prostředků v části **monitorování**, můžete také vytvořit výstrahy.

3. Klikněte na **vybrat cíl** a v kontextovém podokně, které se načte, vyberte cílový prostředek, na kterém chcete upozornit. K vyhledání prostředku, který chcete monitorovat, použijte rozevírací seznam pro **předplatné** a **typ prostředku Application Insights** . K vyhledání prostředku můžete použít také panel hledání.

4. Po výběru cílového prostředku klikněte na **Přidat podmínku**.

5. Vyberte **hodnotu doba provádění požadavku HTTP**.

6. Volitelně můžete metriku Upřesnit úpravou **period** a **agregace**. Pro tento typ metriky se nedoporučuje používat typ agregace "maximum", protože je méně typický pro chování. Pro možnost maximální prahová hodnota statického typu agregace možná bude vhodnější.

7. Zobrazí se graf metriky za posledních 6 hodin. Zadejte parametry výstrahy:
    1. **Typ podmínky** – vyberte možnost ' Dynamic '.
    1. – Pokud chcete omezit výstrahy vyvolané na zlepšení doby trvání, vyberte **operátor** větší než.
    1. **Frekvence** – zvažte snížení úrovně na základě obchodního dopadu výstrahy.

8. V grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat.

9. Klikněte na **Hotovo**.

10. Vyplňte **Podrobnosti výstrahy** , jako **je název pravidla výstrahy**, **Popis** a **závažnost**.

11. Přidejte skupinu akcí k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.

12. Kliknutím na **Hotovo** uložte pravidlo upozornění na metriku.

> [!NOTE]
> Pravidla upozornění na metriky vytvořená prostřednictvím portálu se vytvoří ve stejné skupině prostředků jako cílový prostředek.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretace dynamických prahových grafů

Následuje graf metrika, jejíž dynamická prahová mezní hodnota a některé výstrahy jsou vyvolány, pokud byla hodnota mimo povolený počet prahových hodnot.

![Další informace o tom, jak nakonfigurovat výstrahy metriky](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

K interpretaci předchozího grafu použijte následující informace.

- **Modrá čára** – skutečná měřená metrika v průběhu času.
- **Modrá šedá oblast** – zobrazuje povolený rozsah pro danou metriku. Dokud hodnoty metriky zůstanou v tomto rozsahu, nebude k dispozici žádná výstraha.
- **Modré tečky** : Pokud jste ponechali kliknout na část grafu a pak najedete myší na modrou čáru, zobrazí se pod kurzorem modrá tečka ukazující jednotlivé agregované hodnoty metriky.
- **Automaticky otevíraná okna s modrou tečkou** – zobrazuje měřenou hodnotu metriky (modrou tečkou) a horní a dolní hodnoty povoleného rozsahu.  
- **Červená tečka s černým kruhem** – zobrazí první hodnotu metriky mimo povolený rozsah. Jedná se o hodnotu, která aktivuje výstrahu metriky a umístí ji do aktivního stavu.
- **Červené tečky**– označují dodatečné měřené hodnoty mimo povolený rozsah. Nebudou aktivovat další výstrahy metrik, ale výstraha zůstane aktivní.
- **Červená oblast** – zobrazuje čas, kdy byla hodnota metriky mimo povolený rozsah. Výstraha zůstane v aktivním stavu, pokud jsou následující měřené hodnoty mimo povolený rozsah, ale neaktivují se žádné nové výstrahy.
- **Konec červené oblasti** – když se modrá čára vrátí do povolených hodnot, červená oblast se zastaví a osa měřené hodnoty se změní na modrou. Stav aktivované výstrahy metriky v okamžiku červené tečky s černým obrysem je nastaven na hodnotu Vyřešeno. 
