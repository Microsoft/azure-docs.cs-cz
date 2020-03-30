---
title: Případ použití analýzy mínění
titleSuffix: ML Studio (classic) - Azure
description: Jak vytvořit modely analýzy textu v Azure Machine Learning Studio (klasické) pomocí modulů pro předběžné zpracování textu, N-gramů nebo hash funkcí
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217883"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Vytvoření modelu analýzy mínění v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klasické) můžete použít k vytváření a zprovoznění modelů analýzy textu. Tyto modely vám mohou pomoci vyřešit například problémy s klasifikací dokumentů nebo analýzou mínění.

V experimentu analýzy textu byste obvykle:

1. Vyčistit a předběžně zpracovat textovou datovou sadu
2. Extrahovat vektory číselných prvků z předem zpracovaného textu
3. Typklasifikace vlaku nebo regresní model
4. Skóre a ověření modelu
5. Nasazení modelu do produkčního prostředí

V tomto tutoriálu se naučíte tyto kroky, když procházíme modelem analýzy sentimentu pomocí datové sady Amazon Book Reviews (viz tento výzkumný dokument "Biografie, Bollywood, Boom-boxy a mixéry: Přizpůsobení domény pro klasifikaci sentimentu" od Johna Blitzera, Mark Dredze a Fernando Pereira; Asociace výpočetní lingvistiky (ACL), 2007.) Tato datová sada se skládá z hodnocení recenze (1-2 nebo 4-5) a volného textu. Cílem je předpovědět skóre recenze: nízké (1-2) nebo vysoké (4-5).

Experimenty, které jsou popsány v tomto kurzu, najdete v Galerii AI Azure:

[Předpovědět recenze knih](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Předpovědět recenze knih - Prediktivní experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: Vyčištění a předběžné zpracování textové datové sady
Experiment zahájíme rozdělením skóre recenze do kategorických nízkých a vysokých košů, abychom problém formulovali jako klasifikaci dvou tříd. Používáme [moduly Upravit metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) a [Skupinové kategorické hodnoty.](https://msdn.microsoft.com/library/azure/dn906014.aspx)

![Vytvořit popisek](./media/text-analytics-module-tutorial/create-label.png)

Potom vyčistíme text pomocí modulu [Preprocess Text.](https://msdn.microsoft.com/library/azure/mt762915.aspx) Čištění snižuje šum v datové sadě, pomáhá najít nejdůležitější funkce a zlepšuje přesnost konečného modelu. Odstraňujeme stopwords - běžná slova jako "" nebo "a" - a čísla, speciální znaky, duplicitní znaky, e-mailové adresy a adresy URL. Text také převedeme na malá písmena, slova lemmatize a detekujeme hranice vět, které jsou pak v předem zpracovaném textu označeny symbolem |||.I převést text na malá písmena, lemmatize slova a detekovat hranice věty, které jsou pak označeny symbolem "|||" v předem zpracovaném textu.

![Předzpracování textu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co když chcete použít vlastní seznam stopwords? Můžete předat jako volitelný vstup. Můžete také použít vlastní c# syntaxe regulární výraz nahradit podřetězce a odstranit slova podle části řeči: subsládky, slovesa nebo přídavná jména.

Po dokončení předběžného zpracování rozdělíme data do vlakových a testovacích sad.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Extrahování vektorů číselných prvků z předem zpracovaného textu
Chcete-li vytvořit model pro textová data, obvykle je třeba převést volný text na vektory číselných prvků. V tomto příkladu používáme [Extract N-Gram Features z textového modulu](https://msdn.microsoft.com/library/azure/mt762916.aspx) k transformaci textových dat do takového formátu. Tento modul přebírá sloupec slov oddělených mezerami a vypočítá slovník slov neboli N-gramů slov, která se zobrazí v datové sadě. Potom spočítá, kolikrát se každé slovo nebo N-gram zobrazí v každém záznamu a vytvoří vektory funkcí z těchto počtů. V tomto kurzu jsme nastavili velikost N-gram na 2, takže naše vektory funkcí zahrnují jednotlivá slova a kombinace dvou následujících slov.

![Extrakt N-gramů](./media/text-analytics-module-tutorial/extract-ngrams.png)

Použijeme tSP*IDF (Term Frequency Inverzní frekvence dokumentu) vážení na N-gram počítá. Tento přístup přidá váhu slov, která se často zobrazují v jednom záznamu, ale jsou vzácné v celé datové sadě. Mezi další možnosti patří binární, TF a graf vážení.

Takové textové prvky mají často vysokou dimenzionalitu. Pokud má například korpus 100 000 jedinečných slov, prostor pro funkci bude mít 100 000 nebo více, pokud se použijí N-gramy. Modul Extrahovat n-gram funkce nabízí sadu možností, jak snížit dimenzionalitu. Můžete vyloučit slova, která jsou krátká nebo dlouhá nebo příliš neobvyklá nebo příliš častá na to, aby měla významnou prediktivní hodnotu. V tomto kurzu vyloučíme N-gramy, které se zobrazují v méně než 5 záznamech nebo ve více než 80 % záznamů.

Výběr funkcí můžete také použít k výběru funkcí, které jsou nejvíce korelovány s cílem předpovědi. K výběru 1000 funkcí používáme výběr funkcí Chi-Squared. Slovní zásobu vybraných slov nebo N-gramů můžete zobrazit kliknutím na pravý výstup modulu Extrahovat N-gramů.

Jako alternativní přístup k použití extrahovat N-Gram funkce, můžete použít funkce hash modul. Všimněte si však, že [funkce hashing](https://msdn.microsoft.com/library/azure/dn906018.aspx) nemá možnosti výběru vestavěných funkcí nebo vážení TF*IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: Klasifikace vlaku nebo regresní model
Nyní byl text transformován na číselné sloupce funkcí. Datová sada stále obsahuje sloupce řetězců z předchozích fází, takže je v datové sadě vyloučíme pomocí možnosti Vybrat sloupce v datové sadě.

Pak použijeme [dvoutřídní logistickou regresi](https://msdn.microsoft.com/library/azure/dn905994.aspx) k předvídání našeho cíle: vysoké nebo nízké skóre recenze. V tomto okamžiku byl problém analýzy textu transformován na problém pravidelné klasifikace. Můžete použít nástroje, které jsou k dispozici v Azure Machine Learning Studio (klasické) ke zlepšení modelu. Můžete například experimentovat s různými klasifikátory, abyste zjistili, jak přesné výsledky poskytují, nebo použít hyperparametrické ladění ke zlepšení přesnosti.

![Vlak a skóre](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: Skóre a ověření modelu
Jak byste ověřili trénovaný model? Skórujeme ji proti testovací datové sadě a vyhodnocujeme přesnost. Model se však naučil slovní zásobu N-gramů a jejich hmotností z trénovací datové sady. Proto bychom měli používat tuto slovní zásobu a tyto váhy při extrahování funkcí z testovacích dat, na rozdíl od vytváření slovní zásoby znovu. Proto přidáme modul Extract N-Gram Features do větve hodnocení experimentu, připojíme výstupní slovní zásobu z větve školení a nastavíme režim slovní zásoby na jen pro čtení. Také zakážeme filtrování N-gramů podle frekvence nastavením minimální instance 1 a maximálně na 100 % a vypneme výběr funkcí.

Po textový sloupec v testovacích datech byla transformována na číselné sloupce funkce, vyloučíme sloupce řetězce z předchozích fází, jako v trénovací větvi. Potom použijeme modul Modelu skóre k vyhodnocení předpovědí a modulu Vyhodnotit model k vyhodnocení přesnosti.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5: Nasazení modelu do produkčního prostředí
Model je téměř připraven k nasazení do produkčního prostředí. Při nasazení jako webová služba, trvá volný textový řetězec jako vstup a vrátí předpověď "vysoká" nebo "nízká". Používá naučený N-gram slovní zásobu k transformaci textu na funkce a vyškolený logistický regresní model, aby se předpověď z těchto funkcí. 

Chcete-li nastavit prediktivní experiment, nejprve uložíme slovník N-gram jako datovou sadu a trénovaný logistický regresní model z trénovacího oboru experimentu. Potom uložíme experiment pomocí "Uložit jako" k vytvoření grafu experimentu pro prediktivní experiment. Z experimentu odstraníme modul Split Data a trénovací větev. Poté připojíme dříve uloženou n-gramovou slovní zásobu a model k extrakci n-gramových funkcí a modulů score modelu. Také odebereme modul Vyhodnotit model.

Vložíme vybrat sloupce v modulu datové sady před preprocess text modul uodebrat sloupec popisek a zrušte výběr "Připojit sloupec skóre do datové sady" možnost v modulu skóre. Tímto způsobem webová služba nepožaduje popisek, který se pokouší předpovědět, a neodráží vstupní funkce v odpovědi.

![Prediktivní experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nyní máme experiment, který může být publikován jako webová služba a volána pomocí api pro požadavek a odpověď nebo dávkové spuštění.

## <a name="next-steps"></a>Další kroky
Informace o modulech analýzy textu naleznete v [dokumentaci k msdn](https://msdn.microsoft.com/library/azure/dn905886.aspx).

