---
title: Latentní Dirichlet ovace
titleSuffix: Azure Machine Learning
description: Naučte se používat modul Latent Dirichlet Allocation k seskupení jinak neklasifikovaného textu do několika kategorií.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109222"
---
# <a name="latent-dirichlet-allocation"></a>Latentní Dirichlet ovace

Tento článek popisuje, jak používat modul **Latent Dirichlet Allocation** v návrháři Azure Machine Learning (preview) k seskupení jinak neklasifikovaného textu do několika kategorií. 

Latent Dirichlet Allocation (LDA) se často používá při zpracování přirozeného jazyka (NLP) k nalezení textů, které jsou podobné. Dalším běžným termínem je *modelování témat*.

Tento modul přebírá sloupec textu a generuje tyto výstupy:

+ Zdrojový text spolu se skóre pro každou kategorii

+ Matice funkcí obsahující extrahované termíny a koeficienty pro každou kategorii

+ Transformace, kterou můžete uložit a znovu použít na nový text používaný jako vstup

Tento modul používá knihovnu scikit-learn. Další informace o scikit-learn najdete v tématu [GitHub úložiště, který obsahuje kurzy a vysvětlení algoritmu.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Více o Latent Dirichlet Allocation (LDA)

Obecně řečeno, LDA není metoda pro klasifikaci jako takový, ale používá generativní přístup. To znamená, že nemusíte poskytovat známé popisky třída a potom odvodit vzorky.  Místo toho algoritmus generuje pravděpodobnostní model, který se používá k identifikaci skupin témat. Pravděpodobnostní model můžete použít ke klasifikaci existujících případů školení nebo nových případů, které modelu poskytnete jako vstup.

Generativní model může být vhodnější, protože se vyhýbá vytváření žádné silné předpoklady o vztahu mezi textem a kategorie a používá pouze rozdělení slov matematicky modeltémata.

+ Teorie je popsána v tomto článku, k dispozici ve formátu PDF ke stažení: [Latent Dirichlet Přidělení: Blei, Ng, a Jordánsko](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ Implementace v tomto modulu je založena na [knihovně scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) pro LDA.

Další informace naleznete v části [Technické poznámky.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Jak nakonfigurovat Latent Dirichlet Allocation

Tento modul vyžaduje datovou sadu, která obsahuje sloupec textu, nezpracovaný nebo předem zpracovaný.

1. Přidejte do svého potrubí modul **Latent Dirichlet Allocation.**

2. Jako vstup pro modul zadejte datovou sadu obsahující jeden nebo více textových sloupců.

3. V **případě cílových sloupců**zvolte jeden nebo více sloupců obsahujících text, které chcete analyzovat.

    Můžete zvolit více sloupců, ale musí být typu datového typu řetězce.

    Obecně platí, že vzhledem k tomu, že LDA vytvoří z textu velkou matici prvků, obvykle analyzujete jeden textový sloupec.

4. Do **pole Počet témat k modelování**zadejte celé číslo mezi 1 a 1000, které označuje, kolik kategorií nebo témat chcete ze vstupního textu odvodit.

    Ve výchozím nastavení je vytvořeno 5 témat.

5. U **N-gramů**určete maximální délku N gramů generovaných během hašování.

    Výchozí hodnota je 2, což znamená, že jsou generovány bigrams a unigrams.

6. Vyberte volbu **Normalizovat** pro převod výstupních hodnot na pravděpodobnosti. Proto místo reprezentace transformovaných hodnot jako celá čísla by se hodnoty v datové sadě výstupu a funkce transformovaly následovně:

    + Hodnoty v datové sadě budou reprezentovány `P(topic|document)`jako pravděpodobnost, kde .

    + Hodnoty v matici tématu funkce budou `P(word|topic)`reprezentovány jako pravděpodobnost, kde .

    > [!NOTE] 
    > V Návrháři Azure Machine Learning (preview), protože knihovna, kterou jsme založili, scikit-learn, již nepodporuje nenormalizované *doc_topic_distr* výstup z verze 0.19, proto v tomto modulu **normalize** parametr lze použít pouze na výstup **matice téma funkce,** **transformované datové sady** výstup je vždy normalizován.

7. Vyberte možnost, **Zobrazit všechny možnosti**a nastavte ji na HODNOTU TRUE, pokud chcete zobrazit a pak nastavit další upřesňující parametry.

    Tyto parametry jsou specifické pro scikit-learn implementace LDA. Tam jsou některé dobré návody o LDA v scikit-learn, stejně jako oficiální [scikit-learn dokument](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho parametr**. Zadejte předchozí pravděpodobnost pro řídkou rozdělení tématu. Odpovídá `topic_word_prior` parametru sklearn. Hodnotu 1 byste použili, pokud očekáváte, že rozdělení slov je ploché; tedy všechna slova jsou považována za rovnocenná. Pokud si myslíte, že většina slov se objeví řídce, můžete nastavit na mnohem nižší hodnotu.

    + **Alfa parametr**. Určete předchozí pravděpodobnost pro sparsity tloušťky tématu na dokument.  Odpovídá `doc_topic_prior` parametru sklearn.

    + **Odhadovaný počet dokumentů**. Zadejte číslo, které představuje nejlepší odhad počtu dokumentů (řádků), které budou zpracovány. To umožňuje modulu přidělit tabulku hash dostatečné velikosti.  Odpovídá parametru `total_samples` v scikit-learn.

    + **Velikost dávky**. Zadejte číslo, které označuje, kolik řádků má být zahrnuto do každé dávky textu odeslaného modelu LDA. Odpovídá parametru `batch_size` v scikit-learn.

    + **Počáteční hodnota iterace použitá v plánu aktualizace učení**. Zadejte počáteční hodnotu, která zmírňuje míru učení pro rané iterace v online učení. Odpovídá parametru `learning_offset` v scikit-learn.

    + **Napájení použité na iteraci během aktualizací**. Uveďte úroveň výkonu aplikovaného na počet iterací, aby bylo možné řídit rychlost učení během online aktualizací. Odpovídá parametru `learning_decay` v scikit-learn.

    + **Počet průchodů přes data**. Určete maximální počet, kolikrát bude algoritmus přejíždět data. Odpovídá parametru `max_iter` v scikit-learn.

8. Vyberte **možnost, Vytvořit slovník ngramů** nebo **Vytvořit slovník ngramů před LDA**, pokud chcete vytvořit n-gram seznam v počáteční průchod, před klasifikací textu.

    Pokud vytvoříte počáteční slovník předem, můžete později použít slovník při kontrole modelu. Možnost mapovat výsledky na text spíše než na číselné indexy je obecně jednodušší pro interpretaci. Uložení slovníku však bude trvat déle a použít další úložiště.

9. Do **pole Maximální velikost slovníku ngram**zadejte celkový počet řádků, které lze vytvořit ve slovníku n-gram.

    Tato možnost je užitečná pro řízení velikosti slovníku. Pokud však počet ngramů ve vstupu tuto velikost překročí, může dojít ke kolizím.

10. Odešlete potrubí. Modul LDA používá Bayesovou teorém k určení, která témata mohou být spojena s jednotlivými slovy. Slova nejsou výlučně spojena s žádnými tématy nebo skupinami; místo toho má každý n-gram učenou pravděpodobnost, že bude spojen s některou z objevených tříd.

## <a name="results"></a>Výsledky

Modul má dva výstupy:

+ **Transformovaná datová sada**: Obsahuje vstupní text a zadaný počet zjištěných kategorií spolu s skóre pro každý příklad textu pro každou kategorii.

+ **Matice tématu funkce**: Sloupec zcela vlevo obsahuje funkci extrahovaného textu a pro každou kategorii je sloupec obsahující skóre pro danou funkci v této kategorii.


### <a name="lda-transformation"></a>Transformace LDA

Tento modul také výstupy *LDA transformace,* která platí LDA pro datovou sadu.

Tuto transformaci můžete uložit podle registru datové sady v části **Výstupy+protokoly** kartu v pravém podokně modulu a znovu použít pro jiné datové sady. To může být užitečné, pokud jste trénovali na velkém korpusu a chcete znovu použít koeficienty nebo kategorie.

### <a name="refining-an-lda-model-or-results"></a>Zpřesnění modelu LDA nebo výsledků

Obvykle nelze vytvořit jeden model LDA, který bude splňovat všechny potřeby, a dokonce i model určený pro jednu úlohu může vyžadovat mnoho iterací ke zlepšení přesnosti. Doporučujeme vyzkoušet všechny tyto metody ke zlepšení modelu:

+ Změna parametrů modelu
+ Použití vizualizace k pochopení výsledků
+ Získání zpětné vazby odborníků předmětu zjistit, zda generované témata jsou užitečné.

Kvalitativní opatření mohou být také užitečná pro hodnocení výsledků. Chcete-li vyhodnotit výsledky modelování témat, zvažte:

+ Přesnost - Jsou podobné položky opravdu podobné?
+ Rozmanitost - Může model rozlišovat mezi podobnými položkami, pokud je to nutné pro obchodní problém?
+ Škálovatelnost – funguje na široké škále kategorií textu nebo pouze na úzké cílové doméně?

Přesnost modelů založených na LDA lze často zlepšit pomocí zpracování přirozeného jazyka k čištění, shrnutí a zjednodušení nebo kategorizaci textu. Například následující techniky, všechny podporované v Azure Machine Learning, může zlepšit přesnost klasifikace:

+ Zastavit odebrání slova

+ Normalizace případu

+ Lemmatizace nebo vyplývající

+ Rozpoznávání pojmenovaných entit

Další informace naleznete [v tématu Preprocess Text](preprocess-text.md).

V návrháři můžete také použít knihovny R nebo Python pro zpracování textu: [Spustit Skript R](execute-r-script.md), Spustit skript [Pythonu](execute-python-script.md)



## <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobnosti implementace, tipy a odpovědi na často kladené otázky.

### <a name="implementation-details"></a>Podrobnosti implementace

Ve výchozím nastavení jsou distribuce výstupů pro transformovnou datovou sadu a matici tématu funkcí normalizovány jako pravděpodobnosti.

+ Transformovaná datová sada je normalizována jako podmíněná pravděpodobnost témat daného dokumentu. V tomto případě se součet každého řádku rovná 1.

+ Matice tématu funkce je normalizována jako podmíněná pravděpodobnost slov dané téma. V tomto případě se součet každého sloupce rovná 1.

> [!TIP]
> V některých případě modul může vrátit prázdné téma, což je nejčastěji způsobeno pseudo-náhodné inicializace algoritmu.  Pokud k tomu dojde, můžete zkusit změnit související parametry, jako je například maximální velikost slovníku N-gram nebo počet bitů, které mají být používány pro zahašování funkcí.

### <a name="lda-and-topic-modeling"></a>LDA a modelování témat

Latent Dirichlet Allocation (LDA) se často používá pro *modelování témat založených na obsahu*, což v podstatě znamená učící se kategorie z neklasifikovaného textu. V modelování témat založených na obsahu je téma rozdělením slov.

Předpokládejme například, že jste poskytli soubor recenzí zákazníků, který obsahuje mnoho, mnoho produktů. Text recenzí, které byly odeslány mnoha zákazníky v průběhu času by obsahovat mnoho termínů, z nichž některé se používají ve více tématech.

**Téma** identifikované procesem LDA může představovat recenze pro jednotlivý produkt A nebo může představovat skupinu recenzí produktů. Chcete-li LDA, téma samo o sobě je jen rozdělení pravděpodobnosti v čase pro sadu slov.

Termíny jsou zřídka exkluzivní pro jeden produkt, ale mohou odkazovat na jiné produkty, nebo být obecné podmínky, které se vztahují na všechno ("skvělé", "hrozné"). Jiné termíny mohou být šumslova.  Je však důležité si uvědomit, že metoda LDA nemá za cíl zachytit všechna slova ve vesmíru, nebo pochopit, jak slova jsou příbuzné, kromě pravděpodobností co-výskytu. Může seskupit pouze slova, která byla použita v cílové doméně.

Po výpočtu termínindexy byly vypočteny, jednotlivé řádky textu jsou porovnány pomocí měření podobnosti založené na vzdálenosti, chcete-li zjistit, zda dva kusy textu jsou jako každý jiný.  Můžete například zjistit, že produkt má více názvů, které jsou silně korelovány. Nebo můžete zjistit, že silně negativní termíny jsou obvykle spojeny s konkrétním produktem. Míru podobnosti můžete použít k identifikaci souvisejících termínů a k vytvoření doporučení.

###  <a name="module-parameters"></a>Parametry modulu

|Name (Název)|Typ|Rozsah|Nepovinné|Výchozí|Popis|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Cílový sloupec (sloupce)|Výběr sloupce||Požaduje se|Funkce stringfeature|Název nebo index cílového sloupce|  
|Počet témat k modelu|Integer|[1;1000]|Požaduje se|5|Modelování distribuce dokumentů podle témat N|  
|N-gramů|Integer|[1;10]|Požaduje se|2|Pořadí N gramů generovaných během hašování|  
|Normalizovat|Logická hodnota|Pravda nebo nepravda|Požaduje se|true|Normalizovat výstup na pravděpodobnosti.  Transformovaná datová sada bude P (téma&#124;dokumentu) a matice tématu funkcí bude P (téma&#124;slovo)|  
|Zobrazit všechny možnosti|Logická hodnota|Pravda nebo nepravda|Požaduje se|False|Představuje další parametry specifické pro scikit-learn online LDA|  
|Rho parametr|Plovoucí desetinná čárka|[0.00001;1.0]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|0.01|Téma slovo předchozí distribuce|  
|Alfa parametr|Plovoucí desetinná čárka|[0.00001;1.0]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|0.01|Téma dokumentu před distribucí|  
|Odhadovaný počet dokumentů|Integer|[1;int. Maximální hodnota]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|1000|Odhadovaný počet dokumentů (odpovídá total_samples parametru)|  
|Velikost šarže|Integer|[1;1024]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|32|Velikost šarže|  
|Počáteční hodnota iterace použitá v plánu aktualizace rychlosti učení|Integer|[0;int. Maximální hodnota]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|0|Počáteční hodnota, která downweights učení sazba pro rané iterací. Odpovídá parametru learning_offset|  
|Výkon použité na iteraci během aktualizací|Plovoucí desetinná čárka|[0.0;1.0]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|0,5|Výkon použitý na počet iterace za účelem řízení rychlosti učení. Odpovídá learning_decay parametru |  
|Počet trénovacích iterací|Integer|[1;1024]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|25|Počet trénovacích iterací|  
|Vytvořit slovník ngramů|Logická hodnota|Pravda nebo nepravda|Použije, když *není* zaškrtnuté políčko **Zobrazit všechny možnosti.**|True|Vytvoří slovník ngramů před výpočtem LDA. Užitečné pro kontrolu a interpretaci modelů|  
|Maximální velikost slovníku ngram|Integer|[1;int. Maximální hodnota]|Platí, když je možnost **Vytvořit slovník ngramů** true|20000|Maximální velikost slovníku ngrams. Pokud počet žetonů ve vstupu tuto velikost překročí, může dojít ke kolizím|  
|Počet bitů, které mají být určeny pro zapisování funkcí|Integer|[1;31]|Platí, když *není* zaškrtnuto políčko **Zobrazit všechny možnosti** a **je nepravdivý slovník sestavení ngramů.**|12|Počet bitů, které mají být určeny pro zapisování funkcí| 
|Vytvořit slovník ngramů před LDA|Logická hodnota|Pravda nebo nepravda|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti.**|True|Vytvoří slovník ngramů před LDA. Užitečné pro kontrolu a interpretaci modelů|  
|Maximální počet ngramů ve slovníku|Integer|[1;int. Maximální hodnota]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti** a volba **Vytvořit slovník ngramů** je True.|20000|Maximální velikost slovníku. Pokud počet žetonů ve vstupu tuto velikost překročí, může dojít ke kolizím|  
|Počet bitů hash|Integer|[1;31]|Použije se, když je zaškrtnuto políčko **Zobrazit všechny možnosti** a možnost **Vytvořit slovník ngramů** je False.|12|Počet bitů, které mají být používány během zapisování funkcí|   


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.   
Seznam chyb specifických pro moduly naleznete v tématu [Výjimky a kódy chyb pro návrháře](designer-error-codes.md).
