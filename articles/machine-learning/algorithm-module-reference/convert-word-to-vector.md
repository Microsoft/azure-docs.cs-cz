---
title: 'Převést slovo na vektor: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat tři dodávané modely Word2Vec k extrakci slovníku a odpovídajících vkládání slov z Corpus textu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91536728"
---
# <a name="convert-word-to-vector-module"></a>Převést Word na vektorový modul

Tento článek popisuje, jak použít modul převést Word na vektor v Návrháři Azure Machine Learning k provedení těchto úloh:

- Použijte různé modely Word2Vec (Word2Vec, FastText, šetrnější předvlaked model) na corpus textu, který jste zadali jako vstup.
- Vygenerujte slovník pomocí vkládání slov.

Tento modul používá knihovnu Gensim. Další informace o Gensim najdete v jeho [oficiálním webu](https://radimrehurek.com/gensim/apiref.html), který obsahuje kurzy a vysvětlení algoritmů.

### <a name="more-about-converting-words-to-vectors"></a>Další informace o konverzi slov na vektory

Převod slov na vektory nebo vektorování slov je proces zpracování přirozeného jazyka (NLP). Proces používá jazykové modely k mapování slov do vektorového prostoru. Vektorové místo představuje každé slovo pomocí vektoru reálných čísel. Také umožňuje, aby slova s podobnými významy měla podobná reprezentace.

Používejte vkládání slov jako počáteční vstup pro NLP úkoly, jako je například klasifikace textu a analýza mínění.

V rámci různých technologií pro vkládání slov v tomto modulu jsme implementovali tři široce používané metody. Dva, Word2Vec a FastText, jsou online – školicí modely. Druhý je předem vydaný model šetrnější-wiki-gigaword-100. 

Online – modely školení se vyškole na vstupní data. Předběžně používané modely jsou vyškoleny offline na větších textových corpus (například Wikipedii, Google News), které obvykle obsahují asi 100 000 000 000 slov. Vkládání slov pak při vektorování slov zůstane konstantní. Předpracované modely slov poskytují výhody, jako je například zkrácení doby školení, lepší kódování vektorů slov a vylepšení celkového výkonu.

Zde jsou některé informace o metodách:

+ Word2Vec je jedním z nejoblíbenějších techniků pro seznámení s vkládáním slov pomocí omezené sítě neuronové. Teorie je popsána v tomto dokumentu, který je k dispozici jako soubor PDF ke stažení: [efektivní odhad reprezentace slov ve vektorovém prostoru](https://arxiv.org/pdf/1301.3781.pdf). Implementace v tomto modulu je založena na [knihovně Gensim pro Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ V tomto dokumentu je vysvětlena teoretická FastText, která je k dispozici jako soubor PDF ke stažení: [rozšíření vektorů slov o informace o podslovech](https://arxiv.org/pdf/1607.04606.pdf). Implementace v tomto modulu je založena na [knihovně Gensim pro FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Šetrnější předvlaked model je šetrnější-wiki-gigaword-100. Jedná se o kolekci předwikipediich vektorů založených na text corpus, který obsahuje 5 600 000 000 tokeny a 400 000 nev nepodle slov. Je k dispozici stažení PDF: [šetrnější: globální vektory pro reprezentace slov](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Jak nakonfigurovat převod slova na vektor

Tento modul vyžaduje datovou sadu, která obsahuje sloupec textu. Předzpracovaný text je lepší.

1. Přidejte do svého kanálu modul **Převod Wordu do vektoru** .

2. Jako vstup pro modul zadejte datovou sadu, která obsahuje jeden nebo více textových sloupců.

3. Pro **cílový sloupec** vyberte jenom jeden sloupec, který obsahuje text, který se má zpracovat.

    Vzhledem k tomu, že tento modul vytvoří slovník z textu, obsah sloupců se liší, což vede k různým obsahům slovníku. Proto modul přijímá pouze jeden cílový sloupec.

4. V případě  **strategie Word2Vec** vyberte z **šetrnější předvlaked English model**, **Gensim Word2Vec** a **Gensim FastText**.

5. Pokud je **Word2Vec strategie** **Gensim Word2Vec** nebo **Gensim FastText**:

    + Pro **Word2Vec školicí algoritmus** vyberte z **Skip_gram** a **CBOW**. Rozdíl je představený v [původním dokumentu (PDF)](https://arxiv.org/pdf/1301.3781.pdf).

        Výchozí metoda je **Skip_gram**.

    + Pro **délku vkládání slov** zadejte dimenzionální vektory. Toto nastavení odpovídá `size` parametru v Gensim.

        Výchozí velikost vložení je 100.

    + V poli **Velikost kontextového okna** určete maximální vzdálenost mezi slovem, který je předpovězený, a aktuálním slovem. Toto nastavení odpovídá `window` parametru v Gensim.

        Výchozí velikost okna je 5.

    + V poli **počet epochs** určete počet epochs (iterací) na corpus. Odpovídá `iter` parametru v Gensim.

        Výchozí epocha číslo je 5.

6. V poli **maximální velikost slovníku** určete maximální počet slov v generovaném slovníku.

    Pokud existuje více jedinečných slov, než je maximální velikost, vyřadí se zřídka.

    Výchozí velikost slovníku je 10 000.

7. Pro **minimální počet slov** zadejte minimální počet slov. Modul bude ignorovat všechna slova, která mají četnost nižší než tato hodnota.

    Výchozí hodnota je 5.

8. Odešlete kanál.

## <a name="examples"></a>Příklady

Modul má jeden výstup:

+ **Slovník s vložením**: obsahuje generovaný slovník spolu s vložením každého slova. Jedna dimenze zabírá jeden sloupec.

Následující příklad ukazuje, jak funguje modul Convert Word to Vector. Používá převod Wordu na vektor s výchozím nastavením na předzpracovaný Wikipedii datovou sadu SP 500.

### <a name="source-dataset"></a>Zdrojová datová sada

Datová sada obsahuje sloupec kategorie společně s celým textem načteným z Wikipedii. Následující tabulka obsahuje několik reprezentativních příkladů.

|Text|
|----------|
|Nasdaq 100 komponenty s p 500 umístění zakladatel komponenty Foundation – město – – nekonečná smyčka – ulice – Cupertino Kalifornie Cupertino Kalifornie umístění země USA...|
|BR Nasdaq 100 Nasdaq 100 komponenta br s p 500 s t 500 komponenta Industry Computer Software Foundation br zakladatel Charles Geschke br Jan Warnock Location Adobe Systems...|
|s p 500 s p 500 výrobní odvětví automobilový průmysl automobilový předchůdce obecné části společnosti 1908 2009 nástupce...|
|s p 500 s p 500 součást odvětví konglomerátu (zakladatel), společnost konglomerát Foundation – umístění City Fairfield Connecticut Fairfield Connecticut umístění země USA...|
|BR s p 500 s p 500 Component Foundation 1903 zakladatel William s Harley br Arthur Davidson Harley Davidson zakladatel Arthur Davidson br Waltera Davidson br William a Davidson Location...|

### <a name="output-vocabulary-with-embeddings"></a>Slovník výstupu s vloženími

Následující tabulka obsahuje výstup tohoto modulu, který přebírá jako vstup datovou sadu Wikipedii SP 500. Sloupec úplně vlevo označuje slovník. Jeho vložení vektoru je reprezentované hodnotami zbývajících sloupců na stejném řádku.

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

V tomto příkladu jsme použili výchozí **Gensim Word2Vec** pro **strategii Word2Vec** a **školicí algoritmus** je **Skip-gram**. **Délka vkládání slov** je 100, proto máme 100 vkládání sloupců.

## <a name="technical-notes"></a>Technické poznámky

V této části najdete tipy a odpovědi na nejčastější dotazy.

+ Rozdíl mezi online školením a předvýukým modelem:

    V tomto převodu Wordu na vektorový modul jsme získali tři různé strategie: dva modely online školení a jeden předem vydaný model. Modely online školení využívají vstupní datovou sadu jako školicí data a generují vektory a slovní vektory během školení. Předem vydaný model je již vyškolený o mnohem větší corpus textu, jako je například Wikipedii nebo text na Twitteru. Předvlakový model je ve skutečnosti kolekcí párů slov/vkládání.  

    Předučený model šetrnější shrnuje slovník ze vstupní datové sady a generuje vektor vložení pro každé slovo z předem připraveného modelu. Bez online školení může použití předvýukového modelu ušetřit dobu výuky. Má lepší výkon, zejména v případě, že velikost vstupní datové sady je relativně malá.

+ Velikost vložení:

    Obecně platí, že délka vkládání slov je nastavena na několik set. Například 100, 200, 300. Velikost malého vložení znamená malý vektorový prostor, což může způsobit kolizi vkládání slov.  

    Délka vkládání slov je určena pro předem vypracované modely. V tomto příkladu je velikost vložení šetrnější-wiki-gigaword-100 100.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb, které jsou specifické pro moduly návrháře, najdete v tématu [kódy chyb Machine Learning](designer-error-codes.md).
