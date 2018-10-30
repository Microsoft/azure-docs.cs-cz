---
title: Rozpoznávání rozpoznávání biomedicínských entit – vědecké zpracování týmových dat – Azure Machine Learning | Dokumentace Microsoftu
description: Vědecké zpracování týmových dat projekt rychlý start, která využívá obsáhlý learning pro rozpoznávání rozpoznávání biomedicínských entit v aplikaci Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a3ae9c0926f9c2a8bb900e25b610f8c2f4e6d292
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231572"
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Rozpoznávání biomedicínských entit rozpoznávání pomocí šablony vědecké zpracování týmových dat (TDSP)

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Extrakce entity je dílčí úkol extrakci informací (označované také jako [rozpoznávání pojmenované entit (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), entity dat a identifikaci entity). Cílem této scénářem z reálného prostředí je zvýraznit jak používat Azure Machine Learning Workbench k řešit složité úloh zpracování přirozeného jazyka (NLP) jako je extrakce entity z nestrukturovaného textu:

1. Jak ke cvičení neuronové slovo vložené části modelovat na základě text souhrnu z přehledů PubMed asi 18 milionů pomocí [Spark Word2Vec implementace](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Jak vytvořit podrobné dlouhé krátkodobé paměti (LSTM) opakující se neuronové sítě model, který extrakce entity v podporou grafického procesoru Azure virtuální počítač pro datové vědy (virtuálního počítače s GPU DS) v Azure.
2. Předvedení že tohoto modelu specifického pro doménu slovo vložené části lze překonat modely vkládání obecná slova v úloze rozpoznávání entit. 
3. Ukazují, jak trénovat a zprovozňovat modely obsáhlého learningu pomocí aplikace Azure Machine Learning Workbench.

4. Ukazují následující možnosti v rámci Azure Machine Learning Workbench:

    * Vytvoření instance [struktura vědecké zpracování týmových dat (TDSP) a šablony](how-to-use-tdsp-in-azure-ml.md)
    * Automatizovanou správu závislostí vašeho projektu, včetně soubor ke stažení a instalace
    * Spouštění skriptů Pythonu v různých výpočetních prostředích
    * Spouštění skriptů Pythonu a sledovat historii
    * Spouštění úloh na vzdálené Spark compute kontexty clusterů HDInsight Spark 2.1
    * Spouštění úloh ve vzdálených virtuálních počítačích GPU v Azure
    * Snadné operacionalizace modely obsáhlého learningu jako webové služby v Azure Container Services (ACS)

## <a name="use-case-overview"></a>Přehled případu použití
Rozpoznávání biomedicínských entit s názvem, jako je kritickým krokem pro složité úlohy rozpoznávání biomedicínských NLP: 
* Extrahování zmínky o pojmenovaných entit z elektronických záznamů medical nebo stavu tyto nemoci, drog, chemických a příznaky.
* Léčiv USA se zabývá zjišťování
* Principy interakce mezi různé entity typy, jako je například léčiv USA se zabývá drug interakce, léčiv USA se zabývá stádiu vztah a vztah gene bílkovin.

Náš scénář použití se zaměřuje na jak velký objem nestrukturovaných dat souhrnu, jako je například přehledů Medline PubMed mohou být analyzovány pro trénování slov vkládání modelu. Pak vložené části výstupu se považují za automaticky generované funkce k trénování Extraktor neuronových sítí entity.

Naše výsledků jsou zobrazeny cvičení modelu extrakce rozpoznávání biomedicínských entit na slovo specifického pro doménu vkládání funkcí lepší výkon než modelů trénovaných na typ obecné funkce. Model specifického pro doménu, můžete zjistit 7012 entity správně (mimo 9475) se skóre F1 0.73 ve srovnání s 5274 entity se skóre F1 0,61 pro obecný model.

Následující obrázek ukazuje architekturu, která se používá ke zpracování dat a trénování modelů.

![Architektura](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Popis dat

### <a name="1-word2vec-model-training-data"></a>1. Word2Vec modelu trénovacích dat
Nejdříve stáhli nezpracovaná data MEDLINE abstraktní z [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Data jsou publicaly k dispozici ve formě souborů XML na jejich [FTP server](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Na serveru jsou k dispozici 892 soubory XML a všech souborů XML obsahuje informace o 30 000 článků. Další informace o kroku shromažďování dat jsou uvedeny v části strukturu projektu. Jsou k dispozici v jednotlivých souborech pole 
        
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

### <a name="2-lstm-model-training-data"></a>2. LSTM modelu trénovacích dat

Extrakce modelu neuronové entity má se trénují a vyhodnocují na veřejně dostupné datové sady. Získat podrobný popis o tyto datové sady, může odkazovat na následující zdroje:
 * [Úkol rozpoznávání entit životopis BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR souhrnu úlohy](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 – úloha 9.1 (rozpoznávání léčiv USA se zabývá)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Propojit úložiště GitHub Galerie Azure
Tady je odkaz na veřejné úložiště GitHub scénářem z reálného prostředí, která obsahuje kód a podrobný popis: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Požadavky 

* Azure [předplatného](https://azure.microsoft.com/free/)
* Aplikace Azure Machine Learning Workbench. Zobrazit [Průvodce instalací](quickstart-installation.md). Aktuálně lze nainstalovat aplikaci Azure Machine Learning Workbench na jenom následující operační systémy: 
    * Windows 10 nebo Windows Server 2016
    * macOS Sierra (nebo novější)


### <a name="azure-services"></a>Služby Azure
* [Cluster HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) verzi Sparku 2.1 v Linuxu (HDI 3.6) pro zpracování, horizontální navýšení kapacity. Ke zpracování v plné výši MEDLINE přehledů, viz následující popis, je třeba minimální konfiguraci: 
    * Hlavní uzel: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) velikost
    * Pracovní uzly: minimálně 4 [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). V naší práci jsme použili 11 pracovní uzly D12_V2 velikosti.
* [NC6 dat virtuálního počítače VĚDY](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) pro výpočet vertikálně navýšit kapacitu.

### <a name="python-packages"></a>Balíčky Pythonu

Všechny požadované závislosti jsou definovány v souboru aml_config/conda_dependencies.yml scénář ve složce projektu. Závislosti definované v tomto souboru jsou automaticky zřízena pro spuštění před docker, virtuální počítač a HDI clusteru cíle. Podrobnosti o formátu souborů prostředí Conda, najdete v [tady](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Základní pokyny pro Azure Machine Learning (AML) workbench
* [Přehled](../service/overview-what-is-azure-ml.md)
* [Instalace](quickstart-installation.md)
* [Použití TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Čtení a zápis souborů](how-to-read-write-files.md)
* [Použití poznámkových bloků Jupyter](how-to-use-jupyter-notebooks.md)
* [Jak používat GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Struktura scénář
Pro scénář, používáme TDSP struktury a dokumentace šablony projektu (obrázek 1), které následuje [životního cyklu TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Vytvoření projektu podle pokynů uvedených [tady](./how-to-use-tdsp-in-azure-ml.md).


![Vyplňte informace o projektu](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Pracovní postup krok za krokem datových věd vypadá takto:

### <a name="1-data-acquisition-and-understanding"></a>1. Získávání a pochopení dat

Zobrazit [Data získávání a pochopení](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

Nezpracovaná souhrnu MEDLINE má celkem 27 milionů přehledů, kde přibližně 10 milionů články obsahují prázdné pole abstraktní. Azure HDInsight Spark se používá ke zpracování velkých objemů dat, který nelze načíst do paměti jako jeden počítač [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Data se nejprve stáhne do clusteru Spark. Pak následující kroky se provádějí v [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* analyzovat pomocí analyzátoru XML Medline soubory XML
* předběžné zpracování abstraktní textu, včetně rozdělení věty, Tokenizace a případu normalizace.
* Vylučte články, ve kterém abstraktní pole je prázdné nebo má krátký textový 
* Vytvořit slovník slovo z přehledů školení
* trénování slov vkládání modelu neuronové. Další informace najdete v tématu [odkaz kódu Githubu](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) začít.


Po analýze soubory XML, data má následující formát: 

![Ukázková data](./media/scenario-tdsp-biomedical-recognition/datasample.png)

Extrakce modelu neuronové entity má se trénují a vyhodnocují na veřejně dostupné datové sady. Získat podrobný popis o tyto datové sady, může odkazovat na následující zdroje:
 * [Úkol rozpoznávání entit životopis BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR souhrnu úlohy](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 – úloha 9.1 (rozpoznávání léčiv USA se zabývá)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modelování

Zobrazit [modelování](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modelování je fáze, ve kterém vám ukážeme, jak můžete pomocí dat si stáhli v předchozí části pro trénování vlastních slov vkládání modelu a používat pro jiné podřízené úlohy. I když používáme PubMed data, kanál, aby generoval vkládání je obecný a lze opětovně použít k trénování vkládání slov u všech ostatních domén. Pro vkládání být přesné znázornění dat je nezbytné, aby word2vec jsou trénovaná na velké množství dat.
Jakmile budeme mít připravené vkládání slov, jsme trénování modelu hluboké neuronové sítě, který používá zjištěná vkládání inicializovat vrstvě vložení. Jsme označit vkládání vrstvy jako trainable ale to není povinné. Trénování slov vkládání modelu je bez dohledu a proto jsme budou moct využít výhod bez popisku texty. Nicméně školení model rozpoznávání entit je úlohu učení a jeho přesnost závisí na množství a kvalitu dat ručně poznámkou. 


#### <a name="21-feature-generation"></a>2.1. Funkce generování

Zobrazit [funkci generování](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec je slovo vkládání bez dohledu učení algoritmu, který trénovat modelu neuronové sítě ze bez popisku školení souhrnu. Vytvoří průběžné vektor jednotlivých slov v souhrnu, který představuje jeho sémantické informace. Tyto modely jsou jednoduché neuronové sítě pomocí jednoho skryté vrstvě. Jsou vektory/vkládání slov se dozvěděli, zpětné šíření chyb a stochastického sestupu. Existují dva typy word2vec modelů, konkrétně, přeskočit-Gram a průběžné – kontejner objektů a dat sady slova (zkontrolujte [tady](https://arxiv.org/pdf/1301.3781.pdf) další podrobnosti). Protože používáme MLlib provádění word2vec, která podporuje model přeskočit gramy, jsme Krátce popište ho tady (image na základě [odkaz](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Přeskočit Gram modelu](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

Model využívá hierarchické Softmax a záporná vzorkování pro optimalizaci výkonu. Hierarchické SoftMax (H-SoftMax) je INSPIROVANÉ binárním stromům přibližný. H-SoftMax v podstatě nahradí bez stromové struktury vrstvy SoftMax hierarchické vrstvu, která obsahuje slova jako opustí. To umožňuje rozložit výpočet pravděpodobnosti výskytu slov na sekvenci výpočty pravděpodobnosti, které nám ušetří s vypočítá nákladné normalizace všechna slova. Protože vyvážené binárního stromu je hloubka log2 (| V |) (V je slovník), musíme vyhodnotit maximálně log2 (| V |) uzly získat poslední pravděpodobnost slova. Pravděpodobnost slovo w uveden jeho kontextu c je pak jednoduše produktu pravděpodobností pořízení vpravo a vlevo se změní v uvedeném pořadí, které vedou k jeho listový uzel. Integrujeme Huffmanova stromu na základě četnosti slova v datové sadě k zajištění, že častější slova získání reprezentace kratší. Další informace najdete v tématu [tento odkaz](http://sebastianruder.com/word-embeddings-softmax/).
Image z [tady](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Vizualizace

Jakmile budeme mít vkládání slov, rádi bychom Vizualizujte si je a zobrazení vztahů mezi sémanticky podobné slova. 

![W2V podobnosti](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Nám ukázaly, dva různé způsoby vizualizace vložené části. První z nich používá PCA do projektu vysokou multidimenzionálním vektoru na 2D vektorová znak. Tím dojde k významné ztrátě informací a není tak přesné vizualizace. Druhým je použití DPS s [t Odborníka](https://distill.pub/2016/misread-tsne/). t Odborníka je technika snížení nelineárních dimenzionalitu, je velmi vhodná pro vkládání vysokým počtem rozměrů data do prostoru dvě nebo tři dimenze, které lze následně vizualizovat v korelačním diagramu.  Dvě nebo three trojrozměrným bod tak, že podobně jako objekty jsou modelovány blízké body a odlišné objekty jsou modelovány pomocí vzdálených bodů modeluje každého vysokým počtem rozměrů objektu. Funguje ve dvou částech. Nejprve vytvoří pravděpodobnostní rozdělení přes dvojice v vyšší rozměrného prostoru tak, aby podobně jako objekty mají vysokou pravděpodobností vyzvednutí a mají rozdílné body nízká pravděpodobnost získání výběru. Za druhé definuje podobně jako pravděpodobnostní rozdělení přes body na mapě s nízkou dimenzí a minimalizuje KL rozdílů mezi dvěma distribuce s ohledem na umístění bodů na mapě. Umístění z bodů s nízkou dimenze se získávají minimalizací odchylkami KL pomocí sestupu. Ale t Odborníka nemusí být vždy spolehlivé. Podrobnosti o implementaci najdete [tady](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Jak je znázorněno na následujícím obrázku, vizualizace t Odborník poskytuje další oddělení vzorů vektory aplikace word a potenciální clusteringu. 


* Vizualizace s využitím DPS

![DPS](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Vizualizace s využitím t Odborníka

![t Odborníka](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Body, které jsou nejblíž k "Rakoviny" (jsou všechny podtypy rakoviny)

![Body, které jsou nejblíž k rakoviny](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Trénování Extraktor neuronových sítí entity

Zobrazit [trénování Extraktor neuronových sítí entity](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

Architektura kanál vpřed neuronové sítě vykazuje problém, aby považovat každá vstupu a výstupu jako nezávislé vstupy a výstupy. Tato architektura nelze model sequence sequence popisování úkoly jako strojový překlad a extrakce entity. Modely neuronových sítí opakující se tento problém vyřešit, jak předávají informace počítá dokud nyní na dalším uzlu. Tato vlastnost je volána s paměti v síti, protože je možné použít dříve počítaný informace, jak je znázorněno na následujícím obrázku:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Ve skutečnosti trpí Vanilla Rnn [zmenšovala přechodu problém](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) kvůli které nejsou umožňují využívat všechny informace, které mají neobsahovala. Problém bude zřejmé, pouze v případě, že velké množství kontextu se vyžaduje k předpovědím. Ale žádné modely jako LSTM těmto potížím, ve skutečnosti jsou navrženy pamatovat dlouhodobé závislosti. Na rozdíl od vanilla Rnn, které mají jednu neuronové sítě musí sítí Lstm interakce mezi čtyři neuronových sítí pro každou buňku. Podrobné vysvětlení fungování LSTM, najdete v tématu [tento příspěvek](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM buňky](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Umožňuje sestavit vlastní na základě LSTM opakované neuronové sítě a akci k extrakci typů entit jako drog, choroba a příznak zmínky z PubMed data. Prvním krokem je získání velké množství s popisky dat a jak vám bude mít uhodnout, to není snadné! Většina zdravotnických dat obsahuje velké množství citlivých informací o osobě a proto nejsou veřejně dostupné. Spoléháme na kombinace dvou různých datových sad, které jsou veřejně dostupné. První datovou sadu z Semeval 2013 – úloha 9.1 (rozpoznávání léčiv USA se zabývá) a druhá BioCreative V CDR úkolu. Můžeme se správné kombinování a automatického štítkování tyto dvě datové sady, tak, aby můžeme detekovat drogy a nemocemi chránit z lékařských texty a vyhodnotit naše vkládání slov. Podrobnosti implementace, najdete v tématu [odkaz kódu Githubu](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

Architektura modelu, který jsme použili přes všechny kódy a porovnání je uveden níže. Parametr, který změní pro různé datové sady se pořadí maximální délka (zde 613).

![LSTM modelu](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Vyhodnocení modelu
Zobrazit [Model hodnocení](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Vyhodnocení skriptu ze sdílených úloh používáme [životopis Entity rozpoznávání úkol životopis NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) vyhodnotit přesnost, odvolání a F1 určení skóre modelu. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>V doméně a obecná slova vkládání modelů

Tady je porovnání mezi službou přesnost dva typy funkce: vkládání slov (1) trénovaných na PubMed přehledů a vkládání slov (2) trénovaných na novinky služby Google. Vidíme jasně, že model v doméně, lepší výkon než obecný model. Proto s konkrétním slovem vkládání model, místo použití obecný je velmi užitečné. 

* #1. úkol: Léčivy a nemocemi chránit detekce

![Porovnání modelů 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Můžeme provést vyhodnocení vkládání slov na jiné datové sady v podobným způsobem, vidíme, že vždy je lepší tohoto modelu v doméně.

* Úloha #2: Proteinů buňky řádku, typ buňky, DNA a RNA detekce

![Porovnání modelů 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* #3. úkol: Chemikálií a nemocemi chránit detekce

![Porovnání modelů 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Úkolu #4: Detekce léčivy

![Porovnání modelů 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* #5. úkol: Detekce genů

![Porovnání modelů 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow oproti CNTK
Ohlášené modely se trénuje pomocí Keras TensorFlow jako back-endu. Keras s back-endem CNTK nepodporuje "reverse" v době, kdy bylo provedeno tuto práci. Proto jsme pro účely porovnání, Trénink modelu jednosměrnou LSTM s back-endem CNTK a porovnání jednosměrnou LSTM modelu s TensorFlow back-endu. Instalace CNTK 2.0 pro Keras z [tady](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Porovnání modelů 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Jsme dospělo k závěru, že CNTK funguje jako dobré jako Tensorflow i z hlediska školení doba epocha (60 sekund pro CNTK a 75 sekund pro Tensorflow) a počtu entit testu zjistil. Jednosměrný vrstvy se používá pro hodnocení.


### <a name="3-deployment"></a>3. Nasazení

Zobrazit [nasazení](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Jsme nasadili webovou službu na clusteru v [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Operacionalizace vývojového a testovacího prostředí Docker a Kubernetes v clusteru pro správu nasazení webové služby. Najdete další informace o procesu operacionalizace [tady](model-management-service-deploy.md ).


## <a name="conclusion"></a>Závěr

Jsme se dostali přes podrobnosti toho, jak může trénování modelu aplikace word vkládání pomocí algoritmu Word2Vec ve Sparku a pak použít extrahované vkládání jako funkce k trénování hluboké neuronové sítě pro extrakci entity. Rozpoznávání biomedicínských domény jsme použili kanálu školení. Kanál je však dostatečně obecný použít k detekci vlastní entitu typů z ostatních domén. Potřebujete jenom dostatek dat, a můžete snadno upravit pracovní postup okomentovat pro jinou doménu.

## <a name="references"></a>Odkazy

* Tomáši Mikolov Kai Svoboda, Greg Corrado a Jeffrey Dean. 2013a. Efektivní odhad reprezentace aplikace word v prostoru vektoru. In: Proceedings of ICLR.
* Mikolov Tomáši, Ilya Sutskever, Kai Svoboda, Greg S Corrado a Jeff Dean. 2013b. Distribuované reprezentace slov a vět a jejich compositionality. In: Proceedings NIPS str. 3111 – 3119.
* Billy Chiu Gamal Crichton, Anna Korhonen a Sampo Pyysalo. 2016. [Jak pro trénování dobré vkládání slov pro rozpoznávání Biomedicínských NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), 166 – 174 stránky v in: Proceedings of patnáctý seminář na rozpoznávání Biomedicínských zpracování přirozeného jazyka.
* [Vektor reprezentace slova](https://www.tensorflow.org/tutorials/word2vec)
* [Opakované Neuronových sítí](https://www.tensorflow.org/tutorials/recurrent)
* [Potíže se Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: poučení](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

