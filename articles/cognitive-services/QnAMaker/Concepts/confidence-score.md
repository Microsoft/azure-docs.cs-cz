---
title: Hodnocení spolehlivosti – QnA Maker
titleSuffix: Azure Cognitive Services
description: Skóre spolehlivosti označuje jistotu, že odpověď je doprava odpovídá dotazu daného uživatele.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 14339a61e48866d51089db9a0008a3de982b1710
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277113"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Skóre spolehlivosti znalostní báze QnA Maker
Když uživatelský dotaz je hledána znalostní báze, vrátí QnA Maker příslušné odpovědi, spolu s skóre spolehlivosti. Toto skóre označuje jistotu, že odpověď je doprava odpovídá dotazu daného uživatele. 

Skóre spolehlivosti je číslo mezi 0 a 100. Skóre 100 je pravděpodobně přesnou shodu, zatímco skóre 0 znamená, která nebyla nalezena žádná odpovídající odpověď. Čím vyšší skóre – větší jistotou v odpovědi na dotaz. Pro daný dotaz může být vrátil více odpovědí. V takovém případě odpovědi se vrátí v pořadí podle snižuje pravděpodobnost.

V následujícím příkladu vidíte jednu entitu QnA 2 otázek. 


![Ukázka QnA pár](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Pro výše uvedeného příkladu – můžete očekávat, že skóre jako rozsah skóre ukázka níže – pro různé typy uživatelských dotazů:


![Klasifikátor rozsahu skóre](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Následující tabulka uvádí typické přidruženou skóre spolehlivosti.

|Hodnota skóre|Význam skóre|Příklad dotazu|
|--|--|--|
|90 – 100|A téměř přesnou shodu uživatele dotazu a dotaz KB|"Moje změny nejsou aktualizovány v KB po publikování"|
|> 70|Vysokou spolehlivostí – obvykle dobrou odpověď, která úplně odpovědi na dotaz uživatele|"Jsem publikoval znalostní BÁZE, ale není prováděna"|
|50 - 70|Střední jistotou – obvykle velmi dobrou odpověď, která by měla obsahovat odpovědi hlavním záměrem uživatelský dotaz|"Měli uložit Moje aktualizace před můžu publikovat znalostní BÁZE?"|
|30 – 50|S nízkou spolehlivostí – obvykle související odpovědí, který odpovídá částečně záměru uživatele|"Co uložit a trénování dělá?"|
|< 30|Velmi nízkou spolehlivostí – obvykle neodpovídá dotaz uživatele, ale má některé odpovídající slova nebo fráze |"Kde si můžu přidat synonyma znalostní BÁZE"|
|0|Žádná shoda, proto se vrátí odpověď.|"Kolik službu stojí"|

## <a name="choose-a-score-threshold"></a>Zvolte prahové hodnoty skóre
V tabulce výše najdete skóre, které se očekává, že na většině znalostní báze. Vzhledem k tomu, že se každá KB liší a má různé typy slov, záměrů a cílů – doporučujeme, abyste otestovali a zvolili prahovou hodnotu, která pro vás nejlépe vyhovuje. Ve výchozím nastavení je prahová hodnota nastavena na 0, aby byly vráceny všechny možné odpovědi. Doporučená prahová hodnota, která by měla fungovat pro většinu aktualizací KB, je **50**.

Pokud zvolíte, že vaše mezní hodnota, mějte na paměti rovnováhu mezi přesnost a pokrytí a upravit vaše mezní hodnota, na základě vašich požadavků.

- Pokud **přesnost** (nebo přesnosti) je důležité pro váš scénář, pak zvýšit vaše mezní hodnota. Tímto způsobem pokaždé, když se vrátí odpověď, bude mnohem více důvěrné velikosti písmen a mnohem vyšší pravděpodobnost, že hledá odpověď uživatele. V takovém případě může skončit opuštění další nezodpovězené dotazy. *Příklad:* Pokud provedete prahovou hodnotu **70**, můžete přijít o některé příklady nejednoznačný lajků "co je uložte a trénování?".

- Pokud **pokrytí** (nebo odvolání) je více důležité – a vy chcete odpovězte na tolik otázek, kolik nejvíce, i v případě částečné vztahu uživatele otázku – pak nižší prahová hodnota. To znamená, že může být více případů, kdy odpověď neodpovídá skutečné dotaz uživatele, ale poskytuje některé poněkud související odpovědí. *Příklad:* Pokud nastavíte prahovou hodnotu **30**, můžete poskytnout odpovědi na dotazy, jako je "kde můžu upravit svůj KB?"

> [!NOTE]
> Novější verze nástroje QnA Maker vylepšení bodovací logiku zahrnout a může mít vliv na vaše mezní hodnota. Kdykoli můžete aktualizovat službu, ujistěte se, že pro testování a upravit prahovou hodnotu v případě potřeby. Služba QnA verzi, můžete zjistit [tady](https://www.qnamaker.ai/UserSettings)a zjistit, jak získat nejnovější aktualizace [tady](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Nastavit prahovou hodnotu 

Nastavte skóre prahové hodnoty jako vlastnost [těla JSON GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). To znamená, že ji nastavíte pro každé volání GenerateAnswer. 

Z rozhraní bot Framework nastavte skóre jako součást objektu Options v [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) nebo [Node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Zvýšení skóre spolehlivosti
Pro zvýšení skóre spolehlivosti konkrétní odpovědi na dotaz uživatele, můžete přidat uživatelský dotaz ve znalostní bázi jako alternativní dotaz na odpověď. [Změnou](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) velikosti písmen bez rozlišení velkých a malých písmen můžete také přidat synonyma do klíčových slov ve vaší znalostní bázi.


## <a name="similar-confidence-scores"></a>Podobně jako skóre spolehlivosti
Když ukládat více odpovědí mají podobné skóre spolehlivosti, je pravděpodobné, že dotaz byl příliš obecný a proto shodné s stejnou pravděpodobnost, že s více odpovědí. Vystavět strukturu vaší maximálně lépe tak, aby každá entita QnA obsahuje různé záměr.


## <a name="confidence-score-differences"></a>Skóre rozdíly spolehlivosti
Skóre spolehlivosti odpověď může změnit zanedbatelně mezi testu a publikovanou verzi znalostní báze i v případě, obsah je stejný. Je to proto, že obsah testu a publikované znalostní báze jsou umístěné v různých indexů Azure Search. Když publikujete znalostní bázi, obsah otázky a odpovědi ve znalostní bázi se přesune z indexu testu do výrobního indexu ve službě Azure Search. Podívejte se, jak funguje operace [publikování](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) .

Pokud máte znalostní bázi v různých oblastech, v každé oblasti se používá vlastní index Azure Search. Vzhledem k tomu, že se používají různé indexy, skóre se přesně neshodují. 


## <a name="no-match-found"></a>Nebyla nalezena žádná odpovídající
Když dobré shoda nenajde ve klasifikátor, vrátí se skóre spolehlivosti 0,0 nebo "None" a výchozí odpověď je "Dobrá nebyla nalezena žádná odpovídající v KB". Tuto [výchozí odpověď](#change-default-answer) můžete přepsat v robotu nebo kódu aplikace, který volá koncový bod. Alternativně přepsání odpovědi můžete také nastavit v Azure a tím se změní na výchozí hodnoty pro všechny znalostních bází nasazených v konkrétní služba QnA Maker.

## <a name="change-default-answer"></a>Změnit výchozí odpověď

1. Přejděte [webu Azure portal](https://portal.azure.com) a přejděte do skupiny prostředků, který představuje službu QnA Maker, kterou jste vytvořili.

2. Kliknutím otevřete **služby App Service**.

    ![Na webu Azure Portal přístup k App service pro nástroj QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klikněte na **nastavení aplikace** a upravit **DefaultAnswer** do požadovaného výchozí odpověď. Klikněte na **Uložit**.

    ![Vyberte nastavení aplikace a pak upravte DefaultAnswer pro nástroj QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Restartujte službu App service

    ![Po změně DefaultAnswer restartujte App Service QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Podporované zdroje dat](./data-sources-supported.md)

