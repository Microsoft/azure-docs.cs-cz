---
title: Zdokumentujte analýza kolekce – Azure | Dokumentace Microsoftu
description: Jak vytvořit souhrn a analyzovat velké kolekce dokumentů, včetně technik, jako je například learning frázi, tématu modelování a téma model analýza s využitím Azure ML Workbench.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 762955103aeb48eb8a9b62f4e3ffe193bba71a38
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947213"
---
# <a name="document-collection-analysis"></a>Analýza kolekce dokumentů

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Tento scénář ukazuje, jak vytvořit souhrn a analyzovat velké kolekce dokumentů, včetně technik, jako je například learning frázi, tématu modelování a téma model analýza s využitím Azure ML Workbench. Azure Machine Learning Workbench poskytuje pro snadné škálování pro kolekci velmi velkých dokumentů a mechanismy pro trénování a vyladit modely v rámci různých výpočetní kontext, od místní výpočetní prostředky pro virtuální počítače pro datové vědy ke clusteru Spark. Snadný vývoj je zajišťováno prostřednictvím poznámkové bloky Jupyter v Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie

Veřejného úložiště GitHub pro tento scénář reálného světa obsahuje veškeré materiály, včetně ukázek kódu, třeba v tomto příkladu:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Přehled

Velký objem dat (zejména ukládání nestrukturovaných textových dat) shromážděné každý den je velkou výzvou k uspořádání, vyhledat a porozumět obrovské množství znění. Scénáře pro analýzu kolekce tento dokument ukazuje, efektivní a automatizované začátku do konce pracovním postupu pro analýzu velkých dokumentů kolekce a povolení podřízené úlohy NLP.

Klíčové prvky od tohoto scénáře jsou:

1. Učení charakteristiky fráze více slov z dokumentů.

1. Zjišťování základní témata, která prezentují v kolekci dokumentů.

1. Reprezentujícími dokumenty užitečná distribucí.

1. Nabízí ten samý metody k uspořádání, vyhledávání a souhrnem dokumentů podle obsahu užitečná.

Metody uvedené v tomto scénáři by mohla umožnit celou řadu průmyslových úloh, jako je například zjišťování anomálií trendy tématu, shrnutí kolekce dokumentů a podobně jako vyhledávání dokumentů. Lze použít k mnoha různým typům analýzy dokumentu, jako je například ze státní správy, příběhy, recenzemi produktů, názory zákazníků a vědecký výzkum článků.

Algoritmů strojového učení techniky/používané v tomto scénáři patří:

1. Zpracování textu a čištění

1. Fráze učení

1. Téma modelování

1. Shrnutí souhrnu

1. Užitečná trendů a zjištění anomálií

## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

* Ujistěte se, že jste správně nainstalovali [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) podle [instalace a vytvoření rychlý Start](quickstart-installation.md).

* V tomto příkladu by bylo možné spustit na libovolný výpočetní kontext. Ale doporučuje se ho spustit na počítači s více jádry s aspoň 16GB paměti a místa na disku 5GB.

## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace Workbench

Vytvořte nový projekt s použitím v tomto příkladu jako šablony:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** stránky, klikněte na tlačítko **+** přihlášení a výběr **nový projekt**
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Analýza kolekce dokumentů" a vyberte šablonu
5.  Klikněte na **Vytvořit**

## <a name="data-description"></a>Popis dat

Datová sada v tomto scénáři používají obsahuje textové shrnutí a související metadata pro každou právní akci provedenou kongresu. Data se shromažďují z [GovTrack.us](https://www.govtrack.us/), který sleduje aktivity kongresu Spojené státy a pomáhá Američanů se považuje za účast v jejich národní právní proces. Hromadné dat si můžete stáhnout prostřednictvím [tento odkaz](https://www.govtrack.us/data/congress/) pomocí ruční skriptu, který není součástí tohoto scénáře. Podrobnosti o tom, jak stáhnout data nebyla nalezena v [dokumentace k rozhraní API GovTrack](https://www.govtrack.us/developers).

### <a name="data-source"></a>Zdroj dat

V tomto scénáři je nezpracovaná data shromážděná řadu právní akce zavedené kongresu (navrhovaných účtů a jejich řešení) z 1973 do června 2017 (93rd 115th kongresy). Data shromažďovaná ve formátu JSON a obsahuje širokou škálu informací o o právních akce. Odkazovat na [tento odkaz na GitHub](https://github.com/unitedstates/congress/wiki/bills) podrobný popis datových polí. Pro účely ukázky v tomto scénáři byly jenom určité podmnožiny datových polí extrahovaná ze souborů JSON. Předkompilovaný soubor TSV `CongressionalDataAll_Jun_2017.tsv` záznamy tyto legislativní akce je k dispozici v tomto scénáři. Soubor TSV lze automaticky stáhnout pomocí poznámkových bloků `1_Preprocess_Text.ipynb` ve složce Poznámkový blok nebo `preprocessText.py` v balíčku Python.

### <a name="data-structure"></a>Struktura dat

Existují devět datová pole v datovém souboru služby. Názvy datových polí a popisy jsou uvedeny následovně.

| Název pole | Typ | Popis | Chybí hodnota obsahovat |
|------------|------|-------------|---------------|
| `ID` | Řetězec | ID faktury/řešení. Formát tohoto pole je [bill_type] [číslo]-[kongresu]. Například "hconres1-93" znamená typ bill "hconres" (zastupuje pro řešení současných House odkazovat na [tento dokument](https://github.com/unitedstates/congress/wiki/bills#basic-information)), bill číslo je ' 1 'a číslo kongresu je 93". | Ne |
| `Text` | Řetězec | Obsah na faktuře/řešení. | Ne |
| `Date` | Řetězec | Datum faktury/rozlišení původně navrhl. Ve formátu "yyyy-mm-dd". | Ne |
| `SponsorName` | Řetězec | Název primárního sponzor, který navrhované faktury/řešení. | Ano |
| `Type` | Řetězec | Název typu primární sponzoringu, "zástupce" (zástupce) nebo "Odeslat" (senator). | Ano |
| `State` | Řetězec | Stav primární sponzor. | Ano |
| `District` | Integer | Číslo oblasti primární zadavatele, pokud je název sponzor zástupce. | Ano |
| `Party` | Řetězec | Strana hlavní sponzor. | Ano |
| `Subjects` | Řetězec | Předmět podmínky přidat kumulativně Kongresem knihovny do vyúčtování. Podmínky jsou zřetězeny čárkami. Tyto podmínky jsou zapsána lidských v knihovně kongresu a nejsou obvykle při prvním publikování informací na faktuře. Je možné přidat kdykoli. Proto na konci životnosti faktury, některé subjektu nemusí být relevantní už. | Ano |

## <a name="scenario-structure"></a>Struktura scénář

Příklad analýza kolekce dokumentů je uspořádaný do dvou typů dodávky. Prvním typem je řada iPython poznámkových bloků, které zobrazují podrobné popisy celého pracovního postupu. Druhým typem je balíček Pythonu, jakož i některé příklady, jak používat tento balíček. Balíček Pythonu není dostatečně obecný k použití v mnoha situacích.

Soubory v tomto příkladu jsou průchody uspořádány takto.

| Název souboru | Typ | Popis |
|-----------|------|-------------|
| `aml_config` | Složka | Azure Machine Learning Workbench konfigurační složky, najdete [této dokumentace](./experimentation-service-configuration-reference.md) pro konfiguraci spuštění podrobné experimentu |
| `Code` | Složka | Složky s kódem pro ukládání skriptů Pythonu a balíček Pythonu |
| `Data` | Složka | Složka data lze ukládat zprostředkující soubory |
| `notebooks` | Složka | Složka Poznámkové bloky Jupyter |
| `Code/documentAnalysis/__init__.py` | Soubor Pythonu | Soubor init balíčků Pythonu |
| `Code/documentAnalysis/configs.py` | Soubor Pythonu | Konfigurační soubor používán analýzou dokumentu balíček Pythonu, včetně předdefinovaných konstant |
| `Code/documentAnalysis/preprocessText.py` | Soubor Pythonu | Soubor Pythonu pro předzpracování dat pro podřízené úlohy |
| `Code/documentAnalysis/phraseLearning.py` | Soubor Pythonu | Soubor Pythonu používá další frází z dat a transformujte nezpracovaná data |
| `Code/documentAnalysis/topicModeling.py` | Soubor Pythonu | Soubor Pythonu využívají k tréninku modelu tématu latentní Dirichletův přidělení (LDA) |
| `Code/step1.py` | Soubor Pythonu | Krok 1 Analýza kolekce dokumentů: předzpracování textu |
| `Code/step2.py` | Soubor Pythonu | Krok 2 analýza kolekce dokumentů: frázi učení |
| `Code/step3.py` | Soubor Pythonu | Krok 3 Analýza kolekce dokumentů: natrénování a vyhodnocení modelů LDA tématu |
| `Code/runme.py` | Soubor Pythonu | Příklad provést všechny kroky v jednom souboru |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | Předběžné zpracování textu a transformujte nezpracovaná data |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | Přečtěte si vět z textu data (po transformaci dat) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | Trénování modelu tématu LDA |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | Shrnutí obsah kolekce dokumentů na základě trénovaného modelu tématu LDA |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | Analýza užitečná obsahu z kolekce textové dokumenty a korelovat užitečná informace o dalších meta-data přidružená k kolekce dokumentů |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython Notebook | Interaktivní vizualizace zjištěná tématu modelu |
| `notebooks/winprocess.py` | Soubor Pythonu | Skript v jazyce python pro multiprocesingem používané poznámkové bloky |
| `README.md` | Soubor markdownu | V souboru README markdownu |

### <a name="data-ingestion-and-transformation"></a>Ingestování a transformace

Zkompilovaný objekt dataset `CongressionalDataAll_Jun_2017.tsv` je uložen v úložišti objektů Blob a je přístupný i z v rámci poznámkových bloků a skriptů Pythonu. Existují dva kroky pro ingestování a transformace: předzpracování dat text a frázi učení.

#### <a name="preprocess-text-data"></a>Předběžně zpracovat textová data

Předzpracování krok platí technik zpracování přirozeného jazyka k čištění a příprava dat nezpracovaný text. Slouží jako předchůdce pro učení bez dohledu frázi a modelování latentní tématu. Podrobný podrobný popis najdete v poznámkovém bloku `1_Preprocess_Text.ipynb`. K dispozici je také skript Pythonu `step1.py` odpovídá tento poznámkový blok.

V tomto kroku je datový soubor TSV stáhli ze služby Azure Blob Storage a importovat jako Pandas DataFrame. Každý řádek prvek datového rámce je jeden získá na ucelenosti dlouhý řetězec textu nebo dokumentu. Každý dokument je poté rozděleny do bloků textu, které by mohly být věty, fráze nebo subphrases. Rozdělení je navržena pro zakázat frázi učení procesu pomocí věty napříč nebo mezi frázi slovo řetězce při učení frází.

K dispozici je několik funkcí zadaných pro předzpracování krok v poznámkovém bloku a balíček Pythonu. Většinou úlohy lze dosáhnout pomocí volání těchto dvou řádků kódy.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Fráze učení

Krok učení frázi implementuje základním rozhraním další klíčové fráze mezi velké kolekce dokumentů. Je popsaný v dokumentu s názvem "[modelování Multiword frází pomocí omezeného fráze strom pro vylepšené tématu modelování z konverzační řeči](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", který byl původně předložený Workshop IEEE 2012 na používaný jazyk Technologie. Podrobné provádění krok učení frázi je zobrazena ve iPython Notebook `2_Phrase_Learning.ipynb` a skript v jazyce Python `step2.py`.

Tento krok vezme vyčištěný text jako vstup a zjišťuje největší charakteristiky fráze ve velké kolekce dokumentů. Učení frázi je iterativní proces, který je možné rozdělit do tří úlohy: počet n gramy, řadit potenciální fráze váha Pointwise vzájemné informace o jejich základních slova a přepište frázi, která text. Tyto tři úkoly jsou spouštěny postupně v několika iteracích, dokud se naučili zadaná fráze.

V dokumentu balíčku Python analýzy, třída Pythonu `PhraseLearner` je definována v `phraseLearning.py` souboru. Tady je fragment kódu používá další frází.

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
> Krok učení frázi je implementováno s multiprocesingem. Nicméně udělejte více jader procesoru **není** znamená rychlejší provádění. V testech není výkon s nejvýše osmi jádry z důvodu režie multiprocesingem. Další 25 000 frází na počítači s osmi jádry (3,6 GHz) trvalo asi dva a půl hodiny.
>

### <a name="topic-modeling"></a>Téma modelování

Učení a používá model latentní tématu LDA je třetí krok v tomto scénáři. [Gensim](https://radimrehurek.com/gensim/) balíček Python je vyžadován v tomto kroku se dozvíte [LDA téma model](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Odpovídající poznámkového bloku pro tento krok je `3_Topic_Model_Training.ipynb`. Můžete také odkazovat na `step3.py` týkajících se použití balíčku analýzy dokumentu.

Hlavním úkolem je v tomto kroku, přečtěte si téma model LDA a vyladit hyper parametry. Existuje více parametrů musí být vyladěné při trénování modelu LDA, ale je nejdůležitější parametr počet témat. Příliš málo témata nemusí mít přehled, aby kolekce dokumentů. Při výběru příliš mnoho způsobí "over-pass-the slučování" souhrnu do mnoha malých, velmi podobný témat. Účelem tohoto scénáře je ukazují, jak optimalizovat počet témat. Azure Machine Learning Workbench vám dává svobodu chcete spouštět experimenty s konfigurací různých parametrů na různých výpočetní kontext.

V balíčku Python analýzy dokumentu, byly definovány několik funkce umožňující uživatelům zjistit nejlepší počet témat. Prvním přístupem je vyhodnocením koherence modelu tématu. Existují čtyři vyhodnocení matice podporované: `u_mass`, `c_v`, `c_uci`, a `c_npmi`. Podrobnosti o tyto čtyři metriky jsou popsány v [tento dokument](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Druhý postup je vyhodnotit perplexity na uložené mimo souhrnu.

Pro vyhodnocení perplexity tvaru křivky "U" očekává se, přečtěte si osvědčené počet témat. A pozice Pravoúhlá je nejlepší volbou. Doporučuje se k vyhodnocení více než jednou s různé náhodné a získat průměr. Vyhodnocení soudržnost má být n obrazce, což znamená, že zvýšení koherence se zvyšuje počet témat a pak snížit. Příkladu graf perplexity a `c_v` koherence zobrazuje následujícím způsobem.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v koherence](./media/scenario-document-collection-analysis/c_v_Coherence.png)

V tomto scénáři perplexity zvyšuje výrazně po 200 témata hodnotu koherence snižuje výrazně po 200 témata také. Na základě těchto grafů a touhy obecnější témata a prostřednictvím Clusterované témata, zvolte vhodný by měl být 200 témata.

Trénovat jeden model LDA téma do jednoho experimentu spustit, nebo natrénování a vyhodnocení více modelů LDA počet konfigurací jiné téma v jednom experimentu spustit. Doporučujeme spustit více než jednou pro jednu konfiguraci a potom získá průměrné hodnocení koherence a/nebo perplexity. Podrobnosti o tom, jak používat balíček analýzy dokumentu najdete v `step3.py` souboru. Fragment kódu příkladu vypadá takto.

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
> Doba provádění pro trénování modelu tématu LDA závisí na několika faktorech, jako je například velikost souhrnu, konfigurace hyper parametru a také počet jader v počítači. Použití více jader procesoru trénovat modelu rychlejší. Ale s parametrem stejné hyper nastavení více jader znamená menší počet aktualizací během cvičení. Doporučuje se mít **aspoň 100 aktualizací pro trénování modelu sblížené LDA**. Vztah mezi počet aktualizací a hyper parametrů je podrobněji popsána [tento příspěvek](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) a [tento příspěvek](http://miningthedetails.com/blog/python/lda/GensimLDA/). V testech, jakou trvalo přibližně 3 hodiny pro trénování modelu LDA s 200 témat pomocí konfigurace `workers=15`, `passes=10`, `chunksize=1000` na počítači s 16 jádry (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Shrnutí tématu a analýzy

Shrnutí tématu a analýzy se skládá z dva poznámkové bloky, když neexistují žádné odpovídající funkce v balíčku analýzy dokumentu.

V `4_Topic_Model_Summarization.ipynb`, ukazuje, jak slouží ke shrnutí obsah dokumentu podle trénovaného modelu LDA tématu. Shrnutí se použije na téma model LDA získané v kroku 3. Ukazuje, jak měřit důležitosti nebo kvality tématu pomocí tématu dokumentu čistoty měr. Tato míra čistoty předpokládá, že latentní témata, která převládají na poli dokumenty, ve které jsou zobrazeny jsou sémanticky důležité než latentní témata, které jsou slabě rozděleny mezi více dokumentů. Tento koncept byla zavedena v dokumentu "[latentní tématu modelování pro zvuk souhrnu shrnutí](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)."

Poznámkový blok `5_Topic_Model_Analysis.ipynb` ukazuje, jak analyzovat užitečná obsah kolekce dokumentů a korelovat užitečná informace o dalších meta-data přidružená k kolekce dokumentů. Několik vykreslení jsou popsané v tomto poznámkového bloku k poskytování pomoci uživatelům lepší pochopení zjištěná tématu a kolekce dokumentů.

Poznámkový blok `6_Interactive_Visualization.ipynb` ukazuje, jak interaktivně vizualizovat zjištěná tématu modelu. Zahrnuje čtyři úkoly interaktivní vizualizace.

## <a name="conclusion"></a>Závěr

Tento scénář reálného světa zvýrazní, jak vytvářet robustní model pomocí techniky analytics wkt (v tomto případě, učení frázi a modelování LDA téma) a jak aplikaci Azure Machine Learning Workbench vám může pomoct sledovat výkon modelů a bez obav spouštět inteligentních algoritmů ve větším měřítku. Podrobněji:

* Využijte učení frázi a modelování tématu ke zpracování. kolekce dokumentů a vytvářet robustní model. Pokud kolekce dokumentů, je obrovský, Azure Machine Learning Workbench můžete snadno škálovat ho nahoru a dolů. Kromě toho uživatelé moci svobodně chcete spouštět experimenty v různých výpočetním kontextu snadný přístup z v rámci Azure Machine Learning Workbench.

* Azure Machine Learning Workbench poskytuje obě možnosti pro krok za krokem způsobem a skript v jazyce Python pro celý experimenty možnost spouštět poznámkové bloky.

* Technologie Hyper parametr ladění pomocí Azure Machine Learning Workbench se najít nejlepší počet témat potřebné informace. Azure Machine Learning Workbench pomáhá sledovat výkon modelů a bez obav spouštět jiné inteligentních algoritmů ve větším měřítku.

* Azure Machine Learning Workbench můžete spravovat historii spuštění a zjištěná modely. Umožňuje datovým vědcům rychle identifikovat nejvýkonnější modely a skripty a data použitá k vygenerování.

## <a name="references"></a>Odkazy

* **Alexander J. Hazen, Fred Richardson**, [ _modelování Multiword frází pomocí omezeného fráze strom pro vylepšení modelování tématu konverzační řeči_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Mluvený jazyk technologie Workshop (SLT) 2012 IEEE. IEEE, 2012.

* **Alexander J. Hazen**, [ _latentní tématu modelování pro zvuk souhrnu shrnutí_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12. každoroční konference přidružení komunikace mezinárodní řeči. 2011.

* **Michael Roder, Andreas obě, Alexander Hinneburg**, [ _zkoumání místo tématu koherence opatření_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). In: Proceedings of osmého mezinárodní ACM conference na vyhledávání na webu a dolování dat. ACM, 2015.
