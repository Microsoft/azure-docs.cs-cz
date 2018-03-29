---
title: Předpověď sentimentu Twitter s word vkládaných pomocí procesu vědecké účely Team dat v Azure | Microsoft Docs
description: Kroky, které jsou nutné k provedení vědecké zpracování dat projekty.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev
ms.openlocfilehash: f22da892868a10ac18fdcd703249eaa172f8bf65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Předpověď sentimentu Twitter s word vkládaných pomocí procesu Team dat vědecké účely

V tomto článku se dozvíte, jak efektivně spolupracovat s použitím _Word2Vec_ word vložení algoritmus a _postojích konkrétní aplikace Word vložení (SSWE)_ použitý algoritmus předpovědi sentimentu Twitter s [Azure Machine Learning](../preview/index.yml). Další informace o predikci polarita postojích Twitter, najdete v článku [MachineLearningSamples TwitterSentimentPrediction úložiště](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) na Githubu. Klíč k usnadnění efektivní Týmová spolupráce v projektech vědecké zpracování dat je standardizovat strukturu a projektů s životního cyklu zavedených vědecké zpracování dat naleznete v dokumentaci. [Tým datové vědy procesu (TDSP)](overview.md) poskytuje tento typ strukturovaných [životního cyklu](lifecycle.md). 

Vytváření projektů vědecké zpracování dat pomocí _TDSP šablony_ poskytuje standardizovaná architekturu pro projekty Azure Machine Learning. Dřív, týmem TDSP vydané [úložiště GitHub pro strukturu TDSP projektů a šablon](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Nyní Machine Learning projekty, které jsou vytvářeny instance s [TDSP šablon pro Azure Machine Learning](https://github.com/amlsamples/tdsp) jsou povoleny. Pokyny najdete v tématu Jak používat [TDSP struktura projektů pomocí šablony TDSP](../preview/how-to-use-tdsp-in-azure-ml.md) v Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Ukázka polarita postojích služby Twitter.

Tento článek používá ukázku k ukazují, jak vytvořit a spustit projekt Machine Learning. Příklad používá TDSP strukturu a šablon v nástroji Azure Machine Learning Workbench. Je součástí je kompletní ukázka [Tento názorný postup](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). Úloha modelování předpovídá postojích polarita (kladné a záporné) pomocí textu z tweetů. Tento článek popisuje úlohy modelování dat, které jsou popsané v tomto návodu. Průvodce obsahuje následující úlohy:

- Zkoumání dat, školení a nasazení strojového učení modelu a které se týkají předpovědi problém, který je popsaný v části Přehled případů použití. [Twitter postojích data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) se používá pro tyto úlohy.
- Spuštění projektu pomocí šablony TDSP z Azure Machine Learning pro tento projekt. Pro spuštění projektu a vytváření sestav se používá TDSP životního cyklu.
- Operationalization řešení přímo z Azure Machine Learning v Azure Container Service.

Projekt upozorňuje následující funkce Azure Machine Learning:

- Vytváření instancí a použití TDSP struktury.
- Spuštění kódu v Azure Machine Learning Workbench.
- Snadno operationalization v Container Service pomocí Docker a Kubernetes.

## <a name="team-data-science-process"></a>Vědecké zpracování týmových dat
K provedení této ukázce, použití šablon projektu TDSP struktura a dokumentace v nástroji Azure Machine Learning Workbench. Ukázka implementuje [životního cyklu TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), jak je znázorněno na následujícím obrázku:

![Životní cyklus TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

Vytvoření projektu TDSP v nástroji Azure Machine Learning Workbench na základě [tyto pokyny](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), jak je znázorněno na následujícím obrázku:

![Vytvoření TDSP v nástroji Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Příprava a získávání dat](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Prvním krokem v této ukázce je ke stažení sentiment140 datovou sadu a rozdělují data na trénování a testování datové sady. Datová sada sentiment140 obsahuje skutečný obsah tweet (s emotikony odebrat). Datová sada obsahuje také polarita z každé tweet (záporné = 0, kladné = 4) s neutrálním tweetů odebrat. Po dělení dat jsou Cvičná data má 1.3 milionu řádků a testovacích dat má 320,000 řádky.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Vývoj pro model](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Dalším krokem v ukázce je vyvinout model pro data. Úloha modelování je rozdělené do tří částí:

- Konstruování: generování funkce pro model pomocí různých word vložení algoritmy. 
- Model vytváření: cvičení odlišnými modely k předvídání postojích ze vstupního textu. Příklady těchto modelů _Logistic Regression_ a _přechodu zvyšovat skóre_.
- Model vyhodnocení: vyhodnocení trénované modely přes testování data.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Funkce inženýrství](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Algoritmy Word2Vec a SSWE se používají ke generování vkládaných aplikace word. 


#### <a name="word2vec-algorithm"></a>Algoritmus Word2Vec

Word2Vec algoritmus se používá v modelu přeskočit-Gram. Tento model je vysvětlené v papíru Tomáši Mikolov, a další. "[Distribuované reprezentace slova a slovní spojení a jejich Compositionality. Pokroky v neuronové informace systémy pro zpracování. ](https://arxiv.org/abs/1310.4546)" 2013.

Model Skip Gram je bez podstruktury neuronové sítě. Vstup je cílového slova, který je zakódován vektor horkou jeden, který se používá k předvídání nedaleko slova. Pokud **V** je velikost termínů, pak je velikost vrstvy výstup __C * V__ kde C je velikost okna kontextu. Následující obrázek znázorňuje architekturu, která je založen na modelu přeskočit-Gram:

![Přeskočit-Gram modelu](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Podrobné mechanismů Word2Vec algoritmus a přeskočit-Gram modelu jsou nad rámec této ukázky. Další informace najdete v tématu následující odkazy:

- [Kód 02_A_Word2Vec.PY s odkazované TensorFlow příklady](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vektor reprezentace slova](https://www.tensorflow.org/tutorials/word2vec)
- [Jak přesně word2vec funguje?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Poznámky k Contrastive odhad šumu a záporná vzorkování](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Algoritmus vložení postojích konkrétní aplikace Word
Algoritmus SSWE se pokusí překonat slabiny algoritmu Word2Vec, kde slova s podobnou kontexty nebo opačnou polarita může mít podobné vektory aplikace word. Podobnost může způsobit algoritmus Word2Vec tak, aby neprováděl přesně pro úlohy, jako je postojích analýzy. Algoritmus SSWE se pokusí zpracovat tato slabé místo začleněním polarita větu a jeho kontextu do jeho funkci ztrátu.

Ukázka používá hodnotu typu variant algoritmu SSWE následujícím způsobem:

- Původní _ngram_ a poškozená _ngram_ jsou použity jako vstupy.
- A řazení styl pantu ztrátě funkce se používá pro syntaktické ztrátě a sémantické ztráty.
- Funkci ztrátu ultimate je vyvážené kombinace syntaktické ztrátě a sémantické ztráty.
- Pro jednoduchost jenom sémantického křížové šifrování se používá jako funkci ztrátu.

Příklad ukazuje, že i s jednodušší funkci ztrátu, je lepší, než Word2Vec vložení výkon SSWE vložení. Následující obrázek znázorňuje convolutional model, který se používá ke generování vložení postojích konkrétní word:

![Modelu neuronové sítě vycházející SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Po dokončení procesu školení dva vnoření soubory ve formátu hodnoty oddělené tabulátory (TSV) jsou generovány pro modelování fázi.

Další informace o algoritmech SSWE, najdete v dokumentu pomocí hmotný dlouhodobý Duyu, a další. "[Učení postojích konkrétní aplikace Word vložení pro klasifikaci postojích Twitter](http://www.aclweb.org/anthology/P14-1146)." Přidružení pro výpočetní Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Vytvoření modelu](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Po vygenerování vektory word pomocí algoritmu SSWE nebo Word2Vec klasifikační modely probíhá Trénink k předvídání polarita skutečné postojích. Dva typy funkcí: Word2Vec a SSWE, se použijí pro dva modely: model přechodu zvyšovat skóre a Logistic regresní model. V důsledku toho probíhá Trénink čtyř různých modelů.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Vyhodnocení modelu](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Po probíhá Trénink modely, modely slouží k testování služby Twitter textová data a vyhodnocení výkonu každý model. Ukázka vyhodnotí následující čtyři modely:

- Přechodu Boosting přes SSWE vložení.
- Logistic Regression přes SSWE vložení.
- Přechodu Boosting přes Word2Vec vložení.
- Logistic Regression přes Word2Vec vložení.

Porovnání výkonu čtyři modely je znázorněno na následujícím obrázku:

![Příjemce operační charakteristik porovnání modelů (ROC)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Model přechodu zvyšovat skóre s funkcí SSWE poskytuje nejlepší výkon při porovnávání vzorů pomocí oblasti pod metrika křivky (AUC).


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Nasazení](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Posledním krokem je nasazení modelu předpovědi vyškolení postojích k webové službě na cluster v Azure Container Service. Ukázka používá model přechodu zvyšovat skóre s algoritmem vnoření SSWE jako naučeného modelu. Prostředí operationalization zřídí Docker a Kubernetes v clusteru pro správu nasazení webové služby, jak je znázorněno na následujícím obrázku: 

![Řídicí panel Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Další informace o procesu operationalization najdete v tématu [nasazení Azure Machine Learning model jako webovou službu](../preview/model-management-service-deploy.md).

## <a name="conclusion"></a>Závěr

V tomto článku jste zjistili, jak k natrénování modelu vložení aplikace word pomocí algoritmů Word2Vec a vkládání postojích konkrétní aplikace Word. Extrahované vkládaných používaly jako funkce ke cvičení několik modely k předvídání postojích skóre pro Twitter textová data. Funkci SSWE použita s modelem přechodu zvyšovat skóre přiřadil nejlepší výkon. Model se pak nasadí jako služba webu v reálném čase v kontejneru služby pomocí Azure Machine Learning Workbench.


## <a name="references"></a>Odkazy

* [Vědecké zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Jak používat tým datové vědy procesu (TDSP) v Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Šablony projektů TDSP pro Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../preview/index.yml)
* [USA příjem sady dat z úložiště UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)
* [Rozpoznávání biolékařského entity pomocí TDSP šablon](../preview/scenario-tdsp-biomedical-recognition.md)
* [Mikolov, Tomas, et al. "Distribuované reprezentace slova a slovní spojení a jejich Compositionality. Posune v neuronové informace systémy pro zpracování." 2013.](https://arxiv.org/abs/1310.4546)
* [Hmotný dlouhodobý, Duyu, a další. "Učení postojích konkrétní Word vložení pro klasifikaci postojích Twitter." ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
