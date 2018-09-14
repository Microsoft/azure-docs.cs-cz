---
title: Twitter klasifikaci subjektivního hodnocení s Azure Machine Learning (AML) balíčku pro analýzu textu (AMLPTA) a vědecké zpracování týmových dat (TDSP) | Dokumentace Microsoftu
description: Popisuje použití TDSP (vědecké zpracování týmových dat) a AMLPTA pro klasifikaci subjektivního hodnocení
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
ms.openlocfilehash: 9e5018bc4c7b90897f7f8c91169410284217b172
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577000"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Twitter klasifikaci subjektivního hodnocení s Azure Machine Learning (AML) balíčku pro analýzu textu (AMLPTA) a vědecké zpracování týmových dat (TDSP)

## <a name="introduction"></a>Úvod
Normalizaci struktury a dokumentace pro datovou vědu projekty, který je ukotven zavedené [životního cyklu datové vědy](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), je klíčem k umožňuje efektivní spolupráci v týmech datové vědy.

Jsme už vydali [úložiště GitHub pro šablony a struktura projektu TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Zavedli jsme teď vytváření projektů Azure Machine Learning, které jsou vytvořeny pomocí [TDSP strukturu a dokumentace ke službě šablon pro Azure Machine Learning](https://github.com/amlsamples/tdsp). Pokyny týkající se použití TDSP strukturu a šablon ve službě Azure Machine Learning [tady](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

V tomto příkladu budeme ukazují použití Azure Machine Learning balíčku pro analýzu textu a TDSP vyvíjet a nasazovat prediktivní modely pro klasifikaci subjektivního hodnocení Twitter.


## <a name="use-case"></a>Případ použití
### <a name="twitter-sentiment-polarity-sample"></a>Ukázka polarita subjektivního hodnocení na twitteru
Tento článek provádí ukázky ukazují, jak vytvořit instanci a spuštění projektu Machine learningu. Ukázka používá strukturu TDSP a šablon v Azure Machine Learning Workbench. Úplnou ukázku najdete v tomto názorném postupu. Úloha modelování předpovídá polarita mínění (kladná nebo záporná) pomocí textu z tweety. Tento článek popisuje úkoly modelování dat, které jsou popsané v tomto návodu. Návod zabývá se následujícími úlohami:

1. Zkoumání dat, školení a zavádění model strojového učení, které řeší problém predikcí, který je popsaný v přehledu případu použití. Data subjektivního hodnocení na twitteru se používá pro tyto úlohy.
2. Spuštění projektu pomocí šablony TDSP ze služby Azure Machine Learning pro tento projekt. Pro spuštění projektu a vytváření sestav se používá TDSP životního cyklu.
3. Operacionalizace řešení přímo ze služby Azure Machine Learning v Azure Container Service.

Projekt zvýrazní použít balíček Text Analytics pro Azure Machine Learning.


## <a name="link-to-github-repository"></a>Propojit úložiště GitHub
Odkaz na úložiště GitHub je [tady](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Účel
Primárním účelem této ukázky je ukazují, jak vytvořit instanci a provádění strojového učení pomocí struktury vědecké zpracování týmových dat (TDSP) a šablon v Azure Machine Learning pracovní Bench projektu. V tomto případě používáme [subjektivního hodnocení na Twitteru data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). Modelování úkolu je možnost předvídat polarita mínění (kladná nebo záporná) pomocí textu z tweety.

### <a name="scope"></a>Rozsah
- Zkoumání dat, školení a nasazení modelu strojového učení, které řešení problému předpovědi je popsáno v přehledu případu použití.
- Spuštění projektu ve službě Azure Machine Learning pomocí šablony vědecké zpracování týmových dat (TDSP) ze služby Azure Machine Learning pro tento projekt. Pro spuštění projektu a vytváření sestav budeme používat TDSP životního cyklu.
- Operacionalizace řešení přímo ze služby Azure Machine Learning v Azure Container Service.

Projekt zvýrazní několik funkcí služby Azure Machine Learning, takový vytvoření instance struktury TDSP a použití, provádění kódu v Azure Machine Learning pracovní Bench a snadno operacionalizace v Dockeru a Kubernetes pomocí služby Azure Container Service.

## <a name="team-data-science-process-tds"></a>Vědecké zpracování týmových dat (TDS)
Používáme šablony projektu TDSP strukturu a dokumentaci ke spuštění této ukázky. Vyplývá, [životního cyklu TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). Vytvoření projektu podle pokynů uvedených [tady](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Přehled případu použití
Úloha je předpovědět každý twitteru mínění binární polarita pomocí vkládání funkcí aplikace word extrahovat z twitteru textu. Podrobný popis najdete to [úložiště](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Získávání a pochopení dat](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Prvním krokem v této ukázce je stáhnout sentiment140 datovou sadu a jeho rozdělení na trénování a testování datových sad. Sentiment140 datová sada obsahuje skutečný obsah tweetu (s emotikony odebrána) spolu s polarita každého tweetu (negativní = 0, kladné = 4), pomocí neutrální tweety, které jsou odebrány. Výsledný trénovacích dat má 1.3 milionů řádků a testování dat má 320 tisíc řádků.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modelování](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Tato část ukázky se dál dělí na tři subparts: 
- **Konstrukce funkcí** odpovídá generování funkcí pomocí vkládání algoritmus, konkrétně Word2Vec různá slova. 
- **Model vytváření** zakázek díky školení různých modelů, jako jsou _logistické regrese_ a _přechodu zvýšení skóre_ předpovědět subjektivního hodnocení vstupního textu. 
- **Model hodnocení** platí trénovaného modelu přes testovací data.

#### <a name="feature-engineering"></a>Návrh funkcí
Používáme <b>Word2Vec</b> ke generování vkládání slov. Nejprve můžeme používat algoritmus Word2Vec v režimu Skipgram jak je vysvětleno v knize [Mikolov Tomáši, et al. Distribuované reprezentace slov a vět a jejich compositionality. Pokročilé možnosti neuronových sítí informace systémy pro zpracování. 2013.](https://arxiv.org/abs/1310.4546) ke generování vkládání slov.

Přeskočit gram je bez podstruktury neuronové sítě trvá cílového slova zakódován jako jeden aktivní vektor jako vstup a použít ho k předvídání okolních slova. Pokud V velikost slovníku, pak by se velikost vrstvy výstup __C * V__ kde C je velikost okna kontextu. Přeskočit gram na základě architektury se zobrazí na následujícím obrázku.
 
<table class="image" align="center">
<caption align="bottom">Přeskočit gram modelu</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Podrobnosti o Word2Vec algoritmus a přeskočit gram modelu jsou nad rámec této ukázky a chtějí čtenáři jsou požadovány na následující odkazy pro další podrobnosti. Kód 02_A_Word2Vec.py odkazuje [tensorflow příklady](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Vektor reprezentace slova](https://www.tensorflow.org/tutorials/word2vec)
* [Jak přesně word2vec funguje?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Poznámky k odhadu Contrastive šumu a záporná vzorkování](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

Po dokončení procesu trénování dva vnoření soubory ve formátu TSV jsou generovány pro fázi modelování.

#### <a name="model-training"></a>Cvičení modelu
Jakmile vektory slovo byly vytvořeny pomocí algoritmu SSWE nebo Word2vec, dalším krokem je k trénování modelů klasifikace k předpovědi polarita skutečné mínění. Můžeme použít dva typy funkcí: Word2Vec a SSWE do dvou modelů: Logistický regresní a Konvoluční Neuronové sítě (CNN). 

#### <a name="model-evaluation"></a>Vyhodnocení modelu
Poskytujeme kód o tom, jak načíst a vyhodnotit více trénované modely na testovací datové sady.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Nasazení](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Tato část můžeme poskytnout odkazy na pokyny o tom, jak zprovoznit prediktivní model předem vytrénovaných subjektivního hodnocení na webovou službu v clusteru v Azure Container Service (AKS). Operacionalizace vývojového a testovacího prostředí Docker a Kubernetes v clusteru pro správu nasazení webové služby. Další informace najdete v procesu operacionalizace [tady](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Závěr
Jsme provedli podrobnosti o tom, jak trénování vkládání model aplikace word s použitím Word2Vec a pak použít extrahované vkládání jako funkce k trénování dva různé modely a vytvořte předpověď skóre mínění Twitteru textová data. Jeden z těchto modelech nasazení ve službě Azure Container Services (AKS). 

## <a name="next-steps"></a>Další postup
Přečtěte si další dokumentaci o [Azure Machine Learning balíčku pro analýzu textu (AMLPTA)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) a [vědecké zpracování týmových dat (TDSP)](https://aka.ms/tdsp) začít.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Jak používat vědecké zpracování týmových dat (TDSP) ve službě Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Šablona projektu TDSP pro Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure ML Work Bench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov Tomáši, a kolektiv autorů Distribuované reprezentace slov a vět a jejich compositionality. Pokročilé možnosti neuronových sítí informace systémy pro zpracování. 2013.](https://arxiv.org/abs/1310.4546)
