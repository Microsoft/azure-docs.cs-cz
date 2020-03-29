---
title: Iterativní návrh aplikace - LUIS
titleSuffix: Azure Cognitive Services
description: Služba LUIS se nejlépe učí v iterativním cyklu změn modelu, příkladů utterance, publikování a shromažďování dat z dotazů koncového bodu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422601"
---
# <a name="iterative-app-design-for-luis"></a>Iterativní návrh aplikace pro SLUŽBU LUIS

Aplikace luis (Language Understanding) se učí a provádí nejefektivněji s iterací. Zde je typický iterace cyklu:

* Vytvořit novou verzi
* Upravte schéma aplikace LUIS. To zahrnuje:
    * Záměry s ukázkovými projevy
    * Entity
    * Funkce
* Školení, testování a publikování
    * Test v koncovém bodě predikce pro aktivní učení
* Shromažďování dat z dotazů koncového bodu

![Cyklus vytváření](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Vytváření schématu služby LUIS

Schéma aplikace definuje, co uživatel požaduje _(záměr_ nebo _záměr)_ a jaké části záměru poskytují podrobnosti (nazývané _entity),_ které se používají k určení odpovědi. 

Schéma aplikace musí být specifické pro domény aplikace, aby bylo možné určit slova a fráze, které jsou relevantní, a také určit typické řazení slov. 

Příklad projevy představují uživatelské vstupy, jako je například rozpoznaný projev řeči nebo textu, které aplikace očekává za běhu. 

Schéma vyžaduje záměry a _by měl mít_ entity. 

### <a name="example-schema-of-intents"></a>Příklad schématu záměrů

Nejběžnější schéma je záměr schéma uspořádané s záměry. Tento typ schématu používá LUIS k určení záměru uživatele. 

Typ schématu záměru může mít entity, pokud pomáhá LUIS určit záměr uživatele. Například přepravní entita (jako popisovač záměru) pomáhá luis určit záměr expedice. 

### <a name="example-schema-of-entities"></a>Příklad schématu entit

Schéma entity se zaměřuje na entity, což jsou data, která jsou extrahována z projevy uživatele. Pokud například uživatel řekl: "Chtěl bych objednat tři pizzy." Existují dva subjekty, které by byly extrahovány: _tři_ a _pizzy_. Ty se používají k naplnění záměru, kterým bylo učinit objednávku. 

Pro schéma entity záměr utterance je méně důležité pro klientskou aplikaci. 

Běžnou metodou uspořádání schématu entity je přidání všech ukázkových promluv do záměru **None.** 

### <a name="example-of-a-mixed-schema"></a>Příklad smíšeného schématu

Nejvýkonnější a nejzralejší schéma je záměr ové schéma s celou řadou entit a funkcí. Toto schéma může začít jako záměr nebo schéma entity a zvětšit tak, aby zahrnovala koncepty obou, protože klientská aplikace potřebuje tyto informace. 

## <a name="add-example-utterances-to-intents"></a>Přidání ukázkových promluv do záměrů

Služba LUIS potřebuje několik příkladů projevy v každém **záměru**. Příklad projevy potřebují dostatek varianty výběru slova a pořadí slov, aby bylo možné určit, který záměr utterance je určen pro. 

> [!CAUTION]
> Nepřidávejte příklad projevy hromadně. Začněte s 15 až 30 konkrétními a různými příklady. 

Každý příklad utterance musí mít všechna **požadovaná data extrahovat** navržena a označena **entity**. 

|Klíčovým prvkem|Účel|
|--|--|
|Záměr|**Klasifikace** projevy uživatele do jednoho záměru nebo akce. Příklady `BookFlight` zahrnují `GetWeather`a .|
|Entita|**Extrahovat** data z utterance potřebné k dokončení záměru. Příklady zahrnují datum a čas cesty a místo.|

Aplikace LUIS může být navržena tak, aby ignorovala projevy, které nejsou relevantní pro doménu aplikace přiřazením utterance k záměru **Žádný.**

## <a name="test-and-train-your-app"></a>Testování a trénování aplikace

Poté, co máte 15 až 30 různých příklad projevy v každém záměru, s požadovanými entitami označené, je třeba otestovat a [trénování](luis-how-to-train.md) aplikace LUIS. 

## <a name="publish-to-a-prediction-endpoint"></a>Publikovat do koncového bodu předpovědi

Aplikace LUIS musí být publikována tak, aby byla k dispozici v [oblastech koncového bodu předpovědi](luis-reference-regions.md)seznamu .

## <a name="test-your-published-app"></a>Testování publikované aplikace

Publikovanou aplikaci LUIS můžete otestovat z koncového bodu predikce HTTPS. Testování z koncového bodu předpověď umožňuje LUIS zvolit všechny projevy s nízkou spolehlivostí pro [kontrolu](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Vytvoření nové verze pro každý cyklus

Každá verze je snímek v čase aplikace LUIS. Před provedením změn v aplikaci vytvořte novou verzi. Je jednodušší vrátit se ke starší verzi než pokusit se odebrat záměry a projevy do předchozího stavu.

ID verze se skládá ze znaků, číslic nebo ". a nemůže být delší než 10 znaků.

Výchozí aktivní verze je počáteční verze (0.1). 

### <a name="begin-by-cloning-an-existing-version"></a>Začněte klonováním existující verze

Klonovat existující verzi, která má být používána jako výchozí bod pro každou novou verzi. Po klonování verze se nová verze stane **aktivní** verzí. 

### <a name="publishing-slots"></a>Publikování slotů

Můžete publikovat do fáze nebo produkční sloty. Každý slot může mít jinou verzi nebo stejnou verzi. To je užitečné pro ověření změn před publikováním do produkčního prostředí, které je k dispozici robotům nebo jiným aplikacím pro volání LUIS. 

Trénované verze nejsou automaticky dostupné v [koncovém bodě](luis-glossary.md#endpoint)vaší aplikace LUIS . Je nutné [publikovat](luis-how-to-publish-app.md) nebo znovu publikovat verzi, aby byla k dispozici v koncovém bodě aplikace LUIS. Můžete publikovat do **pracovní** a **produkční ,** které vám dvě verze aplikace k dispozici v koncovém bodě. Pokud v koncovém bodě musí být k dispozici více verzí aplikace, měli byste ji exportovat a znovu ji importovat do nové aplikace. Nová aplikace má jiné ID aplikace.

### <a name="import-and-export-a-version"></a>Import a export verze

Verzi lze importovat na úrovni aplikace. Tato verze se stane aktivní verzí a `versionId` použije ID verze ve vlastnosti souboru aplikace. Můžete také importovat do existující aplikace na úrovni verze. Nová verze se stane aktivní verzí. 

Verzi lze exportovat také na úrovni aplikace nebo verze. Jediným rozdílem je, že exportovaná verze na úrovni aplikace je aktuálně aktivní verze, zatímco na úrovni verze můžete zvolit libovolnou verzi, kterou chcete exportovat na stránce **[Nastavení.](luis-how-to-manage-versions.md)** 

Exportovaný soubor **neobsahuje:**

* Informace získané počítačem, protože aplikace je po importu přetrénována
* Informace o přispěvateli

Chcete-li zálohovat schéma aplikace LUIS, exportujte verzi z [portálu LUIS](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Správa změn přispěvatelů pomocí verzí a přispěvatelů

Služba LUIS používá koncept přispěvatelů do aplikace tím, že poskytuje oprávnění na úrovni prostředků Azure. Zkombinujte tento koncept s verzí a zajistěte cílenou spolupráci. 

Ke správě změn přispěvatele v aplikaci použijte následující postupy.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Správa více verzí ve stejné aplikaci

Začněte [klonováním](luis-how-to-manage-versions.md#clone-a-version) ze základní verze pro každého autora. 

Každý autor provádí změny ve své vlastní verzi aplikace. Když je autor s modelem spokojen, exportujte nové verze do souborů JSON.  

Exportované aplikace, soubory JSON nebo .lu, lze porovnat pro změny. Zkombinujte soubory a vytvořte jeden soubor nové verze. Změňte `versionId` vlastnost, která označuje novou sloučenou verzi. Importujte tuto verzi do původní aplikace. 

Tato metoda umožňuje mít jednu aktivní verzi, jednu fázi verzi a jednu publikovanou verzi. Výsledky aktivní verze můžete porovnat s publikovanou verzí (fáze nebo produkční) v [interaktivním testovacím podokně](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Správa více verzí jako aplikací

[Exportujte](luis-how-to-manage-versions.md#export-version) základní verzi. Každý autor importuje verzi. Vlastník verze je osoba, která aplikaci importuje. Po dokončení úprav aplikace exportujte verzi. 

Exportované aplikace jsou soubory ve formátu JSON, které lze porovnat se základním exportem pro změny. Zkombinujte soubory a vytvořte jeden soubor JSON nové verze. Změňte vlastnost **versionId** v JSON znamenat novou sloučenou verzi. Importujte tuto verzi do původní aplikace.

Přečtěte si další informace o vytváření příspěvků od [spolupracovníků](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Kontrola projevy koncového bodu a zahájení nového iterativního cyklu

Po dokončení iterace cyklu, můžete opakovat proces. Začněte s [kontrolou promluvy koncového bodu předpověď](luis-how-to-review-endpoint-utterances.md) LUIS označené s nízkou spolehlivostí. Zkontrolujte tyto projevy pro správné předpovídané záměr u správné a správné a úplné entity extrahované. Po kontrole a přijetí změn by měl být seznam recenzí prázdný.  

## <a name="next-steps"></a>Další kroky

Seznamte se s koncepty [spolupráce](luis-concept-keys.md).
