---
title: Návrh iterační aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS se nejlépe učí v iterativním cyklu změn modelů, utterance příkladů, publikování a shromažďování dat z dotazů na koncové body.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487767"
---
# <a name="authoring-cycles-and-versions"></a>Vytváření cyklů a verzí

Vaše aplikace LUIS se nejlépe učí v iterativním cyklu:

* vytvořit novou verzi
* Upravit schéma aplikace
    * záměry s příkladem projevy
    * podnikům
    * Database
* průřez
* test
* publish
    * test na koncovém bodu předpovědi pro aktivní učení
* shromažďování dat z dotazů na koncový bod

![Cyklus vytváření](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Sestavení schématu LUIS

Účelem schématu aplikace je definovat, co uživatel žádá (záměr nebo záměr) a jaké části otázky poskytují podrobnosti (entity), které vám pomůžou určit odpověď. 

Aby bylo možné určit slova a fráze, které jsou relevantní, a také typické řazení slov, musí být schéma aplikace specifické pro domény aplikace. 

Příklad projevy představuje vstup uživatele, u kterého se očekává, že se má za běhu získat aplikace. 

Schéma vyžaduje záměry a _musí mít_ entity. 

### <a name="example-schema-of-intents"></a>Příklady schématu záměrů

Nejběžnější schéma je schéma záměru uspořádané s záměry. Tento typ schématu závisí na LUIS a určení záměru uživatele. 

Tento typ schématu může mít entity, pokud pomáhá LUIS určit záměr. Například expediční entita (jako deskriptor k záměru) pomáhá LUIS určit záměr expedice. 

### <a name="example-schema-of-entities"></a>Příklad schématu entit

Schéma entity se zaměřuje na entity, které jsou data pro extrakci z projevy. 

Záměr utterance je menší nebo není důležitý pro klientskou aplikaci. 

Běžnou metodou uspořádání schématu entity je přidání všech příkladů projevy do záměru None. 

### <a name="example-of-a-mixed-schema"></a>Příklad smíšeného schématu

Nejefektivnější a vyspělé schéma je schéma záměru s celou škálou entit a funkcí. Toto schéma může začínat buď jako záměr, nebo jako schéma entit, a rozšířit tak, aby zahrnovalo koncepty obou, protože klientská aplikace potřebuje tyto části informací. 

## <a name="add-example-utterances-to-intents"></a>Přidat příklad projevy k záměrům

LUIS potřebuje několik příkladů projevy v každém **záměru**. Příklad projevy potřebuje dostatečnou variaci volby Wordu a pořadí slov, aby bylo možné určit, pro který záměr je utterance určen. 

> [!CAUTION]
> Nepřidávejte do hromadného projevy příklad. Začněte s 15 až 30 specifickými a různými příklady. 

Každý příklad utterance musí mít všechna **požadovaná data pro extrakci** navrženou a označenou **entitami**. 

|Klíčový element|Účel|
|--|--|
|Záměr|**Klasifikovat** uživatele projevy do jediného záměru nebo akce. Příklady zahrnují `BookFlight` a `GetWeather`.|
|Entita|**Extrahujte** data z utterance, která jsou nutná k dokončení záměru. Mezi příklady patří datum a čas cestování a umístění.|

Aplikaci LUIS navrhujete tak, aby ignorovala projevy, které nejsou relevantní pro doménu vaší aplikace, a to přiřazením utterance k záměru **none** . 

## <a name="test-and-train-your-app"></a>Testování a výuka vaší aplikace

Po 15 až 30 různých příkladech projevy v každém záměru s povinnými entitami, které jsou označeny, je nutné testovat a [vyškolit](luis-how-to-train.md). 

## <a name="publish-to-a-prediction-endpoint"></a>Publikovat do koncového bodu předpovědi

Ujistěte se, že jste aplikaci publikovali tak, aby byla dostupná v [oblastech koncového bodu předpovědi](luis-reference-regions.md) , kterou potřebujete. 

## <a name="test-your-published-app"></a>Testování publikované aplikace

Publikovanou aplikaci LUIS můžete testovat z koncového bodu předpovědi HTTPS. Testování z koncového bodu předpovědi umožňuje LUIS vybrat libovolný projevy s nízkou mírou spolehlivosti ke [kontrole](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Vytvořit novou verzi pro každý cyklus

Verze v LUIS se podobají verzím v tradičním programování. Každá verze je snímkem v čase aplikace. Než v aplikaci provedete změny, vytvořte novou verzi. Je snazší přejít zpět na starší verzi a pak se pokusit odebrat záměry a projevy do předchozího stavu.

ID verze se skládá ze znaků, číslic nebo "." a nemůže být delší než 10 znaků.

Počáteční verze (0,1) je výchozí aktivní verze. 

### <a name="begin-by-cloning-an-existing-version"></a>Zahájení klonování existující verze

Naklonujte existující verzi, která se má použít jako výchozí bod pro novou verzi. Po naklonování verze se nová verze zobrazí jako **aktivní** verze. 

### <a name="publishing-slots"></a>Sloty publikování
Publikujete do fází a produkčních slotů. Každá patice může mít jinou verzi nebo stejnou verzi. To je užitečné pro ověření změn před publikováním do produkčního prostředí, které je k dispozici pro roboty nebo jiné LUIS volání aplikací. 

Školené verze nejsou automaticky dostupné na [koncovém bodu](luis-glossary.md#endpoint)vaší aplikace. Je nutné [publikovat](luis-how-to-publish-app.md) nebo znovu publikovat verzi, aby byla k dispozici ve vašem koncovém bodě aplikace. Můžete publikovat do **přípravy** a **výroby**a poskytnout tak dvě verze aplikace, které jsou k dispozici na koncovém bodu. Pokud potřebujete další verze aplikace, kterou máte k dispozici na koncovém bodu, měli byste verzi exportovat a znovu importovat do nové aplikace. Nová aplikace má jiné ID aplikace.

### <a name="import-and-export-a-version"></a>Import a export verze
Verzi můžete importovat na úrovni aplikace. Tato verze se zobrazí jako aktivní verze a používá ID verze ve vlastnosti `versionId` souboru aplikace. Můžete také importovat do existující aplikace na úrovni verze. Nová verze se zobrazí jako aktivní verze. 

Verzi můžete exportovat na úrovni aplikace nebo verze. Jediným rozdílem je, že vyexportovaná verze na úrovni aplikace je aktuálně aktivní verze, zatímco na úrovni verze můžete na stránce **[Nastavení](luis-how-to-manage-versions.md)** zvolit libovolnou verzi, kterou chcete exportovat. 

Exportovaný soubor neobsahuje:

* informace zjištěné počítačem, protože aplikace je po importu znovu přeučena
* informace o přispěvateli

Chcete-li zálohovat schéma aplikace LUIS, exportujte verzi z portálu LUIS.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Správa změn přispěvatelů pomocí verzí a aplikací

LUIS poskytuje koncept přispěvatelů aplikace tím, že poskytuje oprávnění na úrovni prostředků Azure. Kombinací tohoto konceptu se správou verzí zajistíte cílenou spolupráci. 

Pomocí následujících postupů můžete spravovat změny přispěvatele ve vaší aplikaci.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Správa více verzí ve stejné aplikaci
Začněte [klonováním](luis-how-to-manage-versions.md#clone-a-version), od základní verze pro každého autora. 

Každý autor provádí změny ve své vlastní verzi aplikace. Jakmile s modelem splníte všechny autory, exportujte nové verze do souborů JSON.  

U exportovaných aplikací, souborů JSON nebo. lu se dají porovnat změny. Zkombinujte soubory a vytvořte jeden soubor nové verze. Změnou vlastnosti **versionId** vyznačíte novou sloučenou verzi. Importujte tuto verzi do původní aplikace. 

Tato metoda umožňuje mít jednu aktivní verzi, jednu verzi fáze a jednu publikovanou verzi. Výsledky aktivní verze můžete porovnat s publikovanou verzí (fáze nebo produkce) v [podokně interaktivní testování](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Správa více verzí jako aplikací
[Exportujte](luis-how-to-manage-versions.md#export-version) základní verzi. Každý autor importuje verzi. Osoba, která aplikaci importuje, je vlastníkem verze. Po dokončení úprav aplikace exportujte verzi. 

Exportované aplikace jsou soubory ve formátu JSON, které je možné porovnat se základním exportem pro změny. Zkombinujte soubory a vytvořte jeden soubor JSON nové verze. Změňte vlastnost **versionId** ve formátu JSON tak, aby poznamenat novou sloučenou verzi. Importujte tuto verzi do původní aplikace.

Přečtěte si další informace o vytváření příspěvků od [spolupracovníků](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>Podívejte se na téma Endpoint projevy a začněte nový cyklus vytváření.

Až budete hotovi s cyklem vytváření, můžete začít znovu. Začněte s [revizí koncového bodu projevy](luis-how-to-review-endpoint-utterances.md) Luis označeného s nízkou spolehlivostí. Ověřte tyto projevy pro správný předpokládaný záměr a správný a dokončenou entitu. Po kontrole a přijetí změn by seznam revizí měl být prázdný.  

## <a name="next-steps"></a>Další kroky

Seznamte se s koncepty [spolupráce](luis-concept-keys.md).
