---
title: Případ použití analýzy mínění
titleSuffix: ML Studio (classic) - Azure
description: Postup tvorby modelů textu Analytics v Azure Machine Learning Studio (Classic) pomocí modulů pro předběžné zpracování textu, N-gramů nebo funkcí hashování funkcí
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 89fc5196977f53e040e1a6553b46ca57f39b18b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217883"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio-classic"></a>Vytvoření modelu analýzy mínění v Azure Machine Learning Studio (Classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

K sestavování a zprovoznění modelů textu Analytics můžete použít Azure Machine Learning Studio (Classic). Tyto modely vám mohou pomáhat při řešení problémů, například při klasifikaci dokumentů nebo při potížích s analýzou mínění.

V experimentu pro analýzu textu byste měli obvykle:

1. Vyčistit a předzpracovat textovou datovou sadu
2. Extrahovat vektory číselných funkcí z předem zpracovaného textu
3. Klasifikace a regresní model vlaků
4. Skóre a ověření modelu
5. Nasazení modelu do produkčního prostředí

V tomto kurzu se seznámíte s těmito kroky, které provedeme prostřednictvím modelu analýzy mínění s využitím datové sady pro recenze knih Amazon (podívejte se na tento dokument výzkumu "Biographies, Bollywood, bác-in a Blends: přizpůsobení domény pro klasifikaci mínění" pomocí Jan Blitzer, Mark Dredze a Fernando Pereira; Asociace výpočetních lingvistů (ACL), 2007.) Tato datová sada se skládá z výsledků kontroly (1-2 nebo 4-5) a textu volného formátu. Cílem je předpovědět skóre recenze: nízká (1-2) nebo vysoká (4-5).

Experimenty, které jsou popsané v tomto kurzu, najdete na adrese Azure AI Gallery:

[Předpovědět recenze knih](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Prediktivní revize v knize – prediktivní experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: vyčištění a předzpracování textové datové sady
Experimenty zahájíme vydělením recenze skóre do kategorií nízké a vysoké sady pro formulaci problému jako klasifikace dvou tříd. Používáme moduly [kategorií hodnoty](https://msdn.microsoft.com/library/azure/dn906014.aspx) pro [úpravy metadat](https://msdn.microsoft.com/library/azure/dn905986.aspx) a Group.

![Vytvořit popisek](./media/text-analytics-module-tutorial/create-label.png)

Pak text čistíme pomocí modulu [textu předzpracování](https://msdn.microsoft.com/library/azure/mt762915.aspx) . Čištění omezí šum v datové sadě, což vám usnadní vyhledání nejdůležitějších funkcí a zlepšení přesnosti finálního modelu. Odebrali jsme stopslova – běžná slova, například "" nebo "a"-a čísla, speciální znaky, duplicitní znaky, e-mailové adresy a adresy URL. Převedeme také text na malá písmena, lemmatize slova a detekuje hranice věty, které jsou následně označeny symbolem | | | v předem zpracovávaném textu.

![Předzpracování textu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co když chcete použít vlastní seznam stopslova? Můžete ho předat jako nepovinný vstup. Můžete také použít vlastní syntaxi regulárního výrazu jazyka C# a nahradit podřetězce a odstranit slova podle části řeči: podstatná jména, slovesa nebo jména.

Po dokončení předzpracování jsme data rozdělili do vlakových a testovacích sad.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: extrakce číselných vektorů funkcí z předem zpracovaného textu
Chcete-li vytvořit model pro textová data, obvykle je nutné převést text volné formy na vektory číselných funkcí. V tomto příkladu používáme k transformaci textových dat do takového formátu [extrakci funkcí N-gramů z modulu text](https://msdn.microsoft.com/library/azure/mt762916.aspx) . Tento modul používá sloupec slov oddělených prázdným znakem a vypočítá slovník slov nebo N gramů slov, který se zobrazí ve vaší datové sadě. Pak se počítá, kolikrát se každé slovo nebo N-gram zobrazí v jednotlivých záznamech, a z těchto počtů vytvoří vektory funkcí. V tomto kurzu nastavíme N-gram velikosti na 2, takže naše vektory funkce budou zahrnovat jednotlivá slova a kombinace dvou následujících slov.

![Extrahovat N-gramů](./media/text-analytics-module-tutorial/extract-ngrams.png)

Použijeme TF * IDF (četnost inverzních dokumentů) pro počty N-gramů. Tento přístup přidává váhu slov, která se často zobrazují v jednom záznamu, ale jsou zřídka v celé datové sadě. Mezi další možnosti patří binární, TF a vážení grafu.

Takové funkce textu často mají vysokou dimenzionální možnosti. Například pokud má vaše corpus 100 000 jedinečných slov, bude mít vaše místo ve vaší funkci 100 000 dimenzí, nebo více, pokud se používají N-gramy. Modul extrakce N-gram funkcí poskytuje sadu možností pro omezení dimenzionálního typu. Můžete zvolit, že se mají vyloučit slova, která jsou krátká nebo dlouhá, nebo příliš častá nebo příliš častá, aby měla značnou prediktivní hodnotu. V tomto kurzu vyloučíme N-gramy, které se zobrazují v méně než 5 záznamech, nebo ve více než 80% záznamů.

Můžete také použít výběr funkcí a vybrat jenom ty funkce, které jsou nejvíc korelující s cílem předpovědi. Pro výběr funkcí 1000 používáme výběr funkcí chí-kvadrát. Slovníky vybraných slov nebo N-gramů si můžete zobrazit tak, že kliknete na správný výstup modulu extrakce N-gramů.

Jako alternativní přístup k používání extrakce N-gramových funkcí můžete použít modul hashování funkcí. Všimněte si, že [funkce hash funkcí](https://msdn.microsoft.com/library/azure/dn906018.aspx) nemá možnosti výběru funkcí pro sestavení, nebo vážení TF * IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: klasifikace a regresní model vlaků
Nyní byl text transformován na sloupce číselných funkcí. Datová sada stále obsahuje sloupce řetězců z předchozích fází, proto používáme výběr sloupců v datové sadě k jejich vyloučení.

Potom pro předpověď našeho cíle používáme [dvě logistické regrese](https://msdn.microsoft.com/library/azure/dn905994.aspx) : vysoké nebo nízké skóre. V tuto chvíli byl problém s textovou analýzou transformované na běžný problém klasifikace. Pomocí nástrojů, které jsou k dispozici v Azure Machine Learning Studio (Classic), můžete model vylepšit. Například můžete experimentovat s různými tříděními a zjistit, jak přesné výsledky poskytují, nebo můžete použít ladění pomocí parametrů pro zlepšení přesnosti.

![Vlak a skóre](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: skóre a ověření modelu
Jak byste mohli vyškolený model ověřit? Vyhodnocujeme ho proti testovací sadě a vyhodnotit přesnost. Model však získal slovní druh N-gramů a jejich tloušťky z datové sady školení. Proto při extrakci funkcí z testovacích dat doporučujeme použít tento slovník a váhy, a to na rozdíl od vytvoření poruše slovníku. Proto přidáme do vyhodnocovací větve experimentu tento modul funkcí s výpisem N-gramů, připojíte výstupní slovník z školicí větve a nastavíte režim slovníku na jen pro čtení. Filtrování N-gramů také zakážete tak, že nastavíte minimum na 1 instanci a maximum na 100% a vypnete výběr funkce.

Po transformaci textového sloupce v datech testu na sloupce numerických funkcí vyloučíme sloupce řetězců z předchozích fází, jako je ve školicí větvi. Pak použijeme modul bodového modelu k vytvoření předpovědi a vyhodnocení modulu modelu k vyhodnocení přesnosti.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5: nasazení modelu do produkčního prostředí
Model je skoro připravený k nasazení do produkčního prostředí. Když se nasadí jako webová služba, jako vstup Převede textový řetězec s volným tvarem a vrátí předpověď "vysoké" nebo "nízká". Pomocí zjištěného slovníku N-gramů transformuje text na funkce a vyškolený model logistické regrese, který z těchto funkcí provede předpověď. 

Pro nastavení prediktivního experimentu nejprve uložíme slovník N-gram jako datovou sadu a školicí model logistické regrese z školicí větve experimentu. Pak zkusíme experiment použít "Uložit jako" a vytvořit graf experimentu pro prediktivní experiment. Z experimentu odebereme modul Split data a školicí větev. Pak propojíme dřív uložený slovník a model N-gramů a extrahujete N-gram funkcí a moduly modelu skóre. Odebrali jsme také modul vyhodnocení modelu.

Před tím, než začnete s odebráním sloupce popisku a zrušením výběru možnosti připojit sloupec skóre k datové sadě v modulu skóre, vložíte do modulu datová sada možnost vybrat sloupce. Díky tomu webová služba nepožaduje popisek, který se pokouší odhadnout, a nevrací funkce vstupu v reakci.

![Prediktivní experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Nyní máme experiment, který je možné publikovat jako webovou službu a který se nazývá pomocí rozhraní API pro zpracování požadavků a odpovědí.

## <a name="next-steps"></a>Další kroky
Přečtěte si o modulech Text Analytics z [dokumentace MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

