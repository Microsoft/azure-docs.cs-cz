---
title: 'Dirichletův přidělování latentních: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul Dirichletův pro přidělování latentních přihlášení k seskupení dalších neklasifikovaných textů do kategorií.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f9f239ea69aaf71e591a447feb300c13a45ba1a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907857"
---
# <a name="latent-dirichlet-allocation-module"></a>Modul přidělování latentních Dirichletův

Tento článek popisuje, jak použít modul Dirichletův pro přidělování latentních přihlášení v Návrháři Azure Machine Learning, aby bylo možné seskupit jinak neklasifikovaný text do kategorií. 

Latentní Dirichletův přidělování (LDA) se často používá při zpracování v přirozeném jazyce k nalezení podobných textů. Dalším běžným termínem je *modelování témat*.

Tento modul přebírá sloupec textu a generuje tyto výstupy:

+ Zdrojový text spolu se skóre pro každou kategorii

+ Matice funkcí obsahující extrahované výrazy a koeficienty pro každou kategorii

+ Transformace, kterou můžete uložit a znovu použít na nový text použitý jako vstup

Tento modul používá knihovnu scikit-učení. Další informace o scikit – informace najdete v [úložišti GitHub](https://github.com/scikit-learn/scikit-learn), které obsahuje kurzy a vysvětlení algoritmu.

## <a name="more-about-latent-dirichlet-allocation"></a>Další informace o Dirichletův přidělování latentních

LDA není všeobecně metoda pro klasifikaci. Ale používá regenerační přístup, takže nemusíte poskytovat známé jmenovky tříd a potom odvodit vzory.  Místo toho algoritmus vygeneruje model pravděpodobnostní, který se používá k identifikaci skupin témat. Pomocí modelu pravděpodobnostní můžete klasifikovat buď existující školicí případy, nebo nové případy, které do modelu zadáte jako vstup.

Můžete preferovat regenerační model, protože předejde vytváření silných předpokladů o vztahu mezi textem a kategoriemi. Používá pouze rozdělení slov na matematicky model témata.

Teoretická je diskutována v tomto dokumentu, která je k dispozici jako soubor PDF ke stažení: [latentní Dirichletův Alokace: Blei, NG a Jordánsko](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf).

Implementace v tomto modulu je založená na [knihovně scikit-učení](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) pro LDA.

Další informace najdete v části [technické poznámky](#technical-notes) .

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Jak nakonfigurovat Dirichletův přidělování latentních prostředků

Tento modul vyžaduje datovou sadu, která obsahuje sloupec textu, a to buď nezpracovaná, nebo předzpracovaná.

1. Přidejte do svého kanálu Dirichletův modul pro **přidělení latentních prostředků** .

2. Jako vstup pro modul zadejte datovou sadu, která obsahuje jeden nebo více textových sloupců.

3. Pro **cílové sloupce** vyberte jeden nebo více sloupců, které obsahují text k analýze.

    Můžete zvolit více sloupců, ale musí se jednat o datový typ **String** .

    Vzhledem k tomu, že LDA vytvoří z textu celou matrici funkcí, obvykle budete analyzovat jeden textový sloupec.

4. Pro  **počet témat pro model** zadejte celé číslo od 1 do 1000, které určuje, kolik kategorií nebo témat chcete ze vstupního textu odvodit.

    Ve výchozím nastavení se vytvoří 5 témat.

5. U **n-gramů** zadejte maximální délku N-gramů vygenerovanou během generování hodnoty hash.

    Výchozí hodnota je 2, což znamená, že jsou vygenerovány bigrams i unigrams.

6. Vyberte možnost **normalizovat** pro převod výstupních hodnot na pravděpodobnost. 

    Místo toho, aby představovaly transformované hodnoty jako celá čísla, se hodnoty ve výstupu a datové sadě funkcí transformují takto:

    + Hodnoty v datové sadě budou reprezentovány jako pravděpodobnost, kde `P(topic|document)` .

    + Hodnoty v matici tématu funkce budou vyjádřeny jako pravděpodobnost, kde `P(word|topic)` .

    > [!NOTE] 
    > V Azure Machine Learning Designer už knihovna scikit-učí nepodporuje nenormalizovaný *doc_topic_distr* výstup z verze 0,19. V tomto modulu se parametr **Normalize** dá použít jenom pro výstup *matice tématu funkce* . *Transformovaný výstup datové sady* je vždy normalizován.

7. Vyberte možnost **Zobrazit všechny možnosti** a pak ji nastavte na **hodnotu true** , pokud chcete nastavit následující rozšířené parametry.

    Tyto parametry jsou specifické pro implementaci LDA scikit-učení. K dispozici jsou některé dobré kurzy týkající se LDA v scikit a také oficiální [dokument scikit-učení](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Parametr ró**. Poskytněte předchozí pravděpodobnost pro řídkosti distribucí tématu. Tento parametr odpovídá `topic_word_prior` parametru skriptu sklearn. Pokud očekáváte, že rozdělení slov je ploché, použijte hodnotu **1** . To znamená, že všechna slova se předpokládají equiprobable. Pokud se domníváte, že se většina slov objevuje zhuštěně, můžete ji nastavit na nižší hodnotu.

    + **Parametr alfa** Určete předchozí pravděpodobnost pro řídkostii vah pro každé dokument. Tento parametr odpovídá `doc_topic_prior` parametru skriptu sklearn.

    + **Odhadovaný počet dokumentů** Zadejte číslo, které představuje nejlepší odhad počtu dokumentů (řádků), které se budou zpracovávat. Tento parametr umožňuje modulu přidělit zatřiďovací tabulku s dostatečnou velikostí. Odpovídá `total_samples` parametru v scikit-učení.

    + **Velikost dávky** Zadejte číslo, které určuje počet řádků, které se mají zahrnout do každé dávky textu odeslané do modelu LDA. Tento parametr odpovídá `batch_size` parametru v scikit-učení.

    + **Počáteční hodnota iterace použitá v plánu aktualizace kurzů** Zadejte počáteční hodnotu, která downweights výukovou rychlost při počátečních iteracích při online učení. Tento parametr odpovídá `learning_offset` parametru v scikit-učení.

    + **K iteraci se aplikuje napájení během aktualizací**. Určuje úroveň napájení, která se má použít pro počet iterací za účelem řízení míry učení během online aktualizací. Tento parametr odpovídá `learning_decay` parametru v scikit-učení.

    + **Počet průchodů daty**. Zadejte maximální počet pokusů, kolikrát se algoritmus v datech cyklicky přeskočí. Tento parametr odpovídá `max_iter` parametru v scikit-učení.

8. Vyberte **slovník sestavení ngrams** nebo **slovník sestavení ngrams před LDA**, pokud chcete vytvořit seznam n-gramů v počátečním průchodu před zařazením textu.

    Pokud vytvoříte počáteční slovník předem, můžete ho později použít při kontrole modelu. Schopnost mapovat výsledky na text, nikoli na číselné indexy, je obecně snazší pro výklad. Uložení slovníku ale trvá déle a bude používat další úložiště.

9. Pro **maximální velikost ngram slovníku** zadejte celkový počet řádků, které je možné vytvořit ve slovníku n-gramů.

    Tato možnost je užitečná pro řízení velikosti slovníku. Pokud ale počet ngrams ve vstupu překračuje tuto velikost, může dojít ke kolizím.

10. Odešlete kanál. Modul LDA používá Bayes věta k určení toho, jaká témata můžou být přidružená k jednotlivým slovům. Slova nejsou výhradně přidružena k žádným tématům nebo skupinám. Místo toho má každý n-gram zjištěnou pravděpodobnost přidružení k libovolné zjištěné třídě.

## <a name="results"></a>Výsledky

Modul má dva výstupy:

+ **Transformovaná datová sada**: Tento výstup obsahuje vstupní text, zadaný počet zjištěných kategorií a skóre pro každý textový příklad každé kategorie.

+ **Tabulka tématu funkce**: sloupec umístěný nejvíce vlevo obsahuje funkci extrahovaný text. Sloupec pro každou kategorii obsahuje skóre pro tuto funkci v této kategorii.


### <a name="lda-transformation"></a>Transformace LDA

Tento modul také výstupuje *transformaci LDA* , která aplikuje LDA na datovou sadu.

Tuto transformaci můžete uložit a znovu použít pro jiné datové sady. Tato technika může být užitečná, pokud jste pronaučili velké corpus a chcete znovu použít koeficienty nebo kategorie.

Pokud chcete tuto transformaci použít znovu, vyberte ikonu **zaregistrovat datovou sadu** na pravém panelu modulu Dirichletův přidělování latentních objektů, aby se modul zachoval v kategorii **datové sady** v seznamu modul. Pak můžete tento modul připojit k modulu [použít transformaci](apply-transformation.md) a znovu použít tuto transformaci.

### <a name="refining-an-lda-model-or-results"></a>Úprava modelu nebo výsledků LDA

Typicky nemůžete vytvořit jeden model LDA, který bude vyhovovat všem potřebám. I model navržený pro jeden úkol může vyžadovat mnoho iterací, aby se zlepšila přesnost. Doporučujeme, abyste si vyzkoušeli všechny tyto metody pro zlepšení modelu:

+ Změna parametrů modelu
+ Pochopení výsledků pomocí vizualizace
+ Získání názoru na odborníky na danou problematiku, aby zjistili, jestli jsou vygenerovaná témata užitečná

Kvalitativní míry mohou být užitečné také pro vyhodnocení výsledků. K vyhodnocení výsledků modelování tématu Vezměte v úvahu:

+ Údajů. Jsou podobné položky podobné?
+ Rozmanitost. Může model odlišit podobné položky v případě potřeby pro obchodní potíže?
+ Škálovatelnost Pracuje na široké škále kategorií textu nebo jenom na úzké cílové doméně?

Můžete často vylepšit přesnost modelů založených na LDA pomocí zpracování přirozeného jazyka pro čištění, sumarizaci a zjednodušení nebo zařazování textu do kategorií. Například následující techniky, které jsou podporované v Azure Machine Learning, můžou zlepšit přesnost klasifikace:

+ Zastavit odebrání slova

+ Normalizace případu

+ Lemmatizátor nebo předzpracování nebo odvozování

+ Rozpoznávání pojmenovaných entit

Další informace najdete v tématu [předzpracování textu](preprocess-text.md).

V Návrháři můžete také použít knihovny R nebo Python pro zpracování textu: [Spustit skript jazyka r](execute-r-script.md),  [Spustit skript jazyka Python](execute-python-script.md).



## <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

### <a name="implementation-details"></a>Podrobnosti o implementaci

Ve výchozím nastavení jsou distribuce výstupů pro transformovaná datová sada a matice témat funkcí normalizovány jako pravděpodobnosti:

+ Transformovaná datová sada je normalizována jako podmíněná pravděpodobnost pro daný dokument. V tomto případě se součet každého řádku rovná 1.

+ Matice funkce – téma je normalizována jako podmíněná pravděpodobnost slov v daném tématu. V tomto případě se součet každého sloupce rovná 1.

> [!TIP]
> V některých případech může modul vracet prázdné téma. Většinou je příčinou pseudo náhodná inicializace algoritmu. Pokud k tomu dojde, můžete zkusit změnit související parametry. Můžete například změnit maximální velikost slovníku N-gramů nebo počet bitů, které se mají použít pro funkci hashování funkcí.

### <a name="lda-and-topic-modeling"></a>LDA a modelování témat

Pro *modelování tématem založeného na obsahu* se často používá latentní Dirichletův přidělování. to znamená, že kategorie výuky z neklasifikovaného textu jsou v podstatě. V tématu modelování témat na základě obsahu je téma rozdělením do slov.

Předpokládejme například, že jste zadali corpus recenze zákazníků, které obsahují mnoho produktů. Text revizí odeslaných zákazníky v průběhu času obsahuje mnoho termínů, z nichž některé jsou používány ve více tématech.

*Téma* , které IDENTIFIKUJE proces LDA, může představovat recenze pro jednotlivý produkt, nebo může představovat skupinu revizí produktu. Do LDA, samotné téma je pouze rozdělení pravděpodobnosti v průběhu času pro sadu slov.

Výrazy jsou zřídka exkluzivní pro libovolný produkt. Můžou odkazovat na jiné produkty nebo být obecné podmínky, které se vztahují na všechno ("Skvělé", "awful"). Jinými slovy můžou být hluková slova. Metoda LDA však nepokusí zaznamenat všechna slova v celém systému nebo pochopit, jakým způsobem jsou slova spojena, mimo pravděpodobnost výskytu korelace. Může seskupit jenom slova, která se používají v cílové doméně.

Po vypočítání indexů je míra podobnosti na základě vzdálenosti porovnána s jednotlivými řádky textu a určí, zda jsou dva části textu podobné. Můžete například zjistit, že má produkt více názvů, které jsou silně korelační. Nebo se můžete setkat s tím, že silně záporné výrazy jsou obvykle spojeny s konkrétním produktem. Pomocí míry podobnosti můžete identifikovat související výrazy a vytvořit doporučení.

###  <a name="module-parameters"></a>Parametry modulu

|Název|Typ|Rozsah|Volitelné|Výchozí|Description|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Cílový sloupec (y)|Výběr sloupce||Vyžadováno|StringFeature|Název nebo index cílového sloupce.|  
|Počet témat pro model|Integer|[1; 1000]|Vyžadováno|5|Namodelujte distribuci dokumentu na N témata.|  
|N-gramů|Integer|[1; 10]|Vyžadováno|2|Pořadí N-gramů generovaných během hashace.|  
|Normalizovat|Logická hodnota|True nebo false|Vyžadováno|true|Normalizuje výstup na pravděpodobnost.  Transformovaná datová sada bude P (téma&#124;dokumentu) a matice tématu funkce bude P (Word&#124;téma).|  
|Zobrazit všechny možnosti|Logická hodnota|True nebo false|Vyžadováno|Ne|Uvede další parametry, které jsou specifické pro scikit – Přečtěte si online LDA.|  
|Ró – parametr|Float|[0.00001; 1.0]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0,01|Předchozí distribuce slova tématu|  
|Parametr alfa|Float|[0.00001; 1.0]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0,01|Předchozí distribuce dokumentu|  
|Odhadovaný počet dokumentů|Integer|[1; int. MaxValue|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|1000|Odhadovaný počet dokumentů Odpovídá `total_samples` parametru.|  
|Velikost dávky|Integer|[1; 1024]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|32|Velikost dávky|  
|Počáteční hodnota iterace použitá v plánu aktualizace studijních kurzů|Integer|[0; int. MaxValue|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0|Počáteční hodnota, která downweights kurzů pro počáteční iterace. Odpovídá `learning_offset` parametru.|  
|Zapnutí pro iteraci během aktualizací|Float|[0,0; 1,0]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|0,5|Napájení použité pro počet iterací za účelem řízení míry učení. Odpovídá `learning_decay` parametru. |  
|Počet iterací cvičení|Integer|[1; 1024]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|25|Počet iterací cvičení.|  
|Sestavit slovník pro ngrams|Logická hodnota|True nebo false|Platí v případě *, že není zaškrtnuté* políčko **Zobrazit všechny možnosti** .|Ano|Vytvoří slovník ngrams před výpočetním LDA. Užitečné pro kontrolu a výklad modelu.|  
|Maximální velikost ngram slovníku|Integer|[1; int. MaxValue|Platí v případě, že je **hodnota** **ngrams slovníku pro možnost Build** .|20000|Maximální velikost ngrams slovníku Pokud počet tokenů ve vstupu překračuje tuto velikost, může dojít k kolizí.|  
|Počet bitů, které se mají použít pro funkci hashování funkcí|Integer|[1; 31]|Platí v případě, že *není zaškrtnuté* políčko **Zobrazit všechny možnosti** a **slovník sestavení ngrams** je **false** .|12|Počet bitů, které se mají použít pro funkci hashování funkcí| 
|Sestavit slovník ngrams před LDA|Logická hodnota|True nebo false|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** .|Ano|Vytvoří slovník ngrams před LDA. Užitečné pro kontrolu a výklad modelu.|  
|Maximální počet ngrams ve slovníku|Integer|[1; int. MaxValue|Platí v případě, že je zaškrtnuté políčko **Zobrazit všechny možnosti** a možnost **sestavit slovník ngrams** je **true** .|20000|Maximální velikost slovníku Pokud počet tokenů ve vstupu překračuje tuto velikost, může dojít k kolizí.|  
|Počet bitů hash|Integer|[1; 31]|Platí v případě, že je zaškrtnuto políčko **Zobrazit všechny možnosti** a možnost **sestavit slovník ngrams** je **false** .|12|Počet bitů, které se mají použít při vytváření hodnot hash funkcí|   


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb specifických pro moduly naleznete v tématu [výjimky a kódy chyb pro návrháře](designer-error-codes.md).
