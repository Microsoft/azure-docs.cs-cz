---
title: Zdokumentujte kolekce analýza - Azure | Microsoft Docs
description: Jak shrnout a analyzovat velké kolekce dokumentů, včetně techniky například learning frázi, tématu modelování a analýzy modelu tématu pomocí Azure ML Workbench.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: cbb077796d9f14a7f9d1d00268c7b6889ade8375
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="document-collection-analysis"></a>Kolekce analýzy dokumentu

Tento scénář předvádí shrnout a analyzovat velké kolekce dokumentů, včetně techniky například learning frázi, tématu modelování a analýzy modelu tématu pomocí Azure ML Workbench. Azure Machine Learning Workbench obsahuje pro snadné škálování pro kolekce velké dokumentu a mechanismy pro trénování a ladit modely v rámci různých kontextů výpočetní, od místní výpočetní na Data vědecké účely virtuální počítače na clusteru Spark. Snadný vývoj je zajišťováno prostřednictvím poznámkové bloky Jupyter v rámci Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Propojit s úložišti GitHub Galerie

Veřejné úložiště GitHub pro tento scénář skutečných obsahuje všechny materiály, včetně ukázky kódu, potřebné pro tento příklad:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Přehled

Velký objem dat (zejména nestrukturovaných textová data) shromážděné každý den je důležité výzvu k uspořádání, vyhledávání a pochopit velká množství znění. Tento dokument kolekce analysis scénář ukazuje efektivní a automatizované začátku do konce workflow pro analýzu velkých dokumentu kolekce a povolení podřízené úlohy NLP.

Mezi klíčové prvky doručil tento scénář patří:

1. Learning nejdůležitějšími více slova frázi z dokumentů.

1. Zjišťování základní témata uvedená v kolekci dokumentů.

1. Reprezentujícími dokumenty užitečná distribucí.

1. Představuje metody pro uspořádání, vyhledávání a shrnutí dokumentů na základě užitečná obsahu.

Metody uvedené v tomto scénáři může povolit celou řadu kritické průmyslových úlohy, jako je například zjišťování anomálií trendy tématu, shrnutí kolekce dokumentů a podobně jako vyhledávání dokumentů. Je možné použít pro mnoho různých typů analýzy dokumentu, jako je například government předpisy, nejnovější zprávy, recenzemi produktů, názory zákazníků a vědeckého výzkumu články.

Algoritmy strojového učení techniky nebo použít v tomto scénáři patří:

1. Zpracování textu a čištění

1. Learning fráze

1. Téma modelování

1. Shrnutí svátek

1. Užitečná trendy a detekce anomálií

## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

* Ujistěte se, že jste správně nainstalovali [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) podle [instalovat a vytvořte rychlý Start](../service/quickstart-installation.md).

* V tomto příkladu by bylo možné spustit v kontextu žádné výpočty. Ale doporučujeme ji spustit na počítači s více jádry s minimálně 16GB paměti a místa na disku 5GB.

## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu Workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** klikněte na tlačítko **+** přihlásit a vybrat **nový projekt**
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Analýzy dokumentu kolekce" a vyberte šablonu
5.  Klikněte na **Vytvořit**

## <a name="data-description"></a>Popis dat

V tomto scénáři používají datová sada obsahuje souhrny text a přidružená data meta pro každý legislativní akce kongresu. Data jsou shromažďována z [GovTrack.us](https://www.govtrack.us/), který sleduje aktivity Kongresu Spojených státech amerických a pomáhá Američanů se podílet na jejich national legislativní proces. Hromadné dat si můžete stáhnout prostřednictvím [tento odkaz](https://www.govtrack.us/data/congress/) pomocí ruční skriptu, který není součástí tohoto scénáře. Podrobnosti o tom, jak stáhnout data nebyla nalezena v [dokumentaci k rozhraní API GovTrack](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Zdroj dat

V tomto scénáři se nezpracovaná data shromážděná řada legislativní akce zaváděné kongresu (navrhované faktur a řešení) z 1973 do června 2017 (93rd 115th kongresů). Data shromážděná je ve formátu JSON a obsahuje bohatou sadu informace o legislativní akce. Odkazovat na [tento odkaz Githubu](https://github.com/unitedstates/congress/wiki/bills) podrobný popis datová pole. Pro demonstrační účely v tomto scénáři byly jenom podmnožinu datová pole extrahovat z soubory JSON. Soubor předem kompilovaném TSV `CongressionalDataAll_Jun_2017.tsv` záznamy tyto legislativní akce je k dispozici v tomto scénáři. TSV může být soubor stažen automaticky buď poznámkových bloků `1_Preprocess_Text.ipynb` ve složce poznámkového bloku nebo `preprocessText.py` v balíčku Python.

### <a name="data-structure"></a>Struktura dat

V datovém souboru nejsou devět datová pole. Názvy datových polí a popisy jsou uvedeny následujícím způsobem.

| Název pole | Typ | Popis | Chybí hodnota obsahovat |
|------------|------|-------------|---------------|
| `ID` | Řetězec | ID na faktuře řešení. Formát tohoto pole je [bill_type] [číslo]-[kongresu]. Například "hconres1-93", znamená to typ faktury "hconres" (zastupuje pro řešení souběžných úklidové odkazovat na [tento dokument](https://github.com/unitedstates/congress/wiki/bills#basic-information)), číslo faktury je ' 1 'a počet kongresu je 93'. | Ne |
| `Text` | Řetězec | Obsah na faktuře řešení. | Ne |
| `Date` | Řetězec | Datum začátku navrhované faktury nebo řešení. Formát: rrrr mm-dd'. | Ne |
| `SponsorName` | Řetězec | Název primární řad, který navrhované faktury nebo rozlišení. | Ano |
| `Type` | Řetězec | Název typu primární podporovat 'rep' (typický) nebo "esílat" (senator). | Ano |
| `State` | Řetězec | Stav primární řad. | Ano |
| `District` | Integer | Oblastní počet primární řad, pokud je název sponzor zástupce. | Ano |
| `Party` | Řetězec | Strany primární řad. | Ano |
| `Subjects` | Řetězec | Podmínky subjektu kumulativně přidaných kongresu knihovny do kusovníku. Podmínky jsou zřetězeny čárkami. Tyto podmínky jsou zapsána lidské v knihovně kongresu a nejsou obvykle při prvním publikování informace na kusovníku. Kdykoli mohou být přidány. Proto na konci životnosti faktury některé subjektu nemusí být relevantní už. | Ano |

## <a name="scenario-structure"></a>Struktura scénář

Příklad analýzy dokumentu kolekce jsou uspořádány do dva typy výsledek. Prvním typem je řadu iPython poznámkových bloků, které zobrazují podrobné popisy celého pracovního postupu. Druhý typ je balíček Python, jakož i některé příklady kódu použití daného balíčku. Balíček Python je dostatečně obecné k obsluze mnoho případy použití.

Soubory v tomto příkladu jsou uspořádány následujícím způsobem.

| Název souboru | Typ | Popis |
|-----------|------|-------------|
| `aml_config` | Složka | Azure Machine Learning Workbench složky konfigurace, najdete [této dokumentace](./experimentation-service-configuration-reference.md) pro konfiguraci spuštění podrobné experimentu |
| `Code` | Složka | Kód složku pro ukládání skriptů Pythonu a balíček Python |
| `Data` | Složka | Složka dat používá pro uložení zprostředkující soubory |
| `notebooks` | Složka | Složka Poznámkové bloky Jupyter |
| `Code/documentAnalysis/__init__.py` | Soubor Python | Init – soubor balíčku Python |
| `Code/documentAnalysis/configs.py` | Soubor Python | Použití konfiguračního souboru analýza dokumentu balíček Python, včetně předdefinovaného konstanty |
| `Code/documentAnalysis/preprocessText.py` | Soubor Python | Soubor Python použitý k předzpracování dat pro podřízené úlohy |
| `Code/documentAnalysis/phraseLearning.py` | Soubor Python | Soubor Python použitý k další frází z dat a transformace nezpracovaná data |
| `Code/documentAnalysis/topicModeling.py` | Soubor Python | Soubor Python, které jsou použity při cvičení modelu tématu latentní Dirichletův přidělení LDA) |
| `Code/step1.py` | Soubor Python | Krok 1 analýzy dokumentu kolekce: předběžné zpracování textu |
| `Code/step2.py` | Soubor Python | Krok 2 analýzy dokumentu kolekce: fráze učení |
| `Code/step3.py` | Soubor Python | Krok 3 analýzy dokumentu kolekce: natrénování a vyhodnocení LDA tématu modelu |
| `Code/runme.py` | Soubor Python | Příklad spustit všechny kroky v jednom souboru |
| `notebooks/1_Preprocess_Text.ipynb` | iPython poznámkového bloku | Předběžné zpracování textu a transformace nezpracovaná data |
| `notebooks/2_Phrase_Learning.ipynb` | iPython poznámkového bloku | Další frází z textových dat (po transformaci dat) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython poznámkového bloku | Train LDA tématu modelu |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython poznámkového bloku | Shrnout obsah dokumentu kolekce založené na modulu trained model LDA tématu |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython poznámkového bloku | Analýza užitečná obsah kolekci dokumentů text a korelovat užitečná informace pro ostatní meta-data přidružené ke kolekci dokumentů |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython poznámkového bloku | Interaktivní vizualizace zjištěné tématu modelu |
| `notebooks/winprocess.py` | Soubor Python | Skript jazyka python pro více procesy používané poznámkových bloků |
| `README.md` | Soubor markdownu | Soubor README markdownu |

### <a name="data-ingestion-and-transformation"></a>Přijímání dat a transformace

Kompilované datovou sadu `CongressionalDataAll_Jun_2017.tsv` je uložen v úložišti objektů Blob a je přístupný z i v rámci poznámkových bloků a skriptů Python. Existují dva kroky pro přijímání dat a transformace: předzpracování dat text a fráze učení.

#### <a name="preprocess-text-data"></a>Předběžné zpracování textová data

Předběžného zpracování krok platí přirozeného jazyka zpracování techniky k vyčištění a připravit data nezpracovaný text. Slouží jako prekurzorů pro učení bez dohledu frázi a modelování latentní tématu. Podrobný podrobný popis naleznete v poznámkovém bloku `1_Preprocess_Text.ipynb`. Je také skript v jazyce Python `step1.py` odpovídá tento poznámkový blok.

V tomto kroku je TSV datový soubor stažený z Azure Blob Storage a importovat jako Pandas DataFrame. Každý prvek řádek v DataFrame je jeden získá na ucelenosti dlouhý řetězec textu nebo dokumentu. Každý dokument je pak rozdělí do bloků textu, které by mohly být věty, fráze nebo subphrases. Rozdělení je určena pro zakázat frázi učení procesu z použití řetězců mezi větu nebo frázi mezi word při učení frází.

Existuje více funkcí definovaných předběžného zpracování kroku v poznámkovém bloku i balíčku Python. Většina úlohy lze dosáhnout volání tyto dva řádky kódy.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Learning fráze

Krok learning frázi implementuje základní architekturu další klíče frází mezi velké kolekce dokumentů. Je popsané v dokumentu s názvem "[modelování Multiword frází s vynuceným frází strom pro vylepšené tématu modelování z konverzačního řeči](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", která byla původně prezentovaná pracoviště IEEE 2012 na použitém jazyce Technologie. Podrobné provádění frázi learning krok se zobrazí v iPython poznámkového bloku `2_Phrase_Learning.ipynb` a skript Pythonu `step2.py`.

Tento krok vezme vyčištěnými text jako vstup a zjišťuje nejvíce nejdůležitějšími frází ve velkých kolekci dokumentů. Learning frázi je iterativní proces, který je možné rozdělit do tří úlohy: počet n gram rank potenciální frází vážené Pointwise vzájemné informace o jejich základní slova a přepište frázi na text. Tyto tři úlohy jsou prováděny postupně v několika iterací, dokud se naučili zadaný frází.

V dokumentu balíčku Python analýzy, třídu Python `PhraseLearner` je definována v `phraseLearning.py` souboru. Níže je fragment kódu použít další frází.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> Krok learning frázi je implementováno s více procesy. Ale udělat další jader procesoru **není** znamenat rychlejší dobu provádění. V testech není s více než osm jader kvůli režii multiprocesing lepší výkon. Další 25 000 frází na počítači s osmi jádry (3,6 GHz) trvalo asi dva a půl hodiny.
>

### <a name="topic-modeling"></a>Téma modelování

Učení použití modelu latentní tématu LDA je třetí krok v tomto scénáři. [Gensim](https://radimrehurek.com/gensim/) balíček Python je vyžadován v tomto kroku se dozvíte [LDA tématu modelu](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Odpovídající Poznámkový blok pro tento krok je `3_Topic_Model_Training.ipynb`. Můžete se také podívat na `step3.py` pro použití balíčku analýzy dokumentu.

V tomto kroku je hlavní úloh další model LDA tématu a ladit hyper parametry. Existuje několik parametrů muset ladit při cvičení LDA model, ale počet témat je nejdůležitější parametr. Příliš málo témata nemusí mít přehled ke kolekci dokumentu. Při výběru příliš mnoho bude výsledkem "přepsání clusterování" souhrnu do mnoha malé, vysoce podobné témata. Účelem tohoto scénáře je ukazují, jak ladit počet témat. Azure Machine Learning Workbench poskytuje možnost používat tato zařízení na různých výpočetních kontexty spouštět experimenty s jiným parametrem konfigurace.

V balíčku Python analýzy dokumentu, byly definovány několik funkcí, abyste uživatelům pochopit nejlepší počet témat. Prvním přístupem je vyhodnocením soudržnost tématu modelu. Existují čtyři zkušební matic podporována: `u_mass`, `c_v`, `c_uci`, a `c_npmi`. Podrobnosti o tyto čtyři metriky, které jsou popsané v [tento dokument](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Druhý postup je vyhodnotit perplexity na svátek uchovávat na více systémů.

Pro vyhodnocení perplexity křivka tvar "U" by měl zjistit nejlepší počet témat. A pozice – pravý úhel je nejlepší volbou. Doporučuje se vyhodnotí vícekrát s jinou náhodný počáteční hodnoty a získat průměr. Vyhodnocení soudržnost musí být n utvářejí, což znamená, že zvyšuje soudržnost s zvýšit počet témat a potom snížit. Vykreslení příklad z perplexity a `c_v` soudržnost se zobrazuje následujícím způsobem.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v soudržnost](./media/scenario-document-collection-analysis/c_v_Coherence.png)

V tomto scénáři perplexity zvyšuje výrazně po 200 témata, hodnota soudržnost snižuje výrazně po 200 témata také. Na základě těchto grafů a desire pro další obecné témata versus přes Clusterové témata, vyberte 200 témata by měl být vhodný.

Můžete cvičení jeden LDA tématu model v experimentu jeden spustit, nebo natrénování a vyhodnocení více LDA modelů pomocí jiné téma číslo konfigurace v jednom experimentu spustit. Doporučuje se spouštět více než jednou. pro jednu konfiguraci a potom získat průměrné hodnocení soudržnost nebo perplexity. Podrobnosti o použití balíčku analýzy dokumentu najdete v `step3.py` souboru. Fragment kódu příklad je následující.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> Čas provádění ke cvičení model tématu LDA závisí na několika faktorech, například velikost svátek, konfigurace hyper parametru, jakož i počet jader na počítači. Použití více jader procesoru nastaví model rychlejší. S parametrem stejné hyper nastavení více jader znamená však méně aktualizace během cvičení. Doporučuje se mít **nejméně 100 aktualizací pro učení model sblížené LDA**. Vztah mezi počtem aktualizací a hyper parametry popsané v [tento příspěvek](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) a [tento příspěvek](http://miningthedetails.com/blog/python/lda/GensimLDA/). V testech trvalo asi 3 hodiny pro učení LDA model s 200 témat pomocí konfigurace `workers=15`, `passes=10`, `chunksize=1000` na počítači s 16 jader (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Shrnutí tématu a analýzy

Shrnutí tématu a analýzy se skládá z dvě poznámkových bloků, zatímco v balíčku analýzy dokumentu nejsou žádné odpovídající funkce.

V `4_Topic_Model_Summarization.ipynb`, ukazuje, jak shrnout obsah dokumentu založené na modulu trained model LDA tématu. Shrnutí se použije pro model tématu LDA získané v kroku 3. Ukazuje, jak měřit důležitost nebo kvalitu téma pomocí tématu dokumentu čistotu míru. Tato míra čistotu předpokládá, že latentní témata, která dominujte dokumenty, ve kterých se vyskytují jsou sémanticky důležité než latentní témata, které jsou slabě rozloženy mnoho dokumenty. Tento koncept byla zavedena v papíru "[latentní modelování tématu zvuk svátek souhrnu](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)."

Poznámkový blok `5_Topic_Model_Analysis.ipynb` ukazuje, jak analyzovat užitečná obsah kolekci dokumentů a korelovat užitečná informace o dalších meta-data přidružená k kolekce dokumentů. Několik pozemků jsou popsané v tomto poznámkovém bloku pomoci uživatelům lépe porozumět zjištěné tématu a kolekce dokumentů.

Poznámkový blok `6_Interactive_Visualization.ipynb` ukazuje, jak interaktivně vizualizovat zjištěné tématu modelu. Obsahuje čtyři úlohy interaktivních vizualizace.

## <a name="conclusion"></a>Závěr

Tento scénář skutečných klade důraz, jak vytvořit model robustní pomocí známých text analytics techniky (Tento případ, frázi učení a modelování tématu LDA) a jak Azure Machine Learning Workbench vám mohou pomoci sledovat výkon modelu a bez problémů spustit inteligentních algoritmů na škálování na větší rozsah. Podrobněji:

* Použijte frázi učení a modelování tématu ke zpracování kolekci dokumentů a vytvářet robustní modelu. Pokud je kolekce dokumentů velký, Azure Machine Learning Workbench snadno změnit jeho velikost až a out. Kromě toho uživatelé mají volného experimenty spustit v kontextu různými výpočetními snadno z v rámci Azure Machine Learning Workbench.

* Azure Machine Learning Workbench poskytuje obě možnosti spuštění poznámkových bloků krok za krokem způsobem a ke spuštění experimentu celý skript v jazyce Python.

* Technologie Hyper parametr ladění pomocí Azure Machine Learning Workbench najít nejlepší počet témat potřebné informace. Azure Machine Learning Workbench může pomoct sledovat výkon modelu a bez problémů spustit různé inteligentních algoritmů škálování na větší rozsah.

* Azure Machine Learning Workbench můžete spravovat historie spouštění a učit modelů. Umožňuje datových vědců rychle identifikovat nejvýkonnější modely a najít skripty a data sloužící ke generování je.

## <a name="references"></a>Odkazy

* **Alexander J. Hazen, František Ryšánková**, [ _modelování Multiword frází s omezené frází strom pro vylepšené tématu modelování konverzačního řeči_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Používá se jazyk technologie dílny (SLT), 2012 IEEE. IEEE, 2012.

* **Alexander J. Hazen**, [ _latentní tématu modelování zvuk svátek souhrnu_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12. roční konference přidružení mezinárodní řeči komunikace. 2011.

* **Michael Roder, Andreas obě, Alexander Hinneburg**, [ _zkoumat prostor tématu soudržnost míry_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Řízení osmého ACM mezinárodní konference o vyhledávání na webu a dolování dat. ACM, 2015.
