---
title: Skóre spolehlivosti - QnA Maker
titleSuffix: Azure Cognitive Services
description: Znalostní báze musí být zveřejněna. Po publikování znalostní báze je dotazován na koncovém bodu prognózy za běhu pomocí generateAnswer API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d901a803311805825c22503af6098e805a67e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843448"
---
# <a name="the-confidence-score-of-an-answer"></a>Skóre důvěry odpovědi
Když je uživatelský dotaz porovnán se znalostní základnou, QnA Maker vrátí relevantní odpovědi spolu se skóre spolehlivosti. Toto skóre označuje jistotu, že odpověď je správná shoda pro daný dotaz uživatele.

Skóre spolehlivosti je číslo mezi 0 a 100. Skóre 100 je pravděpodobně přesná shoda, zatímco skóre 0 znamená, že nebyla nalezena žádná odpovídající odpověď. Čím vyšší je skóre- tím větší je důvěra v odpověď. Pro daný dotaz může být vráceno více odpovědí. V takovém případě jsou odpovědi vráceny v pořadí podle klesajícího skóre spolehlivosti.

V níže uvedeném příkladu můžete vidět jednu entitu QnA se 2 otázkami.


![Ukázkový pár QnA](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Pro výše uvedený příklad- můžete očekávat skóre, jako je rozsah skóre vzorku níže- pro různé typy uživatelských dotazů:


![Rozsah skóre Ranker](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


V následující tabulce je uvedeno typické spolehlivosti přidružené pro dané skóre.

|Hodnota skóre|Skóre Význam|Ukázkový dotaz|
|--|--|--|
|90 - 100|Téměř přesná shoda uživatelského dotazu a otázky KB|"Moje změny nejsou aktualizovány v KB po publikování"|
|70 >|Vysoká spolehlivost - typicky dobrá odpověď, která zcela odpovídá na dotaz uživatele|"Publikoval jsem kb, ale není aktualizován"|
|50 - 70|Střední spolehlivost - obvykle poměrně dobrá odpověď, která by měla odpovědět na hlavní záměr uživatelského dotazu|"Mám před publikováním kb uložit aktualizace?"|
|30 - 50|Nízká spolehlivost - typicky související odpověď, která částečně odpovídá záměru uživatele|"Co dělá uložení a vlak dělat?"|
|< 30.|Velmi nízká spolehlivost - obvykle neodpovídá na dotaz uživatele, ale má některá odpovídající slova nebo fráze |" Kde mohu přidat synonyma k mému KB"|
|0|Žádná shoda, takže odpověď není vrácena.|"Kolik stojí služba"|

## <a name="choose-a-score-threshold"></a>Výběr prahové hodnoty skóre
Výše uvedená tabulka ukazuje skóre, které se očekává u většiny kb. Nicméně, protože každý KB je jiný, a má různé typy slov, záměrů a cílů- doporučujeme vyzkoušet a zvolit prahovou hodnotu, která nejlépe vyhovuje pro vás. Ve výchozím nastavení je prahová hodnota nastavena na hodnotu 0, takže jsou vráceny všechny možné odpovědi. Doporučená prahová hodnota, která by měla fungovat pro většinu kbs, je **50**.

Při výběru prahové hodnoty mějte na paměti rovnováhu mezi přesností a pokrytím a vylaďte prahovou hodnotu na základě vašich požadavků.

- Pokud **přesnost** (nebo přesnost) je důležitější pro váš scénář, pak zvýšit prahovou hodnotu. Tímto způsobem, pokaždé, když vrátíte odpověď, bude to mnohem jistější případ a mnohem pravděpodobnější, že budou odpovědí, kterou uživatelé hledají. V takovém případě můžete nakonec nechat další otázky nezodpovězené. *Například:* pokud uděláte prahovou hodnotu **70**, může vám chybět některé nejednoznačné příklady jako "co je uložit a trénovat?".

- Pokud **pokrytí** (nebo odvolání) je důležitější- a chcete odpovědět na co nejvíce otázek, jak je to možné, i když existuje pouze částečný vztah k otázce uživatele-pak snížit prahovou hodnotu. To znamená, že může být více případů, kdy odpověď neodpovídá na skutečný dotaz uživatele, ale poskytuje některé další poněkud související odpověď. *Například:* Pokud uděláte prahovou hodnotu **30**, můžete poskytnout odpovědi na dotazy jako "Kde lze upravit kb?"

> [!NOTE]
> Novější verze QnA Maker uzavělu obsahují vylepšení logiky hodnocení a mohou ovlivnit vaši prahovou hodnotu. Kdykoli aktualizujete službu, ujistěte se, že v případě potřeby otestujete a vyladíte prahovou hodnotu. Můžete zkontrolovat verzi služby QnA [zde](https://www.qnamaker.ai/UserSettings)a zjistit, jak získat nejnovější aktualizace [zde](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Nastavit prahovou hodnotu

Nastavte prahovou hodnotu jako vlastnost [těla GeneAnswer API JSON](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). To znamená, že jej nastavíte pro každé volání GenerateAnswer.

Z rozhraní bot nastavte skóre jako součást objektu options pomocí [jazyka C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) nebo [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Zlepšit skóre spolehlivosti
Chcete-li zlepšit skóre spolehlivosti konkrétní odpovědi na dotaz uživatele, můžete přidat uživatelský dotaz do znalostní báze jako alternativní otázku k této odpovědi. Můžete také použít malá a velká písmena [změny slova](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) přidat synonyma klíčová slova v KB.


## <a name="similar-confidence-scores"></a>Podobné skóre spolehlivosti
Pokud více odpovědí mají podobné skóre spolehlivosti, je pravděpodobné, že dotaz byl příliš obecný a proto uzavřeno se stejnou pravděpodobností s více odpovědí. Pokuste se lépe strukturovat qna tak, aby každá entita QnA měla odlišný záměr.


## <a name="confidence-score-differences-between-test-and-production"></a>Rozdíly ve skóre spolehlivosti mezi testem a výrobou
Skóre spolehlivosti odpovědi se může mezi testem a publikovanou verzí znalostní báze změnit, i když je obsah stejný. Důvodem je, že obsah testu a publikované znalostní báze jsou umístěny v různých indexech Azure Cognitive Search.

Index testu obsahuje všechny dvojice QnA vašich znalostí. Při dotazování na index testu dotaz se dotaz vztahuje na celý index pak výsledky jsou omezeny na oddíl pro konkrétní znalostní báze. Pokud výsledky testovacího dotazu negativně ovlivňují vaši schopnost ověřit znalostní bázi, můžete:
* uspořádejte znalostní bázi pomocí jedné z následujících možností:
    * 1 prostředek omezený na 1 KB: omezit jeden prostředek QnA (a výsledný index testu Azure Cognitive Search) na jednu znalostní bázi.
    * 2 zdroje - 1 pro test, 1 pro výrobu: mají dva qna maker zdroje, pomocí jednoho pro testování (s vlastní mise a výrobní indexy) a jeden pro produkt (také s vlastní test a výrobní indexy)
* a vždy používejte stejné parametry, například **[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** při dotazování testovací i produkční znalostní báze

Když publikujete znalostní bázi, obsah otázek a odpovědí vaší znalostní báze se přesune z indexu testu do produkčního indexu ve vyhledávání Azure. Podívejte se, jak funguje operace [publikování.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

Pokud máte znalostní bázi v různých oblastech, každá oblast používá vlastní index Azure Cognitive Search. Vzhledem k tomu, že se používají různé indexy, skóre nebude přesně stejné.


## <a name="no-match-found"></a>Nebyla nalezena žádná shoda.
Pokud ranker nenajde žádnou dobrou shodu, je vráceno skóre spolehlivosti 0,0 nebo "Žádné" a výchozí odpověď je "V KB nebyla nalezena žádná dobrá shoda". Tuto [výchozí odpověď](../How-To/metadata-generateanswer-usage.md) můžete přepsat v kódu robota nebo aplikace volajícího koncový bod. Alternativně můžete také nastavit odpověď přepsání v Azure a to změní výchozí nastavení pro všechny znalostní báze nasazené v konkrétní službě QnA Maker.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Osvědčené postupy](./best-practices.md)

