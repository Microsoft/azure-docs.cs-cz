---
title: Rozpoznávání biolékařského entity - Team Data vědecké účely proces – Azure Machine Learning | Microsoft Docs
description: Proces vědecké účely dat Team projekt quickstart, využívající hloubkové learning pro rozpoznávání biolékařského entity v nástroji Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 421af12da6d207a50d12dcbf64aac51aba06b196
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Rozpoznávání biolékařského entity pomocí šablony tým datové vědy procesu (TDSP)

Extrakce entity je dílčí úlohy extrakce informace (také označované jako [rozpoznávání pojmenované entity (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), entita, rozdělování a identifikaci entity). Cílem této scénářem z reálného prostředí je zvýrazněte jak pomocí Azure Machine Learning Workbench řešit složité úlohy zpracování přirozeného jazyka (NLP) jako je extrakce entity z nestrukturovaných textu:

1. Jak ke cvičení neuronové slovo vložené části model, na souhrnu text z přehledů o 18 milionů PubMed pomocí [Spark Word2Vec implementace](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Jak vytvořit model hloubkové opakující neuronové sítě dlouho krátkodobé paměti (LSTM) pro extrakci entity na grafický procesor s podporou Azure dat vědecké účely virtuálního počítače (GPU DS VM) v Azure.
2. Ukazují, že tento model vkládaných specifické pro doménu aplikace word lze překonat modely vkládaných obecná slova v úloze rozpoznávání entity. 
3. Ukazují, jak pro trénování a zprovoznit hloubkové learning modelů pomocí Azure Machine Learning Workbench.

4. Ukazují následující možnosti v rámci Azure Machine Learning Workbench:

    * Vytváření instancí [struktura tým datové vědy procesu (TDSP) a šablony](how-to-use-tdsp-in-azure-ml.md)
    * Automatizovanou správu vaší závislosti projektu včetně stažení a instalace
    * Spouštění skriptů Python na různých výpočetních prostředí
    * Spustit sledování skriptů Python historie
    * Provádění úloh na vzdálené Spark výpočetní kontexty pomocí clusterů HDInsight Spark 2.1
    * Provádění úloh v vzdálené GPU virtuální počítače v Azure
    * Snadno operationalization hloubkové learning modely jako webové služby na kontejneru služby Azure (ACS)

## <a name="use-case-overview"></a>Přehled případu použití
Rozpoznávání biolékařského pojmenované entity, jako je důležitý krok pro komplexní biolékařského NLP úlohy: 
* Extrahování zmínky o pojmenované entity takové nákaz, drogy, chemikálií a příznaky z elektronické lékařské nebo stavu záznamů.
* Nedovolenému zjišťování
* Principy interakce mezi různé entity typy například interakce mezi léky, nedovolenému nákazy vztah a vztah gen bílkovin.

Naše scénář případu využití se zaměřuje na tom, jak lze analyzovat velké množství svátek nestrukturovaných dat, jako je například přehledů Medline PubMed ke cvičení slovo vložení modelu. Potom vkládaných výstupní jsou považovány za jako automaticky generované funkce pro učení neuronové entity Extraktor.

Naše výsledky ukazují, že školení modelu extrakce biolékařského entity na specifické pro doménu slovo vložení funkce překonává výkonem modelu trénink na typu Obecné funkce. Model specifické pro doménu, můžete zjistit 7012 entity správně (mimo 9475) s F1 – score 0.73 ve srovnání s 5274 entity s F1 – score 0,61 pro obecný model.

Následující obrázek znázorňuje architekturu, která byla použita pro zpracování dat a cvičení modely.

![Architektura](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Popis dat

### <a name="1-word2vec-model-training-data"></a>1. Word2Vec modelu Cvičná data
První, stáhli jsme nezpracovaná data MEDLINE abstraktní z [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Data jsou veřejně dostupné ve formě souborů XML na jejich [FTP server](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Na serveru nejsou k dispozici 892 souborů XML a všechny soubory XML obsahuje informace o 30 000 článků. Další informace o kroku shromažďování dat jsou uvedeny v části strukturu projektu. Jsou tato pole, které jsou součástí každého souboru 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. LSTM modelu Cvičná data

Extrakce modelu neuronové entity má byla vycvičena a vyhodnocené na veřejně dostupné datové sady. Pokud chcete získat podrobný popis o tyto datové sady, může najdete následující zdroje:
 * [Úloha rozpoznávání bio Entity v BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Úloha souhrnu BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 – úloha 9.1 (nedovolenému rozpoznávání)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Propojit s úložišti GitHub Galerie Azure
Toto je odkaz na veřejné úložiště GitHub reálného scénáře, který obsahuje kód a podrobnější popis: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Požadavky 

* Azure [předplatného](https://azure.microsoft.com/free/)
* Azure Machine Learning Workbench. V tématu [Průvodce instalací](../service/quickstart-installation.md). Aktuálně Azure Machine Learning Workbench lze nainstalovat do jenom následující operační systémy: 
    * Windows 10 nebo Windows Server 2016
    * systému macOS Sierra (nebo novější)


### <a name="azure-services"></a>Služby Azure
* [Cluster HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) verze Spark 2.1 v Linux (HDI 3.6) pro výpočty Škálováním na více systémů. Ke zpracování v plné výši MEDLINE přehledů popsané níže, je třeba minimální konfigurace: 
    * Hlavní uzel: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) velikost
    * Pracovní uzly: minimálně 4 v [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). V našem pracovní jsme použili 11 uzlů pracovního procesu D12_V2 velikosti.
* [NC6 datové vědy virtuálního počítače (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) pro výpočty škálování.

### <a name="python-packages"></a>Balíčky Python

Všechny požadované závislosti jsou definovány v souboru aml_config/conda_dependencies.yml ve složce projektu scénář. Závislosti definované v tomto souboru jsou automaticky zřízené pro spuštění před docker a virtuálních počítačů a HDI clusteru cíle. Podrobnosti o formátu souborů prostředí Conda [zde](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Základní pokyny pro Azure Machine Learning (AML) workbench
* [Přehled](../service/overview-what-is-azure-ml.md)
* [Instalace](../service/quickstart-installation.md)
* [Pomocí TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Tom, jak číst a zapisovat soubory](how-to-read-write-files.md)
* [Jak používat poznámkové bloky Jupyter](how-to-use-jupyter-notebooks.md)
* [Jak používat GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Struktura scénář
Scénář používáme TDSP struktura a dokumentace šablony projektu (obrázek 1), které odpovídá [životního cyklu TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Vytvoření projektu podle pokynů [zde](./how-to-use-tdsp-in-azure-ml.md).


![Zadejte informace o projektu](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Pracovní postup vědecké účely podrobné dat je následující:

### <a name="1-data-acquisition-and-understanding"></a>1. Získávání a pochopení dat

V tématu [Data pořízení a pochopení](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

Nezpracovaná svátek MEDLINE má celkem 27 milionů přehledů, kde přibližně 10 milionů články mají prázdné pole abstraktní. Azure HDInsight Spark se používá ke zpracování velkých objemů dat, která nemůže být načtena do paměti jako jeden počítač [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Data se nejprve stáhne do clusteru Spark. Potom tyto kroky jsou spouštěny na [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* analyzovat pomocí analyzátoru kódu XML Medline soubory XML
* předběžné zpracování abstraktní text včetně rozdělení věty, Tokenizace a případu normalizace.
* Vylučte články, kde abstraktní pole je prázdné nebo má krátký textový 
* Vytvoření aplikace word termínů z přehledu školení
* cvičení slovo vložení neuronové modelu. Další informace najdete v tématu [Githubu kód odkaz](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) začít pracovat.


Po analýze soubory XML, dat má následující formát: 

![Vzorek dat](./media/scenario-tdsp-biomedical-recognition/datasample.png)

Extrakce modelu neuronové entity má byla vycvičena a vyhodnocené na veřejně dostupné datové sady. Pokud chcete získat podrobný popis o tyto datové sady, může najdete následující zdroje:
 * [Úloha rozpoznávání bio Entity v BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Úloha souhrnu BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 – úloha 9.1 (nedovolenému rozpoznávání)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modelování

V tématu [modelování](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modelování je fázi, kde ukážeme, jak můžete použít data staženého v předchozí části pro trénování vlastní aplikace word vložení modelu a použít ji pro další podřízené úlohy. Přestože používáme PubMed data, kanál ke generování vložené části je obecný a lze opětovně použít ke cvičení slovo vložené části pro ostatních domén. Pro vkládaných být přesná reprezentace dat je nezbytné, že je word2vec trénink na velké množství dat.
Když máme připraven word vkládaných, jsme cvičení modelu hluboké neuronové sítě, který používá zjištěné vkládaných k chybě při inicializaci vrstvě vkládání objektů. Jsme označit vnoření vrstvě jako trainable, ale není povinná. Školení aplikace word vložení modelu je bez dohledu a proto jsme se využívat bez popisku texty. Školení rozpoznávání modelu entity jsou pod dohledem learning úloh však a jeho přesnost závisí na množství a kvalitu dat ručně poznámky. 


#### <a name="21-feature-generation"></a>2.1. Funkce generování

V tématu [funkci generování](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec je slovo vložení algoritmus učení bez dohledu, která provede modelu neuronové sítě z souhrnu bez popisku školení. Vyvolá průběžné vektor pro jednotlivých slov v souhrnu, který představuje jeho sémantické informace. Tyto modely jsou jednoduché neuronové sítě pomocí jednoho skryté vrstvě. Word vektory/vkládaných se naučili backpropagation a stochastického klesání přechodu. Existují dva typy word2vec modelů, konkrétně, přeskočit-Gram a průběžné kontejneru z – slova (zkontrolujte [sem](https://arxiv.org/pdf/1301.3781.pdf) podrobnosti). Vzhledem k tomu, že se používá MLlib implementace word2vec, podporujícího přeskočit gram modelu jsme Krátce popište ho tady (bitové kopie, které jsou převzaty z [odkaz](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Přeskočit Gram modelu](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

K optimalizaci výkonu používá model hierarchické Softmax a záporná vzorkování. Hierarchická SoftMax (H-SoftMax) je přibližný vycházející binárním stromům. H SoftMax v podstatě nahradí vrstvě ploché SoftMax hierarchické vrstva, která obsahuje slova, jako je opustí. To umožňuje nám rozložit výpočet pravděpodobnost jednoho slova do sekvenci výpočty pravděpodobnosti, která nám ušetří s vypočítat nákladné normalizaci přes všechna slova. Vzhledem k tomu, že má vyrovnáváním binárního stromu hloubku log2 (| V |) (V je slovníku), musíme vyhodnotit maximálně log2 (| V |) uzly k získání konečné pravděpodobnost slova. Pravděpodobnost word w, danou jeho kontextu c je pak jednoduše produkt pravděpodobností trvá vpravo a vlevo se změní v uvedeném pořadí které vedou k jeho uzel typu list. Můžeme sestavit Huffmanova stromu na základě četnosti slova v datové sadě Ujistěte se, že častější slova získat reprezentace kratší. Další informace najdete v tématu [tento odkaz](http://sebastianruder.com/word-embeddings-softmax/).
Bitové kopie, které jsou převzaty z [zde](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Vizualizace

Jakmile jsme vkládaných word, bychom rádi vizualizovat je a prohlédnout vztah mezi sémanticky podobné slova. 

![Podobnosti W2V](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Nám ukázaly, že dva různé způsoby vizualizace vložené části. První z nich používá PCA do projektu vysoké dimenzí vektoru s prostorem vektoru 2-D. To vede k významné ztrátě informací a vizualizaci není tak přesná. Druhý je použití PCA s [t Odborníka](https://distill.pub/2016/misread-tsne/). t Odborníka je technika snížení nelineární dimenzionalitu, která je vhodná pro vložení vysokou dimenzí dat do prostoru dvě nebo tři dimenzí, které lze následně vizualizovat v bodové vykreslení.  Každý objekt vysokou dimenzí ho modelů dva nebo three trojrozměrným bod tak, že podobné objekty jsou modelovat blízkým body a vzdáleným body jsou modelovat odlišných typů objektů. Funguje ve dvou částech. Nejprve vytvoří pravděpodobnosti rozdělení přes páry v vyšší dimenzí prostoru způsobem, který podobně jako objekty mají vysokou pravděpodobností vyzvednutí a odlišné body mají nízkou pravděpodobnost získávání zaznamenání. Druhý definuje podobně jako pravděpodobnosti rozdělení prostřednictvím bodů na mapě nízkou dimenzí a minimalizuje KL rozdíl mezi dvěma distribuce s ohledem na umístění bodů na mapě. Umístění z bodů nízkou dimenze se získávají pomocí minimalizace odchylkami KL pomocí přechodu klesání. Ale t Odborníka nemusí být vždy spolehlivé. Můžete najít podrobnosti implementace [zde](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Jak je znázorněno na následujícím obrázku, vizualizace t Odborníka poskytuje další oddělení vektory word a potenciální clustering vzory. 


* Vizualizace s PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Vizualizace se Odborníka t

![t Odborníka](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Body nejblíže k "Rakoviny" (jsou všechny podtypů rakoviny)

![Body nejblíže k rakoviny](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Cvičení neuronové entity Extraktor

V tématu [cvičení neuronové entity Extraktor](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

Architektura informačního kanálu dopředného neuronové sítě vykazuje problém, že považovat každý vstup a výstup jako nezávislé na dalších vstupy a výstupy. Tato architektura nelze model pořadí pořadí označování úloh strojový překlad a extrakce entity. Modely opakující neuronové sítě tento problém vyřešit, protože výměnu informací počítaný dokud nyní na další uzel. Tato vlastnost je volána s paměti v síti, protože je možné použít dříve počítaný informace, jak je znázorněno na následujícím obrázku:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Ve skutečnosti trpí Vanilla RNNs [ú přechodu problém](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) z důvodu, které nejsou umožňují využívat všechny informace, které jste viděli. Jasně problém se jenom v případě, že velké množství kontextu je nutná ke správnému předpovědi. Ale žádné modely jako LSTM tento problém, ve skutečnosti jsou navrženy mějte na paměti, dlouhodobé závislosti. Na rozdíl od vanilla RNNs, které mají jedné neuronové sítě, aby LSTMs interakce mezi čtyři neuronové sítě pro každou buňku. Podrobné vysvětlení, jak LSTM fungují, najdete v části [tento příspěvek](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM buňky](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Pojďme zkuste sestavit vlastní na základě LSTM opakující neuronové sítě a zkuste to k extrakci typy entit jako obchodu, z dat PubMed uvádí nákazy a příznakem. Prvním krokem je použít k získání velké množství s popisky dat a jak můžete by mít uhádnout, který není snadno! Většina lékařské dat obsahuje spoustu citlivé informace o osobě a proto nejsou veřejně dostupné. Spoléháme na kombinaci dvou různých datových sad, které jsou veřejně dostupné. První datovou sadu je z Semeval 2013 – úloha 9.1 (nedovolenému rozpoznávání) a druhá je z BioCreative V CDR úlohy. Jsme společném používání a automaticky tak, aby jsme zjistit drogy a nákaz z lékařské texty a vyhodnotit naše aplikace word vkládaných označování tyto dvě datové sady. Podrobnosti implementace, [Githubu kód odkaz](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

Architektura modelu, který jsme použili napříč všechny kódy a pro porovnání je uveden níže. Parametr, který změní pro různé datové sady je pořadí maximální délka (zde 613).

![LSTM modelu](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Vyhodnocení modelu
V tématu [modelu vyhodnocení](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Používáme skript vyhodnocení z sdílené úlohy [úlohu rozpoznávání Bio Entity v Bio NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) vyhodnocení přesnost, odvolání a F1 skóre modelu. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>V doméně a obecná slova vložení modely

Následuje porovnání mezi přesnost dva typy funkcí: slova (1) vkládaných trénink na PubMed přehledů a (2) word vkládaných trénink na zprávy Google. Jasně vidíte, že model v doméně překonává výkonem obecné modelu. Proto s určité slovo vložení model, místo použití obecné jeden je mnohem víc užitečné. 

* Úloha #1: Drogy a detekce nákaz

![Porovnání modelů 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Jsme provést vyhodnocení vkládaných word na ostatní datové sady v podobně jako ochrana a v tématu že tohoto modelu v doméně je lepší vždy.

* Úloha #2: Bílkoviny, buňky řádku, typ buňky, DNA a RNA detekce

![Porovnání modelů 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Úloha #3: Chemikálie a nákaz detekce

![Porovnání modelů 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Úloha #4: Drogy detekce

![Porovnání modelů 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Úloha #5: Genů detekce

![Porovnání modelů 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow versus CNTK
Všechny hlášené modely probíhá Trénink pomocí Keras TensorFlow jako back-end. Keras s back-end CNTK nepodporuje "reverse" v době, kdy bylo provedeno činnost. Z důvodu porovnání, jsme proto natrénovali model jednosměrný LSTM s back-end CNTK a porovnání s jednosměrný model LSTM s TensorFlow back-end. Nainstalujte CNTK 2.0 pro Keras z [zde](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Porovnání modelů 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Jsme k závěru, že CNTK funguje jako dobrý jako Tensorflow i z hlediska školení doba, za epoch (60 sekund pro CNTK a 75 sekundy pro Tensorflow) a počet entit testovací zjištěna. Jednosměrný vrstvy, které se používá pro vyhodnocení.


### <a name="3-deployment"></a>3. Nasazení

V tématu [nasazení](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Jsme nasadili webovou službou na cluster [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Prostředí operationalization zřídí Docker a Kubernetes v clusteru pro správu nasazení webové služby. Můžete najít další informace o procesu operationalization [zde](model-management-service-deploy.md ).


## <a name="conclusion"></a>Závěr

Potřebujeme přes podrobnosti o tom, jak může trénování modelu vnoření word pomocí algoritmu Word2Vec na Spark a pak použít extrahované vkládaných jako funkce ke cvičení hluboké neuronové sítě pro extrakci entity. Provedli jsme kanálu školení na biolékařského domény. Kanál je však dostatečně obecné má být použita k detekci vlastní entity typy ostatních domén. Stačí dostatek dat a můžete snadno upravit uvedeny v tomto tématu pro jinou doménu pracovního postupu.

## <a name="references"></a>Odkazy

* Tomáši Mikolov, Kai Svoboda, Gregu Corrado a Jana Dean. 2013a. Efektivní odhad reprezentace aplikace word v prostoru vektoru. V řízení ICLR.
* Tomáši Mikolov, Ilya Sutskever, Kai Svoboda, Gregu S Corrado a Jeff Dean. 2013b. Distribuované reprezentace slova a slovní spojení a jejich compositionality. V řízení NIPS stránky 3111 – 3119.
* Jirka Chiu, Gamal Crichton, Anna Korhonen a Sampo Pyysalo. 2016. [Postup dobrý slovo vložené Train části pro biolékařského NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), řízení v patnáctého pracoviště na biolékařského přirozeného jazyka zpracování stránky 166 – 174.
* [Vektor reprezentace slova](https://www.tensorflow.org/tutorials/word2vec)
* [Opakující Neuronové sítě](https://www.tensorflow.org/tutorials/recurrent)
* [Problémy s Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: poučení](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

