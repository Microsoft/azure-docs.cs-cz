---
title: Příklady kanálů & datových sad pro návrháře
titleSuffix: Azure Machine Learning
description: Naučte se používat ukázky v Azure Machine Learning designeru k přechodům ke spuštění kanálů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 10/14/2020
ms.custom: designer
ms.openlocfilehash: a27e18d9b6f6307f1cc8ed48ca897dd4d1f0f1f3
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555494"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Příklady kanálů & datových sad pro návrháře Azure Machine Learning

Pomocí vestavěných příkladů v Návrháři Azure Machine Learning můžete rychle začít vytvářet vlastní kanály strojového učení. [Úložiště GitHub](https://github.com/Azure/MachineLearningDesigner) návrháře Azure Machine Learning obsahuje podrobnou dokumentaci, která vám pomůže pochopit některé běžné scénáře strojového učení.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree) .
* Pracovní prostor Azure Machine Learning 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>Použití ukázkových kanálů

Návrhář uloží kopii ukázkových kanálů do pracovního prostoru studia. Kanál můžete upravit, abyste ho přizpůsobili vašim potřebám a ušetřili si ho jako vlastní. Použijte je jako výchozí bod pro rychlé zprovozněníí vašich projektů.

Tady je postup použití ukázkového návrháře:

1. Přihlaste se k <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **Návrhář**.

1. V části **Nový kanál** vyberte vzorový kanál.

    Pro úplný seznam ukázek vyberte **Zobrazit další ukázky** .

1. Pokud chcete spustit kanál, musíte nejdřív nastavit výchozí výpočetní cíl pro spuštění kanálu.

   1. V podokně **Nastavení** napravo od plátna vyberte **vybrat cíl výpočtů**.

   1. V dialogovém okně, které se zobrazí, vyberte existující cíl služby COMPUTE nebo vytvořte nový. Vyberte **Uložit**.

   1. Kliknutím na **Odeslat** v horní části plátna odešlete spuštění kanálu.

   V závislosti na ukázkových kanálech a výpočetních nastaveních může dokončení spuštění trvat delší dobu. Výchozí nastavení COMPUTE mají minimální velikost uzlu 0, což znamená, že Návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky už jsou přidělené. Kromě toho Návrhář používá výsledky v mezipaměti pro každý modul k dalšímu zvýšení efektivity.


1. Až se kanál dokončí, můžete si projít kanál a zobrazit výstup pro každý modul a získat další informace. Výstupy modulu můžete zobrazit pomocí následujících kroků:

   1. Klikněte pravým tlačítkem na modul na plátně, jehož výstup chcete vidět.
   1. Vyberte **vizualizovat**.


   Použijte ukázky jako výchozí body pro některé z nejběžnějších scénářů strojového učení.

## <a name="regression"></a>Regrese

Prozkoumejte tyto integrované regresní ukázky.

| Ukázkový název | Popis | 
| --- | --- |
| [Regrese – předpověď ceny automobilu (základní)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-basic.md) | Předpovědět ceny aut pomocí lineární regrese. |
| [Regrese – předpověď ceny automobilu (rozšířené)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) | Předpovědět ceny aut pomocí rozhodovací doménové struktury a dalších zesílených rozhodovacích stromů. Porovnejte modely a Najděte nejlepší algoritmus.

## <a name="classification"></a>Classification

Prozkoumejte tyto integrované ukázky klasifikace. Další informace o ukázkách se dozvíte bez odkazů na dokumentaci, a to tak, že otevřete ukázky a místo toho zobrazíte komentáře modulu.

| Ukázkový název | Popis | 
| --- | --- |
| [Binární klasifikace s výběrem funkcí – předpověď příjmů](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-feature-selection-income-prediction.md) | Předpovídání výnosů jako vysoké nebo nízké, pomocí rozstředního rozhodovacího stromu se dvěma třídami K výběru funkcí použijte korelaci Pearsonova.
| [Binární klasifikace s vlastním skriptem Pythonu – předpověď úvěrového rizika](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-python-credit-prediction.md) | Klasifikujte úvěrové aplikace jako vysoké nebo nízké riziko. K vážení dat použijte modul spouštění skriptu Pythonu.
| [Binární klasifikace – předpověď vztahů se zákazníky](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-customer-relationship-prediction.md) | Předpověď změn zákazníků pomocí dvou vyrovnaných rozhodovacích stromů se dvěma třídami Pomocí SMOTE můžete vzorkovat data s posunutím.
| [Klasifikace textu – Wikipedii sada dat SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/text-classification-wiki.md) | Klasifikujte typy společností z článků Wikipedii s využitím více tříd logistické regrese. |
| Více tříd – rozpoznávání písmen | Vytvořte soubor se souborem sady binárních klasifikátorů pro klasifikaci psaných písmen. |

## <a name="computer-vision"></a>Počítačové zpracování obrazu

Prozkoumejte tyto integrované ukázky počítačové vize. Další informace o ukázkách se dozvíte bez odkazů na dokumentaci, a to tak, že otevřete ukázky a místo toho zobrazíte komentáře modulu.

| Klasifikace obrázku pomocí DenseNet | Použijte moduly počítačového vidění k sestavení modelu klasifikace imagí založeného na PyTorch DenseNet. | 

## <a name="recommender"></a>Doporučovací systém

Prozkoumejte tyto integrované příklady doporučených kroků. Další informace o ukázkách se dozvíte bez odkazů na dokumentaci, a to tak, že otevřete ukázky a místo toho zobrazíte komentáře modulu.

| Ukázkový název | Popis | 
| --- | --- |
| Špičkové doporučení na hloubku & – předpověď hodnocení restaurace | Sestavte modul doporučených pro restaurace z možností restaurace/uživatel a hodnocení.|
| Doporučení – tweety hodnocení filmu | Sestavte modul doporučený pro film z funkcí video a uživatele a hodnocení.|

## <a name="utility"></a>Nástroj

Přečtěte si další informace o ukázkách, které předvádějí nástroje a funkce machine learningu. Další informace o ukázkách se dozvíte bez odkazů na dokumentaci, a to tak, že otevřete ukázky a místo toho zobrazíte komentáře modulu.

| Ukázkový název | Popis | 
| --- | --- |
| Binární klasifikace pomocí pro dostupné modelu – předpověď příjmů za dospělé | Pro dostupné je systém strojového učení, který nabízí hranici strojového učení s technikami, jako je online, hashing, allreduce, snižování, learning2search, aktivní a interaktivní učení. Tento příklad ukazuje, jak použít model pro dostupné k sestavení binárního klasifikačního modelu. 
| [Použití vlastního skriptu R – předpověď zpoždění letů](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/r-script-flight-delay-prediction.md) | Pomocí vlastního skriptu jazyka R můžete předpovědět, jestli se plánovaný osobní let bude zpozdit o více než 15 minut.
| Křížové ověření pro předpověď v případě binární klasifikace – pro příjem | Pro vytvoření binárního klasifikátoru pro příjem z dospělého použijte vzájemné ověřování.
| Důležitost funkce permutací | Použijte funkci permutace důležitost k výpočtu skóre důležitosti pro testovací datovou sadu. 
| Ladění parametrů pro binární klasifikaci – předpověď příjmů za dospělé | K nalezení optimálních parametrů pro sestavení binárního klasifikátoru použijte parametry ladění modelu. |

## <a name="datasets"></a>Datové sady

Při vytváření nového kanálu v Návrháři Azure Machine Learning je ve výchozím nastavení zahrnuto několik ukázkových datových sad. Tyto ukázkové datové sady jsou používány vzorovými kanály na domovské stránce návrháře. 

Ukázkové datové sady jsou k dispozici v kategorii ukázky **datových sad** - **Samples** . Najdete ho v paletě modulu nalevo od plátna v návrháři. Můžete použít kteroukoli z těchto datových sad ve vlastním kanálu přetažením na plátno.

| Název datové sady &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Popis datové sady |
|-------------|:--------------------|
| Datová sada klasifikace binárního výnosu pro dospělé | Podmnožina 1994ch databází, která používá pracovní dospělí za stáří 16, s upraveným indexem příjmů > 100.<br/>**Použití** : Klasifikujte lidi pomocí demografických údajů, abyste předpovídat, jestli osoba za 50 tis za rok.<br/> **Související výzkum** : Kohavi, R., Becker, B., (1996). [UCI Machine Learning úložiště](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, školní školy a počítačové vědy|
|Údaje o cenách automobilu (RAW)|Informace o automatickém mobilním zařízení, včetně ceny, funkcí, jako je třeba počet lahví a MPG, a hodnocení rizikového rizika.<br/> K automatické ceně je zpočátku přidruženo skóre rizika. Pak se upraví na skutečné riziko v procesu známém Pojistní matematici jako symboling. Hodnota + 3 znamená, že auto je rizikové a hodnota-3 je pravděpodobně bezpečná.<br/>**Použití** : předpověď rizikového skóre podle funkcí pomocí regrese nebo klasifikace lineární<br/>**Související výzkum** : Schlimmer, J.C. (1987). [UCI Machine Learning úložiště](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, školní informace a počítačové vědy. |
| Appetency popisky CRM – sdílené |Štítky z konference KDD ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)2009) – výzva pro předpověď vztahů se zákazníky.|
|Sdílené změny v CRM|Štítky z konference KDD ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)2009) – výzva pro předpověď vztahů se zákazníky.|
|Sdílená datová sada CRM | Tato data pocházejí z konference KDDu pro předpověď vztahů se zákazníky 2009 ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Datová sada obsahuje zákazníky 50 tis z francouzské telekomunikační společnosti oranžová. Každý zákazník má 230 funkcí Anonyme, 190 z nich je numerická a 40 jsou kategorií. Funkce jsou velmi zhuštěné. |
|Sdílení popisků pro CRM|Popisky z konference KDD ([orange_large_train_upselling. labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels) – výzva k vy2009ce vztahu zákazníka|
|Data o zpoždění letů|Data o výkonu pro osobní čas, která jsou přijímána z kolekce TranStatsch dat ministerstva dopravy USA ([včas).](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)<br/>Datová sada se zabývá časovým obdobím April-October 2013. Před nahráním do návrháře byla datová sada zpracována takto: <br/>– Datová sada byla vyfiltrovaná tak, aby se kryla jenom 70 nejvytíženější letiště v kontinentální části USA. <br/>– Zrušené lety byly označeny za zpožděné o více než 15 minut. <br/>– Odstraněné lety byly odfiltrovány. <br/>-Byly vybrány následující sloupce: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled.|
|Datová sada UCI pro německé kreditní karty|Datová sada Statlog (německá kreditní karta) ([Statlog + němčina + Credit + data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))) s použitím souboru německého. data.<br/>Datová sada klasifikuje osoby, které jsou popsány sadou atributů, jako nízké nebo vysoké úvěrové riziko. Každý příklad představuje osobu. K dispozici jsou 20 funkcí, číselné i kategorií a binární označení (hodnota úvěrového rizika). Položky vysokého kreditu mají štítek = 2, nízké položky úvěrového rizika mají označení = 1. Náklady na chybnou klasifikaci příkladu s nízkým rizikem, který je vysoký, je 1, zatímco náklady na netřídění s vysokým rizikovým příkladem jsou 5.|
|Názvy filmů IMDB|Datová sada obsahuje informace o videích, které byly ohodnoceny na Twitteru tweety: IMDB ID filmu, název filmu, Žánr a produkční rok. V datové sadě jsou 17K filmy. Datová sada byla představena v dokumentu "S". Dooms, T. de Pessemier a L. Martens. MovieTweetings: datová sada hodnocení filmu shromážděná z Twitteru. Dílna v Crowdsourcingový a lidské výpočtu pro doporučované systémy, CrowdRec na RecSys 2013. "|
|Hodnocení filmů|Datová sada je rozšířená verze datové sady ve formátu videa. Tato datová sada má 170K hodnocení filmů, která se extrahují z dobře strukturovaných tweety na Twitteru. Každá instance představuje prostupnost a je řazená kolekce členů: ID uživatele, ID videa IMDB, hodnocení, časové razítko, počet oblíbených položek pro tento seznam a počet předaných prostředků. Datová sada byla zpřístupněna prostřednictvím. říkáme, S. Dooms, B. loni a D. Tikk pro doporučované systémy Challenge 2014.|
|Datová sada počasí|Hodinově založené počasí z NOAA ([sloučená data z 201304 do 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Údaje o počasí se týkají pozorování z povětrnostních stanic na letišti, které pokrývají časové období April-October 2013. Před nahráním do návrháře byla datová sada zpracována takto:    <br/> – ID stanice počasí byly namapovány na odpovídající ID letišť.    <br/> – Počasí, které nejsou přidružené k letiště 70 nejvytíženější, se odfiltrují.    <br/> – Sloupec data byl rozdělen na sloupce samostatného roku, měsíce a dne.    <br/> -Byly vybrány následující sloupce: AirportID, rok, měsíc, den, čas, časové pásmo, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter,,|
|Wikipedii sada dat SP 500|Data jsou odvozena z Wikipedii ( https://www.wikipedia.org/) na základě článků z každé z&společnosti P 500, uložená jako data XML).    <br/>Před nahráním do návrháře byla datová sada zpracována takto:    <br/> – Extrakce textového obsahu pro každou konkrétní firmu    <br/> -Odebrat formátování wiki    <br/> -Odebrání jiných než alfanumerických znaků    <br/> -Převést veškerý text na malá písmena    <br/> – Přidaly se známé kategorie společnosti.    <br/>Upozorňujeme, že některé společnosti nemohly najít článek, takže počet záznamů je menší než 500.|
|Data funkcí restaurace| Sada metadat o restauracích a jejich funkcích, jako je například typ potraviny, druh stravování a umístění. <br/>**Použití** : tuto datovou sadu použijte v kombinaci s dalšími dvěma datovými sadami restaurace, abyste mohli naučit a testovat doporučující systém.<br/> **Související výzkum** : Bache, K. a Lichman, M. (2013). [UCI Machine Learning úložiště](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, školní informace a počítačové vědy.|
|Hodnocení restaurace| Obsahuje hodnocení vydaná uživateli do restaurací na škále od 0 do 2.<br/>**Použití** : tuto datovou sadu použijte v kombinaci s dalšími dvěma datovými sadami restaurace, abyste mohli naučit a testovat doporučující systém. <br/>**Související výzkum** : Bache, K. a Lichman, M. (2013). [UCI Machine Learning úložiště](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, školní informace a počítačové vědy.|
|Zákaznická data restaurace| Sada metadat o zákaznících, včetně demografických údajů a předvoleb. <br/>**Použití** : tuto datovou sadu použijte v kombinaci s dalšími dvěma datovými sadami restaurace, abyste mohli naučit a testovat doporučující systém. <br/> **Související výzkum** : Bache, K. a Lichman, M. (2013). [Machine Learning úložiště UCI](https://archive.ics.uci.edu/ml) Irvine, CA: University of California, školní informace a počítačové vědy.|

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Seznamte se se základy prediktivní analýzy a strojového učení s využitím [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md)

