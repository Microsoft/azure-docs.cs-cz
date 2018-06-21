---
title: Twitter postojích klasifikaci s Azure Machine Learning (AML) balíčku pro Analýza textu (AMLPTA) a Team datové vědy procesu (TDSP) | Microsoft Docs
description: Popisuje použití TDSP (Team datové vědy proces) a AMLPTA pro klasifikaci postojích
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: 559af47bcf41cd6af59f8ba1b27ff8e64e849925
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296166"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Klasifikace postojích Twitter s Azure Machine Learning (AML) balíčku pro Analýza textu (AMLPTA) a Team datové vědy procesu (TDSP)

## <a name="introduction"></a>Úvod
Standardizace struktury a vědecké zpracování dat naleznete v dokumentaci projekty, který je ukotven zavedených [životního cyklu vědecké účely data](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), je klíčová pro usnadnění efektivní spolupráce v datové vědy týmy.

Jsme dřív vydané [úložiště GitHub pro strukturu TDSP projektů a šablon](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Nyní jsme povolili vytváření projektů Azure Machine Learning, které jsou vytvářeny instance s [TDSP strukturu a dokumentaci šablon pro Azure Machine Learning](https://github.com/amlsamples/tdsp). Poskytuje pokyny, jak používat TDSP struktura a šablon v Azure Machine Learning [zde](https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

V této ukázce jsme se chystáte ukazují použití Azure Machine Learning balíček pro Analýza textu a TDSP k vývoji a nasazovat prediktivní modely pro klasifikaci postojích Twitter.


## <a name="use-case"></a>Případ použití
### <a name="twitter-sentiment-polarity-sample"></a>Ukázka polarita postojích služby Twitter.
Tento článek používá ukázku k ukazují, jak vytvořit a spustit projekt Machine Learning. Příklad používá TDSP strukturu a šablon v nástroji Azure Machine Learning Workbench. Tento názorný postup je součástí je kompletní ukázka. Úloha modelování předpovídá postojích polarita (kladné a záporné) pomocí textu z tweetů. Tento článek popisuje úlohy modelování dat, které jsou popsané v tomto návodu. Průvodce obsahuje následující úlohy:

1. Zkoumání dat, školení a nasazení strojového učení modelu a které se týkají předpovědi problém, který je popsaný v části Přehled případů použití. Twitter postojích data se používají pro tyto úlohy.
2. Spuštění projektu pomocí šablony TDSP z Azure Machine Learning pro tento projekt. Pro spuštění projektu a vytváření sestav se používá TDSP životního cyklu.
3. Operationalization řešení přímo z Azure Machine Learning v Azure Container Service.

Projekt klade důraz na použití balíčku Analytics Text pro Azure Machine Learning.


## <a name="link-to-github-repository"></a>Propojit s úložišti GitHub
Odkaz na úložiště GitHub je [zde](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Účel
Primárním účelem Tato ukázka je ukazují, jak vytvořit instanci a provedení machine learning projektu v Azure Machine Learning pracovní Bench struktura tým datové vědy procesu (TDSP) a šablony. Pro tento účel využijeme [Twitter postojích data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). Tato úloha modelování je k předvídání postojích polarita (kladné a záporné) pomocí textu z tweetů.

### <a name="scope"></a>Rozsah
- Zkoumání dat, školení a nasazení modelu strojového učení, které adres předpovědi problém popsaný v části Přehled případ použití.
- Spuštění projektu v Azure Machine Learning pomocí šablony tým datové vědy procesu (TDSP) z Azure Machine Learning pro tento projekt. Pro spuštění projektu a vytváření sestav vytvoříme použít TDSP životního cyklu.
- Operationalization řešení přímo z Azure Machine Learning v kontejneru služby Azure.

Projekt označuje některé funkce služby Azure Machine Learning, takové TDSP struktura vytváření instancí a použití, spouštění kódu v Azure Machine Learning pracovní Bench a snadno operationalization v kontejneru služby Azure pomocí Docker a Kubernetes.

## <a name="team-data-science-process-tds"></a>Proces vědecké účely dat Team (TDS)
K provedení této ukázce používáme šablony projektu TDSP strukturu a dokumentaci. Postupuje [životního cyklu TDSP](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/lifecycle). Vytvoření projektu podle pokynů k [zde](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Přehled případu použití
Tato úloha je k předvídání každý twitter postojích binární polarita pomocí funkce aplikace word vkládaných extrahovat z textu twitter. Podrobný popis naleznete to [úložiště](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Získávání dat a principy](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Prvním krokem v této ukázce je ke stažení sentiment140 datovou sadu a rozdělení do vlaku a testovací datové sady. Datová sada Sentiment140 obsahuje skutečný obsah tweet (s emotikony odebrat) společně s polarita jednotlivých tweet (záporné = 0, kladné = 4) taky s neutrálním tweetů odebrat. Výsledná data školení má 1.3 milionu řádků a testování dat má řádky 320 kB.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modelování](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Tato část vzorku se dále dělí do tří subparts: 
- **Konstruování** odpovídá generování funkcí s použitím různých word vložení algoritmus, konkrétně Word2Vec. 
- **Model vytváření** obchody se školení odlišnými modely jako _logistic regression_ a _přechodu zvyšovat skóre_ k předvídání postojích ze vstupního textu. 
- **Model vyhodnocení** používá pro cvičný model přes testování data.

#### <a name="feature-engineering"></a>Návrh funkcí
Používáme <b>Word2Vec</b> ke generování vkládaných aplikace word. Nejprve používáme algoritmus Word2Vec v režimu Skipgram jak je popsáno v papíru [Mikolov Tomáši, a další. Distribuované reprezentace slova a slovní spojení a jejich compositionality. Pokroky v neuronové informace systémy pro zpracování. 2013.](https://arxiv.org/abs/1310.4546) ke generování vkládaných aplikace word.

Přeskočit gram je bez podstruktury neuronové sítě trvá cílového slova kódovaná jako jeden aktivní vektoru jako vstup a použití k předvídání nedaleko slova. Pokud je velikost slovníku V, pak by být velikost vrstvy výstup __C * V__ kde C je velikost okna kontextu. Architektura přeskočit gram na základě je vidět na následujícím obrázku.
 
<table class="image" align="center">
<caption align="bottom">Přeskočit gram modelu</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Podrobnosti o Word2Vec algoritmus a přeskočit gram modelu jsou nad rámec této ukázky a také zajímat, čteček požaduje, aby projít následující odkazy pro další podrobnosti. 02_A_Word2Vec.py kódu odkazovat [tensorflow příklady](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Vektor reprezentace slova](https://www.tensorflow.org/tutorials/word2vec)
* [Jak přesně word2vec funguje?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Poznámky k Contrastive odhad šumu a záporná vzorkování](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

Po dokončení procesu školení dva vnoření soubory ve formátu TSV jsou generovány pro modelování fázi.

#### <a name="model-training"></a>model školení
Jakmile vektory word byly generovány pomocí algoritmu SSWE nebo Word2vec, dalším krokem je ke cvičení klasifikační modely k předvídání polarita skutečné postojích. Jsme použít dva typy funkcí: Word2Vec a SSWE do dvou modelů: Logistic regression a Convolutional Neuronové sítě (CNN). 

#### <a name="model-evaluation"></a>Vyhodnocení modelu
Poskytujeme kód o tom, jak načíst a vyhodnocovat u nich více trénované modely na testovací datové sady.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Nasazení](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Tato část můžeme poskytnout odkazy na pokyny o tom, jak zprovoznit model předem vyškolení postojích předpovědi k webové službě na cluster v kontejneru služby Azure (AKS). Prostředí operationalization zřídí Docker a Kubernetes v clusteru pro správu nasazení webové služby. Můžete najít další informace o procesu operationalization [zde](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Závěr
Potřebujeme prostřednictvím podrobnosti o tom, jak cvičení word vnoření model pomocí Word2Vec a pak použít extrahované vkládaných jako funkce ke cvičení dva různé modely k předvídání postojích skóre Twitter textová data. Jeden z těchto modelů je nasazena v Azure kontejneru služby (AKS). 

## <a name="next-steps"></a>Další postup
Přečtěte si další dokumentaci na [Azure Machine Learning balíčku pro analýzy Text (AMLPTA)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) a [tým datové vědy procesu (TDSP)](https://aka.ms/tdsp) začít pracovat.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Jak používat tým datové vědy procesu (TDSP) v Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Šablona projektu TDSP pro Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure ML pracovní Bench](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [Mikolov Tomáši, a další. Distribuované reprezentace slova a slovní spojení a jejich compositionality. Pokroky v neuronové informace systémy pro zpracování. 2013.](https://arxiv.org/abs/1310.4546)
