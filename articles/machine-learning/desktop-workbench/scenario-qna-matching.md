---
title: Q & párování pomocí Azure Machine Learning Workbench | Microsoft Docs
description: Jak používat různé metody efektivní machine learning tak, aby odpovídaly otevřete skončila dotazy na existující – nejčastější dotazy nebo odpovědi na otázky páry.
services: machine-learning
documentationcenter: ''
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
manager: tihazen
ms.openlocfilehash: 3695657d819f52ef04468a0f1a44828403c65373
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835096"
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Q & párování pomocí Azure Machine Learning workbench
Když otevřete zakončeno otázky odpovíte je obtížné a často vyžaduje úsilí od odborníků (malé a střední podniky). Chcete-li snížit požadavky na interní malé a střední podniky, společnosti často vytvořit seznam – nejčastější dotazy (FAQ) jako prostředek, které uživatelé. Tento příklad umožňující prezentovat různé metody efektivní machine learning tak, aby odpovídaly otevřete zakončeno dotazy na existující nejčastější dotazy k odpovědi na otázky nebo páry. Tento příklad ukazuje o snadný vývoj proces pro sestavování řešení pomocí nástroje Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Propojit s úložišti GitHub Galerie
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Přehled

Tento příklad řeší problém mapování uživatele dotazy na existující otázka a odpověď (otázek a odpovědí) páry jako se většinou poskytuje seznam – nejčastější dotazy (tedy FAQ) nebo v poli otázek & jako páry, která se nachází na webech [zásobníku Přetečení](https://stackoverflow.com/). Existuje mnoho přístupů tak, aby odpovídaly dotaz a její správnou odpověď, jako je hledání odpovědí, která se nejvíce podobá na otázku. Ale v tomto příkladu otevřete zakončeno otázky budou odpovídat dříve dotazy pomocí za předpokladu, že každá odpověď v části Nejčastější dotazy dokáže odpovědět na několik otázek sémanticky ekvivalentní.

Klíče kroky potřebné k poskytování toto řešení jsou následující:

1. Vyčistěte a zpracování dat text.
2. Přečtěte si informativní frází, které jsou pořadí aplikace word, které poskytují další informace v pořadí, než pokud považovat nezávisle.
3. Extrahujte funkce textová data.
4. Text klasifikační modely natrénování a vyhodnocení výkonu modelu.


## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

1. [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
2. Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Průvodce instalací úvodní](../service/quickstart-installation.md) k instalaci programu a vytvořit pracovní prostor.
3. V tomto příkladu by bylo možné spustit v kontextu žádné výpočty. Ale doporučujeme ji spustit na počítači s více jádry s minimálně 16GB paměti a místa na disku 5GB.

## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** klikněte na tlačítko **+** přihlásit a vybrat **nový projekt**
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Q & A odpovídající" a vyberte šablonu
5.  Klikněte na **Vytvořit**

## <a name="data-description"></a>Popis dat

Datové sady použité v tomto příkladu je zásobníku Exchange Data Dump uložené v [archive.org](https://archive.org/details/stackexchange). Tato data jsou anonymizovaná výpis veškerý obsah pro uživatele podílí na [síťový zásobník Exchange](https://stackexchange.com/). Každá lokalita v síti je formátován jako samostatné archivu skládající se z soubory XML, ZIP prostřednictvím 7-zip pomocí bzip2 komprese. Každé lokality archivu zahrnuje příspěvky, uživatelé, hlasování, komentáře, PostHistory a PostLinks. 

### <a name="data-source"></a>Zdroj dat

Tento příklad používá [odešle data (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) a [PostLinks dat (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) z webu Stack Overflow. Informace o dokončení schématu, najdete v článku [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

`PostTypeId` Pole v příspěvcích dat určuje, zda je metodu POST směřující `Question` nebo `Answer`. `PostLinkTypeId` Pole v PostLinks dat určuje, zda dva příspěvcích jsou propojené nebo duplicitní. Příspěvky otázku obvykle obsahují některé značky, které jsou klíčová slova, která kategorizace dotaz s další otázky podobné nebo duplicitní. Existují některé značky s vysoká frekvence, jako například `javascript`, `java`, `c#`, `php` atd., obsahovat větší počet příspěvcích na otázku. V tomto příkladu, podmnožinu Q & páry s `javascript` značky je rozbalena.

Additionionally, post otázku může souviset s více odpovědí příspěvcích nebo příspěvcích duplicitní otázku. Chcete-li vytvořit seznam – nejčastější dotazy z tyto dvě datové sady, jsou považovány za některých kritéria kolekce dat. Tři sady kompilované data jsou vybrány pomocí skriptu SQL, který není zahrnutý v tomto příkladu. Výsledná data popis vypadá takto:

- `Original Questions (Q)`: Otázka příspěvcích jsou žádat a Stack Overflow serveru odpovědi na.
- `Duplications (D)`: Otázka odešle duplicitní jiné existující dotazy (`PostLinkTypeId = 3`), které jsou na původní otázky. Duplicit jsou považovány za jako sémanticky shodné s původní dotazy v tom smyslu, odpovědí zadané na původní otázku také odpovědi novou duplicitní otázku.
- `Answers (A)`: Každý původní otázku a jeho duplicit může propojit více než jeden příspěvcích odpovědí. Tento příklad vybere jen post odpovědí, který je buď přijat původní Autor (filtrovaná podle `AcceptedAnswerId`), nebo má nejvyšší skóre (podle `Score`) v původní otázky. 

Kombinace tyto tři datové sady vytvoří otázek a odpovědí páry kde odpověď (A) je namapovaný na jednu původní otázku (Q) a více duplicitní otázky (D) vidíte na následujícím diagramu dat:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Struktura dat

Schéma dat a odkazy na stažení přímé tři datových sad naleznete v následující tabulce:

| Datová sada | Pole | Typ | Popis
| ----------|------------|------------|--------
| [Otázky](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | ID | Řetězec | Otázka jedinečné ID (primární klíč)
|  | AnswerId | Řetězec | ID jedinečný odpověď na otázku
|  | Text0 | Řetězec | Nezpracovaný text data, včetně nadpis a text na otázku
|  | Datum vytvoření | Časové razítko | Pokud byla požádána otázka časové razítko
| [duplicitní](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | ID | Řetězec | Duplikace jedinečné ID (primární klíč)
|  | AnswerId | Řetězec | ID odpovědí spojené s duplikace
|  | Text0 | Řetězec | Nezpracovaný text data, včetně duplikace na nadpis a text
|  | Datum vytvoření | Časové razítko | Pokud byla požádána duplikace časové razítko
| [odpovědi](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | ID | Řetězec | Odpověď jedinečné ID (primární klíč)
|  | text0 | Řetězec | Data nezpracovaný text odpovědi


## <a name="scenario-structure"></a>Struktura scénář

Příklad odpovídající otázky A odpovědi jsou nabízena prostřednictvím tři typy souborů. Prvním typem je řadu Jupyter Notebooks, který zobrazí podrobné popisy celého pracovního postupu. Druhý typ je určitou sadu souborů Python obsahovat vlastní moduly jazyka Python pro extrakci frázi učení a funkce. Tyto moduly jazyka Python jsou dostatečně obecné, ne jenom poskytovat tento příklad, ale také jiné případy použití. Třetí typ je sada souborů Python na ladění technologie hyper parametry a sledování výkonu modelu pomocí nástroje Azure Machine Learning Workbench.

Soubory v tomto příkladu jsou uspořádány následujícím způsobem.

| Název souboru | Typ | Popis
| ----------|------------|--------
| `Image` | Složka | Složku pro ukládání bitových kopií pro soubor README
| `notebooks` | Složka | Složka Poznámkové bloky Jupyter
| `modules` | Složka | Složka modulů Python
| `scripts` | Složka | Ke složce se soubory Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Poznámkový blok Jupyter | Přístup k ukázková data, předběžně zpracovat text a příprava školení a testování datové sady
| `notebooks/Part_2_Phrase_Learning.ipynb` | Poznámkový blok Jupyter | Další informace informativní frází a tokenizaci text do kontejneru objektů a dat z slova (smyčce) reprezentace
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Poznámkový blok Jupyter | Extrakce funkce, cvičení text klasifikační modely a vyhodnocení modelu výkonu
| `modules/__init__.py` | Soubor Python | Init – soubor balíčku Python
| `modules/phrase_learning.py` | Soubor Python | Python moduly používané k transformaci nezpracovaná data a další informativní fráze
| `modules/feature_extractor.py` | Soubor Python | Moduly jazyka Python extrahovat funkcí pro trénování modelu
| `scripts/naive_bayes.py` | Soubor Python | Python pro optimalizaci technologie hyper parametry v Naive Bayes modelu
| `scripts/random_forest.py` | Soubor Python | Python pro optimalizaci technologie hyper parametry v doménové struktuře náhodných modelu
| `README.md` | Soubor markdownu | Soubor README markdownu

> [!NOTE]
> V části Python 3.5 vychází řadu poznámkových bloků.
> 

### <a name="data-ingestion-and-transformation"></a>Přijímání dat a transformace

Tři kompilované datové sady jsou uloženy v úložišti objektů Blob a jsou načteny v `Part_1_Data_Preparation.ipynb` poznámkového bloku.  

Před cvičení klasifikační modely text, je text v otázky čištění a zpracované vyloučit fragmenty kódu. Learning bez dohledu frázi je použitá v průběhu školicích materiálů další informativní pořadí aplikace word. Tyto frází jsou reprezentovány jako jednotky jednoho složené slova v podřízené kontejner objektů a dat z slova (smyčce) featurization používané klasifikační modely text.

Podrobný popis podrobné předběžného zpracování textu a frázi learning lze nalézt v poznámkových bloků `Part_1_Data_Preparation.ipynb` a `Part_2_Phrase_Learning.ipynb`, v uvedeném pořadí.

### <a name="modeling"></a>Modelování

Tento příklad slouží ke stanovení skóre nové dotazy proti existující otázek a odpovědí páry podle textu školení klasifikační modely, kde každý existující otázek a odpovědí pár je jedinečná třída a podmnožinu duplicitní dotazy pro každý pár otázek a odpovědí jsou dostupné jako školicí materiály. V příkladu původní otázky a 75 % na duplicitní otázky, které se zachovají pro školení a nedávno odeslaných 25 % duplicitní otázky jsou uchovávat-na víc systémů jako data pro vyhodnocení.

Klasifikace model používá metodu komplet k agregaci tři základní třídění, včetně **Naive Bayes**, **Support Vector Machine**, a **doménové struktury náhodných**. V každé základní třídění `AnswerId` slouží jako popisek třídy a vyjádření smyčce slouží jako funkce.

Proces školení model je znázorněn na následujícím `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Vyhodnocení

Dva různé vyhodnocení metriky se používají k vyhodnocení výkonu. 
1. `Average Rank (AR)`: označuje průměrná pozici kterých správnou odpověď se nachází v seznamu párů načtené otázek a odpovědí (mimo kompletní 103 třídy odpovědí). 
2. `Top 3 Percentage`: Určuje procento testovací otázky, že správné odpovědí lze načíst v první tři možnosti ve vráceném seznam hodnocení. 

Vyhodnocení je znázorněn v `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Závěr

V tomto příkladu se dozvíte, jak vytvořit model robustní využít techniky analytics dobře známé text, například klasifikace frázi učení a text. Také hodnotí, jak Azure Machine Learning Workbench vám mohou pomoci s výkonem interaktivní řešení pro vývoj a sledovat modelu. 

Některé klíče označuje tohoto příkladu jsou:

- Otázka a odpověď odpovídající problém podaří efektivně vyřešit s klasifikační modely frázi učení a text.
- Ukázka interaktivní modelu vývoj pomocí Azure Machine Learning Workbench a Poznámkový blok Jupyter.
- Azure Machine Learning Workbench spravuje historie spouštění a učit modely protokolování vyhodnocení metriky pro účely porovnání. Tyto funkce umožňuje rychlé hyper parametr ladění a pomáhá identifikovat nejvýkonnější modelů.


## <a name="references"></a>Odkazy

Alexander J. Hazen, František Ryšánková [ _modelování Multiword frází s omezené frází strom pro vylepšené tématu modelování konverzačního řeči_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Používá se jazyk technologie dílny (SLT), 2012 IEEE. IEEE, 2012.

Alexander J. Hazen, [ _Media Center školení techniky pro identifikaci tématu mluvené zvuk dokumentů_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) IEEE transakcí na zvuk, řeči a jazyk zpracování, obj. 19, ne. 8, s. 2451-2460, listopadu 2011.
