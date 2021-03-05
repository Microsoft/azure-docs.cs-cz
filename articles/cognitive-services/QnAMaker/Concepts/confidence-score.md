---
title: Hodnocení spolehlivosti – QnA Maker
titleSuffix: Azure Cognitive Services
description: Když je dotaz na uživatele shodný se základem znalostní báze, QnA Maker vrátí relevantní odpovědi společně s hodnocením spolehlivosti.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: fcfc70f7bfb9e2bb1b1a0edbffdc1282056e5fa7
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214044"
---
# <a name="the-confidence-score-of-an-answer"></a>Hodnocení spolehlivosti odpovědi
Když je dotaz na uživatele shodný se základem znalostní báze, QnA Maker vrátí relevantní odpovědi společně s hodnocením spolehlivosti. Toto skóre označuje jistotu, že odpověď je správná pro daný dotaz uživatele.

Hodnocení spolehlivosti je číslo mezi 0 a 100. Skóre 100 je nejspíš přesná shoda, zatímco skóre 0 znamená, že se nenašla žádná odpovídající odpověď. Čím vyšší je skóre – tím větší je jistota v odpovědi. Pro daný dotaz může být vráceno více odpovědí. V takovém případě se odpovědi vrátí v pořadí podle snížení skóre spolehlivosti.

V následujícím příkladu vidíte jednu entitu QnA se 2 otázkami.


![Vzorový pár QnA](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Pro výše uvedený příklad můžete očekávat skóre, jako je například rozsah skóre, pro různé typy uživatelských dotazů:


![Rozsah skóre klasifikace](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Následující tabulka uvádí typickou jistotu spojenou s daným skóre.

|Hodnota skóre|Význam skóre|Příklad dotazu|
|--|--|--|
|90 – 100|Téměř přesná shoda s uživatelským dotazem a otázkou znalostní báze|"Moje změny nejsou aktualizovány v KB po publikování"|
|> 70|Vysoká důvěra – obvykle je vhodná odpověď, která úplně odpoví na dotaz uživatele.|Jsem publikoval (a) jsem KB, ale není aktualizovaný|
|50 – 70|Střední důvěra – obvykle je poměrně vhodná odpověď, která by měla odpovědět na hlavní záměr dotazování uživatele.|"Mám moje aktualizace uložit před publikováním KB?"|
|30 - 50|Nízká důvěra – obvykle související odpověď, která částečně odpovídá záměru uživatele|"Co dělá Save and vlak?"|
|< 30|Velmi nízká důvěra – obvykle neodpoví na dotaz uživatele, ale obsahuje několik vyhovujících slov nebo frází. |"Kde mohu přidat synonyma do seznamu KB"|
|0|Žádná shoda, takže odpověď se nevrátí.|To, kolik stojí za službu|

## <a name="choose-a-score-threshold"></a>Zvolit prahovou hodnotu skóre
Výše uvedená tabulka ukazuje skóre, které se očekává u většiny aktualizací KB. Vzhledem k tomu, že se každá KB liší a má různé typy slov, záměrů a cílů – doporučujeme, abyste otestovali a zvolili prahovou hodnotu, která pro vás nejlépe vyhovuje. Ve výchozím nastavení je prahová hodnota nastavena na 0, aby byly vráceny všechny možné odpovědi. Doporučená prahová hodnota, která by měla fungovat pro většinu aktualizací KB, je **50**.

Při volbě prahové hodnoty Pamatujte na rovnováhu mezi přesností a rozsahem a podle vašich požadavků vylepšit prahovou hodnotu.

- Pokud je **přesnost** (nebo přesnost) pro váš scénář důležitější, zvyšte svou prahovou hodnotu. Pokaždé, když vrátíte odpověď, bude to mnohem důležitější případ a bude mnohem pravděpodobnější, že budou vyhledáni uživatelé odpovědi. V takovém případě můžete ukončit ponechání dalších dotazů, které neodpověděly. *Příklad:* Pokud nastavíte prahovou hodnotu **70**, může dojít k nějakým nejednoznačným příkladům, jako je "Co je Save and vlak?".

- Pokud je **pokrytí** (nebo odvolání) důležitější – a chcete odpovědět co nejvíce otázek, i když k otázce uživatele dojde jenom k částečné relaci – pak snižte prahovou hodnotu. To znamená, že může dojít k více případům, kdy odpověď neodpoví na vlastní dotaz uživatele, ale poskytne trochu související odpověď. *Příklad:* Pokud nastavíte prahovou hodnotu **30**, můžete poskytnout odpovědi na dotazy, jako je "kde můžu upravit svůj KB?"

> [!NOTE]
> Novější verze QnA Maker zahrnují vylepšení logiky vyhodnocování a můžou mít vliv na vaši prahovou hodnotu. Při každé aktualizaci služby se ujistěte, že v případě potřeby otestujete a rozvedete prahovou hodnotu. [Tady](https://www.qnamaker.ai/UserSettings)si můžete prohlédnout verzi služby QnA a podívat se, jak získat [nejnovější aktualizace.](../How-To/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)

## <a name="set-threshold"></a>Nastavit prahovou hodnotu

Nastavte skóre prahové hodnoty jako vlastnost [těla JSON GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). To znamená, že ji nastavíte pro každé volání GenerateAnswer.

Z rozhraní bot Framework nastavte skóre v rámci objektu Options pomocí [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) nebo [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Vylepšení hodnocení spolehlivosti
Chcete-li zlepšit míru spolehlivosti konkrétní reakce na dotaz na uživatele, můžete do této odpovědi přidat dotaz uživatele do znalostní báze jako alternativní otázku. [Změnou](/rest/api/cognitiveservices/qnamaker/alterations/replace) velikosti písmen bez rozlišení velkých a malých písmen můžete také přidat synonyma do klíčových slov ve vaší znalostní bázi.


## <a name="similar-confidence-scores"></a>Podobná hodnocení spolehlivosti
Pokud má více odpovědí podobné hodnocení spolehlivosti, je pravděpodobné, že dotaz byl příliš obecný a proto se shodoval s rovností s více odpověďmi. Snažte se strukturu QnAs vylepšit tak, aby každá entita QnA měla odlišný záměr.


## <a name="confidence-score-differences-between-test-and-production"></a>Rozdíly v hodnocení spolehlivosti mezi testováním a výrobou
Hodnocení spolehlivosti odpovědi může změnit negligibly mezi testem a publikovanou verzí znalostní báze, i když je obsah stejný. Důvodem je to, že obsah testu a zveřejněné znalostní báze se nacházejí v různých indexech Kognitivní hledání Azure.

Index testu obsahuje všechny páry QnA vaší znalostní báze. Při dotazování indexu testu se dotaz vztahuje na celý index. výsledky jsou omezeny na oddíl tohoto konkrétního znalostní báze. Pokud výsledky testovacího dotazu mají negativní vliv na vaši schopnost ověřit znalostní bázi, můžete:
* pomocí jedné z následujících akcí uspořádejte znalostní bázi:
    * 1 prostředek je omezený na 1 KB: omezení jednoduchého QnA prostředku (a výsledného indexu Azure Kognitivní hledání test) na jednu znalostní bázi.
    * 2 zdroje-1 pro test, 1 pro produkci: mají dva QnA Maker prostředky, které používají jeden pro testování (s vlastními testy a výrobními indexy) a jeden pro produkt (také mají vlastní testovací a produkční indexy).
* a vždy používejte stejné parametry, jako je například **[Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** při dotazování na znalostní bázi test a produkčního prostředí.

Když publikujete znalostní bázi, obsah otázky a odpovědi ve znalostní bázi se přesune z indexu testu do výrobního indexu ve službě Azure Search. Podívejte se, jak funguje operace [publikování](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Pokud máte znalostní bázi v různých oblastech, v každé oblasti se používá vlastní index služby Azure Kognitivní hledání. Vzhledem k tomu, že se používají různé indexy, skóre se přesně neshodují.


## <a name="no-match-found"></a>Nenašla se žádná shoda.
V případě, že klasifikátor nenalezne žádnou dobrou shodu, bude vráceno hodnocení spolehlivosti 0,0 nebo "none" a výchozí odpověď je "v KB" nebyla nalezena žádná dobrá shoda. Tuto [výchozí odpověď](../How-To/metadata-generateanswer-usage.md) můžete přepsat v robotu nebo kódu aplikace, který volá koncový bod. Alternativně můžete také nastavit odpověď na přepsání v Azure a tato změna je výchozí pro všechny znalostní báze nasazené v určité QnA Maker službě.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Osvědčené postupy](./best-practices.md)
