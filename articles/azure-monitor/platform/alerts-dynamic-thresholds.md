---
title: Vytváření výstrah s dynamickými prahovými hodnotami v Azure Monitoru
description: Vytváření výstrah pomocí dynamických prahových hodnot založených na strojovém učení
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 9345138e948d84e0ea3c804dbd7a4b3c21daca2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668141"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Upozornění metrik s dynamickými prahovými hodnotami v Azure Monitoru

Upozornění na metriky s dynamickými prahovými hodnotami využívá pokročilé strojové učení (ML) k naučení se historického chování metrik, k identifikaci vzorců a anomálií, které označují možné problémy se službami. Poskytuje podporu jednoduchého uživatelského rozhraní i operací ve velkém měřítku tím, že umožňuje uživatelům konfigurovat pravidla výstrah prostřednictvím rozhraní API Azure Resource Manager, a to plně automatizovaným způsobem.

Jakmile je pravidlo výstrahy vytvořeno, bude se spouštět pouze v případě, že se monitorovaná metrika nechová podle očekávání, na základě přizpůsobených prahových hodnot.

Rádi bychom slyšeli vaši zpětnou <azurealertsfeedback@microsoft.com>vazbu, aby to přichází na .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Proč a kdy je doporučeno používat dynamický typ podmínky?

1. **Škálovatelná pravidla** upozornění – dynamická prahová hodnota výstrahy můžete vytvořit přizpůsobené prahové hodnoty pro stovky metrických řad najednou, ale poskytuje stejnou snadnou definování pravidla výstrahy na jednu metriku. Poskytují vám méně výstrah y k vytváření a správě. K jejich vytvoření můžete použít portál Azure nebo rozhraní API Azure Resource Manager. Škálovatelný přístup je zvláště užitečné při práci s dimenze metriky nebo při použití na více prostředků, jako jsou například pro všechny prostředky předplatného.  [Přečtěte si další informace o konfiguraci upozornění na metriky pomocí dynamických prahových hodnot pomocí šablon](alerts-metric-create-templates.md).

1. **Inteligentní rozpoznávání metrických vzorů** – Pomocí naší technologie ML jsme schopni automaticky detekovat metrické vzory a přizpůsobit se změnám metrik v průběhu času, které mohou často zahrnovat sezónnost (hodinová / denní / týdenní). Přizpůsobení se chování metrik v průběhu času a upozorňování na základě odchylek od jejího vzoru zmírňuje zátěž znalosti "správné" prahové hodnoty pro každou metriku. Algoritmus ML používaný v dynamických prahových hodnotách je navržen tak, aby zabránil hlučným (s nízkou přesností) nebo širokým (nízkým navrácením) prahovým hodnotám, které nemají očekávaný vzor.

1. **Intuitivní konfigurace** – dynamické prahové hodnoty umožňuje nastavení upozornění metriky pomocí konceptů vysoké úrovně, zmírnění potřeby mít rozsáhlé znalosti domény o této metriky.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Jak nakonfigurovat pravidla výstrah pomocí dynamických prahových hodnot?

Výstrahy s dynamickými prahovými hodnotami lze konfigurovat prostřednictvím upozornění metrik v Azure Monitoru. [Přečtěte si další informace o konfiguraci upozornění na metriky](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Jak se počítají prahové hodnoty?

Dynamické prahové hodnoty se průběžně učí data z řady metrik a snaží se modelovat pomocí sady algoritmů a metod. Detekuje vzory v datech, jako je sezónnost (Hourly / Daily / Weekly), a je schopen zpracovat hlučné metriky (například procesor nebo paměť počítače), stejně jako metriky s nízkou disperzí (jako je dostupnost a chybovost).

Prahové hodnoty jsou vybrány tak, že odchylka od těchto prahových hodnot označuje anomálii v chování metriky.

> [!NOTE]
> Sezónní detekce vzorů je nastavena na hodinu, den nebo týden interval. To znamená, že jiné vzory, jako je dvouhodinový vzor nebo polotýdenní nemusí být detekovány.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Co znamená nastavení Citlivosti v dynamických prahových hodnotách?

Upozornění prahová citlivost je koncept vysoké úrovně, který řídí množství odchylky od metrické chování potřebné k aktivaci výstrahy.
Tato možnost nevyžaduje znalosti domény o metrice, jako je statická prahová hodnota. Dostupné jsou následující možnosti:

- Vysoká – prahové hodnoty budou těsné a blízké vzoru metrických řad. Pravidlo výstrahy se aktivuje při nejmenší odchylce, což povede k více výstrahám.
- Střední – méně těsné a vyváženější prahové hodnoty, méně výstrah než s vysokou citlivostí (výchozí).
- Nízká – Prahové hodnoty budou volné s větší vzdáleností od vzoru metrických řad. Pravidlo výstrahy se aktivuje pouze u velkých odchylek, což vede k menšímu počtu výstrah.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Jaké jsou možnosti nastavení Operátor v dynamických prahových hodnotách?

Pravidlo upozornění dynamických prahových hodnot můžete vytvořit přizpůsobené prahové hodnoty na základě chování metriky pro horní i dolní hranice pomocí stejné pravidlo výstrahy.
Výstrahu, která se má aktivovat, můžete zvolit za jedné z následujících tří podmínek:

- Větší než horní práh nebo nižší než dolní prahová hodnota (výchozí)
- Větší než horní práh
- Nižší než dolní práh.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Co znamenají upřesňující nastavení v dynamickém prahu?

**Selhání období** - dynamické prahové hodnoty také umožňuje nakonfigurovat "Porušení počtu pro aktivaci výstrahy", minimální počet odchylek požadovaných v rámci určitého časového období pro systém vyvolat výstrahu (výchozí časové okno je čtyři odchylky za 20 minut). Uživatel může nakonfigurovat selhání období a zvolit, co má být upozorněni změnou selhání období a časové okno. Tato schopnost snižuje výstražný šum generovaný přechodovými špičkami. Například:

Chcete-li spustit výstrahu, pokud je problém nepřetržitý po dobu 20 minut, čtyřikrát za sebou v daném období seskupení 5 minut, použijte následující nastavení:

![Selhání nastavení období pro nepřetržitý problém po dobu 20 minut, 4 po sobě jdoucí krát v daném období seskupení 5 minut](media/alerts-dynamic-thresholds/0008.png)

Chcete-li spustit výstrahu, když došlo k porušení dynamických prahových hodnot za 20 minut z posledních 30 minut s dobou 5 minut, použijte následující nastavení:

![Selhání nastavení období pro problém po dobu 20 minut z posledních 30 minut se seskupením období 5 minut](media/alerts-dynamic-thresholds/0009.png)

**Ignorovat data před** - Uživatelé mohou také volitelně definovat počáteční datum, od kterého by měl systém začít počítat prahové hodnoty od. Typický případ použití může dojít, když prostředek byl spuštěn v testovacím režimu a je nyní povýšen sloužit produkční úlohy, a proto chování jakékoli metriky během fáze testování by měla být ignorována.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Jak zjistíte, proč byla aktivována výstraha dynamických prahových hodnot?

Instance aktivovaných výstrah můžete prozkoumat v zobrazení výstrah buď kliknutím na odkaz v e-mailu nebo textové zprávě, nebo prohlížečem zobrazíte zobrazení výstrah na webu Azure Portal. [Přečtěte si další informace o zobrazení upozornění](alerts-overview.md#alerts-experience).

Zobrazení výstrahy zobrazuje:

- Všechny podrobnosti metriky v okamžiku, kdy dynamické prahové hodnoty upozornění aktivována.
- Graf období, ve kterém byla výstraha spuštěna, který obsahuje dynamické prahové hodnoty použité v tomto okamžiku.
- Možnost poskytnout zpětnou vazbu k upozornění dynamických prahových hodnot a zobrazení výstrah, které by mohly zlepšit budoucí zjišťování.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Spustí pomalé změny chování v metrike výstrahu?

Pravděpodobně ne. Dynamické prahové hodnoty jsou dobré pro detekci významných odchylek spíše než pomalu se vyvíjející problémy.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Kolik dat se používá k zobrazení náhledu a následnému výpočtu prahových hodnot?

Při prvním vytvoření pravidla výstrahy se prahové hodnoty uvedené v grafu vypočítají na základě dostatečného množství historických dat pro výpočet hodinových nebo denních sezónních vzorců (10 dní). Po vytvoření pravidla výstrahy dynamické prahové hodnoty používá všechna potřebná historická data, která jsou k dispozici, a budou se průběžně učit a přizpůsobovat na základě nových dat, aby byly prahové hodnoty přesnější. To znamená, že po tomto výpočtu bude graf také zobrazovat týdenní vzorky.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Kolik dat je potřeba k aktivaci výstrahy?

Pokud máte nový prostředek nebo chybějící data metriky, dynamické prahové hodnoty nespustí výstrahy před třemi dny nebo 30 vzorky metrických dat jsou k dispozici pro zajištění přesné prahové hodnoty.

## <a name="dynamic-thresholds-best-practices"></a>Doporučené postupy dynamických prahových hodnot

Dynamické prahové hodnoty lze použít na libovolnou platformu nebo vlastní metriku v Azure Monitoru a byla také vyladěna pro běžné metriky aplikací a infrastruktury.
Následující položky jsou osvědčené postupy pro konfiguraci výstrah na některé z těchto metrik pomocí dynamické prahové hodnoty.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamické prahové hodnoty na metrikách procenta procesoru virtuálního počítače

1. Na [webu Azure Portal](https://portal.azure.com)klikněte na **Monitor**. Zobrazení Sledování konsoliduje všechna nastavení monitorování a data v jednom zobrazení.

2. Klepněte na tlačítko **Výstrahy** a potom klepněte na tlačítko **+ Nové pravidlo výstrah**.

    > [!TIP]
    > Většina nožů prostředků má také **výstrahy** v jejich nabídce prostředků v části **Monitorování**, můžete také vytvořit výstrahy odtud.

3. Klikněte na **Vybrat cíl**, v podokně kontextu, které se načte, vyberte cílový prostředek, na který chcete upozornit. Pomocí rozevíracích seznamů **typu prostředek Odběr** a Virtuální **počítače** vyhledejte prostředek, který chcete monitorovat. K vyhledání zdroje můžete také použít vyhledávací panel.

4. Po výběru cílového zdroje klikněte na **Přidat podmínku**.

5. Vyberte **možnost Procento procesoru**.

6. Volitelně můžete metriku upřesnit úpravou **perioda** a **agregace**. Nedoporučuje se používat typ agregace Maximum pro tento typ metriky, protože je méně reprezentativní pro chování. Pro 'Maximální' typ agregace statické prahové hodnoty možná vhodnější.

7. Zobrazí se graf metriky za posledních 6 hodin. Definujte parametry výstrahy:
    1. **Typ podmínky** – zvolte možnost Dynamický.
    1. **Citlivost** – Chcete-li snížit výstražný šum, zvolte střední/nízkou citlivost.
    1. **Operátor** - Zvolte "větší než", pokud chování představuje využití aplikace.
    1. **Frekvence** – zvažte snížení na základě obchodního dopadu výstrahy.
    1. **Selhání období** (upřesnit možnost) - okno zpětného pohledu by měla být alespoň 15 minut. Například pokud je období nastaveno na pět minut, by měla být selhání období alespoň tři nebo více.

8. V tabulce metrik se zobrazí vypočtené prahové hodnoty na základě posledních dat.

9. Klikněte na **Done** (Hotovo).

10. Vyplňte **podrobnosti výstrahy,** jako **je název pravidla výstrahy**, **popis**a **závažnost**.

11. Přidejte skupinu akcí do výstrahy výběrem existující skupiny akcí nebo vytvořením nové skupiny akcí.

12. Kliknutím na **Hotovo** uložte pravidlo upozornění metriky.

> [!NOTE]
> Pravidla upozornění metriky vytvořená prostřednictvím portálu jsou vytvořena ve stejné skupině prostředků jako cílový prostředek.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamické prahové hodnoty v době spuštění požadavku HTTP přehledů aplikací

1. Na [webu Azure Portal](https://portal.azure.com)klikněte na **Monitor**. Zobrazení Sledování konsoliduje všechna nastavení monitorování a data v jednom zobrazení.

2. Klepněte na tlačítko **Výstrahy** a potom klepněte na tlačítko **+ Nové pravidlo výstrah**.

    > [!TIP]
    > Většina nožů prostředků má také **výstrahy** v jejich nabídce prostředků v části **Monitorování**, můžete také vytvořit výstrahy odtud.

3. Klikněte na **Vybrat cíl**, v podokně kontextu, které se načte, vyberte cílový prostředek, na který chcete upozornit. Pomocí rozevíracích seznamů typu **odběr** a **Přehledy aplikací** najdete prostředek, který chcete monitorovat. K vyhledání zdroje můžete také použít vyhledávací panel.

4. Po výběru cílového zdroje klikněte na **Přidat podmínku**.

5. Vyberte **čas spuštění požadavku HTTP**.

6. Volitelně můžete metriku upřesnit úpravou **perioda** a **agregace**. Nedoporučuje se používat typ agregace Maximum pro tento typ metriky, protože je méně reprezentativní pro chování. Pro 'Maximální' typ agregace statické prahové hodnoty možná vhodnější.

7. Zobrazí se graf metriky za posledních 6 hodin. Definujte parametry výstrahy:
    1. **Typ podmínky** – zvolte možnost Dynamický.
    1. **Operátor** - Zvolte "větší než" snížit výstrahy aktivována na zlepšení doby trvání.
    1. **Frekvence** – zvažte snížení na základě obchodního dopadu výstrahy.

8. V tabulce metrik se zobrazí vypočtené prahové hodnoty na základě posledních dat.

9. Klikněte na **Done** (Hotovo).

10. Vyplňte **podrobnosti výstrahy,** jako **je název pravidla výstrahy**, **popis**a **závažnost**.

11. Přidejte skupinu akcí do výstrahy výběrem existující skupiny akcí nebo vytvořením nové skupiny akcí.

12. Kliknutím na **Hotovo** uložte pravidlo upozornění metriky.

> [!NOTE]
> Pravidla upozornění metriky vytvořená prostřednictvím portálu jsou vytvořena ve stejné skupině prostředků jako cílový prostředek.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretace grafů dynamických prahových hodnot

Následuje graf zobrazující metriku, její dynamické prahové hodnoty a některé výstrahy aktivována, když hodnota byla mimo povolené prahové hodnoty.

![Další informace o konfiguraci upozornění na metriky](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

K interpretaci předchozího grafu použijte následující informace.

- **Modrá čára** - Skutečná měřená metrika v čase.
- **Modrá stínovaná oblast** – Zobrazuje povolený rozsah metriky. Dokud hodnoty metriky zůstanou v tomto rozsahu, nedojde k žádné výstraze.
- **Modré tečky** - Pokud kliknete na část grafu a pak najedete myší na modrou čáru, zobrazí se pod kurzorem modrá tečka zobrazující jednotlivou agregovotou metrickou hodnotu.
- **Rozbalovací okno s modrou tečkou** - Zobrazuje naměřenou metrickou hodnotu (modrou tečku) a horní a dolní hodnoty povoleného rozsahu.  
- **Červená tečka s černým kruhem** – Zobrazí první hodnotu metriky mimo povolený rozsah. Toto je hodnota, která vyvolá upozornění metriky a umístí ji do aktivního stavu.
- **Červené tečky**- Uveďte další naměřené hodnoty mimo povolený rozsah. Nebudou vypalování další chorometrických výstrah, ale výstraha zůstane aktivní.
- **Červená oblast** – Zobrazuje čas, kdy byla hodnota metriky mimo povolený rozsah. Výstraha zůstane v aktivním stavu, dokud jsou následující naměřené hodnoty mimo povolený rozsah, ale nejsou aktivovány žádné nové výstrahy.
- **Konec červené oblasti** - Když je modrá čára zpět uvnitř povolených hodnot, červená oblast se zastaví a naměřená hodnota se změní na modrou. Stav upozornění metriky vypálené v době červené tečky s černým obrysem je nastaven na vyřešený. 
