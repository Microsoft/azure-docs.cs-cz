---
title: Vytvoření modelů analýzy mínění
titleSuffix: Azure Machine Learning Studio
description: Postup vytvoření modelů analýzy textu v Azure Machine Learning Studio pomocí moduly pro předzpracování textu, N-gramy nebo hashování
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 79882fc895262f0fb607ec048e7912ae5f2efdd7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242664"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Vytvoření modelů analýzy subjektivního hodnocení v nástroji Azure Machine Learning Studio

Azure Machine Learning můžete sestavovat a zprovozňovat modely analýzy textu. Tyto modely vám může pomoct vyřešit, například problémy analýzy dokumentu klasifikace nebo mínění.

V experimentu analýzy textu které by obvykle:

1. Čištění a předběžné zpracování textu datové sady
2. Extrahovat vektory číselné funkce z předběžného zpracování textu
3. Klasifikační nebo regresní model trénovat na základě modelu
4. Stanovení skóre a ověření modelu
5. Nasazení modelu do produkčního prostředí

V tomto kurzu se dozvíte tyto kroky jako vás provedeme procesem model analýzy subjektivního hodnocení s použitím datovou sadu Amazon recenzí (najdete v tomto dokumentu výzkumu "biografie, Bollywood, Bum polí a Blenders: Domény přizpůsobení pro klasifikaci subjektivního hodnocení"podle Jan Blitzer označit Dredze a ostrov Fernando Pereira; Přidružení výpočetní lingvistiky (ACL), 2007.) Tato datová sada se skládá z revize skóre (1 – 2 nebo 4. – 5) a textu volného tvaru. Cílem je možnost předvídat skóre revizi: Nízká (1 - 2) nebo Vysoká (4 až 5).

Můžete najít experimenty v galerii Azure AI popsané v tomto kurzu:

[Předpověď recenzí](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Předpověď recenzí – prediktivní Experiment](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: Čištění a předběžné zpracování textu datové sady
Začneme experiment vydělením skóre revize do kategorií nízký klíč a vysoký kbelíků formulovat problému jako dvěma třídami klasifikace. Používáme [upravit Metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) a [Kategorických hodnot skupiny](https://msdn.microsoft.com/library/azure/dn906014.aspx) moduly.

![Vytvoření popisku](./media/text-analytics-module-tutorial/create-label.png)

Potom jsme vyčistit text pomocí [předběžně zpracovat Text](https://msdn.microsoft.com/library/azure/mt762915.aspx) modulu. Čištění snižuje šumu v datové sadě, pomáhají nacházet nejdůležitější funkce a zvýšit přesnost finálního modelu. Odebereme stopword - běžná slova, jako je například "the" nebo "a" - a čísla, speciální znaky, duplicitní znaků, e-mailové adresy a adresy URL. Jsme také převést text na malá písmena, lemmatize slova a zjišťovat hranice věty, které pak jsou označeny "|||" symbol předběžného zpracování textu.

![Předběžné zpracování textu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co když chcete použít vlastní seznam stopword? Můžete ho předat jako volitelný vstup. Můžete použít také vlastní C# syntaxi regulárního výrazu nahrazení podřetězců a odebrat slova slovní: podstatná jména, operace nebo přídavných jmen.

Po dokončení předzpracování jsme rozdělit data do trénování a testování sad.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Extrahovat vektory číselné funkce z předběžného zpracování textu
Vytvoříte model pro textová data, budete muset obvykle převést číselné funkce vektory textu volného tvaru. V tomto příkladu používáme [extrahovat N-gramy funkce z textu](https://msdn.microsoft.com/library/azure/mt762916.aspx) modulu pro transformaci textových dat do těchto formátu. Tento modul přebírá sloupce prázdné znaky oddělený slov a vypočítá slovník slov nebo N-gramy slov, které se zobrazí ve vaší datové sadě. Pak určí, kolik času každé slovo nebo N-gramy, zobrazí se všechny záznamy a vytvoří funkci vektory od těch, které se počítá. V tomto kurzu nastavíme N-gramy velikost na 2, proto naši funkci vektory zahrnout jednotlivá slova a kombinací dvou nebo více následujících slov.

![Extrahovat N-gramy](./media/text-analytics-module-tutorial/extract-ngrams.png)

Můžeme použít TF * počítá vážení N-gramy IDF (termín frekvence inverzní dokumentu frekvence). Tento přístup přidá váhu slova, která se zobrazí v jednom záznamu často ale se vyskytují jen vzácně napříč celou datovou sadu. Další možnosti patří binary, TF a grafy na miskách vah:.

Funkce text mají často vysokou dimenzionalitu. Například pokud vaše souhrnu má 100 000 jedinečných slov, prostor vaší funkce má 100 000 dimenze nebo více pokud N-gramy používají. Modul extrahovat funkce N-gramy poskytuje sadu možností ke snížení dimenzionalitě. Můžete vyloučit slova, která jsou kratší nebo long, nebo příliš neobvyklé nebo moc často má významné novinky prediktivní. V tomto kurzu vylučujeme N-gramy, které se zobrazují v méně než 5 záznamů nebo ve víc než 80 % záznamů.

Navíc můžete použít výběr součástí vybrat pouze ty funkce, které jsou nejvíce korelované s vaší cílovou předpovědi. Výběr funkce rozdělení chí-používáme vyberte 1000 funkce. Slovník vybraný text nebo N-gramy zobrazíte kliknutím pravého výstup modulu extrakce N-gramy.

Jako alternativní způsob použití funkcí extrahovat N-gramy můžete použít funkce algoritmu hash modulu. Pamatujte ale, že [Hashování](https://msdn.microsoft.com/library/azure/dn906018.aspx) nemá žádné možnosti výběru sestavení v funkce nebo TF * na miskách vah: IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: Klasifikační nebo regresní model trénovat na základě modelu
Nyní text transformaci na číselné sloupce. Datovou sadu stále obsahuje řetězec sloupce z předchozích fází, takže použijeme výběr sloupců v datové sadě je z nich vyloučit.

Potom použijeme [Two-Class logistické regrese](https://msdn.microsoft.com/library/azure/dn905994.aspx) předpovědět Naším cílem: vysoká nebo Nízká revize skóre. V tuto chvíli problém text analytics transformaci do regulární klasifikace problému. Můžete použít nástroje, které jsou k dispozici ve službě Azure Machine Learning se model vylepšit. Například můžete experimentovat s jinou třídění a zjistěte, jak přesné výsledky poskytují, nebo použít hyperparameter ladění, aby se zlepšila přesnost.

![Trénování a skóre](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: Stanovení skóre a ověření modelu
Jak byste měli ověřit trénovaného modelu? Jsme skóre datové sadě testů a vyhodnotit jeho přesnost. Ale model zjistili slovník N-gramy a jejich váhy z trénovací datové sady. Proto jsme měli používat tohoto slovníku a tyto váhy při extrahování funkce z testovací data, na rozdíl od vytvoření slovníku poruše. Proto jsme přidat modul extrahovat N-gramy funkce bodování větve experiment, připojit slovník výstup z větve školení a nastavte režim slovník jen pro čtení. Můžeme také zakázat filtrování N-gramy frekvencí nastavením minimální 1 instance a maximálně 100 % a vypnout nastavení výběr funkce.

Po transformaci textového sloupce v testovacích dat na číselné funkce sloupce, vylučujeme řetězcové sloupce z předchozích fází, jako jsou ve větvi školení. Potom použijeme modulu Evaluate Model vyhodnotit jeho přesnost a modul určení skóre modelu k následné predikci.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5: Nasazení modelu do produkčního prostředí
Model je téměř připraveni k nasazení do produkčního prostředí. Po nasazení jako webové služby, přebírá řetězec textu volného tvaru jako vstup a vrátí predikcí "vysoká" nebo "nízká". Používá zjištěná N-gramy slovník pro transformaci textu k funkcím a logistické natrénovaný model k předpovědím na základě těchto funkcí. 

Pokud chcete nastavit prediktivní experiment, nejprve uložíme slovník N-gramy jako datovou sadu a logistické natrénovaný model z větve školení experimentu. Uložte jsme experiment použití "Uložit jako" pro vytvoření s grafem experimentu pro prediktivní experiment. Odebereme modulu rozdělení dat a školení větev z experimentu. Pak připojíme dříve uložený slovník N-gramy a modelu k extrakci funkce N-gramy a určení skóre modelu modulů, v uvedeném pořadí. Můžeme také odebrat modulu Evaluate Model.

Jsme vložit výběr sloupců v datové sadě modulu před modul předzpracování textu odebrat popisek sloupce a zrušte zaškrtnutí možnosti "Skóre sloupec připojit k datové sadě" v modulu skóre. Tímto způsobem webová služba neuvede v požadavku popisku, který se pokouší předvídat a fakturuje se u není echo vstupu funkce v odpovědi.

![Prediktivní Experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Teď máme experiment, který můžete publikovat jako webovou službu a volat pomocí typu žádost odpověď nebo dávkové zpracování rozhraní API.

## <a name="next-steps"></a>Další kroky
Další informace o moduly analýzy textu z [dokumentaci MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

