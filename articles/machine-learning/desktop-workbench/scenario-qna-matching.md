---
title: Funkce Q & odpovídající pomocí Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Jak použít různé metody efektivní strojové učení tak, aby odpovídaly otevřít skončila dotazy na existující – nejčastější dotazy a odpovědi na otázky dvojice.
services: machine-learning
documentationcenter: ''
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ROBOTS: NOINDEX
ms.openlocfilehash: e0f6148e1fb28838bf99c63fbfbfbfe8cd127c8c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973198"
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Funkce Q & odpovídající pomocí Azure Machine Learning workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Odpovídání na dotazy otevřít zakončeno je složité a často vyžadují ruční práce od odborníků na danou problematiku (MSP). Chcete-li snížit požadavky na interní malé a střední podniky, společnosti často vytvořit seznam – nejčastější dotazy (FAQ) jako prostředek pomoci uživatelům. V tomto příkladu představuje různé metody efektivní machine learning tak, aby odpovídaly otevřené zakončeno dotazy na existující dvojice – nejčastější dotazy a odpovědi na otázky. Tento příklad ukazuje snadný vývoj procesu pro sestavení řešení, pomocí aplikace Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Přehled

V tomto příkladu řeší potíže s mapování uživatele dotazy na existující otázku & odpovědí (Q & A) páry obvykle získáte seznam – nejčastější dotazy (to znamená, časté otázky) nebo pole pro otázky Q & dvojice k dispozici na webech, jako jsou [zásobníku Overflow](https://stackoverflow.com/). Existuje celá řada přístupů tak, aby odpovídaly dotaz a jeho správnou odpověď, jako je například hledání odpovědí, která se nejvíce podobá na otázku. Ale v tomto příkladu otevřít zakončeno dotazy budou odpovídat dříve dotazy podle za předpokladu, že každá odpověď v nejčastějších Dotazech může odpovídat na otázky více sémanticky ekvivalentní.

Jsou klíčové kroky potřebné k zajištění tohoto řešení:

1. Čištění a zpracovat textová data.
2. Přečtěte si informativní fráze, které jsou sekvence více slov, které poskytují další informace v pořadí, než když zacházet nezávisle na sobě.
3. Extrakce funkce z textová data.
4. Trénování modelů klasifikace textu a vyhodnotit výkon modelu.


## <a name="prerequisites"></a>Požadavky

Požadavky na spuštění v tomto příkladu jsou následující:

1. [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
2. Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Stručná Instalační příručka](quickstart-installation.md) k instalaci a vytvoření pracovního prostoru.
3. V tomto příkladu by bylo možné spustit na libovolný výpočetní kontext. Ale doporučuje se ho spustit na počítači s více jádry s aspoň 16GB paměti a místa na disku 5GB.

## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace workbench

Vytvořte nový projekt s použitím v tomto příkladu jako šablony:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** stránky, klikněte na tlačítko **+** přihlášení a výběr **nový projekt**
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Q & A odpovídající" a vyberte šablonu
5.  Klikněte na **Vytvořit**

## <a name="data-description"></a>Popis dat

Datové sady použité v tomto příkladu je Exchange Data výpisem zásobníku uloženou v [archive.org](https://archive.org/details/stackexchange). Tato data jsou na anonymizované výpis paměti veškerý obsah za uživatele [Stack Exchange sítě](https://stackexchange.com/). Každá lokalita v síti je formátován jako samostatné archivu skládající se z XML soubory ZIP prostřednictvím použít kompresi bzip2 7-zip. Každý server archiv obsahuje příspěvky, uživatelé, hlasování, komentáře, PostHistory a PostLinks. 

### <a name="data-source"></a>Zdroj dat

V tomto příkladu [publikuje data (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) a [PostLinks data (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) webu Stack Overflow. Informace o dokončení schématu, najdete v článku [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

`PostTypeId` Pole v odesílá data označuje, jestli je příspěvek `Question` nebo `Answer`. `PostLinkTypeId` Pole v datech PostLinks označuje, zda dva příspěvky jsou propojeny nebo duplicitní. Dotaz příspěvky obvykle obsahují některé značky, které jsou klíčová slova, které kategorizují dotaz s použitím jiné podobné/duplicitní otázky. Existují některé značky s vysokou frekvencí, jako například `javascript`, `java`, `c#`, `php` atd., jsou tvořeny větší počet příspěvků otázku. V tomto příkladu, podmnožinu Q & páry s `javascript` značka je extrahován.

Additionionally, příspěvek dotaz může souviset s více odpovědí příspěvky nebo duplicitní otázka příspěvky. Pro vytvoření seznamu Nejčastější dotazy týkající se z těchto dvou datových sad, jsou považovány za kritérií data kolekce. Tři sady shromážděná data jsou vybrány pomocí skriptu SQL, která není zahrnutá v tomto příkladu. Výsledná data popis vypadá takto:

- `Original Questions (Q)`: Příspěvky dotaz se zobrazí výzva a odpovědi na webu Stack Overflow.
- `Duplications (D)`: Otázka příspěvky duplicitní další již existující dotazy (`PostLinkTypeId = 3`), které jsou na původní otázky. Duplicit se považují za sémantické ekvivalenty původní dotazy v tom smyslu, že odpovědi na původní dotaz najdete tady také odpovědi nové duplicitní otázka.
- `Answers (A)`: Každý původní dotaz a jeho duplicit může propojit více odpovědí příspěvky. Tento příklad vybere pouze odpovědí příspěvku, který je buď přijal původní Autor (filtrované podle `AcceptedAnswerId`) nebo má nejvyšší skóre (seřazených podle `Score`) v původní dotazy. 

Kombinace těchto tří datové sady vytvoří funkce Q & A dvojice kde odpověď (A) je namapovaný na jednu původní dotaz (Q) a více duplicitní otázky (D) jak je znázorněno v následujícím diagramu dat:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Struktura dat

Schéma dat a odkazy na přímé stažení ze tří datových sad najdete v následující tabulce:

| Datová sada | Pole | Typ | Popis
| ----------|------------|------------|--------
| [Dotazy](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | ID | Řetězec | Dotaz jedinečné ID (primární klíč)
|  | AnswerId | Řetězec | ID jedinečné odpověď na otázku
|  | Text0 | Řetězec | Nezpracovaný text data, včetně název a text na otázku
|  | Datum vytvoření | Časové razítko | Pokud byla požádána dotaz časové razítko
| [duplicitní](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | ID | Řetězec | Duplikace jedinečné ID (primární klíč)
|  | AnswerId | Řetězec | Odpověď ID přidružené k opakování
|  | Text0 | Řetězec | Nezpracovaný text data, včetně názvu duplicity a provádí tělo
|  | Datum vytvoření | Časové razítko | Pokud byla požádána opakování časové razítko
| [odpovědi](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | ID | Řetězec | Odpověď jedinečné ID (primární klíč)
|  | text0 | Řetězec | Data nezpracovaný text odpovědi


## <a name="scenario-structure"></a>Struktura scénář

Příklad odpovídající funkce Q & A jsou nabízena prostřednictvím tři typy souborů. Prvním typem je řada poznámkové bloky Jupyter, které obsahují podrobné popisy celého pracovního postupu. Druhým typem je sadu Python soubory obsahují vlastní moduly Pythonu pro učení a funkce extrakce frází. Tyto moduly Pythonu jsou dostatečně obecný nejen poskytovat v tomto příkladu, ale také dalších případů použití. Typ třetí je sada soubory Pythonu ladit hyperparametry a sledovat výkon modelů pomocí Azure Machine Learning Workbench.

Soubory v tomto příkladu jsou průchody uspořádány takto.

| Název souboru | Typ | Popis
| ----------|------------|--------
| `Image` | Složka | Složku, která slouží k ukládání imagí pro soubor README
| `notebooks` | Složka | Složka Poznámkové bloky Jupyter
| `modules` | Složka | Složka moduly Pythonu
| `scripts` | Složka | Složka se soubory Pythonu
| `notebooks/Part_1_Data_Preparation.ipynb` | Poznámkový blok Jupyter | Přístup k ukázková data, předběžně zpracovat text a příprava školení a testovací datové sady
| `notebooks/Part_2_Phrase_Learning.ipynb` | Poznámkový blok Jupyter | Další informativní fráze a tokenizaci text do kontejneru objektů a dat z slova (smyčce) reprezentace
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Poznámkový blok Jupyter | Extrahovat funkcí, trénování modelů klasifikace textu a vyhodnocení výkonu modelu
| `modules/__init__.py` | Soubor Pythonu | Soubor init balíčků Pythonu
| `modules/phrase_learning.py` | Soubor Pythonu | Moduly Pythonu používané k transformaci nezpracovaných dat a další informativní fráze
| `modules/feature_extractor.py` | Soubor Pythonu | Moduly Pythonu extrahovat funkce k trénování modelu
| `scripts/naive_bayes.py` | Soubor Pythonu | Chcete-li ladit hyperparametry v modelu Naive Bayes Pythonu
| `scripts/random_forest.py` | Soubor Pythonu | Python k vyladění hyperparametrů v doménové struktuře Random modelu
| `README.md` | Soubor markdownu | V souboru README markdownu

> [!NOTE]
> Řadu poznámkových bloků je integrována v rámci Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Ingestování a transformace

Tři kompilované datové sady jsou uložené ve službě Blob storage a se načtou `Part_1_Data_Preparation.ipynb` poznámkového bloku.  

Před trénování modelů klasifikace textu je text v otázkách Vyčištěná a předzpracovaná vyloučit fragmenty kódu. Učení bez dohledu frázi je použitá v průběhu školicí materiály další informativní sekvence více slov. Následující možnosti jsou reprezentovány jako složené slovo jednotky v podřízené snadné kontejner objektů a dat o slova (smyčce) používá modelů klasifikace textu.

Podrobný popis podrobné předběžného zpracování textu a frází učení najdete v poznámkových bloků `Part_1_Data_Preparation.ipynb` a `Part_2_Phrase_Learning.ipynb`v uvedeném pořadí.

### <a name="modeling"></a>Modelování

V tomto příkladu slouží ke stanovení skóre pro nové dotazy proti existující pole pro otázky Q & dvojice podle textu trénování modelů klasifikace, kde každé existující funkce Q & dvojice je jedinečný třídy a podmnožinu duplicitní dotazy pro každý pár funkce Q & A jsou k dispozici jako školicí materiály. V tomto příkladu původní dotazy a 75 % na duplicitní otázky, které se uchovávají po dobu školení a nedávno odeslaná 25 % duplicitní otázky jsou uložená – na víc systémů jako data pro vyhodnocení.

Klasifikační model využívá metodu skupiny stromů k agregaci tři základní třídění, včetně **Naive Bayes**, **Support Vector Machine**, a **doménové struktury Random**. V každé základní třídění `AnswerId` slouží jako název třídy a vyjádření smyčce se používá jako funkce.

Procesu trénování modelu je znázorněný v `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Vyhodnocení

Dvě různé vyhodnocení metriky se používají k vyhodnocení výkonu. 
1. `Average Rank (AR)`: označuje průměrná pozici, ve kterých se správnou odpověď nachází v seznamu párů načtený funkce Q & A (mimo úplnou sadu 103 odpovědí třídy). 
2. `Top 3 Percentage`: Určuje procento testů otázky, které se, že správné odpovědi lze načíst v první tři možnosti ve vráceném seznamu seřazené. 

Hodnocení je ukázáno v `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Závěr

Tento příklad ukazuje, jak pomocí wkt analytics techniky, jako je například fráze učení a text klasifikace, vytvářet robustní model. Také předvede, jak může pomoct Azure Machine Learning Workbench s interaktivní řešení vývoje a sledování výkonu modelu. 

K nejdůležitějším prvkům v tomto příkladu jsou:

- Otázka a odpověď odpovídající problému lze efektivně vyřešit s modelů klasifikace frázi učení a text.
- Ukázka interaktivní model vývoje pomocí Azure Machine Learning Workbench a Poznámkový blok Jupyter.
- Azure Machine Learning Workbench slouží ke správě historie spuštění a zjištěná modely hodnocení metriky pro účely porovnání pomocí protokolování. Tato funkce umožňuje rychlé hyperparametrické ladění a pomáhá identifikovat nejvýkonnější modely.


## <a name="references"></a>Odkazy

Alexander J. Hazen, Fred Richardson [ _modelování Multiword frází pomocí omezeného fráze strom pro vylepšení modelování tématu konverzační řeči_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Mluvený jazyk technologie Workshop (SLT) 2012 IEEE. IEEE, 2012.

Hazen J. Alexander, [ _Media Center školení techniky pro identifikaci tématu mluvené slovo zvuku dokumentů_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) v IEEE Transactions zvuk, řeč nebo jazyková zpracování, SV 19, ne. 8, str. 2451-2460, 2011 listopadu.
