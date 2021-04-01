---
title: Návrh iterační aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS se nejlépe učí v iterativním cyklu změn modelů, utterance příkladů, publikování a shromažďování dat z dotazů na koncové body.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 753d214b520affb59722bc29dbabc50c6e5968f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91538717"
---
# <a name="iterative-app-design-for-luis"></a>Návrh iterační aplikace pro LUIS

Aplikace Language Understanding (LUIS) se učí a efektivně funguje s iterací. Tady je typický cyklus iterace:

* Vytvořit novou verzi
* Úprava schématu aplikace LUIS Sem patří:
    * Záměry s příkladem projevy
    * Entity
    * Funkce
* Výuka, testování a publikování
    * Testování na koncovém bodu předpovědi pro aktivní učení
* Shromažďování dat z dotazů na koncový bod

![Cyklus vytváření](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Sestavení schématu LUIS

Schéma aplikace definuje, k čemu uživatel žádá ( _záměr_ nebo _záměr_ ) a jaké části záměru poskytují podrobnosti (označované jako _entity_), které slouží k určení odpovědi.

Schéma aplikace musí být specifické pro domény aplikace, aby bylo možné určit slova a fráze, které jsou relevantní, a také k určení typického řazení slov.

Příklad projevy představuje uživatelské vstupy, jako je například rozpoznaný hlas nebo text, který aplikace očekává za běhu.

Schéma vyžaduje záměry a _musí mít_ entity.

### <a name="example-schema-of-intents"></a>Příklady schématu záměrů

Nejběžnější schéma je schéma záměru uspořádané s záměry. Tento typ schématu používá LUIS k určení záměru uživatele.

Typ schématu záměru může mít entity, pokud pomáhá LUIS určit záměr uživatele. Například expediční entita (jako _funkce strojového učení_ k záměru) pomáhá Luis určit záměr expedice.

### <a name="example-schema-of-entities"></a>Příklad schématu entit

Schéma entit se zaměřuje na entity, což jsou data extrahovaná z projevy uživatele. Například pokud by uživatel chtěl vyslovit, "chtěl bych objednat tři pizzasy." Jsou k dispozici dvě entity, které by byly extrahovány: _tři_ a _pizzas_. Slouží k tomu, aby pomohly plnit záměr.

U schématu entity je záměrem utterance méně důležité pro klientskou aplikaci.

Běžnou metodou uspořádání schématu entity je přidání všech příkladů projevy do záměru **none** .

### <a name="example-of-a-mixed-schema"></a>Příklad smíšeného schématu

Nejefektivnější a vyspělé schéma je schéma záměru s celou škálou entit a funkcí. Toto schéma může začínat buď jako záměr, nebo jako schéma entit, a rozšířit tak, aby zahrnovalo koncepty obou, protože klientská aplikace potřebuje tyto části informací.

## <a name="add-example-utterances-to-intents"></a>Přidat příklad projevy k záměrům

LUIS potřebuje několik příkladů projevy v každém **záměru**. Příklad projevy potřebuje dostatečnou variaci volby Wordu a pořadí slov, aby bylo možné určit, pro který záměr je utterance určen.

> [!CAUTION]
> Nepřidávejte do hromadného projevy příklad. Začněte s 15 až 30 specifickými a různými příklady.

Každý příklad utterance musí mít všechna **požadovaná data pro extrakci** navrženou a označenou **entitami**.

|Klíčový element|Účel|
|--|--|
|Záměr|**Klasifikovat** uživatele projevy do jediného záměru nebo akce. Příklady zahrnují `BookFlight` a `GetWeather` .|
|Entita|**Extrahujte** data z utterance, která jsou nutná k dokončení záměru. Mezi příklady patří datum a čas cestování a umístění.|

Aplikace LUIS může být navržena tak, aby ignorovala projevy, které nejsou relevantní pro doménu aplikace, přiřazením utterance k záměru **none** .

## <a name="test-and-train-your-app"></a>Testování a výuka vaší aplikace

Po 15 až 30 různých příkladech projevy v každém záměru s povinnými entitami, které jsou označeny, je nutné otestovat a [naučit](luis-how-to-train.md) svou aplikaci Luis.

## <a name="publish-to-a-prediction-endpoint"></a>Publikovat do koncového bodu předpovědi

Aplikace LUIS musí být publikovaná, aby byla dostupná v [oblastech koncového bodu předpovědi](luis-reference-regions.md)seznamu.

## <a name="test-your-published-app"></a>Testování publikované aplikace

Publikovanou aplikaci LUIS můžete testovat z koncového bodu předpovědi HTTPS. Testování z koncového bodu předpovědi umožňuje LUIS vybrat libovolný projevy s nízkou mírou spolehlivosti ke [kontrole](luis-how-to-review-endpoint-utterances.md).

## <a name="create-a-new-version-for-each-cycle"></a>Vytvořit novou verzi pro každý cyklus

Každá verze je snímkem v čase aplikace LUIS. Než v aplikaci provedete změny, vytvořte novou verzi. Je snazší přejít zpět na starší verzi, než se pokusíte odebrat záměry a projevy do předchozího stavu.

ID verze se skládá ze znaků, číslic nebo "." a nemůže být delší než 10 znaků.

Počáteční verze (0,1) je výchozí aktivní verze.

### <a name="begin-by-cloning-an-existing-version"></a>Zahájení klonování existující verze

Naklonujte existující verzi, která se má použít jako výchozí bod pro každou novou verzi. Po naklonování verze se nová verze zobrazí jako **aktivní** verze.

### <a name="publishing-slots"></a>Sloty publikování

Můžete publikovat na buď fázi, nebo produkční sloty. Každá patice může mít jinou verzi nebo stejnou verzi. To je užitečné pro ověření změn před publikováním do produkčního prostředí, které je k dispozici pro roboty nebo jiné LUIS volání aplikací.

Školené verze nejsou automaticky dostupné na [koncovém bodu](luis-glossary.md#endpoint)aplikace Luis. Pokud chcete, aby byla verze dostupná na koncovém bodu aplikace LUIS, musíte [publikovat](luis-how-to-publish-app.md) nebo znovu publikovat verzi. Můžete publikovat do **přípravy** a **výroby** a poskytnout tak dvě verze aplikace, které jsou k dispozici na koncovém bodu. Pokud je potřeba, aby byly v koncovém bodě dostupné další verze aplikace, měli byste verzi exportovat a znovu ji naimportovat do nové aplikace. Nová aplikace má jiné ID aplikace.

### <a name="import-a-version"></a>Import verze

Verzi je možné **importovat** jako novou:
* Aplikace s novým ID aplikace
* Verze existující aplikace

Tato verze se stala aktivní verzí a používá ID verze ve `versionId` vlastnosti souboru aplikace.

### <a name="export-a-version"></a>Exportovat verzi

Verzi je možné **exportovat** z portálu Luis buď na úrovni aplikace, nebo na úrovni verze:

* Úroveň aplikace – vyberte aplikace na stránce **Moje aplikace** a pak vyberte **exportovat** .
* Úroveň verze – vyberte odkaz aplikace na stránce **Moje aplikace** , vyberte **Spravovat**, vybrat **verze** .

Jediným rozdílem je, že vyexportovaná verze na úrovni aplikace je aktuálně aktivní verze, zatímco na úrovni verze, můžete na stránce **[Nastavení](luis-how-to-manage-versions.md)** zvolit libovolnou verzi, kterou chcete exportovat.

Exportovaný **soubor neobsahuje** :

* informace o strojovém učení, protože aplikace je po importu znovu převlakovaná
* Informace o přispěvateli

Chcete-li zálohovat schéma aplikace LUIS, exportujte verzi z [portálu Luis](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Správa změn přispěvatelů pomocí verzí a přispěvatelů

LUIS využívá koncept přispěvatelů do aplikace tím, že poskytuje oprávnění na úrovni prostředků Azure. Kombinací tohoto konceptu se správou verzí zajistíte cílenou spolupráci.

Pomocí následujících postupů můžete spravovat změny přispěvatele ve vaší aplikaci.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Správa více verzí ve stejné aplikaci

Začněte [klonací](luis-how-to-manage-versions.md#clone-a-version) ze základní verze pro každého autora.

Každý autor provádí změny ve své vlastní verzi aplikace. Když je autor spokojeni s modelem, exportujte nové verze do souborů JSON.

Exportované aplikace `.json` nebo `.lu` soubory lze porovnat se změnami. Zkombinujte soubory a vytvořte jeden soubor nové verze. Změňte `versionId` vlastnost tak, aby signalizuje novou sloučenou verzi. Importujte tuto verzi do původní aplikace.

Tato metoda umožňuje mít jednu aktivní verzi, jednu verzi fáze a jednu publikovanou verzi. Výsledky aktivní verze můžete porovnat s publikovanou verzí (fáze nebo produkce) v [podokně interaktivní testování](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Správa více verzí jako aplikací

[Exportujte](luis-how-to-manage-versions.md#export-version) základní verzi. Každý autor importuje verzi. Osoba, která aplikaci importuje, je vlastníkem verze. Po dokončení úprav aplikace exportujte verzi.

Exportované aplikace jsou soubory ve formátu JSON, které je možné porovnat se základním exportem pro změny. Zkombinujte soubory a vytvořte jeden soubor JSON nové verze. Změňte vlastnost **versionId** ve formátu JSON tak, aby poznamenat novou sloučenou verzi. Importujte tuto verzi do původní aplikace.

Přečtěte si další informace o vytváření příspěvků od [spolupracovníků](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Podívejte se na bod Endpoint projevy a zahajte nový iterační cyklus.

Až budete s cyklem iterace hotovi, můžete postup opakovat. Začněte s [revizí koncového bodu projevy](luis-how-to-review-endpoint-utterances.md) Luis označeného s nízkou spolehlivostí. Ověřte tyto projevy pro správný předpokládaný záměr a správný a dokončenou entitu. Po kontrole a přijetí změn by seznam revizí měl být prázdný.

## <a name="next-steps"></a>Další kroky

Seznamte se s koncepty [spolupráce](luis-how-to-azure-subscription.md).
