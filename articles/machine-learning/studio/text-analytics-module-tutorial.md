---
title: Vytvoření textu analýzy v nástroji Azure Machine Learning Studio | Microsoft Docs
description: Postup vytvoření textu analýzy v Azure Machine Learning Studio pomocí moduly pro předzpracování text, N-gram nebo hashování
services: machine-learning
documentationcenter: ''
author: rastala
ms.author: roastala
manager: cgronlun
editor: ''
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: 6a89d77c84f7a7dd15b9d73252ac7373a7f1faf9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Vytvoření modelů analýzy textu v nástroji Azure Machine Learning Studio
Azure Machine Learning můžete vytvářet a zprovoznit modely analytics text. Tyto modely vám může pomoct vyřešit, například problémy analýzy dokumentu klasifikace nebo postojích.

V analýzy text experimentu byste obvykle:

1. Vyčistěte a předběžně zpracovat datovou sadu textu
2. Extrahování vektory číselné funkce z předem zpracovaných textu
3. Train klasifikační nebo regresní model
4. Stanovení skóre a ověření modelu
5. Model nasadit do produkčního prostředí

V tomto kurzu zjistíte podle jsme provede model analysis postojích pomocí Amazon kniha recenze datovou sadu, tyto kroky (najdete v tomto dokumentu research "biografie, Bollywood, výložníku polí a Blenders: přizpůsobení a domény pro klasifikaci postojích" Jan Blitzer, označit Dredze a Fernando Pereira; Přidružení výpočetní Linguistics (ACL), 2007.) Tato datová sada se skládá z Zkontrolujte skóre (1 – 2 nebo 4 až 5) a vlastní text. Cílem je k předvídání Zkontrolujte skóre: nízkou (1 - 2) nebo vysokou (4 až 5).

Můžete najít experimenty, které jsou popsané v tomto kurzu v Azure AI Gallery:

[Předpověď recenze adresáře](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Předpověď kniha recenze - prediktivní Experiment](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Krok 1: Vyčištění a předběžně zpracovat datovou sadu textu
Začneme experimentu vydělením skóre zkontrolujte do kategorií nízkou a vysokou kbelíků formulace problému jako dvě třídy klasifikace. Používáme [upravit Metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) a [skupiny kategorií hodnoty](https://msdn.microsoft.com/library/azure/dn906014.aspx) moduly.

![Vytvoření popisku](./media/text-analytics-module-tutorial/create-label.png)

Potom jsme vyčistit text pomocí [předběžně zpracovat Text](https://msdn.microsoft.com/library/azure/mt762915.aspx) modulu. Čištění snižuje šumu v datové sadě, pomáhají najít nejdůležitějších funkcích a zvyšte tak přesnost konečné modelu. Jsme odebrat stopslova - běžná slova, jako je například "na" nebo "a" - a čísla, speciální znaky, znaky duplicitní, e-mailové adresy a adresy URL. Jsme také převést text na malá písmena, lemmatize slova a zjišťovat hranice věty, které jsou pak indikován "|||" symbol v předem zpracovaných text.

![Předběžné zpracování textu](./media/text-analytics-module-tutorial/preprocess-text.png)

Co dělat, pokud chcete použít vlastní seznam stopslova? Můžete ho v předat jako volitelné vstup. Můžete také použít vlastní C# syntaxe regulární výraz k nahrazení dílčích řetězců a odeberte slova slovní: podstatná jména, operace nebo přídavných jmen.

Po dokončení předběžném zpracování jsme rozdělení dat do vlaku a testování sad.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Krok 2: Extrahování vektory číselné funkce z předem zpracovaných textu
Pokud chcete vytvořit model pro textová data, máte obvykle převést textové vektory číselné funkce. V tomto příkladu používáme [funkce N-Gram extrahovat z textu](https://msdn.microsoft.com/library/azure/mt762916.aspx) modulu pro transformaci dat text na takový formát. Tento modul trvá sloupec oddělené prázdný znak slova a vypočítá slovník výrazů nebo N-gram slov, které se zobrazují v datovou sadu. Potom udává, kolik časy každé aplikace word nebo N-gram, se zobrazí všechny záznamy a vytvoří funkci vektory od těch, počty. V tomto kurzu jsme nastavené velikost N-gram na 2, takže naše vektory funkce zahrnují jednoho slova a kombinací dvou dalších slov.

![Extrahování N-gram](./media/text-analytics-module-tutorial/extract-ngrams.png)

Použijeme TF * počty IDF (termín frekvence inverzní dokumentu frekvenci) vážení do N-gram. Tento postup přidá váhu slova, která zobrazí často v jeden záznam, ale vyskytují jen vzácně napříč celou datovou sadu. Další možnosti zahrnují binární TF a graf vážení.

Funkce text mají často vysokou dimenzionalitu. Například pokud vaše svátek má 100 000 jedinečných slov, váš prostor funkce by mít dimenze 100 000 nebo více pokud používají N-gram. Modul extrahovat funkce N-Gram poskytuje sadu možností ke snížení dimenzionalitě. Můžete vyloučit slova, která krátký nebo dlouhý, nebo příliš neobvyklé nebo příliš časté významné prediktivní hodnotou. V tomto kurzu jsme vyloučit N-gram, které se zobrazují v méně než 5 záznamy nebo ve víc než 80 % záznamy.

Navíc můžete výběr funkce Vybrat jenom ty funkce, které jsou nejvíce korelační s cílem vaší předpovědi. Výběr chí-kvadrát funkce používáme k výběru funkcí 1000. Slovník vybrané slova nebo N-gram můžete zobrazit kliknutím pravého výstup modulu extrakce N-gram.

Jako alternativní způsob používání funkcí N-Gram extrahovat můžete použít modul funkce algoritmu hash. Všimněte si ale, že [Hashování](https://msdn.microsoft.com/library/azure/dn906018.aspx) nemá sestavení ve funkci Výběr možnosti nebo TF * IDF vážení.

## <a name="step-3-train-classification-or-regression-model"></a>Krok 3: Train klasifikační nebo regresní model
Text má nyní převedeny na funkce číselné sloupce. Datovou sadu stále obsahuje řetězec sloupců z předchozí fázích, proto používáme výběr sloupců v datové sadě je z nich vyloučit.

Potom pomocí [Logistic Regression Two-Class](https://msdn.microsoft.com/library/azure/dn905994.aspx) k předvídání naše cíl: vysoká nebo Nízká Zkontrolujte skóre. V tomto okamžiku problém analytics text transformaci do regulární klasifikace problému. Můžete nástrojů dostupných v Azure Machine Learning se model vylepšit. Například můžete experimentovat s jinou třídění a zjistěte, jak přesné výsledky uvedou nebo použít hyperparameter ladění k zajištění přesnosti.

![Train a skóre](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Krok 4: Stanovení skóre a ověření modelu
Jak by ověření pro cvičný model? Jsme skóre podle datové sady testů a vyhodnotit jeho přesnost. Model ale zjistili termínů N-gram a jejich váhu z trénovací datové sady. Proto jsme měli používat tuto termínů a tyto váhu při extrahování funkce z testovací data a vytváření slovníku znovu. Proto jsme přidat modul extrahovat N-Gram funkce pro výpočet skóre větev experiment, připojit termínů výstup z větve školení a nastavit režim slovník jen pro čtení. Můžeme také zakázat filtrování N-gram frekvenci nastavením minimální na 1 instance a maximální na 100 % a vypnout nastavení výběr funkce.

Po transformaci textového sloupce v testovací data na číselné funkce sloupce, jsme z předchozích fází jako v školení větvi vyloučit řetězcové sloupce. Potom používáme modulu Evaluate Model vyhodnotit jeho přesnost a modul určení skóre modelu provádět předpovědi.

## <a name="step-5-deploy-the-model-to-production"></a>Krok 5: Model nasadit do produkčního prostředí
Model je téměř připravená k nasazení do produkčního prostředí. Při nasazení jako webové služby, přebírá textové řetězce jako vstup a vrátí předpovědi "vysoká" nebo "nízká". Zjištěné termínů N-gram používá k transformaci text, který má funkce a vyškolení logistic regresní model k předpovědím na tyto funkce. 

Pokud chcete nastavit prediktivní experiment, nejprve uložíme termínů N-gram jako datové sady a vyškolení logistic regresní model z větve školení experimentu. Potom jsme uložit experimentu pomocí "Uložit jako" k vytvoření grafu experiment prediktivní experimentu. Jsme odebrat modul rozdělení dat a školení větev z experimentu. Jsme se potom připojují dříve uloženou N-gram termínů a modelu k funkcím N-Gram extrahovat a Score Model modulů, v uvedeném pořadí. Můžeme také odebrat modulu Evaluate Model.

Nemůžeme vložit výběr sloupců v datové sadě modulu před modulu předběžně zpracovat Text odebrat popisek sloupce a zrušte výběr možnost "Sloupec skóre připojení k datové sadě" v modulu skóre. Tímto způsobem, webovou službu neuvede v požadavku popiskem, který se pokouší předvídat a nemá není echo vstup funkce v odpovědi.

![Prediktivní Experiment](./media/text-analytics-module-tutorial/predictive-text.png)

Máme teď experimentu, který umožňuje publikovat jako webovou službu a volat pomocí požadavků a odpovědí nebo dávkového spuštění rozhraní API.

## <a name="next-steps"></a>Další kroky
Další informace o modulech analytics text z [dokumentace MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

