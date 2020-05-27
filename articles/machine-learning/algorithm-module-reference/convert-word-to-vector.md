---
title: Převést slovo na vektor
titleSuffix: Azure Machine Learning
description: Naučte se používat tři dodávané modely Word2Vec k extrakci slovníku a odpovídajících vkládání slov z Corpus textu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853768"
---
# <a name="convert-word-to-vector"></a>Převést slovo na vektor

Tento článek popisuje, jak použít modul **převést Word na vektor** v Návrháři Azure Machine Learning (Preview), aby se použily různé modely Word2Vec (Word2Vec, FastText, šetrnější předškolený model) na corpus textu, který jste zadali jako vstup, a vygenerujte slovník s použitím vkládání slov.

Tento modul používá knihovnu Gensim. Další informace o Gensim najdete v jeho [oficiálním webu](https://radimrehurek.com/gensim/apiref.html) , který obsahuje kurzy a vysvětlení algoritmů.

### <a name="more-about-convert-word-to-vector"></a>Další informace o převedení slova na vektor

Obecně řečeno, převod slova na vektor nebo vektorování textu je proces zpracování v přirozeném jazyce, který používá jazykové modely nebo techniky k mapování slov na vektorové místo, to znamená, aby představovalo každé slovo pomocí vektoru reálných čísel a mezitím umožňuje, aby slova s podobnými významy měla podobná reprezentace.

Vkládání slov lze použít jako počáteční vstup pro NLP úkoly, jako je například klasifikace textu, analýza mínění atd.

V rámci různých technologií pro vkládání slov v tomto modulu jsme implementovali tři široce používané metody, včetně dvou online školicích modelů, Word2Vec a FastText a jednoho předem připraveného modelu šetrnější-wiki-gigaword-100. Online – školicí modely jsou připravené na vstupní data, zatímco předučené modely jsou ve větším textu corpus, (například Wikipedii, Google News) obvykle obsahuje asi 100 000 000 000 slov, takže vkládání slov zůstává během vektorování slov konstantní. Předem připravené modely slov poskytují výhody, jako je například zkrácení doby školení, lepší kódování vektorů slov a vylepšení celkového výkonu.

+ Word2Vec je jedním z nejoblíbenějších techniků pro seznámení s vkládáním slov pomocí sítě s omezeným neuronové. Teoretická je v tomto dokumentu dostupná jako stahování PDF: [efektivní odhad reprezentace slov ve vektorovém prostoru, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). Implementace v tomto modulu je založená na [knihovně gensim pro Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ V tomto dokumentu je vysvětlena teoretická FastText, která je k dispozici jako soubor PDF ke stažení: [rozšíření vektorů slov pomocí informací o podslovu, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). Implementace v tomto modulu je založená na [knihovně gensim pro FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Šetrnější předučený model: šetrnější-wiki-gigaword-100, je kolekce předučených vektorů založených na Wikipedii text corpus, které obsahují tokeny 5.6 B a 400 tisíc nerozlišovatelné slovníky, je k dispozici PDF: [šetrnější: globální vektory pro slovo reprezentace](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Jak nakonfigurovat převod slova na vektor

Tento modul vyžaduje datovou sadu, která obsahuje sloupec textu, předzpracovaný text je lepší.

1. Přidejte do svého kanálu modul **Převod Wordu do vektoru** .

2. Jako vstup pro modul zadejte datovou sadu obsahující jeden nebo více textových sloupců.

3. Pro **cílový sloupec**vyberte jenom jeden sloupec, který obsahuje text, který se má zpracovat.

    Obecně platí, že vzhledem k tomu, že tento modul vytvoří slovník z textu, obsah různých sloupců se liší, což vede k různým obsahům slovníku, proto modul přijímá pouze jeden cílový sloupec.

4. V případě **strategie Word2Vec**vyberte z `GloVe pretrained English Model` , `Gensim Word2Vec` a `Gensim FastText` .

5. Pokud je **Word2Vec strategie** `Gensim Word2Vec` nebo `Gensim FastText` :

    + **Word2Vec školicí algoritmus**. Vyberte z `Skip_gram` a `CBOW` . V původním [dokumentu](https://arxiv.org/pdf/1301.3781.pdf)se zavádí rozdíl.

        Výchozí metoda je `Skip_gram` .

    + **Délka vkládání slov** Zadejte dimenzionální počet vektorů slova. Odpovídá `size` parametru v gensim.

        Výchozí embedding_size je 100.

    + **Velikost kontextového okna** Zadejte maximální vzdálenost mezi předpovězeným slovem a aktuálním slovem. Odpovídá `window` parametru v gensim.

        Výchozí velikost okna je 5.

    + **Počet epochs** Zadejte počet epochs (iterací) nad corpus. Odpovídá `iter` parametru v gensim.

        Výchozí epochs číslo je 5.

6. Pro **maximální velikost slovníku**zadejte maximální počet slov v generovaném slovníku.

    Pokud existuje více jedinečných slov, vyřadí se zřídka.

    Výchozí velikost slovníku je 10000.

7. Pro hodnotu **minimální počet slov**zadejte minimální počet slov, což znamená, že modul ignoruje všechna slova, jejichž četnost je nižší než tato hodnota.

    Výchozí hodnota je 5.

8. Odešlete kanál.

## <a name="examples"></a>Příklady

Modul má jeden výstup:

+ **Slovník s vložením**: obsahuje vygenerovaný slovník spolu s vložením každého slova, jedna dimenze zabírá jeden sloupec.

### <a name="result-examples"></a>Příklady výsledků

Pro ilustraci, jak funguje modul **Convert Word na vektor** , následující příklad aplikuje tento modul s výchozím nastavením na předzpracovaný Wikipedii datovou sadu SP 500, kterou poskytuje Azure Machine Learning (Preview).

#### <a name="source-dataset"></a>Zdrojová datová sada

Datová sada obsahuje sloupec kategorie a také celý text načtený z Wikipedii. Tato tabulka obsahuje jenom několik reprezentativních příkladů.

|text|
|----------|
|Nasdaq 100 komponenty s p 500 umístění zakladatel komponenty Foundation – město – – nekonečná smyčka – ulice – Cupertino Kalifornie Cupertino Kalifornie umístění země USA...|
|BR Nasdaq 100 Nasdaq 100 komponenta br s p 500 s t 500 komponenta Industry Computer Software Foundation br zakladatel Charles Geschke br Jan Warnock Location Adobe Systems...|
|s p 500 s p 500 výrobní odvětví automobilový průmysl automobilový předchůdce obecné části společnosti 1908 2009 nástupce...|
|s p 500 s p 500 součást odvětví konglomerátu (zakladatel), společnost konglomerát Foundation – umístění City Fairfield Connecticut Fairfield Connecticut umístění země USA...|
|BR s p 500 s p 500 Component Foundation 1903 zakladatel William s Harley br Arthur Davidson Harley Davidson zakladatel Arthur Davidson br Waltera Davidson br William a Davidson Location...|

#### <a name="output-vocabulary-with-embeddings"></a>Slovník výstupu s vloženími

Následující tabulka obsahuje výstup tohoto modulu, který jako vstup bere Wikipedii sadu dat SP 500. Sloupec umístěný nejvíce vlevo označuje slovník, jeho vložení vektoru je reprezentované hodnotami zbývajících sloupců na stejném řádku.

|Slovník|Vložení dimenze 0|Dimenze vložení 1|Dimenze vložení 2|Dimenze vložení 3|Dimenze vložení 4|Dimenze vložení 5|...|Vkládání dimenze 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0,375865|0,609234|0,812797|-0,002236|0,319071|-0,591986|...|0,364276
|komponenta|0,081302|0,40001|0,121803|0,108181|0,043651|-0,091452|...|0,636587
|s|-0,34355|-0,037092|-0,012167|0,151542|0,601019|0,084501|...|0,149419
|p|-0,133407|0,073244|0,170396|0,326706|0,213463|-0,700355|...|0,530901
Foundation|-0,166819|0,10883|-0,07933|-0,073753|0,262137|0,045725|...|0,27487
zakladatel|-0,297408|0,493067|0,316709|-0,031651|0,455416|-0,284208|...|0,22798
location|-0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|-0,460828|0,505516|-0,074294|-0,00639|0,116545|0,494368|...|-0,2403
Apple|0,05779|0,672657|0,597267|-0,898889|0,099901|0,11833|...|0,4636
vysokoškolské|-0,281835|0,29312|0,106966|-0,031385|0,100777|-0,061452|...|0,05978
program|-0,263074|0,245753|0,07058|-0,164666|0,162857|-0,027345|...|-0,0525
loop|-0,391421|0,52366|0,141503|-0,105423|0,084503|-0,018424|...|-0,0521

V tomto příkladu jsme použili výchozí nastavení `Gensim Word2Vec` jako **strategii Word2Vec**, což je, že pro **školicí algoritmus** je `Skip-gram` **délka vkládání slov** 100, proto máme 100 sloupců vkládání.

## <a name="technical-notes"></a>Technické poznámky

V této části najdete tipy a odpovědi na nejčastější dotazy.

+ Rozdíl mezi online a předvýukovým modelem

    V tomto **převodu Wordu na vektorový modul**jsme poskytovali tři různé strategie, dva Online školicí modely a jeden předem připravený model. Online model školení používá vaši vstupní datovou sadu jako školicí data a generuje vektory slovního a Wordu během školení, zatímco předem připravený model je již vyškolený pomocí mnohem většího textu corpus, jako je například Wikipedii nebo text na Twitteru, takže předem připravený model je vlastně kolekcí páru (Word, vkládání).  

    Pokud je předem vyškolený model šetrnější jako strategie dělení slov, shrnuje slovník ze vstupní datové sady a generuje vektor pro každé slovo z předem vyučeného modelu bez online školení, může být použití předem připraveného modelu ušetřit čas školení a má lepší výkon, zejména pokud je velikost vstupní datové sady relativně malá.

+ Velikost vložení

    Obecně platí, že délka vkládání slov je nastavená na několik set (například 100, 200, 300), aby se dosáhlo dobrého výkonu, protože velikost malého vkládání znamená malý vektorový prostor, což může způsobit kolizi vkládání slov.  

    V případě předdefinovaných modelů je délka vkládání slov v této implementaci opravena. velikost šetrnější-wiki-gigaword-100 je 100.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb, které jsou specifické pro moduly návrháře (Preview), najdete v tématu [kódy chyb Machine Learning](designer-error-codes.md).
