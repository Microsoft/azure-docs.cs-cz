---
title: Model LDA (Latent Dirichlet Allocation)
titleSuffix: Azure Machine Learning
description: Naučte se používat modul Dirichletův pro přidělování latentních přihlášení k seskupení jiného neklasifikovaného textu do několika kategorií.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80109222"
---
# <a name="latent-dirichlet-allocation"></a>Model LDA (Latent Dirichlet Allocation)

Tento článek popisuje, jak použít modul **Dirichletův Allocation** v Návrháři Azure Machine Learning (Preview) k seskupení dalších neklasifikovaných textů do několika kategorií. 

Dirichletův (LDA) se často používá při zpracování přirozeného jazyka (NLP) k nalezení podobných textů. Dalším běžným termínem je *modelování témat*.

Tento modul přebírá sloupec textu a generuje tyto výstupy:

+ Zdrojový text spolu se skóre pro každou kategorii

+ Matice funkcí obsahující extrahované výrazy a koeficienty pro každou kategorii

+ Transformace, kterou můžete uložit a znovu použít na nový text použitý jako vstup

Tento modul používá knihovnu scikit-učení. Další informace o scikit – informace najdete v tématu [úložiště GitHub, které obsahuje kurzy a vysvětlení algoritmu.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Další informace o Dirichletův přidělování latentních prostředků (LDA)

Obecně řečeno, LDA není metoda klasifikace za se, ale používá regenerační přístup. To znamená, že nemusíte zadávat známé jmenovky tříd a potom odvodit vzory.  Místo toho algoritmus generuje model pravděpodobnostní, který se používá k identifikaci skupin témat. Pomocí modelu pravděpodobnostní můžete klasifikovat buď existující školicí případy, nebo nové případy, které do modelu zadáte jako vstup.

Tento regenerační model může být vhodnější, protože se vyhne vytváření jakýchkoli silných předpokladů týkajících se vztahu mezi textem a kategoriemi a používá pouze rozdělení slov na matematicky modelová témata.

+ Teoretická je diskutována v tomto dokumentu, která je k dispozici jako soubor PDF ke stažení: [latentní Dirichletův Alokace: Blei, NG a Jordánsko](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ Implementace v tomto modulu je založená na [knihovně scikit-učení](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) pro LDA.

Další informace najdete v části [technické poznámky](#technical-notes) .

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Jak nakonfigurovat Dirichletův přidělování latentních prostředků

Tento modul vyžaduje datovou sadu, která obsahuje sloupec textu, a to buď nezpracovaná, nebo předzpracovaná.

1. Přidejte do svého kanálu Dirichletův modul pro **přidělení latentních prostředků** .

2. Jako vstup pro modul zadejte datovou sadu obsahující jeden nebo více textových sloupců.

3. Pro **cílové sloupce**vyberte jeden nebo více sloupců obsahujících text, který chcete analyzovat.

    Můžete zvolit více sloupců, ale musí být datového typu řetězec.

    Obecně platí, že vzhledem k tomu, že LDA vytvoří z textu celou matrici funkcí, obvykle budete analyzovat jeden textový sloupec.

4. Pro **počet témat pro model**zadejte celé číslo od 1 do 1000, které určuje, kolik kategorií nebo témat chcete ze vstupního textu odvodit.

    Ve výchozím nastavení se vytvoří 5 témat.

5. U **n-gramů**zadejte maximální délku N-gramů vygenerovanou během generování hodnoty hash.

    Výchozí hodnota je 2, což znamená, že jsou vygenerovány bigrams i unigrams.

6. Vyberte možnost **normalizovat** pro převod výstupních hodnot na pravděpodobnost. Proto místo reprezentace transformovaných hodnot jako celých čísel, hodnoty ve výstupu a datové sadě funkcí budou transformovány takto:

    + Hodnoty v datové sadě budou reprezentovány jako pravděpodobnost, `P(topic|document)`kde.

    + Hodnoty v matici tématu funkce budou vyjádřeny jako pravděpodobnost, kde `P(word|topic)`.

    > [!NOTE] 
    > V Azure Machine Learning Designer (Preview), protože knihovna, kterou používáme, scikit-učí, již nepodporuje nenormalizovaný *doc_topic_distr* výstup z verze 0,19, proto v tomto modulu může být parametr **Normalize** použit pouze pro výstup **matice funkce** , **transformovaná datová sada** je vždy normalizována.

7. Vyberte možnost, **Zobrazit všechny možnosti**a pak ji nastavte na hodnotu true, pokud chcete zobrazit a následně nastavit další upřesňující parametry.

    Tyto parametry jsou specifické pro implementaci LDA scikit-učení. K dispozici jsou některé dobré kurzy týkající se LDA v scikit a také oficiální [dokument scikit-učení](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parametr ró**. Poskytněte předchozí pravděpodobnost pro řídkosti distribucí tématu. Odpovídá `topic_word_prior` parametru skriptu sklearn. Pokud očekáváte, že rozdělení slov je ploché, použijte hodnotu 1. To znamená, že všechna slova se předpokládají equiprobable. Pokud se domníváte, že se většina slov objevuje zhuštěně, můžete ji nastavit na mnohem nižší hodnotu.

    + **Parametr alfa** Určete předchozí pravděpodobnost pro řídkostii vah pro každé dokument.  Odpovídá `doc_topic_prior` parametru skriptu sklearn.

    + **Odhadovaný počet dokumentů** Zadejte číslo, které představuje nejlepší odhad počtu dokumentů (řádků), které se budou zpracovávat. Díky tomu může modul přidělovat zatřiďovací tabulku s dostatečnou velikostí.  Odpovídá `total_samples` parametru v scikit-učení.

    + **Velikost dávky** Zadejte číslo, které označuje počet řádků, které se mají zahrnout do každé dávky textu odeslané do LDA modelu. Odpovídá `batch_size` parametru v scikit-učení.

    + **Počáteční hodnota iterace použitá v plánu aktualizace kurzů** Zadejte počáteční hodnotu, kterou downweights výukový kurz pro počáteční iterace při online učení. Odpovídá `learning_offset` parametru v scikit-učení.

    + **K iteraci se aplikuje napájení během aktualizací**. Určuje úroveň napájení, která se má použít pro počet iterací za účelem řízení míry učení během online aktualizací. Odpovídá `learning_decay` parametru v scikit-učení.

    + **Počet průchodů daty**. Zadejte maximální počet pokusů, kolikrát se algoritmus v datech cyklicky přeskočí. Odpovídá `max_iter` parametru v scikit-učení.

8. Vyberte možnost, **slovník sestavení ngrams** nebo **slovníku sestavení ngrams před LDA**, pokud chcete vytvořit seznam n-gramů v počátečním průchodu před zařazením textu.

    Pokud vytvoříte počáteční slovník předem, můžete ho později použít při kontrole modelu. Schopnost mapovat výsledky na text, nikoli na číselné indexy, je obecně snazší pro výklad. Uložení slovníku ale trvá déle a bude používat další úložiště.

9. Pro **maximální velikost ngram slovníku**zadejte celkový počet řádků, které je možné vytvořit ve slovníku n-gramů.

    Tato možnost je užitečná pro řízení velikosti slovníku. Pokud však počet ngrams ve vstupu překračuje tuto velikost, může dojít k kolizí.

10. Odešlete kanál. Modul LDA používá Bayes věta k určení toho, jaká témata můžou být přidružená k jednotlivým slovům. Slova nejsou výhradně přidružena k žádným tématům nebo skupinám; místo toho má každý n-gram zjištěnou pravděpodobnost přidružení k libovolné zjištěné třídě.

## <a name="results"></a>Výsledky

Modul má dva výstupy:

+ **Transformovaná datová sada**: obsahuje vstupní text a zadaný počet zjištěných kategorií spolu s skóre pro každý textový příklad každé kategorie.

+ **Tabulka tématu funkce**: sloupec umístěný nejvíce vlevo obsahuje funkci extrahovaný text a sloupec pro každou kategorii, který obsahuje skóre této funkce v této kategorii.


### <a name="lda-transformation"></a>Transformace LDA

Tento modul také výstupuje *transformaci LDA* , která aplikuje LDA na datovou sadu.

Tuto transformaci můžete uložit pomocí položky zaregistrovat datovou sadu na kartě **výstupy + protokoly** v pravém podokně modulu a znovu ji použít pro jiné datové sady. To může být užitečné, pokud jste proučeni velké corpus a chcete znovu použít koeficienty nebo kategorie.

### <a name="refining-an-lda-model-or-results"></a>Úprava modelu nebo výsledků LDA

Typicky nemůžete vytvořit jeden model LDA, který bude vyhovovat všem potřebám, a dokonce i model navržený pro jeden úkol může vyžadovat mnoho iterací, aby se zlepšila přesnost. Doporučujeme, abyste si vyzkoušeli všechny tyto metody pro zlepšení modelu:

+ Změna parametrů modelu
+ Pochopení výsledků pomocí vizualizace
+ Získání názoru na odborníky na danou problematiku, aby se ověřilo, jestli jsou vygenerovaná témata užitečná.

Kvalitativní míry mohou být užitečné také pro vyhodnocení výsledků. K vyhodnocení výsledků modelování tématu Vezměte v úvahu:

+ Přesnost – podobné položky jsou ve skutečnosti podobné?
+ Rozmanitost – může model odlišit podobné položky, pokud je to potřeba pro obchodní potíže?
+ Škálovatelnost – funguje na široké škále textových kategorií nebo jenom na úzké cílové doméně?

Přesnost modelů založených na LDA se dá často zlepšit pomocí zpracování přirozeného jazyka pro čištění, sumarizaci a zjednodušení nebo zařazování textu do kategorií. Například následující techniky, které jsou podporované v Azure Machine Learning, můžou zlepšit přesnost klasifikace:

+ Zastavit odebrání slova

+ Normalizace případu

+ Lemmatizátor nebo předzpracování nebo odvozování

+ Rozpoznávání pojmenovaných entit

Další informace najdete v tématu [předzpracování textu](preprocess-text.md).

V Návrháři můžete také použít knihovny R nebo Python pro zpracování textu: [Spustit skript jazyka r](execute-r-script.md), [Spustit skript jazyka Python](execute-python-script.md)



## <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

### <a name="implementation-details"></a>Podrobnosti implementace

Ve výchozím nastavení jsou distribuce výstupů pro transformovaná datová sada a témata funkcí a témat normalizovány jako pravděpodobnosti.

+ Transformovaná datová sada je normalizována jako podmíněná pravděpodobnost pro daný dokument. V tomto případě se součet každého řádku rovná 1.

+ Matice funkce – téma je normalizována jako podmíněná pravděpodobnost slov v daném tématu. V tomto případě se součet každého sloupce rovná 1.

> [!TIP]
> V některých případech může modul vracet prázdné téma, které je nejčastěji způsobeno pseudo náhodným inicializací algoritmu.  Pokud k tomu dojde, můžete zkusit změnit související parametry, jako je maximální velikost slovníku N-gramů, nebo počet bitů, které se mají použít pro funkci hashování funkcí.

### <a name="lda-and-topic-modeling"></a>LDA a modelování témat

Pro *modelování tématu založené na obsahu*se často používá latentní Dirichletův přidělování (LDA), které v podstatě znamená výukové kategorie z neklasifikovaného textu. V tématu modelování témat na základě obsahu je téma rozdělením do slov.

Předpokládejme například, že jste poskytli corpus recenze zákazníků, které obsahují mnoho různých produktů. Text revizí, které byly odeslány mnoha zákazníky v průběhu času, by obsahoval mnoho termínů, z nichž některé jsou používány ve více tématech.

**Téma** , které je identifikováno procesem LDA, může představovat recenze pro jednotlivé produkty a nebo může představovat skupinu revizí produktu. Do LDA, samotné téma je pouze rozdělení pravděpodobnosti v průběhu času pro sadu slov.

Podmínky jsou zřídka exkluzivní pro libovolný produkt, ale můžou odkazovat na jiné produkty nebo na obecné podmínky, které se vztahují na všechno ("Skvělé", "awful"). Jinými slovy můžou být hluková slova.  Je však důležité pochopit, že metoda LDA neznamená, že zachytává všechna slova v celém universu, nebo pokud chcete pochopit, jakým způsobem se slova vztahují, mimo pravděpodobnost výskytu korelace. Může seskupit jenom slova, která se použila v cílové doméně.

Po vypočítání indexů jsou jednotlivé řádky textu porovnány pomocí míry podobnosti na základě vzdálenosti, aby bylo možné určit, zda jsou dva části textu navzájem podobné.  Můžete například zjistit, že má produkt více názvů, které jsou silně korelační. Nebo se můžete setkat s tím, že silně záporné výrazy jsou obvykle spojeny s konkrétním produktem. Pomocí míry podobnosti můžete identifikovat související výrazy a vytvořit doporučení.

###  <a name="module-parameters"></a>Parametry modulu

|Název|Typ|Rozsah|Nepovinné|Výchozí|Popis|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Cílový sloupec (y)|Výběr sloupce||Požaduje se|StringFeature|Název nebo index cílového sloupce|  
|Počet témat pro model|Integer|[1; 1000]|Požaduje se|5|Modelování distribuce dokumentu před N tématy|  
|N-gramů|Integer|[1; 10]|Požaduje se|2|Pořadí N-gramů generovaných během hashace|  
|Normalizovat|Logická hodnota|True nebo false|Požaduje se|true|Normalizuje výstup na pravděpodobnost.  Transformovaný objekt DataSet bude P (téma&#124;dokumentu) a matice tématu funkce bude P (Word&#124;téma).|  
|Zobrazit všechny možnosti|Logická hodnota|True nebo false|Požaduje se|False|Uvede další parametry specifické pro scikit – Přečtěte si online LDA|  
|Ró – parametr|Plovoucí desetinná čárka|[0.00001; 1.0]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0,01|Předchozí distribuce slova tématu|  
|Parametr alfa|Plovoucí desetinná čárka|[0.00001; 1.0]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0,01|Téma předchozí distribuce dokumentu|  
|Odhadovaný počet dokumentů|Integer|[1; int. MaxValue|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|1000|Odhadovaný počet dokumentů (odpovídá parametru total_samples)|  
|Velikost dávky|Integer|[1; 1024]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|32|Velikost dávky|  
|Počáteční hodnota iterace použitá v plánu aktualizace studijních kurzů|Integer|[0; int. MaxValue|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0|Počáteční hodnota, která downweights kurzů pro počáteční iterace. Odpovídá parametru learning_offset|  
|Zapnutí pro iteraci během aktualizací|Plovoucí desetinná čárka|[0,0; 1,0]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0,5|Napájení použité pro počet iterací za účelem řízení míry učení. Odpovídá parametru learning_decay |  
|Počet iterací cvičení|Integer|[1; 1024]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|25|Počet iterací cvičení|  
|Sestavit slovník pro ngrams|Logická hodnota|True nebo false|Platí v případě, že *není* zaškrtnuté políčko **Zobrazit všechny možnosti** .|True|Vytvoří slovník ngrams před výpočetním LDA. Užitečné pro kontrolu a výklad modelu|  
|Maximální velikost ngram slovníku|Integer|[1; int. MaxValue|Platí v případě, že je hodnota **ngrams slovníku pro možnost Build** .|20000|Maximální velikost ngrams slovníku Pokud počet tokenů ve vstupu překračuje tuto velikost, může dojít k kolizím.|  
|Počet bitů, které se mají použít pro funkci hashování funkcí|Integer|[1; 31]|Platí v případě *, že není vybráno* zaškrtávací políčko **Zobrazit všechny možnosti** a **slovník sestavení ngrams** je false.|12|Počet bitů, které se mají použít pro funkci hashování funkcí| 
|Sestavit slovník ngrams před LDA|Logická hodnota|True nebo false|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|True|Vytvoří slovník ngrams před LDA. Užitečné pro kontrolu a výklad modelu|  
|Maximální počet ngrams ve slovníku|Integer|[1; int. MaxValue|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** a možnost **sestavit slovník Ngrams** má hodnotu true.|20000|Maximální velikost slovníku Pokud počet tokenů ve vstupu překračuje tuto velikost, může dojít k kolizím.|  
|Počet bitů hash|Integer|[1; 31]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** a možnost **sestavit slovník ngrams** je false.|12|Počet bitů, které se mají použít při vytváření hodnot hash funkcí|   


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.   
Seznam chyb specifických pro moduly naleznete v tématu [výjimky a kódy chyb pro návrháře](designer-error-codes.md).
